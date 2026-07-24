# Linux Attack Simulation – Cyber Kill Chain Validation

## Overview

This attack simulation demonstrates a complete attack lifecycle against the Ubuntu endpoint within the SOC Homelab.

The objective was to emulate realistic attacker behavior against a Linux system while validating custom Splunk detections, alerts, and dashboards developed for Linux security monitoring.

All activities were performed within an isolated lab environment for educational, defensive, and detection engineering purposes.

---

# Objectives

The objectives of this exercise were to:

- Validate Linux telemetry collection.
- Verify Linux SPL detections.
- Trigger Splunk alerts.
- Monitor attacker activity throughout the attack lifecycle.
- Demonstrate Linux attack techniques.
- Practice SOC investigation workflows.

---

# Lab Systems

| System | Role | IP Address |
|----------|------|------------|
| Kali Linux | Attacker | 192.168.50.40 |
| Ubuntu Desktop | Target | 192.168.50.30 |
| Splunk Enterprise | SIEM | 192.168.50.101 |
| pfSense Firewall | Network Gateway | 192.168.50.1 |

---

# Attack Flow

```
                Linux Attack Simulation

      Kali Linux (192.168.50.40)
                 │
                 ▼
      Reconnaissance (Nmap Scan)
                 │
                 ▼
      Initial Access (SSH Brute Force)
                 │
                 ▼
      Successful SSH Compromise
                 │
                 ▼
      Persistence (Cron Reverse Shell)
                 │
                 ▼
      Post-Exploitation
                 │
                 ▼
      Defense Evasion
                 │
                 ▼
      Data Exfiltration
                 │
                 ▼
      Splunk Alerts Triggered
                 │
                 ▼
      SOC Investigation & Dashboards
```

---

# Attack Timeline

| Stage | Activity | Detection |
|--------|----------|-----------|
| Reconnaissance | Nmap Port Scan | ✅ Linux Reconnaissance – Port Scan Detected |
| Initial Access | SSH Brute Force | ✅ Linux Access – High Velocity SSH Brute Force Detected |
| Initial Access | Successful SSH Login | ✅ CRITICAL – SSH Brute Force Success – Host Compromised |
| Initial Access | Successful Login After Multiple Failures | ✅ SSH Brute Force Success after Multiple Failed Logins |
| Persistence | Cron Reverse Shell | ✅ CRITICAL – Linux Cron Reverse Shell Persistence Detected |
| Post-Exploitation | Defense Evasion & Exfiltration | ✅ CRITICAL – Linux Post-Exploitation Evasion & Exfiltration |

---

# Stage 1 – Reconnaissance

## Objective

Identify exposed services on the Ubuntu endpoint.

### Tool

- Nmap

### Command

```bash
sudo nmap -sS -sV -O -p- 192.168.50.30
```

### Result

The scan identified open services available on the Linux host.

### Triggered Detection

- Linux Reconnaissance – Port Scan Detected

### MITRE ATT&CK

- T1595 – Active Scanning

### Screenshots

- Nmap output
- Splunk alert
- Detection events

---

# Stage 2 – Initial Access

## Objective

Obtain access using valid SSH credentials.

### Tool

- Hydra

### Technique

A brute-force attack was launched against the SSH service using username and password wordlists.

### Result

Valid credentials were discovered, allowing successful SSH access.

### Triggered Detections

- Linux Access – High Velocity SSH Brute Force Detected
- CRITICAL – SSH Brute Force Success – Host Compromised
- SSH Brute Force Success after Multiple Failed Logins

### MITRE ATT&CK

- T1110 – Brute Force

### Screenshots

- Hydra attack
- SSH login
- Splunk alerts

---

# Stage 3 – Persistence

## Objective

Maintain access to the compromised Linux endpoint.

### Technique

A reverse shell was configured using a cron job to establish persistent access.

### Triggered Detection

- CRITICAL – Linux Cron Reverse Shell Persistence Detected

### MITRE ATT&CK

- T1053.003 – Cron

### Screenshots

- Cron configuration
- Reverse shell
- Splunk alert

---

# Stage 4 – Post-Exploitation

## Objective

Simulate attacker activity after compromising the host.

Activities included:

- Defense evasion techniques
- System discovery
- Data collection
- Outbound communication

### Triggered Detection

- CRITICAL – Linux Post-Exploitation Evasion & Exfiltration

### MITRE ATT&CK

Examples include:

- T1070 – Indicator Removal
- T1082 – System Information Discovery
- T1041 – Exfiltration Over C2 Channel

> *Update this section with the exact techniques you simulated.*

### Screenshots

- Terminal activity
- Splunk detection
- Related events

---

# Detection Summary

| Attack Stage | Detection Status |
|--------------|------------------|
| Reconnaissance | ✅ Detected |
| SSH Brute Force | ✅ Detected |
| Successful Compromise | ✅ Detected |
| Persistence | ✅ Detected |
| Post-Exploitation | ✅ Detected |
| Exfiltration Monitoring | ✅ Detected |

---

# Lessons Learned

This simulation validated the Linux-focused detections developed within the SOC Homelab. Authentication logs, system logs, and kernel logs were collected and analyzed by Splunk Enterprise, allowing attacker activity to be detected throughout the simulated attack lifecycle.

The exercise demonstrated how Linux telemetry can be leveraged to detect brute-force attacks, persistent access mechanisms, and post-exploitation behavior within a centralized SIEM.

---

# Evidence

The following artifacts are included with this simulation:

- Screenshots of each attack stage
- SPL search results
- Triggered Splunk alerts
- Dashboard visualizations
- Terminal output from Kali Linux
- Terminal output from Ubuntu
- Screen recording of the complete attack simulation
