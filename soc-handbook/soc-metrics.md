# SOC Metrics

## Overview

Security Operations Center (SOC) metrics measure the effectiveness and efficiency of security monitoring, alert triage, incident response, and detection engineering.

These metrics help organizations evaluate analyst performance, identify operational bottlenecks, and continuously improve the SOC.

Understanding these metrics is essential for SOC analysts, detection engineers, and incident responders.

---

# Why SOC Metrics Matter

SOC metrics help teams:

- Measure analyst performance
- Improve incident response
- Reduce attacker dwell time
- Improve detection quality
- Reduce alert fatigue
- Demonstrate security effectiveness
- Track operational improvements
- Support management reporting

---

# MTTD — Mean Time to Detect

## Definition

The average time it takes to detect a security incident after it begins.

Formula

```
MTTD = Total Detection Time / Number of Incidents
```

Example

```
Attack begins

10:00

Alert generated

10:08

MTTD = 8 minutes
```

Lower MTTD is better because attacks are detected sooner.

---

# MTTA — Mean Time to Acknowledge

## Definition

The average time between an alert being generated and an analyst acknowledging it.

Formula

```
MTTA = Total Acknowledgement Time / Number of Alerts
```

Example

```
Alert Generated

09:30

Analyst Acknowledged

09:33

MTTA = 3 minutes
```

This metric reflects analyst responsiveness.

---

# MTTR — Mean Time to Respond / Resolve

## Definition

The average time required to investigate, contain, and close an incident.

Formula

```
MTTR = Total Response Time / Number of Incidents
```

Example

```
Incident Detected

10:15

Incident Closed

11:00

MTTR = 45 minutes
```

Lower MTTR indicates a more efficient response process.

---

# Dwell Time

## Definition

The amount of time an attacker remains in the environment before being detected.

Example

```
Compromise

Monday

Detected

Wednesday

Dwell Time

2 Days
```

Reducing dwell time is a key SOC objective.

---

# False Positive Rate

## Definition

The percentage of alerts that are investigated but determined to be benign.

Formula

```
False Positive Rate =
(False Positive Alerts / Total Alerts) × 100
```

High false positive rates can lead to alert fatigue and wasted analyst effort.

---

# True Positive Rate

## Definition

The percentage of alerts that represent genuine malicious activity.

A higher true positive rate indicates that detections are accurate and effective.

---

# Detection Coverage

## Definition

Measures how much of the attack surface is monitored by existing detection rules.

Coverage can be evaluated across:

- MITRE ATT&CK techniques
- Endpoints
- Network devices
- Cloud environments
- Identity systems

Higher detection coverage improves visibility into attacker behavior.

---

# Alert Volume

## Definition

The total number of alerts generated during a given period.

Monitoring alert volume helps identify:

- Detection tuning opportunities
- Noisy rules
- Emerging attack trends
- Analyst workload

---

# Alert Fidelity

## Definition

Alert fidelity measures how trustworthy an alert is.

High-fidelity alerts:

- Have a low false positive rate
- Provide strong evidence of malicious activity
- Require less manual validation

Examples:

- LSASS memory access
- Mimikatz execution
- Reverse shell detection

---

# Escalation Rate

## Definition

The percentage of alerts escalated to higher-tier analysts or incident responders.

A consistently high escalation rate may indicate:

- Complex threats
- Insufficient analyst training
- Poor documentation
- Overly broad detection rules

---

# Incident Closure Rate

## Definition

The number of incidents successfully resolved within a reporting period.

This metric helps track SOC productivity and operational capacity.

---

# SLA Compliance

## Definition

Service Level Agreements (SLAs) define expected response times for different alert severities.

Example

| Severity | Target Response Time |
|----------|----------------------:|
| Critical | 15 minutes |
| High | 30 minutes |
| Medium | 2 hours |
| Low | 1 business day |

Meeting SLAs helps ensure timely incident handling.

---

# Dashboard Metrics

A SOC dashboard commonly includes:

- Total Alerts
- Open Incidents
- Closed Incidents
- MTTD
- MTTA
- MTTR
- Alert Severity Distribution
- Top Alert Types
- Top Affected Hosts
- Top Source IPs
- MITRE ATT&CK Coverage
- False Positive Rate

These metrics provide analysts and managers with a real-time view of SOC performance.

---

# Best Practices

- Monitor metrics consistently over time.
- Focus on improving detection quality, not just alert quantity.
- Tune noisy detection rules to reduce false positives.
- Review metrics regularly to identify operational improvements.
- Align metrics with organizational goals and service level objectives.

---

# Key Takeaways

SOC metrics provide measurable insight into the performance of security operations. Metrics such as MTTD, MTTA, MTTR, false positive rate, and detection coverage help teams evaluate their effectiveness, improve incident response, reduce attacker dwell time, and demonstrate the value of the SOC to stakeholders.
