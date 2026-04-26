# Incident Reporting

The companion to incident response: how you communicate during and after an incident, internally and externally.

## Incident declaration

**When does an event become an incident?** This must be defined *before* something happens. Common criteria:

- **Confirmed unauthorized access** to a system or data.
- **Confirmed malware** beyond what AV cleaned automatically.
- **Suspected breach** even if not confirmed.
- **Significant operational disruption** caused by a security event.
- **Regulatory reporting threshold** crossed (e.g., PII potentially exposed).
- **Threshold exceeded** in monitoring (e.g., > N affected hosts).

**Who can declare:**
- Documented in IR plan — typically IR lead, SOC manager, or on-call analyst with escalation.
- Avoid both extremes: under-declaring (incidents handled informally, lessons lost) and over-declaring (declaration fatigue, exec frustration).

## Escalation

Once declared, escalate per severity. Document an **escalation matrix** in the IR plan.

### Typical levels
- **Tier 1 (Analyst)** — initial triage, low-severity handling, escalates if needed.
- **Tier 2 (Senior Analyst / IR Lead)** — investigation, coordination.
- **Tier 3 (IR Manager / CISO)** — major incidents, cross-team coordination.
- **Executive (CIO/CEO/Board)** — Sev 1 incidents, regulatory reportable, public visibility.
- **Legal / Privacy** — breach notification triggers, evidence handling, law enforcement contact.
- **PR / Comms** — media-attention or customer-impact incidents.
- **Customer Success** — customer-affecting incidents.
- **External IR retainer** — major incidents requiring expert help.
- **Law enforcement** — depends on threat type and jurisdiction.

### Escalation triggers
- Severity rises (Sev 3 → Sev 1).
- Reportable threshold met (regulatory clock starts).
- Resource needed beyond current authority (e.g., shut down prod system, pay ransom — exec only).
- Time elapsed without resolution (default escalation after X hours).

## Reporting during the incident

### Internal status updates
- **Cadence** — frequent for active incidents (e.g., every 30-60 min during active phase, daily during containment/recovery).
- **Format** — concise, structured, factual.
- **Audience-tailored** — exec brief vs. operational detail.
- **Single source of truth** — one document/channel everyone references; prevents conflicting versions.
- **Out-of-band channel** — if normal email/chat may be compromised, use phone bridge, Signal, or separate cloud tenant.

### Sample status update structure
- **Incident ID / name**.
- **Status** — Active / Contained / Eradicated / Recovered / Closed.
- **Severity**.
- **Summary** — 2-3 sentences, plain language.
- **Timeline** — what's happened so far.
- **Current actions** — what's being done now.
- **Next steps** — what's planned.
- **Decisions needed** — if any.
- **Next update time**.

## Reporting to external parties

### Regulators
Depends on what data was affected and where you operate. Common ones:

- **GDPR** (EU) — supervisory authority within **72 hours** of becoming aware of a personal data breach. Affected individuals "without undue delay" if high risk.
- **HIPAA Breach Notification Rule** (US health) — individuals within **60 days** for breaches of unsecured PHI; HHS for breaches affecting ≥500 individuals immediately, smaller ones annually.
- **US state breach notification laws** — every US state has one; vary on threshold, timing, and notification details. CCPA / CPRA in California particularly notable.
- **SEC cybersecurity rule** (US, 2023) — public companies must disclose material cybersecurity incidents within **4 business days** on Form 8-K.
- **CIRCIA** (US critical infrastructure, when fully implemented) — 72 hours for incident, 24 hours for ransomware payment.
- **NIS2 Directive** (EU) — early warning within 24 hours, full notification within 72 hours, final report within 1 month for essential/important entities.
- **PCI DSS** — incident reporting to acquirer, card brands.
- **Sector-specific** — NERC CIP (electricity), FFIEC (banking), etc.

**Key principle:** the clock often starts when you *become aware* (or "should have been aware"), not when you're certain. Talk to legal early.

### Customers
- Contractual obligations (most B2B contracts require breach notification within X days/hours).
- Brand / trust considerations.
- Operational impact disclosures.
- Coordinate with PR.

### Law enforcement
- **FBI / IC3** (US), **NCSC** (UK), **CSE** (Canada), **BSI** (Germany), **ANSSI** (France), local CSIRT.
- Helpful for: nation-state attacks, ransomware (sanctions screening), large-scale fraud, intellectual property theft.
- Considerations: they may have IOCs/intel useful to you; they may want evidence preserved in specific ways; investigations can take time.

### Public / media
- **PR-led, legal-approved** statements.
- Avoid speculation; share what's confirmed.
- Don't promise what you haven't verified.
- Coordinate timing with regulator and customer notifications.

## Communication: do's and don'ts

### Do
- Be **factual and timely**.
- **Acknowledge unknowns** — "we don't yet know X, expect to know by Y."
- **Coordinate** — legal, PR, leadership aligned before external comms.
- **Document everything** — what was said, when, to whom, by whom.
- **Use templates** — pre-drafted notification templates save time during a crisis.

