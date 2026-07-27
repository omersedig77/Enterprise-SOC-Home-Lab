# Active Directory Privilege Escalation

## Overview

Privilege escalation is the process where an attacker gains higher permissions than they originally have.

In Active Directory environments, attackers commonly attempt to move from:

```
  Standard User

        |

Local Administrator

        |

Domain Administrator

        |

Enterprise Administrator
```

Privilege escalation is one of the most dangerous stages of an Active Directory attack because gaining administrative access can allow attackers to:

- Disable security controls
- Access sensitive systems
- Modify Active Directory
- Create persistence
- Control the entire domain

SOC analysts must monitor privilege changes because many AD compromises involve unauthorized privilege elevation.

---

# Active Directory Privilege Levels

## Standard User

Normal employee account.

Example:

```
john.smith@company.local
```

Permissions:

- Access approved resources
- Run normal applications
- No administrative control

---

## Local Administrator

Administrator privileges on a single machine.

Example:

```
WIN10-PC01\Administrator
```

Can:

- Install software
- Modify local settings
- Access protected files

---

## Domain Administrator

Administrative access across the domain.

Can:

- Manage domain computers
- Create accounts
- Modify Group Policy
- Access Domain Controllers

---

## Enterprise Administrator

Highest privilege level in a forest.

Can:

- Control multiple domains
- Modify forest-wide settings

---

# Common Active Directory Privilege Escalation Techniques

Main techniques:

1. Excessive Group Membership
2. ACL Abuse
3. DCSync Attack
4. Group Policy Abuse
5. Service Account Abuse
6. Kerberos Delegation Abuse
7. Local Administrator Password Reuse
8. Unquoted Service Paths
9. Weak Permissions

---

# 1. Privileged Group Abuse

## Overview

Attackers attempt to add compromised accounts into privileged groups.

Common targets:

```
Domain Admins

Enterprise Admins

Administrators

Backup Operators
```

---

# Attack Example

Normal:

```
User Account

Member Of:

Domain Users
```

After compromise:

```
User Account

Added To:

Domain Admins
```

The attacker now controls the domain.

---

# Detection

Important Events:

## Event ID 4728

Member Added To Security Enabled Global Group

Example:

```
User added to Domain Admins
```

---

## Event ID 4732

Member Added To Local Security Group

Example:

```
User added to Local Administrators
```

---

# Splunk Detection

```spl
index=wineventlog (EventCode=4728 OR EventCode=4732)
| table _time Subject_User_Name Member_Name Group_Name Computer
```

---

# Investigation

Check:

```
Who added the account?

Which account was added?

Which group?

Source computer?

Was the change authorized?
```

---

# MITRE ATT&CK

```
T1098

Account Manipulation
```

---

# 2. ACL Abuse

## Overview

Access Control Lists (ACLs) define who can perform actions on Active Directory objects.

Attackers abuse excessive permissions to escalate privileges.

---

# Common Dangerous Permissions

Examples:

```
GenericAll

GenericWrite

WriteDACL

WriteOwner

ResetPassword
```

---

# Attack Example

A normal user has:

```
WriteDACL permission

on

Domain Admin Group
```

The attacker can modify permissions and add themselves.

---

# Detection

Monitor:

- Permission changes
- Directory modifications
- Privilege changes

Important Event:

```
5136

Directory Object Modified
```

---

# Investigation

Review:

```
Object Modified

Old Permissions

New Permissions

User Performing Change
```

---

# MITRE ATT&CK

```
T1222

File and Directory Permissions Modification
```

---

# 3. DCSync Attack

## Overview

DCSync is an Active Directory replication abuse technique.

Instead of attacking the Domain Controller directly, attackers request password hashes through replication permissions.

---

# Normal Replication

```
Domain Controller A

        |

Replication

        |

Domain Controller B
```

---

# Attack Scenario

Attacker obtains:

```
Replicating Directory Changes

permission
```

Then requests:

```
Password Hashes
```

---

# Impact

Attackers can obtain:

- NTLM hashes
- KRBTGT hash
- Administrator credentials

---

# Attack Flow

```
Compromised Account

        |

Replication Permissions

        |

Request Hashes

        |

Domain Compromise
```

---

# Detection

Monitor:

## Event ID 4662

Directory Service Object Access

Look for:

```
Replication permissions

DS-Replication-Get-Changes
```

---

# Splunk Example

```spl
index=wineventlog EventCode=4662
| search Properties="*Replication*"
| table _time SubjectUserName ObjectName Properties
```

---

# MITRE ATT&CK

```
T1003.006

OS Credential Dumping: DCSync
```

---

# 4. Group Policy Abuse

## Overview

Group Policy controls configurations across domain computers.

Attackers abuse Group Policy for:

- Persistence
- Privilege escalation
- Malware deployment

---

