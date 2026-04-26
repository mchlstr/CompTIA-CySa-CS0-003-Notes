# System Hardening & Configuration Management

## System hardening techniques

**Hardening** = reducing attack surface by removing/disabling anything not strictly needed and tightening what remains.

Standard hardening checklist:
- **Remove unused software/services** — fewer running services = fewer exploit targets.
- **Disable unused ports/protocols** — close unnecessary network listeners.
- **Patch the OS and applications** — known CVEs are the most common entry point.
- **Apply secure baselines** — CIS Benchmarks, DISA STIGs, vendor hardening guides.
- **Enforce strong authentication** — MFA, strong passwords, lockout policies.
- **Apply least privilege** — users/services get only the permissions they need.
- **Disable default accounts** or rename/repassword them.
- **Enable host firewall** — block inbound by default.
- **Enable logging/auditing** — you can't investigate what you didn't log.
- **Encrypt data at rest** — BitLocker, LUKS, FileVault.
- **Application allowlisting** — AppLocker, WDAC; far stronger than blocklisting.
- **Use EDR/AV** with tamper protection.

**Common baselines:**
- **CIS Benchmarks** — vendor-neutral, widely respected, tiered (Level 1 = practical, Level 2 = high-security).
- **DISA STIGs** — US Department of Defense hardening guides, very strict.
- **Microsoft Security Compliance Toolkit** — applies recommended GPOs.

## Configuration management

**Configuration management (CM)** = tracking and controlling system configurations so you know what "approved" looks like and detect drift.

Key concepts:
- **Baseline configuration** — the documented, approved state of a system.
- **Configuration drift** — when actual state differs from baseline (manual changes, failed updates, malicious tampering).
- **CMDB (Configuration Management Database)** — central record of all configuration items (CIs) and relationships.
- **Change management** — formal process for proposing, reviewing (CAB/CMB), approving, implementing changes.
- **Version control** — for infrastructure-as-code (Terraform, Ansible playbooks), Group Policy backups, firewall rule changes.

**Tools:**
- **Ansible, Puppet, Chef, SaltStack** — declarative config management; enforce desired state.
- **Microsoft DSC, Intune, GPO** — Windows-centric.
- **Terraform, CloudFormation** — cloud infrastructure as code.
- **Tripwire, OSSEC, AIDE** — file integrity monitoring (FIM); detect drift.

**Exam angle:** if you see "the system configuration changed without authorization" → answer involves FIM or drift detection. If "rebuild a server to known-good state" → baseline + CM tooling.

## How they connect

Hardening sets the *target* state. Configuration management *enforces and monitors* that state over time. Without CM, hardening decays: someone disables a control "temporarily," patches lapse, and within months the system is no longer hardened.

---

← Back: [01_01 OS Basics](01_01_os_basics.md) — Next: [01_03 Infrastructure Concepts](01_03_infrastructure_concepts.md) →

## Related

**Internal:**
- [01_01 OS basics](01_01_os_basics.md) — what you're hardening
- [02_04 Mitigation and controls](../02_vulnerability_management/02_04_mitigation_and_controls.md) — patching, compensating controls
- [02_07 Cloud-specific vulnerabilities](../02_vulnerability_management/02_07_cloud_specific_vulnerabilities.md) — cloud configuration baselines

**External:**
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
- [DISA STIGs](https://public.cyber.mil/stigs/)
- [Microsoft Security Baselines](https://learn.microsoft.com/en-us/windows/security/threat-protection/windows-security-configuration-framework/security-compliance-toolkit-10)
- [NIST SP 800-128 — Configuration Management](https://csrc.nist.gov/publications/detail/sp/800-128/final)
