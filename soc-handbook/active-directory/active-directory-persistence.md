# Active Directory Persistence

## Overview

Persistence is the ability of an attacker to maintain access to an environment after the initial compromise.

In Active Directory environments, attackers often create multiple persistence mechanisms to survive:

- Password changes
- Endpoint cleanup
- Security investigations
- Reboots
- Account removal

After gaining access, attackers usually attempt to establish long-term access by modifying:

- Users
- Groups
- Kerberos authentication
- Group Policy
- Services
- Scheduled Tasks
- Delegation settings

SOC analysts must understand persistence techniques because many advanced attacks are not focused only on gaining access, but maintaining access for extended periods.

---

# Active Directory Persistence Lifecycle

Typical attacker behavior:

```
Initial Access

       |

Credential Theft

       |

Privilege Escalation

       |

  Persistence

       |

Lateral Movement

       |

   Data Access
```

---

# Common Active Directory Persistence Techniques

Main techniques:

1. Creating Backdoor Accounts
2. Adding Users To Privileged Groups
3. Golden Ticket Persistence
4. Silver Ticket Persistence
5. AdminSDHolder Abuse
6. Group Policy Persistence
7. Scheduled Task Persistence
8. Service Persistence
9. Account Manipulation
10. Delegation Abuse

---

# 1. Creating Backdoor Accounts

## Overview

Attackers create new accounts to maintain access.

Example:

```
Legitimate Users:

john.smith

malicious_user
```

The attacker may:

- Create new users
- Hide accounts
- Assign privileges

---

# Attack Scenario

```
Compromise Administrator Account

        |

 Create New User

        |

  Add Privileges

        |

 Maintain Access
```

---

# Detection

Important Events:

## Event ID 4720

User Account Created

Monitor:

```
New Username

Creator Account

Creation Time

Source Computer
```

---

## Event ID 4732

Member Added To Local Security Group

---

## Event ID 4728

Member Added To Domain Security Group

---

# Investigation

Questions:

```
Who created the account?

Was it approved?

What permissions were assigned?

When was it created?

Where was it created?
```

---

# Splunk Example

```spl
index=wineventlog EventCode=4720
| table _time Subject_User_Name Target_User_Name Computer
```

---

# MITRE ATT&CK

```
T1136

Create Account
```

---

# 2. Privileged Group Persistence

## Overview

Attackers maintain access by adding accounts into privileged groups.

Common targets:

```
Domain Admins

Enterprise Admins

Administrators

Backup Operators
```

---

# Attack Example

Before:

```
attacker_user

Member:

Domain Users
```

After:

```
attacker_user

Member:

Domain Admins
```

---

# Detection

Monitor:

```
4728

4732

4756
```

---

# Investigation

Review:

```
Modified Group

Added Member

Actor

Source Computer
```

---

# 3. Golden Ticket Persistence

## Overview

Golden Ticket attacks provide extremely powerful persistence.

Attackers create forged Kerberos tickets using the KRBTGT hash.

---

# Why It Provides Persistence

A forged ticket can:

- Impersonate users
- Access resources
- Survive password changes

---

# Attack Flow

```
Compromise Domain Controller

          |

 Extract KRBTGT Hash

          |

  Create Fake TGT

          |

 Authenticate As Any User
```

---

# Detection Challenges

Golden Tickets are difficult because:

- Authentication appears legitimate
- No password theft occurs during usage

---

# Detection Indicators

Look for:

- Unusual ticket lifetime
- Domain Admin activity from unusual systems
- Abnormal authentication patterns

---

# Important Events

```
4624

4768

4769
```

---

# MITRE ATT&CK

```
T1558.001

Golden Ticket
```

---

# 4. Silver Ticket Persistence

## Overview

Silver Tickets create forged service tickets.

Unlike Golden Tickets:

```
Golden Ticket:

Domain-wide access


Silver Ticket:

Specific service access
```

---

# Attack Flow

```
Obtain Service Account Hash

        |

Create Fake Service Ticket

        |

  Access Service
```

---

# Common Targets

Examples:

```
SQL Server

File Server

Web Services
```

---

# Detection

Monitor:

- Service authentication
- Abnormal service access
- Missing authentication patterns

---

# MITRE ATT&CK

```
T1558.002

Silver Ticket
```

---

# 5. AdminSDHolder Abuse

## Overview

AdminSDHolder is an Active Directory security mechanism that protects privileged accounts.

It automatically applies permissions to protected objects.

Examples:

```
Domain Admins

Enterprise Admins

Administrators
```

---

# Attack Scenario

Attackers modify AdminSDHolder permissions:

```
  AdminSDHolder

        |

Malicious Permission

        |

Privileged Accounts Receive Access
```

---

# Why It Is Dangerous

Even if administrators remove attacker permissions:

```
AdminSDHolder Process

        |

Restores Permissions Automatically
```

---

# Detection

Monitor:

## Event ID 5136

Directory Object Modified

Look for:

```
CN=AdminSDHolder
```

---

# Investigation

Check:

