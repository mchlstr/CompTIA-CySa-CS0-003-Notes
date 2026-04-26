# DNS Security

DNS is the most-abused protocol in security. Attackers use it for C2, exfiltration, redirection, and reconnaissance — and defenders use it as one of the strongest control points (DNS sinkholes, threat-feed blocking, anomaly detection). Expect CySA+ to test DNS attacks by name and by signature in logs.

## DNS basics (just enough)

- **Resolver** — the client/recursive resolver that asks "what's the IP of `example.com`?" (your laptop's stub resolver, or a recursive resolver like 1.1.1.1, 8.8.8.8, your ISP's).
- **Authoritative server** — holds the zone for a domain; answers definitively.
- **Recursion** — resolver walks root → TLD → authoritative until it gets an answer.
- **Caching** — answers cached per their TTL.
- **Common record types:**
  - **A** — IPv4 address.
  - **AAAA** — IPv6 address.
  - **CNAME** — alias to another name.
  - **MX** — mail server for the domain.
  - **NS** — authoritative nameservers.
  - **TXT** — arbitrary text (used for SPF, DMARC, domain verification, and *abuse* — DNS tunneling).
  - **PTR** — reverse lookup (IP → name).
  - **SOA** — start of authority (zone metadata).
  - **SRV** — service location.
- **Ports:** UDP/53 (queries), TCP/53 (large responses, AXFR zone transfers).

## DNS-based attacks

### DNS cache poisoning (DNS spoofing)
Attacker injects forged responses into a resolver's cache so future queries return attacker-controlled IPs.
- **Kaminsky attack (2008)** — exploited weak transaction ID randomization to win the race against legitimate responses; led to widespread source-port randomization and DNSSEC adoption.
- **Modern variants** — SAD DNS (2020) revived cache poisoning via side channels.

**Mitigation:** DNSSEC, source-port randomization, 0x20 (case randomization), use DoH/DoT to encrypt and authenticate the resolver path.

### DNS tunneling
Encoding data in DNS queries/responses to bypass firewalls (DNS is rarely blocked).
- Attacker controls an authoritative server for a domain (e.g., `evil.example`).
- Compromised host queries `<base32-encoded-data>.evil.example` — every query carries a chunk of stolen data.
- Responses (especially TXT) carry commands back.
- Tools: **iodine, dnscat2, DNScat**.

**Indicators:**
- High volume of queries to one or a few domains.
- Long, randomized-looking subdomain names.
- Heavy use of TXT records.
- Same client querying repeatedly with no cache hits.
- Unusual entropy in query names.

**Detection:** SIEM rules on query length, entropy, frequency per host; commercial DNS security platforms (Cisco Umbrella, Infoblox, Akamai); Zeek `dns.log`.

