# Linux Data Onboarding

## Overview

The Ubuntu endpoint was configured to forward system logs to Splunk Enterprise using the Splunk Universal Forwarder. This provides centralized visibility into authentication events, kernel activity, and system operations, allowing Linux-based attack simulations to be detected and investigated.

The endpoint serves as the monitored Linux system within the SOC Homelab and plays a key role in validating detection logic against Linux-specific attack techniques.

---

# Splunk Universal Forwarder Installation

The Splunk Universal Forwarder was installed using the official Debian package.

The forwarder was configured to:

- Run as a system service
- Start automatically during system boot
- Forward logs directly to the Splunk Enterprise server
- Monitor selected log files continuously

---

# Forwarding Configuration

| Setting | Value |
|----------|-------|
| Destination | 192.168.50.101 |
| Receiving Port | 9997 |

---

# Linux Data Sources

The following log files are monitored.

## syslog

```
/var/log/syslog
```

Provides visibility into:

- System activity
- Service events
- General operating system messages
- Scheduled tasks

---

## auth.log

```
/var/log/auth.log
```

Provides visibility into:

- User authentication
- SSH logins
- Failed login attempts
- Sudo usage
- Privilege escalation attempts

---

## kern.log

```
/var/log/kern.log
```

Provides visibility into:

- Kernel messages
- Hardware events
- Driver activity
- System-level operations

---

# Verification

Successful onboarding was verified by confirming that Linux events were searchable within the **linux_logs** index.

Verification included:

- Continuous log ingestion
- Authentication events
- System log events
- Kernel log events

---

# Security Value

The Linux endpoint provides critical visibility into operating system activity that supports:

- SSH monitoring
- Authentication monitoring
- Privilege escalation detection
- Command execution monitoring
- Linux attack simulations
- Incident investigations

These logs form the foundation for the Linux detection rules and attack scenarios documented later in this project.
