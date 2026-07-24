# Inbound Network Port Scanning Detected

## Overview

This detection identifies inbound network port scanning activity targeting the Windows endpoint. Port scanning is commonly performed during the reconnaissance phase of an attack to discover open ports, exposed services, and potential entry points.

The detection monitors Sysmon network connection events and identifies source hosts generating connections to multiple destination ports within a short period of time.

---

# Detection Objective

Detect reconnaissance activity before an attacker gains initial access.

---

# Attack Phase

**Cyber Kill Chain**

- Reconnaissance

**MITRE ATT&CK**

- TA0043 – Reconnaissance

Potential techniques may include:

- T1595 – Active Scanning

---

# Data Source

Windows Endpoint

Data collected from:

- Sysmon Operational Log

Index:

```
wineventlog
```

---

# SPL Search

> Paste your SPL query here.

```spl
YOUR SPL QUERY
```

---

# Alert Configuration

| Setting | Value |
|----------|-------|
| Alert Type | Scheduled |
| Trigger | (Your configuration) |
| Severity | High |
| Index | wineventlog |

---

# Detection Logic

This detection looks for multiple network connection attempts from a single source IP toward numerous destination ports within a defined time window.

High volumes of connection attempts may indicate reconnaissance activity performed before exploitation.

---

# Validation

Attack performed from:

- Kali Linux

Target:

- Windows 10

Validation steps:

1. Execute a network port scan from Kali Linux.
2. Generate Sysmon network connection events.
3. Confirm events are indexed.
4. Verify the SPL search returns matching results.
5. Confirm the Splunk alert triggers successfully.

---

# Expected Results

The alert should identify:

- Source IP address
- Destination host
- Number of scanned ports
- Timestamp
- Related Sysmon events

---

# Investigation Notes

During an investigation, analysts should:

- Identify the attacking source IP.
- Review additional activity from the source host.
- Determine whether exploitation attempts followed the scan.
- Correlate firewall logs with endpoint telemetry.

---

# Screenshots

To be added:

- SPL Search
- Alert Configuration
- Triggered Alert
- Sysmon Events
- Dashboard Visualization

---

# Lessons Learned

Port scanning is frequently the earliest observable stage of an attack. Detecting reconnaissance activity provides defenders with an opportunity to identify suspicious behavior before an attacker progresses further through the intrusion lifecycle.
