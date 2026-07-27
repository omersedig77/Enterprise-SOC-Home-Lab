# Important Active Directory Event IDs

## Overview

Windows Active Directory generates security events that provide visibility into:

- Authentication activity
- Account changes
- Privilege escalation
- Group modifications
- Kerberos activity
- Directory modifications
- Lateral movement

For SOC analysts, understanding Active Directory Event IDs is critical because attackers commonly interact with AD during:

- Initial access
- Credential theft
- Privilege escalation
- Persistence
- Domain compromise

These events are collected from:

- Domain Controllers
- Windows endpoints
- Security Event Logs
- Sysmon
- SIEM platforms such as Splunk

---

# Authentication Event IDs

---

# Event ID 4624

## Successful Logon

## Description

Generated when a user successfully authenticates to a Windows system.

This is one of the most important events for SOC investigations.

---

## Important Fields

```
Account Name

Account Domain

Source Network Address

Logon Type

Authentication Package
```

---

## Common Logon Types

| Logon Type | Description |
|---|---|
| 2 | Interactive Login |
| 3 | Network Login |
| 5 | Service Login |
| 7 | Unlock |
| 8 | Network Clear Text |
| 9 | New Credentials |
| 10 | Remote Interactive (RDP) |
| 11 | Cached Interactive |

---

## SOC Use Cases

Detect:

- Unauthorized logins
- RDP access
- Lateral movement
- Privileged account usage

---

## Investigation Example

Suspicious:

```
Administrator

Logon Type: 10

Source IP: Unknown External IP
```

Possible:

```
Compromised RDP Account
```

---

## MITRE ATT&CK

T1078 - Valid Accounts

---

# Event ID 4625

## Failed Logon

## Description

Generated when authentication fails.

Common causes:

- Wrong password
- Brute force
- Password spraying
- Expired account

---

## Important Fields

```
Account Name

Source Address

Failure Reason

Logon Type
```

---

## SOC Use Cases

Detect:

- Password attacks
- Account compromise attempts
- Suspicious login activity

---

## Detection Example

```
100 failed logins

+

1 successful login

=

Possible account compromise
```

---

## MITRE ATT&CK

T1110 - Brute Force

---

# Event ID 4648

## Explicit Credential Use

## Description

Generated when a user attempts authentication using provided credentials.

Examples:

- Run As
- Remote connections
- Administrative tools

---

## SOC Use Cases

Detect:

- Credential abuse
- Suspicious administrator activity

---

## MITRE ATT&CK

T1550 - Use Alternate Authentication Material

---

# Event ID 4672

## Special Privileges Assigned To New Logon

## Description

Generated when an account receives powerful privileges.

Common accounts:

```
Administrator

SYSTEM

Domain Admins
```

---

## Important Privileges

Examples:

```
SeDebugPrivilege

SeBackupPrivilege

SeTakeOwnershipPrivilege
```

---

## SOC Use Cases

Detect:

- Privileged account usage
- Privilege escalation

---

# Account Management Events

---

# Event ID 4720

## User Account Created

## Description

Generated when a new user account is created.

---

## Attack Scenario

Attackers create accounts for persistence.

Example:

```
attacker_user

+

Administrator privileges
```

---

## Investigation

Check:

```
Who created account?

When?

Where?

What permissions?
```

---

## MITRE ATT&CK

T1136 - Create Account

---

# Event ID 4722

## User Account Enabled

## Description

Generated when a disabled account becomes active.

---

## SOC Use Cases

Detect:

- Unauthorized account activation
- Persistence attempts

---

# Event ID 4725

## User Account Disabled

Used to track account status changes.

---

# Event ID 4726

## User Account Deleted

Investigate:

- Unauthorized cleanup
- Attacker activity removal

---

# Event ID 4738

## User Account Changed

Generated when user attributes change.

Examples:

- Password changes
- Account properties modified

---

## SOC Use Cases

Detect:

- Privilege changes
- Persistence

---

# Group Management Events

---

# Event ID 4728

## Member Added To Security Enabled Global Group

## Description

Generated when a user is added to a global security group.

Example:

```
User Added To Domain Admins
```

---

## SOC Importance

Critical event.

Attackers commonly add accounts to:

```
Domain Admins

Enterprise Admins
```

---

## MITRE ATT&CK

T1098 - Account Manipulation

---

# Event ID 4729

## Member Removed From Security Group

Used to investigate:

- Unauthorized privilege removal
- Account manipulation

---

# Event ID 4732

## Member Added To Local Security Enabled Group

Example:

```
User added to Local Administrators
```

---

## SOC Use Cases

Detect:

- Local privilege escalation
- Persistence

---

# Event ID 4733

## Member Removed From Local Group

---

# Event ID 4756

## Member Added To Universal Security Group

Important in multi-domain environments.

---

# Account Lockout Events

---

# Event ID 4740

## User Account Locked Out

