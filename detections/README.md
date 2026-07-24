# Detection Engineering

## Overview

Detection engineering is a core component of this SOC Homelab. The objective is to transform raw security telemetry into actionable detections capable of identifying malicious activity across Windows, Linux, and network infrastructure.

Each detection begins as an SPL search developed to identify a specific attack technique or suspicious behavior. Once validated, the search is converted into a Splunk alert and tested by executing the corresponding attack within the lab environment.

This process ensures that every documented detection has been verified against realistic attack simulations rather than relying solely on theoretical detection logic.

---

# Detection Development Methodology

The detections in this repository were developed through a combination of original SPL development, research, and iterative refinement. Where appropriate, AI-assisted drafting was used to accelerate query development. Every detection was reviewed, adapted to the lab environment, tested against simulated attacks, and validated by confirming that the corresponding alerts triggered as expected.

---

# Detection Workflow

Each detection follows the same engineering process:

1. Identify an attack technique or suspicious behavior.
2. Determine the required telemetry.
3. Develop or refine an SPL search.
4. Validate the search against collected logs.
5. Convert the search into a Splunk alert.
6. Execute the corresponding attack simulation.
7. Confirm that the alert triggers successfully.
8. Investigate the resulting events.
9. Refine the detection if necessary.

---

# Data Sources

The detections in this project leverage multiple sources of telemetry.

## Windows

- Security Event Log
- System Event Log
- Application Event Log
- Sysmon Operational Log

## Linux

- /var/log/syslog
- /var/log/auth.log
- /var/log/kern.log

## Network

- pfSense Firewall Logs

---

# Detection Categories

## Windows Detections

- Inbound Network Port Scanning
- Successful RDP Brute Force Compromise
- PowerShell Script with Outbound Network Connection
- Malicious Binary Reverse Shell
- Registry Run Key Persistence
- Unauthorized Registry Modification
- Unquoted Service Path Exploitation
- LSASS Memory Dumping
- Mimikatz Command Execution Correlation
- High-Volume Network Anomaly

## Linux Detections

- Port Scanning
- SSH Brute Force
- Successful SSH Compromise
- Cron Reverse Shell Persistence
- Post-Exploitation Evasion and Exfiltration

## Firewall Detection

- Internal Host Attempting Access to Phishing Infrastructure

---

# Validation

Every detection documented in this repository has been validated by executing the corresponding attack within the isolated SOC Homelab environment. Detection success was confirmed by:

- Alert generation
- Indexed event verification
- Timeline analysis
- Dashboard visibility
- Investigation within Splunk Enterprise

This validation process demonstrates that the detections are operational within the documented lab environment.
