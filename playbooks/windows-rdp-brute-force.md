# Windows RDP Brute Force Playbook

## Purpose

Investigate repeated Remote Desktop Protocol (RDP) authentication attempts that may indicate a brute-force attack against a Windows endpoint.

---

## Detection

**Data Sources**

- Windows Security Event Logs
- Sysmon
- Splunk Enterprise

Typical indicators include:

- Multiple failed RDP logins
- Successful login after repeated failures
- Authentication attempts from a single source IP

---

## Investigation

### Step 1

Identify:

- Source IP
- Destination host
- Target user account

---

### Step 2

Determine:

- Number of failed logins
- Time window
- Authentication pattern

---

### Step 3

Determine whether a successful login occurred after multiple failed attempts.

---

### Step 4

Review activity performed after authentication:

- Process creation
- PowerShell execution
- Network connections
- Privilege escalation
- Persistence

---

## Recommended Containment

- Disable compromised account
- Reset user credentials
- Block malicious source IP
- Isolate affected endpoint if compromise is confirmed

---

## Recommended Eradication

- Remove persistence mechanisms
- Review newly created accounts
- Scan for malware
- Review scheduled tasks and services

---

## Recommended Recovery

- Restore normal user access
- Continue monitoring authentication activity
- Verify no additional persistence remains

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Brute Force | T1110 |
| Valid Accounts | T1078 |

---

## Lessons Learned

Successful authentication following repeated failed login attempts should be treated as a high-priority event and investigated immediately.