### Don't
- **Speculate** about cause, attribution, scope before confirmed.
- **Underplay** — "limited impact" before scoping is complete.
- **Communicate via potentially-compromised channels** (use OOB).
- **Delay legally required notifications** to "look better."
- **Blame individuals** in public comms (or internally — keep it blameless).

## After-action / final incident report

The post-incident report consolidates what happened and what was done. Consumed by leadership, sometimes auditors, regulators, customers, courts.

### Standard structure

#### 1. Executive summary
- 1-page max. What happened, impact, what we did, what we learned, status.
- For non-technical readers.

#### 2. Incident overview
- **Detection** — how, when, by whom.
- **Severity** — final classification.
- **Affected systems / data / users** — scope.
- **Duration** — start (initial compromise), detection, containment, eradication, full recovery.

#### 3. Timeline
- Chronological events: detection, escalation, containment actions, comms, eradication, recovery.
- Time-stamped, sourced (logs, ticket entries, comms records).
- Often the most useful section for lessons-learned.

#### 4. Root cause analysis
- The systemic *why*, not just the trigger.
- Contributing factors (process, technology, people).
- Use 5 Whys / fishbone if formal RCA done.

#### 5. Scope & evidence
- What was accessed, modified, exfiltrated.
- Evidence sources used to determine scope (logs, packet captures, EDR telemetry, forensic images).
- Chain of custody references.
- Hashes of preserved evidence.

#### 6. Response actions
- What we did, in what order.
- What worked, what didn't.
- Containment, eradication, recovery details.

#### 7. Communications log
- Who was notified, when, by whom.
- External notifications (regulators, customers, law enforcement).
- Public statements made.

#### 8. Lessons learned
- What went well.
- What didn't.
- What we'd do differently.

#### 9. Action items
- Concrete improvements with **owner**, **due date**, **success criteria**.
- Tracked to closure separately.

#### 10. Appendices
- IOCs (consider sharing with ISAC/ISAO, sector partners).
- Sample malware analysis.
- Detailed forensic findings.
- Supporting screenshots / log excerpts.

### Distribution
- Internal: leadership, IR team, asset owners, audit/compliance.
- External: regulators (per requirements), customers (where contractual), partners.
- **Versioning**: redacted versions for broader distribution; full version restricted.

## Metrics & KPIs (incident side)

Numbers that quantify IR program health:

- **MTTD (Mean Time to Detect)** — compromise → detection. Lower = better.
- **MTTC (Mean Time to Contain)** — detection → containment.
- **MTTR (Mean Time to Recover)** — detection → full recovery.
- **Dwell time** — initial compromise → eradication. Industry trends are improving but still measured in days/weeks.
- **% of incidents detected by internal vs. external** — high external % = monitoring gap.
- **# of incidents by category** — phishing, malware, account compromise, web attack, insider, etc.
- **# of repeat causes** — same root cause recurring.
- **% of action items completed on time** post-incident.
- **Cost per incident** — direct (response, recovery) + indirect (downtime, customer impact, regulatory).

### Example targets
- Critical: detect within 1 hour, contain within 4 hours.
- High: detect within 4 hours, contain within 24.
- All Sev 1/2: post-incident review within 2 weeks; action items 90% closed within 90 days.

**Exam tip:** know the **time clocks** — GDPR 72h, HIPAA 60 days, SEC 4 business days. If a scenario mentions a regulatory regime + breach, the clock matters and is often the answer.

## Related

**Internal:**
- [03_02 Incident response activities](../03_incident_response_management/03_02_incident_response_activities.md) — what's being reported on
- [03_03 Preparation and post-incident](../03_incident_response_management/03_03_preparation_and_post_incident.md) — playbooks, BC/DR
- [03_05 Forensic artifacts](../03_incident_response_management/03_05_forensic_artifacts.md) — evidence in reports
- [04_01 Vulnerability reporting](04_01_vulnerability_reporting.md) — sibling reporting domain

**Templates:**
- [Incident status update](../06_templates/incident_status_update.md)
- [Incident final report / after-action](../06_templates/incident_final_report.md)
- [Customer breach notification](../06_templates/customer_breach_notification.md)

**External:**
- [GDPR Art. 33 — Notification of personal data breach](https://gdpr-info.eu/art-33-gdpr/)
- [HHS HIPAA Breach Notification Rule](https://www.hhs.gov/hipaa/for-professionals/breach-notification/index.html)
- [SEC cybersecurity disclosure rule (2023)](https://www.sec.gov/news/press-release/2023-139)
- [NIS2 Directive (EU)](https://www.nis-2-directive.com/)
- [CISA — Reporting](https://www.cisa.gov/report)
- [FBI IC3](https://www.ic3.gov/)
- [State data breach notification laws (NCSL)](https://www.ncsl.org/technology-and-communication/security-breach-notification-laws)
