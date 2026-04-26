# Forensic Artifacts

When responding to an incident, the OS leaves a paper trail. Knowing where to look — and what each artifact tells you — is core analyst knowledge. This file maps the most useful artifacts on Windows and Linux, plus memory analysis basics.

## Windows artifacts

### Registry hives
On disk at `C:\Windows\System32\config\` (offline) or live via the Registry.

- **SYSTEM** — services, drivers, networking, last boot info, USB device history.
- **SOFTWARE** — installed apps, Run keys, uninstall info.
- **SAM** — local user accounts (hashes, last logon).
- **SECURITY** — security policy, secrets (cached, LSA).
- **NTUSER.DAT** (per user, in `C:\Users\<user>\`) — user-specific apps, recent docs, run history.
- **UsrClass.dat** (per user, in `\AppData\Local\Microsoft\Windows\`) — file associations, shellbag data.

**Key registry locations during IR:**
- `Run` / `RunOnce` keys (HKLM and HKCU) — autostart persistence.
- `Services` (HKLM\SYSTEM\CurrentControlSet\Services) — service-based persistence.
- `Image File Execution Options` — debugger hijack persistence.
- `AppInit_DLLs`, `AppCertDLLs` — DLL injection persistence.
- `Schedule\TaskCache\Tree` — scheduled tasks list.
- `MountedDevices`, `USBSTOR` — connected USB device history.

### File system artifacts

- **Prefetch** — `C:\Windows\Prefetch\*.pf`. Created when an executable runs (first 10 sec). Tells you: program path, last 8 run times, # of times run, files/DLLs loaded. Excellent execution evidence even after the binary is deleted.
- **Shimcache (AppCompatCache)** — in registry. Records executables seen by the system (path + last modified time + flag). Survives reboots, doesn't always mean execution but presence is significant.
- **Amcache.hve** (`C:\Windows\AppCompat\Programs\Amcache.hve`) — richer than Shimcache: SHA-1 hash, path, size, install date for executables.
- **Master File Table (MFT)** — `$MFT` in NTFS root. Record of every file (filename, timestamps, size, parent dir). Survives deletion (record marked free but data persists).
- **USN Journal** — `$Extend\$UsnJrnl:$J`. Change journal — file create/modify/delete with timestamps. Great for timeline reconstruction.
- **LNK files** — `\AppData\Roaming\Microsoft\Windows\Recent\`. Created when files are opened. Includes target path, drive serial, MAC times — even for files on removable media now disconnected.
- **Jump Lists** — `\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations\`. Per-app file usage history.
- **Recycle Bin** — `C:\$Recycle.Bin\<SID>\`. `$I*` (metadata) and `$R*` (data) files. Deleted-but-not-purged content.
- **Shellbags** — registry-stored info about folders the user has browsed via Explorer. Reveals access to folders that are now gone or on removable media.
- **Volume Shadow Copies** (VSS) — system snapshots; sometimes contain data the attacker tried to delete.

### Browser artifacts
Per-browser, in user profiles.

- **Chrome / Edge (Chromium)** — `\AppData\Local\<Vendor>\<Browser>\User Data\Default\`:
  - `History` (SQLite) — visited URLs, downloads.
  - `Cookies`, `Login Data`.
  - `Sessions`, `Tabs`, `Top Sites`.
- **Firefox** — `\AppData\Roaming\Mozilla\Firefox\Profiles\<profile>\`:
  - `places.sqlite` — history, bookmarks, downloads.
  - `cookies.sqlite`, `formhistory.sqlite`.
- **Cache** — locally stored web content.

Tools: **Hindsight** (Chromium), **Mz Browser History View**, **NirSoft** suite.

### Event Logs (`*.evtx`)
At `C:\Windows\System32\winevt\Logs\`. Key files:
- `Security.evtx` — auth, policy, object access.
- `System.evtx` — services, drivers, system events.
- `Application.evtx` — application messages.
- `Microsoft-Windows-Sysmon%4Operational.evtx` — Sysmon (if installed).
- `Microsoft-Windows-PowerShell%4Operational.evtx` — PowerShell (4103, 4104).
- `Microsoft-Windows-TaskScheduler%4Operational.evtx` — task creation/execution.
- `Microsoft-Windows-WindowsDefender%4Operational.evtx` — Defender alerts.

(Event IDs covered in `01_07_logs_and_monitoring.md`.)

### Other Windows artifacts
- **Hibernation file** (`hiberfil.sys`) — RAM contents at last hibernate. Memory forensics offline.
- **Pagefile** (`pagefile.sys`) — paged-out memory. Strings carve can recover commands, URLs, etc.
- **Swap / crash dumps** (`memory.dmp`, mini dumps).
- **Task Scheduler XML** at `C:\Windows\System32\Tasks\` — task definitions.
- **WMI subscriptions** — persistence via WMI event subscription. Examined via `wmiprvse.exe` activity, Sysmon events 19-21.
- **Print spooler logs**, **Defender quarantine** (`C:\ProgramData\Microsoft\Windows Defender\Quarantine\`).

### Tool list for Windows forensics
- **FTK Imager** — image acquisition.
- **EnCase** / **Magnet Axiom** — commercial all-in-one.
- **Autopsy / Sleuth Kit** — open-source forensic suite.
- **KAPE** — fast targeted artifact collection.
- **Eric Zimmerman tools** (free, open-source) — `MFTECmd`, `RECmd`, `PECmd`, `JLECmd`, `LECmd`, `AmcacheParser`, `ShellBagsExplorer`, `EZViewer`.
- **Volatility / Volatility 3** — memory forensics.
- **Plaso (log2timeline)** + **Timesketch** — timeline.
- **Sysinternals**: Autoruns, Process Explorer, Procmon, Sigcheck, Strings, TCPView.

## Linux artifacts

### Filesystem
- `/var/log/` — system logs:
  - `auth.log` (Debian/Ubuntu) / `secure` (RHEL) — auth events, sudo, SSH.
  - `syslog` / `messages` — general system.
  - `kern.log` — kernel.
  - `apache2/`, `nginx/` — web server logs.
  - `audit/audit.log` — auditd events (if enabled).
- `/var/log/wtmp` — login history (binary; `last` command).
- `/var/log/btmp` — failed logins (`lastb`).
- `/var/log/lastlog` — last logon per user.
- `/var/log/journal/` — systemd journal (binary; `journalctl`).

### Persistence locations
- `/etc/cron*` — system crontabs.
- `/var/spool/cron/crontabs/` — user crontabs.
- `/etc/systemd/system/`, `/usr/lib/systemd/system/`, `~/.config/systemd/user/` — systemd units.
- `/etc/init.d/`, `/etc/rc*.d/` — legacy init scripts.
- `~/.bashrc`, `~/.bash_profile`, `~/.profile`, `/etc/profile.d/` — shell init (often abused).
- `~/.ssh/authorized_keys` — SSH backdoor accounts.
- `/etc/passwd`, `/etc/shadow`, `/etc/sudoers`, `/etc/sudoers.d/` — accounts and privileges.
- `~/.config/autostart/` — desktop env autostart.
- LD_PRELOAD env / `/etc/ld.so.preload` — library injection persistence.

### User activity
- `~/.bash_history` (or `zsh_history`, `fish_history`) — shell command history. Often cleared by attackers; check for absence/truncation.
- `~/.viminfo`, `~/.lesshst` — files viewed.
- `~/.local/share/recently-used.xbel` — recently opened files.
- `/tmp`, `/var/tmp`, `/dev/shm` — common malware staging directories.

### Process / runtime (live host)
- `/proc/<pid>/` — per-process info: `cmdline`, `exe` (link to binary), `cwd`, `environ`, `status`, `maps`, `fd/`.
- `ps auxf` — process tree.
- `lsof -p <pid>` — open files.
- `ss -tulpn` (modern) / `netstat -tulpn` — listening ports + owning process.
- `who`, `w`, `last`, `lastb` — sessions.

### Mounts & filesystems
- `/etc/fstab` — mount config.
- `mount` — current mounts.
- `/etc/exports` — NFS shares.

### Containers (on Linux hosts)
- `/var/lib/docker/`, `/var/lib/containerd/` — Docker / containerd state.
- Image layers, container filesystems.
- `crictl ps`, `docker ps`, container logs.

### Tool list for Linux forensics
- **dd / dcfldd** — image acquisition.
- **The Sleuth Kit / Autopsy**.
- **LiME** — Linux memory acquisition.
- **AVML** (Microsoft) — also memory acquisition.
- **Volatility 3** — memory analysis (Linux profiles).
- **chkrootkit / rkhunter** — rootkit hunters.
- **auditd** + **ausearch** — kernel audit framework.
- **journalctl** — systemd log query.
- **strings**, **xxd**, **hexdump** — binary inspection.
- **YARA** — pattern matching.

## Memory forensics

Memory captures evidence the disk doesn't have:
- Running processes and their full command lines.
- Network connections.
- Loaded DLLs / modules (including only-in-memory).
- Decrypted in-memory data (vs encrypted on disk).
- Process injection artifacts.
- Kernel rootkit hooks.

### Acquisition
- **Windows:** WinPMEM, FTK Imager, Magnet RAM Capture, Belkasoft Live RAM Capturer.
- **Linux:** LiME, AVML.
- **macOS:** Mac Memory Reader, OSXPMem.

### Analysis with Volatility
Common commands (Vol 2.x syntax; Vol 3 has updated commands):
- `pslist` — process list.
- `pstree` — parent-child process tree.
- `psscan` — scan for hidden / terminated processes (vs `pslist` which uses linked list and can be hidden by rootkits).
- `dlllist` — loaded DLLs per process.
- `handles` — open handles.
- `netscan` / `connscan` — network connections.
- `cmdscan`, `consoles` — command history in cmd.exe consoles.
- `malfind` — process injection / hollowing detection.
- `hollowfind` — process hollowing.
- `svcscan` — services.
- `hashdump` — local password hashes.
- `lsadump`, `mimikatz` (plugin) — secrets.
- `dumpfiles` — extract files from memory.
- `procdump` — extract process memory.
- `yarascan` — search memory for YARA rules.

### Memory-only indicators
- **Process hollowing** — legitimate process started suspended, image replaced.
- **DLL injection** — DLL loaded by a process via remote thread.
- **Reflective loading** — DLL loaded entirely in memory, no disk artifact.
- **Process doppelgänging / herpaderping** — abuse Windows transactions / file handle quirks to hide injection.
- **Rootkits** — kernel hooks visible only via memory cross-reference.

## Anti-forensics (what attackers do)

- **Log clearing** — `wevtutil cl Security`, deleting evtx files. Leaves traces (Event 1102 "Audit log cleared," gaps in sequence).
- **Timestomping** — modify file timestamps to evade timeline. MFT $STD_INFO vs $FILE_NAME mismatch is the tell.
- **`shred`, `sdelete`, `cipher /w`** — secure delete tools to overwrite freed space.
- **Encryption** — full-disk crypto (BitLocker, LUKS) with password unknown to forensics.
- **Steganography** — hide data in images, audio.
- **In-memory only** — never touch disk (fileless malware).
- **History wiping** — `history -c`, `> ~/.bash_history`, unsetting `HISTFILE`.
- **Living off the land** — using built-in tools (PowerShell, certutil, bitsadmin, mshta, regsvr32, rundll32) to avoid dropping files.

## Order of volatility (recap)

When acquiring evidence, work most-volatile first:
1. CPU registers, cache.
2. RAM.
3. Network state, ARP cache, routing tables.
4. Running processes.
5. Disk.
6. Remote logs / archive.
7. Physical media (printouts).

## Chain of custody

Every artifact handled must be tracked end-to-end. Document: what, where, when, who, why, how stored. Hashes (MD5 + SHA-256) at each transfer. Covered in detail in `03_02_incident_response_activities.md`.

## Exam tips

- Match the **artifact** to **what it proves**:
  - "Did this binary execute?" → Prefetch, Amcache, Sysmon 1, 4688.
  - "Was this USB device connected?" → SYSTEM\\USBSTOR, MountedDevices.
  - "What did the user search?" → Browser history, `bash_history`, NTUSER UserAssist.
  - "Was this file opened?" → LNK, Jump Lists, Shellbags, recently-used.
  - "What network connections were active?" → Memory (`netscan`), `netstat`, firewall logs.
- **Memory forensics** beats disk forensics for **fileless malware** and **process injection**.
- **Order of volatility** — RAM before disk.
- **Anti-forensics indicators** are themselves evidence — gaps, missing logs, timestomp mismatches all tell a story.
- **Sysmon and PowerShell logging** are not on by default — if a question mentions rich endpoint logging, check whether it would have been captured at all.

## Related

**Internal:**
- [01_01 OS basics](../01_security_operations/01_01_os_basics.md) — OS fundamentals these artifacts come from
- [01_09 Logs and monitoring](../01_security_operations/01_09_logs_and_monitoring.md) — Windows Event IDs, Sysmon
- [01_12 File and malware analysis](../01_security_operations/01_12_file_and_malware_analysis.md) — file-level deep dive
- [03_02 Incident response activities](03_02_incident_response_activities.md) — chain of custody, evidence acquisition

**External:**
- [Volatility 3 documentation](https://volatility3.readthedocs.io/)
- [Eric Zimmerman tools](https://ericzimmerman.github.io/) — KAPE, MFTECmd, RECmd, etc.
- [SANS Windows Forensic Analysis Poster](https://www.sans.org/posters/windows-forensic-analysis/)
- [SANS Linux Forensic Analysis Poster](https://www.sans.org/posters/linux-shell-survival-guide/)
- [Velociraptor](https://docs.velociraptor.app/) — endpoint visibility & DFIR
- [auditd documentation (Red Hat)](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/security_guide/chap-system_auditing)
