# Active Directory Lateral Movement

## Overview

Lateral movement is the process where an attacker moves from one compromised system to another inside an organization.

After gaining initial access, attackers rarely stop at one machine.

Their goal is usually to:

- Expand access
- Find valuable systems
- Obtain higher privileges
- Reach Domain Controllers
- Access sensitive data
- Maintain persistence

Active Directory environments provide many legitimate administration methods that attackers abuse for lateral movement.

Common techniques include:

- Remote Desktop Protocol (RDP)
- Server Message Block (SMB)
- Windows Remote Management (WinRM)
- Windows Management Instrumentation (WMI)
- PsExec
- Pass-the-Hash
- Remote Services Abuse

---

# Lateral Movement Attack Lifecycle

Typical attack path:

```
Initial Compromise

        |

Compromised Endpoint

        |

Credential Theft

        |

Lateral Movement

        |

Privilege Escalation

        |

Domain Controller Access

        |

Domain Compromise
```

---

# Why Attackers Perform Lateral Movement

Attackers use lateral movement to:

- Access file servers
- Compromise administrator accounts
- Find sensitive information
- Deploy malware
- Reach critical infrastructure

Example:

```
Employee Laptop

        |

File Server

        |

Database Server

        |

Domain Controller
```

---

# Common Active Directory Lateral Movement Techniques

---

# 1. Remote Desktop Protocol (RDP)

## Overview

RDP allows users to remotely access Windows systems.

Default port:

```
TCP 3389
```

Organizations use RDP for:

- Administration
- Remote support
- Server management

Attackers abuse RDP after obtaining credentials.

---

# RDP Attack Flow

```
Compromised Account

        |

RDP Authentication

        |

Remote System Access

        |

Privilege Escalation
```

---

# Common RDP Attacks

## Brute Force

Attackers attempt many passwords.

Example:

```
username:

administrator


password attempts:

1000+
```

---

## Credential Reuse

Attackers reuse stolen passwords.

Example:

```
Compromised Employee Password

        |

RDP Login To Server
```

---

# RDP Detection

Important Events:

## Event ID 4624

Successful Logon

Review:

```
Logon Type = 10

Source IP

Account Name
```

---

## Event ID 4625

Failed Logon

Detect:

- Brute force
- Password spraying

---

## Event ID 1149

Remote Desktop Authentication Successful

---

# RDP Investigation

Check:

```
Source IP

Username

Target Host

Login Time

Privilege Level

Previous Activity
```

---

# Splunk Example

```spl
index=wineventlog EventCode=4624 Logon_Type=10
| stats count by Account_Name Source_Network_Address Computer
```

---

# MITRE ATT&CK

```
T1021.001

Remote Services: RDP
```

---

# 2. SMB Lateral Movement

## Overview

SMB is a Windows file-sharing protocol.

Default ports:

```
TCP 445

TCP 139
```

Attackers use SMB for:

- File access
- Remote execution
- Malware propagation

---

# SMB Attack Methods

Common examples:

- PsExec
- Remote file copying
- Administrative shares

---

# Administrative Shares

Windows provides hidden shares:

```
C$

ADMIN$

IPC$
```

Example:

```
Attacker

 |

\\SERVER01\C$

 |

Copy Malware
```

---

# SMB Detection

Important Events:

## Event ID 5140

Network Share Accessed

Shows:

- Share name
- Source address
- User account

---

## Event ID 5145

Detailed File Share Access

Useful for:

- Suspicious file access
- Malware movement

---

# Investigation Example

Suspicious:

```
User workstation

accesses

ADMIN$

on Server
```

Investigate:

```
Was user authorized?

Was file copied?

Was execution performed?
```

---

# MITRE ATT&CK

```
T1021.002

SMB/Windows Admin Shares
```

---

# 3. PsExec

## Overview

PsExec is a Microsoft Sysinternals tool used for remote command execution.

Administrators use it for:

- Remote management
- Troubleshooting

Attackers abuse it for:

- Malware deployment
- Lateral movement

---

# PsExec Attack Flow

```
Attacker

 |

SMB Connection

 |

Create Remote Service

 |

Execute Command

 |

Remote Access
```

---

# Indicators

Look for:

- Service creation
- Remote execution
- Suspicious binaries

---

# Important Events

## Event ID 7045

Service Created

Example:

```
Service Name:

PSEXESVC
```

---

## Event ID 4697

Service Installed

---

# Sysmon Detection

## Event ID 1

Process Creation

Look for:

```
psexec.exe

cmd.exe

powershell.exe
```

---

## Event ID 3

Network Connection

Look for:

```
SMB connections
```

---

# MITRE ATT&CK

```
T1569.002

System Services: Service Execution
```

---

# 4. Windows Management Instrumentation (WMI)

## Overview

WMI is a Windows management framework.

Administrators use it for:

- System management
- Automation

Attackers abuse WMI for:

- Remote execution
- Persistence
- Discovery

---

# WMI Attack Flow

```
Attacker

 |

WMI Request

 |

Remote Computer

 |

Command Execution
```

---

# Detection

Important Events:

## Sysmon Event ID 1

Process Creation

Look for:

```
wmic.exe

powershell.exe

cmd.exe
```

---

## Sysmon Event ID 19

WMI Event Filter Created

---

## Sysmon Event ID 20

WMI Event Consumer Created

---

## Sysmon Event ID 21

WMI Binding Created

---

# Suspicious Example

```
wmic /node:SERVER01 process call create powershell.exe
```

---

# MITRE ATT&CK

```
T1047

Windows Management Instrumentation
```

---

# 5. Windows Remote Management (WinRM)

## Overview

WinRM allows remote PowerShell management.

Default ports:

```
5985 HTTP

5986 HTTPS
```

---

# Attack Usage

Attackers use:

- PowerShell Remoting
- Remote command execution

Example:

```
Enter-PSSession SERVER01
```

---

# Detection

Monitor:

## Event ID 4624

Logon Type:

```
3 Network Logon
```

---

## PowerShell Events

Important:

```
4103

4104
```

---

# Suspicious Activity

Example:

```
Normal User

+

Remote PowerShell Session

+

Sensitive Server
```

---

# MITRE ATT&CK

```
T1021.006

Windows Remote Management
```

---

# 6. Pass-The-Hash

## Overview

Pass-the-Hash allows attackers to authenticate using stolen NTLM hashes.

The attacker does not need the password.

---

# Attack Flow

```
Credential Dumping

        |

Obtain NTLM Hash

        |

Authenticate Remotely

        |

Move Laterally
```

---

# Common Targets

Attackers target:

- Local administrators
- Domain administrators
- Service accounts

---

# Detection Indicators

Look for:

- NTLM authentication
- Remote admin logins
- Unusual source computers

---

# Important Events

## Event ID 4624

Review:

```
Authentication Package

NTLM

Logon Type
```

---

## Event ID 4672

Privileged logon

---

# MITRE ATT&CK

```
T1550.002

Pass the Hash
```

---

# 7. Remote Service Abuse

## Overview

Attackers create or abuse Windows services to execute code remotely.

Examples:

- Malicious services
- Service hijacking

---

# Detection

Monitor:

## Event ID 7045

Service Creation

Review:

```
Service Name

Binary Path

Account
```

---

# Suspicious Example

```
Service:

Updater


Binary:

C:\Temp\malware.exe
```

---

# Lateral Movement Detection Strategy

SOC analysts should correlate multiple data sources.

---

# Windows Security Logs

Monitor:

```
4624

4625

4672

4688

4697

4728
```

---

# Sysmon

Monitor:

```
Event ID 1

Process Creation


Event ID 3

Network Connection


Event ID 10

Process Access


Event ID 11

File Creation
```

---

# Network Logs

Monitor:

- SMB traffic
- RDP connections
- Remote administration protocols

---

# Example Attack Investigation

Scenario:

```
Alert:

Suspicious RDP Login
```

Investigation:

```
1. Identify source IP

2. Identify account

3. Check logon type

4. Review previous failed logins

5. Check privilege level

6. Investigate commands executed

7. Look for lateral movement
```

---

# Lateral Movement Hunting Queries

## Detect RDP Logins

```spl
index=wineventlog EventCode=4624 Logon_Type=10
| stats count by Account_Name Source_Network_Address Computer
```

---

## Detect Service Creation

```spl
index=wineventlog EventCode=7045
| table _time Computer Service_Name Image_Path
```

---

## Detect Administrative Share Access

```spl
index=wineventlog EventCode=5140
| table _time Account_Name Source_Address Share_Name
```

---

# Prevention Recommendations

## Restrict Remote Access

Implement:

- Network segmentation
- Firewall rules
- VPN access

---

## Protect Credentials

Use:

- MFA
- Privileged Access Management
- LAPS
- gMSA accounts

---

## Disable Unnecessary Services

Review:

- SMB exposure
- RDP access
- WinRM usage

---

## Monitor Privileged Accounts

Track:

- Domain Admin usage
- Administrator logins
- Service accounts

---

# SOC Investigation Checklist

```
[ ] Source system identified

[ ] Destination system identified

[ ] Account identified

[ ] Authentication method reviewed

[ ] Privilege level checked

[ ] Process execution reviewed

[ ] Network connections reviewed

[ ] Malware indicators checked

[ ] Containment considered
```

---

# Key Takeaways

Lateral movement is a critical stage of Active Directory attacks.

Attackers commonly abuse legitimate Windows administration protocols:

- RDP
- SMB
- PsExec
- WMI
- WinRM

SOC analysts must monitor authentication, process execution, and network activity together because lateral movement usually involves multiple stages and multiple systems.

Early detection of lateral movement can prevent full domain compromise.
