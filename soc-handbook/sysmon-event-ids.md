# Sysmon Event IDs

## Overview

Sysmon (System Monitor) is a Windows system service from Microsoft Sysinternals that provides detailed endpoint telemetry far beyond native Windows Event Logs.

Unlike standard Windows logging, Sysmon records process creation, network connections, registry changes, file creation, driver loading, process injection, and many other security-relevant events.

These logs are invaluable for detection engineering, threat hunting, malware analysis, and incident response.

---

# Why SOC Analysts Use Sysmon

Sysmon provides visibility into attacker behavior that is often invisible in default Windows logs.

It enables analysts to detect:

- Malware execution
- PowerShell abuse
- Credential dumping
- Process injection
- Registry persistence
- Reverse shells
- Lateral movement
- Command and Control (C2)
- File creation
- Network connections

Many modern SOC detections rely heavily on Sysmon telemetry.

---

# Most Important Sysmon Event IDs

| Event ID | Event | Why It Matters |
|----------|-------|----------------|
| 1 | Process Creation | Detects executed programs and command lines |
| 3 | Network Connection | Detects outbound and inbound connections |
| 5 | Process Terminated | Tracks process lifecycle |
| 7 | Image Loaded | Detects suspicious DLL loading |
| 8 | CreateRemoteThread | Detects process injection |
| 10 | Process Access | Detects LSASS access and credential dumping |
| 11 | File Create | Detects malware payload creation |
| 12 | Registry Object Create/Delete | Registry persistence |
| 13 | Registry Value Set | Registry Run Keys and persistence |
| 22 | DNS Query | Detects suspicious domains |

These events cover the majority of attacks encountered during SOC operations.

---

# Event ID 1 — Process Creation

## Description

Logs every process created on a Windows endpoint.

Captured fields include:

- Process Name
- Parent Process
- Command Line
- User
- Process GUID
- Hashes

Example:

```
powershell.exe -enc SQBFAFgA...
```

Common Detections

- Encoded PowerShell
- Mimikatz
- PsExec
- Certutil
- LOLBins
- Reverse shells

MITRE ATT&CK

- T1059
- T1105
- T1218

---

# Event ID 3 — Network Connection

## Description

Logs outbound TCP/UDP connections initiated by processes.

Captured fields:

- Source IP
- Destination IP
- Destination Port
- Process
- User

Example

```
powershell.exe

↓

192.168.50.40:4444
```

Common Detections

- Reverse shells
- Beaconing
- Malware C2
- PowerShell downloads
- Suspicious outbound traffic

MITRE ATT&CK

- T1071
- T1105
- T1041

---

# Event ID 5 — Process Terminated

## Description

Records when a process exits.

Useful for:

- Malware execution timeline
- Process lifecycle reconstruction
- Threat hunting

---

# Event ID 7 — Image Loaded

## Description

Logs DLLs loaded into processes.

Useful for detecting:

- DLL sideloading
- Malicious DLL injection
- Unsigned DLLs

Example

```
evil.dll

loaded into

explorer.exe
```

MITRE ATT&CK

T1574

---

# Event ID 8 — CreateRemoteThread

## Description

Records remote thread creation.

Highly valuable for detecting:

- Process Injection
- Meterpreter
- Cobalt Strike
- Malware

Common techniques

- CreateRemoteThread()
- Reflective DLL Injection

MITRE ATT&CK

T1055

---

# Event ID 10 — Process Access

## Description

One of the most important Sysmon events.

Records one process attempting to access another process.

Most commonly used for detecting:

- LSASS dumping
- Mimikatz
- Procdump
- Credential theft

Example

```
mimikatz.exe

↓

lsass.exe
```

Useful fields

- SourceImage
- TargetImage
- GrantedAccess

MITRE ATT&CK

T1003.001

---

# Event ID 11 — File Create

## Description

Logs newly created files.

Useful for detecting:

- Malware dropped
- Payload staging
- Ransomware
- Suspicious executables

Common paths

```
C:\Users\
C:\ProgramData\
C:\Temp\
```

---

# Event ID 12 — Registry Object Create/Delete

## Description

Records registry key creation and deletion.

Useful for detecting:

- Persistence
- Malware installation
- Registry abuse

MITRE ATT&CK

T1112

---

# Event ID 13 — Registry Value Set

## Description

Records registry value modifications.

Most commonly used to detect:

- Run Keys
- RunOnce Keys
- Malware persistence

Example

```
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

MITRE ATT&CK

T1547.001

---

# Event ID 22 — DNS Query

## Description

Records DNS lookups performed by processes.

Useful for:

- Malware domains
- C2 infrastructure
- Phishing domains
- DNS tunneling

Example

```
powershell.exe

↓

evil-domain.com
```

MITRE ATT&CK

T1071.004

---

# Common Detection Examples

| Activity | Primary Sysmon Events |
|-----------|-----------------------|
| PowerShell | 1, 3 |
| Reverse Shell | 1, 3 |
| Malware Execution | 1, 11 |
| Registry Persistence | 13 |
| Credential Dumping | 10 |
| Process Injection | 8 |
| DLL Hijacking | 7 |
| File Creation | 11 |
| DNS Tunneling | 22 |
| C2 Communication | 3, 22 |

---

# Sysmon vs Windows Event Logs

| Windows Event Logs | Sysmon |
|--------------------|---------|
| Logons | Process command lines |
| Account changes | Network connections |
| Service events | Registry changes |
| Basic auditing | File creation |
| Authentication | DNS queries |
| Limited telemetry | Rich endpoint telemetry |

---

# Best Practices

- Deploy a well-maintained Sysmon configuration (e.g., SwiftOnSecurity or Olaf Hartong).
- Collect Event IDs 1, 3, 10, 11, 13, and 22 at a minimum.
- Forward Sysmon logs into your SIEM for centralized monitoring.
- Correlate Sysmon telemetry with Windows Security Logs and firewall events.
- Build behavioral detections instead of relying solely on file names or hashes.

---

# Key Takeaways

Sysmon is one of the most valuable telemetry sources for Windows security monitoring. It provides detailed visibility into process execution, network activity, registry modifications, credential access, and persistence techniques, enabling SOC analysts to detect, investigate, and respond to modern cyber threats with greater accuracy.