### Domain Generation Algorithms (DGAs)
Malware computes pseudo-random domains daily/hourly; the attacker registers a small subset; malware tries them all until one resolves → C2.
- Defeats static blocklists (the domains don't exist yet).
- Examples: **Conficker, Kraken, Murofet, Necurs, Locky.**

**Indicators:**
- High NXDOMAIN rate from one host (most generated domains aren't registered).
- Long, random-looking, often algorithmically structured domain names.
- Bursts of unique queries clustered in time.

**Detection:** statistical analysis of NXDOMAIN rates per host; ML/entropy classifiers (commercial DNS security); known DGA family pattern matching.

### Fast flux
Attacker rotates the IP addresses behind a domain very rapidly (low TTL, big address pool) to evade IP-based blocks.
- **Single flux** — A records change rapidly.
- **Double flux** — both A *and* NS records change rapidly.
- Used by botnets and phishing kits.

**Indicators:** unusually short TTLs; huge address pools; geographically scattered IPs; short-lived domains.

### NXDOMAIN flood / Slow-drip DDoS
Attacker sends many queries for non-existent subdomains of a target zone, exhausting the authoritative server's capacity.

**Mitigation:** rate limiting (RRL — Response Rate Limiting), upstream DNS providers with anycast capacity.

### DNS amplification (reflective DDoS)
Attacker sends small queries spoofed from victim IP to open recursive resolvers; resolvers send large responses back to victim.
- Amplification factor: small query → large `ANY` or DNSKEY response.
- Used to flood third parties.

**Mitigation:** disable open recursion, rate limit, BCP38 ingress filtering at network edges.

### Subdomain takeover
DNS still points (CNAME) at a third-party service the org no longer owns (e.g., abandoned S3 bucket, GitHub Pages, Heroku app). Attacker registers the now-free service name and serves arbitrary content under your domain.

**Mitigation:** asset hygiene; remove DNS records when decommissioning resources; monitor with tools like **Subjack, SubOver, or commercial ASM**.

### Zone transfer abuse (AXFR/IXFR)
Misconfigured authoritative servers may allow `dig @ns.example.com example.com AXFR` from anyone — handing over the whole zone (every host).

**Mitigation:** restrict AXFR to trusted secondary nameservers via ACLs and TSIG.

### Reconnaissance via DNS
- **Brute-force subdomain enumeration** — `subfinder`, `amass`, `gobuster dns`, `dnsenum`, `dnsrecon`.
- **Certificate Transparency mining** — every TLS cert reveals subdomains; `crt.sh`.
- **Reverse DNS / PTR sweeps**.
- **Passive DNS** databases — Farsight, RiskIQ.

### DNS hijacking / domain hijacking
- **Registrar account compromise** — attacker logs into your domain registrar (e.g., GoDaddy) and changes nameservers / WHOIS.
- **BGP hijack of DNS prefixes** — route DNS server traffic to attacker.
- **Local resolver tampering** — malware changes `/etc/resolv.conf` or host file.

**Mitigation:** registrar lock, MFA on registrar account, separate registrant email, DNSSEC, monitoring DNS records for changes.

### Typosquatting / homograph
- **Typosquatting** — register `gooogle.com`, `microsft.com` for phishing.
- **Homograph (IDN)** — use Unicode characters that look identical to ASCII (`аpple.com` with Cyrillic 'а').
- **Combosquatting** — `microsoft-secure-update.com`.

**Mitigation:** brand monitoring services, browser punycode display, user training.

## DNS as a defensive control

Because DNS is required for almost all activity, controlling it is high leverage.

### DNS sinkholing
- Resolver returns a controlled IP (often `0.0.0.0` or an internal logging server) for known-bad domains.
- Effectively blocks malware from reaching its C2.
- Provides *visibility* into infected hosts (sinkhole logs the source IPs that tried).

### Threat intelligence integration
- DNS firewalls (Response Policy Zones — RPZ) consume threat feeds and block at the resolver.
- Commercial: Cisco Umbrella, Infoblox BloxOne Threat Defense, Akamai Edge DNS, Cloudflare Gateway, Quad9.

### Logging & monitoring
- **Query logs** — every DNS query a host makes. Goldmine for hunting.
- **Passive DNS** — historical resolution data; pivot point for incident analysis.
- **Tools:** Zeek `dns.log`, Splunk Stream, ELK with Packetbeat, BIND query log, Microsoft DNS analytical log.

## DNSSEC, DoH, DoT

### DNSSEC (Domain Name System Security Extensions)
Cryptographically signs DNS records so resolvers can verify authenticity — **defends against cache poisoning and forgery**, not against DDoS or content-related attacks.
- Adoption is uneven; many TLDs and zones still don't sign.
- Doesn't encrypt — DNSSEC adds integrity, not confidentiality.

### DoT (DNS over TLS, port 853)
Encrypts the resolver-client query channel using TLS — **prevents eavesdropping and tampering** on the wire.

### DoH (DNS over HTTPS, port 443)
Encrypts queries inside HTTPS — same goals as DoT, but harder to distinguish from regular web traffic.

**Defender's headache with DoH:**
- DNS no longer visible at the network layer.
- Browsers can use external DoH resolvers (Cloudflare 1.1.1.1, Google 8.8.8.8) bypassing corporate DNS controls and threat-intel sinkholing.
- Mitigation:
  - **Block known DoH resolver IPs/SNIs** at firewall.
  - **Force corporate DoH** to your own resolver.
  - **Browser GPO** to disable third-party DoH on managed devices.
  - **Endpoint DNS visibility** (EDR, host-based DNS logging).

### DoQ (DNS over QUIC) — emerging
Same encryption goal, on QUIC/UDP. Even harder to inspect than DoH.

## Common DNS-related indicators (recap)

| Indicator | Likely cause |
|---|---|
| High NXDOMAIN rate from one host | DGA-driven malware |
| Long, random-looking subdomain queries to single domain | DNS tunneling |
| Sudden flood of TXT queries | DNS tunneling (data exfil via TXT) |
| Same client beaconing-style DNS queries at fixed intervals | C2 over DNS |
| Domain with very low TTL and rotating A records | Fast flux botnet |
| Queries to known DoH provider IPs from non-browser process | DoH-based C2 evasion |
| Registrar account login from unusual IP | Domain hijack attempt |
| New CNAME pointing to abandoned cloud resource | Subdomain takeover risk |

## Tools

- **dig**, **nslookup**, **host** — query specific records.
- **whois**, **rdap** — registration info.
- **dnsenum**, **dnsrecon**, **fierce** — recon.
- **amass**, **subfinder**, **assetfinder** — subdomain discovery.
- **MassDNS** — bulk resolution.
- **Zeek** — passive DNS logging from network traffic.
- **Passive DNS providers** — Farsight DNSDB, RiskIQ, SecurityTrails.
- **DNSSEC Analyzer (Verisign Labs)**, **DNSViz** — DNSSEC chain inspection.
- **MXToolbox** — DNS health, blocklist checks.

## Exam tips

- **High NXDOMAIN from one host** → DGA.
- **Long random subdomains + heavy TXT** → DNS tunneling / exfil.
- **Domain with rotating IPs and very short TTL** → fast flux.
- **Defense for cache poisoning** → DNSSEC.
- **Defense for cleartext queries on the wire** → DoH or DoT.
- **DoH challenge for defenders** → loss of network DNS visibility; mitigate with endpoint logging or block known DoH resolvers.
- **Sinkholing** = redirect bad domains to a controlled IP — both blocks and provides visibility.
- **AXFR exposure** = misconfigured zone transfer; restrict to authorized secondaries.

---

← Back: [01_05 Wireless Security](01_05_wireless_security.md) — Next: [01_07 Identity and Access Management (IAM)](01_07_identity_and_access_management.md) →

## Related

**Internal:**
- [01_04 Network architecture](01_04_network_architecture.md) — DNS sits at the network foundation
- [01_10 Malicious activity detection](01_10_malicious_activity_detection.md) — DNS-based indicators
- [01_11 Email analysis](01_11_email_analysis.md) — SPF/DKIM/DMARC are DNS records
- [01_12 File and malware analysis](01_12_file_and_malware_analysis.md) — DGAs and DNS C2

**External:**
- [ICANN — DNSSEC](https://www.icann.org/resources/pages/dnssec-what-is-it-why-important-2019-03-05-en)
- [RFC 9499 — DNS Terminology](https://datatracker.ietf.org/doc/html/rfc9499)
- [Cloudflare 1.1.1.1 — DoH/DoT explained](https://developers.cloudflare.com/1.1.1.1/encryption/)
- [SANS — Detecting DNS tunneling](https://www.sans.org/white-papers/34152/)
