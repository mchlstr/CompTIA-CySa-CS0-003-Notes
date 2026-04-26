# Incident Response Activities

This file walks through the operational phases of IR: detection → analysis → containment → eradication → recovery. (Preparation and post-incident in 03_03.)

## Detection & Analysis

### Detection sources
- **SIEM alerts** - correlation rules fire.
- **EDR/XDR alerts** - endpoint behavior detection.
- **IDS/IPS alerts** - network signature/anomaly.
- **DLP alerts** - data movement.
- **Threat intel match** - internal IOC hit.
- **User report** - phishing, suspicious activity ("see something, say something").
- **Third party** - partner, customer, ISP, law enforcement, security researcher reports something to you. (This is an unfortunately common detection path.)
- **Threat hunting** - proactive discovery.

### Initial triage questions
- **What is the alert telling me?** - read the rule, what triggered.
- **Is this a true positive?** - eliminate FPs early.
- **What's the scope?** - one host, many hosts, which users, which data?
- **What's the timeline?** - when did this start?
- **Is it ongoing?** - active vs. historical.
- **What's the criticality of affected assets?** - drives severity.
- **Is there a threat intel match?** - known actor / campaign?

### Analysis using IOCs
- **Atomic IOCs** - IPs, hashes, domains. Search across SIEM/EDR for other hits.
- **Behavioral IOCs** - process trees, parent-child, command lines.
- **Pivoting** - one IOC leads to others (the file connected to *this* IP, which was downloaded via *this* email, which targeted *these* users).

### Log/data analysis
- Correlate **across sources**: firewall + endpoint + auth logs together.
- **Timeline analysis** - build a chronological picture of attacker actions.
- **Frequency analysis** - what's normal volume vs. anomalous?
- **Outlier detection** - rare user-agent, rare login location, rare process executable.
- Tools: SIEM search, EDR queries, forensic timeline tools (Plaso, Timesketch).

### Scoping
A core IR question: *how big is this?*
- How many hosts compromised?
- Which accounts? Did attacker pivot to admin?
- What data was accessed / exfiltrated?
- What persistence mechanisms remain?
- Is the attacker still in?

**Don't act before scoping.** Premature containment alerts the attacker, who may detonate destructive payloads or rapidly persist elsewhere.

## Evidence acquisition

### Order of volatility (RFC 3227)
Collect from most volatile to least, or you lose data:
1. **CPU registers, cache** - gone in microseconds.
2. **RAM (memory)** - gone on power-off / reboot.
3. **Network state** - connections, ARP cache, routing tables.
4. **Running processes** - process list, open files.
5. **Disk** - files, registry, logs.
6. **Remote logging / archived data** - long-term.
7. **Physical media** (printouts, optical) - most stable.

### Acquisition principles
- **Forensic image** - bit-for-bit copy. Tools: **dd**, **dcfldd**, **FTK Imager**, **EnCase**.
- **Hash the original and the image** (MD5 + SHA-256) - proves no tampering.
- **Work from copies, never the original** - original goes into evidence storage.
- **Write blocker** - hardware/software preventing modifications to source media.
- **Memory acquisition** - Volatility, FTK Imager, WinPMEM, LiME (Linux).
- **Live response** - when shutting down isn't an option (production system, encrypted disk needs unlocked OS to read).

### Evidence integrity
- Hash everything; document hashes.
- Time-sync source and forensic workstation.
- Photograph the scene if physical.
- Document who did what, when.

## Chain of custody

**Chain of custody** = documented, unbroken trail showing evidence handling from collection to court.

For each item, record:
- **What** it is (description, identifier, hash).
- **Where** it came from (system, location).
- **When** it was collected.
- **Who** collected it.
- **Who** has had custody at every step (transfers logged with date/time/signature).
- **Why** each handover.
- **How** it was stored (sealed bag, evidence locker, etc.).

Any gap = evidence may be inadmissible. Even within the org, if you don't intend to go to court, maintain it - you may end up there.

## Legal hold

**Legal hold** (a.k.a. **litigation hold**) = formal directive to preserve all relevant data when litigation, investigation, or regulatory action is anticipated.

- **Suspends normal data destruction** (retention schedules, auto-delete).
- Issued by legal/compliance.
- Failure to preserve = **spoliation** → sanctions, adverse inference at trial.
- Scope: emails, files, logs, backups, voicemails, chat messages, mobile devices.

