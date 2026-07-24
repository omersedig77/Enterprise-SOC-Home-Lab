# Incident Investigation Dashboard

## Overview

The Incident Investigation Dashboard simulates a SOC analyst investigation workspace used after an incident has been selected from the incident queue.

The dashboard provides analysts with a centralized view of incident metadata, related security events, evidence, MITRE ATT&CK mappings, and recommended response actions.

The goal of this dashboard is to support structured incident analysis and reduce the time required to understand the scope and impact of a security event.

---

# Purpose

The primary objectives of this dashboard are:

- Investigate individual incidents.
- Review incident context.
- Analyze related security events.
- Correlate evidence.
- Identify affected systems.
- Map activity to MITRE ATT&CK techniques.
- Provide recommended response actions.

---

# Data Source

This dashboard uses structured incident data imported through Splunk lookup files.

The lookup data simulates enterprise SOC case management information, including:

- Incident metadata
- Severity
- Priority
- Status
- Detection information
- Analyst assignment
- MITRE ATT&CK mapping
- Response recommendations

Additional event information is retrieved from Splunk indexes containing security telemetry.

---

# Incident Selection

## Incident ID Filter

The dashboard uses an Incident ID filter to allow analysts to select a specific case.

Once an incident is selected, the dashboard dynamically displays related information.

This simulates opening an individual ticket or case inside a SOC platform.

---

# Incident Summary

## Incident ID

Displays the unique identifier of the selected incident.

Purpose:

- Provides case tracking reference.
- Allows correlation between dashboard information and incident records.

---

## Severity

Displays the risk classification of the incident.

Examples:

- Critical
- High
- Medium
- Low

Security value:

Allows analysts to understand investigation priority.

---

## Status

Displays the current investigation state.

Examples:

- Open
- Investigating
- Closed

Security value:

Provides visibility into incident progress.

---

## Priority

Displays the assigned investigation priority.

Security value:

Helps analysts determine response urgency.

---

# Investigation Details

## Incident Details Table

Provides contextual information about the selected incident.

Possible fields include:

- Incident description
- Detection source
- Affected host
- Source IP
- Username
- Analyst assignment
- Timestamp

Security value:

Provides initial investigation context.

---

## Related Events Table

Displays security events associated with the incident.

Potential sources include:

- Windows Event Logs
- Sysmon
- Linux Logs
- pfSense Firewall Logs

Security value:

Allows analysts to review the timeline of activity.

---

## Related Events & Evidence

Displays supporting evidence collected during investigation.

Examples:

- Authentication events
- Process execution
- Network connections
- Firewall activity
- Suspicious commands

Security value:

Provides the evidence required to confirm or dismiss an incident.

---

# MITRE ATT&CK Mapping

## MITRE ATT&CK Mapping Table

Displays the techniques associated with the incident.

Examples:

- Initial Access
- Execution
- Persistence
- Credential Access
- Command and Control

Security value:

Provides attacker behavior context and improves threat understanding.

---

# Raw Event Analysis

## Raw Events Visualization

Displays the original security events associated with the incident.

Purpose:

Allows analysts to:

- Validate detection accuracy.
- Review event details.
- Perform timeline analysis.
- Identify additional indicators of compromise.

---

# Analyst Workload

## Analyst Workload Table

Displays analyst ownership and investigation assignment.

Security value:

Supports:

- Case ownership tracking.
- Workload visibility.
- Operational management.

---

# Recommended Response

## Response Recommendation Table

Provides suggested actions based on the incident type.

Examples:

- Isolate affected host.
- Reset compromised credentials.
- Block malicious IP address.
- Remove persistence mechanism.
- Collect additional forensic evidence.

Security value:

Helps analysts follow consistent response procedures.

---

# Investigation Workflow

A SOC analyst can use this dashboard workflow:

1. Select an Incident ID.
2. Review incident severity and priority.
3. Analyze related events.
4. Review supporting evidence.
5. Identify MITRE ATT&CK techniques.
6. Determine impact.
7. Follow recommended response actions.
8. Document investigation findings.

---

# SOC Value

This dashboard demonstrates the transition from detection to investigation.

It combines:

- Detection engineering
- Security telemetry
- Threat analysis
- Incident handling
- Response planning

The dashboard simulates how analysts investigate security incidents after an alert has been generated.

---

# Screenshots

To be added:

- Incident ID filter
- Incident summary panels
- Related events table
- MITRE ATT&CK mapping
- Raw event investigation
- Recommended response section
