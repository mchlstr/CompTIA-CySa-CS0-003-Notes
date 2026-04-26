# Process & Automation

SOCs drown in alerts and toil. Automation and process improvement are how teams scale without hiring linearly.

## Efficiency & process improvement

**The problem:** alert fatigue, repetitive manual work, slow MTTR (mean time to respond), inconsistent handling.

**Improvement frameworks:**
- **Lean** — eliminate waste in workflows.
- **Six Sigma** — reduce defects/variance; data-driven.
- **Kaizen** — continuous incremental improvement.
- **PDCA (Plan-Do-Check-Act / Deming cycle)** — iterative improvement loop.

**Common SOC improvements:**
- **Tune alerts** — most SIEM rules out-of-the-box are too noisy. Tune to your environment.
- **Reduce false positives** — every FP investigated is time lost. Suppress, refine, or correlate to filter.
- **Standardize triage** — playbooks, decision trees so Tier 1 doesn't reinvent the wheel.
- **Eliminate repetitive tasks** — automate everything done >5x/day.
- **Measure** — MTTD, MTTR, dwell time, alert volume, FP rate, escalation rate. Improve what you measure.

## SOAR (Security Orchestration, Automation, and Response)

**SOAR** = platform that orchestrates security tools, automates workflows, and manages cases.

Three pillars:
- **Orchestration** — connect disparate tools (SIEM, EDR, firewall, ticketing, threat intel) via APIs.
- **Automation** — execute predefined actions without human intervention (block IP, isolate host, enrich alert).
- **Response** — manage cases, track incidents, coordinate teams.

**Major products:** Splunk SOAR (Phantom), Palo Alto Cortex XSOAR, IBM Resilient, Tines, Swimlane, Microsoft Sentinel automation rules.

**Typical SOAR playbook (phishing):**
1. SIEM alerts on suspicious email.
2. SOAR pulls the email, extracts URLs and attachments.
3. Detonates attachments in a sandbox.
4. Submits URLs/hashes to VirusTotal, URLscan.
5. Checks recipient mailboxes for similar emails.
6. If malicious: deletes the email org-wide, blocks sender, opens incident ticket, notifies user.
7. Whole flow in seconds, no analyst touch.

**Where SOAR shines:** repetitive, well-defined workflows (phishing triage, IOC enrichment, password resets, account lockout investigations).

**Where SOAR struggles:** novel attacks, judgment calls, anything requiring real human investigation.

## Data enrichment & feed combination

**Enrichment** = adding context to raw alerts so analysts can decide quickly.

**Common enrichments:**
- **IP** → geolocation, ASN, reputation (AbuseIPDB, Cisco Talos), passive DNS, prior incidents.
- **Domain** → WHOIS, age, certificate transparency, Alexa/Tranco rank, threat feeds.
- **Hash** → VirusTotal, Hybrid Analysis, internal sandbox results.
- **User** → department, manager, normal login patterns, prior incidents.
- **Asset** → criticality, owner, location, OS version, patch level.

**Feed combination:** correlate multiple intel feeds to raise/lower confidence. A hash flagged by 1/70 AVs vs 60/70 means very different things.

**TIPs (Threat Intelligence Platforms):** MISP, Anomali, ThreatConnect, OpenCTI, Recorded Future. They aggregate, deduplicate, score, and distribute intel.

## Single pane of glass

**Single pane of glass (SPOG)** = one console where analysts see everything they need (alerts, telemetry, cases, intel) instead of swivel-chairing between 10 tools.

**Why it matters:**
- Faster decisions — no context-switch tax.
- Less missed correlation across tool silos.
- Easier onboarding (fewer UIs to learn).

**How it's typically achieved:**
- **SIEM/XDR as the hub** — pull data from everywhere into one search/correlation engine.
- **SOAR as the workflow layer** — case management, action triggering.
- **Custom dashboards** — Grafana, Splunk dashboards, Sentinel workbooks.

**Reality check:** "single pane of glass" is more aspiration than reality at most orgs. Even with SIEM + SOAR + EDR consolidated, analysts still touch the email gateway, IdP console, and cloud portals separately.

## Workflow standardization

**Playbooks / runbooks** — documented step-by-step procedures for handling specific scenarios.
- **Playbook** = the higher-level "what we do for X type of incident" (often holds the SOAR automation).
- **Runbook** = the operator's checklist of detailed steps.

**Why standardize:**
- Consistent quality regardless of who's on shift.
- Faster response (no thinking from scratch).
- Easier audit / compliance evidence.
- Onboarding accelerator for new analysts.

**Typical playbooks every SOC has:**
- Phishing reported by user.
- Malware detection on endpoint.
- Suspected account compromise.
- Brute-force / password spray.
- DDoS detected.
- Insider data exfiltration suspected.
- Ransomware detection.

**Exam angle:** if a question asks how to scale a SOC without hiring more analysts → SOAR + playbooks + tuning. If it asks why MTTR is high → unstandardized response, no playbooks, alert fatigue.
