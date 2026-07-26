# Windows Registry Run Key Persistence Playbook

## Purpose

Investigate alerts indicating modifications to Windows Registry Run Keys, which may be used by attackers to establish persistence and automatically execute malicious programs during system startup or user logon.

---

## Detection

### Data Sources

- Sysmon Event ID 13 – Registry Value Set
- Sysmon Event ID 12 – Registry Object Create/Delete
- Windows Event Logs
- Splunk Enterprise

### Indicators

- Modification of `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
- Modification of `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- Newly created autorun entries
- Suspicious executable paths

---

## Investigation

### Step 1

Identify:

- Hostname
- User account
- Registry key modified
- Registry value
- Executable path

---

### Step 2

Determine:

- Which process modified the registry
- Parent process
- Execution timestamp

---

### Step 3

Verify whether the executable is legitimate.

Review:

- File location
- Digital signature
- Hash (if available)

---

### Step 4

Determine if additional persistence mechanisms exist.

Examples:

- Scheduled Tasks
- Startup Folder
- Services
- WMI Event Subscriptions

---

## Recommended Containment

- Isolate the endpoint if malicious persistence is confirmed.
- Prevent execution of the malicious executable.
- Disable compromised accounts if necessary.

---

## Recommended Eradication

- Remove malicious Run Key entries.
- Delete associated malware.
- Review additional persistence mechanisms.

---

## Recommended Recovery

- Monitor registry activity.
- Validate startup entries.
- Continue endpoint monitoring.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Registry Run Keys / Startup Folder | T1547.001 |

---

## Lessons Learned

Registry Run Keys remain one of the most common persistence mechanisms used by malware. Monitoring registry modifications provides valuable visibility into attacker persistence.
