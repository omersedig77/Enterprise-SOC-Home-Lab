# Alert Triage

## Overview

Alert triage is the initial process of evaluating security alerts to determine their validity, severity, and required response.

The goal is to quickly identify high-priority incidents while reducing time spent on false positives.

---

## Triage Workflow
```
Alert Generated

      ↓

Validate Alert

      ↓

Identify Affected Asset

      ↓

Review Related Events

      ↓

Determine Severity

      ↓

 Investigate

      ↓

Escalate or Close
```

---

## Questions to Ask

- What triggered the alert?
- Which host is affected?
- Which user is involved?
- What process was executed?
- What network connections occurred?
- Is this expected behavior?
- Has this happened before?

---

## Possible Outcomes

- False Positive
- Benign Activity
- Suspicious Activity
- Confirmed Security Incident

---

## Best Practices

- Never assume an alert is malicious.
- Gather evidence before making conclusions.
- Correlate endpoint and network telemetry.
- Document investigation findings.
