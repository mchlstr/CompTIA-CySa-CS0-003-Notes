# Logs and Monitoring

## Log ingestion

**Log ingestion** = collecting logs from sources (servers, network gear, apps, cloud) into a central system for storage, search, and correlation.

**Common log sources:**
- **OS logs** — Windows Event Log, Linux syslog/journald.
- **Application logs** — web server (Apache/Nginx access + error), database, custom apps.
- **Network device logs** — firewall, IDS/IPS, router, switch, WAF.
- **Endpoint logs** — EDR telemetry, AV.
- **Authentication logs** — AD, RADIUS, IdP (Okta, Azure AD).
- **Cloud logs** — AWS CloudTrail, Azure Activity Log, GCP Audit Logs.
- **Identity logs** — sign-in events, MFA challenges, conditional access.

**Transport:**
- **Syslog (UDP 514, TCP 514, TLS 6514)** — Linux/network device standard. UDP loses messages; prefer TCP/TLS.
- **WEF/WEC (Windows Event Forwarding)** — native Windows log forwarding.
- **Agents** — Splunk Forwarder, Elastic Beats, Wazuh agent, OSQuery.
- **APIs** — pull logs from cloud/SaaS via REST.

**Considerations:** volume (logs grow fast — plan storage), parsing (normalize formats), retention (compliance often dictates 1+ year), integrity (logs are evidence — protect them).

## Time synchronization

If clocks aren't synced, log correlation breaks. A login at 10:00 on one server and a connection at 09:55 on another could be the same event — or unrelated — and you can't tell.

- **NTP (Network Time Protocol)** — UDP 123. Standard.
- **PTP (Precision Time Protocol)** — sub-microsecond precision; used in financial trading.
- **Stratum levels** — Stratum 0 (atomic clocks, GPS), Stratum 1 (synced to S0), down to Stratum 15.

**Best practice:** all systems sync to a small set of internal NTP servers, which sync to authoritative external sources. Use UTC everywhere; convert to local time only at display.

**NTP attacks:** spoofed NTP responses can shift system clocks → backdate certs, replay tokens, evade detection.

## Logging levels

Standard levels (syslog severity):

| Level | Name | Use |
|---|---|---|
| 0 | Emergency | System unusable |
| 1 | Alert | Immediate action needed |
| 2 | Critical | Critical conditions |
| 3 | Error | Error conditions |
| 4 | Warning | Warning — may indicate problems |
| 5 | Notice | Normal but significant |
| 6 | Informational | Routine operational messages |
| 7 | Debug | Verbose, dev/troubleshooting |

**Trade-off:** more verbose = more data = more storage cost and noise, but more detail when investigating. Production usually runs at Info or Notice; Debug enabled temporarily for troubleshooting.

## SIEM (Security Information and Event Management)

**SIEM** = central platform that collects, normalizes, correlates, and alerts on logs. The SOC's primary console.

Functions:
- **Aggregation** — pull logs from everywhere into one place.
- **Normalization** — convert different formats into a common schema.
- **Correlation** — connect events across sources (e.g., failed login + new admin add + outbound transfer).
- **Alerting** — fire on rules or anomalies.
- **Search and reporting** — investigative queries, compliance reports.
- **Long-term retention** — for forensics and compliance.

**Major products:** Splunk Enterprise Security, Microsoft Sentinel, IBM QRadar, Elastic SIEM, Sumo Logic, LogRhythm, Securonix, Exabeam.

**Common rule examples:** brute-force login (N failed logins in M minutes), impossible travel (logins from two distant locations close in time), new admin account creation, mass file access, beaconing patterns.

## EDR (Endpoint Detection and Response)

**EDR** = endpoint agent that records detailed activity (process tree, file ops, registry changes, network connections), detects threats, enables response (isolate host, kill process, collect forensics).

Capabilities:
- **Behavioral detection** — beyond signatures; spots living-off-the-land (LOLBins), suspicious child processes.
- **Threat hunting** — query historical telemetry across all endpoints.
- **Response** — isolate, contain, rollback (some products).
- **Forensics** — full process tree of an incident.

