# Incident Review Dashboard

## Overview

The Incident Review Dashboard simulates a SOC Level 1 incident queue management system.

The dashboard is designed to help analysts monitor, prioritize, and track security incidents after detection alerts have been generated.

Unlike the threat monitoring dashboards, which focus on security telemetry, this dashboard focuses on the operational workflow of managing incidents.

---

# Purpose

The primary objectives of this dashboard are:

- Monitor active incidents.
- Prioritize incidents based on severity.
- Track investigation status.
- Monitor analyst workload.
- Identify the most common detection sources.
- Support incident triage and escalation.

---

# Data Source

This dashboard uses structured incident data imported through Splunk lookup files.

The lookup-based approach was used to simulate an enterprise SOC case management workflow, including:

- Incident records
- Severity classification
- Investigation status
- Analyst assignment
- Detection rule information

The incident data is connected to the Splunk dashboard using lookup functionality rather than direct Universal Forwarder ingestion.

---

# Dashboard Workflow

The dashboard represents the incident lifecycle:

```
Detection Alert
       |
       ↓
Incident Created
       |
       ↓
SOC Queue Review
       |
       ↓
Analyst Assignment
       |
       ↓
Investigation
       |
       ↓
Response / Closure
```

---

# Dashboard Visualizations

## Incident Summary

### Total Incidents

Displays the total number of incidents currently tracked in the SOC environment.

Security value:

- Provides overall incident workload visibility.
- Helps analysts understand active case volume.

---

### Critical Incidents

Displays the number of incidents classified as critical severity.

Security value:

- Allows prioritization of urgent investigations.
- Supports escalation decisions.

---

### Open Incidents

Displays incidents that are currently active and require investigation.

Security value:

- Shows unresolved security cases.
- Helps analysts prioritize workload.

---

### SLA Incidents

Displays incidents requiring attention based on service-level agreement requirements.

Security value:

- Helps track response timelines.
- Supports operational performance monitoring.

---

# Incident Distribution Analysis

## Incidents by Severity

Bar visualization showing incident distribution across severity levels.

Categories include:

- Critical
- High
- Medium
- Low

Security value:

Helps identify the overall risk level of current incidents.

---

## Incident Status Distribution

Shows incidents grouped by investigation status.

Examples:

- Open
- In Progress
- Closed
- Escalated

Security value:

Provides visibility into incident handling progress.

---

## Analyst Workload

Shows incident assignment distribution across analysts.

Security value:

Helps identify:

- Analyst workload balance
- Resource requirements
- Investigation ownership

---

## Top Detection Rules

Displays the detection rules generating the highest number of incidents.

Security value:

Helps identify:

- Common attack patterns
- Frequently triggered detections
- Detection tuning opportunities

---

# Incident Queue

## INCIDENT QUEUE Table

The incident queue provides detailed visibility into individual cases.

Example information includes:

- Incident ID
- Severity
- Status
- Assigned analyst
- Detection source
- Priority
- Creation time

Security value:

Allows analysts to select incidents for investigation and track case progression.

---

# Analyst Workflow

A SOC analyst can use this dashboard to:

1. Review newly created incidents.
2. Prioritize critical and high-severity cases.
3. Assign or review analyst ownership.
4. Identify the detection generating the alert.
5. Open the related investigation dashboard.
6. Document findings and response actions.

---

# Security Operations Value

This dashboard demonstrates how security alerts can be transformed into managed incidents.

It simulates the operational side of a SOC by connecting:

- Detection engineering
- Alert management
- Analyst workflow
- Incident investigation

---

# Screenshots

To be added:

- Full incident review dashboard
- Incident severity panels
- Incident queue table
- Analyst workload visualization
- Detection rule distribution
