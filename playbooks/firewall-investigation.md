# Firewall Investigation Playbook

## Purpose

Investigate suspicious network activity using pfSense firewall telemetry and correlate network events with endpoint logs collected in Splunk Enterprise.

---

## Detection

### Data Sources

- pfSense Firewall Logs
- Sysmon Event ID 3 – Network Connection
- Windows Event Logs
- Linux Logs
- Splunk Enterprise

### Indicators

- Allowed outbound connections
- Blocked inbound connections
- Unusual destination ports
- Unexpected source IPs
- High-volume connection attempts

---

## Investigation

### Step 1

Identify:

- Source IP
- Destination IP
- Source Port
- Destination Port
- Firewall Action (Allowed or Blocked)

---

### Step 2

Review the firewall rule responsible for the action.

Determine:

- Rule name
- Interface
- Protocol
- Direction

---

### Step 3

Correlate firewall events with endpoint telemetry.

Examples:

- Sysmon Event ID 3
- Windows authentication events
- Linux authentication logs
- Process creation events

---

### Step 4

Determine whether the activity is associated with:

- Port scanning
- Brute-force attacks
- Reverse shell communication
- Command-and-control
- Data exfiltration

---

## Recommended Containment

- Block malicious IP addresses.
- Update firewall rules if required.
- Increase monitoring for affected systems.

---

## Recommended Eradication

- Remove malicious access.
- Review firewall policies.
- Validate exposed services.

---

## Recommended Recovery

- Continue monitoring network activity.
- Validate firewall logging.
- Review detection rules.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Active Scanning | T1595 |
| Application Layer Protocol | T1071 |
| Exfiltration Over C2 Channel | T1041 |

---

## Lessons Learned

Firewall telemetry provides valuable network context during investigations. Correlating firewall logs with endpoint telemetry enables analysts to reconstruct attack timelines and better understand attacker behavior.
