# Windows Security Event IDs

## Overview

Windows Security Event Logs are one of the primary data sources used by Security Operations Centers (SOCs) to detect malicious activity, investigate security incidents, and monitor endpoint behavior.

Every authentication attempt, privilege change, process execution, account modification, and audit event can generate a Windows Security Event ID. Understanding these events allows analysts to reconstruct attacker activity and identify suspicious behavior across the attack lifecycle.

This document highlights the Windows Security Event IDs most commonly encountered during real-world SOC investigations.

---

# Why Event IDs Matter

Windows Security Event IDs help analysts:

- Detect brute-force attacks
- Investigate compromised accounts
- Identify privilege escalation
- Monitor account management
- Detect persistence
- Investigate lateral movement
- Correlate endpoint activity
- Build SIEM detections
- Support incident response

---

# Most Important Windows Security Event IDs

| Event ID | Description | Priority |
|----------|-------------|----------|
| 4624 | Successful Logon | ⭐⭐⭐⭐⭐ |
| 4625 | Failed Logon | ⭐⭐⭐⭐⭐ |
| 4634 | Logoff | ⭐⭐⭐ |
| 4648 | Logon Using Explicit Credentials | ⭐⭐⭐⭐ |
| 4672 | Special Privileges Assigned | ⭐⭐⭐⭐⭐ |
| 4688 | Process Creation | ⭐⭐⭐⭐⭐ |
| 4689 | Process Termination | ⭐⭐⭐ |
| 4697 | Service Installed | ⭐⭐⭐⭐⭐ |
| 4698 | Scheduled Task Created | ⭐⭐⭐⭐ |
| 4702 | Scheduled Task Updated | ⭐⭐⭐⭐ |
| 4720 | User Account Created | ⭐⭐⭐⭐⭐ |
| 4722 | User Account Enabled | ⭐⭐⭐⭐ |
| 4723 | Password Change Attempt | ⭐⭐⭐⭐ |
| 4724 | Password Reset | ⭐⭐⭐⭐⭐ |
| 4725 | User Account Disabled | ⭐⭐⭐ |
| 4726 | User Account Deleted | ⭐⭐⭐⭐ |
| 4732 | User Added to Security Group | ⭐⭐⭐⭐⭐ |
| 4733 | User Removed from Security Group | ⭐⭐⭐⭐ |
| 4740 | Account Locked Out | ⭐⭐⭐⭐ |
| 4768 | Kerberos TGT Requested | ⭐⭐⭐⭐ |
| 4769 | Kerberos Service Ticket | ⭐⭐⭐⭐ |
| 4771 | Kerberos Authentication Failed | ⭐⭐⭐⭐ |
| 4776 | NTLM Authentication | ⭐⭐⭐⭐ |
| 5140 | Network Share Access | ⭐⭐⭐⭐ |
| 5156 | Windows Filtering Platform Allowed Connection | ⭐⭐⭐⭐⭐ |

---

# Critical Event IDs

## Event ID 4624 — Successful Logon

### Description

Generated whenever a user successfully authenticates to a Windows system.

### Why It Matters

This event is one of the most investigated Windows events because it confirms successful access to a system.

### Common Use Cases

- Successful RDP login
- Interactive logon
- Service authentication
- Lateral movement
- Compromised accounts

### Investigate

- Source IP
- Account Name
- Logon Type
- Workstation Name
- Authentication Package

---

## Event ID 4625 — Failed Logon

### Description

Generated whenever an authentication attempt fails.

### Why It Matters

A high number of failed logons often indicates password spraying or brute-force attacks.

### Common Use Cases

- Password spraying
- Brute-force attacks
- Invalid accounts
- Incorrect passwords

### Investigate

- Source IP
- Failure Reason
- Username
- Logon Type
- Frequency of failures

---

## Event ID 4672 — Special Privileges Assigned

### Description

Generated when an account logs on with administrative privileges.

### Why It Matters

Attackers frequently seek administrative privileges after initial access.

### Investigate

- Account Name
- Logon Session
- Source IP
- Associated 4624 event

---

## Event ID 4688 — Process Creation

### Description

Generated whenever a new process starts.

### Why It Matters

One of the most valuable events during malware investigations.

### Common Detections

- PowerShell
- cmd.exe
- rundll32.exe
- regsvr32.exe
- certutil.exe
- mshta.exe
- wscript.exe
- cscript.exe

### Investigate

- Parent Process
- Process Name
- Command Line
- User
- Integrity Level

---

## Event ID 4697 — Service Installed

### Description

A new Windows service has been installed.

### Why It Matters

Attackers often install malicious services for persistence.

### Investigate

- Service Name
- Binary Path
- Account
- Creator Process

---

## Event ID 4720 — User Account Created

### Description

A new local or domain account was created.

### Why It Matters

Unexpected account creation may indicate persistence or privilege escalation.

### Investigate

- Who created the account
- Privileges
- Creation time
- Group membership

---

## Event ID 4724 — Password Reset

### Description

An account password was reset.

### Why It Matters

Unexpected password resets are common after account compromise.

### Investigate

- Target account
- Initiating account
- Time
- Source system

---

## Event ID 4732 — User Added to Security Group

### Description

A user was added to a privileged group.

### Why It Matters

Membership changes may indicate privilege escalation.

### Investigate

- Group Name
- User Added
- Who performed the action
- Source computer

---

## Event ID 4740 — Account Locked Out

### Description

A user account became locked.

### Why It Matters

Often indicates password guessing or brute-force activity.

### Investigate

- Source workstation
- Username
- Number of failed attempts
- Correlate with Event 4625

---

## Event ID 4768 — Kerberos TGT Requested

### Description

A Kerberos Ticket Granting Ticket (TGT) was requested.

### Why It Matters

Useful for detecting Kerberos attacks and authentication anomalies.

---

## Event ID 4769 — Kerberos Service Ticket

### Description

A Kerberos service ticket was requested.

### Why It Matters

Frequently monitored for Kerberoasting activity.

---

## Event ID 4776 — NTLM Authentication

### Description

Credential validation using NTLM.

### Why It Matters

Useful for identifying legacy authentication and password attacks.

---

## Event ID 5140 — Network Share Access

### Description

A shared folder was accessed.

### Why It Matters

Useful for detecting lateral movement and data collection.

### Investigate

- Share Name
- Source IP
- Username
- Files accessed

---

## Event ID 5156 — Windows Filtering Platform Allowed Connection

### Description

A network connection was allowed by the Windows Filtering Platform.

### Why It Matters

Provides network telemetry for detecting:

- Port scanning
- Reverse shells
- Command and Control
- Malware communications
- Data exfiltration

This event was used extensively throughout this SOC Homelab to detect network reconnaissance and outbound malicious connections.

---

# Investigation Tips

When reviewing Windows Event IDs:

- Never investigate a single event in isolation.
- Correlate authentication, process, and network events.
- Look for unusual parent-child process relationships.
- Compare activity with historical baselines.
- Validate user activity with asset owners when necessary.
- Map suspicious behavior to the MITRE ATT&CK framework.

---

# Related Handbook Articles

- Sysmon Event IDs
- Windows Logon Types
- Threat Intelligence
- MITRE ATT&CK
- Splunk SPL Cheat Sheet
- Threat Hunting Methodology
