# Architecture Overview
## Network Architecture

The following diagram illustrates the overall SOC homelab architecture, including the network topology, monitored endpoints, centralized SIEM deployment, firewall, and adversary simulation host.

![SOC Homelab Architecture](../diagrams/soc-homelab-architecture.png)


## Overview

The SOC Homelab was designed to simulate a small enterprise network where multiple systems generate security telemetry that is centrally collected and analyzed by Splunk Enterprise. The environment includes Windows and Linux endpoints, a firewall responsible for routing network traffic, and an attacker machine used to simulate real-world cyber attacks.

All virtual machines communicate through a pfSense firewall, which serves as the network gateway while also forwarding firewall logs to Splunk Enterprise. Endpoint logs from Windows and Linux are collected using Splunk Universal Forwarders, enabling centralized monitoring, detection engineering, and incident investigation.

The architecture provides a realistic environment for validating security detections, testing attack scenarios, and developing SOC analyst skills.

### Components
Splunk Enterprise Server
Operating System: Ubuntu Desktop
Role: Centralized Security Information and Event Management (SIEM)

Responsibilities:
Collect logs from all monitored systems.
Index and search security events.
Generate alerts.
Display dashboards.
Support incident investigations.

#### Windows Endpoint
Operating System: Windows 10
Role: Monitored endpoint
Log Collection:
Windows Event Logs
Splunk Universal Forwarder
This system is used to simulate attacks against Windows and validate custom detections.

#### Linux Endpoint
Operating System: Ubuntu Desktop
Role: Monitored endpoint
Log Collection:
Linux system logs
Splunk Universal Forwarder
This machine is used to simulate attacks targeting Linux systems and validate detection logic.

#### Kali Linux
Role:

Attack simulation platform.

Responsibilities:

Simulate adversary behavior.
Execute attack scenarios.
Test detection rules.
Generate realistic security events.
pfSense Firewall
Role:

#### Network gateway and firewall.

Responsibilities:

Route traffic between virtual machines.
Monitor network connections.
Forward firewall logs to Splunk Enterprise.
Provide additional network visibility during investigations.
Log Flow
Windows 10
↓
Splunk Universal Forwarder
↓
Splunk Enterprise

Ubuntu Desktop
↓
Splunk Universal Forwarder
↓
Splunk Enterprise

pfSense Firewall
↓
Syslog
↓
Splunk Enterprise

All collected telemetry is indexed within Splunk Enterprise, where custom SPL detections, alerts, and dashboards provide centralized security monitoring.