# Examples

Attackers modify:

```
Startup Scripts

Scheduled Tasks

Security Settings

Software Deployment
```

---

# Attack Flow

```
Compromise Account

        |

Modify Group Policy

        |

Apply Policy To Machines

        |

Execute Malicious Activity
```

---

# Detection

Important Events:

```
5136

Directory Object Modified

4739

Domain Policy Changed
```

---

# Investigation

Review:

```
Who modified GPO?

Which policy changed?

Which machines affected?
```

---

# MITRE ATT&CK

```
T1484.001

Domain Policy Modification
```

---

# 5. Service Account Privilege Escalation

## Overview

Service accounts often have excessive privileges.

Examples:

```
svc_sql

svc_backup

svc_application
```

---

# Why Service Accounts Are Dangerous

Common problems:

- Weak passwords
- No rotation
- Excessive permissions
- Domain admin privileges

---

# Attack Methods

Attackers may use:

- Kerberoasting
- Password guessing
- Credential dumping

---

# Detection

Monitor:

```
4769

Kerberos Service Ticket Requests
```

Look for:

- Abnormal service ticket requests
- Privileged service accounts

---

# Prevention

Use:

- gMSA accounts
- Password rotation
- Least privilege

---

# 6. Kerberos Delegation Abuse

## Overview

Delegation allows services to authenticate on behalf of users.

Misconfigured delegation can allow privilege escalation.

---

# Types

## Unconstrained Delegation

Most dangerous.

Allows services to obtain user tickets.

---

## Constrained Delegation

Allows limited delegation.

---

## Resource-Based Constrained Delegation

Configured on target resources.

---

# Detection

Monitor:

- Delegation settings
- Computer account changes

Important:

```
5136

4742
```

---

# MITRE ATT&CK

```
T1134

Access Token Manipulation
```

---

# 7. Local Administrator Password Reuse

## Overview

Many organizations historically used the same local administrator password across machines.

Example:

```
PC01

Administrator Password:

Password123


PC02

Administrator Password:

Password123
```

---

# Attack Scenario

Compromise one machine:

```
PC01

 |

Obtain Local Admin Password

 |

Access PC02

```

---

# Detection

Look for:

- Same account logging into many machines
- Unusual administrator activity

---

# Prevention

Use:

```
Microsoft LAPS
```

---

# 8. Unquoted Service Path Privilege Escalation

## Overview

Windows services with incorrect executable paths can allow privilege escalation.

Example:

Unsafe:

```
C:\Program Files\App Service\service.exe
```

Windows may search:

```
C:\Program.exe
```

---

# Attack Scenario

Attacker places:

```
C:\Program.exe
```

When service starts:

```
SYSTEM executes malicious binary
```

---

# Detection

Monitor:

```
Event ID 7045

Event ID 4697
```

---

# Splunk Example

```spl
index=wineventlog (EventCode=7045 OR EventCode=4697)
| search ImagePath="* *"
```

---

# MITRE ATT&CK

```
T1574.009

Unquoted Path Service Hijacking
```

---

# 9. Weak Object Permissions

## Overview

Poorly configured Active Directory permissions can allow unauthorized modifications.

Examples:

- Users modifying groups
- Users changing passwords
- Excessive delegated rights

---

# Detection

Monitor:

```
5136

Directory Object Modified
```

Review:

```
Actor

Target Object

Permission Change
```

---

# Privilege Escalation Investigation Workflow

When an escalation alert triggers:

```
Alert Generated

        |

Identify Account

        |

Identify Privilege Change

        |

Review Source System

        |

Check Previous Authentication

        |

Review AD Changes

        |

Determine Authorization

        |

Contain Account
```

---

# SOC Investigation Checklist

```
[ ] Account identified

[ ] Previous privileges reviewed

[ ] New privileges identified

[ ] Group membership checked

[ ] AD changes reviewed

[ ] Source computer identified

[ ] Authentication history reviewed

[ ] Related processes checked

[ ] Persistence checked

[ ] Account contained if malicious
```

---

# Prevention Recommendations

## Least Privilege

Users should receive only required permissions.

---

## Privileged Access Management

Use:

- Separate admin accounts
- Approval workflows
- Session monitoring

---

## Monitor High-Privilege Groups

Regularly review:

```
Domain Admins

Enterprise Admins

Administrators

Backup Operators
```

---

## Enable Advanced Auditing

Monitor:

```
Account Changes

Group Changes

Directory Changes

Authentication Events
```

---

# Key Takeaways

Privilege escalation is a critical stage of Active Directory attacks.

Attackers commonly abuse:

- Group memberships
- Permissions
- Kerberos
- Replication
- Group Policy
- Service accounts

SOC analysts must continuously monitor privileged activity because unauthorized privilege escalation can quickly lead to complete domain compromise.
