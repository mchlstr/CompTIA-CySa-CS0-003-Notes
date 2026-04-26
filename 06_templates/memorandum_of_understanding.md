# Memorandum of Understanding (MoU)

> An MoU records a **non-binding** mutual understanding between parties about how they will cooperate. In security contexts it is commonly used for information sharing, joint incident response, third-party access, penetration test engagements, or system interconnection. For binding obligations (payment, liability, IP), use a **contract or Inter-Connection Agreement (ISA / ICA)** instead — and always have legal review before signing.

- **MoU title / reference:** `<short descriptive title — e.g., "Threat Intelligence Sharing MoU between <Org A> and <Org B>">`
- **Effective date:** `<YYYY-MM-DD>`
- **Expiration / review date:** `<YYYY-MM-DD>` (typical: 1–3 years)
- **MoU version:** `<x.y>`

---

## 1. Parties

This Memorandum of Understanding is entered into between:

- **Party A:** `<Legal name>`, `<address>`, represented by `<name, title>`. Hereafter referred to as "`<short name>`".
- **Party B:** `<Legal name>`, `<address>`, represented by `<name, title>`. Hereafter referred to as "`<short name>`".

(Add additional parties as needed.)

## 2. Purpose

`<one short paragraph: what the parties intend to accomplish together. Examples: "share cyber threat intelligence relevant to the financial services sector", "provide mutual incident response support during a declared cyber incident", "govern interconnection of System X and System Y for the duration of the joint program">`

## 3. Background / Recitals

`<optional paragraph(s) describing why the parties are entering into this MoU — e.g., regulatory drivers, prior collaboration, shared customer base, sector ISAC membership>`

## 4. Scope

**In scope:**
- `<activity / data type / system / time window>`
- `<activity 2>`

**Out of scope:**
- `<explicitly excluded items — important for avoiding misunderstanding>`

## 5. Roles and Responsibilities

### Party A will:
- `<commitment 1>`
- `<commitment 2>`
- `<commitment 3>`

### Party B will:
- `<commitment 1>`
- `<commitment 2>`
- `<commitment 3>`

### Joint responsibilities:
- `<commitment 1 — e.g., quarterly review meeting>`
- `<commitment 2>`

## 6. Information handling

- **Classification of shared information:** `<TLP:RED / AMBER / GREEN / CLEAR per FIRST TLP 2.0, or other classification scheme>`
- **Permitted use:** `<how the receiving party may use the information>`
- **Onward sharing:** `<is redistribution allowed? to whom? under what conditions?>`
- **Storage and protection:** `<minimum security controls — e.g., encrypted at rest, access on need-to-know>`
- **Retention and destruction:** `<duration; method of destruction at end of MoU>`
- **Personal data:** `<reference to GDPR / other data protection law; whether a separate Data Processing Agreement (DPA) is required>`

## 7. Points of Contact

| Role | Party A | Party B |
|---|---|---|
| Primary operational contact | `<name, email, phone>` | `<name, email, phone>` |
| Escalation / management | `<name, email, phone>` | `<name, email, phone>` |
| Security / IR contact | `<name, email, phone, 24x7 number>` | `<name, email, phone, 24x7 number>` |
| Legal / privacy | `<name, email>` | `<name, email>` |

Out-of-band communication channel (in case primary email is unavailable): `<e.g., Signal numbers, dedicated phone bridge>`

## 8. Term, Termination, and Review

- **Effective date:** `<YYYY-MM-DD>`
- **Initial term:** `<duration — typical: 1 year, 2 years, 3 years>`
- **Renewal:** `<auto-renew on anniversary unless notice given | requires written renewal>`
- **Termination for convenience:** either party may terminate by giving `<n>` days written notice to the other party's primary contact.
- **Termination for cause:** either party may terminate immediately upon material breach.
- **Review cadence:** parties will review this MoU at least `<annually>` to confirm it remains accurate and useful.

### Effects of termination
- All shared information must be `<returned / destroyed>` within `<n>` days, with written confirmation of destruction.
- Confidentiality obligations survive termination for `<n>` years.

## 9. Costs and Resources

- Each party bears its own costs of performing under this MoU unless explicitly agreed otherwise in a separate written agreement.
- `<list any specific shared costs, in-kind contributions, or cost-sharing arrangements>`

## 10. Confidentiality

- Each party will treat non-public information received under this MoU with at least the same care it applies to its own confidential information, and no less than reasonable care.
- Disclosure permitted only to personnel with a need to know, who are bound by confidentiality obligations.
- Disclosure required by law: receiving party will give the disclosing party prompt written notice (where legally permitted) before disclosure.

## 11. Limitations

- **Non-binding:** This MoU expresses the parties' mutual intent and understanding. It is **not** a legally binding contract and creates **no enforceable rights, obligations, or remedies** at law or equity, except where specifically stated to be binding (e.g., confidentiality, termination).
- **No agency or partnership:** Nothing in this MoU creates a partnership, joint venture, agency, or employment relationship between the parties.
- **No exclusivity:** Each party remains free to enter into similar arrangements with other organisations.
- **No transfer of intellectual property** is intended or implied.

## 12. Dispute Resolution

- Parties will attempt to resolve disputes informally through their primary contacts within `<n>` days.
- Unresolved disputes will be escalated to executive sponsors of each party.
- Governing law for interpretation (if needed): `<jurisdiction>`.

## 13. Amendments

This MoU may be amended only in writing, signed by authorised representatives of both parties.

## 14. Signatures

| Party | Name | Title | Signature | Date |
|---|---|---|---|---|
| Party A | | | | |
| Party B | | | | |

---

## References

There is **no single mandated MoU format** for cybersecurity collaboration — each MoU is tailored. This template synthesises common practice from federal interconnection guidance, ISAC member agreements, and standard contractual frameworks. Always have legal counsel review before signing.

- **NIST SP 800-47 Rev. 1 — Managing the Security of Information Exchanges** — covers ISAs / MoUs / MoAs for system interconnection, including required content and lifecycle: https://csrc.nist.gov/pubs/sp/800/47/r1/final
- **CISA — Information Sharing and Analysis Organizations (ISAOs)** — guidance on member sharing arrangements: https://www.cisa.gov/topics/cyber-threats-and-advisories/information-sharing
- **Cyber Information Sharing and Collaboration Program (CISCP) / AIS** — model agreements: https://www.cisa.gov/topics/cyber-threats-and-advisories/information-sharing/automated-indicator-sharing-ais
- **FIRST Traffic Light Protocol (TLP) 2.0** — for classifying information shared under the MoU: https://www.first.org/tlp/
- **NIST SP 800-115 §4.2 — Rules of Engagement** — when the MoU covers penetration testing or technical assessment activities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-115.pdf
- **Federal CIO Council — sample interconnection security agreements (ISAs)** — public examples of structured agreements: search csrc.nist.gov for "Interconnection Security Agreement template"
