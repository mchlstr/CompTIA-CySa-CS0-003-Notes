# Threat Intelligence

## Threat actors

Knowing *who* is attacking you shapes *how* you defend. CySA+ expects you to recognize each type by motive, capability, and typical TTPs.

- **APT (Advanced Persistent Threat)** — well-funded, usually nation-state-backed groups (e.g., APT28, APT29, Lazarus). Long dwell times, custom malware, espionage or sabotage goals. Hard to detect, harder to evict.
- **Nation-state** — government or government-sponsored. Goals: espionage, IP theft, critical infrastructure disruption. Examples: Stuxnet (US/Israel), Industroyer (Russia).
- **Insider threat** — current/former employees or contractors with legitimate access. Two flavors: **malicious** (intentional theft, sabotage) and **unintentional** (negligence, phishing victims). Hardest to detect because access is normal.
- **Hacktivist** — ideologically motivated (Anonymous, environmental groups). Usually loud — DDoS, defacement, data leaks for publicity.
- **Organized crime** — financially motivated. Ransomware, banking trojans, BEC fraud. Increasingly professionalized (RaaS, affiliate programs).
- **Script kiddie** — low skill, uses pre-built tools. Opportunistic, rarely persistent. Still dangerous due to volume.
- **Supply chain** — emerging category. Compromise a trusted vendor (SolarWinds, Kaseya) to reach downstream targets.

## TTPs (Tactics, Techniques, Procedures)

The pyramid of pain (David Bianco) — what hurts the attacker most when you detect/block it:

1. **Hash values** — trivial to change.
2. **IP addresses** — easy to rotate.
3. **Domain names** — easy but takes effort.
4. **Network/host artifacts** — harder.
5. **Tools** — painful to swap.
6. **TTPs** — most painful; forces the attacker to relearn how they operate.

**Tactics** = the *why* (high-level goal, e.g., persistence, exfiltration). **Techniques** = the *how* (e.g., scheduled task for persistence). **Procedures** = the *exact way* this actor does it (e.g., specific PowerShell command).

MITRE ATT&CK is the canonical TTP catalog.

## Confidence levels

Intelligence is only useful if you can judge its quality. Three dimensions:

- **Timeliness** — how fresh is it? IOCs decay fast (IPs change in hours, hashes in days). Old intel = false negatives.
- **Relevancy** — does it apply to *your* environment? An iOS exploit is irrelevant if you're a Windows-only shop.
- **Accuracy** — is it correct, or is it a guess/false positive? Sources should provide a confidence rating.

Standard scoring (e.g., MISP) uses **Admiralty Code** (A1–F6): letter for source reliability, number for information credibility.

## Collection methods

- **Open Source Intelligence (OSINT)** — public sources. Free, broad, but noisy. Examples: blog posts, social media, public threat feeds (AlienVault OTX, abuse.ch), CVE databases, Shodan, Censys.
- **Closed source** — paid feeds, ISAC/ISAO membership, vendor reports (Mandiant, CrowdStrike, Recorded Future). More curated, more reliable, more expensive.
- **HUMINT** — human sources (informants, undercover, dark-web forum infiltration). Rare for most orgs.
- **SIGINT/TECHINT** — signals/technical collection. Mostly government.
- **Internal telemetry** — your own logs, IR cases, honeypots. Often the most relevant intel you have.

## Threat intelligence sharing & operational uses

**Sharing standards:**
- **STIX** (Structured Threat Information eXpression) — the *language* for describing threats.
- **TAXII** (Trusted Automated eXchange of Indicator Information) — the *transport* for sharing STIX.
- **OpenIOC**, **MISP** — alternative formats/platforms.

**Sharing communities:**
- **ISACs** (Information Sharing and Analysis Centers) — sector-specific (FS-ISAC for finance, H-ISAC for health, E-ISAC for electricity).
- **ISAOs** — broader, less formal.
- **CISA AIS** — US government threat-sharing program.

**Operational uses:**
- **Strategic** — long-term trends for executives (which actors target our sector?).
- **Operational** — campaigns and TTPs for SOC managers (how is APT29 operating right now?).
- **Tactical** — specific TTPs for analysts/hunters (what techniques to look for in logs).
- **Technical** — IOCs (hashes, IPs, domains) fed into SIEM, EDR, firewall blocklists.

**Exam tip:** if asked which intel type goes to whom — strategic = exec, operational = managers, tactical = analysts, technical = tools.

## Related

**Internal:**
- [01_10 Malicious activity detection](01_10_malicious_activity_detection.md) — applying intel to detection
- [01_12 File and malware analysis](01_12_file_and_malware_analysis.md) — IOC enrichment
- [01_14 Threat hunting](01_14_threat_hunting.md) — proactive use of intel
- [03_01 Attack methodology frameworks](../03_incident_response_management/03_01_attack_methodology_frameworks.md) — Kill Chain, ATT&CK, Diamond

**External:**
- [MITRE ATT&CK](https://attack.mitre.org/)
- [CISA Known Exploited Vulnerabilities (KEV)](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)
- [CISA Automated Indicator Sharing (AIS)](https://www.cisa.gov/topics/cyber-threats-and-advisories/information-sharing/automated-indicator-sharing-ais)
- [MISP — open threat intel platform](https://www.misp-project.org/)
- [STIX / TAXII](https://oasis-open.github.io/cti-documentation/)
