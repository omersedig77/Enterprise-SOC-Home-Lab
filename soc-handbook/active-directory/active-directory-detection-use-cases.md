# Active Directory Detection Use Cases

## Overview

Active Directory is one of the most important components in enterprise environments.

Because Active Directory controls:

- User identities
- Authentication
- Authorization
- Computer accounts
- Group memberships
- Security policies

attackers commonly target AD after gaining initial access.

A SOC must monitor Active Directory activity to detect:

- Credential attacks
- Privilege escalation
- Persistence
- Lateral movement
- Domain compromise

This document covers common real-world Active Directory detections used by SOC teams.

---

# Required Data Sources

Active Directory detections require visibility from multiple sources.

## Windows Security Logs

Common Event IDs:

```
4624  Successful Logon

4625  Failed Logon

4672  Special Privileges Assigned

4688  Process Creation

4720  User Account Created

4722  User Enabled

4728  Member Added To Global Group

4732  Member Added To Local Group

4738  User Account Changed

4740  Account Locked Out

4768  Kerberos Authentication Ticket Requested

4769  Kerberos Service Ticket Requested

4771  Kerberos Pre-Authentication Failed

5136  Directory Object Modified

7045  Service Created
```

---

# Detection 1

# Password Spray Attack Detection

## Overview

Password spraying is an attack where an attacker tries one password against many accounts instead of many passwords against one account.

Example:

```
Password:

Spring2026!


Accounts:

user1

user2

user3

user4
```

The goal is to avoid account lockouts.

---

# Attack Indicators

Look for:

- Many users failing authentication
- Same source IP
- Short timeframe
- Common password attempts

---

# Important Events

```
4625

4771
```

---

# Detection Logic

Detect:

```
One Source IP

+

Many Failed Users

+

Short Time Window
```

---

# Splunk Example

```spl
index=wineventlog EventCode=4625
| stats dc(TargetUserName) as users count by Source_Network_Address
| where users > 10
```

---

# Severity

Medium / High

---

# Investigation

Check:

```
Source IP reputation

Targeted accounts

Successful login after failures

Geolocation

User activity
```

---

# Detection 2

# Brute Force Login Detection

## Overview

Brute force attempts many passwords against a single account.

Example:

```
administrator

password1

password2

password3
```

---

# Indicators

- High failed login count
- Single account targeted
- Same source IP

---

# Events

```
4625

4624
```

---

# Detection Logic

```
Multiple failures

+

Successful login

=

Possible compromise
```

---

# Splunk Example

```spl
index=wineventlog (EventCode=4625 OR EventCode=4624)
| stats count(eval(EventCode=4625)) as failures
count(eval(EventCode=4624)) as success by User Source_Network_Address
| where failures > 10 AND success > 0
```

---

# Severity

High

---

# Detection 3

# Suspicious Kerberos Ticket Request (Kerberoasting)

## Overview

Kerberoasting targets service accounts.

Attackers request Kerberos service tickets and attempt offline password cracking.

---

# Attack Flow

```
User Account

       |

Request Service Ticket

       |

Extract Ticket

       |

Offline Password Cracking
```

---

# Detection Indicators

Look for:

- Large number of service ticket requests
- One user requesting many SPNs
- Abnormal encryption types

---

# Important Event

```
4769

Kerberos Service Ticket Requested
```

---

# Detection Logic

Detect:

```
Single User

+

Many Service Tickets

+

Short Time Period
```

---

# Splunk Example

```spl
index=wineventlog EventCode=4769
| stats count dc(ServiceName) as services by Account_Name
| where services > 20
```

---

# Severity

High

---

# Investigation

Review:

```
Requesting account

Requested services

Source computer

Account privilege
```

---

# Detection 4

# DCSync Attack Detection

## Overview

DCSync abuses Active Directory replication permissions to steal password hashes.

---

# Attack Indicators

Look for:

- Non-domain controllers requesting replication
- Replication permissions abuse

---

# Event

```
4662

Directory Service Access
```

---

# Detection Logic

Detect:

```
4662

+

Replication Rights

+

Non Domain Controller Source
```

---

# Splunk Example

```spl
index=wineventlog EventCode=4662
| search Properties="*Replication*"
| table _time SubjectUserName ObjectName Computer
```

---

# Severity

Critical

---

# Investigation

Check:

```
User account

Source machine

Domain Controller activity

Credential theft indicators
```

---

# Detection 5

# Golden Ticket Detection

## Overview

Golden Ticket attacks forge Kerberos authentication tickets using the KRBTGT account hash.

---

# Indicators

Look for:

- Abnormal Kerberos tickets
- Unusual administrator access
- Long ticket lifetime

