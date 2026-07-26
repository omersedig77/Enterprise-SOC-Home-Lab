# Suspicious PowerShell Activity Playbook

## Purpose

Investigate suspicious PowerShell execution that may indicate malicious scripting, payload delivery, or post-exploitation activity.

---

## Detection

### Data Sources

- Sysmon Event ID 1 – Process Creation
- Sysmon Event ID 3 – Network Connection
- Windows Event Logs
- Splunk Enterprise

### Indicators

- Encoded PowerShell commands
- Download cradle techniques
- Outbound network connections
- Child process creation
- Hidden PowerShell execution
- Execution policy bypass

---

## Investigation

### Step 1

Identify:

- User
- Host
- Parent process
- Command line

---

### Step 2

Review the PowerShell command.

Look for:

- `-enc`
- `-EncodedCommand`
- `-nop`
- `-w hidden`
- `-ExecutionPolicy Bypass`

---

### Step 3

Determine whether PowerShell initiated:

- Network connections
- File downloads
- Process execution
- Script execution

---

### Step 4

Review child processes.

Examples:

- cmd.exe
- rundll32.exe
- regsvr32.exe
- mshta.exe

---

### Step 5

Correlate:

- Sysmon Process Creation
- Network Connections
- Firewall Logs

---

## Recommended Containment

- Terminate malicious PowerShell processes.
- Isolate the endpoint if compromise is confirmed.
- Block outbound communication.

---

## Recommended Eradication

- Remove malicious scripts.
- Remove persistence.
- Scan for malware.

---

## Recommended Recovery

- Continue monitoring PowerShell activity.
- Review PowerShell logging configuration.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| PowerShell | T1059.001 |
| Ingress Tool Transfer | T1105 |

---

## Lessons Learned

PowerShell is widely used by administrators and attackers alike. Context, command-line analysis, and process relationships are essential when determining whether activity is malicious.
