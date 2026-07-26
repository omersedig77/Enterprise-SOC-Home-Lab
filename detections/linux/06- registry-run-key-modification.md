# Persistence - Registry Run Key Modification Detected

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Persistence Detection |
| Platform | Windows |
| Data Source | Sysmon |
| Event ID | 13 - Registry Event |
| Severity | High |
| MITRE ATT&CK | T1547.001 - Registry Run Keys / Startup Folder |
| Schedule | Every 5 minutes |
| Alert Type | Behavior-Based Detection |
| Status | Validated |

---

# Overview

This detection identifies suspicious modifications to Windows Registry Run Keys and RunOnce keys.

Attackers commonly abuse registry startup locations to establish persistence by configuring malicious programs to execute automatically when a user logs in.

The detection monitors Sysmon Registry Events to identify suspicious payloads written into common Windows persistence locations.

---

# Detection Objective

The goal of this detection is to identify:

- Malware persistence mechanisms
- Unauthorized startup entries
- Registry-based persistence
- Post-compromise attacker activity

The detection provides visibility into:

- Modified registry location
- Payload path
- Executing process
- User context
- Target endpoint

---

# Attack Scenario

During the Windows attack simulation, persistence was established using a Registry Run Key modification.

The attacker configured a malicious payload to execute automatically after user login.

Attack flow:

```
Attacker Access

        |

Reverse Shell Established

        |

Registry Run Key Modified

        |

Payload Added To Startup Location

        |

Sysmon Event ID 13 Generated

        |

Splunk Detection Triggered

        |

SOC Investigation
```

---

# Lab Systems

| System | Role | IP |
|---|---|---|
| Kali Linux | Attacker | 192.168.50.40 |
| Windows 10 | Target Endpoint | 192.168.50.20 |
| Splunk Enterprise | SIEM | 192.168.50.101 |

---

# Data Source

## Sysmon Registry Events

Event:

```
Event ID 13
```

Description:

```
Registry Value Set
```

Sysmon provides visibility into:

- Registry modifications
- Modified keys
- Process responsible
- User context
- Payload details

Log flow:

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

# Monitored Registry Locations

The detection monitors:

```
HKCU\Software\Microsoft\Windows\CurrentVersion\Run

HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce

HKLM\Software\Microsoft\Windows\CurrentVersion\Run

HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

These locations execute applications automatically during startup or user login.

---

# SPL Detection

```spl
index=wineventlog EventCode=13 (TargetObject="*\\CurrentVersion\\Run*" OR TargetObject="*\\CurrentVersion\\RunOnce*")

| eval host_name = Computer
| eval registry_key = TargetObject
| eval registry_payload = Details
| eval modifying_process = Image
| eval user_context = User

| where match(registry_payload, "\\\\Users\\\\") 
OR match(registry_payload, "\\\\ProgramData\\\\") 
OR match(registry_payload, "\\\\Temp\\\\")

| stats
    min(_time) as modification_time
    values(registry_payload) as "Configured Payload"
    values(modifying_process) as "Modifying Process"
    values(user_context) as "Executed By"
    by host_name, registry_key

| eval "Persistence Time" = strftime(modification_time, "%Y-%m-%d %H:%M:%S")

| rename host_name as "Target Host",
registry_key as "Modified Registry Key"

| table 
"Persistence Time",
"Target Host",
"Modified Registry Key",
"Configured Payload",
"Modifying Process",
"Executed By"
```

---

# Detection Logic Explanation

## 1. Monitor Registry Persistence Locations

The query monitors:

```
Run
RunOnce
```

registry paths.

These locations are frequently abused by attackers.

---

## 2. Identify Suspicious Payload Locations

The detection focuses on registry values pointing to:

```
Users
ProgramData
Temp
```

directories.

Example suspicious entry:

```
C:\Users\User\AppData\malware.exe
```

---

## 3. Identify Responsible Process

The detection extracts:

```
Modifying Process
```

Example:

```
powershell.exe

cmd.exe

unknown.exe
```

---

# Alert Configuration

## Severity

```
High
```

---

## Trigger Condition

The alert triggers when a process modifies Windows Run or RunOnce registry locations and the configured payload points to suspicious user-controlled directories.

Example:

```
Registry:

HKCU\Software\Microsoft\Windows\CurrentVersion\Run


Payload:

C:\Users\User\payload.exe


Process:

powershell.exe
```

This behavior indicates possible persistence after compromise.

---

## Schedule

```
Every 5 minutes
```

---

# Investigation Workflow

When this alert triggers:

---

## 1. Review Registry Modification

Analyze:

```
Modified Registry Key
```

Determine:

- User hive or system hive
- Persistence location
- New value created

---

## 2. Analyze Payload

Review:

```
Configured Payload
```

Investigate:

- File location
- File hash
- Digital signature
- Reputation

---

## 3. Identify Process Responsible

Review:

```
Modifying Process
```

Determine:

- Legitimate installer
- Administrative activity
- Suspicious execution

---

## 4. Correlate With Other Detections

Search for:

```
Process creation

PowerShell activity

Reverse shell connections

Credential dumping

Firewall activity
```

Persistence usually occurs after initial compromise.

---

# Incident Response Actions

Recommended actions:

1. Confirm unauthorized registry modification.
2. Identify persistence payload.
3. Capture malware sample if required.
4. Remove malicious registry entry.
5. Terminate malicious processes.
6. Investigate initial compromise.
7. Verify endpoint integrity.

---

# False Positives

Possible legitimate activity:

- Software installations
- Enterprise applications
- Update mechanisms
- Administrative configuration changes

Validation should consider:

- Software ownership
- Change approvals
- Executing user
- Payload location

---

# Validation

This detection was validated during the Windows attack simulation.

Simulation:

```
Initial Access

        |

Reverse Shell

        |

Registry Run Key Persistence

        |

Sysmon Event ID 13

        |

Splunk Alert Triggered
```

Result:

```
Detection Triggered Successfully
```

---

# Related Detections

```
detections/windows/unautorized-registry-run-key.md

detections/windows/reverse-shell.md

detections/windows/powershell-download-cradle.md

detections/windows/lsass-dumping.md
```

---

# Lessons Learned

Registry Run Keys are a common persistence technique used by attackers because they provide reliable execution after login.

Monitoring registry modifications with Sysmon provides SOC analysts with visibility into persistence attempts and allows investigation before attackers maintain long-term access.
