# Exploitation - Malicious Binary Outbound Reverse Shell Connection

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Command and Control / Reverse Shell |
| Platform | Windows |
| Data Source | Sysmon |
| Event ID | Event ID 3 - Network Connection |
| Severity | Critical |
| MITRE ATT&CK | T1059 - Command and Scripting Interpreter |
| MITRE ATT&CK | T1071 - Application Layer Protocol |
| Schedule | Every 2 minutes |
| Alert Type | Behavioral Detection |
| Status | Validated |

---

# Overview

This detection identifies potential reverse shell activity by detecting suspicious user-space binaries initiating outbound network connections over uncommon ports.

A reverse shell allows an attacker to establish remote command execution on a compromised endpoint by forcing the victim machine to connect back to attacker-controlled infrastructure.

Unlike traditional malware detections that rely on known hashes or filenames, this detection focuses on attacker behavior:

- Unknown binaries
- Execution from suspicious directories
- Outbound connections
- Non-standard communication ports

This approach helps identify custom payloads and previously unknown tooling.

---

# Detection Objective

The objective of this detection is to identify:

- Reverse shell execution
- Malware command and control communication
- Unauthorized outbound connections
- Suspicious binaries running from user-controlled locations
- Post-exploitation activity

---

# Attack Scenario

During validation, a reverse shell payload was generated on the Kali Linux attacker machine and executed on the Windows endpoint.

Attack flow:

```
Kali Linux Attacker
        |
        |
Reverse Shell Payload Created
        |
        |
Payload Delivered
        |
        |
Execution on Windows
        |
        |
Outbound Connection Established
        |
        |
Splunk Detection Triggered
```

Network communication:

```
Windows Endpoint
192.168.50.20

        |

Kali Listener
192.168.50.40:4444
```

---

# Data Sources

## Sysmon Event ID 3

Provides:

- Process network connections
- Source address
- Destination address
- Destination port
- Executing process

Collected through:

```
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
index=wineventlog EventCode=3 (DestinationPort!=80 AND DestinationPort!=443 AND DestinationPort!=53 AND DestinationPort!=3389)
| eval process_path = Image
| eval host_name = Computer
| eval connection_destination = DestinationIp . ":" . DestinationPort
| eval user_context = User
| where match(process_path, "\\Users\\") OR match(process_path, "\\ProgramData\\") OR match(process_path, "\\Temp\\")
| stats
    min(_time) as execution_time
    values(connection_destination) as "C2 Remote Address"
    values(user_context) as "Account Context"
    by host_name, process_path
| eval "Session Trigger Time" = strftime(execution_time, "%Y-%m-%d %H:%M:%S")
| rename host_name as "Compromised Host", process_path as "Malicious Binary Path"
| table "Session Trigger Time", "Compromised Host", "Account Context", "Malicious Binary Path", "C2 Remote Address"
```

---

# Detection Logic Explanation

The detection identifies suspicious outbound communication by applying multiple behavioral filters.

---

## 1. Exclude Common Web Traffic

The detection excludes common legitimate ports:

```
80   HTTP
443  HTTPS
53   DNS
3389 RDP
```

This reduces normal application noise.

---

## 2. Identify Suspicious Execution Locations

The detection focuses on binaries executed from:

```
Users
ProgramData
Temp
```

These directories are commonly abused by attackers because standard users can write files there.

---

## 3. Detect External Communication

The query identifies:

- Process path
- Destination IP
- Destination port
- User context

A suspicious binary creating outbound communication is treated as potential command-and-control activity.

---

# Alert Configuration

## Severity

```
Critical
```

---

## Trigger Condition

The alert triggers when an executable located in a user-writable directory initiates an outbound network connection over a non-standard application port.

Examples:

```
C:\Users\Public\payload.exe
        |
        |
192.168.50.40:4444
```

This behavior is consistent with:

- Reverse shells
- Custom malware
- C2 implants

---

## Schedule

```
Every 2 minutes
```

---

# Investigation Guidance

When this alert triggers, analysts should investigate:

---

# 1. Identify the Process

Review:

```
Malicious Binary Path
```

Questions:

- Is the binary known?
- Who created it?
- When was it created?
- Is it digitally signed?

---

# 2. Investigate Network Destination

Review:

```
C2 Remote Address
```

Determine:

- Internal or external destination
- Reputation of destination IP
- Communication frequency
- Port usage

---

# 3. Review User Context

Determine:

- Which account executed the binary
- Whether the account is privileged
- Whether compromise occurred through phishing or stolen credentials

---

# 4. Check Related Activity

Review:

Sysmon:

```
Event ID 1
Process Creation
```

Windows Security:

```
4624
Successful Logon
```

Persistence:

```
Registry Run Keys
Scheduled Tasks
Services
```

---

# 5. Incident Response Actions

Recommended analyst actions:

- Isolate endpoint
- Capture volatile evidence
- Terminate malicious process
- Block destination IP
- Remove persistence mechanisms
- Perform malware analysis

---

# False Positives

Possible legitimate activity:

- Internal testing tools
- Developer applications
- Custom enterprise software
- Remote administration utilities

Validation should consider:

- Binary reputation
- User context
- Destination ownership
- Business justification

---

# Validation

This detection was validated during the Windows Attack Simulation.

Technique mapping:

```
T1059
Command and Scripting Interpreter

T1071
Application Layer Protocol
```

Testing:

```
Kali Linux
       |
       |
Reverse Shell Listener
       |
       |
Windows Payload Execution
       |
       |
Outbound Connection
       |
       |
Splunk Alert
```

Result:

```
Detection Triggered Successfully
```

---

# Related Playbooks

Related incident response procedures:

```
incident-response/playbooks/command-and-control.md

incident-response/playbooks/malware-investigation.md

incident-response/playbooks/windows-endpoint-compromise.md
```

---

# Lessons Learned

Reverse shell detection is challenging because attackers often use legitimate tools and custom payloads.

Behavior-based detection provides stronger coverage by focusing on abnormal execution locations and unexpected network communication rather than relying only on known malware signatures.
