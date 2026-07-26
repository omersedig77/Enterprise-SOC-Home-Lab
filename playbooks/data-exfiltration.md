# Data Exfiltration Investigation Playbook

## Purpose

Investigate alerts or suspicious activity indicating that sensitive data may be leaving the network or being transmitted to an unauthorized external destination.

---

## Detection

### Data Sources

- Sysmon Event ID 3 – Network Connection
- pfSense Firewall Logs
- Windows Event Logs
- Linux System Logs
- Splunk Enterprise

### Indicators

- Large outbound data transfers
- Connections to unfamiliar external IP addresses
- Unusual outbound ports
- Repeated outbound network sessions
- Data transfers occurring outside normal business hours

---

## Investigation

### Step 1

Identify:

- Source host
- User account
- Destination IP
- Destination port
- Time of activity

---

### Step 2

Determine:

- Which process initiated the connection
- Parent process
- Command-line arguments
- Amount of data transferred (if available)

---

### Step 3

Correlate:

- Sysmon network events
- Firewall logs
- Authentication logs
- Process creation events

---

### Step 4

Determine whether additional attacker activity occurred before the outbound connection.

Examples:

- Credential dumping
- Persistence
- Reverse shell
- PowerShell execution

---

## Recommended Containment

- Isolate the affected endpoint.
- Block the external destination.
- Preserve forensic evidence.

---

## Recommended Eradication

- Remove malicious tools.
- Remove persistence mechanisms.
- Reset compromised credentials.

---

## Recommended Recovery

- Continue monitoring outbound traffic.
- Validate system integrity.
- Review firewall rules.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Exfiltration Over C2 Channel | T1041 |
| Exfiltration Over Alternative Protocol | T1048 |

---

## Lessons Learned

Outbound network activity should always be correlated with endpoint telemetry to determine whether legitimate applications or malicious processes initiated the communication.
