# Active Directory Threat Hunting

## Overview

Threat hunting is the proactive process of searching for malicious activity that may have bypassed traditional security detections.

Unlike alert-based monitoring, threat hunting does not wait for an alert.

SOC analysts actively search for:

- Compromised accounts
- Privilege escalation
- Lateral movement
- Persistence mechanisms
- Credential abuse
- Domain compromise activity

Active Directory is a primary target for attackers because controlling AD often means controlling the entire organization.

---

# Active Directory Threat Hunting Objectives

The main objectives are:

- Identify compromised identities
- Detect abnormal authentication behavior
- Discover unauthorized privilege changes
- Find hidden persistence
- Detect attacker movement inside the domain
- Identify early signs of domain compromise

---

# Required Data Sources

Effective AD threat hunting requires:

## Windows Security Logs

Important Event IDs:

```
4624
Successful Logon

4625
Failed Logon

4672
Special Privileges Assigned

4688
Process Creation

4720
User Created

4722
User Enabled

4728
Member Added To Global Group

4732
Member Added To Local Group

4740
Account Locked Out

4768
Kerberos Authentication Ticket

4769
Kerberos Service Ticket

4771
Kerberos Authentication Failure

5136
Directory Object Modified

7045
Service Created
```

---

## Sysmon

Important events:

```
Event ID 1

Process Creation


Event ID 3

Network Connection


Event ID 10

Process Access


Event ID 11

File Creation


Event ID 22

DNS Query
```

---

## Network Data

Monitor:

- SMB traffic
- LDAP traffic
- Kerberos traffic
- RDP connections
- WinRM connections

---

# Threat Hunting Methodology

The hunting process:

```
Define Hypothesis

        |

Collect Data

        |

Search For Indicators

        |

Analyze Findings

        |

Validate Threat

        |

Improve Detection
```

---

# Hunt 1: Suspicious Privileged Account Usage

## Objective

Identify abnormal use of privileged accounts.

Attackers commonly use stolen administrator credentials.

---

# Hunting Hypothesis

Example:

```
A Domain Admin account is being used from an unusual workstation.
```

---

# Indicators

Look for:

- Domain Admin login
- Unknown source computer
- Unusual working hours
- New geographic location

---

# Events

```
4624

4672
```

---

# Splunk Query

```spl
index=wineventlog EventCode=4624
| search Group="Domain Admins"
| stats count values(Computer) by Account_Name Source_Network_Address
```

---

# Investigation

Review:

```
Account

Source IP

Computer

Login Time

Previous Activity
```

---

# Hunt 2: Password Spray Activity

## Objective

Find attackers testing passwords against many accounts.

---

# Hunting Hypothesis

```
A single source is attempting authentication against many users.
```

---

# Indicators

- Many failed logins
- Multiple usernames
- Same source

---

# Events

```
4625

4771
```

---

# Splunk Query

```spl
index=wineventlog EventCode=4625
| stats dc(TargetUserName) as Users count by Source_Network_Address
| where Users > 10
```

---

# Investigation

Check:

```
Source reputation

Target users

Successful logins after failures

Authentication location
```

---

# Hunt 3: Kerberoasting Activity

## Objective

Detect attackers requesting service tickets for offline cracking.

---

# Hunting Hypothesis

```
A compromised user is requesting unusual numbers of service tickets.
```

---

# Indicators

- Many SPNs requested
- Single user requesting many services
- Unusual accounts requesting tickets

---

# Event

```
4769
```

---

# Splunk Query

```spl
index=wineventlog EventCode=4769
| stats count dc(ServiceName) as Service_Count by Account_Name
| where Service_Count > 20
```

---

# Investigation

Review:

```
Account

Requested Services

Source Host

Time Period
```

---

# Hunt 4: DCSync Activity

## Objective

Identify attempts to extract password hashes from Active Directory.

---

# Hunting Hypothesis

```
A non-domain controller is requesting replication data.
```

---

# Indicators

Look for:

- Replication permissions
- Suspicious accounts
- Unknown machines

---

# Event

```
4662
```

---

# Splunk Query

```spl
index=wineventlog EventCode=4662
| search Properties="*Replication*"
| table _time SubjectUserName Computer ObjectName
```

---

# Investigation

