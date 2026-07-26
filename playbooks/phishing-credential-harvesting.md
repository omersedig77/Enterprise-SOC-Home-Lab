# Phishing Credential Harvesting Playbook

## Purpose

Investigate phishing incidents involving credential harvesting and validate user interaction using endpoint telemetry and phishing campaign data.

---

## Detection

### Data Sources

- GoPhish Campaign Logs
- Sysmon Event ID 3 – Network Connection
- Splunk Enterprise

### Indicators

- User opened phishing email
- User clicked malicious link
- Outbound connection to phishing server
- Credential submission recorded

---

## Investigation

### Step 1

Review GoPhish campaign results.

Confirm:

- Email delivered
- Email opened
- Link clicked
- Credentials submitted

---

### Step 2

Review Sysmon network events.

Identify:

- Source host
- Destination IP
- Destination port
- Timestamp

---

### Step 3

Correlate:

- GoPhish timestamps
- Sysmon events
- Splunk searches

---

## Recommended Containment

- Reset affected credentials.
- Remove phishing email from mailboxes.
- Block phishing infrastructure.

---

## Recommended Eradication

- Review additional endpoints.
- Search for similar phishing campaigns.
- Update email security controls if applicable.

---

## Recommended Recovery

- Continue authentication monitoring.
- Conduct user awareness training.
- Review email filtering policies.

---

## MITRE ATT&CK

| Technique | ID |
|-----------|----|
| Phishing | T1566 |
| Spearphishing Link | T1566.002 |

---

## Lessons Learned

Combining phishing campaign data with endpoint telemetry allows analysts to reconstruct the attack timeline and validate user interaction.
