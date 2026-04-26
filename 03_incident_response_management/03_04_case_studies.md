# Case Studies

These are scenario walk-throughs that link **IOCs**, **logs**, **threat intelligence**, and the **IR phases** from prior files. Use them to practice the analyst mindset: from a single alert, scope and respond.

## Case 1 — The phishing alert

### Initial signal
Tier 1 alert: "User reported a suspicious email." Email is from `it-helpdesk@company-secure.com` (lookalike domain), subject "Password reset required," with link to `https://company-secure.com/login.aspx`.

### Triage steps
1. **Quarantine the email** in the mail platform.
2. **Pull the email headers** — confirm sender IP, SPF/DKIM/DMARC results, mail path.
3. **Search the mail gateway** — how many other recipients got this?
4. **Threat intel pivot:**
   - Domain age (new = suspicious) → WHOIS, certificate transparency.
   - URL reputation → VirusTotal, URLScan, urlhaus.
   - Hosting infra → ASN, related domains via passive DNS.
5. **Check if anyone clicked** — proxy / web gateway / EDR network connections to the domain.
6. **Check if anyone entered credentials** — IdP login telemetry; correlate timestamps.

### Findings
50 employees received it. 8 clicked. 2 entered credentials. One of those 2 had MFA; the other (an IT admin) did not.

### Response
- **Containment:** disable the IT admin account, reset password, revoke active sessions and refresh tokens, force re-MFA on next login.
- **Eradication:** delete the email org-wide. Add sender domain + URL to block list. WAF / proxy block.
- **Recovery:** re-enable accounts after credential reset and MFA enrollment for the 2 affected. Monitor for residual access (suspicious logins, mailbox forwarding rules — common attacker move).
- **Post-incident:**
  - **Lessons:** an admin account without MFA — fix policy gap.
  - **Detection:** add SIEM rule for new mailbox forwarding rules (common BEC indicator).
  - **Training:** targeted phishing training for the impacted users.

### IOCs documented
- Domain: `company-secure.com`
- Sender IP, ASN.
- URL paths.
- Email subject regex.

---

## Case 2 — The beaconing host

### Initial signal
Zeek logs show one workstation connecting to `203.0.113.45:443` every ~60 seconds, ±5 sec jitter, for the past 14 hours. ~150 bytes per request.

### Why this is suspicious
- Periodic with low jitter → automated, not human.
- Constant size → likely structured C2 messages.
- HTTPS → opaque content.
- Long duration → established C2.

### Triage
1. **Pivot on the IP:**
   - Reputation: VirusTotal, AbuseIPDB, threat feeds.
   - Passive DNS: what domains resolve(d) to it?
   - ASN: who owns it? (often a hosting provider abused for C2).
2. **Pivot on the host:**
   - Which user? When did beaconing start?
   - EDR: what process is making the connections? Process tree?
   - Recent file activity, scheduled tasks, services on host.
3. **Check for other hosts** beaconing to same IP or to related infra.

### Findings
- IP flagged by 8 threat feeds as Cobalt Strike C2.
- Process: `svchost.exe` (legitimate-looking, but parent is `winword.exe` — anomaly).
- Beaconing started 3 days ago, right after user opened an email attachment "Q3-Report.docm" (macro-enabled).
- Two other hosts also beaconing to related infra.

### Response
- **Scope first** — find all affected hosts before containing (otherwise attacker pivots and re-establishes).
- **Containment (coordinated, not piecemeal):** isolate all 3 hosts via EDR network containment simultaneously. Block C2 IP at firewall.
- **Eradication:** rebuild affected hosts from clean image. Search org-wide for the malicious doc hash (delete from mailboxes and file shares). Patch the Office macro policy gap (block macros from internet).
- **Recovery:** restore user data from clean backups. Heightened monitoring on those users for 30 days.
- **Post-incident:**
  - Detection: add SIEM rule for `winword.exe` spawning unusual children (a common ATT&CK technique — T1059).
  - Process: review Office macro group policy — should be "block macros from internet" by default.

### Frameworks applied
- **Kill Chain:** Delivery (email) → Exploitation (macro) → Installation (CS implant) → C2 (beaconing). Caught at C2; future detections aim for earlier (Delivery via mail filtering, Exploitation via macro block).
- **MITRE ATT&CK:** T1566.001 (Phishing - Spearphishing Attachment), T1204.002 (User Execution - Malicious File), T1059.005 (VBA), T1071.001 (Web Protocol C2).
- **Diamond Model:** Adversary (unknown), Capability (Cobalt Strike), Infrastructure (203.0.113.45), Victim (3 hosts in finance dept).

