# Active Directory Overview

## Introduction

Active Directory (AD) is a Microsoft directory service used by organizations to manage:

- Users
- Computers
- Servers
- Applications
- Security policies
- Authentication
- Authorization

In enterprise environments, Active Directory is usually the central identity management system.

From a SOC perspective, Active Directory is one of the most important areas to monitor because attackers frequently target AD to:

- Obtain user credentials
- Escalate privileges
- Move laterally
- Maintain persistence
- Gain Domain Administrator access

A compromised Active Directory environment can result in complete organizational compromise.

---

# Active Directory Architecture

A typical enterprise Active Directory environment contains:

```
                    Enterprise Network

                           |
                           |

                    Domain Controller

                           |
        --------------------------------------

        |                  |                 |

      Users             Computers          Groups

        |                  |                 |

 Employees             Servers          Permissions
```

The Domain Controller acts as the central authority for authentication and authorization.

---

# Main Active Directory Components

## Domain Controller (DC)

A Domain Controller is a Windows Server responsible for managing Active Directory.

Main responsibilities:

- User authentication
- Password validation
- Kerberos ticket issuance
- NTLM authentication
- Group Policy processing
- Directory database management
- Replication between controllers


Example:

```
Domain Controller

hostname:

DC01.company.local

IP:

192.168.50.10
```

---

# Active Directory Domain

A domain is a logical security boundary containing:

- Users
- Computers
- Groups
- Policies

Example:

```
company.local
```

Objects inside the domain:

```
company.local

 |
 |
 +-- Users

 |
 +-- Computers

 |
 +-- Servers

 |
 +-- Groups
```

---

# Organizational Units (OU)

Organizational Units are containers used to organize AD objects.

Example:

```
company.local

 |
 +-- IT

 |
 +-- Finance

 |
 +-- HR

 |
 +-- Servers
```

OUs allow administrators to apply:

- Group Policies
- Permissions
- Administrative controls

---

# Active Directory Objects

Active Directory stores different object types.

Common objects:

---

## User Accounts

Examples:

```
john.smith

administrator

svc_backup
```

Used for:

- Authentication
- Access control
- Resource access

SOC analysts monitor user accounts because attackers often compromise them.

---

## Computer Accounts

Represent machines joined to the domain.

Examples:

```
WIN10-USER01

FILESERVER01

DC01
```

Attackers often use compromised computers for:

- Lateral movement
- Credential theft
- Malware execution

---

## Groups

Groups simplify permission management.

Examples:

```
Domain Admins

Enterprise Admins

Administrators

Backup Operators

Users
```

High privilege groups are critical monitoring targets.

---

# Important Security Groups

## Domain Admins

Members have administrative control over the entire domain.

Privileges include:

- Managing domain computers
- Creating users
- Changing policies
- Accessing domain resources

Compromise of this group can lead to full domain compromise.

---

## Enterprise Admins

One of the highest privilege groups in Active Directory.

Used for:

- Forest-wide administration
- Cross-domain management

---

## Administrators

Local administrative privileges on systems.

Attackers target administrator accounts because they allow:

- Software installation
- Configuration changes
- Security control modification

---

## Backup Operators

A commonly abused group.

Members may have permissions that allow access to sensitive files.

Attackers may abuse this group for:

- Credential theft
- Data access

---

# Authentication in Active Directory

Active Directory mainly uses:

- Kerberos
- NTLM

These protocols handle user authentication.

---

# Kerberos Authentication

Kerberos is the default authentication protocol in modern Active Directory environments.

It uses tickets instead of sending passwords over the network.

Authentication flow:

```
User Login

      |

Authentication Request

      |

Domain Controller

      |

Ticket Granting Ticket (TGT)

      |

Service Ticket

      |

Access Resource
```

---

# Kerberos Components

## KDC (Key Distribution Center)

Runs on Domain Controllers.

Responsible for:

