# Malicious Activity Detection

The CySA+ exam expects you to recognize *indicators* — observable signs that something is wrong, grouped by where they appear (network, host, application, social).

## Network indicators

**Bandwidth anomalies** — sudden upload spikes from a workstation = possible exfil. Sustained high bandwidth from a server during off-hours = suspicious.

**Beaconing** — regular, periodic connections from a host to an external IP/domain. Classic C2 (command and control) signature.
- Look for: consistent interval (every 30s, every 5min, etc.), small packet sizes, often HTTPS or DNS as cover.
- Tools: Zeek logs, RITA, EDR behavioral analytics.
- Even with jitter (randomized interval), statistical analysis catches it over time.

**Rogue devices** — unauthorized devices on the network.
- **Rogue access points** — unauthorized Wi-Fi (employee plugs in a home router).
- **Rogue switches/hubs** — extending the network without approval.
- **Unknown MAC addresses** — detect via NAC, ARP scanning, switch port monitoring.

**Other network indicators:**
- **DNS anomalies** — DGA (Domain Generation Algorithm) domains (long, random); DNS tunneling (huge volume of TXT or unusual record types); NXDOMAIN spikes.
- **Geographic anomalies** — connections from countries you don't do business in.
- **Port scans** — multiple ports probed in sequence; reconnaissance.
- **Lateral movement** — internal-to-internal SMB/RDP/WinRM from unusual sources.
- **Protocol mismatches** — DNS traffic on non-53 ports, HTTPS on port 80.

## Host indicators

**Resource spikes:**
- **CPU spike** without a corresponding workload reason → possible cryptominer, malware encryption (ransomware), or runaway process.
- **Memory spike** → potential memory-resident malware, fileless attack.
- **Disk I/O spike** → ransomware encryption in progress, mass file access.

**Unauthorized changes:**
- **New scheduled tasks / cron jobs** — common persistence.
- **New services / systemd units** — same.
- **New local admin accounts** — privilege escalation.
- **New / modified startup entries** — registry Run keys, autostart.
- **Modified GPOs** (Windows AD) — broader impact.

**File system anomalies:**
- Files in `%TEMP%`, `%APPDATA%`, `/tmp`, `/dev/shm` that are executables.
- Recently modified system files.
- Hidden files with system attributes.
- Mass file renames / extension changes (ransomware).
- Alternate Data Streams on Windows NTFS.

**Registry anomalies (Windows):**
- New entries in Run, RunOnce keys.
- Changed default debugger (`HKLM\Software\Microsoft\Windows NT\CurrentVersion\Image File Execution Options`).
- AppInit_DLLs, COM hijacking entries.

**Tools:** Sysinternals suite (Autoruns, Process Explorer, Procmon, TCPView), osquery, EDR.

## Application indicators

**Unauthorized account creation** — particularly admin or service accounts in apps. Watch for "ghost" admin accounts created at unusual hours.

**Unexpected outbound communication** — application calling out to addresses it shouldn't (data exfil, C2). Web app talking to a Pastebin URL = bad sign.

**Service interruptions** — apps crashing, restarting, slow responses. Could be:
- Exploit attempts (memory corruption).
- Resource exhaustion (DoS).
- Malware interfering.
- Legitimate bugs (always rule out).

**Application log anomalies:**
- Authentication errors at scale → brute force.
- Authorization failures → enumeration / privilege escalation attempts.
- SQL errors visible to users → SQL injection probing.
- Stack traces in responses → information disclosure.

**Web-specific indicators:**
- Unusual user-agents (sqlmap, nikto, Burp).
- Long URLs / encoded payloads (XSS, SQLi attempts).
- Sudden 5xx error spikes.
- High traffic from one source (scraping, brute force).

## Social engineering & obfuscated links

**Phishing indicators:**
- **Lookalike domains** — `paypa1.com`, `microsft.com`, IDN homograph attacks (using Cyrillic letters that look like Latin).
- **Display name spoofing** — sender shows as "CEO" but actual address is gmail.
- **Urgency / fear / authority** — classic pressure tactics.
- **Mismatched links** — link text shows one URL, href is another.
- **Unusual attachments** — `.iso`, `.lnk`, `.html`, macro-enabled docs (`.docm`, `.xlsm`).

**Obfuscation tricks:**
- **URL shorteners** (bit.ly, tinyurl) — hides destination.
- **Encoded URLs** — `%`-encoding, hex.
- **Open redirects** — using a legitimate domain to bounce to malicious one (`legit.com/redirect?url=evil.com`).
- **HTML smuggling** — assembling the malicious payload via JS in the browser to bypass content filters.
- **Punycode / IDN** — `аpple.com` (with Cyrillic 'а') displays identically.

**Other social engineering:**
- **Pretexting** — fabricated scenario to extract info.
- **BEC (Business Email Compromise)** — impersonating an exec to redirect a wire transfer.
- **Vishing** — voice phishing, often combined with phishing.
- **Smishing** — SMS phishing.
- **MFA fatigue** — push bombing until user approves.

**Exam tip:** know the indicator-to-source mapping. Bandwidth spike = network. Cryptominer CPU = host. Account enum = application. Lookalike domain = social.

## Related

**Internal:**
- [01_06 DNS security](01_06_dns_security.md) — DNS-based indicators
- [01_09 Logs and monitoring](01_09_logs_and_monitoring.md) — where the indicators come from
- [01_11 Email analysis](01_11_email_analysis.md) — phishing indicators in detail
- [01_12 File and malware analysis](01_12_file_and_malware_analysis.md) — analyzing suspicious files
- [01_13 Threat intelligence](01_13_threat_intelligence.md) — context for indicators
- [03_05 Forensic artifacts](../03_incident_response_management/03_05_forensic_artifacts.md) — host-side investigation

**External:**
- [MITRE ATT&CK Matrix](https://attack.mitre.org/)
- [The Pyramid of Pain (David Bianco)](https://detect-respond.blogspot.com/2013/03/the-pyramid-of-pain.html)
- [SANS — Hunting evil](https://www.sans.org/posters/hunt-evil/)
