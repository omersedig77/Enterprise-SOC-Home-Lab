# Investigation Process

## Step 1 - Alert Review

Reviewed Splunk alerts related to suspicious Windows activity.

---

## Step 2 - Authentication Analysis

Investigated:

- Failed RDP attempts
- Successful authentication events
- Source IP addresses

---

## Step 3 - Endpoint Analysis

Reviewed Sysmon telemetry for:

- Process execution
- Network connections
- Registry modifications
- Credential dumping behavior

---

## Step 4 - Network Correlation

Firewall logs were reviewed to identify:

- External communication
- Suspicious connections
- Attacker infrastructure

---

## Investigation Result

The investigation confirmed a simulated Windows endpoint compromise.
