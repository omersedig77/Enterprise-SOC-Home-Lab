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
| Cross-Site Scripting (XSS) | DOM / Reflected Payload | Splunk SPL | ✅ In Progress |
| SQL Injection | Authentication / Search Parameters | Splunk SPL | ✅ In Progress |
| Brute Force Attack | Login Endpoint | Splunk SPL | ✅ In Progress |


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


# Stage 1 – Cross-Site Scripting (XSS)

## Objective

Simulate a Reflected Cross-Site Scripting (XSS) attack targeting the OWASP Juice Shop search API to validate reverse proxy log collection and custom Splunk SPL detection rules.

---

## Attack Execution

An attacker issues an HTTP `GET` request containing a URL-encoded JavaScript script tag targeting the product search query parameter (`q`).

### Target Endpoint

```http
GET /rest/products/search?q=%3Cscript%3Ealert%27xss%27%29%3C%2Fscript%3E HTTP/1.1
```

## Attack Vector & Tool

### Tool: 
curl

### Vector: 
Injected Search Query Parameter (q)

### Command

```bash
curl -i "[http://192.168.50.30/rest/products/search?q=%3Cscript%3Ealert%27xss%27%29%3C%2Fscript%3E](http://192.168.50.30/rest/products/search?q=%3Cscript%3Ealert%27xss%27%29%3C%2Fscript%3E)"
```

## Result
The target application processed the payload and returned an HTTP/1.1 500 Internal Server Error containing an unhandled application stack trace, confirming the input reached the backend processing engine.

## Detection & Mapping

Triggered Detection

- Alert: ```Potential XSS Attempt ```

## MITRE ATT&CK Mapping

- Tactic: Initial Access / Execution

- Technique: T1059.007 – Command and Scripting Interpreter: JavaScript

## Primary Telemetry Source

- Log Path: ```/var/log/nginx/juice-shop_access.log ```

Splunk Source: ```index=web sourcetype="nginx:juice_shop:access" ```

## Splunk Detection Engineering

Because web application payloads are frequently URL-encoded to evade static pattern matching, the SPL detection rule unmasks input string components using urldecode() before evaluating signature conditions.

```bash
index=web sourcetype="nginx:juice_shop:access"
| eval decoded_query=urldecode(query_string)
| where match(decoded_query, "(?i)<script>") 
     OR match(decoded_query, "(?i)javascript:") 
     OR match(decoded_query, "(?i)onerror=") 
     OR match(decoded_query, "(?i)onload=") 
     OR match(decoded_query, "(?i)onclick=")
| eval detection="Potential XSS Attempt"
| table _time detection clientip method uri_path decoded_query status useragent request_time
| sort - _time
```

## Evidence & Screenshots

- Attacker Command & Server Response:

- Nginx Access Log Telemetry:

- Splunk SIEM Detection Alert:


<ElicitationsGroup message="Ready for the next stage?">
  <Elicitation label="Format Stage 2 – SQL Injection" query="Let's build Stage 2 – SQL Injection next."/>
  <Elicitation label="Format Stage 3 – Brute Force Attack" query="Let's build Stage 3 – Brute Force Attack next."/>
</ElicitationsGroup>



# Stage 2 – SQL Injection (Authentication Bypass)

## Objective

Simulate a classic SQL Injection (SQLi) attack targeting the OWASP Juice Shop authentication endpoint (`/rest/user/login`) to bypass authentication mechanisms, gain administrative access, and validate Splunk SPL request-body decoding and detection capabilities.

---

## Attack Execution

An attacker injects a SQL syntax payload into the application login form's `email` input field to force the backend database query to evaluate to `TRUE`.

### Target Endpoint

```http
POST /rest/user/login HTTP/1.1
```

## Attack Vector & Payload

### Vector:
Injected Login Input Parameter (email)

### Payload: 
``` admin' OR '1'='1'-- ```

### URL-Encoded Payload Body: 

```email=admin%27%20OR%20%271%27%3D%271%27--&password=test```

## Result
The database executed the manipulated query, returning a 200 OK HTTP status and authenticating the attacker as admin@juice-sh.op without requiring a valid password.

## Detection & Mapping

- Triggered Detection
- Alert: ```SQL Injection Attempt Detected```

## MITRE ATT&CK Mapping

- Tactic: ```Initial Access / Credential Access```

- Technique: ```T1190 – Exploit Public-Facing Application```

## Primary Telemetry Source

- Log Path: ```/var/log/nginx/juice-shop_access.log```

- Splunk Source: ```index=web sourcetype="nginx:juice_shop:access"```

## Splunk Detection Engineering

Because attackers transmit malicious SQL payloads inside URL-encoded HTTP POST request bodies, the detection pipeline extracts the raw request_body, decodes the string using urldecode(), and checks for standard SQL injection signatures (admin', ' OR '1'='1').

