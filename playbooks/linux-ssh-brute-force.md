# Linux SSH Brute Force Playbook

## Purpose

Investigate repeated SSH authentication attempts targeting Linux systems.

---

## Detection

Data Sources:

- /var/log/auth.log
- Splunk Enterprise

Indicators:

- Multiple failed SSH logins
- Successful authentication following failures
- Repeated attempts from the same IP address

---

## Investigation

1. Identify source IP.
2. Identify targeted user account.
3. Confirm successful authentication.
4. Review executed commands.
5. Review sudo activity.
6. Review persistence mechanisms.
7. Review outbound network activity.

---

## Recommended Containment

- Disable compromised account
- Terminate active SSH sessions
- Block attacker IP
- Restrict SSH access if appropriate

---

## Recommended Eradication

- Remove malicious cron jobs
- Remove unauthorized SSH keys
- Delete malicious scripts

---

## Recommended Recovery

- Reset credentials
- Validate system integrity
- Continue authentication monitoring

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Brute Force | T1110 |
| Cron | T1053.003 |

---

## Lessons Learned

Authentication logs are one of the most valuable data sources for identifying SSH attacks and validating successful compromises.
