# Web Attack Simulation – OWASP Juice Shop

## Overview

This repository documents web application attack simulations performed against **OWASP Juice Shop** within the SOC Homelab.

The objective is to simulate common web application attacks from a Kali Linux attacker system, collect the resulting telemetry through Nginx and application logs, and validate detection and investigation capabilities in Splunk.

All activities are performed within an isolated lab environment for educational, defensive, and detection engineering purposes.

---

## Objectives

The objectives of these simulations are to:

- Simulate realistic web application attacks.
- Validate web application telemetry collection.
- Monitor attacker activity through Nginx and Juice Shop logs.
- Develop and validate Splunk SPL detections.
- Trigger and investigate security alerts.
- Practice SOC L1 investigation workflows.
- Map web attacks to the MITRE ATT&CK framework where applicable.
- Document attack evidence and detection results.

---

## Lab Environment

| System | Role | IP Address |
|---|---|---|
| Kali Linux | Attacker | `192.168.50.40` |
| Ubuntu Desktop | Web Attack Target / Juice Shop | `192.168.50.30` |
| Splunk Enterprise | SIEM / Detection | `192.168.50.101` |
| pfSense | Network Gateway | `192.168.50.1` |
| OWASP Juice Shop | Vulnerable Web Application | `192.168.50.30:3000` |

### Web Architecture

```text
                    Web Attack Simulation

              Kali Linux
             192.168.50.40
                    │
                    │ Web Attack
                    ▼
             Ubuntu Web Server
             192.168.50.30
                    │
                    ▼
                 Nginx
                  :80
                    │
                    │ Reverse Proxy
                    ▼
             OWASP Juice Shop
                  :3000
                    │
                    │ Logs
                    ▼
           Splunk Universal Forwarder
                    │
                    ▼
            Splunk Enterprise
             192.168.50.101
                    │
                    ▼
        Detection / Alert / Investigation
```

### Attack Categories

The following web attack simulations will be documented in this repository:

Attack	Status	Detection
Cross-Site Scripting (XSS)	🔄 In Progress	Splunk SPL

SQL Injection	⏳ Planned	Splunk SPL

Authentication Attacks	⏳ Planned	Splunk SPL

Broken Access Control	⏳ Planned	Splunk SPL

Security Misconfiguration	⏳ Planned	Splunk SPL

Path Traversal	⏳ Planned	Splunk SPL

Command Injection	⏳ Planned	Splunk SPL

SSRF	⏳ Planned	Splunk SPL

XXE	⏳ Planned	Splunk SPL

File Upload Attacks	⏳ Planned	Splunk SPL

JWT / Token Attacks	⏳ Planned	Splunk SPL

Web Reconnaissance	⏳ Planned	Splunk SPL


The attack list will be expanded as additional Juice Shop challenges are simulated and detected.


### Attack Simulation Workflow

```text

Each attack follows a consistent SOC-oriented workflow:

Attack Planning
      │
      ▼
Attacker Simulation
(Kali Linux)
      │
      ▼
Web Request
      │
      ▼
Nginx
      │
      ▼
OWASP Juice Shop
      │
      ▼
Log Generation
      │
      ▼
Splunk Ingestion
      │
      ▼
SPL Detection
      │
      ▼
Alert / Investigation
      │
      ▼
Evidence & Documentation
```

### Telemetry

The primary telemetry sources used for the web attack simulations are:

- Nginx Access Logs
- Nginx acts as the reverse proxy in front of Juice Shop and records HTTP requests.

#### Example log location:

/var/log/nginx/juice-shop_access.log


#### The logging configuration captures information such as:

Source IP
HTTP method
Request URI
Query string
HTTP status
User-Agent
Referer
Request time
Juice Shop Container Logs

Juice Shop application activity is also collected from the Docker container.

#### Example:

sudo docker logs juice-shop

Splunk

The Nginx logs are forwarded to Splunk using the Splunk Universal Forwarder.

#### Example Splunk metadata:

index=web
host=ubuntu-web
sourcetype=nginx:juice_shop:access

### Detection Methodology

Detections are designed around observable attacker behavior rather than relying solely on known payloads.

Detection logic may examine:

- Suspicious request parameters
- Encoded payloads
- Web attack keywords
- HTTP methods
- Request frequency
- HTTP response codes
- Source IP addresses
- User-Agent anomalies
- Repeated failed requests
- Suspicious URI paths
- Abnormal request patterns

Where appropriate, URL-encoded values are decoded during analysis before detection.

### Attack Documentation Structure

Each attack will have its own directory containing the attack simulation and detection documentation.

Example:
```text

Web-attack-simulation/
│
├── README.md
│
├── XSS/
│   └── README.md
│
├── SQL-Injection/
│   └── README.md
│
├── Authentication/
│   └── README.md
│
└── ...
```

#### Each attack README will document:

- Attack objective
- Lab target
- Attacker system
- Attack technique
- Attack command/request
- Attacker-side evidence
- Nginx/application telemetry
- Splunk search
- Detection logic
- Alert result
- Investigation notes
- MITRE ATT&CK mapping
- Lessons learned
- SOC Investigation Workflow


#### For each simulated attack, the investigation follows a basic SOC L1 process:
```text

Alert
  │
  ▼
Identify Source IP
  │
  ▼
Identify Target
  │
  ▼
Review HTTP Request
  │
  ▼
Analyze Payload
  │
  ▼
Check HTTP Response
  │
  ▼
Review Related Events
  │
  ▼
Determine Attack Technique
  │
  ▼
Document Findings
```

### Attack Timeline

As simulations are completed, the following table will track the overall exercise:

- Stage	Attack Activity	Detection	Status
- Reconnaissance	Web Reconnaissance	Splunk Detection	⏳
- Initial Access	XSS	XSS Detection	🔄
- Initial Access	SQL Injection	SQLi Detection	⏳
- Initial Access	Authentication Attack	Authentication Detection	⏳
- Exploitation	Command Injection	Command Injection Detection	⏳
- Exploitation	Path Traversal	Path Traversal Detection	⏳
- Post-Exploitation	Broken Access Control	Access Control Detection	⏳
- Skills Demonstrated

#### This project demonstrates practical SOC and detection engineering skills including:

- Web attack simulation
- OWASP Juice Shop
- Nginx log analysis
- Splunk log ingestion
- SPL development
- Detection engineering
- Alert investigation
- HTTP request analysis
- Attack pattern recognition
- MITRE ATT&CK mapping
- Security monitoring
- SOC L1 investigation methodology

### Disclaimer

This project is intended for educational and defensive security purposes.

All attacks are performed against intentionally vulnerable applications inside an isolated SOC Homelab environment.

Do not perform these techniques against systems or applications without explicit authorization.


### GitHub structure

I would keep the parent README **this level of detail** and avoid putting individual XSS/SQLi commands into it.

Then:

```text
Web-attack-simulation/
│
├── README.md
│
├── XSS/
│   ├── README.md
│   └── screenshots/
│       ├── 01-attacker-xss.png
│       ├── 02-nginx-log.png
│       └── 03-splunk-detection.png
│
├── SQL-Injection/
│   ├── README.md
│   └── screenshots/
│
├── Authentication/
│   ├── README.md
│   └── screenshots/
│
└── ...
```