**Major products:** CrowdStrike Falcon, Microsoft Defender for Endpoint, SentinelOne, Carbon Black, Cortex XDR, Cybereason.

**XDR (Extended Detection and Response)** — EDR + network + email + identity + cloud telemetry, correlated. The "next-gen" SIEM/EDR hybrid.

## Packet capture

**Packet capture (pcap)** = recording raw network packets for analysis. The ground truth of network activity.

**Tools:**
- **Wireshark** — GUI, the analyst's standard.
- **tcpdump** — CLI, ubiquitous on Linux.
- **tshark** — Wireshark's CLI.
- **NetworkMiner** — extracts artifacts (files, sessions) from pcaps.
- **Zeek (Bro)** — turns network traffic into structured logs (much more efficient than full pcap for long-term).

**Use cases:** confirming malware C2, validating IDS alerts, deep forensic reconstruction, troubleshooting.

**Challenges:** volume (full pcap on a busy link = TB/day), encryption (TLS hides payload — only metadata visible), legal (capture may be regulated).

## Practical examples

- **Brute-force detection:** SIEM rule `auth_failed > 10 in 5 min from same source` → alert.
- **Beaconing detection:** Zeek logs show one host hitting the same external IP every 60 ± 5 seconds → likely C2.
- **Lateral movement:** Windows event 4624 (logon) on multiple servers from same account in short window → investigate.
- **Data exfil:** outbound transfer > 1 GB to external IP from a database server → DLP / SIEM alert.

**Exam tip:** know the difference — SIEM = correlate across sources, EDR = deep on endpoints, packet capture = network ground truth. Each answers a different kind of question.

## Key Windows Event IDs

CySA+ frequently shows event log excerpts and asks what the activity represents. Memorize the high-frequency ones.

### Authentication / logon
| ID | Source | Meaning | Why it matters |
|---|---|---|---|
| **4624** | Security | Successful logon | Look at **Logon Type** field |
| **4625** | Security | Failed logon | Brute force / password spray detection |
| **4634** | Security | Logoff | Pair with 4624 to measure session length |
| **4648** | Security | Explicit credential use (RunAs / scheduled task) | Lateral movement indicator |
| **4672** | Security | Special privileges assigned | Admin login flag |
| **4720** | Security | Account created | Persistence (rogue admin?) |
| **4722 / 4725** | Security | Account enabled / disabled | Watch for enabling disabled accounts |
| **4724 / 4738** | Security | Password reset / account changed | Account compromise pivot |
| **4728 / 4732 / 4756** | Security | Member added to security-enabled group (Global / Local / Universal) | Privilege escalation — esp. Domain Admins |
| **4768** | Security | Kerberos TGT requested | Auth baseline |
| **4769** | Security | Kerberos service ticket requested | Kerberoasting / golden ticket investigation |
| **4771** | Security | Kerberos pre-auth failed | Brute force against Kerberos |
| **4776** | Security | NTLM auth | NTLM use trending down → anomaly if spike |

### Logon Type field (within 4624 / 4625)
| Type | Meaning |
|---|---|
| 2 | Interactive (console) |
| 3 | Network (SMB, file share) |
| 4 | Batch (scheduled task) |
| 5 | Service |
| 7 | Unlock |
| 8 | NetworkCleartext (e.g., basic auth — bad sign for many apps) |
| 9 | NewCredentials (RunAs / explicit) |
| 10 | RemoteInteractive (RDP) |
| 11 | CachedInteractive |

### Process / execution
| ID | Source | Meaning |
|---|---|---|
| **4688** | Security | Process create (with command line if enabled) |
| **4689** | Security | Process exit |
| **4697** | Security | New service installed (persistence) |
| **5140 / 5145** | Security | Network share access |

