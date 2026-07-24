# Lab Setup

## Overview

The SOC Homelab was built using VMware Workstation to simulate a small enterprise environment consisting of Windows, Linux, firewall, and attacker systems. The environment is designed to provide realistic security telemetry for monitoring, detection engineering, attack simulation, and incident response.

Each virtual machine performs a specific role within the environment while communicating through a centralized pfSense firewall. Security events generated throughout the lab are forwarded to Splunk Enterprise for analysis.

---

# Virtualization Platform

The entire lab is hosted on **VMware Workstation** using custom virtual networks to isolate the environment from the host network while maintaining controlled connectivity.

## Virtual Networks

| Network | Purpose |
|----------|---------|
| VMnet2 | Internal SOC lab network |
| VMnet8 | NAT network used for Internet connectivity when required |

All lab virtual machines communicate over **VMnet2**, allowing network traffic to traverse the pfSense firewall before reaching other systems.

---

# Network Configuration

## WAN Interface

| Setting | Value |
|----------|-------|
| Network | VMnet8 |
| Address | 192.168.159.0/24 |

The WAN interface provides external network connectivity.

---

## LAN Interface

| Setting | Value |
|----------|-------|
| Network | VMnet2 |
| Gateway | 192.168.50.1 |

The LAN interface serves as the internal enterprise network where all monitored systems reside.

---

# Virtual Machines

## Splunk Enterprise Server

| Property | Value |
|----------|-------|
| Operating System | Ubuntu Desktop 22.04 |
| IP Address | 192.168.50.101 |
| Role | Centralized SIEM |

Responsibilities:

- Collect endpoint logs
- Index security events
- Generate alerts
- Host dashboards
- Support incident investigations

---

## Windows Endpoint

| Property | Value |
|----------|-------|
| Operating System | Windows 10 |
| IP Address | 192.168.50.20 |
| Role | Monitored Windows endpoint |

Responsibilities:

- Generate Windows security events
- Forward logs using Splunk Universal Forwarder
- Serve as a Windows attack target

---

## Linux Endpoint

| Property | Value |
|----------|-------|
| Operating System | Ubuntu Desktop 22.04 |
| IP Address | 192.168.50.30 |
| Role | Monitored Linux endpoint |

Responsibilities:

- Generate Linux system logs
- Forward logs using Splunk Universal Forwarder
- Serve as a Linux attack target

---

## Kali Linux

| Property | Value |
|----------|-------|
| Operating System | Kali Linux |
| IP Address | 192.168.50.40 |
| Role | Adversary Simulation Host |

Responsibilities:

- Simulate attacker behavior
- Execute attack scenarios
- Validate security detections
- Generate realistic attack telemetry

---

## pfSense Firewall

| Property | Value |
|----------|-------|
| LAN Address | 192.168.50.1 |
| Role | Firewall and Network Gateway |

Responsibilities:

- Route traffic between systems
- Monitor network communications
- Forward firewall logs to Splunk Enterprise
- Provide centralized network visibility

---

## Suricata IDS/IPS

Suricata is deployed alongside pfSense to provide network intrusion detection and prevention capabilities.

At the current stage of the project, Suricata is operating independently and is **not yet integrated with Splunk Enterprise**. Future enhancements will include forwarding Suricata alerts to Splunk for centralized network threat monitoring.

---

# Lab Design Goals

The lab was designed to simulate a realistic SOC environment capable of:

- Centralized log collection
- Security monitoring
- Detection engineering
- Dashboard development
- Windows attack simulations
- Linux attack simulations
- Phishing simulations
- Incident investigation
- Threat detection validation

This architecture provides a flexible environment for developing and testing blue team capabilities using realistic attack scenarios.
