# Windows Attack Simulation – Cyber Kill Chain Validation

## Overview

This attack simulation demonstrates a complete attack lifecycle against the Windows 10 endpoint within the SOC Homelab.

The objective was to emulate realistic attacker behavior across multiple stages of the Cyber Kill Chain while validating custom Splunk detections, alerts, and dashboards.

All activities were performed within an isolated lab environment for educational, defensive, and detection engineering purposes.

---

# Objectives

This attack simulation was designed to validate the effectiveness of custom Splunk detections developed within the SOC Homelab.

The objectives of the exercise were to:

- Validate Windows endpoint telemetry collection.
- Verify custom SPL detections.
- Trigger Splunk alerts.
- Correlate endpoint and firewall events.
- Demonstrate the attack lifecycle from reconnaissance to post-exploitation.
- Practice SOC monitoring and investigation workflows.

---

# Lab Systems

| System | Role | IP Address |
|----------|------|------------|
| Kali Linux | Attacker | 192.168.50.40 |
| Windows 10 | Target | 192.168.50.20 |
| Splunk Enterprise | SIEM | 192.168.50.101 |
| pfSense Firewall | Network Gateway | 192.168.50.1 |

---

# Attack Flow

```

                    Windows Attack Simulation

        Kali Linux (192.168.50.40)
                    │
                    ▼
        Reconnaissance (Nmap Scan)
                    │
                    ▼
      Initial Access (Hydra RDP Brute Force)
                    │
                    ▼
      Weaponization (Generate Reverse Shell Payload)
                    │
                    ▼
        Delivery (Transfer Payload)
                    │
                    ▼
      Exploitation (Execute Reverse Shell)
                    │
                    ▼
     Persistence (Registry Run Key)
                    │
                    ▼
 Privilege Escalation (Unquoted Service Path)
                    │
                    ▼
 Credential Access (LSASS Dump + Mimikatz)
                    │
                    ▼
     Exfiltration / Outbound Communication
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
| Reconnaissance | Nmap Port Scan | ✅ Inbound Network Port Scanning Detected |
| Initial Access | Hydra RDP Brute Force | ✅ Credential Access – Successful RDP Brute Force Compromise |
| Weaponization | Generate Reverse Shell Payload | Preparation Stage |
| Delivery | Transfer Reverse Shell Payload | ✅ Delivery – PowerShell Script with Outbound Network Connection |
| Exploitation | Execute Reverse Shell | ✅ Exploitation – Malicious Binary Outbound Reverse Shell Connection |
| Persistence | Registry Run Key | ✅ Persistence – Registry Run Key Modifications Detected |
| Persistence | Unauthorized Registry Modification | ✅ Persistence – Unauthorized Registry Run Key Modification |
| Privilege Escalation | Unquoted Service Path | ✅ Privilege Escalation – Unquoted Service Path Reverse Shell Verified |
| Credential Access | LSASS Memory Dump | ✅ Credential Access – LSASS Memory Dumping Behavior Detected |
| Credential Access | Mimikatz Execution | ✅ Credential Access – Mimikatz Ingress and Command Execution Correlation |
| Exfiltration | Outbound Communication | Monitored through Splunk telemetry and pfSense logs |

---

# Stage 1 – Reconnaissance

## Objective

Identify exposed services and potential attack surfaces on the Windows endpoint.

### Tool

- Nmap

### Command

```bash
sudo nmap -sS -sV -O -p- 192.168.50.20
```

### Result

The scan identified open ports, running services, and operating system information used to plan the next stage of the attack.

### Triggered Detection

- Inbound Network Port Scanning Detected

### MITRE ATT&CK

- T1595 – Active Scanning

### Screenshots

- Nmap scan output
- Splunk detection
- Triggered alert

---

# Stage 2 – Initial Access

## Objective

Obtain valid credentials through a brute-force attack against the Windows RDP service.

### Tool

- Hydra

### Technique

A brute-force attack was launched against RDP using separate username and password wordlists.

### Result

A valid username and password combination was identified, allowing successful Remote Desktop access to the Windows endpoint.

### Triggered Detection

- Credential Access – Successful RDP Brute Force Compromise

### MITRE ATT&CK

- T1110 – Brute Force

### Screenshots

- Hydra attack
- Successful RDP login
- Splunk alert

---

# Stage 3 – Weaponization

## Objective

Prepare a reverse shell payload for later execution on the compromised Windows system.

### Tool

- Metasploit Framework

### Activity

A reverse shell payload was generated, and a listener was configured on the Kali Linux attacker system.

Listener:

```
192.168.50.40:4444
```

> **Note:** Initial access had already been achieved through RDP. A reverse shell payload was created to simulate a malware-based intrusion scenario and validate additional detections related to payload delivery, exploitation, and command-and-control behavior.

### Screenshots

- msfvenom payload generation
- Metasploit listener configuration

---

# Stage 4 – Delivery

## Objective

Transfer the reverse shell payload to the Windows endpoint.

### Result

The payload was successfully delivered to the target system in preparation for execution.

### Triggered Detection

- Delivery – PowerShell Script with Outbound Network Connection

### MITRE ATT&CK

- T1105 – Ingress Tool Transfer

### Screenshots

- Payload delivery
- Splunk detection
- Triggered alert

---

# Stage 5 – Exploitation

## Objective

Execute the reverse shell payload and establish a remote session.

### Result

The Windows endpoint initiated an outbound connection to the Kali Linux listener, providing an interactive reverse shell.

### Triggered Detection

- Exploitation – Malicious Binary Outbound Reverse Shell Connection

### MITRE ATT&CK

- T1059 – Command and Scripting Interpreter
- T1071 – Application Layer Protocol

### Screenshots

- Reverse shell session
- Splunk alert
- Dashboard visualization

---

# Stage 6 – Persistence

## Objective

Maintain long-term access to the compromised system.

### Technique

Registry Run Key modification.

### Triggered Detections

- Persistence – Registry Run Key Modifications Detected
- Persistence – Unauthorized Registry Run Key Modification

### MITRE ATT&CK

- T1547.001 – Registry Run Keys / Startup Folder

### Screenshots

- Registry modification
- Splunk alerts
- Detection dashboard

---

# Stage 7 – Privilege Escalation

## Objective

Obtain elevated privileges using an Unquoted Service Path vulnerability.

### Triggered Detection

- Privilege Escalation – Unquoted Service Path Reverse Shell Verified

### MITRE ATT&CK

- T1574.009 – Unquoted Path

### Screenshots

- Service configuration
- Successful privilege escalation
- Splunk alert

---

# Stage 8 – Credential Access

## Objective

Access credentials stored in LSASS memory.

### Activities

- LSASS memory dumping
- Mimikatz execution

### Triggered Detections

- Credential Access – LSASS Memory Dumping Behavior Detected
- Credential Access – Mimikatz Ingress and Command Execution Correlation

### MITRE ATT&CK

- T1003.001 – LSASS Memory

### Screenshots

- LSASS dump
- Mimikatz execution
- Splunk alerts

---

# Stage 9 – Exfiltration

## Objective

Demonstrate outbound communication from the compromised endpoint.

### Result

Outbound communication was successfully observed and correlated using endpoint telemetry and pfSense firewall logs.

### Monitoring

- Splunk Enterprise
- pfSense Firewall

### Screenshots

- Firewall logs
- Splunk events
- Dashboard visualization

---

# Detection Summary

| Attack Stage | Detection Status |
|--------------|------------------|
| Reconnaissance | ✅ Detected |
| Initial Access | ✅ Detected |
| Delivery | ✅ Detected |
| Exploitation | ✅ Detected |
| Persistence | ✅ Detected |
| Privilege Escalation | ✅ Detected |
| Credential Access (LSASS) | ✅ Detected |
| Credential Access (Mimikatz) | ✅ Detected |
| Exfiltration Monitoring | ✅ Observed |

---

# Lessons Learned

This simulation successfully validated the custom detections developed for the SOC Homelab by exercising multiple stages of the Cyber Kill Chain. Telemetry from Windows Event Logs, Sysmon, and the pfSense firewall was collected, correlated, and analyzed within Splunk Enterprise.

The exercise demonstrated how endpoint and network visibility can be combined to detect attacker activity, generate actionable alerts, and support SOC analyst investigations from initial reconnaissance through post-exploitation.
