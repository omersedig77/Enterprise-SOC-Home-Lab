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
             Impact / Damage
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
| Impact | Download EICAR file | Splunk detects the malicious file on the endpoint |

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
  
  <img width="822" height="521" alt="Nmap scanning on kali using namp" src="https://github.com/user-attachments/assets/4ef4fbd5-fbad-413f-8da0-4d23e602e9f0" />

- Triggered alert
<img width="1637" height="343" alt="1 The network scanning alert triggerd" src="https://github.com/user-attachments/assets/b6560e03-244d-494e-b5ba-634389bb9e19" />

- Splunk detection
<img width="1507" height="627" alt="Detecting Nmap using SPL query 3" src="https://github.com/user-attachments/assets/6fcfc6fc-c176-4dea-92fb-171f6855c895" />

- Dashboard

<img width="1827" height="1026" alt="Final Scanning Dashboard" src="https://github.com/user-attachments/assets/4d2c1146-3aea-4624-ae6d-64366905b252" />

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
  <img width="1657" height="582" alt="3 RDP Brute Forcing" src="https://github.com/user-attachments/assets/35c30480-7813-4cf8-af92-75c50ccebdd2" />

- Successful RDP login and the Splunk Alert
  <img width="3362" height="1122" alt="14 Full pic of the RDP brute force and access" src="https://github.com/user-attachments/assets/588ec177-6bff-4d40-93c0-7969d6f6001e" />

- Splunk detection
<img width="1500" height="792" alt="15 The Result of the SPL" src="https://github.com/user-attachments/assets/9397c671-6801-475a-b139-665e548b812d" />


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
<img width="792" height="432" alt="1 Weponization creating the malware file" src="https://github.com/user-attachments/assets/8487a612-583f-4c7f-9d7b-898ede6fd129" />

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
  
  <img width="487" height="182" alt="2 kali is hosting" src="https://github.com/user-attachments/assets/bc89f9b9-d385-4ec3-84e0-c84a20e50a2b" />
<img width="1913" height="377" alt="12 Both Windows   kali Delivery payload" src="https://github.com/user-attachments/assets/27ef7712-0c3b-4a62-b943-0dec9f6476b8" />

- Triggered alert
<img width="3345" height="267" alt="13 The delivery alert triggered" src="https://github.com/user-attachments/assets/29e935d4-2508-43eb-8e36-2ba2029a2e3c" />

- Splunk detection
<img width="2272" height="833" alt="5 SPL detecting delivery" src="https://github.com/user-attachments/assets/5856a7ff-4e3c-44e6-bd93-77dd98dc6b82" />

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
  <img width="877" height="1045" alt="11 we got shell from the windows machine" src="https://github.com/user-attachments/assets/4800cfda-dfa2-437f-96fe-9a6ed723562e" />

- Splunk alert
  <img width="3353" height="406" alt="18 Alert is triggerd" src="https://github.com/user-attachments/assets/b577150a-e009-4a34-ac51-3abd12be9ad7" />

- Splunk detection
<img width="3113" height="563" alt="19 The search result" src="https://github.com/user-attachments/assets/0489cb11-a3cd-4522-a372-8f49632c0872" />

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
  <img width="1103" height="115" alt="17 persistance powershell command" src="https://github.com/user-attachments/assets/838ba172-1cec-4fd4-87f2-e24b385435fb" />

- Splunk alerts
  <img width="3347" height="468" alt="23 Persistance Alerts" src="https://github.com/user-attachments/assets/f99685ea-3991-44e2-9306-6090517e7fce" />

- Splunk detection
<img width="3363" height="732" alt="24 Persistence Registry Key Modification Detected" src="https://github.com/user-attachments/assets/360632fe-963d-4054-aaa0-3e2dcf94d71c" />

---

# Stage 7 – Privilege Escalation

## Objective

Obtain elevated privileges using an Unquoted Service Path vulnerability.

### Triggered Detection

- Privilege Escalation – Unquoted Service Path Reverse Shell Verified

### MITRE ATT&CK

