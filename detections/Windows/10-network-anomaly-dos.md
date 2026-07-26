# High-Volume Network Anomaly - Possible DoS Activity

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Network Anomaly |
| Platform | Windows |
| Data Source | Sysmon |
| Event ID | Event ID 3 - Network Connection |
| Severity | High |
| MITRE ATT&CK | T1498 - Network Denial of Service |
| Related Technique | T1046 - Network Service Scanning |
| Schedule | Every 5 minutes |
| Alert Type | Threshold-Based Detection |
| Status | Validated |

---

# Overview

This detection identifies abnormal volumes of network connections targeting a Windows endpoint.

A sudden increase in completed network connections from a single source may indicate:

- Connection flooding
- Port scanning activity
- Denial of Service attempts
- Automated reconnaissance
- Network abuse

The detection focuses on connection volume rather than individual packets, allowing analysts to identify unusual network behavior.

---

# Detection Objective

The purpose of this detection is to identify:

- High-frequency connection attempts
- Network resource exhaustion attempts
- Automated scanning activity
- Suspicious traffic patterns

The detection provides visibility into:

- Source IP
- Destination IP
- Target host
- Destination port
- Connection volume

---

# Attack Scenario

During security testing, abnormal network activity was simulated to validate network visibility.

Attack flow:

```
External Source

        |

High Volume Connection Attempts

        |

Windows Endpoint Receives Traffic

        |

Sysmon Network Events Generated

        |

Splunk Alert Triggered

        |

SOC Investigation Started
```

---

# Data Sources

## Sysmon Event ID 3

Provides visibility into:

- Network connections
- Source address
- Destination address
- Destination port
- Connection activity

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
index=wineventlog sourcetype="XmlWinEventLog" "<EventID>3</EventID>"
| rex field=_raw "Name='SourceIp'>(?<Remote_IP>[^<]+)"
| rex field=_raw "Name='DestinationIp'>(?<Windows_IP>[^<]+)"
| rex field=_raw "Name='DestinationPort'>(?<Target_Port>[^<]+)"
| stats count as Total_Packets by Computer, Remote_IP, Windows_IP, Target_Port
| sort - Total_Packets
| rename Computer as "Target Host"
| table "Target Host", "Remote_IP", "Windows_IP", "Target_Port", Total_Packets
```

---

# Detection Logic Explanation

## 1. Monitor Network Connections

The detection analyzes:

```
Sysmon Event ID 3
```

which records network communication from monitored endpoints.

---

## 2. Count Connection Volume

The query aggregates:

```
Source IP

Destination IP

Destination Port

Target Host
```

and counts the number of connections.

---

## 3. Identify Abnormal Activity

A high number of connections from a single source may indicate:

- Automated scanning
- Connection flooding
- Denial of Service behavior

---

# Alert Configuration

## Severity

```
High
```

---

## Trigger Condition

The alert triggers when a remote source generates an unusually high number of established connections against a single Windows host within a short time period.

Example:

```
Source IP:

192.168.50.40


Target Host:

192.168.50.20


Connections:

500+ events within 5 minutes
```

This activity requires investigation for possible network abuse.

---

## Schedule

```
Every 5 minutes
```

---

# Investigation Guidance

When this alert triggers:

---

# 1. Identify Source System

Review:

```
Remote_IP
```

Determine:

- Internal or external source
- Known asset ownership
- Previous activity

---

# 2. Analyze Destination Port

Review:

```
Target_Port
```

Determine:

- Targeted service
- Expected traffic
- Possible scanning behavior

Examples:

```
22     SSH

3389   RDP

445    SMB

80     HTTP
```

---

# 3. Correlate Firewall Logs

Review pfSense telemetry:

```
firewall index
```

Investigate:

- Allowed connections
- Blocked connections
- Source address
- Destination address
- Firewall rule

---

# 4. Review Endpoint Impact

Determine:

- CPU utilization
- Memory usage
- Service availability
- Application degradation

---

# Incident Response Actions

Recommended actions:

- Identify attacking source
- Block malicious IP if required
- Review firewall rules
- Isolate affected endpoint if compromise is suspected
- Capture network evidence
- Investigate related alerts

---

# False Positives

Possible legitimate activity:

- Vulnerability scanners
- Monitoring systems
- Backup applications
- Software deployment tools
- Internal testing

Validation should consider:

- Source ownership
- Time of activity
- Expected business operations
- Connection purpose

---

# Validation

This detection was validated during SOC Homelab testing.

Simulation:

```
Traffic Generation

        |

Network Events Created

        |

Sysmon Event ID 3 Collected

        |

Splunk Detection Triggered
```

Result:

```
Detection Triggered Successfully
```

---

# Related Playbooks

```
incident-response/playbooks/firewall-investigation.md

incident-response/playbooks/c2-investigation.md

incident-response/playbooks/network-investigation.md
```

---

# Lessons Learned

Network-based anomaly detection provides additional visibility beyond endpoint telemetry.

By correlating Sysmon network events with firewall logs, SOC analysts can identify suspicious traffic patterns and determine whether activity represents reconnaissance, abuse, or active compromise.
