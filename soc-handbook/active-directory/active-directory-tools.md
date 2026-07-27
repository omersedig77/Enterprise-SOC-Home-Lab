# Active Directory Security Tools

## Overview

Active Directory investigations require specialized tools to understand:

- Identity relationships
- Privilege paths
- Authentication activity
- Misconfigurations
- Attack paths
- Persistence mechanisms

SOC analysts, incident responders, and security engineers use these tools to investigate suspicious Active Directory activity and identify weaknesses before attackers exploit them.

This document covers commonly used Active Directory security tools, their purpose, and when SOC teams use them.

---

# Tool Categories

Active Directory tools can be divided into:

```
Enumeration Tools

        |

Attack Path Analysis Tools

        |

Incident Response Tools

        |

Credential Investigation Tools

        |

Security Assessment Tools
```

---

# 1. BloodHound

## Overview

BloodHound is one of the most popular Active Directory analysis tools.

It maps relationships between:

- Users
- Groups
- Computers
- Permissions
- Sessions
- Trust relationships

It helps identify attack paths inside Active Directory.

---

# Main Purpose

BloodHound answers questions like:

```
Who can become Domain Admin?

Which users have excessive permissions?

Where are privileged accounts logged in?

Which systems are vulnerable?
```

---

# Common SOC Use Cases

## Investigating Privilege Escalation

Example:

```
User Account

        |

GenericAll Permission

        |

Domain Admin Group
```

BloodHound can reveal this attack path.

---

## Finding Excessive Permissions

Identify:

- Dangerous ACLs
- Delegated permissions
- Privileged accounts

---

## Incident Response

After compromise:

Use BloodHound to determine:

- Attacker possible movement paths
- Accounts affected
- Systems exposed

---

# Data Collection

BloodHound collectors gather:

- Users
- Groups
- Computers
- Sessions
- Permissions

Common collector:

```
SharpHound
```

---

# SOC Investigation Example

Alert:

```
Suspicious Domain Admin Login
```

Analyst checks:

```
What privileges does this account have?

What systems can it access?

Which users can control it?
```

---

# 2. Microsoft Active Directory Explorer (AD Explorer)

## Overview

AD Explorer is a Microsoft Sysinternals tool used to view and analyze Active Directory objects.

---

# Main Purpose

Allows analysts to inspect:

- Users
- Groups
- Computers
- Organizational Units
- Attributes

---

# SOC Use Cases

## Investigating User Objects

Review:

```
Account Status

Password Attributes

Group Membership

Last Login
```

---

## Investigating Suspicious Accounts

Example:

```
User:

backup_admin01


Created:

Yesterday


Member Of:

Domain Admins
```

---

# 3. Sysinternals Suite

## Overview

Microsoft Sysinternals provides powerful Windows investigation tools.

Common tools:

```
Process Explorer

Process Monitor

Autoruns

TCPView

PsExec
```

---

# Process Explorer

## Purpose

Advanced process investigation.

Useful for:

- Malware analysis
- Suspicious processes
- Parent-child relationships

---

# SOC Example

Suspicious:

```
powershell.exe
```

Investigate:

```
Parent Process

Command Line

User Context

Network Activity
```

---

# Process Monitor

## Purpose

Real-time monitoring of:

- File activity
- Registry changes
- Process actions

---

# SOC Use Cases

Investigate:

- Malware behavior
- Persistence creation
- Registry modifications

---

# Autoruns

## Purpose

Find persistence mechanisms.

Checks:

- Registry Run Keys
- Services
- Scheduled Tasks
- Drivers

---

# SOC Use Case

After malware detection:

Search:

```
How does malware survive reboot?
```

---

# TCPView

## Purpose

Monitor network connections.

Shows:

- Process
- Destination IP
- Port
- Connection state

---

# SOC Use Case

Investigate:

```
Unknown.exe

connecting to

External IP
```

---

# 4. PowerView

## Overview

PowerView is a PowerShell-based Active Directory enumeration tool.

Security teams use it for AD visibility and assessments.

---

# Main Purpose

Discover:

- Users
- Groups
- Computers
- Permissions
- Domain information

---

# SOC Use Cases

Identify:

- Privileged accounts
- Suspicious permissions
- Attack paths

---

# Example Investigation

Question:

```
Who has administrative rights?
```

PowerView can help identify:

```
Domain Admin Members

Local Admin Access

Delegated Permissions
```

---

# 5. PingCastle

## Overview

PingCastle is an Active Directory security assessment tool.

It evaluates AD security posture.

---

# Main Purpose

Find:

- Weak configurations
- Privilege risks
- Security weaknesses

---

# Common Reports

Examples:

```
Domain Risk Score

Privileged Account Analysis

Trust Relationships

Security Configuration
```