## Description

Generated when an account becomes locked.

---

## Common Causes

- Brute force
- Password spraying
- Service account issues

---

## Investigation

Review:

```
Caller Computer Name

Source IP

Failed Authentication Events
```

---

# Kerberos Events

---

# Event ID 4768

## Kerberos Authentication Ticket Requested

## Description

Generated when a user requests a Ticket Granting Ticket (TGT).

---

## SOC Use Cases

Detect:

- Kerberos attacks
- Authentication anomalies

---

## Important Fields

```
Account Name

Client Address

Encryption Type
```

---

# Event ID 4769

## Kerberos Service Ticket Requested

## Description

Generated when requesting access to services.

---

## Important For

Detecting:

- Kerberoasting
- Abnormal service access

---

## Suspicious Indicators

```
Many service tickets

+

Single user

+

RC4 encryption
```

---

## MITRE ATT&CK

T1558.003 - Kerberoasting

---

# Event ID 4771

## Kerberos Pre-Authentication Failed

## Description

Generated when Kerberos authentication fails.

---

## SOC Use Cases

Detect:

- Password spraying
- Brute force attempts

---

# Event ID 4776

## NTLM Authentication Attempt

## Description

Generated when domain authentication is performed using NTLM.

---

## SOC Use Cases

Detect:

- Pass-the-Hash
- NTLM abuse

---

# Directory Service Events

---

# Event ID 5136

## Directory Service Object Modified

## Description

Generated when an object in Active Directory is modified.

Examples:

- User changes
- Group changes
- Permission changes

---

## SOC Use Cases

Detect:

- AD manipulation
- Persistence
- Privilege escalation

---

# Event ID 5137

## Directory Service Object Created

Detect:

- Unauthorized objects
- Persistence

---

# Event ID 5138

## Directory Service Object Restored

---

# Event ID 5141

## Directory Service Object Deleted

---

# Policy Modification Events

---

# Event ID 4719

## System Audit Policy Changed

## Description

Generated when audit policy settings are modified.

---

## Security Importance

Attackers may disable logging before performing malicious activity.

---

## MITRE ATT&CK

T1562.002 - Disable Windows Event Logging

---

# Group Policy Events

---

# Event ID 4739

## Domain Policy Changed

Used to detect:

- Group Policy abuse
- Security setting changes

---

# Event ID 5136

Also important for:

- Group Policy object modifications
- AD object changes

---

# Computer Account Events

---

# Event ID 4741

## Computer Account Created

Detect:

- Unauthorized machines joining domain

---

# Event ID 4742

## Computer Account Changed

Detect:

- Machine modifications
- Domain abuse

---

# Service Account Monitoring

Important because attackers target service accounts.

Monitor:

```
4720

4768

4769

4624

4672
```

Look for:

- New service accounts
- Privilege changes
- Abnormal authentication

---

# High Priority AD Events For SOC Monitoring

| Event ID | Description | Severity |
|-|-|-|
| 4624 | Successful Logon | Medium |
| 4625 | Failed Logon | Medium |
| 4672 | Privileged Logon | High |
| 4720 | User Created | High |
| 4728 | Added To Domain Admin Group | Critical |
| 4732 | Added To Local Admin Group | High |
| 4740 | Account Lockout | Medium |
| 4768 | Kerberos Authentication | Medium |
| 4769 | Service Ticket Request | High |
| 4771 | Kerberos Failure | Medium |
| 4776 | NTLM Authentication | Medium |
| 5136 | AD Object Modified | High |
| 4719 | Audit Policy Changed | Critical |

---

# Splunk Detection Examples

## Detect New User Creation

```spl
index=wineventlog EventCode=4720
| table _time Subject_User_Name Target_User_Name Computer
```

---

## Detect Domain Admin Group Changes

```spl
index=wineventlog EventCode=4728
| search Group_Name="Domain Admins"
| table _time Member_Name Subject_User_Name Computer
```

---

## Detect Failed Logins

```spl
index=wineventlog EventCode=4625
| stats count by Account_Name Source_Network_Address
| where count > 10
```

---

## Detect Privileged Logins

```spl
index=wineventlog EventCode=4672
| table _time Account_Name Computer Privilege_List
```

---

# SOC Investigation Workflow

When an AD alert triggers:

```
Alert Generated

        |

Identify User

        |

Identify Source Computer

        |

Review Authentication Events

        |

Review Privileges

        |

Check Group Membership

        |

Review Related Processes

        |

Determine Impact

        |

Contain Account
```

---

# Key Takeaways

Active Directory Event IDs provide visibility into identity activity inside enterprise environments.

The most important areas for SOC analysts are:

- Authentication monitoring
- Privileged access monitoring
- Account changes
- Group membership changes
- Kerberos activity
- Directory modifications

Attackers who compromise Active Directory often leave traces through these events, making AD logging and monitoring essential for detection and incident response.
