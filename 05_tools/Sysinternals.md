# Sysinternals

## What it is
Suite of Windows troubleshooting and diagnostic tools.

## Purpose
Analyze, monitor, and manage Windows systems.

## Key Features
- Process, file, and registry monitoring  
- Remote administration utilities  
- Security auditing tools

## Practical Use Cases
- Root cause analysis for incidents  
- Malware and anomaly investigation  
- System performance monitoring

## Tips
- Learn each tool’s purpose; some require admin rights  
- Combine with EDR logs for deeper analysis

-----

# Sigcheck

## What it is
A Sysinternals command-line utility for verifying digital signatures and inspecting file version information on Windows systems.

## Purpose
Ensure the authenticity and integrity of executable files and DLLs, and detect potential tampering or malware.

## Key Features
- Verify digital signatures against the Microsoft root certificate store  
- Display file version, timestamp, and checksum  
- Recursive scanning of directories for unsigned or suspicious files  
- Detect rootkit or malware modifications

## Practical Use Cases
- Audit system files for integrity  
- Investigate unknown executables during incident response  
- Check downloaded software for valid signatures

## Tips
- Run with administrative privileges for full access  
- Combine with Sysinternals tools for comprehensive endpoint investigation
