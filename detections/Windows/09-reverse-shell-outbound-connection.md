# Exploitation - Malicious Binary Outbound Reverse Shell Connection

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Command and Control / Exploitation |
| Platform | Windows |
| Data Source | Sysmon |
| Event ID | Event ID 3 - Network Connection |
| Severity | Critical |
| MITRE ATT&CK | T1059 - Command and Scripting Interpreter |
| Related Technique | T1071 - Application Layer Protocol |
| Schedule | Every 2 minutes |
| Alert Type | Behavioral Detection |
| Status | Validated |

---

# Overview

This detection identifies suspicious outbound network connections initiated by unknown binaries running from user-controlled directories.

Attackers commonly establish reverse shells after compromising a Windows endpoint to:

- Maintain remote access
- Execute commands remotely
- Control compromised hosts
- Transfer additional tools
- Perform post-exploitation activity

A reverse shell typically follows this pattern:

```
Victim Endpoint

        |

Malicious Binary Execution

        |

Outbound Connection

        |

Attacker Listener

        |

Remote Command Execution
```

---

# Detection Objective

The objective of this detection is to identify:

- Reverse shell activity
- Command and control communication
- Malware callbacks
- Unauthorized remote sessions
- Suspicious outbound connections

The detection focuses on:

- Process execution location
- Network destination
- Destination port
- User context

---

# Attack Scenario

During the Windows attack simulation, a reverse shell payload was executed after initial compromise.

Attack flow:

```
Initial Access

        |

Payload Delivered

        |

Malicious Binary Executed

        |

Outbound Connection Established

        |

Kali Listener Receives Connection

        |

Splunk Critical Alert Triggered
```

---

# Data Sources

## Sysmon Event ID 3

Provides visibility into:

- Network connections
- Source process
- Destination IP
- Destination port
- Connection timeline

Collected through:

```
Windows Endpoint

        |

Splunk Universal Forwarder

        |

Splunk Enterprise
```

Index:

```
wineventlog
```

---

# SPL Detection

```spl
index=wineventlog EventCode=3 (DestinationPort!=80 AND DestinationPort!=443 AND DestinationPort!=53 AND DestinationPort!=3389)
| eval process_path = Image
| eval host_name = Computer
| eval connection_destination = DestinationIp . ":" . DestinationPort
| eval user_context = User
| where match(process_path, "\\\\Users\\\\") OR match(process_path, "\\\\ProgramData\\\\") OR match(process_path, "\\\\Temp\\\\")
| stats
    min(_time) as execution_time
    values(connection_destination) as "C2 Remote Address"
    values(user_context) as "Account Context"
    by host_name, process_path
| eval "Session Trigger Time" = strftime(execution_time, "%Y-%m-%d %H:%M:%S")
| rename host_name as "Compromised Host", process_path as "Malicious Binary Path"
| table "Session Trigger Time", "Compromised Host", "Account Context", "Malicious Binary Path", "C2 Remote Address"
```

---

# Detection Logic Explanation

## 1. Monitor Outbound Connections

The detection monitors:

```
Sysmon Event ID 3
```

for outbound connections initiated by endpoint processes.

---

## 2. Identify Suspicious Execution Locations

The detection focuses on binaries executed from:

```
C:\Users\

C:\ProgramData\

C:\Temp\
```

These directories are commonly abused for:

- Malware execution
- Payload staging
- Reverse shells
- Unauthorized tools

---

## 3. Exclude Common Web Traffic

The detection excludes common legitimate ports:

```
80   HTTP

443  HTTPS

53   DNS

3389 RDP
```

This reduces noise from normal Windows activity.

---

# Example Detection Scenario

Example event:

```
Process:

C:\Users\Public\payload.exe


Connection:

192.168.50.20

        |

192.168.50.40:4444
```

Result:

```
Critical Reverse Shell Activity Detected
```

---

# Alert Configuration

## Severity

```
Critical
```

---

## Trigger Condition

The alert triggers when an unknown binary located in a user-controlled directory creates an outbound network connection using a non-standard application port.

Example:

```
Executable:

C:\Users\Public\shell.exe


Connection:

192.168.50.40:4444
```

This behavior is consistent with reverse shell or command-and-control activity.

---

## Schedule

```
Every 2 minutes
```

---

# Investigation Guidance

When this alert triggers:

---

# 1. Identify the Executed Binary

Review:

```
Malicious Binary Path
```

Determine:

- File name
- File location
- Digital signature
- Hash reputation

---

# 2. Investigate Network Destination

Review:

```
C2 Remote Address
```

Determine:

- Internal or external destination
- Destination ownership
- Firewall activity
- Related alerts

---

# 3. Review Process Timeline

Investigate:

```
Sysmon Event ID 1
Process Creation
```

Look for:

- Parent process
- Command line
- User account
- Initial execution source

---

# 4. Correlate Firewall Logs

Review:

```
pfSense Firewall Logs
```

Check:

- Allowed connection
- Destination IP
- Destination port
- Connection timing

---

# Incident Response Actions

Recommended actions:

- Isolate compromised endpoint
- Terminate malicious process
- Block attacker infrastructure
- Capture forensic evidence
- Remove malicious binaries
- Search for persistence mechanisms
- Reset compromised credentials

---

# False Positives

Possible legitimate activity:

- Developer tools
- Custom applications
- Internal scripts
- Testing utilities

Validation should consider:

- File reputation
- User authorization
- Destination legitimacy
- Process ownership

---

# Validation

This detection was validated during the Windows Attack Simulation.

Technique:

```
MITRE ATT&CK

T1059
Command and Scripting Interpreter

T1071
Application Layer Protocol
```

Simulation:

```
Kali Linux Attacker

        |

Reverse Shell Listener

        |

Windows Malicious Payload

        |

Outbound Connection

        |

Sysmon Event ID 3

        |

Splunk Critical Alert
```

Result:

```
Detection Triggered Successfully
```

---

# Related Playbooks

```
incident-response/playbooks/c2-investigation.md

incident-response/playbooks/malware-investigation.md

incident-response/playbooks/windows-endpoint-compromise.md
```

---

# Lessons Learned

Outbound connections from unknown binaries are strong indicators of compromise.

Combining process execution telemetry with network visibility allows SOC analysts to identify reverse shells, malware callbacks, and unauthorized remote access attempts.
