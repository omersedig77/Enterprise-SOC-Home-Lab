# Command and Control (C2) Investigation Playbook

## Purpose

Investigate outbound communications that may indicate an endpoint is communicating with an attacker-controlled command-and-control (C2) server.

---

## Detection

### Data Sources

- Sysmon Event ID 3
- pfSense Firewall Logs
- Splunk Enterprise

### Indicators

- Repeated outbound connections
- Unusual destination IPs
- Connections initiated by uncommon processes
- Beaconing behavior
- Reverse shell communication

---

## Investigation

### Step 1

Identify:

- Source host
- Destination IP
- Destination port
- Initiating process

---

### Step 2

Review:

- Process creation
- Parent process
- Command-line arguments
- User context

---

### Step 3

Correlate:

- Firewall logs
- Authentication events
- Process execution
- Reverse shell activity

---

### Step 4

Determine whether the communication supports:

- Remote control
- Payload delivery
- Data exfiltration

---

## Recommended Containment

- Isolate the endpoint.
- Block the destination IP.
- Terminate malicious processes.

---

## Recommended Eradication

- Remove malware.
- Remove persistence.
- Rotate compromised credentials.

---

## Recommended Recovery

- Continue monitoring for recurring outbound connections.
- Validate endpoint integrity.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Application Layer Protocol | T1071 |
| Non-Application Layer Protocol | T1095 |

---

## Lessons Learned

Outbound communications should always be investigated alongside endpoint telemetry to determine whether they represent legitimate traffic or attacker command-and-control activity.
