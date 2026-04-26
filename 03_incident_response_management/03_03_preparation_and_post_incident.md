# Preparation & Post-Incident

The two phases that bookend incident response: getting ready *before* anything happens, and learning *after* it does.

## Playbooks

**Playbook** = documented step-by-step procedure for a specific scenario.

### Why playbooks matter
- Consistent response regardless of who's on shift.
- Faster decisions (no thinking from scratch at 3 AM).
- Compliance / audit evidence.
- Onboarding accelerator.
- Foundation for **SOAR automation** (codified human steps → automated workflow).

### Common playbooks every SOC needs
- Phishing email reported by user.
- Malware detection on endpoint (commodity vs. targeted).
- Suspected account compromise.
- Brute-force / password spray.
- DDoS attack.
- Insider data exfiltration.
- Ransomware / wiper attack.
- Web application attack (SQLi, XSS exploitation).
- Cloud account compromise.
- Lost / stolen device.
- Third-party / supply chain incident.

### Anatomy of a good playbook
- **Trigger** — what condition activates this playbook.
- **Severity guidance** — how to classify.
- **Roles** — who does what.
- **Steps** — clear, actionable, in order.
- **Decision points** — branching logic with criteria.
- **Comms templates** — pre-drafted user/exec/customer notifications.
- **Escalation** — when and to whom.
- **Closure criteria** — when is the incident "done"?
- **References** — related runbooks, contacts, tooling.

### Playbook hygiene
- **Version control** — playbooks change as environment changes.
- **Tested regularly** — via tabletop exercises and real incidents.
- **Owned** — someone is accountable for keeping each playbook current.
- **Accessible during an incident** — printed copy or out-of-band system in case prod is down.

## Tabletop exercises (TTX)

**Tabletop exercise** = discussion-based simulation of an incident scenario. Participants walk through their roles and decisions. No real systems touched.

### Purpose
- Test the IR plan and playbooks.
- Find gaps in roles, comms, decision authority.
- Build muscle memory for the team.
- Get cross-functional alignment (legal, PR, exec, IT).
- Check assumptions (e.g., "we'll just call our IR retainer" — but who has that number on a Saturday at 2 AM?).

### Frequency
- At minimum **annually** — most regulated frameworks (PCI DSS, HIPAA, ISO 27001, SOC 2) require it.
- More often (quarterly) for mature programs or after major changes.

### Types of exercises
- **Tabletop** — discussion only.
- **Walkthrough** — slightly more hands-on; review tools and configs.
- **Functional** — execute parts of the response in a sandbox.
- **Full-scale / live-fire** — actually do the actions in a controlled environment.
- **Red team exercise** — adversarial simulation; defenders may or may not be informed.
- **Purple team exercise** — red and blue work together to develop and validate detections.

### Common scenarios for TTX
- Ransomware encrypts file shares.
- Major customer data breach reported by external researcher.
- Insider exfiltrates data before resigning.
- Cloud admin credentials leaked publicly on GitHub.
- Vendor breach exposes our supply chain.
- Wiper malware spreading via AD.

### TTX deliverables
- After-action report.
- Tracked action items with owners and due dates.
- Updated playbooks / IR plan.

## Training

- Analyst skills (tooling, threat hunting, forensics).
- Cross-team training (engineers know basics of how to preserve evidence).
- User awareness (phishing simulations, security training).
- Tabletop exercises double as training.

## Business Continuity (BC) & Disaster Recovery (DR)

These overlap with IR but have a wider scope (any disruption, not just security).

### Business Continuity (BC)
- **Goal:** keep the business operating during a disruption.
- **BCP (Business Continuity Plan)** — how the organization continues key processes.
- Includes: alternate work locations, manual workarounds, communication trees, succession plans.

### Disaster Recovery (DR)
- **Subset of BC** focused on **IT system recovery** after a disaster.
- **DRP (Disaster Recovery Plan)** — how to restore systems and data.

### Key metrics
- **RTO (Recovery Time Objective)** — how long can we be down for system X? (Time-based.)
- **RPO (Recovery Point Objective)** — how much data can we afford to lose for system X? (Data-based — drives backup frequency.)
- **MTD (Maximum Tolerable Downtime)** — absolute worst case before business is unrecoverable.
- **MTTR (Mean Time To Recover)** — average actual recovery time.
- **WRT (Work Recovery Time)** — time after system is up but before it's actually usable for the business.

Example: RTO = 4h, RPO = 15 min for a payment system means you can be down up to 4 hours and lose at most 15 minutes of data → drives architecture (HA, frequent transactional backup or log-shipping).

### BIA (Business Impact Analysis)
- Identifies critical business processes.
- Quantifies impact of disruption (financial, reputational, regulatory, operational).
- Defines RTO/RPO/MTD per process.
- Drives investment priority.

### DR strategies
- **Hot site** — fully operational standby; near-instant cutover. Most expensive.
- **Warm site** — partially configured, needs some work to take over.
- **Cold site** — facilities only; need to bring in equipment and data. Cheapest, slowest.
- **Cloud-based DR** — DRaaS providers (Azure Site Recovery, AWS Elastic Disaster Recovery).
- **Active-active** — both sites serve traffic; failure of one = no downtime. Highest cost.
- **Active-passive** — one serves, other is standby.

