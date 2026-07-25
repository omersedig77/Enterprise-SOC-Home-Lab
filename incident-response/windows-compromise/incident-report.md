# Incident Report: Windows Endpoint Compromise

## Incident ID

IR-001

## Severity

Critical

## Incident Type

Endpoint Compromise

## Affected Asset

Windows 10 Endpoint

IP Address:

192.168.50.20

## Attacker System

Kali Linux

IP Address:

192.168.50.40

## Detection Platform

Splunk Enterprise

IP Address:

192.168.50.101

---

# Summary

A simulated multi-stage attack was performed against the Windows endpoint.

The attack demonstrated a complete compromise lifecycle including:

- Reconnaissance
- RDP brute force
- Reverse shell execution
- Persistence
- Privilege escalation
- Credential access
- Outbound communication

Security telemetry from Windows Event Logs, Sysmon, and pfSense firewall logs was collected and analyzed through Splunk Enterprise.

---

# Impact Assessment

Observed attacker activities:

- Unauthorized remote access
- Malware execution simulation
- Persistence creation
- Credential dumping behavior
- Suspicious outbound communication

---

# Detection

The incident was detected through custom Splunk detections using:

- Windows Security logs
- Sysmon telemetry
- Firewall events

---

# Status

Detection and investigation completed.

Response actions are documented as recommendations.
