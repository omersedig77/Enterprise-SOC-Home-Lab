# Windows Data Onboarding

## Overview

The Windows 10 endpoint was configured to forward security telemetry to Splunk Enterprise using the Splunk Universal Forwarder. In addition to native Windows Event Logs, Sysmon was deployed to provide enhanced endpoint visibility for process creation, network connections, registry modifications, and other security-relevant events.

This combination enables comprehensive monitoring of Windows activity and supports the development of custom detections, alerts, and investigations.

---

# Splunk Universal Forwarder Installation

The Splunk Universal Forwarder was installed using the official Windows MSI installer.

During installation:

- The Universal Forwarder was installed as a Windows Service.
- The forwarder was configured to start automatically during system boot.
- The deployment was configured manually without using a Splunk Deployment Server.

---

# Forwarding Configuration

The Universal Forwarder forwards all configured data to the Splunk Enterprise server.

| Setting | Value |
|----------|-------|
| Destination | 192.168.50.101 |
| Receiving Port | 9997 |

---

# Windows Data Sources

The following Windows Event Logs are collected.

## Security

Provides visibility into:

- User logons
- Failed authentication attempts
- Account management
- Privilege usage
- Audit events

---

## System

Provides visibility into:

- Driver events
- Service activity
- System startup
- System shutdown
- Hardware events

---

## Application

Provides visibility into:

- Application errors
- Software crashes
- Application warnings
- Service events

---

# Sysmon

Sysmon was installed using the **SwiftOnSecurity Sysmon configuration**, providing enhanced endpoint telemetry beyond the default Windows Event Logs.

The Sysmon Operational log is forwarded to Splunk Enterprise.

Examples of monitored activity include:

- Process creation
- Network connections
- Image loads
- File creation
- Registry modifications
- Driver loading
- DNS queries
- Process injection
- PowerShell activity

---

# Verification

Successful onboarding was verified by confirming that events from all configured Windows data sources were searchable within the **wineventlog** index.

Verification included:

- Windows Event Logs successfully indexed
- Sysmon Operational events received
- Continuous log ingestion confirmed

---

# Security Value

The Windows endpoint provides detailed host-based telemetry used to:

- Detect brute-force attacks
- Monitor PowerShell activity
- Detect privilege escalation
- Identify persistence techniques
- Investigate malware execution
- Validate custom SPL detections

This telemetry serves as one of the primary data sources for attack simulations and incident investigations performed throughout the project.
