# Active Directory Authentication

## Overview

Authentication is the process of verifying the identity of a user, computer, or service before granting access to resources.

In Active Directory environments, authentication is one of the most important areas monitored by SOC teams because attackers frequently abuse authentication mechanisms to:

- Steal credentials
- Access systems
- Move laterally
- Escalate privileges
- Maintain persistence

The two main authentication protocols used in Active Directory are:

- Kerberos
- NTLM

SOC analysts must understand how these protocols work because many detection use cases are based on authentication behavior.

---

# Active Directory Authentication Flow

A simplified authentication process:

```
User Attempts Login

        |

Domain Controller Receives Request

        |

Authentication Protocol Validates Identity

        |

User Receives Access Token

        |

Access Granted To Resources
```

---

# Authentication Components

## Security Principal

A security principal is an identity that can authenticate.

Examples:

```
User Account

Computer Account

Service Account

Group
```

---

# Credentials

Credentials prove identity.

Examples:

```
Username + Password

Password Hash

Kerberos Ticket

Certificate
```

---

# Security Identifier (SID)

Every Active Directory object has a unique SID.

Example:

```
S-1-5-21-123456789-123456789-123456789-1105
```

Used by Windows for:

- Identity tracking
- Access control
- Permissions

---

# Access Token

After successful authentication, Windows creates an access token.

It contains:

- User SID
- Group memberships
- Privileges

Example:

```
User:

john.smith


Groups:

Domain Users

IT Administrators


Privileges:

Remote Desktop Access
```

Windows uses this token to determine what resources the user can access.

---

# Kerberos Authentication

## Overview

Kerberos is the default authentication protocol in modern Active Directory environments.

It provides:

- Secure authentication
- Mutual authentication
- Ticket-based access

Instead of sending passwords across the network, Kerberos uses encrypted tickets.

---

# Kerberos Components

## Key Distribution Center (KDC)

The KDC runs on Domain Controllers.

It contains two services:

```
Authentication Service (AS)

Ticket Granting Service (TGS)
```

---

# Ticket Granting Ticket (TGT)

A TGT proves that a user has authenticated successfully.

The user receives a TGT after logging in.

Flow:

```
User Login

      |

Request TGT

      |

Domain Controller

      |

Receive TGT

```

The TGT is then used to request access to services.

---

# Service Ticket

A service ticket provides access to a specific service.

Example:

User wants access to:

```
\\FileServer01\Finance
```

Flow:

```
User

 |

Requests Service Ticket

 |

Domain Controller

 |

Receives Ticket

 |

Access File Server
```

---

# Kerberos Authentication Flow

Complete process:

```
1. User enters username/password

            |

2. Client requests authentication

            |

3. Domain Controller validates user

            |

4. KDC issues TGT

            |

5. User requests service ticket

            |

6. Service validates ticket

            |

7. Access granted
```

---

# Kerberos Event IDs

## Event ID 4768

### Kerberos Authentication Ticket Requested

Generated when a user requests a TGT.

SOC uses it to investigate:

- Authentication attempts
- Password spraying
- Kerberos attacks


Important fields:

```
Account Name

Client Address

Ticket Encryption Type
```

---

## Event ID 4769

### Kerberos Service Ticket Requested

Generated when requesting access to a service.

Important for detecting:

- Kerberoasting
- Abnormal service access


Important fields:

```
Account Name

Service Name

Client Address

Encryption Type
```

---

## Event ID 4771

### Kerberos Pre-Authentication Failed

Generated when Kerberos authentication fails.

Common causes:

- Wrong password
- Password attacks
- Account compromise attempts


SOC investigates:

```
Many failures

+

Same source IP

+

Different users
```

Possible:

```
Password Spraying
```

---

# Kerberos Attacks

---

# Kerberoasting

## Overview

Kerberoasting targets service accounts.

Attack flow:

```
Attacker Compromises User Account

          |

Requests Service Tickets

          |

Extracts Ticket Hash

          |

Offline Password Cracking

          |

Service Account Compromised
```

---

# Detection Indicators

Look for:

- Large number of 4769 events
- Unusual service ticket requests
- RC4 encryption usage
- Requests from normal user accounts

---

# SOC Investigation Example

Alert:

```
Suspicious Kerberos Activity
```

Investigate:

```
Who requested ticket?

Which service?

What encryption type?

Source computer?

Was account privileged?
```

---

# Golden Ticket Attack

## Overview

Golden Ticket attacks target the KRBTGT account.

The attacker steals:

```
KRBTGT password hash
```

Then creates forged Kerberos tickets.

---

# Attack Flow

