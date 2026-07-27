# Active Directory Components

## Overview

Active Directory is built from several core components that work together to provide:

- Identity management
- Authentication
- Authorization
- Resource management
- Security policy enforcement

Understanding Active Directory components helps SOC analysts investigate:

- Account compromise
- Privilege escalation
- Lateral movement
- Unauthorized changes
- Domain compromise

---

# Active Directory Logical Structure

The logical structure defines how objects are organized inside Active Directory.

Main components:

```
Forest

 |

Tree

 |

Domain

 |

Organizational Units

 |

Objects
```

---

# 1. Forest

## Definition

A forest is the highest-level container in Active Directory.

It contains:

- One or more domains
- Shared schema
- Global catalog
- Trust relationships


Example:

```
Forest:

company.com
```

Inside the forest:

```
company.com

 |
 +-- usa.company.com

 |
 +-- europe.company.com
```

---

## Security Importance

The forest represents the largest security boundary.

If an attacker compromises:

```
Enterprise Admin
```

they can potentially control the entire forest.

SOC teams should monitor:

- Enterprise Admin activity
- Forest trust changes
- Domain additions

---

# 2. Tree

## Definition

A tree is a collection of domains that share a common namespace.

Example:

```
company.com

 |
 |
 +-- it.company.com

 |
 +-- finance.company.com
```

Domains inside a tree automatically have trust relationships.

---

# 3. Domain

## Definition

A domain is the main security boundary where users, computers, and resources are managed.

Example:

```
corp.company.com
```

A domain contains:

- User accounts
- Computer accounts
- Groups
- Policies
- Applications

---

## Domain Controller

Each domain requires at least one Domain Controller.

The Domain Controller stores:

```
Active Directory Database

NTDS.dit
```

It manages:

- Authentication
- Authorization
- Directory changes

---

# 4. Organizational Units (OU)

## Definition

Organizational Units are containers used to organize AD objects.

Example:

```
company.local

 |

 +-- Users

 |

 +-- Workstations

 |

 +-- Servers

 |

 +-- Security Team
```

---

## Why OUs Matter

OUs allow administrators to apply:

- Group Policies
- Permissions
- Administrative delegation

---

## SOC Investigation Example

Alert:

```
Unauthorized Group Policy Change
```

Analyst investigates:

```
Which OU changed?

Who modified it?

What machines are affected?
```

---

# 5. Active Directory Objects

Objects are the resources stored inside AD.

Common objects:

- Users
- Computers
- Groups
- Printers
- Shared folders
- Services

---

# User Objects

Represent human or service identities.

Examples:

```
john.smith

administrator

svc_backup
```

---

## User Security Attributes

Important attributes:

```
Username

Password Hash

Group Membership

Account Status

Last Login

Permissions
```

---

## SOC Monitoring

Monitor:

- New accounts
- Disabled accounts
- Privilege changes
- Suspicious logins

---

# Computer Objects

Represent domain-joined systems.

Examples:

```
DESKTOP001

SERVER01

DC01
```

---

## Security Importance

Attackers target computer accounts for:

- Lateral movement
- Remote execution
- Credential theft

---

# Group Objects

Groups simplify permission management.

Two major types:

---

# Security Groups

Used to assign permissions.

Examples:

```
Domain Admins

IT Administrators

Finance Users
```

---

# Distribution Groups

Used mainly for email distribution.

Example:

```
All Employees
```

---

# Important Privileged Groups

## Domain Admins

Highest common administrative group.

Members can:

- Manage domain computers
- Create users
- Modify policies
- Access sensitive resources

---

## Enterprise Admins

Forest-level administrative privileges.

Can:

- Manage multiple domains
- Modify forest configuration

---

## Schema Admins

Can modify the Active Directory schema.

Rarely used.

Compromise is highly dangerous.

---

## Administrators

Local/domain administrative permissions.

---

## Backup Operators

Can access backup data.

Attackers may abuse this for credential theft.

---

# 6. Global Catalog

## Definition

The Global Catalog is a distributed database containing information about objects across the forest.

It stores:

- Full information for objects in its domain
- Partial information for objects in other domains

---

## Security Importance

Attackers may target Global Catalog servers because they provide information about:

- Users
- Groups
- Domains

SOC monitoring should include:

