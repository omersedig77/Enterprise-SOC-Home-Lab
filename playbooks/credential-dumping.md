# Credential Dumping Investigation Playbook

## Purpose

Investigate alerts indicating attempts to access credentials stored in system memory, including LSASS dumping and credential extraction tools such as Mimikatz.

---

## Detection

### Data Sources

- Sysmon Event ID 1 – Process Creation
- Sysmon Event ID 10 – Process Access
- Windows Security Logs
- Splunk Enterprise

### Indicators

- Access to LSASS memory
- Mimikatz execution
- Suspicious process access rights
- Credential dumping utilities

---

## Investigation

### Step 1

Identify:

- Hostname
- User
- Executed process
- Parent process

---

### Step 2

Determine whether:

- LSASS was accessed
- Memory dump files were created
- Credential dumping tools were executed

---

### Step 3

Review additional attacker activity:

- Privilege escalation
- Persistence
- Lateral movement
- Network communication

---

## Recommended Containment

- Isolate the endpoint immediately.
- Disable compromised accounts.
- Block attacker communication.

---

## Recommended Eradication

- Remove malicious tools.
- Reset affected credentials.
- Review privileged accounts.

---

## Recommended Recovery

- Force password changes.
- Continue authentication monitoring.
- Review domain controller authentication logs if applicable.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| OS Credential Dumping | T1003 |
| LSASS Memory | T1003.001 |

---

## Lessons Learned

Credential dumping often indicates a mature stage of an intrusion and should be treated as a high-severity incident requiring immediate investigation.
