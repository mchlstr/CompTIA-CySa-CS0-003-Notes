# Compensating Control Documentation

- **Control ID:** `<unique identifier>`
- **Date implemented:** `<YYYY-MM-DD>`
- **Owner:** `<name, role>`
- **Linked finding / standard:** `<vuln ID, PCI DSS req, ISO control, etc.>`

---

## Original control requirement

`<state the control that should normally be in place - e.g., "patch CVE-2026-XXXX within 7 days per PCI DSS 6.3.3">`

## Why the original control isn't feasible

`<concrete reason: vendor EOL, business-critical legacy system, no maintenance window available, change freeze, etc.>`

## Compensating control implemented

`<describe the alternative measure - e.g., "Web Application Firewall rule blocking known exploit patterns; vulnerable service moved to isolated VLAN with strict ACL; enhanced SIEM detection on related TTPs">`

## How it meets intent

`<explain how the compensating control addresses the same risk the original control would have addressed; reference equivalent rigor - for PCI: meet or exceed the rigor of the original requirement>`

## Effectiveness assessment

- **What attacks does this stop?** `<list>`
- **What attacks does this NOT stop?** `<list - be honest>`
- **How is effectiveness measured?** `<monitoring, periodic testing, log review cadence>`

## Residual risk

`<what remains after the compensating control; level (Low / Medium / High); accepted by whom>`

## Validation

| Date | Method | Result | Validated by |
|---|---|---|---|
| `<YYYY-MM-DD>` | `<scan / pen test / config review>` | Pass / Fail | `<name>` |

## Re-evaluation

- **Next review date:** `<YYYY-MM-DD>`
- **Trigger for early review:** `<e.g., vendor releases patch; environment change; new threat intel>`
- **Plan to retire compensating control:** `<when and how the original control will be implemented>`

## Approvals

| Role | Name | Date |
|---|---|---|
| Control owner | | |
| Information Security | | |
| Compliance / Audit | | |

---

## References

This template is **directly modelled on PCI DSS v4.x Appendix E - "Compensating Controls Worksheet"**, which is the most prescriptive industry-standard format for documenting a compensating control. The worksheet is part of the official PCI DSS standard document.

- **PCI DSS v4.x - Compensating Controls Worksheet** (Appendix E in v4.0.1): download from PCI SSC Document Library at https://www.pcisecuritystandards.org/document_library/ (free registration required to download the standard)
- **PCI DSS - guidance on compensating controls** (Appendix B in earlier versions / Appendix E in v4.x): defines the four criteria - meet intent, meet rigor, provide similar level of defence, be commensurate with the additional risk imposed by not adhering to the requirement.
- **NIST SP 800-53 Rev. 5 - Security and Privacy Controls** - recognises compensating controls as a category and requires documentation: https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final
- **ISO/IEC 27001 Annex A** - requires control deviations to be documented and approved.