```
Domain Controller Compromise

        |

Extract KRBTGT Hash

        |

Create Fake Ticket

        |

Access Domain Resources
```

---

# Detection Indicators

Look for:

- Unusual ticket lifetime
- Abnormal user authentication
- Authentication from unexpected systems

---

# Pass-The-Ticket

## Overview

Attackers steal valid Kerberos tickets and reuse them.

Attack flow:

```
Steal Ticket

      |

Inject Ticket

      |

Authenticate Without Password
```

---

# Detection Indicators

Monitor:

- Unusual authentication sources
- Privileged accounts
- Abnormal ticket usage

---

# NTLM Authentication

## Overview

NTLM is an older Windows authentication protocol.

It uses:

- Challenge-response authentication
- Password hashes

Although older, NTLM still exists for compatibility.

---

# NTLM Authentication Flow

```
User Requests Access

        |

Server Sends Challenge

        |

Client Responds With Hash

        |

Server Validates Response

        |

Access Granted
```

---

# NTLM Security Risks

Attackers abuse NTLM for:

- Pass-the-Hash
- Credential relay
- Hash theft

---

# Pass-The-Hash Attack

## Overview

Attackers use stolen NTLM hashes instead of passwords.

Example:

Normal:

```
Username

+

Password
```

Attack:

```
Username

+

NTLM Hash
```

---

# Attack Flow

```
Credential Theft

        |

Obtain NTLM Hash

        |

Authenticate To Remote System

        |

Lateral Movement
```

---

# Detection Indicators

Look for:

- Unexpected NTLM authentication
- Administrative logins
- Remote authentication from unusual systems

---

# NTLM Event IDs

## Event ID 4624

Successful Logon

Important fields:

```
Account Name

Source Address

Logon Type

Authentication Package
```

---

## Event ID 4625

Failed Logon

Used for:

- Brute force detection
- Password spraying


---

## Event ID 4776

NTLM Authentication Attempt

Used to monitor:

- Credential validation
- Failed authentication attempts

---

# Windows Logon Types

Understanding logon types is important during investigations.

---

# Logon Type 2

## Interactive Logon

Example:

User physically logs into a workstation.

```
Keyboard Login
```

---

# Logon Type 3

## Network Logon

Example:

Accessing:

```
SMB Share

Remote Resource
```

Common in:

- Lateral movement investigations

---

# Logon Type 5

## Service Logon

Example:

Windows service starts.

---

# Logon Type 7

## Unlock

User unlocks workstation.

---

# Logon Type 10

## Remote Interactive Logon

Example:

```
RDP Login
```

Important for:

- Remote access investigation
- Brute force attacks

---

# Authentication Attack Detection Examples

---

# Password Spraying

Attack pattern:

```
One Password

Against

Many Users
```

Detection:

Look for:

```
Same source IP

+

Multiple usernames

+

Failed authentication
```

---

# Brute Force

Attack pattern:

```
Many Password Attempts

Against

One Account
```

Detection:

Look for:

```
Many 4625 events

+

Same account

+

Same source
```

---

# Suspicious Admin Login

Example:

Normal:

```
Administrator

Logs into Server
```

Suspicious:

```
Normal User

Logs into Domain Controller
```

---

# Authentication Investigation Workflow

When investigating authentication alerts:

```
Alert Triggered

        |

Identify User

        |

Identify Source IP

        |

Identify Target System

        |

Review Authentication Type

        |

Check Privileges

        |

Review Related Events

        |

Determine Compromise
```

---

# SOC Authentication Investigation Checklist

```
[ ] Username identified

[ ] Source IP identified

[ ] Destination system identified

[ ] Authentication protocol identified

[ ] Logon type reviewed

[ ] Failed attempts reviewed

[ ] Successful login reviewed

[ ] Privilege level checked

[ ] Lateral movement checked

[ ] Account compromise determined
```

---

# Authentication Hardening

Recommended security controls:

## Enable MFA

Protects against:

- Password theft
- Credential phishing

---

## Reduce NTLM Usage

Where possible:

- Disable unnecessary NTLM
- Prefer Kerberos

---

## Protect Privileged Accounts

Use:

- Separate admin accounts
- Privileged Access Management
- Strong authentication

---

## Monitor Authentication Events

Enable auditing for:

- Logons
- Failed attempts
- Privilege changes
- Kerberos activity

---

# Key Takeaways

Authentication monitoring is one of the most important SOC responsibilities.

Attackers commonly target authentication to:

- Steal credentials
- Access systems
- Move laterally
- Gain administrator privileges

Understanding:

- Kerberos
- NTLM
- Logon types
- Authentication events

allows SOC analysts to detect and investigate Active Directory attacks effectively.