```
Who modified AdminSDHolder?

What permissions changed?

Which accounts affected?
```

---

# MITRE ATT&CK

```
T1222

File and Directory Permissions Modification
```

---

# 6. Group Policy Persistence

## Overview

Group Policy Objects (GPOs) control thousands of systems.

Attackers abuse GPOs for enterprise-wide persistence.

---

# Common Abuse Methods

Attackers modify:

- Startup scripts
- Logon scripts
- Scheduled tasks
- Software deployment
- Security settings

---

# Attack Flow

```
Compromise Account

        |

   Modify GPO

        |

Domain Computers Receive Policy

        |

Malicious Code Executes
```

---

# Detection

Important Events:

```
5136

4739
```

---

# Investigation

Review:

```
Modified Policy

Changed Setting

Affected Computers

Account Making Change
```

---

# MITRE ATT&CK

```
T1484.001

Domain Policy Modification
```

---

# 7. Scheduled Task Persistence

## Overview

Scheduled tasks allow automatic execution at specific times.

Attackers abuse them for persistence.

---

# Attack Example

Create:

```
Daily Task

Runs Malware

As SYSTEM
```

---

# Detection

Monitor:

## Event ID 4698

Scheduled Task Created

---

## Event ID 4702

Scheduled Task Updated

---

# Investigation

Review:

```
Task Name

Command

User

Execution Time
```

---

# MITRE ATT&CK

```
T1053.005

Scheduled Task/Job
```

---

# 8. Service Persistence

## Overview

Windows services automatically start applications.

Attackers create malicious services.

---

# Attack Flow

```
 Create Service

       |

Configure Malware Path

       |

 Restart System

       |

 Malware Executes
```

---

# Detection

Important Events:

## Event ID 7045

New Service Created

---

## Event ID 4697

Service Installed

---

# Suspicious Example

```
Service Name:

Windows Update Helper


Binary:

C:\Temp\malware.exe
```

---

# Investigation

Review:

```
Service Name

Binary Path

Account Used

Creation Time
```

---

# 9. Account Manipulation

## Overview

Attackers modify existing accounts instead of creating new ones.

Examples:

- Change permissions
- Enable disabled accounts
- Modify attributes

---

# Detection

Important Events:

```
4722

4725

4738
```

---

# Investigation

Review:

```
Changed Attribute

Previous Value

New Value

Actor
```

---

# 10. Delegation Abuse

## Overview

Delegation allows services to authenticate on behalf of users.

Attackers abuse delegation configurations.

---

# Common Abuse

Examples:

- Unconstrained delegation
- Resource-based constrained delegation

---

# Detection

Monitor:

```
5136

4742
```

Look for:

- Delegation changes
- Suspicious computer accounts

---

# Persistence Hunting Strategy

SOC analysts should hunt for:

---

## New Accounts

Search:

```
4720
```

---

## Privileged Group Changes

Search:

```
4728

4732
```

---

## Kerberos Abuse

Search:

```
4768

4769
```

---

## Directory Changes

Search:

```
5136
```

---

## Scheduled Tasks

Search:

```
4698

4702
```

---

## Services

Search:

```
7045

4697
```

---

# Splunk Hunting Examples

## Detect New Administrator Accounts

```spl
index=wineventlog EventCode=4720
| stats values(Target_User_Name) by Subject_User_Name
```

---

## Detect Domain Admin Membership Changes

```spl
index=wineventlog EventCode=4728
| search Group_Name="Domain Admins"
| table _time Member_Name Subject_User_Name
```

---

## Detect Suspicious Services

```spl
index=wineventlog EventCode=7045
| table _time Service_Name Image_Path Account_Name
```

---

# Persistence Investigation Workflow

```
Alert Generated

        |

Identify Persistence Method

        |

Identify Attacker Account

        |

Review Timeline

        |

Check Related Authentication

        |

Remove Persistence

        |

Reset Credentials

        |

Monitor For Re-entry
```

---

# SOC Persistence Checklist

```
[ ] New accounts reviewed

[ ] Privileged groups checked

[ ] Kerberos activity reviewed

[ ] GPO changes reviewed

[ ] Scheduled tasks reviewed

[ ] Services reviewed

[ ] Delegation settings reviewed

[ ] Suspicious permissions checked

[ ] Compromised accounts contained
```

---

# Prevention Recommendations

## Strong Identity Controls

Implement:

- MFA
- Privileged Access Management
- Least privilege

---

## Monitor High Risk Changes

Alert on:

- Domain Admin changes
- New administrators
- GPO modifications
- Service creation

---

## Protect Domain Controllers

Use:

- Tiered administration model
- Restricted access
- Advanced monitoring

---

# Key Takeaways

Persistence allows attackers to maintain access after compromise.

Active Directory persistence commonly uses:

- Backdoor accounts
- Privileged groups
- Kerberos abuse
- Group Policy
- Scheduled Tasks
- Services
- Delegation abuse

SOC teams must continuously monitor identity changes and privileged activity because persistence mechanisms often indicate deeper compromise.
