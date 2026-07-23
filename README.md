# SOC Homelab: Building and Operating a Security Operations Center with Splunk Enterprise

### Project Overview

This project documents the design, implementation, and operation of a Security Operations Center (SOC) home lab built to simulate an enterprise security monitoring environment. The lab provides hands-on experience with Security Information and Event Management (SIEM), detection engineering, threat simulation, alerting, dashboard development, and incident investigation across both Windows and Linux systems.

The environment centralizes security telemetry in Splunk Enterprise, where logs from multiple endpoints and network devices are collected, analyzed, and correlated to detect malicious activity. A dedicated Kali Linux machine is used to simulate real-world attacks, allowing detections to be validated against realistic adversary behavior.

The objective of this project is not only to deploy a SIEM solution, but also to demonstrate the complete SOC workflow—from log collection and monitoring to detection development, attack simulation, alert generation, and incident response.

### Project Objectives

- Design and deploy a functional SOC home lab using Splunk Enterprise.
  
- Centralize security logs from Windows, Linux, and pfSense.
  
- Configure Splunk Universal Forwarders for endpoint log collection.
  
- Develop custom SPL searches for threat detection.
  
- Convert SPL searches into real-time alerts.
  
- Build dashboards for continuous security monitoring.
  
- Simulate cyber attacks against Windows and Linux environments.
  
- Validate detections through realistic attack scenarios.
  
- Investigate security events using endpoint and firewall telemetry.
  
- Document the complete deployment and operational workflow.

### Lab Environment

#### Component	Role

- Ubuntu Desktop	Splunk Enterprise Server
  
- Windows 10	Monitored endpoint with Splunk Universal Forwarder
  
- Ubuntu Desktop	Monitored Linux endpoint with Splunk Universal Forwarder
  
- Kali Linux	Attack platform used to simulate adversary activity
  
- pfSense	Firewall, network gateway, and log source for Splunk


### Key Capabilities

- Centralized log collection
  
- Windows event monitoring
  
- Linux log monitoring
  
- Firewall log analysis
  
- Custom SPL detections
  
- Real-time security alerts
  
- Interactive security dashboards
  
- Windows attack simulations
  
- Linux attack simulations
  
- Phishing simulation
  
- Incident investigation
  
- Detection validation
  
- Repository Structure
  
The repository is organized into dedicated sections covering the complete SOC lifecycle, including architecture, deployment, detection engineering, attack simulations, dashboards, incident response, and supporting documentation.

### Project Status
This project is actively being expanded with additional detections, attack scenarios, dashboards, documentation, screenshots, and technical walkthrough videos.

#### Disclaimer
This laboratory was created exclusively for educational, research, and defensive cybersecurity purposes. All attack simulations were performed within an isolated virtual environment owned and controlled by the author. No testing was conducted against external systems or third-party infrastructure.

This is intentionally focused on what the project is and why it exists. We haven't mentioned installation steps, screenshots, or individual attacks yet—those will each have their own documentation.
