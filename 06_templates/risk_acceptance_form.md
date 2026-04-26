# Risk Acceptance Form

**Risk ID:** `<unique identifier>`
**Date submitted:** `<YYYY-MM-DD>`
**Requested by:** `<name, role>`
**Owner / sponsor:** `<accountable manager>`

---

## Risk description

**Source finding(s):** `<vulnerability ID, audit finding, threat assessment reference>`

**Description:** `<plain-language description of the risk being accepted — what could happen?>`

**Affected assets / scope:** `<systems, data, business processes, geographies>`

## Risk assessment

| Dimension | Rating | Notes |
|---|---|---|
| Likelihood | High / Medium / Low | `<basis>` |
| Impact | High / Medium / Low | `<basis>` |
| CVSS / EPSS / KEV | `<scores>` | |
| Inherent risk | `<level>` | Before any controls |
| Residual risk | `<level>` | After existing controls and any compensating measures |

## Why mitigation isn't feasible

`<choose all that apply and explain>`
- [ ] Vendor end-of-life; no patch available
- [ ] Patching would break critical functionality
- [ ] Cost of mitigation exceeds expected loss
- [ ] Business deadline conflict
- [ ] Dependency on third-party action
- [ ] Other: `<specify>`

## Compensating controls in place

- `<control 1 — e.g., network segmentation isolating the asset>`
- `<control 2 — e.g., enhanced monitoring with alert on anomalous access>`
- `<control 3 — e.g., MFA required for any access>`

## Residual risk statement

`<one paragraph: what we are accepting, the worst-case outcome, why the business is choosing to accept it>`

## Re-evaluation date

`<YYYY-MM-DD — typically 6 or 12 months>`

## Approvals

| Role | Name | Decision | Date | Signature |
|---|---|---|---|---|
| Asset owner | | Accept / Mitigate / Transfer / Avoid | | |
| Information Security | | Approved / Rejected | | |
| Risk Officer / CRO | | Approved / Rejected | | |
| Executive sponsor | | Approved / Rejected | | |

## Notes

`<additional context, exceptions, conditions of acceptance, monitoring requirements>`

---

## References

Risk acceptance documentation isn't standardised in a single template — every framework expects "documented risk acceptance with appropriate authority sign-off" but leaves the form to the organisation. This template synthesises the requirements common to:

- **NIST SP 800-39 — Managing Information Security Risk** (organisation, mission, system levels): https://csrc.nist.gov/publications/detail/sp/800-39/final
- **NIST SP 800-37 Rev. 2 — Risk Management Framework** (Authorize step requires documented risk acceptance by an Authorizing Official): https://csrc.nist.gov/publications/detail/sp/800-37/rev-2/final
- **NIST SP 800-30 Rev. 1 — Guide for Conducting Risk Assessments** (likelihood × impact tables): https://csrc.nist.gov/publications/detail/sp/800-30/rev-1/final
- **ISO/IEC 27005 — Information security risk management** (risk treatment options including acceptance): https://www.iso.org/standard/80585.html
- **ISO 31000 — Risk Management Guidelines**: https://www.iso.org/iso-31000-risk-management.html
- **PCI DSS v4.x** — Requirement 12.3.1 mandates a documented risk analysis for any case where a control is not met as designed.
