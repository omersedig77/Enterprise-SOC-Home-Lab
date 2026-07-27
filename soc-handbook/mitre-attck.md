# MITRE ATT&CK

MITRE ATT&CK (Adversarial Tactics, Techniques, and Common Knowledge) is a knowledge base that documents how real-world adversaries operate after gaining access to a system.

Rather than focusing on malware families, MITRE ATT&CK categorizes attacker behaviors into tactics and techniques. Security Operations Centers (SOCs) use this framework to develop detections, investigate incidents, perform threat hunting, and measure detection coverage.

Many SIEM detections, EDR products, and threat intelligence reports map directly to MITRE ATT&CK techniques.


Common techniques demonstrated within this project include:

| Technique | ID |
|----------|------|
| Active Scanning | T1595 |
| Brute Force | T1110 |
| Ingress Tool Transfer | T1105 |
| Command and Scripting Interpreter | T1059 |
| Registry Run Keys | T1547.001 |
| Cron | T1053.003 |
| Unquoted Service Path | T1574.009 |
| LSASS Memory | T1003.001 |
| Exfiltration Over C2 | T1041 |


# Why MITRE ATT&CK Matters

SOC analysts use MITRE ATT&CK to:

- Understand attacker behavior
- Map alerts to known techniques
- Improve detection engineering
- Guide threat hunting
- Measure security coverage
- Prioritize investigations
- Communicate findings using a common language

---

# ATT&CK Tactics

| Tactic | Description |
|---------|-------------|
| Reconnaissance | Gather information before attacking |
| Resource Development | Acquire infrastructure and tools |
| Initial Access | Gain entry into the target environment |
| Execution | Run malicious code |
| Persistence | Maintain long-term access |
| Privilege Escalation | Gain higher privileges |
| Defense Evasion | Avoid detection |
| Credential Access | Steal credentials |
| Discovery | Learn about the environment |
| Lateral Movement | Move between systems |
| Collection | Gather sensitive information |
| Command and Control | Communicate with compromised systems |
| Exfiltration | Steal data |
| Impact | Disrupt or destroy systems |

---

# Common Techniques Every SOC Analyst Should Know

## T1595 – Active Scanning

Tactic

Reconnaissance

Description

Attackers scan networks to discover hosts, services, and open ports.

Examples

- Nmap
- Masscan

Detection

- Firewall logs
- Windows Event ID 5156
- IDS alerts

---

## T1110 – Brute Force

Tactic

Credential Access

Description

Repeated authentication attempts against accounts.

Examples

- Hydra
- Medusa

Detection

Windows

- Event ID 4625
- Event ID 4624

Linux

- auth.log
- SSH failed logins

---

## T1059 – Command and Scripting Interpreter

Tactic

Execution

Description

Execution of malicious commands using interpreters.

Examples

- PowerShell
- CMD
- Bash
- Python

Detection

Sysmon Event ID 1

Windows Event ID 4688

---

## T1105 – Ingress Tool Transfer

Tactic

Command and Control

Description

Downloading tools or malware onto a compromised system.

Examples

- certutil
- curl
- wget
- Invoke-WebRequest

Detection

- Sysmon Event ID 1
- Sysmon Event ID 3

---

## T1547.001 – Registry Run Keys

Tactic

Persistence

Description

Maintaining persistence through Windows Registry Run Keys.

Detection

- Sysmon Event ID 13

---

## T1053.003 – Cron

Tactic

Persistence

Description

Using cron jobs for persistence on Linux systems.

Detection

- /var/log/syslog
- Cron activity
- Splunk searches

---

## T1574.009 – Unquoted Service Path

Tactic

Privilege Escalation

Description

Abusing improperly configured Windows services.

Detection

- Event ID 7045
- Event ID 4697
- Service creation events

---

## T1003.001 – LSASS Memory

Tactic

Credential Access

Description

Dumping credentials from LSASS memory.

Examples

- Mimikatz
- ProcDump

Detection

- Sysmon Event ID 10

---

## T1055 – Process Injection

Tactic

Defense Evasion

Description

Injecting malicious code into another process.

Detection

- Sysmon Event ID 8
- Sysmon Event ID 10

---

## T1071 – Application Layer Protocol

Tactic

Command and Control

Description

Using HTTP, HTTPS, DNS, or other protocols for C2 communication.

Detection

- Sysmon Event ID 3
- Firewall logs
- Proxy logs

---

## T1041 – Exfiltration Over C2 Channel

Tactic

Exfiltration

Description

Sending stolen data through existing command-and-control channels.

Detection

- Sysmon Event ID 3
- Firewall logs
- Proxy logs

---

# MITRE Techniques Used in This SOC Homelab

| Detection | MITRE Technique |
|-----------|-----------------|
| Inbound Network Port Scanning | T1595 |
| RDP Brute Force | T1110 |
| SSH Brute Force | T1110 |
| Reverse Shell | T1071 |
| PowerShell Download | T1105 |
| Registry Run Keys | T1547.001 |
| Cron Persistence | T1053.003 |
| Unquoted Service Path | T1574.009 |
| LSASS Dump | T1003.001 |
| Mimikatz | T1003.001 |
| Data Exfiltration | T1041 |

---

# Best Practices

- Map every detection to a MITRE ATT&CK technique.
- Use ATT&CK to identify gaps in detection coverage.
- Include MITRE IDs in alerts, dashboards, and incident reports.
- Review ATT&CK regularly as new techniques are published.

---

# Key Takeaways

MITRE ATT&CK provides a common framework for understanding adversary behavior. By mapping detections and investigations to ATT&CK techniques, SOC analysts can improve detection coverage, communicate findings consistently, and better understand how attackers progress through the stages of an intrusion.