---

# SOC Use Cases

Used during:

- Security reviews
- Hardening projects
- Incident response

---

# Example Finding

```
Risk:

Domain Admin accounts without MFA

Severity:

High
```

---

# 6. Purple Knight

## Overview

Purple Knight is an Active Directory security assessment tool.

It focuses on detecting indicators of compromise and security weaknesses.

---

# Detects

Examples:

- Suspicious accounts
- Kerberos weaknesses
- Privilege problems
- Attack indicators

---

# SOC Use Cases

Useful for:

- Baseline security checks
- Post-incident validation
- Continuous improvement

---

# 7. Mimikatz

## Overview

Mimikatz is a well-known credential extraction tool.

Security teams study it because attackers commonly abuse similar techniques.

---

# Security Use Cases

Used in:

- Detection testing
- Purple team exercises
- Incident validation

---

# Attack Techniques Studied

Examples:

- Credential dumping
- Pass-the-Hash
- Kerberos ticket attacks

---

# SOC Detection Areas

Monitor:

```
LSASS Access

Credential Dumping

Suspicious Process Access
```

Important:

```
Sysmon Event ID 10

Process Access
```

---

# 8. Impacket

## Overview

Impacket is a collection of Python tools for network protocol interaction.

Used by:

- Security researchers
- Red teams
- Incident responders

---

# Common Investigation Areas

Protocols:

```
SMB

Kerberos

LDAP

RPC
```

---

# SOC Relevance

Security teams use Impacket knowledge to detect:

- Lateral movement
- Remote execution
- Credential abuse

---

# Common Attacker Behaviors

Monitor for:

```
Remote service creation

SMB execution

Kerberos abuse
```

---

# 9. Microsoft Defender for Identity

## Overview

Microsoft Defender for Identity monitors Active Directory activity.

---

# Detects

Examples:

- Identity attacks
- Credential theft
- Lateral movement
- Domain compromise

---

# SOC Use Cases

Provides alerts for:

- Suspicious authentication
- Privilege escalation
- Reconnaissance

---

# 10. Splunk Active Directory Monitoring

## Overview

Splunk is commonly used as the SIEM platform for AD monitoring.

---

# Common Data Sources

Collect:

```
Windows Security Logs

Sysmon

PowerShell Logs

DNS Logs

Firewall Logs

Endpoint Logs
```

---

# Important Splunk Searches

## Failed Logins

```spl
index=wineventlog EventCode=4625
| stats count by Account_Name Source_Network_Address
```

---

## Privileged Group Changes

```spl
index=wineventlog EventCode=4728
| table _time Member_Name Group_Name Subject_User_Name
```

---

## New Users

```spl
index=wineventlog EventCode=4720
| table _time Target_User_Name Subject_User_Name Computer
```

---

# Tool Usage During SOC Investigations

## Scenario 1

Alert:

```
User Added To Domain Admins
```

Tools:

```
Splunk

AD Explorer

BloodHound
```

Investigation:

```
Who added user?

What permissions exist?

Can attacker move further?
```

---

# Scenario 2

Alert:

```
Possible DCSync Attack
```

Tools:

```
Splunk

BloodHound

AD Explorer
```

Investigation:

```
Who requested replication?

Does account have replication rights?

Was Domain Controller compromised?
```

---

# Scenario 3

Alert:

```
Suspicious Administrator Login
```

Tools:

```
Splunk

Sysinternals

EDR Platform
```

Investigation:

```
Source machine

Processes executed

Network connections
```

---

# Recommended SOC Toolkit

A mature SOC should have:

```
SIEM

        |

Splunk


Endpoint Visibility

        |

Sysmon + EDR


AD Analysis

        |

BloodHound

AD Explorer


Security Assessment

        |

PingCastle

Purple Knight


Investigation

        |

Sysinternals
```

---

# Tool Selection Guide

| Situation | Recommended Tools |
|---|---|
| Find attack paths | BloodHound |
| Investigate AD objects | AD Explorer |
| Analyze processes | Process Explorer |
| Find persistence | Autoruns |
| Review network connections | TCPView |
| Assess AD security | PingCastle |
| Detect identity weaknesses | Purple Knight |
| Investigate logs | Splunk |
| Analyze endpoint activity | Sysmon + EDR |

---

# Key Takeaways

Active Directory security requires visibility into identities, permissions, and authentication behavior.

The most valuable SOC tools help answer:

- Who has access?
- Who changed permissions?
- How did the attacker move?
- What privileges exist?
- Where is persistence located?

Combining:

- SIEM monitoring
- Endpoint telemetry
- AD analysis tools
- Security assessments

allows SOC teams to detect and investigate Active Directory attacks effectively.
