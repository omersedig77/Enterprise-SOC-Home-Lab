# 🛡️ SOC Homelab: Building and Operating a Security Operations Center with Splunk Enterprise

## 📌 Project Overview

This project documents the design, implementation, and operation of a Security Operations Center (SOC) home lab built to simulate an enterprise security monitoring environment. The lab provides hands-on experience with Security Information and Event Management (SIEM), detection engineering, threat simulation, alerting, dashboard development, and incident investigation across both Windows and Linux systems.

The environment centralizes security telemetry in **Splunk Enterprise**, where logs from multiple endpoints and network devices are collected, analyzed, and correlated to detect malicious activity. A dedicated **Kali Linux** machine is used to simulate real-world attacks, allowing detections to be validated against realistic adversary behavior.

The primary objective of this project is not only to deploy a SIEM solution, but also to demonstrate the complete SOC workflow—from log collection and monitoring to detection development, attack simulation, alert generation, and incident response.

---

## 🎯 Project Objectives

* **Architecture & Deployment:** Design and deploy a functional SOC home lab using Splunk Enterprise.
* **Telemetry Centralization:** Collect and ingest security logs from Windows, Linux, and pfSense.
* **Forwarder Configuration:** Configure Splunk Universal Forwarders for seamless endpoint log streaming.
* **Detection Engineering:** Develop custom Search Processing Language (SPL) queries for active threat detection.
* **Alerting Mechanism:** Convert verified SPL searches into automated, real-time security alerts.
* **Continuous Monitoring:** Build interactive, custom dashboards for operational security visibility.
* **Adversary Emulation:** Simulate realistic cyber attacks against Windows and Linux target environments.
* **Validation & Investigation:** Validate detections against attack scenarios and investigate security events using endpoint and firewall telemetry.
* **Documentation:** Thoroughly document the complete deployment, setup, and operational workflow.

---

## 🖥️ Lab Environment

| Component | OS / Software | Role |
| :--- | :--- | :--- |
| **SIEM Server** | Ubuntu Desktop | Central Splunk Enterprise Server (Indexing & Searching) |
| **Windows Endpoint** | Windows 10 | Monitored target endpoint running Splunk Universal Forwarder |
| **Linux Endpoint** | Ubuntu Desktop | Monitored target endpoint running Splunk Universal Forwarder |
| **Attacker Platform** | Kali Linux | Attack platform used to simulate adversary TTPs |
| **Perimeter / Gateway** | pfSense | Firewall, network gateway, and syslog provider |

---

## ⚡ Key Capabilities

* 📥 **Centralized Log Collection:** Unified visibility across endpoints and network devices.
* 🪟 **Windows Event Monitoring:** Deep visibility into system, security, and process activity.
* 🐧 **Linux Log Monitoring:** Tracking authentication, execution, and system logs.
* 🧱 **Firewall Log Analysis:** Ingesting and parsing pfSense network traffic logs.
* 🔍 **Custom SPL Detections:** Tailored detection logic written for high-fidelity alerts.
* 🚨 **Real-Time Security Alerts:** Instant notification triggers based on threshold and behavioral rules.
* 📊 **Interactive Dashboards:** Custom SOC views for threat hunting and operational metrics.
* ⚔️ **Multi-OS Attack Simulations:** Executing attack chains against Windows and Linux environments.
* 🎣 **Phishing Attack Emulation:** End-to-end simulation and detection of phishing vectors.
* 🕵️‍♂️ **Incident Investigation:** Root-cause analysis leveraging raw event telemetry.
* ✅ **Detection Validation:** Testing, tuning, and verifying detection effectiveness.

---

🔄 Project Status

[NOTE]
This project is actively being expanded. Upcoming updates include additional detection rules, expanded attack scenarios, custom dashboard layouts, detailed screenshots, and technical walkthrough videos.

⚠️ Disclaimer

This laboratory was created exclusively for educational, research, and defensive cybersecurity purposes. All attack simulations were performed within an isolated virtual environment owned and controlled by the author. No testing was conducted against external systems, unauthorized networks, or third-party infrastructure.
