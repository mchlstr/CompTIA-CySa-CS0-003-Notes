# Attack Methodology Frameworks

These frameworks give analysts a shared vocabulary for *how attacks unfold*. CySA+ tests recognition of each and where to apply them.

## Cyber Kill Chain (Lockheed Martin)

A linear, 7-stage model of an intrusion. Originally military; adapted to cyber in 2011.

1. **Reconnaissance** — research target (OSINT, scanning, social media).
2. **Weaponization** — couple exploit with payload (malicious doc + macro, exploit + RAT).
3. **Delivery** — get the payload to the target (email, USB, watering hole, exposed service).
4. **Exploitation** — payload runs, exploits a vulnerability.
5. **Installation** — install malware / backdoor for persistence.
6. **Command & Control (C2)** — attacker's malware beacons home; attacker controls remotely.
7. **Actions on Objectives** — exfiltrate data, encrypt for ransom, sabotage, lateral move.

**Defensive use:** map controls to each stage. Block at *any* stage breaks the chain. Earlier blocks are cheaper than later.

**Limitations:**
- Linear; doesn't represent loops or parallel paths.
- Doesn't capture insider threats well (no Recon → Delivery for an insider).
- "Exploitation" assumes a vuln; many attacks today use stolen credentials, no exploit needed.

## MITRE ATT&CK

A **knowledge base** of attacker tactics, techniques, and procedures, derived from real-world observations. Way more granular than the Kill Chain.

### Structure
- **Tactics** (the *why* — attacker's goal). Currently 14 in Enterprise matrix:
  - Reconnaissance, Resource Development, Initial Access, Execution, Persistence, Privilege Escalation, Defense Evasion, Credential Access, Discovery, Lateral Movement, Collection, Command and Control, Exfiltration, Impact.
- **Techniques** (the *how* — general method, e.g., T1059 Command-Line Interface).
- **Sub-techniques** (the *specifically how* — e.g., T1059.001 PowerShell).
- **Procedures** (specific implementations, often actor-attributed).

### Matrices
- **Enterprise** (Windows, macOS, Linux, cloud, containers).
- **Mobile** (iOS, Android).
- **ICS** (industrial control systems).

### Why it dominates
- Living catalog updated continuously.
- Maps to detections, intel, and red team actions.
- Used for hunting, gap analysis, adversary emulation, vendor product evaluation.

### Use cases
- **Detection coverage assessment** — which techniques can my SIEM/EDR detect?
- **Threat hunting** — pick a technique, hypothesize, search.
- **Adversary emulation** — replay specific actor TTPs (Caldera, Atomic Red Team).
- **Threat intel** — describe actors and campaigns in a common language.

**Companion tool:** **MITRE D3FEND** — defensive countermeasures mapped to attack techniques.

**Exam tip:** know that ATT&CK = behaviors, not signatures. Tactic / Technique / Procedure (TTP) terminology comes from here.

## Diamond Model of Intrusion Analysis

A model for representing a single intrusion event. Four "vertices" of a diamond:

- **Adversary** — who.
- **Capability** — what tools / TTPs they used.
- **Infrastructure** — what systems they used (C2 servers, domains).
- **Victim** — who/what they targeted.

Plus **meta-features:** timestamp, phase, result, direction, methodology, resources.

**Use case:** structured intrusion analysis. As you discover one vertex (e.g., a malicious IP = infrastructure), you can pivot to discover others (other victims sharing that infra, capabilities used from it). Ideal for analytical pivoting.

**vs Kill Chain:** Kill Chain models the *sequence* of an attack. Diamond models the *components* of a single intrusion. They complement each other (Diamond instances strung along Kill Chain stages).

## OSSTMM (Open Source Security Testing Methodology Manual)

A formal **security testing methodology** by ISECOM. Structured, scientific, repeatable.

- Covers physical, human, telecom, data, wireless testing.
- Defines metrics (RAVs — Risk Assessment Values) for objective comparison.
- Less commonly used in practice than PTES, but referenced for academic / formal engagements.

**Exam relevance:** know it's a *security testing* methodology, distinct from incident response.

## OWASP Testing Guide / WSTG (Web Security Testing Guide)

A comprehensive guide to **web application security testing**. Maps to OWASP Top 10 categories with detailed test cases.

### Structure
- **Information gathering** — fingerprinting, enumeration.
- **Configuration & deployment management testing**.
- **Identity management testing**.
- **Authentication testing**.
- **Authorization testing**.
- **Session management testing**.
- **Input validation testing** — XSS, SQLi, SSRF, etc.
- **Error handling**.
- **Cryptography**.
- **Business logic testing**.
- **Client-side testing**.

**Use case:** standard reference for web app pen testers and DAST tools.

**Companion:** **OWASP ASVS (Application Security Verification Standard)** — what *should* be true (requirements), where WSTG is *how to test*.

## How they relate

| Framework | Purpose | When you use it |
|---|---|---|
| Cyber Kill Chain | Sequence of attack stages | Defensive planning, tabletop |
| MITRE ATT&CK | Catalog of TTPs | Hunting, detection coverage, intel |
| Diamond Model | Single intrusion components | Analyst pivoting, attribution |
| OSSTMM | Security testing methodology | Formal pen test engagement |
| OWASP Testing Guide | Web app testing methodology | Web app pen test |

**Exam tip:** the test loves "which framework would you use for X?" → know which is *for* sequence vs. catalog vs. analysis vs. testing.

## Related

**Internal:**
- [01_13 Threat intelligence](../01_security_operations/01_13_threat_intelligence.md) — TTPs, actors
- [01_14 Threat hunting](../01_security_operations/01_14_threat_hunting.md) — ATT&CK-driven hunting
- [02_05 Attack surface and threat modeling](../02_vulnerability_management/02_05_attack_surface_and_threat_modeling.md) — STRIDE/DREAD/PASTA
- [03_02 Incident response activities](03_02_incident_response_activities.md) — applying frameworks during IR

**External:**
- [Lockheed Martin — Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [MITRE D3FEND](https://d3fend.mitre.org/)
- [Diamond Model paper (Caltagirone, Pendergast, Betz)](https://www.activeresponse.org/wp-content/uploads/2013/07/diamond.pdf)
- [OSSTMM (ISECOM)](https://www.isecom.org/OSSTMM.3.pdf)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
