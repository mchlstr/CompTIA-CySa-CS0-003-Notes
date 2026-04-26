# Identity and Access Management (IAM)

IAM is the answer to two questions: **who are you** (authentication) and **what can you do** (authorization). Identity is the new perimeter — most modern breaches involve credential abuse.

## MFA (Multi-Factor Authentication)

**MFA** = combining two or more authentication factors:
- **Something you know** — password, PIN.
- **Something you have** — token, phone, smart card.
- **Something you are** — biometrics (fingerprint, face).
- **Somewhere you are** — geolocation (less common, contextual).
- **Something you do** — behavioral biometrics (typing rhythm).

**Strength ranking (best to worst):**
1. **Hardware tokens (FIDO2/WebAuthn)** — phishing-resistant, e.g., YubiKey. Best.
2. **Push notifications with number matching** — strong, user-friendly.
3. **TOTP apps** — Google/Microsoft Authenticator. Good.
4. **SMS / voice** — vulnerable to SIM swap; deprecated by NIST.

**MFA fatigue / push bombing** — attacker spams push notifications hoping the user approves one. Mitigations: number matching, rate limiting.

## SSO (Single Sign-On)

**SSO** = one login grants access to multiple applications.

Benefits: better UX, fewer passwords to manage, centralized auth (easier to enforce MFA, lockout, monitoring).

Risk: compromise of the SSO account = compromise of *everything* the user could access. Mitigation: strong MFA on SSO, conditional access, session monitoring.

**Common protocols:**
- **SAML 2.0** — XML-based, browser-redirect SSO. Common in enterprise (Okta, Azure AD, ADFS).
- **OAuth 2.0** — authorization framework (delegate access). Not authentication by itself.
- **OpenID Connect (OIDC)** — authentication layer on top of OAuth 2.0. Modern, JSON/REST-based.
- **Kerberos** — Windows AD's native protocol; ticket-based.

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
- **Vaulting** — store privileged credentials in an encrypted vault; users check them out.
- **Just-in-time (JIT) access** — elevate privileges only when needed, for a limited time.
- **Session recording** — every privileged session video/keystroke logged.
- **Password rotation** — automatic, after each use.
- **MFA for privileged access** — always.

**Tools:** CyberArk, BeyondTrust, Delinea (Thycotic), HashiCorp Vault, Azure PIM.

## Passwordless authentication

Eliminates the password as a factor. Replacements:
- **FIDO2 / WebAuthn / passkeys** — public-key crypto; private key never leaves the device.
- **Biometrics** — Windows Hello, Touch ID.
- **Magic links / OTP via email** — weakest form.

Benefits: no password to phish, reuse, or leak.

## CASB (Cloud Access Security Broker)

**CASB** = a security control point between users and cloud services. Enforces visibility, compliance, data security, and threat protection for SaaS/IaaS.

Four pillars (Gartner):
1. **Visibility** — discover shadow IT, who's using what.
2. **Compliance** — enforce data residency, regulatory rules.
3. **Data security** — DLP, encryption, tokenization for cloud data.
4. **Threat protection** — detect anomalous behavior, account compromise.

**Deployment modes:**
- **API-based** — pulls data from SaaS APIs; out-of-band, no latency, but reactive.
- **Forward proxy** — agent on endpoint routes traffic through CASB; sees all traffic.
- **Reverse proxy** — sits in front of the SaaS app; agentless, but only works for managed apps.

CASBs are often part of a **SASE/SSE** stack now (Netskope, Zscaler, Microsoft Defender for Cloud Apps).

**Exam tip:** if a question is about controlling SaaS use or detecting shadow IT → CASB.
