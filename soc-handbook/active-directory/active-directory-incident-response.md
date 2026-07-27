# Active Directory Incident Response

## Overview

Active Directory (AD) incidents require a structured response process because AD controls authentication, authorization, and access across an organization.

A compromised Active Directory environment can allow attackers to:

- Create unauthorized accounts
- Escalate privileges
- Move laterally
- Access sensitive systems
- Maintain persistence
- Control the entire enterprise

SOC analysts must respond quickly to prevent attackers from reaching Domain Controllers and achieving full domain compromise.

---

# Active Directory Incident Response Lifecycle

The response process follows the standard incident response lifecycle:

```
Preparation

     |

Identification

     |

Containment

     |

Eradication

     |

 Recovery

     |

Lessons Learned
```

---

# Phase 1: Preparation

## Objective

Prepare the environment before an AD incident occurs.

---

# Required Visibility

A SOC should collect:

## Windows Security Logs

Important Events:

```
4624  Successful Logon

4625  Failed Logon

4672  Privileged Logon

4688  Process Creation

4720  User Created

4728  Group Membership Change

4732  Local Group Change

4740  Account Locked

4768  Kerberos Authentication

4769  Kerberos Service Ticket

4771  Kerberos Failure

5136  Directory Object Modified

7045  Service Created
```

---

## Sysmon Logs

Recommended:

```
Event ID 1

Process Creation


Event ID 3

Network Connection


Event ID 7

Image Loaded


Event ID 10

Process Access


Event ID 11

File Creation
```

---

## Required Assets Documentation

Maintain:

- Domain Controllers list
- Critical servers
- Administrator accounts
- Service accounts
- Privileged groups
- Security tools
- Network segmentation

---

# Phase 2: Identification

## Objective

Determine whether suspicious activity represents a real compromise.

---

# Common AD Incident Alerts

Examples:

```
Suspicious Domain Admin Login

DCSync Detection

Golden Ticket Activity

Password Spray

Kerberoasting

New Administrator Account

GPO Modification

Suspicious LDAP Enumeration

Privilege Group Changes
```

---

# Initial Investigation Questions

Analysts should determine:

```
Who performed the action?

What account was involved?

Where did it happen?

When did it happen?

Was the activity authorized?

What happened before and after?
```

---

# Alert Triage Example

Alert:

```
User added to Domain Admins
```

Investigation:

```
Account:

john.smith


Action:

Added to Domain Admins


Performed By:

administrator01


Source:

WORKSTATION01
```

Questions:

```
Was this approved?

Is administrator01 compromised?

Was the workstation suspicious?
```

---

# Phase 3: Containment

## Objective

Stop attacker activity and prevent further compromise.

Containment actions depend on the attack stage.

---

# Account Containment

Actions:

- Disable compromised account
- Reset password
- Remove unauthorized privileges
- Revoke sessions

Example:

```
Compromised User

        |

Disable Account

        |

Reset Password

        |

Monitor Activity
```

---

# Privileged Account Containment

For:

- Domain Admins
- Enterprise Admins
- Service accounts

Actions:

- Disable account temporarily
- Reset credentials
- Review all activity
- Remove suspicious permissions

---

# Endpoint Containment

If a workstation is compromised:

Actions:

- Isolate endpoint
- Block network communication
- Collect forensic evidence

---

# Domain Controller Protection

If Domain Controller compromise is suspected:

Immediately:

- Restrict access
- Monitor administrator activity
- Review replication activity
- Check privileged accounts

---

# Phase 4: Eradication

## Objective

Remove attacker persistence and eliminate the root cause.

---

# Remove Unauthorized Accounts

Check:

```
Created Users

Modified Users

Disabled Accounts

Unknown Administrators
```

Events:

```
4720

4722

4725

4738
```

---

# Remove Malicious Group Membership

Review:

```
Domain Admins

Enterprise Admins

Administrators

Backup Operators
```

Events:

```
4728

4732

4756
```

---

# Remove Persistence Mechanisms

Investigate:

## Scheduled Tasks

Events:

```
4698

4702
```

---

## Services

Events:

```
7045

4697
```

---

## Group Policy

Events:

```
5136

4739
```

---

## Registry Persistence

Check:

```
HKLM\Software\Microsoft\Windows\CurrentVersion\Run

HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

---

# Credential Reset Strategy

Credential reset priority:

```
1. Compromised User

2. Local Administrators

3. Service Accounts

