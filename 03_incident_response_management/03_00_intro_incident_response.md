# Intro: Incident Response Management

Domain 3 is ~20% of the CySA+ exam. Where Domain 1 is about *seeing* threats and Domain 2 is about *hardening against* them, Domain 3 is about *what you do when something gets through*.

## What is incident response (IR)?

**Incident** — any event that compromises (or threatens to compromise) the confidentiality, integrity, or availability of an information asset.

**Event** vs **incident**: every incident is an event, but not every event is an incident. A failed login is an event; 10,000 failed logins from one IP is an incident.

**Incident response** = the structured process of preparing for, detecting, containing, eradicating, recovering from, and learning from incidents.

## Why structure matters

Without a process, IR turns into chaos:
- People step on each other's evidence.
- Containment actions tip off the attacker.
- Communications go to the wrong people (or none at all).
- Lessons aren't captured, same incident happens again.
- Legal/regulatory obligations get missed.

A documented IR process turns chaos into a repeatable workflow.

## The incident response lifecycle

Two main models you must know.

### NIST SP 800-61 (4 phases)
1. **Preparation** — IR plan, tools, training, comms before incidents happen.
2. **Detection & Analysis** — identify what happened, scope, severity.
3. **Containment, Eradication & Recovery** — stop the bleeding, remove the threat, restore service.
4. **Post-Incident Activity** — lessons learned, improve detections and process.

### SANS PICERL (6 phases)
1. **Preparation**
2. **Identification**
3. **Containment**
4. **Eradication**
5. **Recovery**
6. **Lessons Learned**

These are equivalent — NIST collapses some SANS phases. Either model is valid; CySA+ uses both. **Know both names.**

## The IR team

- **CSIRT (Computer Security Incident Response Team)** or **CIRT** — the named team responsible for IR. May be permanent or virtual.
- **Incident Commander / Incident Manager** — runs the response, makes decisions, coordinates.
- **Lead Analyst** — technical lead.
- **Communications** — internal updates, customer notifications, PR.
- **Legal** — regulatory obligations, evidence handling, law enforcement liaison.
- **HR** — for insider incidents.
- **Executive sponsor** — authority for major decisions (shutdown systems, ransomware payment, public disclosure).
- **External support** — IR retainer firms (Mandiant, CrowdStrike, Unit 42), forensics specialists.

## Severity classification (typical schema)

| Level | Example | Response |
|---|---|---|
| **Sev 1 / Critical** | Active breach, ransomware, customer data theft | All hands, exec briefing, possibly law enforcement |
| **Sev 2 / High** | Confirmed malware on critical asset, account compromise | IR team activated, 24/7 if needed |
| **Sev 3 / Medium** | Single endpoint malware, low-impact compromise | Standard hours, contained quickly |
| **Sev 4 / Low** | Suspicious but unconfirmed activity, isolated event | Tier 1 handling |

Severity drives who's involved, how fast, and what disclosures may be required.

## Key terms

- **Dwell time / breach time** — how long an attacker was in the environment before detection. Industry average historically measured in months; reducing this is a primary goal.
- **MTTD (Mean Time to Detect)** — average time to detect after compromise.
- **MTTR (Mean Time to Respond / Resolve / Recover)** — depends on definition; clarify which.
- **MTTC (Mean Time to Contain)** — between detection and containment.
- **Blast radius** — scope of impact if not contained.
- **Out-of-band (OOB) communications** — channel separate from your potentially-compromised network (use phone, Signal, separate email tenant during a major breach — your normal email may be monitored).

## Common pitfalls
- **Acting too fast** — destroying evidence by reimaging, blocking attacker without first scoping their access.
- **Acting too slow** — extended dwell time allows the attacker to deepen persistence.
- **Tunnel vision** — focusing on one infected box while attacker is on five others.
- **Poor comms** — execs hear about it from Twitter, not you.
- **No retrospective** — same incident happens again in 6 months.

**Exam framing:** know the phase names (NIST + SANS), what happens in each, and the order. Many CySA+ questions hand you a scenario mid-incident and ask "what phase are you in?" or "what should you do next?"

---

← Back: [02_08 Memory & Data Integrity Vulnerabilities](../02_vulnerability_management/02_08_memory_and_data_integrity_vulnerabilities.md) — Next: [03_01 Attack Methodology Frameworks](03_01_attack_methodology_frameworks.md) →

## Related

**Internal:**
- [03_01 Attack methodology frameworks](03_01_attack_methodology_frameworks.md)
- [03_02 Incident response activities](03_02_incident_response_activities.md)
- [03_03 Preparation and post-incident](03_03_preparation_and_post_incident.md)
- [03_04 Case studies](03_04_case_studies.md)
- [03_05 Forensic artifacts](03_05_forensic_artifacts.md)
- [04_02 Incident reporting](../04_reporting_and_communication/04_02_incident_reporting.md)

**External:**
- [NIST SP 800-61r3 — Incident Response Recommendations (CSF 2.0 Community Profile, April 2025)](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
- [SANS Incident Handler's Handbook](https://www.sans.org/white-papers/33901/)
- [FIRST — Computer Security Incident Response Teams](https://www.first.org/)
