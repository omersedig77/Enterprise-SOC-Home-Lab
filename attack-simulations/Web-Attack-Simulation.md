# Web Attack Simulation – OWASP Juice Shop

## Overview

This repository documents web application attack simulations performed against OWASP Juice Shop within the SOC Homelab.

The objective is to simulate common web application attacks from a Kali Linux attacker system, collect the resulting telemetry through Nginx and OWASP Juice Shop logs, and validate detection and investigation capabilities in Splunk.

All activities are performed within an isolated lab environment for educational, defensive, and detection engineering purposes.

# Objectives

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

# Lab Systems

| System | Role | IP Address / Port |
|--------|------|-------------------|
| Kali Linux | Attacker | 192.168.50.40 |
| Ubuntu Web Server | Target Host | 192.168.50.30 |
| OWASP Juice Shop | Target Application | 192.168.50.30:3000 |
| Splunk Enterprise | SIEM / Detection | 192.168.50.101 |
| pfSense Firewall | Network Gateway | 192.168.50.1 |

---

# Web Architecture

```text
                    Web Attack Simulation Architecture

                     Kali Linux
                   192.168.50.40
                          │
                          │ HTTP Attack
                          ▼
                  Ubuntu Web Server
                    192.168.50.30
                          │
                          ▼
                      Nginx :80
                          │
                          │ Reverse Proxy
                          ▼
                OWASP Juice Shop :3000
                          │
                          │ Application Logs
                          ▼
                    Log Collection
                          │
                          ▼
              Splunk Universal Forwarder
                          │
                          ▼
            Splunk Enterprise :192.168.50.101
                          │
                          ▼
                Detection / Alerting
                          │
                          ▼
                 SOC L1 Investigation
```

# Attack Categories

| Attack Category | Target / Vector | Primary Detection Method | Status |
|---|---|---|---|
| Cross-Site Scripting (XSS) | DOM / Reflected Payload | Splunk SPL | 🔄 In Progress |
| SQL Injection | Authentication / Search Parameters | Splunk SPL | 🔄 In Progress |
| Brute Force Attack | Login Endpoint | Splunk SPL | 🔄 In Progress |


# Attack Simulation Workflow

```text

                         SOC Attack Simulation Workflow

                              Attack Planning
                                    │
                                    ▼
                           Attacker Simulation
                             Kali Linux Host
                                    │
                                    ▼
                          HTTP Request Delivered
                                    │
                                    ▼
                           Nginx Reverse Proxy
                                    │
                                    ▼
                       OWASP Juice Shop Application
                                    │
                                    ▼
                             Log Generation
                                    │
                                    ▼
                             SIEM Ingestion
                                    │
                                    ▼
                           Splunk SPL Detection
                                    │
                                    ▼
                              SOC L1 Alert
                                    │
                                    ▼
                               Investigation
                                    │
                                    ▼
                           Evidence Collection
                                    │
                                    ▼
                       Detection / Mitigation Rules

```

# Telemetry Sources

The primary telemetry sources used for the web attack simulations are:

## Nginx Access Logs

Nginx provides visibility into inbound HTTP traffic, including:

- Source IP addresses
- HTTP methods
- Requested URIs
- Query parameters
- HTTP status codes
- User-Agent strings
- Request frequency
- Response size

### Example Log Location

```bash
/var/log/nginx/juice-shop_access.log
```

## OWASP Juice Shop Container Logs

Application-level telemetry can be reviewed directly from the Juice Shop Docker container:

```bash
sudo docker logs juice-shop
```

These logs can provide additional context when investigating application behavior that may not be visible from the Nginx access logs alone.

## Splunk Metadata

Example Splunk search:

```bash
index=web host=ubuntu-web sourcetype=nginx:juice_shop:access
```

# Detection Methodology

Detections are designed around observable attacker behavior rather than relying exclusively on static signatures.

## Parameter Analysis

Identify suspicious HTTP request parameters and common web attack indicators, including:

- XSS-related payloads
- SQL injection syntax
- Authentication abuse
- Suspicious query parameters
- Encoded attack strings

## Decoding & Unmasking

URL-encoded or otherwise obfuscated request parameters can be decoded within SPL before evaluating detection conditions.

This helps identify payloads that would otherwise evade simple keyword matching.

## Behavioral Triggers

Behavior-based detections can monitor:

- Abnormally high request rates
- Repeated authentication failures
- HTTP method anomalies
- Source IP request spikes
- High-volume HTTP 4xx responses
- High-volume HTTP 5xx responses
- Repeated requests against sensitive endpoints
- Suspicious request parameter patterns

# SOC L1 Investigation Workflow