4. Privileged Accounts

5. Domain Admin Accounts

6. KRBTGT Account
```

---

# KRBTGT Reset

## When Required

Reset KRBTGT when:

- Golden Ticket attack suspected
- Domain Controller compromise confirmed

---

# KRBTGT Reset Process

The account should be reset twice because Kerberos maintains previous password history.

Process:

```
Reset KRBTGT

        |

Wait For Replication

        |

Reset KRBTGT Again

        |

Monitor Authentication
```

---

# Phase 5: Recovery

## Objective

Return systems to normal operation securely.

---

# Recovery Steps

## Restore Access

Actions:

- Re-enable approved accounts
- Restore required permissions
- Validate authentication

---

## Monitor Environment

Increase monitoring for:

- Privileged logins
- Authentication failures
- New accounts
- Group changes

---

# Validate Security Controls

Check:

```
MFA

Password Policies

Privileged Access

Logging

Endpoint Protection

Network Segmentation
```

---

# Phase 6: Lessons Learned

## Objective

Improve security after the incident.

---

# Post Incident Review

Document:

```
What happened?

How did attacker enter?

Which systems affected?

Which accounts compromised?

How detected?

How contained?

What improvements required?
```

---

# Active Directory Investigation Playbooks

---

# Scenario 1: Suspected Domain Admin Compromise

## Indicators

```
Unknown Domain Admin login

Unusual location

Unexpected time

```

---

## Investigation

Check:

```
4624

4672

4688

4769
```

Review:

- Source machine
- Commands executed
- Network activity

---

## Response

Actions:

```
Disable account

Reset password

Review privileges

Investigate endpoint
```

---

# Scenario 2: DCSync Attack

## Indicators

```
4662

Replication permissions

Unknown user
```

---

## Investigation

Check:

```
Source computer

Account performing replication

Domain Controller activity
```

---

## Response

Actions:

```
Disable account

Remove replication rights

Reset privileged credentials

Consider KRBTGT reset
```

---

# Scenario 3: Golden Ticket Attack

## Indicators

```
Abnormal Kerberos tickets

Impossible authentication patterns

Privileged access anomalies
```

---

## Investigation

Review:

```
4768

4769

4624
```

---

## Response

Actions:

```
Reset KRBTGT twice

Reset privileged accounts

Investigate Domain Controllers
```

---

# Scenario 4: Password Spray Attack

## Indicators

```
Many failed logins

Many users

Same source
```

---

## Investigation

Check:

```
Source IP

Target accounts

Successful authentication
```

---

## Response

Actions:

```
Block source

Reset compromised accounts

Enable MFA
```

---

# Active Directory Incident Severity Classification

## Critical

Examples:

- Domain Controller compromise
- DCSync
- Golden Ticket
- Domain Admin compromise

---

## High

Examples:

- Unauthorized admin account
- Privilege escalation
- Kerberoasting success
- Lateral movement

---

## Medium

Examples:

- Password spray
- Suspicious authentication
- LDAP reconnaissance

---

## Low

Examples:

- Single failed login
- Normal administrative activity

---

# SOC AD Incident Checklist

```
[ ] Alert validated

[ ] User identified

[ ] Source host identified

[ ] Timeline created

[ ] Authentication reviewed

[ ] Privilege changes checked

[ ] Persistence checked

[ ] Endpoint investigated

[ ] Network activity reviewed

[ ] Threat intelligence checked

[ ] Account contained

[ ] Credentials reset

[ ] Recovery completed

[ ] Lessons documented
```

---

# Recommended AD Security Improvements

## Identity Protection

Implement:

- MFA
- Conditional Access
- Privileged Access Management

---

## Privilege Management

Use:

- Least privilege
- Separate admin accounts
- Just-in-Time access

---

## Monitoring

Enable:

- Advanced auditing
- Sysmon
- SIEM correlation
- Threat hunting

---

## Domain Controller Security

Protect:

- Administrator accounts
- KRBTGT account
- Replication permissions
- Domain Controller access

---

# Key Takeaways

Active Directory compromise is one of the highest-impact security incidents.

A SOC must quickly identify:

- Compromised accounts
- Privilege escalation
- Persistence methods
- Lateral movement
- Domain compromise

Effective response requires combining:

- Windows Event Logs
- Sysmon telemetry
- Identity monitoring
- Network visibility
- Threat intelligence

Fast detection and containment can prevent complete enterprise compromise.
