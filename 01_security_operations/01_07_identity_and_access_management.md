# Identity and Access Management (IAM)

IAM is the answer to two questions: **who are you** (authentication) and **what can you do** (authorization). Identity is the new perimeter - most modern breaches involve credential abuse.

## MFA (Multi-Factor Authentication)

**MFA** = combining two or more authentication factors:
- **Something you know** - password, PIN.
- **Something you have** - token, phone, smart card.
- **Something you are** - biometrics (fingerprint, face).
- **Somewhere you are** - geolocation (less common, contextual).
- **Something you do** - behavioral biometrics (typing rhythm).

**Strength ranking (best to worst):**
1. **Hardware tokens (FIDO2/WebAuthn)** - phishing-resistant, e.g., YubiKey. Best.
2. **Push notifications with number matching** - strong, user-friendly.
3. **TOTP apps** - Google/Microsoft Authenticator. Good.
4. **SMS / voice** - vulnerable to SIM swap; deprecated by NIST.

**MFA fatigue / push bombing** - attacker spams push notifications hoping the user approves one. Mitigations: number matching, rate limiting.

## SSO (Single Sign-On)

**SSO** = one login grants access to multiple applications.

Benefits: better UX, fewer passwords to manage, centralized auth (easier to enforce MFA, lockout, monitoring).

Risk: compromise of the SSO account = compromise of *everything* the user could access. Mitigation: strong MFA on SSO, conditional access, session monitoring.

**Common protocols:**
- **SAML 2.0** - XML-based, browser-redirect SSO. Common in enterprise (Okta, Azure AD, ADFS).
- **OAuth 2.0** - authorization framework (delegate access). Not authentication by itself.
- **OpenID Connect (OIDC)** - authentication layer on top of OAuth 2.0. Modern, JSON/REST-based.
- **Kerberos** - Windows AD's native protocol; ticket-based.

## Federation

**Federation** = trusting identities issued by another organization's identity provider.

Example: log into a partner's portal using your company credentials. Your IdP (Identity Provider) vouches for you to their SP (Service Provider) via SAML or OIDC.

**Use cases:**
- B2B partnerships.
- Acquisitions (federate two ADs without merging them).
- SaaS access ("Sign in with Google/Microsoft").

**Risks:** misconfigured trust relationships, IdP compromise propagates to all federated SPs (Golden SAML attack).

## PAM (Privileged Access Management)

**PAM** = controlling, monitoring, and auditing access for *privileged* accounts (admins, service accounts, root).

Why it matters: privileged credentials are the attacker's prize. One compromised admin = total network compromise.

Features:
- **Vaulting** - store privileged credentials in an encrypted vault; users check them out.
- **Just-in-time (JIT) access** - elevate privileges only when needed, for a limited time.
- **Session recording** - every privileged session video/keystroke logged.
- **Password rotation** - automatic, after each use.
- **MFA for privileged access** - always.

**Tools:** CyberArk, BeyondTrust, Delinea (Thycotic), HashiCorp Vault, Azure PIM.

## Passwordless authentication

Eliminates the password as a factor. Replacements:
- **FIDO2 / WebAuthn / passkeys** - public-key crypto; private key never leaves the device.
- **Biometrics** - Windows Hello, Touch ID.
- **Magic links / OTP via email** - weakest form.

Benefits: no password to phish, reuse, or leak.

## CASB (Cloud Access Security Broker)

**CASB** = a security control point between users and cloud services. Enforces visibility, compliance, data security, and threat protection for SaaS/IaaS.

Four pillars (Gartner):
1. **Visibility** - discover shadow IT, who's using what.
2. **Compliance** - enforce data residency, regulatory rules.
3. **Data security** - DLP, encryption, tokenization for cloud data.
4. **Threat protection** - detect anomalous behavior, account compromise.

**Deployment modes:**
- **API-based** - pulls data from SaaS APIs; out-of-band, no latency, but reactive.
- **Forward proxy** - agent on endpoint routes traffic through CASB; sees all traffic.
- **Reverse proxy** - sits in front of the SaaS app; agentless, but only works for managed apps.

CASBs are often part of a **SASE/SSE** stack now (Netskope, Zscaler, Microsoft Defender for Cloud Apps).

**Exam tip:** if a question is about controlling SaaS use or detecting shadow IT → CASB.

## Access control models

How the system decides *who* gets access to *what*. CySA+ expects recognition of each model by its decision logic.