- Unusual LDAP queries
- Directory enumeration activity

---

# 7. Active Directory Schema

## Definition

The schema defines what objects and attributes can exist in Active Directory.

Example:

User object attributes:

```
Username

Email

Password Information

Group Membership
```

---

## Security Risk

Unauthorized schema modifications can indicate:

- Persistence attempts
- Domain compromise

Monitor:

```
Event ID 5136

Directory Object Modified
```

---

# 8. Trust Relationships

## Definition

Trusts allow authentication between domains.

Example:

```
Domain A

     Trust

Domain B
```

---

# Types of Trusts

## Parent-Child Trust

Created automatically between domains in the same tree.

---

## Tree-Root Trust

Connects domain trees in a forest.

---

## External Trust

Connects separate forests or domains.

---

# Security Risks

Attackers may abuse trusts for:

- Lateral movement
- Privilege escalation
- Cross-domain compromise

SOC monitoring:

- New trust creation
- Trust modifications
- Cross-domain authentication

---

# 9. Active Directory Replication

## Definition

Replication synchronizes changes between Domain Controllers.

Example:

```
          DC01

           |

      Replication

           |

          DC02
```

---

## Replicated Information

Includes:

- Users
- Password changes
- Group memberships
- Policies

---

# Security Importance

Attackers may abuse replication permissions.

Example:

```
DCSync Attack
```

Allows attackers to request password hashes from Domain Controllers.

---

# 10. LDAP

## Definition

LDAP (Lightweight Directory Access Protocol) is used to query and modify Active Directory information.

Used for:

- Searching users
- Retrieving groups
- Directory management

---

## Security Monitoring

Suspicious LDAP activity:

- Large directory searches
- Enumeration attempts
- Unknown applications querying AD

---

# 11. Service Accounts

## Definition

Accounts used by applications and services.

Examples:

```
svc_sql

svc_backup

svc_web
```

---

## Security Risks

Service accounts often have:

- Long passwords
- Excessive privileges
- Non-expiring passwords

Attackers target them for:

- Kerberoasting
- Privilege escalation

---

# 12. Group Managed Service Accounts (gMSA)

## Definition

gMSA accounts automatically manage passwords for services.

Advantages:

- Automatic password rotation
- Reduced credential exposure
- Better security

---

# Traditional Service Account vs gMSA

| Feature | Service Account | gMSA |
|-|-|-|
| Password Management | Manual | Automatic |
| Rotation | Rare | Automatic |
| Security | Lower | Higher |
| Recommended | No | Yes |

---

# 13. FSMO Roles

Active Directory has five Flexible Single Master Operations (FSMO) roles.

---

# Schema Master

Controls:

- Schema changes

---

# Domain Naming Master

Controls:

- Domain creation
- Domain removal

---

# RID Master

Provides:

- Security identifier allocation

---

# PDC Emulator

Responsible for:

- Password changes
- Account lockouts
- Time synchronization

---

# Infrastructure Master

Maintains:

- Cross-domain references

---

# SOC Importance of FSMO Roles

Monitor:

- FSMO role transfers
- Unexpected role changes
- Unauthorized administrative actions

---

# Active Directory Attack Surface Summary

| Component | Common Attacks |
|-|-|
| Users | Credential theft, phishing |
| Groups | Privilege escalation |
| Domain Controllers | Full domain compromise |
| Trusts | Lateral movement |
| LDAP | Enumeration |
| Service Accounts | Kerberoasting |
| Replication | DCSync |
| Group Policy | Persistence |

---

# SOC Investigation Checklist

When investigating AD activity:

```
[ ] Identify affected user

[ ] Identify affected computer

[ ] Review authentication logs

[ ] Check group membership

[ ] Check privilege level

[ ] Review AD changes

[ ] Review source IP

[ ] Check lateral movement indicators

[ ] Check persistence mechanisms
```

---

# Key Takeaways

Active Directory is composed of multiple interconnected components that create the identity foundation of an organization.

For SOC analysts, understanding:

- Forests
- Domains
- Users
- Groups
- Trusts
- Replication
- Service Accounts

is essential for detecting and investigating enterprise attacks.

Attackers rarely compromise a single machine. They often attempt to move from an endpoint compromise toward Active Directory control.
