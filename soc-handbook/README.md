# SOC Handbook

## Overview

The SOC Handbook is a collection of practical reference material designed to support day-to-day Security Operations Center (SOC) activities. It complements the SOC Homelab by documenting the concepts, event IDs, frameworks, investigation techniques, and operational knowledge that SOC analysts commonly rely on during security monitoring and incident investigations.

Rather than focusing on lab implementation, this handbook serves as a quick-reference guide covering Windows security events, Sysmon telemetry, threat intelligence, incident handling, threat hunting, SOC metrics, and other foundational topics encountered in enterprise environments.

The objective is to provide a centralized knowledge base that can be used alongside the technical components of this project to better understand how security events are generated, investigated, and interpreted throughout the incident response lifecycle.

---

# Handbook Structure

## Fundamentals

Core Windows and Linux telemetry commonly used during investigations.

- Windows Security Event IDs
- Sysmon Event IDs
- Windows Logon Types
- Common Network Ports
- Linux Log Cheat Sheet

---

## Detection & Investigation

Guides focused on alert analysis, detection engineering, and investigations.

- Threat Intelligence
- Splunk SPL Cheat Sheet
- Threat Hunting Methodology
- Investigation Checklists

---

## Security Frameworks

Industry-standard frameworks frequently referenced by SOC analysts.

- MITRE ATT&CK
- Cyber Kill Chain
- Pyramid of Pain

---

## SOC Operations

Operational concepts used within modern Security Operations Centers.

- Incident Lifecycle
- Alert Triage
- Incident Severity Classification
- SOC Metrics (MTTD, MTTA, MTTR)
- Malware Analysis Basics
- Common Offensive Security Tools

---

# Purpose

The purpose of this handbook is to:

- Build a strong understanding of SOC operations.
- Provide quick-reference documentation during investigations.
- Support detection engineering efforts.
- Improve familiarity with Windows, Linux, and network telemetry.
- Document common attacker behaviors and defensive concepts.
- Reinforce practical knowledge gained through the SOC Homelab.

---

# Intended Audience

This handbook is intended for:

- SOC Analysts (Tier 1 / Tier 2)
- Blue Team Practitioners
- Detection Engineers
- Cybersecurity Students
- Home Lab Builders
- Anyone preparing for SOC analyst interviews

---

# Relationship to This Project

The documentation contained within this handbook directly complements the attack simulations, detections, dashboards, alerts, and incident response playbooks included throughout this repository.

Many of the concepts described here are demonstrated practically within the Windows attack simulation, Linux attack simulation, phishing campaign, and custom Splunk detections developed as part of the SOC Homelab.
