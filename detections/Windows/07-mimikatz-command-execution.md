# Credential Access - Mimikatz Ingress and Command Execution Correlated

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Credential Access |
| Platform | Windows |
| Data Source | Windows Security Logs + Sysmon |
| Event IDs | Event ID 4688 - Process Creation / Event ID 5156 - Network Connection |
| Severity | Critical |
| MITRE ATT&CK | T1003 - OS Credential Dumping |
| Related Technique | T1105 - Ingress Tool Transfer |
| Schedule | Every 10 minutes |
| Alert Type | Correlation Detection |
| Status | Validated |

---

# Overview

This detection identifies suspicious credential access activity involving tools such as:

```
mimikatz
certutil
sekurlsa
privilege::debug
```

Attackers commonly use these tools after gaining access to a Windows endpoint to extract credentials from memory or download additional attack utilities.

The detection correlates:

- Suspicious tool execution
- Command-line activity
- Process creation
- Network communication
- Credential dumping behavior

---

# Detection Objective

The goal of this detection is to identify attacker behavior associated with:

- Credential dumping
- Mimikatz execution
- Security privilege abuse
- Tool transfer
- Post-compromise activity

The detection focuses on attacker actions rather than relying only on file names.

---

# Attack Scenario

During the Windows attack simulation, credential access was performed after obtaining elevated access.

Attack flow:

```
Windows Endpoint Compromised

        |

Tool Transfer / Execution

        |

Privilege Debug Enabled

        |

Mimikatz Commands Executed

        |

Credential Extraction Attempt

        |

Splunk Critical Alert Triggered
```

---

# Data Sources

## Windows Security Event ID 4688

Provides visibility into:

- Process creation
- Command line arguments
- Executed binaries
- User context

---

## Windows Filtering Platform Event ID 5156

Provides visibility into:

- Network connections
- Source IP
- Destination IP
- Destination ports

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
index=wineventlog (EventCode=4688 AND ("mimikatz" OR "certutil" OR "privilege::debug" "sekurlsa::")) OR (EventCode=5156 AND ("mimikatz" OR "certutil" OR "vuln"))
| eval Host_Name = coalesce(Computer, ComputerName, host, "DESKTOP-KE4I2IC")
| rex field=_raw "(?i)(Parent Process Name|Creator Process Name):\s*(?<win_parent>[^\r\n]+)"
| rex field=_raw "(?i)(Account Name|Security ID):\s*(?<win_user>[^\r\n]+)"
| rex field=_raw "(?i)(Source Address|Source IP):\s*(?<win_src_ip>[^\r\n]+)"
| rex field=_raw "(?i)(Destination Address|Destination IP):\s*(?<win_dest_ip>[^\r\n]+)"
| rex field=_raw "(?i)(Destination Port|Port):\s*(?<win_dest_port>[^\r\n]+)"
| rex field=_raw "(?i)Process Command Line:\s*(?<win_cmd>[^\r\n]+)"
| eval normalized_user = if(match(win_user, "(?i)(SYSTEM|LocalSystem|DESKTOP)"), "SYSTEM", win_user)
| stats
    min(_time) as earliest_time
    values(normalized_user) as "Account Who Executed It"
    latest(win_parent) as "Spawning Parent Process"
    values(win_cmd) as "Commands Executed"
    latest(win_src_ip) as "Compromised Host IP"
    latest(win_dest_ip) as "Attacker Kali IP"
    latest(win_dest_port) as "Attacker Listener Port"
    by Host_Name
| mvexpand "Commands Executed"
| where match('Commands Executed', "(?i)(certutil|mimikatz|sekurlsa|privilege)")
| rex field="Commands Executed" "(?i)>\s*(?<extracted_out_file>[^\s\"]+)"
| eval "Output Staging File" = coalesce(extracted_out_file, "Displayed to Console / Active Session")
| eval "Attack Start Time" = strftime(earliest_time, "%Y-%m-%d %H:%M:%S")
| rename Host_Name as "Target Host"
| table "Attack Start Time", "Target Host", "Account Who Executed It", "Spawning Parent Process", "Commands Executed", "Compromised Host IP", "Attacker Kali IP", "Attacker Listener Port", "Output Staging File"
```

---

# Detection Logic Explanation

## 1. Identify Suspicious Tools

The detection searches for indicators associated with credential theft:

```
mimikatz

sekurlsa::

privilege::debug

certutil
```

---

## 2. Correlate Execution Context

The alert collects:

- Executing user
- Parent process
- Command line
- Target system

This allows analysts to determine whether the activity was legitimate administration or malicious behavior.

---

## 3. Identify Tool Transfer Activity

The detection includes:

```
certutil
```

because attackers commonly abuse it to:

- Download payloads
- Encode/decode files
- Transfer tools without dropping traditional malware

---

# Alert Configuration

## Severity

```
Critical
```

---

## Trigger Condition

The alert triggers when suspicious credential access commands are executed, including:

```
mimikatz

sekurlsa::

privilege::debug
```

or when attacker tooling is transferred and executed through suspicious utilities.

Example:

```
Command Line:

mimikatz.exe privilege::debug sekurlsa::logonpasswords
```

This indicates possible credential dumping activity.

---

## Schedule

```
Every 10 minutes
```

---

# Investigation Guidance

When this alert triggers:

---

# 1. Review Executed Commands

Analyze:

```
Commands Executed
```

Look for:

- Credential dumping commands
- Password extraction attempts
- Tool download commands

---

# 2. Identify User Context

Review:

```
Account Who Executed It
```

Determine:

- Compromised account
- Privilege level
- Authentication history

---

# 3. Investigate Parent Process

Review:

```
Spawning Parent Process
```

Examples:

Suspicious:

```
powershell.exe

cmd.exe

wscript.exe

winword.exe
```

---

# 4. Review Network Activity

Investigate:

```
Attacker Kali IP

Attacker Listener Port
```

Correlate with:

- Firewall logs
- Sysmon Event ID 3
- Reverse shell alerts

---

# Incident Response Actions

Recommended actions:

- Isolate compromised endpoint
- Terminate malicious processes
- Reset affected credentials
- Remove attacker tools
- Review lateral movement attempts
- Hunt for additional compromised systems

---

# False Positives

Possible legitimate activity:

- Security testing
- Penetration testing
- Red team exercises
- Authorized administrators

Validation should consider:

- User identity
- Business justification
- Tool location
- Command arguments

---

# Validation

This detection was validated during the Windows Attack Simulation.

Technique:

```
MITRE ATT&CK T1003

OS Credential Dumping
```

Simulation:

```
Compromised Windows Host

        |

Mimikatz Execution

        |

Credential Dump Attempt

        |

Windows Telemetry Generated

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
incident-response/playbooks/credential-compromise.md

incident-response/playbooks/windows-endpoint-compromise.md

incident-response/playbooks/malware-investigation.md
```

---

# Lessons Learned

Credential dumping is a common objective after endpoint compromise.

Combining process execution telemetry with network visibility allows SOC analysts to identify attacker tooling, understand attack progression, and respond before credentials are abused for further compromise.
