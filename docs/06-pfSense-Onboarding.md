# pfSense Firewall Data Onboarding

## Overview

The pfSense firewall acts as the network gateway for the SOC Homelab and provides centralized network telemetry. Firewall logs are forwarded to Splunk Enterprise using remote syslog, allowing network events to be correlated with endpoint activity.

This additional data source improves visibility into network communications and supports more comprehensive incident investigations.

---

# Remote Syslog Configuration

Remote logging was enabled within pfSense to forward firewall events to the Splunk Enterprise server.

| Setting | Value |
|----------|-------|
| Destination | 192.168.50.101 |
| Protocol | UDP |
| Port | 514 |

---

# Data Source

Firewall logs are indexed within the dedicated **firewall** index.

These logs provide visibility into:

- Allowed connections
- Blocked traffic
- Firewall rule activity
- Network communication events

---

# Verification

Successful onboarding was verified by confirming that firewall events were searchable within the **firewall** index.

Verification included:

- Firewall log ingestion
- Network traffic events
- Blocked connection events
- Allowed connection events

---

# Security Value

Firewall telemetry complements endpoint logs by providing network-level visibility.

This enables analysts to:

- Investigate suspicious connections
- Correlate endpoint and network events
- Identify unauthorized communication
- Validate attack simulations
- Improve incident investigations

The pfSense firewall serves as the primary network telemetry source within the SOC Homelab.