In an incident, legal hold often kicks in alongside IR: the moment you suspect an incident may lead to legal action (regulator, customer suit, criminal investigation, employee dispute), preserve everything.

## Containment

**Goal:** stop the bleeding without destroying evidence and without alerting the attacker prematurely.

### Strategies
- **Short-term containment** - quick action to limit damage (isolate host, block IP at firewall, disable account).
- **Long-term containment** - stable measures while you finish eradication (system rebuild image readied, attacker comms watched).

### Common containment actions
- **Network isolation** - pull cable, VLAN quarantine, EDR network containment (host can talk to EDR console only).
- **Account disable** - for compromised accounts; rotate credentials org-wide if AD compromise suspected.
- **Block at firewall / proxy / DNS** - known C2 infrastructure.
- **Reset / revoke tokens** - OAuth, API keys, session tokens, certificates.
- **Disable services / functions** - stop a vulnerable service.
- **Segment** - restrict the compromised network from sensitive zones.

### Containment trade-offs
- **Speed vs. evidence** - pulling power saves data from destruction but loses RAM forensics.
- **Visibility vs. silence** - if you block the attacker's C2, they know you're aware. Sometimes you watch covertly first.
- **Business impact** - taking a critical system offline may hurt more than the attacker. Decision must involve business owners.

## Eradication

**Goal:** completely remove the attacker and their tooling. Don't leave artifacts that allow re-entry.

### Steps
- **Remove malware** - but don't trust AV cleanup for advanced threats; **rebuild** is safer.
- **Close the entry point** - patch the exploited vuln, rotate the stolen creds, fix the misconfig.
- **Remove persistence** - scheduled tasks, services, registry run keys, WMI subscriptions, cron jobs, accounts.
- **Audit related systems** - attacker likely touched more than the one you know about.
- **Rotate credentials** - passwords, API keys, certificates, Kerberos krbtgt (twice, 10+ hours apart, if AD compromise).

### Why "wipe and reinstall" is often the answer
- You can never be 100% sure you got everything.
- Modern attackers chain persistence (multiple backdoors, firmware implants).
- "Defense-in-depth eradication" - assume the attacker stashed something you missed.

## Recovery

**Goal:** restore systems to normal operation while monitoring for re-compromise.

### Activities
- **Rebuild from known-good** images / backups (validated clean).
- **Apply patches and hardening** before bringing back online.
- **Restore data** from clean backups (test for compromise first - backups themselves may be infected).
- **Reissue credentials, certs, keys**.
- **Phased return to production** - bring back gradually, monitor.
- **Heightened monitoring** - for days/weeks after recovery, watch closely for re-entry attempts.
- **Validate functionality** with business owners.

### Communications during recovery
- Stakeholders informed of progress and ETA.
- External (customers, regulators) per legal/PR plan.
- Lessons being captured continuously, not waiting for the post-mortem.

## Sequence summary

```
Detect → Analyze → Contain → Eradicate → Recover
                ↓
       (preserve evidence and chain of custody throughout)
```

**Exam scenarios:** if a question describes an analyst rebuilding a system before scoping the breach → that's wrong (containment / eradication before scope = blown investigation). If it asks the *first* response action → usually containment, after detection / scoping. If it asks the *most important* documentation → chain of custody.

---

← Back: [03_01 Attack Methodology Frameworks](03_01_attack_methodology_frameworks.md) - Next: [03_03 Preparation & Post-Incident](03_03_preparation_and_post_incident.md) →

## Related

**Internal:**
- [03_01 Attack methodology frameworks](03_01_attack_methodology_frameworks.md) - frameworks applied here
- [03_03 Preparation and post-incident](03_03_preparation_and_post_incident.md) - what comes before/after
- [03_04 Case studies](03_04_case_studies.md) - scenarios applying these activities
- [03_05 Forensic artifacts](03_05_forensic_artifacts.md) - evidence sources
- [04_02 Incident reporting](../04_reporting_and_communication/04_02_incident_reporting.md) - comms during IR

**External:**
- [NIST SP 800-86 - Forensic Techniques into Incident Response](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-86.pdf)
- [RFC 3227 - Evidence Collection and Archiving](https://datatracker.ietf.org/doc/html/rfc3227)
- [Volatility Foundation](https://www.volatilityfoundation.org/)
- [SANS DFIR Cheat Sheets](https://www.sans.org/posters/?focus-area=digital-forensics)
