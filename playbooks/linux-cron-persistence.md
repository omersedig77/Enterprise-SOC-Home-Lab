# Linux Cron Persistence Playbook

## Purpose

Investigate unauthorized cron jobs that may be used by attackers to maintain persistence on Linux systems.

---

## Detection

### Data Sources

- /var/log/syslog
- /var/log/auth.log
- Splunk Enterprise

### Indicators

- New cron jobs
- Modified cron schedules
- Reverse shell commands
- Suspicious scripts executed by cron

---

## Investigation

### Step 1

Identify:

- User account
- Cron schedule
- Executed command

---

### Step 2

Review:

- Recently modified cron files
- Executed scripts
- File permissions

---

### Step 3

Determine whether the command initiates:

- Reverse shell
- External connection
- Malicious script execution

---

### Step 4

Review additional persistence mechanisms.

Examples:

- SSH Authorized Keys
- Systemd Services
- Bash Profiles

---

## Recommended Containment

- Disable the malicious cron job.
- Isolate the host if compromise is confirmed.
- Restrict unauthorized user access.

---

## Recommended Eradication

- Remove malicious cron entries.
- Delete associated scripts.
- Rotate compromised credentials.

---

## Recommended Recovery

- Validate cron configuration.
- Continue monitoring scheduled tasks.
- Review authentication activity.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Cron | T1053.003 |

---

## Lessons Learned

Cron jobs are commonly abused for persistence. Monitoring scheduled task activity improves visibility into long-term attacker access.
