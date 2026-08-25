# Web Attack Simulation – OWASP Juice Shop
Overview

This repository contains a series of controlled web attack simulations against OWASP Juice Shop within the SOC Homelab environment.

The objective is to simulate common web application attacks, generate realistic security telemetry, and validate Splunk SIEM detections, alerts, and investigation workflows.

All activities are performed in an isolated lab environment for educational, defensive, and detection-engineering purposes.

Objectives

The objectives of these simulations are to:

Simulate common web application attacks.
Validate web application telemetry collection.
Develop and test Splunk SPL detections.
Trigger and investigate security alerts.
Map attacks to MITRE ATT&CK where applicable.
Practice SOC L1 investigation workflows.
Build reusable detection logic for web attacks.
Lab Systems
System	Role	IP Address
Kali Linux	Attacker	192.168.50.40
Ubuntu Desktop / Juice Shop	Web Application Target	192.168.50.30
Splunk Enterprise	SIEM	192.168.50.101
pfSense	Network Gateway	192.168.50.1
Web Application

OWASP Juice Shop

Architecture:

Kali Linux
192.168.50.40
      │
      │ HTTP Requests
      ▼
Ubuntu / Nginx
192.168.50.30
      │
      │ Reverse Proxy
      ▼
OWASP Juice Shop
Docker :3000
      │
      ▼
Nginx Logs
      │
      ▼
Splunk Forwarder
      │
      ▼
Splunk Enterprise
192.168.50.101

Attack Categories

The repository will contain individual simulations for different web attack techniques.

Web Attack Simulation
│
├── XSS
├── SQL Injection
├── Authentication Attacks
├── Authorization / Access Control
├── Path Traversal
├── Command Injection
├── SSRF
├── XXE
├── File Upload Attacks
├── API Attacks
└── Other OWASP Juice Shop Challenges


Each attack will have its own subdirectory/repository section containing the attack procedure, evidence, Splunk detection, and investigation results.

Attack Simulation Structure

Each attack simulation should follow the same workflow:

Attacker
   │
   ▼
Attack Simulation
   │
   ▼
Nginx / Juice Shop Telemetry
   │
   ▼
Splunk Ingestion
   │
   ▼
SPL Detection
   │
   ▼
Alert
   │
   ▼
SOC Investigation
   │
   ▼
Documentation & Evidence

Attack Timeline
Stage	Activity	Detection
Reconnaissance	Web/API enumeration	🔄
Initial Access	Authentication attacks	🔄
Exploitation	XSS / SQLi / Command Injection	🔄
Privilege / Access Abuse	Authorization attacks	🔄
Post-Exploitation	Application abuse	🔄
Detection	Splunk SPL detection	🔄
Investigation	SOC analysis	🔄

The exact stages will depend on the attack being simulated.

Telemetry

Web attack telemetry is collected primarily from Nginx reverse-proxy logs.

Example fields:

clientip
method
uri_path
request_uri
query_string
decoded_query
status
useragent
request_time


Example event:

clientip=192.168.50.40
method=GET
uri_path=/rest/products/search
query_string=q=%3Cscript%3Ealert%28%27xss%27%29%3C%2Fscript%3E
decoded_query=q=<script>alert('xss')</script>
status=500
useragent=curl/8.19.0


These fields provide the foundation for web attack detection and investigation.

Detection Objectives

For each attack, the simulation should answer:

What did the attacker do?
What telemetry was generated?
Can Splunk identify the activity?
What SPL detects it?
What fields are useful for investigation?
What alert should a SOC analyst receive?
What additional investigation would an L1 analyst perform?
Documentation Standard

Each attack subdirectory will use a consistent structure:

Attack-Name/
│
├── README.md
├── attack/
│   └── commands.md
├── detection/
│   └── spl.md
└── screenshots/
    ├── attacker/
    ├── splunk/
    └── alert/


The README.md for each attack will contain:

Overview
Objective
Attack scenario
Lab systems
Attack execution
Evidence
Splunk detection
Detection results
MITRE ATT&CK mapping
SOC investigation notes
Conclusion
Planned Attacks

The simulations will be added incrementally.

Web Attack 01 – XSS

Simulate Cross-Site Scripting against Juice Shop and detect malicious payloads in HTTP requests.

Status: ✅ Completed

Detection telemetry includes:

clientip
method
uri_path
query_string
decoded_query
status
useragent
request_time

Web Attack 02 – SQL Injection

Simulate SQL injection against vulnerable Juice Shop functionality and develop Splunk detections for SQLi indicators.

Status: 🔄 Planned

Web Attack 03 – Authentication Attacks

Simulate suspicious login activity such as repeated authentication attempts and identify abnormal authentication behavior.

Status: 🔄 Planned

Web Attack 04 – Authorization / Access Control

Simulate unauthorized access to application resources and identify suspicious access patterns.

Status: 🔄 Planned

Web Attack 05 – Path Traversal

Simulate malicious path traversal requests and detect traversal patterns in HTTP requests.

Status: 🔄 Planned

Web Attack 06 – Command Injection

Simulate command-injection activity against vulnerable application functionality and detect suspicious command patterns.

Status: 🔄 Planned

Web Attack 07 – SSRF

Simulate Server-Side Request Forgery behavior and investigate suspicious outbound/request parameters.

Status: 🔄 Planned

Web Attack 08 – File Upload Abuse

Simulate malicious or unexpected file-upload activity and develop corresponding detections.

Status: 🔄 Planned

SOC Investigation Workflow

For each attack, the investigation will follow a simple SOC L1 process:

Alert
  │
  ▼
Identify Source IP
  │
  ▼
Identify Target URI
  │
  ▼
Inspect Request / Payload
  │
  ▼
Check HTTP Status
  │
  ▼
Check User-Agent
  │
  ▼
Review Related Events
  │
  ▼
Determine Attack Technique
  │
  ▼
Document Findings

Goal

The goal of this project is to build a practical web attack detection portfolio demonstrating the complete workflow:

Attack → Telemetry → Detection → Alert → Investigation → Documentation

The simulations will be performed one attack at a time, with each attack maintaining the same documentation and detection structure.
