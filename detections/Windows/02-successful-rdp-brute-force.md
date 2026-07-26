# Credential Access - Successful RDP Brute Force Compromise

## Detection Metadata

| Field | Value |
|---|---|
| Detection Type | Credential Access / Brute Force |
| Platform | Windows |
| Data Source | Windows Security Event Logs |
| Event IDs | 4625 - Failed Logon, 4624 - Successful Logon |
| Severity | Medium |
| MITRE ATT&CK | T1110 - Brute Force |
| Schedule | Every 5 minutes |
| Alert Type | Scheduled Detection |
| Status | Validated |

---

# Overview

This detection identifies successful Remote Desktop Protocol (RDP) compromises by detecting a pattern of multiple failed authentication attempts followed by a successful login from the same source IP address.

RDP brute-force attacks are commonly used by attackers to obtain valid credentials and gain initial access to Windows systems.

The detection focuses on authentication behavior rather than specific usernames, allowing identification of automated password guessing attempts against multiple accounts.

This detection was validated during the Windows Attack Simulation using Hydra against the Windows endpoint.

---

# Detection Objective

The objective of this detection is to identify:

- RDP password spraying attacks
- Automated credential guessing
- Successful compromise after brute-force activity
- Unauthorized remote access attempts
- External attackers obtaining valid credentials

---

# Attack Scenario

During validation, the attacker performed a brute-force attack against the Windows Remote Desktop service.

Attacker:

```
Kali Linux
192.168.50.40
```

Target:

```
Windows 10
192.168.50.20
```

Attack tool:

```
Hydra
```

Attack flow:

```
Multiple Failed RDP Logins
          |
          |
Valid Credentials Discovered
          |
          |
Successful RDP Authentication
          |
          |
Splunk Detection Triggered
```

---

# Data Sources

## Windows Security Event Logs

Successful authentication:

```
EventCode=4624
```

Failed authentication:

```
EventCode=4625
```

Collected using:

```
Splunk Universal Forwarder
        |
        |
Splunk Enterprise
```

Index:

```
wineventlog
```

---

# SPL Detection

```spl
index=wineventlog (EventCode=4625 OR EventCode=4624)
| eval attacker_ip = Source_Network_Address
| eval victim_host = ComputerName
| eval source_workstation = coalesce(Workstation_Name, Workstation, "Unknown_Workstation")
| rex field=_raw "TargetUserName\s*=\s*\"(?<extracted_user>[^\"]+)\""
| stats 
    count(eval(EventCode=4625)) as failed_attempts
    count(eval(EventCode=4624)) as successful_attempts
    values(extracted_user) as targeted_accounts
    min(_time) as first_seen
    max(_time) as last_seen
    by attacker_ip, source_workstation, victim_host
| where failed_attempts > 5
| eval "Attack Start"=strftime(first_seen, "%Y-%m-%d %H:%M:%S")
| eval "Attack End"=strftime(last_seen, "%Y-%m-%d %H:%M:%S")
| rename attacker_ip as "Attacker IP", source_workstation as "Attacker Hostname", victim_host as "Target Host", failed_attempts as "Failed Tries Before Success", successful_attempts as "Success Count"
| table "Attacker IP", "Attacker Hostname", "Target Host", "Attack Start", "Attack End", "Failed Tries Before Success", "Success Count"
```

---

# Detection Logic Explanation

The detection analyzes Windows authentication activity and searches for:

1. Multiple failed authentication attempts.

2. A successful authentication event.

3. Both events originating from the same source.

The logic assumes that a successful login after multiple failures may indicate compromised credentials.

The threshold:

```
Failed Attempts > 5
```

helps reduce normal user authentication mistakes while identifying suspicious authentication patterns.

---

# Alert Configuration

## Severity

```
Medium
```

---

## Trigger Condition

The alert triggers when:

- A source IP generates more than five failed authentication attempts.
- A successful authentication occurs from the same source.
- The activity targets the same Windows endpoint.

This indicates a possible successful RDP brute-force compromise.

---

## Schedule

```
Every 5 minutes
```

---

# Investigation Guidance

When this alert triggers, analysts should investigate:

---

## 1. Validate the Source IP

Determine:

- Is the source internal or external?
- Does the source belong to an administrator?
- Is the source expected?

Review:

```
Attacker IP
```

---

## 2. Review Authentication Details

Investigate:

- Username targeted
- Login time
- Source workstation
- Authentication type

Important fields:

```
Target Account
Source Address
Logon Type
Computer Name
```

---

## 3. Check Post-Authentication Activity

After successful RDP access, review:

Windows Security Logs:

```
4624
Successful Logon
```

Sysmon:

```
Event ID 1
Process Creation
```

Look for:

- PowerShell execution
- Payload execution
- Credential dumping
- Persistence creation

---

## 4. Correlate Network Activity

Review:

pfSense logs:

- Source IP
- Destination IP
- RDP traffic
- Allowed connections

---

# False Positives

Possible legitimate causes:

- Users entering incorrect passwords
- Forgotten credentials
- Helpdesk troubleshooting
- Remote administration activity

Additional validation should be performed before escalation.

---

# Validation

This detection was validated during the Windows Attack Simulation.

Attack technique:

```
T1110 - Brute Force
```

Simulation:

```
Kali Linux
     |
     |
Hydra RDP Brute Force
     |
     |
Windows 10 Endpoint
     |
     |
Event ID 4625 / 4624
     |
     |
Splunk Alert
```

Result:

```
Detection Triggered Successfully
```

---

# Related Playbooks

Related incident response procedures:

```
incident-response/playbooks/account-compromise.md

incident-response/playbooks/windows-endpoint-compromise.md
```

---

# Lessons Learned

Successful authentication following repeated failures is a strong indicator of credential compromise.

This detection demonstrates how authentication telemetry can be used to identify brute-force attacks and provide early warning before attackers continue with persistence, privilege escalation, or lateral movement.