```bash
index=web sourcetype="nginx:juice_shop:access"
| rex field=_raw "^(?<clientip>\S+)\s+\[[^\]]+\]\s+\"(?<method>[^\s]+)\s+(?<uri>[^\s]+).*:request_body=\"(?<request_body>[^\"]*)\""
| where match(request_body, "admin%27") OR match(request_body, "%27%20OR%20%27")
| eval decoded = urldecode(request_body)
| eval Time = strftime(_time, "%Y-%m-%d %H:%M:%S")
| table Time, clientip, method, uri, status, request_body, decoded, _raw
| sort - Time
| rename clientip as "Source IP", request_body as "URL Encoded Payload", decoded as "Decoded SQL Injection", _raw as "Raw Log"
```

## Evidence & Screenshots

- SQL Injection Payload Submission:

- Successful Authentication Bypass (Logged in as Admin):

- Splunk SIEM Detection & Payload Extraction:


<ElicitationsGroup message="Ready for the next stage?">
  <Elicitation label="Format Stage 3 – Brute Force / Credential Stuffing" query="Let's build Stage 3 – Brute Force Attack next."/>
</ElicitationsGroup>



# Stage 3 – Brute Force / Credential Stuffing

## Objective

Simulate an automated password guessing attack against the OWASP Juice Shop authentication endpoint (`/rest/user/login`) using Burp Suite Intruder to identify valid user credentials and construct an aggregation-based Splunk SPL rule to detect brute force attempts and account breaches.

---

## Attack Execution

An attacker intercepts a legitimate authentication request using Burp Suite Proxy and routes it to Burp Suite Intruder. A dictionary attack (Sniper mode) is executed against the `password` field for the target account `admin@juice-sh.op`.

### Target Endpoint

```http
POST /rest/user/login HTTP/1.1
```

## Attack Vector & Tool

### Tool: 
Burp Suite Community Edition (Proxy & Intruder)

### Vector: 
Automated Credential Stuffing / Dictionary Attack

### Target User: ```admin@juice-sh.op```

## Result
Burp Intruder transmitted multiple login attempts. The backend responded with HTTP 401 Unauthorized (status=401, response size 26 bytes) for invalid passwords, before returning an HTTP 200 OK (status=200, response size 784 bytes) upon discovering the valid credential (admin123).

## Detection & Mapping

- Triggered Detection
- Alert: ```Brute Force Attack Detected - Account Breached```

## MITRE ATT&CK Mapping

- Tactic: ```Credential Access```

- Technique: ```T1110.001 – Brute Force: Password Guessing```

## Primary Telemetry Source

- Log Path: ```/var/log/nginx/juice-shop_access.log```

- Splunk Source: ```index=web sourcetype="nginx:juice_shop:access"```

## Splunk Detection Engineering
To differentiate routine login failures from automated password attacks, the SPL query extracts the source IP and targeted username, calculates total, failed (HTTP 401), and successful (HTTP 200) login counts over time, and flags high-volume failure spikes followed by a compromise.

```bash
index=web sourcetype="nginx:juice_shop:access" uri="/rest/user/login"
| rex field=_raw "^(?<src_ip>[^\s]+)"
| rex field=_raw "email.*?(?<target_user>[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})"
| stats 
    count as total_attempts,
    count(eval(status=401)) as failed_attempts,
    count(eval(status=200)) as successful_attempts,
    values(target_user) as targeted_accounts,
    earliest(_time) as attack_start,
    latest(_time) as attack_end
    by src_ip
| where failed_attempts >= 5
| eval 
    attack_duration_seconds = attack_end - attack_start,
    is_compromised = if(successful_attempts > 0, "⚠️ YES - Account Breached!", "❌ No - Blocked")
| fieldformat attack_start = strftime(attack_start, "%Y-%m-%d %H:%M:%S")
| fieldformat attack_end = strftime(attack_end, "%Y-%m-%d %H:%M:%S")
```

## Evidence & Screenshots

- Intercepting Login Request via Burp Proxy:

- Burp Intruder Payload Configuration:

- Burp Intruder Attack Results (HTTP 401 vs HTTP 200):

- Raw Nginx Access Logs in Splunk:

- Splunk Aggregation & Breach Detection Alert:










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
| Initial Access | Cross-Site Scripting (XSS) | Splunk SPL | ✅ In Progress |
| Initial Access | SQL Injection | Splunk SPL | ✅ In Progress |
| Credential Access | Brute Force Attack | Splunk SPL | ✅ In Progress |


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
| XSS Simulation | ✅ In Progress |
| SQL Injection Simulation | ✅ In Progress |
| Brute Force Simulation | ✅ In Progress |
| SPL Detection Rules | ✅ In Progress |
| SOC Investigation Documentation | 🔄 In Progress |


# Disclaimer

This repository is maintained strictly for educational, research, and defensive security engineering purposes.

All attack simulations are performed within an isolated, non-production SOC homelab against explicitly vulnerable targets.

Do not reproduce these activities against systems, applications, networks, or infrastructure without explicit authorization.
