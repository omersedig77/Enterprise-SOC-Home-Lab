# Brute Force Monitoring Dashboard

## Overview

The Brute Force Monitoring Dashboard provides SOC analysts with centralized visibility into authentication attacks targeting Windows and Linux endpoints.

The dashboard is designed to identify password-based attacks, analyze authentication patterns, and support investigation of successful compromises following repeated failed login attempts.

The dashboard supports monitoring of multiple authentication protocols, including:

- Remote Desktop Protocol (RDP)
- Secure Shell (SSH)

---

# Purpose

The primary purpose of this dashboard is to help SOC analysts:

- Identify brute-force attack activity.
- Detect successful authentication after repeated failures.
- Investigate targeted accounts and systems.
- Identify attacking source IP addresses.
- Analyze authentication attack trends.

---

# Data Sources

The dashboard uses authentication telemetry collected from:

## Windows

Sources include:

- Windows Security Event Logs
- Successful logon events
- Failed authentication events

Examples:

- Event ID 4624 — Successful Logon
- Event ID 4625 — Failed Logon

Indexed under:

```
wineventlog
```

---

## Linux

Sources include:

- Linux authentication logs

Example:

```
/var/log/auth.log
```

Indexed under:

```
linux_logs
```

---

# Dashboard Filters

The dashboard provides interactive filtering capabilities.

## Global Time Range

Allows analysts to investigate authentication activity during a specific timeframe.

---

## Protocol

Filters authentication activity by protocol.

Available protocols:

- RDP
- SSH

---

## Source IP

Allows analysts to investigate activity originating from a specific attacking host.

Useful for identifying:

- Internal attackers
- Compromised systems
- Repeated attack sources

---

## Username

Filters events targeting specific user accounts.

Useful for identifying:

- Targeted accounts
- Privileged accounts
- Compromised credentials

---

## Destination Host

Filters authentication activity against specific endpoints.

Useful for identifying:

- Targeted machines
- Potentially compromised systems

---

# Dashboard Visualizations

## Authentication Summary

### Failed Login Attempts

Displays the total number of failed authentication attempts.

Security value:

- Identifies brute-force activity
- Highlights abnormal authentication failures

---

### Successful Logins

Displays successful authentication events.

Security value:

- Helps identify possible account compromise
- Supports investigation after brute-force activity

---

### Unique Source IPs

Displays the number of unique systems generating authentication attempts.

Security value:

- Identifies distributed attacks
- Highlights suspicious sources

---

### Targeted Users

Displays user accounts receiving authentication attempts.

Security value:

- Identifies targeted accounts
- Helps prioritize investigations

---

### Targeted Hosts

Displays systems receiving authentication attempts.

Security value:

- Identifies affected endpoints

---

# Authentication Trends

## Login Attempts Over Time

Line visualization showing authentication activity trends.

Security value:

Allows analysts to identify:

- Attack duration
- Attack spikes
- Abnormal authentication patterns

---

# Attack Source Analysis

## Top Source IPs

Shows the most active sources generating authentication attempts.

Security value:

Helps identify:

- Primary attacking systems
- Suspicious internal hosts
- Repeated attack sources

---

## Top Targeted Users

Shows the most frequently targeted accounts.

Security value:

Helps identify:

- High-value accounts
- Credential attack targets

---

## Top Targeted Hosts

Shows the endpoints receiving the highest number of authentication attempts.

---

## Brute Force Protocol

Displays attack activity grouped by authentication protocol.

Supported protocols:

- RDP
- SSH

Security value:

Allows analysts to determine whether the attack is targeting Windows remote access or Linux remote access services.

---

# Investigation Tables

## Successful Login After Multiple Failures

This table highlights potential successful compromises where authentication succeeded after multiple failed attempts.

Security value:

Helps identify:

- Valid credential compromise
- Account takeover attempts
- Successful brute-force attacks

---

## Authentication Events

Provides detailed authentication event information for analyst investigation.

Information may include:

- Timestamp
- Username
- Source IP
- Destination host
- Protocol
- Authentication result

---

# Analyst Workflow

A SOC analyst can use this dashboard workflow:

1. Review failed authentication volume.
2. Identify suspicious source IP addresses.
3. Filter by protocol.
4. Identify targeted accounts and hosts.
5. Investigate successful authentication after failures.
6. Pivot into raw authentication events.
7. Determine whether additional response actions are required.

---

# Security Value

This dashboard provides cross-platform visibility into credential-based attacks and supports early detection of account compromise attempts.

By correlating failed and successful authentication events, analysts can identify attacks that would otherwise appear as isolated login failures.

---

# Screenshots

To be added:

- Full dashboard view
- Protocol filter selection
- Successful login after multiple failures panel
- Authentication event details
- Investigation workflow example
