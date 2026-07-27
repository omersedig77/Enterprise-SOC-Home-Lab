# Kerberos Attacks

## Overview

Kerberos is the default authentication protocol used by Active Directory environments.

Because Kerberos controls access to enterprise resources, attackers frequently target it to:

- Steal authentication tickets
- Extract password hashes
- Forge authentication requests
- Move laterally
- Maintain persistence
- Obtain Domain Administrator privileges

Understanding Kerberos attacks is essential for SOC analysts because many advanced Active Directory compromises involve Kerberos abuse.

---

# Kerberos Authentication Review

Before understanding attacks, it is important to understand the normal Kerberos process.

```
User Login

     |

Request Authentication

     |

Domain Controller (KDC)

     |

Ticket Granting Ticket (TGT)

     |

Request Service Ticket

     |

Access Service
```

---

# Kerberos Components

## KDC (Key Distribution Center)

Runs on Domain Controllers.

Contains:

```
Authentication Service (AS)

Ticket Granting Service (TGS)
```

---

## TGT (Ticket Granting Ticket)

A ticket proving the identity of a user.

Used to request service tickets.

---

## Service Ticket

Allows access to a specific service.

Examples:

```
SQL Server

File Server

HTTP Service

LDAP
```

---

# Common Kerberos Attack Types

The most common Kerberos attacks:

1. Kerberoasting
2. AS-REP Roasting
3. Golden Ticket
4. Silver Ticket
5. Pass-the-Ticket
6. Unconstrained Delegation Abuse

---

# 1. Kerberoasting

## Overview

Kerberoasting is an attack where adversaries request Kerberos service tickets for service accounts and attempt to crack the encrypted ticket offline.

The goal:

```
Obtain Service Account Password
```

---

# Why Service Accounts Are Targeted

Service accounts often have:

- High privileges
- Weak passwords
- Passwords that rarely change

Examples:

```
svc_sql

svc_backup

svc_web
```

---

# Kerberoasting Attack Flow

```
Attacker Has Domain User Account

            |

Request Service Ticket

            |

Receive Encrypted Ticket

            |

Extract Ticket Hash

            |

Offline Password Cracking

            |

Service Account Compromised
```

---

# Technical Explanation

The attacker requests a TGS ticket:

```
Service Principal Name (SPN)

        |

KDC

        |

Encrypted Service Ticket
```

The attacker extracts the ticket and attempts offline cracking.

---

# Example Attack Tools

Common tools:

```
Rubeus

Impacket

Mimikatz
```

---

# Detection Indicators

SOC analysts should monitor:

## Event ID 4769

Kerberos Service Ticket Request

Look for:

- Large number of service ticket requests
- Requests from unusual machines
- RC4 encryption usage
- Normal users requesting privileged services

---

## Suspicious Pattern Example

Normal:

```
User

Requests

Few Service Tickets
```

Suspicious:

```
Single User

Requests

Hundreds of Service Tickets

For Multiple Services
```

---

# Splunk Detection Example

```spl
index=wineventlog EventCode=4769
| stats count by Account_Name Service_Name Ticket_Encryption_Type Client_Address
| where count > 50
```

---

# MITRE ATT&CK

```
T1558.003

Steal or Forge Kerberos Tickets: Kerberoasting
```

---

# Investigation Steps

When Kerberoasting is suspected:

```
1. Identify requesting account

2. Identify requested service

3. Review source computer

4. Check account privileges

5. Reset compromised service account password

6. Review lateral movement activity
```

---

# 2. AS-REP Roasting

## Overview

AS-REP Roasting targets accounts that do not require Kerberos pre-authentication.

Normally:

```
User Authentication

        |

Pre-Authentication

        |

Ticket Granted
```

With vulnerable accounts:

```
No Pre-Authentication

        |

Encrypted Response Returned

        |

Offline Cracking
```

---

# Attack Flow

```
Attacker Finds Vulnerable Account

          |

Requests Authentication Response

          |

Receives Encrypted Data

          |

Cracks Password Offline
```

---

# Vulnerable Configuration

Affected accounts:

```
Do not require Kerberos pre-authentication
```

---

# Detection

Monitor:

## Event ID 4768

Kerberos Authentication Ticket Requested

Suspicious indicators:

- Unusual encryption types
- Requests from unknown systems
- Authentication attempts against multiple users

---

# Splunk Example

```spl
index=wineventlog EventCode=4768
| stats count by Account_Name Client_Address Ticket_Encryption_Type
```

---

# MITRE ATT&CK

```
T1558.004

Steal or Forge Kerberos Tickets: AS-REP Roasting
```

---

# 3. Golden Ticket Attack

## Overview

Golden Ticket is one of the most dangerous Active Directory attacks.

The attacker creates a forged Kerberos TGT.

