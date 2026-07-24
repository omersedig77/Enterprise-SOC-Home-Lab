# SOC Dashboard Collection

## Overview

Dashboards are a critical component of this SOC Homelab, providing security analysts with centralized visibility into events, alerts, attack activity, and incident investigations.

The dashboards were designed to simulate different SOC analyst workflows, including threat monitoring, alert triage, incident queue management, and detailed incident investigation.

The dashboard collection combines live telemetry collected from Splunk indexes with structured incident data imported through lookup files to simulate enterprise SOC workflows.

---

# Dashboard Categories

## Threat Monitoring

Dashboards focused on identifying suspicious activity and monitoring active attacks.

Includes:

- Brute Force Monitoring Dashboard

---

## SOC Operations

Dashboards designed for Level 1 SOC analysts to monitor alerts and prioritize investigations.

Includes:

- Enterprise SOC Level 1 Dashboard

---

## Incident Response

Dashboards designed to simulate incident tracking, investigation, evidence review, and response workflows.

Includes:

- Incident Review Dashboard
- Incident Investigation Dashboard

---

# Data Sources

The dashboards use multiple sources of security information:

## Live Splunk Data

Collected from:

- Windows Event Logs
- Sysmon
- Linux Logs
- pfSense Firewall Logs

## Lookup Data

Some incident management dashboards use CSV lookup files to simulate:

- Incident records
- Analyst assignments
- Investigation status
- MITRE ATT&CK mappings
- Recommended response actions

---

# Dashboard Workflow

The dashboards represent a simplified SOC workflow:

```
Security Events
       |
       ↓
Detection Rules
       |
       ↓
    Alerts
       |
       ↓
Incident Queue
       |
       ↓
Investigation
       |
       ↓
Response Actions
```

This workflow demonstrates how security telemetry can be transformed into actionable incidents.
