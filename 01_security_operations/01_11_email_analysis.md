# Email Analysis

Email is the #1 initial-access vector. CySA+ tests both *header analysis* and *email authentication* (SPF/DKIM/DMARC). Expect scenario questions with real headers — be able to identify spoofing, alignment failures, and indicators of phishing.

## Email authentication: SPF, DKIM, DMARC

These three work together. None of them alone is sufficient.

### SPF (Sender Policy Framework)

**What it does:** lets a domain owner publish (in DNS) which IPs are allowed to send mail for that domain.

- DNS record type: **TXT** (e.g., `v=spf1 ip4:203.0.113.0/24 include:_spf.google.com -all`).
- Receiver checks the **envelope sender** ("Mail From" / Return-Path) IP against the SPF record.
- Common qualifiers:
  - `+` pass (default if omitted)
  - `-` fail (hard reject)
  - `~` softfail (accept but mark)
  - `?` neutral
- `-all` at the end = "anyone not listed above is unauthorized." This is the strictest, recommended setting.

**Limitations:**
- SPF checks the **envelope sender**, not the visible **From** header. Attackers can pass SPF using their own domain while spoofing the From.
- Breaks on forwarding (the forwarder's IP isn't in the original sender's SPF). Solved by **SRS (Sender Rewriting Scheme)**.
- 10-DNS-lookup limit per check; complex chains can exceed it and cause permerror.

### DKIM (DomainKeys Identified Mail)

**What it does:** the sending server cryptographically **signs** parts of the email; receiver verifies the signature using a public key in the sender's DNS.

- DNS record: TXT at `<selector>._domainkey.<domain>` (e.g., `mail._domainkey.example.com`).
- Email gets a `DKIM-Signature` header containing the selector (`s=`), domain (`d=`), signed headers (`h=`), and signature (`b=`).
- Receiver fetches the public key from DNS, verifies the signature.
- If the signature validates, the message hasn't been tampered with in transit and was authorized by the signing domain.

**Limitations:**
- DKIM only proves the signing domain authorized the message — *not* that the visible From matches.
- Forwarding usually preserves DKIM (signature stays valid as long as signed headers aren't modified).
- Key rotation is operational overhead.

### DMARC (Domain-based Message Authentication, Reporting & Conformance)

**What it does:** ties SPF and DKIM together with **alignment** and tells receivers what to do on failure. Also provides reporting.

- DNS record: TXT at `_dmarc.<domain>` (e.g., `v=DMARC1; p=reject; rua=mailto:dmarc@example.com; pct=100`).
- **Policy (`p=`):**
  - `none` — monitor only, don't act on failures.
  - `quarantine` — send failures to spam.
  - `reject` — refuse failures outright.
- **Alignment** — the visible `From` domain must match the SPF/DKIM domain (relaxed = same organizational domain, strict = exact match).
- **Reporting:**
  - `rua` — aggregate reports (where to send daily summary XML).
  - `ruf` — forensic reports (per-failure details).
- **`pct=`** — percentage of messages to apply the policy to (used during phased rollouts).

**Why DMARC matters:** it's the only one that protects the **visible From** header (the one users actually see). Without DMARC, an attacker can spoof your-bank.com in the From field even if SPF/DKIM are configured.

### How they combine

A receiver's check (simplified):
1. **SPF check** — does sender IP match SPF for envelope-from domain? Pass/fail.
2. **DKIM check** — does signature validate against published public key? Pass/fail.
3. **DMARC alignment** — does the visible From domain align with SPF or DKIM domain?
4. **DMARC policy** — if alignment fails, apply `p=` (none / quarantine / reject).

**Pass condition:** SPF OR DKIM aligns (only one needs to align for DMARC to pass).

## Email header analysis

Headers are the truth of an email. The body can lie; headers leave a trail.

### Reading order
Email headers are added top-down by each server in reverse order: **the last server to handle the email is at the top, the original sender's server is at the bottom**. Read **bottom-up** to trace the path.

### Key headers to know

| Header | What it tells you |
|---|---|
| `From:` | Visible sender. Trivially spoofed. |
| `Reply-To:` | Where replies actually go. Often differs from From in phishing. |
| `Return-Path:` (envelope sender / Mail From) | Where bounces go. SPF checks against this. |
| `Received:` | Each hop the message took. Multiple of these — read bottom-up. |
| `Message-ID:` | Unique identifier; often reveals originating server. |
| `Authentication-Results:` | Receiver's verdict on SPF, DKIM, DMARC. |
| `DKIM-Signature:` | DKIM signature data. |
| `X-Originating-IP:` | Originating IP (if present — often stripped). |
| `User-Agent:` / `X-Mailer:` | Sending mail client (Outlook, Thunderbird, custom). |
| `Subject:`, `Date:` | As shown. |
| `MIME-Version:`, `Content-Type:` | Body structure. |

### What to look for (phishing indicators in headers)

- **Mismatch between `From:` and `Return-Path:`** — display says one thing, actual envelope is different.
- **`Reply-To:` differs from `From:`** — common BEC tactic so replies go to attacker.
- **Authentication-Results showing `spf=fail` or `dmarc=fail`** — strongest signal.
- **Originating IP doesn't match the claimed sender's expected infrastructure** — pivot in WHOIS, ASN.
- **Strange Received chain** — the chain skips expected mail servers, includes IPs in unexpected geographies, or has bizarre timestamps.
- **`Message-ID` domain doesn't match `From` domain.**
- **Very old or future timestamp** in Date.
- **Suspicious `X-Mailer`** — known phishing toolkits.
- **Display name spoofing** (`From: "CEO Name" <random@gmail.com>`) — display says CEO, actual address is gmail.

### Sample header excerpt

```
Received: from mail-eu-west.example.com (203.0.113.45)
    by mx.victim.com with ESMTPS id abc123
    Tue, 22 Apr 2026 09:14:33 +0000
Received: from unknown (179.61.200.5)
    by mail-eu-west.example.com with ESMTP
    Tue, 22 Apr 2026 09:14:30 +0000
From: "IT Helpdesk" <helpdesk@victim-corp.com>
Reply-To: helpdesk-reset@protonmail.com
Return-Path: <bounce@cheap-vps-host.ru>
Authentication-Results: mx.victim.com;
    spf=fail smtp.mailfrom=cheap-vps-host.ru;
    dkim=none;
    dmarc=fail (p=reject) header.from=victim-corp.com
Subject: Password reset required
```

Indicators here: SPF fail, DKIM none, DMARC fail with `p=reject` (the receiver should have rejected it — possible misconfig), Reply-To pointing to ProtonMail (free email), Return-Path on a Russian VPS, originating IP unrelated to the claimed sender.

## Tools

- **MXToolbox** (https://mxtoolbox.com/) — header analyzer, SPF/DKIM/DMARC checkers, MX lookup.
- **Google Admin Toolbox Messageheader** — paste headers, get parsed view.
- **dmarcian, EasyDMARC, Postmark DMARC** — DMARC report parsers and dashboards.
- **emailrep.io** — sender reputation lookup.
- **PhishTool** — phishing investigation platform.
- **VirusTotal / urlscan.io** — for any URLs in the message.
- **Hybrid Analysis / ANY.RUN** — for attachments.
- **Microsoft 365 / Google Workspace admin consoles** — search and pull copies of suspicious mail org-wide.

## Other phishing indicators (recap from 01_10)

- Lookalike domains (homograph attacks, IDN punycode).
- URL shorteners hiding the destination.
- Unexpected attachments (`.iso`, `.lnk`, `.html`, `.docm`, `.xlsm`).
- Urgency / fear / authority pressure language.
- Mismatched link text and href.

## Exam tips

- Know **which check protects what**:
  - SPF protects the envelope sender (Return-Path).
  - DKIM protects message integrity + signing domain.
  - DMARC protects the **visible From** by enforcing alignment.
- A passing SPF + DKIM does **not** mean the email is safe — it just means it came from authorized infrastructure.
- DMARC `p=none` is **monitoring only** — it does not block anything.
- Read `Received` headers **bottom up** to trace the path.
- The `Authentication-Results` header is the receiver's verdict — read it first when analyzing a phishing report.

---

← Back: [01_10 Malicious Activity Detection](01_10_malicious_activity_detection.md) — Next: [01_12 File & Malware Analysis](01_12_file_and_malware_analysis.md) →

## Related

**Internal:**
- [01_06 DNS security](01_06_dns_security.md) — SPF/DKIM/DMARC live in DNS TXT records
- [01_10 Malicious activity detection](01_10_malicious_activity_detection.md) — broader phishing indicators
- [01_12 File and malware analysis](01_12_file_and_malware_analysis.md) — for attachments
- [05_tools — VirusTotal](../05_tools/VirusTotal.md)

**External:**
- [RFC 7208 — SPF](https://datatracker.ietf.org/doc/html/rfc7208)
- [RFC 6376 — DKIM](https://datatracker.ietf.org/doc/html/rfc6376)
- [RFC 7489 — DMARC](https://datatracker.ietf.org/doc/html/rfc7489)
- [MXToolbox](https://mxtoolbox.com/) — header analyzer + lookups
- [dmarcian — DMARC Inspector](https://dmarcian.com/dmarc-inspector/)
