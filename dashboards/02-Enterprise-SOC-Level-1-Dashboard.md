# Enterprise SOC Level 1 Dashboard

## Overview

The Enterprise SOC Level 1 Dashboard provides a centralized operational view of the security environment.

The dashboard is designed to simulate the primary monitoring interface used by a Tier 1 Security Operations Center analyst during daily operations.

It provides visibility into:

- Security alert severity
- Event volume
- Endpoint availability
- Attack trends
- Targeted systems

This dashboard allows analysts to quickly assess the current security posture and identify areas requiring further investigation.

---

# Purpose

The primary goals of this dashboard are:

- Provide a high-level SOC overview.
- Monitor active security alerts.
- Prioritize investigations based on severity.
- Monitor endpoint visibility.
- Identify attack activity trends.
- Support initial alert triage.

---

# Data Sources

The dashboard uses security telemetry collected from:

## Windows

Sources:

- Windows Security Events
- Windows System Events
- Sysmon Operational Logs

Index:

```
wineventlog
```

---

## Linux

Sources:

- Authentication logs
- System logs
- Kernel logs

Index:

```
linux_logs
```

---

## Firewall

Source:

- pfSense Firewall Logs

Index:

```
firewall
```

---

# Dashboard Visualizations

## Security Alert Summary

### Critical Alerts

Displays the number of active critical severity alerts.

Security value:

- Allows analysts to immediately identify high-priority security events.
- Supports rapid escalation and investigation.

---

### High Alerts

Displays the number of high-severity alerts requiring analyst attention.

---

### Medium Alerts

Displays medium-priority detections that require review.

---

### Low Alerts

Displays low-priority security events and informational activity.

---

# Endpoint Visibility

## Windows Hosts

Displays the number of active Windows endpoints reporting telemetry.

Security value:

- Confirms endpoint visibility.
- Helps identify missing telemetry sources.

---

## Linux Hosts

Displays the number of active Linux endpoints reporting telemetry.

Security value:

- Confirms Linux monitoring status.
- Identifies potential logging gaps.

---

# Event Monitoring

## Total Events

Displays the total amount of security telemetry received by Splunk.

Security value:

Helps analysts identify:

- Normal activity levels.
- Sudden increases in event volume.
- Potential logging issues.

---

# Attack Analysis

## Top Target Hosts

Bar visualization showing systems receiving the highest amount of security activity.

Security value:

Helps analysts identify:

- Frequently targeted endpoints.
- Potential compromised systems.
- Systems requiring additional investigation.

---

## Attack Trend 24 Hours

Line visualization displaying attack activity over the previous 24 hours.

Security value:

Allows analysts to identify:

- Attack spikes.
- Active campaigns.
- Unusual security activity patterns.

---

# Analyst Workflow

A Tier 1 analyst can use this dashboard workflow:

1. Review critical and high-severity alerts.
2. Confirm endpoint visibility.
3. Identify abnormal event volume.
4. Review attack trends.
5. Identify targeted hosts.
6. Pivot into specific investigation dashboards.
7. Escalate confirmed incidents.

---

# SOC Operational Value

This dashboard simulates a real SOC monitoring workflow by providing analysts with a centralized view of security operations.

It demonstrates the transition from:

```
Raw Security Events
        |
        ↓
Detection Alerts
        |
        ↓
SOC Analyst Review
        |
        ↓
  Investigation
```

---

# Screenshots

To be added:

- Full SOC Level 1 dashboard view
- Alert severity panels
- Attack trend visualization
- Host visibility panels
- Analyst monitoring workflow example
