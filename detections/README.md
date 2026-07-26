# 🔍 Detection Engineering

## Overview

This directory contains the custom detection rules developed for the SOC Homelab.

The detections were created using Splunk Search Processing Language (SPL) and validated through controlled attack simulations against Windows and Linux endpoints.

Each detection documents the attack technique, detection logic, data sources, investigation guidance, and MITRE ATT&CK mapping.

The objective of these detections is to identify malicious behavior while minimizing false positives and supporting efficient SOC investigations.

---

## Detection Methodology

Each detection follows a standardized format:

- Overview
- Detection Objective
- Data Sources
- SPL Detection Logic
- Detection Logic Explanation
- MITRE ATT&CK Mapping
- Alert Severity
- Investigation Guidance
- False Positives
- Validation
- Related Playbooks

---

## Detection Categories

### Windows

- Inbound Network Port Scanning
- Successful RDP Brute Force
- PowerShell Outbound Network Connection
- Reverse Shell
- Registry Run Key Persistence
- Unquoted Service Path
- LSASS Memory Dump
- Mimikatz Correlation

### Linux

- Port Scan
- SSH Brute Force
- SSH Brute Force Success
- Cron Persistence
- Post-Exploitation

### Phishing

- Sysmon Network Connection Investigation

---

## Validation

Every detection included in this directory was validated using the attack simulations documented within this repository.

Where applicable, detections successfully generated Splunk alerts during testing.

---

> **Note:** These detections were developed and tested within an isolated SOC Homelab for educational and defensive cybersecurity purposes.