### PowerShell
| ID | Source | Meaning |
|---|---|---|
| **4103** | PowerShell/Operational | Module logging (pipeline execution) |
| **4104** | PowerShell/Operational | Script block logging — **decoded malicious scripts surface here** |
| **400 / 403** | Windows PowerShell (legacy) | Engine start/stop |

### Sysmon (if installed — recommended)
| ID | Meaning |
|---|---|
| **1** | Process create (richer than 4688) |
| **3** | Network connection |
| **7** | Image (DLL) loaded |
| **8** | CreateRemoteThread (process injection) |
| **10** | Process access (credential dumping indicator) |
| **11** | File create |
| **12 / 13 / 14** | Registry events |
| **22** | DNS query |

### Account lockout / changes
| ID | Meaning |
|---|---|
| **4740** | Account locked out |
| **4767** | Account unlocked |
| **5024 / 5025** | Windows Firewall service start/stop (rare = suspicious) |

### Common scenario mappings
- **5+ Event 4625 from one source in short window** → brute force.
- **4624 Type 10 from unusual location** → suspicious RDP.
- **4720 + 4732 (Domain Admins)** in close succession → account creation followed by privilege grant — classic persistence.
- **4104 with `IEX (New-Object Net.WebClient)`** → malicious PowerShell.
- **4688 with parent `winword.exe` and child `cmd.exe`/`powershell.exe`** → macro-based execution.
- **Sysmon 10 targeting `lsass.exe`** → likely credential dumping (Mimikatz).

## Common ports & protocols (quick reference)

CySA+ assumes you can identify a service from its port number. Memorize the high-frequency ones.

| Port(s) | Protocol | Notes |
|---|---|---|
| 20 / 21 | FTP (data / control) | Cleartext; deprecated for sensitive data |
| 22 | SSH / SCP / SFTP | Encrypted shell + file transfer |
| 23 | Telnet | Cleartext shell — never use externally |
| 25 | SMTP | Mail relay (server-to-server) |
| 53 | DNS | UDP (queries) + TCP (large responses, AXFR). Tunneling vector |
| 67 / 68 | DHCP | Server / client |
| 69 | TFTP | Cleartext, UDP — used in network device config |
| 80 | HTTP | Cleartext web |
| 88 | Kerberos | Windows AD auth |
| 110 | POP3 | Mail retrieval (cleartext) |
| 111 | RPC portmapper | NFS / SunRPC |
| 123 | NTP | Time sync (UDP) |
| 135 | MS RPC EPMAP | Windows RPC endpoint mapper |
| 137 / 138 / 139 | NetBIOS | Legacy Windows networking |
| 143 | IMAP | Mail retrieval (cleartext) |
| 161 / 162 | SNMP / SNMP trap | UDP. v1/v2c cleartext (community strings); v3 encrypted |
| 179 | BGP | Routing |
| 389 | LDAP | Directory (cleartext) |
| 443 | HTTPS | TLS-encrypted web |
| 445 | SMB | Windows file sharing — major lateral movement port |
| 465 / 587 | SMTPS / SMTP submission | Encrypted mail submission |
| 514 | Syslog | UDP cleartext (TCP/TLS variant on 6514) |
| 515 / 631 | LPR / IPP | Printing |
| 636 | LDAPS | Encrypted LDAP |
| 873 | rsync | File sync |
| 989 / 990 | FTPS | TLS FTP |
| 993 | IMAPS | Encrypted IMAP |
| 995 | POP3S | Encrypted POP3 |
| 1433 / 1434 | MSSQL | SQL Server |
| 1521 | Oracle DB | |
| 1701 | L2TP | VPN tunneling |
| 1723 | PPTP | VPN — deprecated, weak |
| 1812 / 1813 | RADIUS | Auth / accounting |
| 2049 | NFS | Linux file share |
| 3306 | MySQL | |
| 3389 | RDP | Remote Desktop — heavy attacker target |
| 5060 / 5061 | SIP / SIPS | VoIP signaling |
| 5432 | PostgreSQL | |
| 5900 | VNC | Remote desktop |
| 5985 / 5986 | WinRM | Windows remote management (HTTP / HTTPS) |
| 6379 | Redis | Often exposed by accident |
| 8080 / 8443 | HTTP-alt / HTTPS-alt | Common app servers, proxies |
| 9200 / 9300 | Elasticsearch | Often exposed publicly |
| 27017 | MongoDB | Often exposed publicly |
| 6514 | Syslog over TLS | Encrypted syslog |