- T1574.009 – Unquoted Path

### Screenshots

- Service configuration and Successful privilege escalation
  <img width="1888" height="477" alt="13 The privilege Escalation full pic windows and kali" src="https://github.com/user-attachments/assets/bf7dfc3e-febc-4a72-b69c-4f2a188c60fb" />

- Splunk alert
<img width="3360" height="757" alt="14 Alert Triggerd" src="https://github.com/user-attachments/assets/06c392b0-bf0e-4cda-b43f-5faf1a375449" />

- Splunk detection
<img width="3366" height="780" alt="15 The Result" src="https://github.com/user-attachments/assets/fe93f703-987f-4ed4-a49e-416659d791d6" />

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
  
  <img width="1020" height="1161" alt="EX 3" src="https://github.com/user-attachments/assets/4037d743-8b98-4184-a9ab-ff452746638f" />

- Mimikatz execution
  
  <img width="950" height="1010" alt="mimikatz dump 1" src="https://github.com/user-attachments/assets/a1695d50-acd4-43fc-9d36-f15922750932" />

- Splunk alerts
  
Credential Access – LSASS Memory Dumping Behavior Detected

<img width="3362" height="1022" alt="20 As we can see the credintial access alert triggerd" src="https://github.com/user-attachments/assets/6868cba2-a33e-4809-ad6b-fd79968bead9" />

Credential Access – Mimikatz Ingress and Command Execution Correlation

<img width="3362" height="972" alt="Mimikatz alert triggered" src="https://github.com/user-attachments/assets/ec69b35d-c558-4540-b951-7d6d315dd3c5" />



- Splunk detection

LSASS memory dumping

  <img width="3363" height="688" alt="21 The result of the creds access" src="https://github.com/user-attachments/assets/cd9f12fd-72a9-4f34-9066-b59d4b7c9ebe" />

Mimikatz execution

<img width="3178" height="868" alt="The SPL query of the mimikatz" src="https://github.com/user-attachments/assets/db2f0393-c0e4-48f0-98f6-0508fc6d4e58" />

---

# Stage 9 – Impact

## Objective

Demonstrate the transfer of a simulated malicious executable to the compromised Windows endpoint, representing an attacker attempting to cause damage or disruption.

The attacker uses the previously established reverse shell to transfer the executable to the victim.

### Result

The simulated malicious executable was successfully transferred to the Windows endpoint. Splunk detected the file when it was downloaded, allowing the activity to be monitored and investigated.

### Monitoring

- Splunk Enterprise – File creation and security events
- VirusTotal Integration – File/hash reputation analysis
- Windows Endpoint – File activity

### MITRE ATT&CK

- T1105 – Ingress Tool Transfer

#### | Note
The EICAR test file was used as a safe substitute for a real malicious executable. It does not damage the system; in this scenario, it represents a payload intended to cause impact.

### Screenshots

- Downloading the malware file on the windows system through the reverse shell
  
  <img width="1898" height="1018" alt="9 downloading the malware file on the windows system" src="https://github.com/user-attachments/assets/0241f65b-507f-461d-9f26-b317724f1169" />

- Splunk Alert
  
  <img width="2316" height="1013" alt="13 Alert triggred there is malware" src="https://github.com/user-attachments/assets/46563d82-7e84-472b-94f8-351a3f203b75" />

- Splunk Detection
  
<img width="3182" height="742" alt="10 the malware query" src="https://github.com/user-attachments/assets/ae175042-acea-4c2d-9690-177ab363f91e" />

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
| Impact | ✅ Observed |

---

# Lessons Learned

This simulation successfully validated the custom detections developed for the SOC Homelab by exercising multiple stages of the Cyber Kill Chain. Telemetry from Windows Event Logs, Sysmon, and the pfSense firewall was collected, correlated, and analyzed within Splunk Enterprise.

The exercise demonstrated how endpoint and network visibility can be combined to detect attacker activity, generate actionable alerts, and support SOC analyst investigations from initial reconnaissance through post-exploitation.
