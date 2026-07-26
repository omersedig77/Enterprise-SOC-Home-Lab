# Persistence - Registry Run Key Modification Detected

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Persistence |
| Platform | Windows |
| Data Source | Sysmon |
| Event ID | Event ID 13 - Registry Value Set |
| Severity | High |
| MITRE ATT&CK | T1547.001 - Registry Run Keys / Startup Folder |
| Schedule | Every 5 minutes |
| Alert Type | Behavioral Detection |
| Status | Validated |

---

# Overview

This detection identifies suspicious modifications to Windows Registry Run Keys, a common persistence technique used by attackers to automatically execute malicious programs when a user logs into the system.

Attackers commonly abuse:

```
HKCU\Software\Microsoft\Windows\CurrentVersion\Run

HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce

HKLM\Software\Microsoft\Windows\CurrentVersion\Run

HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

By adding malicious entries to these locations, attackers can maintain access after reboot or user logon.

---

# Detection Objective

The purpose of this detection is to identify:

- Malware persistence mechanisms
- Unauthorized startup programs
- Reverse shell persistence
- User-level persistence attempts
- Registry abuse after compromise

---

# Attack Scenario

During the Windows attack simulation, persistence was established by creating a Registry Run Key entry.

Attack flow:

```
Initial Compromise
        |
        |
Payload Execution
        |
        |
Registry Run Key Created
        |
        |
System Restart / User Login
        |
        |
Malicious Payload Executes
        |
        |
Splunk Detection Triggered
```

---

# Data Sources

## Sysmon Event ID 13

Provides visibility into:

- Registry modifications
- Registry paths
- Modified values
- Writing process
- User context

Collected through:

```
Windows Endpoint
        |
        |
Splunk Universal Forwarder
        |
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
index=wineventlog EventCode=13 (TargetObject="*\\CurrentVersion\\Run*" OR TargetObject="*\\CurrentVersion\\RunOnce*")
| eval host_name = Computer
| eval registry_key = TargetObject
| eval registry_payload = Details
| eval modifying_process = Image
| eval user_context = User
| where match(registry_payload, "\\\\Users\\\\") OR match(registry_payload, "\\\\ProgramData\\\\") OR match(registry_payload, "\\\\Temp\\\\")
| stats
    min(_time) as modification_time
    values(registry_payload) as "Configured Payload"
    values(modifying_process) as "Modifying Process"
    values(user_context) as "Executed By"
    by host_name, registry_key
| eval "Persistence Time" = strftime(modification_time, "%Y-%m-%d %H:%M:%S")
| rename host_name as "Target Host", registry_key as "Modified Registry Key"
| table "Persistence Time", "Target Host", "Modified Registry Key", "Configured Payload", "Modifying Process", "Executed By"
```

---

# Detection Logic Explanation

## 1. Monitor Registry Persistence Locations

The query monitors:

```
CurrentVersion\Run

CurrentVersion\RunOnce
```

These registry locations are frequently abused for automatic execution.

---

## 2. Identify Suspicious Payload Locations

The detection focuses on registry values pointing to:

```
C:\Users\

C:\ProgramData\

C:\Temp\
```

These locations are commonly used by attackers to store malicious binaries.

---

## 3. Collect Investigation Data

The alert provides:

| Field | Purpose |
|-|-|
| Target Host | Affected endpoint |
| Registry Key | Modified persistence location |
| Payload | Executed file |
| Process | Process responsible for change |
| User | Account performing modification |

---

# Alert Configuration

## Severity

```
High
```

---

## Trigger Condition

The alert triggers when a process modifies Windows Run or RunOnce registry keys and the configured payload points to a user-controlled or suspicious directory.

Example:

```
Registry:

HKCU\Software\Microsoft\Windows\CurrentVersion\Run


Value:

Updater


Data:

C:\Users\Public\payload.exe
```

This behavior indicates possible malicious persistence.

---

## Schedule

```
Every 5 minutes
```

---

# Investigation Guidance

When this alert triggers:

---

# 1. Review Registry Modification

Check:

```
Modified Registry Key

Configured Payload
```

Determine:

- What executable will run?
- Is the entry expected?
- Was it recently created?

---

# 2. Investigate Creating Process

Review:

```
Modifying Process
```

Questions:

- Was the process signed?
- Was it executed from a suspicious location?
- Is it associated with another alert?

---

# 3. Review User Activity

Investigate:

```
Executed By
```

Determine:

- User account involved
- Privilege level
- Recent authentication activity

---

# 4. Correlate Events

Review related telemetry:

## Sysmon

```
Event ID 1
Process Creation
```

## Sysmon

```
Event ID 3
Network Connections
```

## Windows Security

```
4624
Successful Logon
```

---

# Incident Response Actions

Recommended actions:

- Isolate affected endpoint
- Disable compromised account if required
- Remove malicious registry entry
- Terminate malicious processes
- Search for additional persistence mechanisms
- Perform malware analysis

---

# False Positives

Possible legitimate activity:

- Enterprise software installers
- Auto-update applications
- Security software
- Corporate management tools

Validation should consider:

- Software reputation
- File location
- Digital signature
- User authorization

---

# Validation

This detection was validated during the Windows Attack Simulation.

Technique:

```
MITRE ATT&CK T1547.001

Registry Run Keys / Startup Folder
```

Simulation:

```
Compromised Windows Host

        |

Registry Run Key Created

        |

Sysmon Event ID 13 Generated

        |

Splunk Alert Triggered
```

Result:

```
Detection Triggered Successfully
```

---

# Related Playbooks

Related response procedures:

```
incident-response/playbooks/windows-endpoint-compromise.md

incident-response/playbooks/malware-investigation.md

incident-response/playbooks/persistence-investigation.md
```

---

# Lessons Learned

Registry Run Keys remain one of the most common Windows persistence techniques because they require minimal privileges and survive user logon events.

Monitoring registry modifications with Sysmon provides defenders with visibility into attacker persistence behavior before long-term access is established.
