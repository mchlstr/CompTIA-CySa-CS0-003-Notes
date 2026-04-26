# Threat Hunting

**Threat hunting** = *proactively* searching for threats that have evaded existing detections. The opposite of waiting for alerts. Assumes attackers are already in.

Difference vs. monitoring:
- **Monitoring** is reactive - alert fires, analyst investigates.
- **Hunting** is hypothesis-driven - "if APT29 were here, what would I see?" → go look.

## Indicators of Compromise (IOCs)

**IOCs** = forensic evidence that a system has been compromised.

**Categories:**
- **Atomic** - single observables: IP, domain, hash, URL.
- **Computed** - derived: regex matches, statistical thresholds.
- **Behavioral** - patterns: "process X spawning Y followed by network connection to Z."

**Common IOCs:**
- File hashes (MD5, SHA-1, SHA-256).
- IP addresses, domains, URLs.
- Email addresses, subjects.
- Registry keys, file paths.
- Mutex names.
- User-agents.
- Service / scheduled task names.

### Collection
- Internal: SIEM, EDR, IR cases, malware sandbox output.
- External: threat feeds (commercial + open), ISAC sharing, vendor reports, MISP communities.

### Analysis
- **Validate** - is the IOC still valid? (IPs change fast)
- **Enrich** - pivot in VirusTotal, Shodan, passive DNS, WHOIS to understand context.
- **Correlate** - does this IOC appear in your environment? When? On what assets?
- **Attribute (carefully)** - group of TTPs may point to a known actor. Don't over-attribute.

### Application
- **Block** at firewalls, proxies, DNS (sinkhole).
- **Hash-block** in EDR/AV.
- **Detect** by writing SIEM/EDR rules.
- **Hunt** - search historical telemetry for matches (retrohunting).

**IOC fragility (pyramid of pain):** atomic IOCs (IPs, hashes) are cheap for attackers to change. Hunting on **TTPs and behaviors** is more durable.

## Focus areas

### Configurations
- Misconfigurations are the most common path in.
- Hunt for: open RDP/SSH to internet, default credentials, unpatched services, exposed admin panels, S3 bucket public, weak TLS.
- Tools: Shodan, Nessus, internal CIS benchmark scans.

### Isolated networks (OT/ICS, air-gapped)
- "Isolated" rarely means isolated - USBs, vendor laptops, jump hosts bridge them.
- Hunt for: unexpected outbound connections, unusual protocols (DNP3 from non-utility hosts), removable media activity.
- High consequence environments - assume the attacker has spent time on recon.

### Business-critical assets ("crown jewels")
- Identify what really matters: domain controllers, financial systems, code repos, customer databases, PHI/PII stores.
- Disproportionate hunting effort goes here.
- Look for unusual access patterns: who logs in, when, from where, what they touch.

## Active defense & honeypots

**Active defense** = defensive actions that go beyond passive monitoring - deception, engagement, attribution, sometimes (legally fraught) hack-back.

### Deception techniques
- **Honeypots** - fake systems designed to attract attackers.
  - **Low-interaction** - emulate services (Honeyd, Cowrie). Easy to deploy, less data, easier to fingerprint.
  - **High-interaction** - real systems, fully observed. Rich data, higher risk if attacker pivots.
- **Honeynets** - networks of honeypots.
- **Honeyfiles / honeytokens** - fake documents, fake credentials, fake AWS keys, fake DB rows. Any access = guaranteed alert (no false positives because nothing legitimate touches them).
- **Canary tokens** - tiny tripwires (e.g., a unique URL embedded in a "salary spreadsheet"; access pings home).

**Deception platforms:** Thinkst Canary, Attivo (now SentinelOne), Illusive, TrapX.

### Why honeypots / honeytokens are powerful
- **Low false positive rate** - legitimate users don't open the fake "passwords.xlsx".
- **Early detection** - attackers in recon phase often touch decoys.
- **Intel collection** - observe TTPs in your specific environment.

### Active defense legal/ethical
- **Hack-back** - striking back at attackers. Generally **illegal** (Computer Fraud and Abuse Act in US). Don't do it.
- **Sinkholing** - redirecting malicious domains you control to a logging server. Legal if you own/control infrastructure.
- **Legal active defense:** deception in your own network, attribution research, sharing intel with law enforcement.

## Hunting frameworks

- **Sqrrl / Threat Hunting Loop** - Hypothesis → Investigate → Uncover → Inform & Enrich Analytics → repeat.
- **MITRE ATT&CK-driven hunting** - pick a technique (e.g., T1059.001 PowerShell), define what it would look like in your data, hunt.
- **Pyramid of Pain** - prioritize hunting for high-pain indicators (TTPs over IPs).

**Exam tip:** know the difference - IOC = evidence, IOA (Indicator of Attack) = behavior in progress. Hunting looks for both, but mature programs lean toward IOAs.

---

← Back: [01_13 Threat Intelligence](01_13_threat_intelligence.md) - Next: [01_15 Scripting Languages for Analysts](01_15_scripting_languages.md) →

## Related

**Internal:**
- [01_13 Threat intelligence](01_13_threat_intelligence.md) - feeds the hypotheses
- [01_15 Scripting languages](01_15_scripting_languages.md) - for ad-hoc queries
- [03_01 Attack methodology frameworks](../03_incident_response_management/03_01_attack_methodology_frameworks.md) - ATT&CK-driven hunting
- [03_05 Forensic artifacts](../03_incident_response_management/03_05_forensic_artifacts.md) - what to look for on hosts

**External:**
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE D3FEND - defensive countermeasures](https://d3fend.mitre.org/)
- [Atomic Red Team](https://atomicredteam.io/)
- [SANS - Hunt Evil poster](https://www.sans.org/posters/hunt-evil/)
- [Thinkst Canary - deception platform](https://canary.tools/)
