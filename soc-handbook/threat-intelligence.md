# Threat Intelligence

## Overview

Threat Intelligence (TI) is the process of collecting, analyzing, and applying information about cyber threats to improve an organization's ability to detect, investigate, and respond to attacks.

Rather than simply reacting to alerts, SOC analysts use threat intelligence to understand who may be attacking, what infrastructure they use, which techniques they employ, and how to prioritize investigations.

Threat intelligence transforms raw data into actionable information that supports faster and more accurate incident response.

---

# Why Threat Intelligence Matters

Threat Intelligence helps SOC teams:

- Detect malicious infrastructure
- Identify known attacker IP addresses
- Investigate suspicious domains
- Analyze malware hashes
- Correlate indicators of compromise (IOCs)
- Improve detection rules
- Prioritize high-risk alerts
- Reduce false positives
- Support threat hunting activities

Without threat intelligence, analysts often investigate events without important context.

---

# Types of Threat Intelligence

## 1. Strategic Intelligence

Audience:

- Executives
- Security Managers
- CISOs

Focus:

- Threat landscape
- Industry trends
- Emerging threats
- Risk assessment

Example:

> Ransomware attacks against healthcare organizations have increased by 40% this quarter.

---

## 2. Operational Intelligence

Audience:

- SOC Analysts
- Incident Responders

Focus:

- Active campaigns
- Threat actor activity
- Current malware families
- Attack infrastructure

Example:

A ransomware group is actively exploiting VPN appliances.

---

## 3. Tactical Intelligence

Focus:

Adversary behavior

Includes:

- MITRE ATT&CK techniques
- Initial access methods
- Persistence methods
- Credential theft
- Lateral movement
- Command and Control

Example:

APT29 commonly abuses PowerShell and scheduled tasks for persistence.

---

## 4. Technical Intelligence

Technical intelligence contains Indicators of Compromise (IOCs).

Examples include:

- IP addresses
- Domains
- URLs
- File hashes
- Email addresses
- SSL certificates
- User agents
- Mutex names

Technical intelligence is the type used most frequently inside a SOC.

---

# Indicators of Compromise (IOCs)

Common IOC categories include:

## IP Address

Example:

```
185.220.101.25
```

Used for:

- Firewall investigation
- Network monitoring
- Threat hunting

---

## Domain

Example

```
malicious-example.com
```

Used for:

- DNS monitoring
- Web proxy logs
- Email investigations

---

## URL

Example

```
http://malicious-site.com/login
```

Used for:

- Phishing investigations
- Malware delivery

---

## File Hash

Examples

MD5

```
44d88612fea8a8f36de82e1278abb02f
```

SHA1

```
3395856ce81f2b7382dee72602f798b642f14140
```

SHA256

```
275a021bbfb6485c4fddf8d3a0c5ef2a0d2d6dfdf87b5fd88dcbeb4bba96c31d
```

Used for:

- Malware identification
- VirusTotal searches
- EDR investigations

---

## Email Address

Example

```
support@fake-company.com
```

Used during:

- Phishing investigations
- Business Email Compromise (BEC)

---

# Indicators of Attack (IOAs)

Unlike IOCs, IOAs describe attacker behavior rather than known artifacts.

Examples include:

- PowerShell encoded commands
- Mimikatz execution
- LSASS memory access
- Registry Run Key modifications
- Reverse shell connections
- Scheduled task creation
- Suspicious service installation

Behavior-based detections remain effective even if attackers change their infrastructure.

---

# Threat Intelligence Sources

## VirusTotal

Used for:

- File hash reputation
- URL analysis
- Domain reputation
- IP reputation

Best for:

- Malware investigations
- Suspicious downloads

---

## AbuseIPDB

Used for:

- Malicious IP reputation
- Abuse reports
- Brute force sources

Best for:

- Firewall alerts
- SSH brute force investigations

---

## AlienVault OTX

Provides:

- Community threat intelligence
- IOC feeds
- Malware indicators
- Threat pulses

Useful for:

- Threat hunting
- IOC enrichment

---

## Cisco Talos Intelligence

Provides:

- IP reputation
- Domain reputation
- Spam reputation
- Malware intelligence

Useful during:

- Email investigations
- Firewall investigations

---

## GreyNoise

Focuses on:

Internet background noise.

Useful for determining whether a scanner is:

- Internet-wide scanning
- Vulnerability research
- Known benign scanner
- Malicious actor

Extremely useful during port scan investigations.

---

## URLhaus

Focus:

Malware URLs.

Useful during:

- Phishing investigations
- Malware download analysis

---

## MalwareBazaar

Provides:

- Malware samples
- SHA256 hashes
- Malware families

Useful for:

- Malware research
- Detection engineering

---

## MITRE ATT&CK

Provides:

- Adversary techniques
- Detection guidance
- Mitigation recommendations

One of the most important references for detection engineering.

---

# When SOC Analysts Use Threat Intelligence

Threat Intelligence is commonly used during:

## Alert Triage

Example:

Alert shows outbound connection to:

```
103.x.x.x
```

The analyst checks:

- VirusTotal
- AbuseIPDB
- GreyNoise

to determine whether the IP is malicious.

---

## Incident Investigation

Questions answered include:

- Has this IP been reported?
- Is the domain malicious?
- Is the file hash known malware?
- Is this attacker associated with ransomware?

---

## Threat Hunting

Threat hunters search their environment for:

- Known malicious IPs
- Domains
- File hashes
- Registry keys
- PowerShell commands

before alerts are triggered.

---

## Detection Engineering

Threat Intelligence helps build detections based on:

- Known attacker behavior
- Emerging malware
- Current attack campaigns

---

# IOC Enrichment Workflow

```
Splunk Alert
      │
      ▼
Extract IOC
(IP, Domain, Hash)
      │
      ▼
Threat Intelligence Lookup
      │
      ▼
VirusTotal
AbuseIPDB
GreyNoise
OTX
Talos
      │
      ▼
Determine Reputation
      │
      ▼
Benign
or
Malicious
      │
      ▼
Continue Investigation
```

---

# Example Investigation

Alert:

```
Reverse Shell Connection
Destination:

192.168.50.40
```

Investigation:

1. Identify destination IP.

2. Check AbuseIPDB.

3. Search VirusTotal.

4. Review firewall logs.

5. Review Sysmon Event ID 3.

6. Correlate process creation.

7. Determine whether the destination is malicious.

---

# Best Practices

- Never rely on a single intelligence source.
- Correlate multiple IOC providers.
- Validate intelligence before blocking.
- Focus on behavior, not only indicators.
- Continuously update detection rules using new intelligence.
- Use MITRE ATT&CK to map attacker techniques.
- Document enriched IOCs within incident investigations.

---

# Key Takeaways

Threat Intelligence adds context to security alerts, enabling SOC analysts to distinguish between benign activity and real threats. By enriching investigations with indicators of compromise, adversary behavior, and reputation data, analysts can triage alerts more efficiently, accelerate investigations, improve detection quality, and support proactive threat hunting.
