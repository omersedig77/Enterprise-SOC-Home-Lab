# Common Network Ports

## Overview

Network ports identify specific services running on hosts and are essential for communication over TCP and UDP. Understanding common ports helps SOC analysts identify normal network activity, investigate suspicious connections, detect unauthorized services, and recognize attacker behavior.

During incident investigations, analysts frequently correlate network connections, firewall logs, IDS alerts, and endpoint telemetry using port numbers.

---

# Well-Known Ports

| Port | Protocol | Service | Common SOC Use Case |
|------:|:--------:|---------|---------------------|
| 20/21 | TCP | FTP | File transfers, possible data exfiltration |
| 22 | TCP | SSH | Remote administration, brute-force attacks |
| 23 | TCP | Telnet | Legacy remote access, insecure protocol |
| 25 | TCP | SMTP | Email delivery, phishing investigations |
| 53 | TCP/UDP | DNS | Domain resolution, DNS tunneling detection |
| 67/68 | UDP | DHCP | IP address assignment |
| 69 | UDP | TFTP | Unauthorized file transfers |
| 80 | TCP | HTTP | Web browsing, malware downloads |
| 88 | TCP/UDP | Kerberos | Active Directory authentication |
| 110 | TCP | POP3 | Email retrieval |
| 123 | UDP | NTP | Time synchronization |
| 135 | TCP | RPC | Windows RPC communication |
| 137-139 | TCP/UDP | NetBIOS | Windows file and printer sharing |
| 143 | TCP | IMAP | Email retrieval |
| 161 | UDP | SNMP | Network device management |
| 389 | TCP/UDP | LDAP | Active Directory queries |
| 443 | TCP | HTTPS | Secure web traffic |
| 445 | TCP | SMB | Windows file sharing, lateral movement |
| 465 | TCP | SMTPS | Secure email |
| 514 | UDP | Syslog | Network device logging |
| 587 | TCP | SMTP Submission | Secure email sending |
| 636 | TCP | LDAPS | Secure LDAP |
| 993 | TCP | IMAPS | Secure email retrieval |
| 995 | TCP | POP3S | Secure email retrieval |
| 1433 | TCP | Microsoft SQL Server | Database communication |
| 1521 | TCP | Oracle Database | Database communication |
| 3306 | TCP | MySQL | Database communication |
| 3389 | TCP | RDP | Remote Desktop, brute-force target |
| 5432 | TCP | PostgreSQL | Database communication |
| 5900 | TCP | VNC | Remote desktop access |
| 5985 | TCP | WinRM (HTTP) | PowerShell Remoting |
| 5986 | TCP | WinRM (HTTPS) | Secure PowerShell Remoting |
| 8080 | TCP | HTTP Alternate | Web applications, proxies |
| 8443 | TCP | HTTPS Alternate | Secure web applications |

---

# Ports Frequently Targeted by Attackers

| Port | Service | Common Threat |
|------:|---------|---------------|
| 22 | SSH | Brute force attacks |
| 23 | Telnet | Credential attacks |
| 80 | HTTP | Malware delivery |
| 443 | HTTPS | Command & Control (C2), malware downloads |
| 445 | SMB | Lateral movement, ransomware |
| 3389 | RDP | Password spraying, brute force |
| 5900 | VNC | Unauthorized remote access |
| 5985 | WinRM | Remote execution |
| 5986 | WinRM HTTPS | Lateral movement |

---

# Common Malware & C2 Ports

Attackers often use non-standard ports to evade detection.

Examples include:

| Port | Common Use |
|------:|------------|
| 4444 | Metasploit Meterpreter |
| 1337 | Reverse shells (common in labs) |
| 9001 | Custom Command & Control |
| 9000 | Malware communication |
| 5555 | Android Debug Bridge (ADB) or custom malware |
| Random High Ports | Reverse shells and backdoors |

> **Note:** Modern malware can use any port, including common ports such as 80 and 443, to blend in with legitimate traffic.

---

# Ports Used in This SOC Homelab

| Port | Service | Purpose |
|------:|---------|---------|
| 22 | SSH | Linux remote administration and brute-force simulation |
| 80 | HTTP | GoPhish phishing landing page |
| 443 | HTTPS | Secure web traffic |
| 514 | Syslog | pfSense log forwarding |
| 8000 | Splunk Web | Splunk Enterprise management interface |
| 9997 | Splunk Forwarder | Log ingestion from Universal Forwarders |
| 3389 | RDP | Windows remote access and brute-force simulation |
| 4444 | Metasploit Listener | Reverse shell demonstration |

---

# SOC Investigation Examples

## Port 22 (SSH)

Investigate:

- Multiple failed logins
- Successful login after failures
- Unknown source IPs
- Impossible travel
- Off-hours access

Common Log Sources:

- Linux `auth.log`
- Syslog
- Firewall logs

---

## Port 3389 (RDP)

Investigate:

- Failed authentication attempts
- Successful logons after repeated failures
- External connections
- New source IPs

Common Log Sources:

- Windows Security Logs (4624, 4625)
- Sysmon
- Firewall logs

---

## Port 445 (SMB)

Investigate:

- Lateral movement
- File share access
- PsExec activity
- Ransomware behavior

Common Log Sources:

- Windows Security Logs
- Sysmon
- EDR
- Firewall logs

---

## Port 53 (DNS)

Investigate:

- Unusual domains
- High query volume
- Long DNS requests
- DNS tunneling
- Newly registered domains

Common Log Sources:

- DNS server logs
- Sysmon Event ID 22
- Firewall logs

---

## Port 443 (HTTPS)

Investigate:

- Connections to malicious IPs
- PowerShell downloads
- Reverse shells
- Beaconing
- Large outbound transfers

Common Log Sources:

- Sysmon Event ID 3
- Firewall logs
- Proxy logs
- EDR

---

# Tips for SOC Analysts

- Know the purpose of common ports before investigating alerts.
- Pay attention to unusual services running on unexpected ports.
- Correlate network connections with endpoint telemetry.
- Validate destination IPs using threat intelligence.
- Monitor non-standard outbound ports for Command and Control (C2) activity.
- Remember that attackers often use legitimate ports such as 80 and 443 to evade detection.

---

# Key Takeaways

Understanding common network ports is a fundamental SOC skill. Recognizing the services associated with each port, identifying abnormal traffic patterns, and correlating network activity with endpoint telemetry enables analysts to detect reconnaissance, brute-force attacks, malware communication, lateral movement, and data exfiltration more effectively.
