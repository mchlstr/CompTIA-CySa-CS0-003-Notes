# Encryption & Data Protection

## PKI (Public Key Infrastructure)

**PKI** = the framework of policies, hardware, software, and procedures for creating, managing, distributing, and revoking digital certificates.

Components:
- **CA (Certificate Authority)** — issues and signs certificates. Root CA → intermediate CAs → end-entity certs (chain of trust).
- **RA (Registration Authority)** — verifies identity before the CA issues a cert.
- **CRL (Certificate Revocation List)** — list of revoked certs; clients should check.
- **OCSP (Online Certificate Status Protocol)** — real-time revocation check; lighter than CRL.
- **OCSP stapling** — server attaches a fresh OCSP response to its TLS handshake (privacy + performance win).

Certificate types:
- **DV (Domain Validated)** — proves you control the domain. Cheapest, fastest, most common.
- **OV (Organization Validated)** — also verifies the organization exists.
- **EV (Extended Validation)** — strict identity vetting; used to give the green address bar (mostly deprecated visually now).
- **Wildcard** — `*.example.com`; covers all subdomains.
- **SAN** — Subject Alternative Name; one cert covers multiple domains.

**Key management** is the hard part. Lose the private key = lose trust. Compromise of the CA = catastrophic (DigiNotar, 2011).

## SSL/TLS inspection

**SSL/TLS inspection** (a.k.a. **break-and-inspect**) = decrypting TLS traffic at a security device, inspecting the plaintext, then re-encrypting.

How it works:
- Inspection device (NGFW, proxy) holds a CA cert trusted by all client devices.
- When a client requests `bank.com`, the device intercepts, generates a cert on-the-fly signed by its CA, presents it to the client.
- Device terminates the original TLS, inspects, then opens its own TLS to `bank.com`.

**Benefits:** see malware in encrypted traffic, enforce DLP, detect C2.

**Concerns:**
- **Privacy** — exempt sensitive sites (banking, healthcare).
- **Cert pinning** — apps that pin certs (banking apps, Office) will fail; need exceptions.
- **Performance overhead** — TLS termination is expensive.
- **Compliance** — some regulations restrict decryption of certain data.

## DLP (Data Loss Prevention)

**DLP** = technology and policies that prevent sensitive data from leaving the organization (or being misused internally).

Three modes:
- **Data at rest** — scanning storage (file shares, databases, endpoints) for sensitive content.
- **Data in motion** — inspecting network traffic (email, web, file transfers) for sensitive data leaving.
- **Data in use** — endpoint DLP watching clipboard, USB writes, screenshots, printing.

Detection methods:
- **Pattern matching** — regex for credit card numbers (Luhn check), SSNs.
- **Keyword/dictionary** — words like "confidential," "proprietary."
- **File fingerprinting** — exact match of known sensitive documents.
- **Statistical / ML** — context-aware classification.

**Tools:** Microsoft Purview (formerly Microsoft Information Protection), Symantec DLP, Forcepoint, Netskope, Zscaler.

## PII (Personally Identifiable Information)

**PII** = any data that can identify a specific individual, alone or combined.

- **Direct identifiers:** name, SSN, passport number, driver's license, biometric data, email.
- **Quasi-identifiers:** zip code + birthdate + gender (can re-identify ~87% of US population per Sweeney study).

Regulations: **GDPR (EU), CCPA/CPRA (California), HIPAA (US health), PIPEDA (Canada), LGPD (Brazil)**. CySA+ leans US-centric but knows GDPR.

**Protection techniques:**
- **Encryption** — at rest and in transit.
- **Tokenization** — replace sensitive value with a non-sensitive token; mapping in a secure vault.
- **Masking** — show only partial data (e.g., `***-**-1234`).
- **Anonymization** — irreversibly remove identifying info.
- **Pseudonymization** — replace identifiers with pseudonyms; reversible with key (GDPR concept).

## CHD (Cardholder Data) — PCI DSS

**CHD** = data on payment cards. Governed by **PCI DSS** (Payment Card Industry Data Security Standard).

Three categories:
- **PAN (Primary Account Number)** — the 16-digit card number. Most sensitive; if stored, must be encrypted/tokenized.
- **Cardholder name, expiration date, service code** — sensitive but less so.
- **SAD (Sensitive Authentication Data)** — full track data, CVV, PIN. **Never** stored after authorization, even encrypted.

**PCI DSS scope** = any system that stores, processes, or transmits CHD. Reduce scope = reduce audit pain. Tokenization is the standard scope-reduction technique.

**Exam tip:** if a question mentions credit card storage → PCI DSS. If it asks what *cannot* be stored after auth → SAD (CVV, PIN, full track).

## Other sensitive data categories

- **PHI (Protected Health Information)** — HIPAA-regulated medical data.
- **IP (Intellectual Property)** — source code, trade secrets, designs.
- **Financial data** — non-PCI: bank account numbers, financial reports (SOX-relevant).
- **Government/classified** — controlled by clearance levels (Confidential, Secret, Top Secret).

---

← Back: [01_07 Identity and Access Management (IAM)](01_07_identity_and_access_management.md) — Next: [01_09 Logs and Monitoring](01_09_logs_and_monitoring.md) →

## Related

**Internal:**
- [01_07 Identity and access management](01_07_identity_and_access_management.md) — PKI underpins SSO/federation
- [01_11 Email analysis](01_11_email_analysis.md) — DKIM uses public-key crypto over DNS
- [04_01 Vulnerability reporting](../04_reporting_and_communication/04_01_vulnerability_reporting.md) — PCI / HIPAA / SOX compliance reporting

**External:**
- [NIST SP 800-57 — Key Management Recommendations](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final)
- [PCI DSS Standards](https://www.pcisecuritystandards.org/)
- [HHS HIPAA Security Rule](https://www.hhs.gov/hipaa/for-professionals/security/index.html)
- [CA/Browser Forum (TLS cert standards)](https://cabforum.org/)