### Backups (DR essentials)
- **3-2-1 rule** — 3 copies, 2 media types, 1 offsite.
- **3-2-1-1-0** modern variant — adds 1 immutable/air-gapped copy + 0 errors after verification.
- **Test restores regularly** — backup that hasn't been tested may not work.
- **Protect backups from ransomware** — air-gap, immutability, separate credentials.

### IR vs DR vs BC
| | Trigger | Goal | Scope |
|---|---|---|---|
| **IR** | Security incident | Stop, eradicate, recover from threat | Affected systems |
| **DR** | Major disruption | Restore IT systems | IT infrastructure |
| **BC** | Any business disruption | Keep business running | All operations |

A major ransomware incident invokes all three.

## Forensic analysis

- **Goal:** reconstruct what happened, with admissible evidence if needed.
- Conducted by trained forensic analysts; often outsourced for serious incidents.
- Includes: disk forensics (file system, deleted files, timestamps), memory forensics, network forensics (pcap), mobile forensics, cloud forensics (logs, snapshots, API records).

**Key tools:**
- **Autopsy** / **Sleuth Kit** — open-source disk forensics.
- **EnCase**, **FTK** — commercial.
- **Volatility** — memory forensics.
- **Wireshark**, **NetworkMiner** — network forensics.
- **Plaso / log2timeline + Timesketch** — timeline analysis.

## Lessons learned

After containment + recovery, the **post-incident review** (a.k.a. **lessons learned meeting**, **postmortem**, **after-action review**).

### Timing
- Within **2 weeks** of incident closure (memory still fresh, urgency hasn't faded).

### Participants
- IR team, asset owners, affected users, leadership, sometimes legal.

### Agenda
- Walk through the timeline.
- What worked? What didn't?
- Where did we lose time?
- What did we miss?
- What should we change?

### Output
- **After-action report** documenting timeline, impact, root cause, response actions, lessons.
- **Action items** — concrete improvements with owners and due dates (e.g., "create playbook for X by date Y").
- **Metric updates** — MTTD, MTTR, dwell time.
- **Detection improvements** — new SIEM rules, EDR queries, hunt hypotheses.
- **Process improvements** — playbook updates, comms changes, role clarifications.

### Tone
- **Blameless** — focus on systemic causes, not individuals. People work in systems; bad systems beat good people.
- **Honest** — don't paper over what failed.
- **Actionable** — vague conclusions go nowhere.

## Root cause analysis (RCA)

**RCA** = systematic investigation of *why* an incident occurred at the deepest level.

### Why it matters
Treating symptoms without root causes guarantees repeats. Patching one box doesn't fix the patching process that left it vulnerable for 18 months.

### Common techniques
- **5 Whys** — keep asking "why" until you reach a systemic issue.
  - Example: "Server compromised. Why? Unpatched. Why? Wasn't in the patch group. Why? Inventory missed it. Why? Onboarding script broken. Why? No automated check. → root cause: missing automated inventory validation."
- **Fishbone (Ishikawa) diagram** — categorize possible causes (people, process, technology, environment).
- **Fault tree analysis** — top-down logic tree of contributing factors.
- **Pareto analysis** — 80% of issues from 20% of causes; focus there.
- **Timeline / sequence-of-events** — visualize what happened when.

### Avoid common pitfalls
- **Stopping at "user clicked link"** — that's a symptom. Why was the link reachable? Why did clicking it execute code? Why was the endpoint not detected? Why no MFA? Why no segmentation?
- **Blaming a person** — usually a system failure dressed up.
- **Solving with documentation alone** — "we'll write a procedure" rarely fixes anything by itself.

### RCA → improvement loop
RCA findings should drive concrete changes:
- Detection rules.
- Process changes.
- Tooling investments.
- Training.
- Architecture changes.
- Policy changes.

**Exam tip:** RCA goal = address the *systemic* cause, not just the immediate trigger. "User opened phishing email" is a symptom, not a root cause.

---

← Back: [03_02 Incident Response Activities](03_02_incident_response_activities.md) — Next: [03_04 Case Studies](03_04_case_studies.md) →

## Related

**Internal:**
- [01_16 Process and automation](../01_security_operations/01_16_process_and_automation.md) — playbook authoring
- [03_02 Incident response activities](03_02_incident_response_activities.md) — operational phases
- [03_04 Case studies](03_04_case_studies.md) — practice scenarios
- [04_02 Incident reporting](../04_reporting_and_communication/04_02_incident_reporting.md) — after-action reports

**External:**
- [NIST SP 800-34 — Contingency Planning Guide](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-34r1.pdf)
- [ISO 22301 — Business Continuity](https://www.iso.org/standard/75106.html)
- [SANS — Incident Handler's Handbook](https://www.sans.org/white-papers/33901/)
- [Google SRE — Postmortem culture (blameless RCA)](https://sre.google/sre-book/postmortem-culture/)