---

# Events

```
4768

4769

4624
```

---

# Investigation

Review:

```
Ticket lifetime

Account

Source workstation

Authentication pattern
```

---

# Severity

Critical

---

# Detection 6

# New Domain Admin Account Created

## Overview

Attackers create or elevate accounts to maintain privileged access.

---

# Detection Indicators

Example:

```
New User

+

Added To Domain Admins
```

---

# Events

```
4720

4728
```

---

# Splunk Example

```spl
index=wineventlog (EventCode=4720 OR EventCode=4728)
| table _time SubjectUserName TargetUserName GroupName Computer
```

---

# Severity

Critical

---

# Investigation

Check:

```
Creator account

New account purpose

Group membership

Authentication activity
```

---

# Detection 7

# Privileged Group Modification

## Overview

Attackers add accounts to privileged groups.

Targets:

```
Domain Admins

Enterprise Admins

Administrators
```

---

# Events

```
4728

4732

4756
```

---

# Splunk Example

```spl
index=wineventlog EventCode=4728
| search Group_Name="Domain Admins"
| table _time Member_Name Subject_User_Name
```

---

# Severity

Critical

---

# Detection 8

# Suspicious LDAP Query Activity

## Overview

Attackers use LDAP queries for Active Directory reconnaissance.

They search for:

- Users
- Groups
- Computers
- Privileges

---

# Attack Examples

Finding:

```
Domain Admin members

Service accounts

High value computers
```

---

# Detection Sources

Monitor:

- Domain Controller logs
- LDAP auditing
- Network traffic

---

# Indicators

Suspicious:

- Large LDAP queries
- Unknown hosts
- Enumeration tools

---

# Severity

Medium

---

# Detection 9

# Account Lockout Abuse

## Overview

Attackers intentionally cause account lockouts.

Can be:

- Denial of service
- Password spraying indicator

---

# Event

```
4740

Account Locked Out
```

---

# Detection Logic

Look for:

```
Many locked accounts

+

Same source machine
```

---

# Splunk Example

```spl
index=wineventlog EventCode=4740
| stats count by Caller_Computer_Name TargetUserName
```

---

# Severity

Medium

---

# Detection 10

# Suspicious Service Creation

## Overview

Attackers create services for:

- Persistence
- Remote execution

---

# Event

```
7045

Service Created
```

---

# Indicators

Suspicious:

```
Service Binary:

C:\Temp

C:\Users

C:\ProgramData
```

---

# Splunk Example

```spl
index=wineventlog EventCode=7045
| search ImagePath="*Temp*" OR ImagePath="*Users*"
```

---

# Severity

High

---

# Detection 11

# Group Policy Modification Detection

## Overview

Attackers abuse GPOs for enterprise-wide persistence.

---

# Events

```
5136

4739
```

---

# Detection Logic

Detect:

```
GPO Modified

+

Unknown User

+

Sensitive Policy Change
```

---

# Severity

Critical

---

# Detection 12

# Suspicious Administrator Login

## Overview

Detect unusual privileged account usage.

---

# Indicators

Example:

```
Domain Admin

logging into

employee workstation
```

---

# Events

```
4624

4672
```

---

# Detection Logic

Monitor:

```
Privileged Account

+

Unexpected Source

+

Unusual Time
```

---

# Severity

High

---

# Active Directory Investigation Workflow

When an AD alert triggers:

```
Alert Generated

        |

Identify Account

        |

Identify Source Computer

        |

Review Authentication Events

        |

Check Privileges

        |

Review AD Changes

        |

Search Related Activity

        |

Contain If Malicious
```

---

# SOC AD Investigation Checklist

```
[ ] Account identified

[ ] Source IP identified

[ ] Authentication reviewed

[ ] Privileges reviewed

[ ] Group membership checked

[ ] AD modifications checked

[ ] Endpoint activity checked

[ ] Network activity checked

[ ] Threat intelligence checked

[ ] Containment performed
```

---

# Active Directory Monitoring Priorities

Highest priority detections:

```
1. DCSync

2. Golden Ticket

3. Domain Admin Changes

4. Password Spray

5. Kerberoasting

6. Suspicious Privileged Logins

7. GPO Modification

8. New Administrator Accounts

9. Service Creation

10. LDAP Reconnaissance
```

---

# Key Takeaways

Active Directory is usually the main target in enterprise attacks.

Most serious compromises involve:

- Credential attacks
- Privilege escalation
- Persistence
- Lateral movement

A mature SOC must combine:

- Windows Event Logs
- Sysmon
- Identity monitoring
- Network telemetry
- Threat intelligence

to detect and respond before attackers achieve full domain compromise.
