# Credential Access - LSASS Memory Dumping Behavior Detected

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Credential Access |
| Platform | Windows |
| Data Source | Sysmon |
| Event ID | Event ID 10 - Process Access |
| Severity | Critical |
| MITRE ATT&CK | T1003.001 - OS Credential Dumping: LSASS Memory |
| Schedule | Every 5 minutes |
| Alert Type | Behavioral Detection |
| Status | Validated |

---

# Overview

This detection identifies suspicious processes attempting to access the memory space of:

```
lsass.exe
```

The Local Security Authority Subsystem Service (LSASS) stores authentication material used by Windows.

Attackers commonly target LSASS memory to extract:

- Password hashes
- Kerberos tickets
- NTLM credentials
- Domain authentication material

Common credential dumping tools include:

- Mimikatz
- ProcDump
- Custom credential dumping utilities

---

# Detection Objective

The purpose of this detection is to identify unauthorized processes requesting sensitive access rights against LSASS.

The detection focuses on:

- Process behavior
- Memory access rights
- Source process
- Execution context
- Suspicious binaries

This approach detects credential dumping behavior regardless of the tool name used.

---

# Attack Scenario

During the Windows attack simulation, credential access was performed after obtaining persistence on the endpoint.

Attack flow:

```
Windows Compromise

        |

Malicious Execution

        |

LSASS Memory Access Attempt

        |

Credential Dumping Activity

        |

Sysmon Event ID 10 Generated

        |

Splunk Critical Alert Triggered
```

---

# Data Sources

## Sysmon Event ID 10

Provides visibility into:

- Target process
- Source process
- Granted access rights
- User context
- Process relationship

Collected through:

```
Windows Endpoint

        |

Splunk Universal Forwarder

        |

Splunk Enterprise
```

Index:

```
wineventlog
```

---

# SPL Detection

```spl
index=wineventlog EventCode=10 TargetImage="*\\lsass.exe"
| eval host_name = coalesce(Computer, host, "Unknown Endpoint")
| eval source_binary = SourceImage
| eval access_mask = GrantedAccess
| eval user_account = coalesce(User, "NT AUTHORITY\\SYSTEM")
| eval call_trace = CallTrace
| where (access_mask="0x1010" OR access_mask="0x1410" OR access_mask="0x1F3FFF" OR access_mask="0x1418" OR access_mask="0x1018")
  AND NOT match(source_binary, "(?i)\\\\((Windows\\\\System32\\\\(lsass|svchost|csrss|wininit|services|smss|winlogon|audiodg|msiexec))\\.exe|Program Files)")
| stats
    min(_time) as first_seen
    max(_time) as last_seen
    count by host_name, source_binary, access_mask, user_account
| eval "First Detection" = strftime(first_seen, "%Y-%m-%d %H:%M:%S")
| eval "Last Detection" = strftime(last_seen, "%Y-%m-%d %H:%M:%S")
| rename host_name as "Target Host", source_binary as "Unknown Process Name", user_account as "Execution Context", access_mask as "Granted Access Mask", count as "Access Attempts"
| table "First Detection", "Last Detection", "Target Host", "Unknown Process Name", "Execution Context", "Granted Access Mask", "Access Attempts"
```

---

# Detection Logic Explanation

## 1. Monitor LSASS Access

The detection monitors:

```
TargetImage = lsass.exe
```

Any process requesting access to LSASS memory is reviewed.

---

## 2. Analyze Granted Access Rights

Suspicious access masks include:

```
0x1010

0x1410

0x1F3FFF

0x1418

0x1018
```

These permissions allow processes to read or manipulate LSASS memory.

---

## 3. Exclude Legitimate Windows Processes

The detection excludes known Windows components such as:

```
svchost.exe

wininit.exe

services.exe

csrss.exe

winlogon.exe
```

This reduces false positives from normal operating system behavior.

---

# Alert Configuration

## Severity

```
Critical
```

---

## Trigger Condition

The alert triggers when an unauthorized process attempts to access LSASS memory with credential-dumping related permissions.

Example:

```
Source Process:

C:\Users\Public\mimikatz.exe


Target:

C:\Windows\System32\lsass.exe


Access:

0x1F3FFF
```

This behavior indicates possible credential theft activity.

---

## Schedule

```
Every 5 minutes
```

---

# Investigation Guidance

When this alert triggers:

---

# 1. Identify Source Process

Review:

```
Unknown Process Name
```

Determine:

- Process location
- File reputation
- Digital signature
- Parent process

Suspicious locations:

```
C:\Users\

C:\Temp\

C:\ProgramData\
```

---

# 2. Investigate Process Creation

Review Sysmon:

```
Event ID 1
Process Creation
```

Look for:

- Command line arguments
- Parent process
- User account
- Execution timeline

---

# 3. Review Authentication Activity

Check:

```
Windows Event ID 4624
Successful Logon

Windows Event ID 4625
Failed Logon
```

Determine:

- How the attacker accessed the system
- Compromised accounts
- Privilege level

---

# 4. Correlate Additional Activity

Investigate:

## Network Connections

```
Sysmon Event ID 3
```

Look for:

- C2 communication
- Data transfer
- External connections

---

## Malware Activity

Search for:

- Known dumping tools
- Suspicious binaries
- PowerShell execution

---

# Incident Response Actions

Recommended actions:

- Isolate the endpoint
- Disable compromised accounts
- Terminate malicious processes
- Collect forensic evidence
- Reset affected credentials
- Review lateral movement attempts
- Search environment for the same indicators

---

# False Positives

Possible legitimate activity:

- Security software
- Endpoint detection tools
- Backup applications
- Administrative troubleshooting tools

Validation should consider:

- Process reputation
- Execution path
- User context
- Business justification

---

# Validation

This detection was validated during the Windows Attack Simulation.

Technique:

```
MITRE ATT&CK T1003.001

OS Credential Dumping:
LSASS Memory
```

Simulation activity:

```
Windows Endpoint Compromise

        |

Credential Dumping Attempt

        |

LSASS Memory Access

        |

Sysmon Event ID 10

        |

Splunk Critical Alert
```

Result:

```
Detection Triggered Successfully
```

---

# Related Playbooks

Related response procedures:

```
incident-response/playbooks/credential-compromise.md

incident-response/playbooks/malware-investigation.md

incident-response/playbooks/windows-endpoint-compromise.md
```

---

# Lessons Learned

Monitoring LSASS access behavior provides defenders with visibility into one of the most common credential theft techniques used after endpoint compromise.

Behavior-based detection using Sysmon Event ID 10 allows SOC analysts to detect credential dumping attempts even when attackers modify or rename their tools.