- Authentication Service
- Ticket Granting Service


---

## Ticket Granting Ticket (TGT)

A ticket proving the user's identity.

Used to request service tickets.

---

## Service Ticket

Allows access to specific services.

Example:

```
User

 |

Requests File Server Access

 |

Receives Service Ticket

 |

Accesses File Share
```

---

# NTLM Authentication

NTLM is an older authentication protocol.

It uses:

- Challenge-response authentication
- Password hashes

Attackers commonly abuse NTLM for:

- Pass-the-Hash attacks
- Credential relay attacks

---

# Active Directory Security Risks

Because AD controls identity, attackers commonly target it.

Common attacks:

---

# Credential Theft

Attackers attempt to obtain:

- Passwords
- NTLM hashes
- Kerberos tickets

Examples:

- LSASS dumping
- Mimikatz
- Credential dumping tools

---

# Privilege Escalation

Attackers attempt to obtain higher privileges.

Examples:

```
User Account

       |

Local Administrator

       |

Domain Admin
```

---

# Lateral Movement

Attackers move between systems after gaining access.

Common methods:

- Remote Desktop Protocol (RDP)
- SMB
- WinRM
- PsExec
- WMI

---

# Persistence

Attackers maintain access through:

- New user accounts
- Group membership changes
- Scheduled tasks
- Services
- Group Policy abuse

---

# Active Directory Attack Lifecycle

Typical attacker workflow:

```
Reconnaissance

        |

Initial Access

        |

Credential Theft

        |

Privilege Escalation

        |

Lateral Movement

        |

Domain Compromise

        |

Persistence
```

---

# SOC Monitoring Priorities

SOC analysts should monitor:

## Authentication Activity

Look for:

- Failed logins
- Successful unusual logins
- Login from new locations
- Privileged account usage


---

## Privileged Account Changes

Monitor:

- Domain Admin membership
- New administrator accounts
- Permission changes


---

## Suspicious Authentication Patterns

Examples:

```
One user

+

Many failed logins

+

Successful login

=

Possible compromise
```

---

## Unusual Computer Activity

Examples:

```
Normal:

User workstation → File Server


Suspicious:

User workstation → Domain Controller
```

---

# Active Directory Logging

Important telemetry sources:

## Windows Security Logs

Examples:

```
4624
Successful Logon


4625
Failed Logon


4672
Special Privileges Assigned


4720
User Created


4728
Member Added To Security Group


4768
Kerberos Authentication


4769
Kerberos Service Ticket Request
```

---

## Sysmon

Useful for:

- Process execution
- Network connections
- Credential theft detection
- Malware activity

Examples:

```
Event ID 1

Process Creation


Event ID 3

Network Connection
```

---

# Active Directory Investigation Example

Scenario:

```
Alert:

New Domain Admin Added
```

Investigation:

```
1. Identify account added

2. Identify who performed the change

3. Review authentication activity

4. Check source computer

5. Investigate related processes

6. Determine if account is legitimate
```

---

# Active Directory Hardening Recommendations

Organizations should implement:

## Least Privilege

Users should only have required permissions.

---

## Multi-Factor Authentication

Protects against:

- Password theft
- Credential phishing

---

## Separate Administrator Accounts

Avoid:

```
Daily User Account

+

Domain Admin Privileges
```

Use:

```
john.smith

john.admin
```

---

## Monitor Privileged Groups

Regularly review:

- Domain Admins
- Enterprise Admins
- Administrators

---

## Enable Auditing

Enable logging for:

- Account changes
- Group changes
- Authentication events
- Directory modifications

---

# Key Takeaways

Active Directory is the foundation of enterprise identity management.

For SOC analysts, understanding AD is essential because many attacks involve:

- Credential compromise
- Authentication abuse
- Privilege escalation
- Lateral movement
- Domain takeover

Effective Active Directory monitoring allows security teams to detect attacks earlier and prevent full environment compromise.