- **DAC (Discretionary Access Control)** - the **owner** of a resource decides who can access it. Flexible; standard in commercial OSes (Windows NTFS, Linux file permissions). Weakness: easy to misconfigure; permissions sprawl.

- **MAC (Mandatory Access Control)** - the **system** enforces access based on **labels and clearances** set by a central authority. Users cannot delegate. Used in military / classified environments (SELinux, Trusted Solaris). Strongest, least flexible.

- **RBAC (Role-Based Access Control)** - permissions are assigned to **roles**, and users get permissions through role membership. The standard for enterprise systems. Scales better than DAC.

- **ABAC (Attribute-Based Access Control)** - decisions evaluate **attributes** of the subject (department, clearance), object (sensitivity, owner), and environment (time, location, device posture). Most flexible; foundation of Zero Trust and cloud IAM (AWS IAM Conditions, Azure Conditional Access).

- **Rule-Based Access Control** - uses **explicit rules** defined by an admin ("no access from non-corporate networks after hours"). Often combined with another model.

> Note: "MAC" is overloaded - it also means **Media Access Control** (Layer 2 address). Context disambiguates.

**Exam tip:** owner decides → DAC. Labels + clearances → MAC. Role membership → RBAC. Attribute / context-driven → ABAC. If-then rules → Rule-Based.

## Credential-based attacks (Active Directory)

Common AD authentication attacks. CySA+ frequently shows you Mimikatz output or a Kerberos event and expects identification.

- **Pass-the-Hash (PtH)** - attacker obtains an NTLM hash (often from LSASS via Mimikatz) and authenticates with the hash directly, no cracking needed. Defence: disable LM/NTLMv1, **Credential Guard**, **LSA Protection**, tier-0 admin separation, restrict NTLM.

- **Pass-the-Ticket (PtT)** - attacker steals a Kerberos TGT or service ticket from memory and reuses it. Defence: short ticket lifetimes, alert on tickets used from unusual hosts.

- **Golden Ticket** - attacker with the **krbtgt** account hash forges arbitrary Kerberos TGTs valid for years by default. Effectively permanent domain admin. Defence: **rotate krbtgt twice**, 10+ hours apart, when AD compromise is suspected; protect DCs; tier-0 model.

- **Silver Ticket** - attacker forges a Kerberos **service ticket** using a service-account hash. Scope is one service, but stealthier (the DC is never contacted).

- **Kerberoasting** - attacker requests TGS tickets for service accounts with SPNs, then **offline-cracks** the encrypted portion to recover the password. Defence: long random service-account passwords (25+ chars) or gMSAs; monitor anomalous **Event 4769** patterns.

- **AS-REP Roasting** - accounts with *"Do not require Kerberos preauthentication"* enabled return AS-REP responses crackable offline. Defence: ensure preauth is required on every account.

- **DCSync** - attacker with replication rights uses MS-DRSR to pull password hashes from a DC, posing as a DC itself. Often the path to dumping krbtgt for a Golden Ticket. Defence: monitor for replication requests from non-DC hosts; tightly restrict replication rights.

- **DCShadow** - attacker registers a rogue DC and pushes malicious changes into AD. Stealthier than DCSync.

**Exam tip:** Mimikatz / LSASS dumping → PtH, Golden Ticket, or DCSync. Service-account ticket cracking → Kerberoasting. "Forged tickets valid for years" → Golden Ticket. Standard defences: **tier-0 separation, krbtgt rotation, Credential Guard / LSA Protection, monitor Event 4769 patterns.**

---

← Back: [01_06 DNS Security](01_06_dns_security.md) - Next: [01_08 Encryption & Data Protection](01_08_encryption_and_data_protection.md) →

## Related

**Internal:**
- [01_08 Encryption and data protection](01_08_encryption_and_data_protection.md) - PKI, certificates underpin federation
- [02_06 Web vulnerability classes](../02_vulnerability_management/02_06_web_vulnerability_classes.md) - auth and session attacks
- [02_07 Cloud-specific vulnerabilities](../02_vulnerability_management/02_07_cloud_specific_vulnerabilities.md) - IAM in cloud, OAuth abuse

**External:**
- [NIST SP 800-63 - Digital Identity Guidelines](https://pages.nist.gov/800-63-3/)
- [FIDO Alliance - passkeys](https://fidoalliance.org/passkeys/)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [Microsoft - Conditional Access](https://learn.microsoft.com/en-us/entra/identity/conditional-access/overview)