```text

                           SOC L1 Investigation Flow

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
                           Analyze Web Payload
                                    │
                                    ▼
                           Check Response Code
                                    │
                                    ▼
                          Correlate Telemetry
                                    │
                                    ▼
                          Determine Attack Type
                                    │
                                    ▼
                         Validate Detection Logic
                                    │
                                    ▼
                            Document Findings
```

# Attack Investigation Process

Each simulated attack follows a consistent investigation process.

### 1. Attack Execution

The attack is generated from the isolated Kali Linux attacker system.

### 2. Traffic Observation

Nginx records the HTTP request and associated metadata.

### 3. Application Validation

OWASP Juice Shop processes the request and generates application telemetry where applicable.

### 4. SIEM Ingestion

Relevant logs are forwarded to Splunk Enterprise.

### 5. Detection

Splunk SPL searches identify suspicious activity based on signatures and behavioral indicators.

### 6. Investigation

The SOC analyst reviews:

- Source IP
- Destination
- Requested URI
- HTTP method
- Query parameters
- User-Agent
- Response code
- Request frequency
- Related events
  
### 7. Evidence Collection

Relevant logs, SPL searches, timestamps, and screenshots are documented as investigation evidence.

### 8. Detection Improvement

Detection logic is refined based on the observed attack telemetry.


# Attack Timeline

| Stage | Attack Activity | Detection Method | Status |
|---|---|---|---|
| Initial Access | Cross-Site Scripting (XSS) | Splunk SPL | 🔄 In Progress |
| Initial Access | SQL Injection | Splunk SPL | 🔄 In Progress |
| Credential Access | Brute Force Attack | Splunk SPL | 🔄 In Progress |


# MITRE ATT&CK Mapping

Where applicable, simulated attack behaviors are mapped to relevant MITRE ATT&CK techniques.

# MITRE ATT&CK Mapping

| Attack | ATT&CK Technique | Technique ID | Purpose |
|---|---|---|---|
| Cross-Site Scripting (XSS) | Exploitation for Client Execution | T1203 | Analyze malicious web input and client-side execution |
| SQL Injection | Exploitation for Client Execution | T1203 | Test application input validation and exploitation behavior |
| Brute Force Attack | Brute Force | T1110 | Test authentication abuse and repeated credential attempts |

> **Note:** MITRE ATT&CK mappings should be finalized based on the exact behavior demonstrated during each simulation rather than assigning techniques solely from the attack name.


# Detection Development

Each attack simulation follows a detection engineering lifecycle:

```text

Attack Simulation
       │
       ▼
Telemetry Collection
       │
       ▼
Field Extraction / Parsing
       │
       ▼
SPL Detection Development
       │
       ▼
Detection Testing
       │
       ▼
Alert Generation
       │
       ▼
SOC Investigation
       │
       ▼
Detection Tuning
       │
       ▼
Final Detection Rule

```

# Evidence Structure

Each attack can be documented using the following structure:

```text

attacks/
├── xss/
│   ├── README.md
│   ├── screenshots/
│   ├── spl/
│   └── evidence/
│
├── sql-injection/
│   ├── README.md
│   ├── screenshots/
│   ├── spl/
│   └── evidence/
│
└── brute-force/
    ├── README.md
    ├── screenshots/
    ├── spl/
    └── evidence/
```

# Recommended evidence includes:

Attack timestamp
Source IP
Destination IP
Target endpoint
HTTP request
HTTP response code
Relevant Nginx log entry
Relevant Juice Shop log entry
Splunk search
Splunk detection result
Alert screenshot
Investigation notes
Final detection status

# Skills Demonstrated

- Web Attack Simulation — OWASP Top 10
- Reverse Proxy Telemetry — Nginx access log structuring
- Docker Application Monitoring — Juice Shop container logging
- SIEM Ingestion — Splunk Universal Forwarder
- Splunk SPL Engineering — Detection and investigation queries
- SOC L1 Incident Response — Alert triage and investigation
- Detection Engineering — Behavioral and signature-based detections
- MITRE ATT&CK Mapping — Attack behavior classification
- Security Documentation — Evidence and investigation reporting

# Project Status

| Component | Status |
|---|---|
| Lab Architecture | ✅ Complete |
| Nginx Reverse Proxy | ✅ Complete |
| Juice Shop Deployment | ✅ Complete |
| Splunk Log Ingestion | ✅ Complete |
| XSS Simulation | 🔄 In Progress |
| SQL Injection Simulation | 🔄 In Progress |
| Brute Force Simulation | 🔄 In Progress |
| SPL Detection Rules | 🔄 In Progress |
| SOC Investigation Documentation | 🔄 In Progress |


# Disclaimer

This repository is maintained strictly for educational, research, and defensive security engineering purposes.

All attack simulations are performed within an isolated, non-production SOC homelab against explicitly vulnerable targets.

Do not reproduce these activities against systems, applications, networks, or infrastructure without explicit authorization.
