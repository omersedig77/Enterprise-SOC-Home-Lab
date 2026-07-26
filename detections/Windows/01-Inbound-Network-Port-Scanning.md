# Inbound Network Port Scanning Detected

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Network Reconnaissance |
| Platform | Windows |
| Data Source | Windows Security Event Logs |
| Event ID | 5156 - Windows Filtering Platform Connection Allowed |
| Severity | Medium |
| MITRE ATT&CK | T1595 - Active Scanning |
| Schedule | Every 5 minutes |
| Alert Type | Scheduled Detection |
| Status | Validated |

---

# Overview

This detection identifies possible network reconnaissance activity against Windows endpoints by detecting a single source IP address connecting to a large number of unique destination ports.

Port scanning is commonly performed during the reconnaissance phase of an attack lifecycle to identify exposed services, running applications, and potential attack surfaces.

This detection was validated using Nmap scans from the Kali Linux attacker machine against the Windows endpoint.

---

# Detection Objective

The objective of this detection is to identify:

- Internal reconnaissance activity
- External scanning attempts
- Nmap scans
- Automated port sweep activity
- Unauthorized service discovery

---

# Attack Scenario

During testing, the attacker performed a full TCP port scan against the Windows endpoint.

Attacker:

```
Kali Linux
192.168.50.40
```

Target:
```
Windows 10
192.168.50.20
```

Example attack command:

```bash
sudo nmap -sS -sV -O -p- 192.168.50.20
```
The scan generated multiple Windows Filtering Platform events which were collected by Splunk.

### Data Source

Windows Event Log:

```
EventCode=5156
```
Event 5156 records allowed network connections through the Windows Filtering Platform.

### Collected through:
```
Splunk Universal Forwarder
        |
        |
  Splunk Enterprise
```

### Index:
```
wineventlog
```

## SPL Detection
```
index=wineventlog EventCode=5156

| stats min(_time) as start_raw max(_time) as end_raw dc(Source_Port) as unique_ports count by Source_Address, Destination_Address

| eval "Scan Duration (Sec)" = end_raw - start_raw

| eval scan_start=strftime(start_raw, "%Y-%m-%d %H:%M:%S")

| eval scan_end=strftime(end_raw, "%Y-%m-%d %H:%M:%S")

| where unique_ports > 30

| rename Source_Address as "Attacker IP", Destination_Address as "Victim IP", unique_ports as "Total Ports Hit"

| table "Attacker IP", "Victim IP", scan_start, scan_end, "Scan Duration (Sec)", "Total Ports Hit"
```

#### Detection Logic Explanation
The detection works by:

Searching Windows Filtering Platform connection events.

#### Grouping events by:

Source IP
Destination IP
Counting unique source ports contacted.

Identifying hosts that communicate across more than 30 unique ports.

A high number of unique ports accessed within a short timeframe is characteristic of automated reconnaissance tools such as Nmap.

#### Alert Configuration
Severity
```
Medium
```

#### Trigger Condition
The alert triggers when an internal or external IP address communicates with more than 30 unique ports against a Windows host.

This behavior indicates possible active reconnaissance or port sweep activity.

#### Schedule
```
Every 5 minutes
```
### Investigation Guidance
When this alert triggers, analysts should investigate:

#### 1. Identify the Source
Review:

Attacker IP

Determine whether the source is:

Known administrative infrastructure
Security scanner
Unauthorized host

#### 2. Review Targeted Services
Investigate:

Which ports were accessed
Whether exposed services exist
Whether exploitation followed reconnaissance

#### 3. Correlate With Other Telemetry
Review:

##### Windows:

Event ID 4624 successful logons
Event ID 4625 failed logons
Sysmon process creation events

##### Firewall:

Allowed connections
Blocked connections

#### 4. Determine Attack Progression
Check if reconnaissance was followed by:

Brute force attempts
Malware execution
Suspicious PowerShell activity
Reverse shell activity
False Positives
Possible legitimate sources include:

Vulnerability scanners
IT monitoring tools
Asset discovery systems
Security assessment activities
The source IP should always be validated before escalation.

Validation
This detection was validated during the Windows Attack Simulation.

Attack technique:

T1595 - Active Scanning


Testing:
```
    Kali Linux
        |
        |
   Nmap TCP Scan
        |
        |
  Windows Endpoint
        |
        |
  Splunk Detection
```

Result:

Detection Triggered Successfully

Related Playbooks
Related incident response procedures:

incident-response/playbooks/network-reconnaissance.md
incident-response/playbooks/firewall-investigation.md

Lessons Learned
This detection demonstrates the importance of monitoring reconnaissance activity before exploitation occurs.

Early identification of scanning behavior allows SOC analysts to investigate attacker activity before credential attacks, exploitation attempts, or persistence mechanisms are deployed.






















