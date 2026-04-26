# Templates

Reusable fill-in-the-blank templates for common SOC / VM / IR documents. Copy a template, replace `<placeholders>`, and adapt to your org.

## Vulnerability management
- [Executive vulnerability summary](vulnerability_report_executive.md) — 1-page leadership view.
- [Technical vulnerability finding](vulnerability_report_technical.md) — per-finding detail.
- [Risk acceptance form](risk_acceptance_form.md) — formal sign-off when not remediating.
- [Compensating control documentation](compensating_control.md) — when patching isn't feasible.

## Incident response
- [Incident status update](incident_status_update.md) — used during an active incident.
- [Incident final report / after-action](incident_final_report.md) — post-incident write-up.
- [Customer breach notification](customer_breach_notification.md) — external comms (legal-approved).

---

## On these templates

There is **no single universally-mandated template** for most of these documents. Each file in this folder is a **synthesis of common industry practice** based on the standards and published examples linked at the bottom of each file. Organisations customise heavily based on their sector, regulator, tooling, and culture.

A few are directly derived from a specific standard:
- The **compensating control worksheet** mirrors the structure required by **PCI DSS v4.x Appendix E** ("Compensating Controls Worksheet").
- The **vulnerability finding** structure mirrors **CVE/NVD** entries and **CVSS** vector formatting.
- The **incident final report** follows **NIST SP 800-61r2 Appendix A** sample format.
- The **breach notification** follows requirements in **GDPR Art. 34**, US state breach laws, and HIPAA — see California AG's [public list of submitted notices](https://oag.ca.gov/privacy/databreach/list) for real examples.

Always check what your specific regulator, framework, or contract requires before sending anything externally.
