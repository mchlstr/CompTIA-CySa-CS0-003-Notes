# OS Basics

A SOC analyst reads logs and artifacts from operating systems daily. You need to know where things live and what "normal" looks like.

## Windows: Registry and processes

**Registry** — hierarchical database storing OS and application configuration. Five root hives:
- **HKLM (HKEY_LOCAL_MACHINE)** — system-wide settings.
- **HKCU (HKEY_CURRENT_USER)** — current user.
- **HKCR (HKEY_CLASSES_ROOT)** — file associations.
- **HKU (HKEY_USERS)** — all loaded user profiles.
- **HKCC (HKEY_CURRENT_CONFIG)** — current hardware profile.

**Persistence keys to know** (malware loves these):
- `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKLM\System\CurrentControlSet\Services` — service definitions.
- Scheduled Tasks (`%WINDIR%\System32\Tasks`).

**Processes** — running instances of executables. Tools: Task Manager, **Process Explorer / Process Hacker** (Sysinternals), `tasklist`, PowerShell `Get-Process`. Look for:
- Unsigned binaries running from `%TEMP%`, `%APPDATA%`.
- Parent-child mismatches (`winword.exe` spawning `cmd.exe` = suspicious).
- Processes with unusual network connections (`netstat -anob`).

## File structure & configuration locations

**Windows:**
- `C:\Windows\System32` — core OS binaries.
- `C:\Windows\System32\config` — registry hive files (SAM, SYSTEM, SECURITY).
- `C:\Windows\System32\winevt\Logs` — event logs (.evtx).
- `C:\Users\<user>\AppData\` — per-user app data (Local, Roaming, LocalLow).
- `C:\ProgramData` — system-wide app data.

**Linux:**
- `/etc/` — system configuration files.
- `/var/log/` — logs (auth.log, syslog, messages, secure).
- `/home/<user>/` — user data.
- `/proc/` and `/sys/` — kernel/process info (virtual filesystems).
- `/tmp/`, `/var/tmp/`, `/dev/shm/` — common malware drop locations.
- `~/.bashrc`, `~/.profile`, `/etc/cron*`, `/etc/systemd/system/` — persistence spots.

## Hardware architecture

Knowing the hardware layer matters for low-level threats and forensic acquisition.

- **CPU rings** — Ring 0 (kernel) vs Ring 3 (user). Rootkits aim for Ring 0 to hide.
- **Memory (RAM)** — volatile; lost on power-off. Forensics tools (Volatility, FTK Imager) capture it for analysis.
- **Storage** — HDDs, SSDs (TRIM complicates forensics — deleted data may be wiped immediately), NVMe.
- **Firmware/UEFI** — runs before the OS. UEFI rootkits (e.g., LoJax) survive OS reinstall. Secure Boot helps mitigate.
- **TPM (Trusted Platform Module)** — hardware crypto chip; stores BitLocker keys, supports attestation.
- **HSM (Hardware Security Module)** — dedicated crypto hardware for enterprise key management.

**Exam tip:** if a question mentions persistence surviving OS reinstall → think UEFI/firmware. If it mentions volatile evidence → memory acquisition first, in correct order of volatility.