---

## Case 3 — The impossible travel alert

### Initial signal
SIEM alert: same user account logged in from New York at 14:00 and from Singapore at 14:35. Physically impossible.

### Triage
1. **Confirm both events** — IdP logs, source IPs, user-agents.
2. **Check VPN / corporate proxy** — could one of them be a proxied connection? (False positive cause.)
3. **Check device** — same device fingerprint? Different?
4. **Check what the account did** in each session — anything sensitive accessed?
5. **MFA status** — was MFA prompted in both? If yes, who approved?

### Findings
- NY = legitimate user, on company laptop, normal activity.
- Singapore = different device fingerprint, accessed SharePoint and downloaded customer list, then created a mailbox forwarding rule sending all mail to an external Gmail.
- MFA was approved in Singapore — possibly via push fatigue or MFA bypass token.

### Response
- **Containment:** disable account, revoke all active sessions and refresh tokens, force MFA re-enroll, remove forwarding rule.
- **Investigate:** how was MFA bypassed? Token theft? Push fatigue? SIM swap? Adversary-in-the-middle phishing kit (Evilginx / EvilProxy)?
- **Scope:** does attacker have other accounts? Other forwarding rules elsewhere?
- **Notify:** the user, manager, security leadership; if customer data exfiltrated, follow breach notification procedures.
- **Eradication:** ensure no persistence (OAuth grants, app passwords, alternate emails on account, device registrations).

### Lessons
- Add detection for new mailbox forwarding rules.
- Add detection for OAuth grants to unusual apps.
- Move from push-only MFA to **number matching** or hardware tokens (FIDO2) for high-privilege accounts.
- Conditional access policies — block sign-ins from unmanaged devices for sensitive data.

---

## Case 4 — The ransomware

### Initial signal
3 AM page: file server alerting, mass file modifications in last 30 min. EDR on multiple servers reporting ransomware behavior. User reports starting to come in.

### Triage (fast — minutes matter)
1. **Confirm scope:** how many hosts are encrypting? Which network segments?
2. **Identify the variant** if possible (note file extensions, ransom note name, ID Ransomware site).
3. **Check entry vector quickly:** any auth anomalies in last 24-48h? RDP brute force? Phishing? Vulnerable internet-facing service?

### Containment (aggressive)
- **Network isolation** of affected hosts — pull cable / VLAN quarantine / EDR isolation.
- **Block lateral spread:** disable SMB on affected segments, block RDP between segments.
- **Disable compromised accounts** identified.
- **Snapshot file shares** if storage allows (pre-encryption state, useful for recovery).
- **If AD compromised:** rotate krbtgt password (twice, 10+ hours apart) — invalidates Kerberos golden tickets.
- **DON'T just power off** — lose memory forensics. EDR isolation is preferred.

### Eradication & recovery
- Identify and close the entry vector.
- Wipe and rebuild infected hosts.
- **Restore from backups** — verified clean (test in isolated environment first; many ransomware groups infect backups).
- **Decrypt if possible** — check NoMoreRansom for available decryptors.
- **Do NOT pay** by default — funds criminal activity, no guarantee, may violate sanctions (OFAC). Decision involves legal, exec, possibly law enforcement.

### External obligations
- **Law enforcement** notification (FBI / IC3 in US, NCSC in UK, local CSIRT).
- **Cyber insurance** — most policies require notification; failure may void coverage.
- **Regulators** — depends on data exposed (GDPR 72-hour notice, HIPAA, state breach laws).
- **Customers / public** — if customer data affected.

### Post-incident
- Root cause: how did they get in? (Often: unpatched VPN, weak RDP creds, phishing, supply chain).
- Hardening: MFA on all external access, segment, immutable backups, EDR everywhere, tested IR plan.
- Tabletop the next ransomware scenario.

---

## Cross-cutting analyst skills these cases test

- **Pivoting** from one IOC to others (URL → domain → IP → other domains → other affected users).
- **Scoping before action** — never contain before you understand the breadth.
- **Correlating across log sources** — endpoint + network + identity together.
- **Knowing when to use which framework** — Kill Chain for sequence, ATT&CK for technique mapping, Diamond for analytical pivot.
- **Distinguishing symptoms from root causes**.
- **Communicating clearly under pressure** — to execs, users, regulators.

**Exam tip:** scenario questions often ask "what should the analyst do *next*" — pick the option that follows IR best practice (scope before contain, contain before eradicate, document throughout, escalate when criteria met). Avoid the "act fast" answer that skips a phase.
