# Linux Privilege Escalation Playbook

## Purpose

Investigate activity indicating an attempt to obtain elevated privileges on a Linux endpoint.

---

## Detection

### Data Sources

- /var/log/auth.log
- /var/log/syslog
- Splunk Enterprise

### Indicators

- Excessive sudo usage
- Changes to passwd or group files
- Execution of setuid binaries
- Suspicious administrative commands
- User privilege changes

---

## Investigation

### Step 1

Review authentication logs.

Identify:

- User
- Source IP
- Successful sudo events
- Failed sudo attempts

---

### Step 2

Review account modifications.

Examples:

- passwd
- usermod
- groupadd
- useradd

---

### Step 3

Identify execution of setuid binaries.

Determine:

- Binary executed
- User
- Timestamp

---

### Step 4

Review suspicious administrative commands.

Examples:

- chmod
- chown
- visudo
- su
- sudo

---

### Step 5

Correlate:

- Authentication
- Process execution
- Persistence
- Outbound communication

---

## Recommended Containment

- Disable compromised accounts.
- Restrict privileged access.
- Isolate the endpoint if necessary.

---

## Recommended Eradication

- Remove unauthorized accounts.
- Correct file permissions.
- Remove persistence.

---

## Recommended Recovery

- Review privileged group membership.
- Rotate credentials.
- Continue monitoring authentication logs.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Abuse Elevation Control Mechanism | T1548 |
| Sudo and Sudo Caching | T1548.003 |

---

## Lessons Learned

Monitoring authentication logs and privileged command execution provides valuable visibility into privilege escalation attempts on Linux systems.