Requirements:

```
KRBTGT Account Hash
```

---

# Why KRBTGT Is Important

The KRBTGT account is used by Active Directory to sign Kerberos tickets.

If attackers obtain its password hash, they can create fake tickets.

---

# Golden Ticket Attack Flow

```
Domain Controller Compromise

          |

Extract KRBTGT Hash

          |

Create Forged TGT

          |

Impersonate Any User

          |

Access Domain Resources
```

---

# Impact

Attackers can:

- Become Domain Administrator
- Access servers
- Maintain persistence
- Survive password changes

---

# Detection Indicators

Golden Tickets are difficult to detect.

Look for:

- Abnormal ticket lifetime
- Users authenticating from unusual systems
- Privileged activity without normal authentication
- Unusual domain administrator behavior

---

# Important Events

## Event ID 4624

Successful Logon

Review:

```
Account

Source IP

Logon Type
```

---

## Event ID 4769

Service Ticket Request

Review:

```
Ticket Information

Encryption Type

User Activity
```

---

# MITRE ATT&CK

```
T1558.001

Golden Ticket
```

---

# 4. Silver Ticket Attack

## Overview

Silver Ticket attacks forge service tickets instead of TGTs.

Difference:

Golden Ticket:

```
Forge TGT

Access Many Services
```

Silver Ticket:

```
Forge Service Ticket

Access Specific Service
```

---

# Attack Requirements

Attacker needs:

```
Service Account Hash
```

Example:

```
SQL Service Account

Computer Account
```

---

# Attack Flow

```
Steal Service Hash

        |

Create Fake Service Ticket

        |

Access Target Service
```

---

# Detection

Monitor:

- Service access anomalies
- Abnormal authentication
- Missing corresponding authentication events

---

# MITRE ATT&CK

```
T1558.002

Silver Ticket
```

---

# 5. Pass-The-Ticket

## Overview

Pass-the-Ticket occurs when attackers steal legitimate Kerberos tickets and reuse them.

The attacker does not need the password.

---

# Attack Flow

```
Compromise Endpoint

        |

Steal Kerberos Ticket

        |

Inject Ticket

        |

Authenticate As User
```

---

# Common Tools

Examples:

```
Mimikatz

Rubeus
```

---

# Detection Indicators

Look for:

- Authentication from unusual systems
- Privileged accounts moving unexpectedly
- Ticket usage without normal login activity

---

# Important Events

Event IDs:

```
4624

4768

4769
```

---

# MITRE ATT&CK

```
T1550.003

Pass the Ticket
```

---

# 6. Unconstrained Delegation Abuse

## Overview

Delegation allows services to authenticate on behalf of users.

Unconstrained delegation allows a service to store user Kerberos tickets.

Attackers abuse this to steal privileged tickets.

---

# Attack Flow

```
Compromise Delegated Server

          |

Wait For Admin Login

          |

Capture Kerberos Ticket

          |

Impersonate Administrator
```

---

# Detection

Monitor:

- Computer accounts with delegation enabled
- Unexpected privileged authentication

---

# Defensive Recommendations

## Protect Service Accounts

Implement:

- Strong passwords
- Password rotation
- Managed Service Accounts

---

# Disable Weak Kerberos Encryption

Avoid:

```
RC4 Encryption
```

Prefer:

```
AES Encryption
```

---

# Protect Privileged Accounts

Use:

- Separate admin accounts
- MFA
- Privileged Access Management

---

# Monitor Kerberos Events

Enable auditing for:

```
4768

4769

4771
```

---

# Disable Unnecessary Delegation

Review:

```
TrustedForDelegation

TrustedToAuthForDelegation
```

---

# SOC Kerberos Investigation Workflow

When detecting Kerberos abuse:

```
Alert Triggered

        |

Identify Account

        |

Review Event ID

        |

Identify Source Computer

        |

Check Requested Service

        |

Check Privileges

        |

Review Related Authentication

        |

Contain Account If Needed
```

---

# Kerberos Investigation Checklist

```
[ ] User identified

[ ] Source IP identified

[ ] Domain Controller identified

[ ] Ticket type identified

[ ] Encryption type reviewed

[ ] Service account reviewed

[ ] Privilege level checked

[ ] Related logons investigated

[ ] Lateral movement checked
```

---

# Key Takeaways

Kerberos is a critical component of Active Directory authentication.

Attackers abuse Kerberos to:

- Steal credentials
- Forge authentication tickets
- Impersonate users
- Maintain persistence
- Compromise entire domains

The most important Kerberos attacks for SOC analysts are:

- Kerberoasting
- AS-REP Roasting
- Golden Ticket
- Silver Ticket
- Pass-the-Ticket

Monitoring Kerberos events and abnormal authentication behavior is essential for detecting Active Directory compromise.
