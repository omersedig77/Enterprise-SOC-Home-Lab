# Network Port Scanning Investigation Playbook

## Purpose

Investigate alerts indicating reconnaissance activity through network port scanning.

---

## Detection

### Data Sources

- Sysmon Event ID 3 – Network Connection
- pfSense Firewall Logs
- Splunk Enterprise

### Indicators

- High number of connection attempts
- Sequential destination ports
- Connections to multiple services
- Short time interval between connections

---

## Investigation

### Step 1

Identify:

- Source IP
- Destination IP
- Destination ports

---

### Step 2

Determine:

- Number of ports scanned
- Time window
- Scan pattern

Examples:

- SYN Scan
- TCP Connect Scan
- Service Enumeration

---

### Step 3

Review firewall telemetry for additional activity originating from the same source.

---

### Step 4

Determine whether reconnaissance progressed to exploitation.

Examples:

- Brute-force attacks
- Payload delivery
- Reverse shell activity

---

## Recommended Containment

- Block malicious source IP.
- Increase monitoring for targeted assets.
- Review firewall rules.

---

## Recommended Eradication

- Verify exposed services.
- Disable unnecessary services.
- Apply security patches.

---

## Recommended Recovery

- Continue monitoring network activity.
- Validate that no follow-on attacks occurred.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Active Scanning | T1595 |

---

## Lessons Learned

Reconnaissance is often the first observable stage of an attack. Early detection provides defenders with an opportunity to investigate and prepare before further intrusion attempts occur.
