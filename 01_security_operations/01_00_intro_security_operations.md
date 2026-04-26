# Intro: Security Operations

Domain 1 is the largest weight on the CySA+ exam (~33%). It covers everything a SOC analyst touches: knowing what your environment looks like, watching it, and recognizing when something's wrong.

## Overview of SecOps

**Security Operations (SecOps)** = the day-to-day work of detecting, investigating, and responding to threats against an organization's IT environment. The team that does this is the **SOC (Security Operations Center)**.

Core SOC functions:
- **Monitor** — collect logs, alerts, network traffic.
- **Detect** — spot anomalies and known-bad patterns.
- **Investigate** — triage alerts, separate true positives from false.
- **Respond** — contain, eradicate, recover from incidents.
- **Improve** — feed lessons back into detections, playbooks, controls.

SOC tiers:
- **Tier 1** — alert triage, basic enrichment.
- **Tier 2** — deeper investigation, IR.
- **Tier 3** — threat hunting, advanced analysis, tooling.

## Importance of system/network architecture

You cannot defend what you don't understand. CySA+ emphasizes that an analyst must know:

- **What assets exist** (CMDB, asset inventory).
- **How they're connected** (network diagrams, data flows).
- **What's normal traffic** (baselines) — anomalies only stand out against a known-good reference.
- **Where the crown jewels live** (sensitive data stores, domain controllers, payment systems).

Without this context, alerts are noise. With it, you can prioritize: an unusual outbound connection from a database server matters more than the same from a guest Wi-Fi.

## Layers of defense (defense-in-depth)

The principle: no single control is sufficient — overlap them so a failure in one is caught by another.

Typical layers, outside-in:
1. **Physical** — locks, badges, cameras.
2. **Perimeter** — firewalls, IPS, WAF, DDoS protection.
3. **Network** — segmentation, VLANs, NAC, internal IDS.
4. **Endpoint** — EDR, AV, host firewall, hardening.
5. **Application** — secure coding, input validation, RASP.
6. **Data** — encryption at rest/in transit, DLP, access control.
7. **Identity** — MFA, least privilege, PAM.
8. **Administrative** — policies, training, audits.

**Exam angle:** when asked "best control to add" — look for what *layer* is missing, not just what's powerful. Adding another firewall when the gap is endpoint detection won't help.

## Related

**Internal:**
- [01_01 OS basics](01_01_os_basics.md) — start of the architecture cluster
- [01_09 Logs and monitoring](01_09_logs_and_monitoring.md) — the SOC's primary console
- [01_10 Malicious activity detection](01_10_malicious_activity_detection.md) — what the SOC is watching for
- [01_16 Process and automation](01_16_process_and_automation.md) — how the SOC scales

**External:**
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework) — Identify / Protect / Detect / Respond / Recover
- [SANS SOC Survey](https://www.sans.org/white-papers/) — annual SOC capability benchmarks
