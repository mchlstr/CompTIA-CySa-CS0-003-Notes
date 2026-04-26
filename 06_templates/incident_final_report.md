# Incident Final Report — `<Incident ID>`

- **Title:** `<short descriptive title>`
- **Severity (final):** `<Sev N>`
- **Status:** Closed
- **Date opened:** `<YYYY-MM-DD HH:MM TZ>`
- **Date closed:** `<YYYY-MM-DD HH:MM TZ>`
- **Report author:** `<name>`
- **Report version:** `<x.y>`
- **Distribution:** `<internal-only | restricted | redacted version for external>`

---

## Executive summary

`<one paragraph, plain language: what happened, what was affected, what we did, current state, what we changed as a result. Written for non-technical leadership.>`

---

## Incident overview

- **Detection source:** `<SIEM rule | EDR alert | user report | external notification | hunt>`
- **Initial compromise vector (root cause):** `<phishing | exposed service | credential reuse | supply chain | etc.>`
- **Affected systems:** `<list — hostnames, services, accounts>`
- **Affected data:** `<categories, volume, regulated?>`
- **Affected users / customers:** `<approximate count, who>`
- **Threat actor (if known/suspected):** `<actor or "unknown">`
- **Attribution confidence:** Low / Medium / High

## Key time markers

| Event | Time | Source |
|---|---|---|
| Initial compromise (estimated) | `<YYYY-MM-DD HH:MM>` | `<evidence>` |
| First detection | `<YYYY-MM-DD HH:MM>` | `<source>` |
| Incident declared | `<YYYY-MM-DD HH:MM>` | `<who>` |
| Containment achieved | `<YYYY-MM-DD HH:MM>` | |
| Eradication complete | `<YYYY-MM-DD HH:MM>` | |
| Recovery complete | `<YYYY-MM-DD HH:MM>` | |
| Incident closed | `<YYYY-MM-DD HH:MM>` | |

**Dwell time:** `<initial compromise → eradication>`
**MTTD:** `<compromise → detection>`
**MTTC:** `<detection → containment>`

## Timeline (chronological)

| Time | Event / action | Source / evidence |
|---|---|---|
| `<YYYY-MM-DD HH:MM>` | `<what happened or was done>` | `<log, ticket, comm>` |

## Root cause analysis

**Immediate trigger:** `<the proximate event>`

**Contributing factors:** `<process, technology, people>`

**5 Whys (or chosen RCA method):**
1. `<problem statement>`
2. Why? `<answer>`
3. Why? `<answer>`
4. Why? `<answer>`
5. Why? `<systemic cause>`

**Systemic root cause:** `<the underlying organizational / architectural / process issue>`

## Scope and impact

- **Hosts compromised:** `<n>` (`<list or appendix reference>`)
- **Accounts compromised:** `<n>`
- **Data exfiltrated:** `<volume, type, confirmed vs suspected>`
- **Operational impact:** `<downtime, degraded service, customer-facing impact>`
- **Financial impact (estimate):** direct `<$>` + indirect `<$>`
- **Regulatory exposure:** `<frameworks triggered: GDPR, HIPAA, SEC, etc.>`
- **Reputational impact:** `<media coverage, customer escalations>`

## Response actions

### Containment
`<what was done, when, by whom>`

### Eradication
`<what was done, when, by whom>`

### Recovery
`<what was done, when, by whom — including validation>`

## Communications log

| Time | Audience | Channel | Sent by | Notes |
|---|---|---|---|---|
| `<HH:MM>` | `<exec / customers / regulator / LE>` | `<email / call / public>` | `<name>` | `<subject / outcome>` |

## Evidence preserved

| Item | Source | Hash (SHA-256) | Custodian | Retention |
|---|---|---|---|---|
| `<image / pcap / log export>` | `<host / system>` | `<hash>` | `<name>` | `<duration>` |

## What worked well

- `<observation>`

## What didn't work / gaps

- `<observation>`

## Action items

| # | Action | Owner | Due date | Status |
|---|---|---|---|---|
| 1 | `<concrete improvement>` | `<name>` | `<YYYY-MM-DD>` | Open |

## Lessons learned

`<narrative — what the org should take away beyond the action items>`

## IOCs (for sharing with ISAC / partners)

- **Hashes:** `<list>`
- **IPs / domains:** `<list>`
- **TTPs (MITRE ATT&CK):** `<technique IDs>`

## Appendices

- A: detailed forensic findings
- B: malware analysis report
- C: full evidence log
- D: customer / regulator notifications sent

---

## References

This template follows the structure recommended in **NIST SP 800-61r3** and the SANS PICERL framework. It mirrors the reporting structure expected by CISA and most regulators.

- **NIST SP 800-61r3 — Incident Response Recommendations and Considerations for Cybersecurity Risk Management (CSF 2.0 Community Profile, April 2025)**: https://csrc.nist.gov/pubs/sp/800/61/r3/final
- **SANS Incident Handler's Handbook (PICERL)**: https://www.sans.org/white-papers/33901/
- **ENISA — Good Practice Guide for Incident Management**: https://www.enisa.europa.eu/publications/good-practice-guide-for-incident-management
- **CISA — Incident Reporting** (federal incident notification format): https://www.cisa.gov/topics/cyber-threats-and-advisories/information-sharing/cisa-coordinated-vulnerability-disclosure-process
- **DFIR Report** — public real-world intrusion reports follow a similar structure: https://thedfirreport.com/
- **MITRE ATT&CK** for IOC / TTP appendix structure: https://attack.mitre.org/
- **Verizon DBIR methodology** (VERIS — Vocabulary for Event Recording and Incident Sharing) is a more formal taxonomy if you want machine-readable incidents: http://veriscommunity.net/