Check:

```
Actor

Computer

Permissions

Domain Controller activity
```

---

# Hunt 5: Golden Ticket Activity

## Objective

Find forged Kerberos tickets.

---

# Hunting Hypothesis

```
An attacker is using forged Kerberos authentication.
```

---

# Indicators

- Abnormal ticket lifetime
- Privileged access from unusual systems
- Impossible authentication patterns

---

# Events

```
4768

4769

4624
```

---

# Investigation

Look for:

```
User

Source Computer

Ticket Information

Authentication Pattern
```

---

# Hunt 6: New Administrator Accounts

## Objective

Find attacker-created accounts.

---

# Hunting Hypothesis

```
An attacker created a hidden administrative account.
```

---

# Events

```
4720

4728

4732
```

---

# Splunk Query

```spl
index=wineventlog (EventCode=4720 OR EventCode=4728 OR EventCode=4732)
| table _time SubjectUserName TargetUserName Group_Name Computer
```

---

# Investigation

Review:

```
Who created account?

Why?

When?

Permissions?

Login activity?
```

---

# Hunt 7: Abnormal Group Membership Changes

## Objective

Detect privilege escalation.

---

# Target Groups

Monitor:

```
Domain Admins

Enterprise Admins

Administrators

Backup Operators
```

---

# Events

```
4728

4732

4756
```

---

# Splunk Query

```spl
index=wineventlog EventCode=4728
| search Group_Name="Domain Admins"
| table _time Member_Name Subject_User_Name
```

---

# Hunt 8: Suspicious LDAP Reconnaissance

## Objective

Detect attackers enumerating Active Directory.

---

# Common Attacker Searches

Attackers search for:

- Domain Admins
- Service accounts
- Computers
- Trust relationships

---

# Indicators

- Large LDAP queries
- Unknown systems
- Enumeration tools

---

# Investigation

Review:

```
Source Host

User

Query Volume

Timing
```

---

# Hunt 9: Lateral Movement Detection

## Objective

Detect attackers moving between systems.

---

# Common Protocols

```
RDP

SMB

WinRM

WMI
```

---

# Indicators

Example:

```
Employee Laptop

        |

Server Access

        |

Administrator Login
```

---

# Events

```
4624

4688

7045
```

---

# Splunk Query

```spl
index=wineventlog EventCode=4624
| stats count by Source_Network_Address Computer Account_Name
```

---

# Hunt 10: Persistence Hunting

## Objective

Find attacker persistence mechanisms.

---

# Search For

## New Services

Event:

```
7045
```

---

## Scheduled Tasks

Events:

```
4698

4702
```

---

## Registry Persistence

Sysmon:

```
Event ID 13
```

---

## Group Policy Changes

Events:

```
5136

4739
```

---

# Active Directory Threat Hunting Checklist

```
[ ] Review privileged logins

[ ] Hunt password spraying

[ ] Review Kerberos activity

[ ] Hunt Kerberoasting

[ ] Check replication abuse

[ ] Review Domain Admin changes

[ ] Search new accounts

[ ] Review GPO modifications

[ ] Check services

[ ] Check scheduled tasks

[ ] Investigate lateral movement

[ ] Review suspicious LDAP activity
```

---

# Threat Hunting Reports

Every hunt should document:

## Hypothesis

Example:

```
Attackers may be abusing privileged accounts.
```

---

## Data Sources

Example:

```
Windows Security Logs

Sysmon

Network Logs
```

---

## Findings

Document:

```
What was found?

Affected systems?

Accounts?

Timeline?
```

---

## Action

Document:

```
Detection created

Incident opened

False positive

Monitoring improvement
```

---

# Improving Detection After Hunting

Threat hunting should improve SOC capability.

Example:

Hunt discovers:

```
Unauthorized Domain Admin Addition
```

Create:

```
New Detection Rule

+

Alert

+

Playbook
```

---

# Key Takeaways

Active Directory threat hunting helps discover attacks before they become major incidents.

The highest-value AD hunts focus on:

- Privileged account abuse
- Kerberos attacks
- Credential theft
- Lateral movement
- Persistence
- Domain compromise

A mature SOC continuously hunts Active Directory because identity compromise is often the beginning of a full enterprise breach.
