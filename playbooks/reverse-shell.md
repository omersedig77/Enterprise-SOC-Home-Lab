# Reverse Shell Investigation Playbook

## Purpose

Investigate alerts indicating that an endpoint established an outbound connection to a remote system, potentially providing an attacker with interactive access.

---

## Detection

Potential indicators:

- Suspicious outbound connections
- PowerShell network activity
- Unknown executable initiating external communication
- Sysmon Event ID 3

---

## Investigation

Review:

- Parent process
- Child process
- Destination IP
- Destination port
- User context
- Command-line arguments

Correlate endpoint telemetry with firewall logs.

---

## Recommended Containment

- Isolate affected endpoint
- Block command-and-control destination
- Terminate malicious process

---

## Recommended Eradication

- Remove malicious executable
- Remove persistence
- Perform malware analysis if applicable

---

## Recommended Recovery

- Validate endpoint integrity
- Continue monitoring for recurring outbound communication

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Command and Scripting Interpreter | T1059 |
| Application Layer Protocol | T1071 |

---

## Lessons Learned

Outbound network connections initiated by uncommon processes should be investigated immediately to identify potential command-and-control activity.
