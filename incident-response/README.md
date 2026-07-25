# 🚨 Incident Response

## Overview

This section documents incident investigations performed within the SOC Home-Lab environment.

The objective is to demonstrate the complete SOC analyst workflow:

- Alert identification
- Initial triage
- Evidence collection
- Timeline reconstruction
- Threat analysis
- MITRE ATT&CK mapping
- Response recommendations
- Lessons learned

All incidents documented in this folder were generated through controlled attack simulations performed inside an isolated laboratory environment.

---

# Incident Response Methodology

The investigation process follows the standard incident response lifecycle:
```
Detection
   |
   ↓

Alert Triage
   |
   ↓

Investigation
   |
   ↓

Impact Assessment
   |
   ↓

Containment Recommendation
   |
   ↓

Eradication Recommendation
   |
   ↓

Recovery Recommendation
   |
   ↓

Lessons Learned

```

---

# Incident Sources

The investigations rely on telemetry collected from:

| Source | Purpose |
|---|---|
| Splunk Enterprise | SIEM platform for searching and correlation |
| Windows Event Logs | Authentication and system activity |
| Sysmon | Endpoint detection telemetry |
| Linux Logs | Authentication and system monitoring |
| pfSense Firewall | Network visibility |

---

# Documented Incidents

## 1. Windows Endpoint Compromise

A complete attack lifecycle simulation against Windows 10 including:

- Network reconnaissance
- RDP brute force
- Reverse shell execution
- Persistence
- Privilege escalation
- Credential dumping
- Outbound communication

---

## 2. Linux Endpoint Compromise

A Linux attack simulation including:

- Network scanning
- SSH brute force
- Successful compromise
- Cron persistence
- Post exploitation activity

---

## 3. Phishing Credential Harvesting

A phishing simulation using GoPhish including:

- Email delivery
- User interaction
- Credential harvesting simulation
- Sysmon network investigation

---

# Note

The laboratory simulations focused on detection, investigation, and analysis.

Containment and remediation actions documented in this section represent recommended SOC response procedures.