**Common attack-relevant ports to recognize:**
- **445 (SMB)** — EternalBlue, ransomware lateral movement.
- **3389 (RDP)** — brute force, BlueKeep.
- **22 (SSH)** — brute force on weak creds.
- **53 (DNS)** — tunneling, DGA, exfiltration.
- **5985/5986 (WinRM)** — admin lateral movement, often used by red teams / attackers.
- **161 (SNMP)** — community-string brute force, info disclosure.

**Exam tip:** if you see an alert with port 445 between two internal hosts where they don't normally talk → suspect lateral movement. Port 22 from external IP repeatedly → SSH brute force.

## NetFlow and flow data

**NetFlow** (Cisco) and equivalents (**sFlow**, **IPFIX** = open standard, **jFlow** = Juniper) report **metadata** about network conversations — not packet contents.

A flow record typically contains: source/dest IP, source/dest port, protocol, packet count, byte count, start/end time, TCP flags, ToS.

**Why analysts use it:**
- Lightweight (orders of magnitude less storage than full pcap).
- Network-wide visibility (every router/switch can export flows).
- Long-retention friendly — keep a year of metadata where pcap would be impossible.
- Excellent for **beaconing detection**, **lateral movement**, **data exfil volume analysis**.

**Limitations:**
- No payload — can't read what was sent.
- Encrypted traffic looks the same in flows as anything else (which is the point).

**Tools:** SiLK, Argus, **Zeek conn.log** (similar concept), commercial NDR (ExtraHop, Vectra, Darktrace, Corelight).

## UEBA (User and Entity Behaviour Analytics)

**UEBA** = automatically baselining "normal" behaviour for each user and entity (host, service account), then flagging deviations.

- **Inputs:** auth logs, file access, network flows, application activity.
- **Outputs:** risk scores per user/entity, prioritised alerts.
- **Detects:** account compromise (impossible travel, off-hours, new device), insider threat (mass downloads, unusual share access), lateral movement (account suddenly hitting new hosts), service-account abuse (interactive logon by a service account).

**Where it lives:**
- Built into modern SIEMs (Splunk UBA, Microsoft Sentinel, Securonix, Exabeam, IBM QRadar UBA).
- Often part of **XDR** stacks.
- Some IdPs (Okta, Microsoft Entra ID Protection) include identity-only UEBA.

**Strengths:** catches "low-and-slow" and credential-misuse attacks that signature/correlation rules miss.
**Limitations:** noisy until tuned; baseline-poisoning if the attacker is in long enough to look "normal."

---

← Back: [01_08 Encryption & Data Protection](01_08_encryption_and_data_protection.md) — Next: [01_10 Malicious Activity Detection](01_10_malicious_activity_detection.md) →

## Related

**Internal:**
- [01_10 Malicious activity detection](01_10_malicious_activity_detection.md) — what these logs help you spot
- [01_15 Scripting languages](01_15_scripting_languages.md) — parsing and querying logs
- [03_05 Forensic artifacts](../03_incident_response_management/03_05_forensic_artifacts.md) — evtx structure, log artifacts
- [05_tools — Splunk](../05_tools/Splunk.md)
- [05_tools — Wireshark](../05_tools/Wireshark.md)

**External:**
- [Microsoft — Windows Event Log Reference](https://learn.microsoft.com/en-us/windows/win32/eventlog/event-logging)
- [Microsoft — Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Ultimate Windows Security — Event ID encyclopedia](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/)
- [IANA Service Name and Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml)
