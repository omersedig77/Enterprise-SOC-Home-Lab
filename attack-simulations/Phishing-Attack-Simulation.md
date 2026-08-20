# Phishing Attack Simulation – Credential Harvesting Validation

## Overview

This simulation demonstrates a phishing campaign executed within the SOC Homelab using GoPhish.

The objective was to simulate a realistic credential harvesting attack, monitor user interaction throughout the phishing process, and validate detection capabilities using Windows Sysmon telemetry collected by Splunk Enterprise.

All activities were performed within an isolated lab environment for educational, defensive, and detection engineering purposes.

---

# Objectives

The objectives of this simulation were to:

- Simulate a realistic phishing campaign.
- Deliver a phishing email to a Windows endpoint.
- Track user interaction with the phishing email.
- Capture simulated credential submissions.
- Validate detection using Windows Sysmon network telemetry.
- Demonstrate the complete phishing attack lifecycle.

---

# Lab Systems

| System | Role | IP Address |
|----------|------|------------|
| Kali Linux | GoPhish Server | 192.168.50.40 |
| Windows 10 | Victim | 192.168.50.20 |
| Splunk Enterprise | SIEM | 192.168.50.101 |

---

# Attack Flow

```
Attacker Creates Campaign
          │
          ▼
Custom Email Template
          │
          ▼
Phishing Email Delivered
          │
          ▼
Victim Opens Email
          │
          ▼
Victim Clicks Malicious Link
          │
          ▼
Fake Google Password Update Page
          │
          ▼
Victim Submits Credentials
          │
          ▼
GoPhish Records User Actions
          │
          ▼
Sysmon Network Events Generated
          │
          ▼
  Splunk Detection
          │
          ▼
  SOC Investigation
```

---

# Campaign Configuration

## Framework

- GoPhish

---

## Campaign Name

```
Google Password Expired - Training
```

---

## Email Template

```
Password Expired
```

Subject:

```
Action Required: Your Password Has Expired
```

---

## Landing Page

```
Google Password Update
```

The landing page was designed to closely resemble a legitimate Google password update page in order to simulate a realistic phishing scenario.

---

## Target User

A test user was created within GoPhish using a controlled email account.

---

## Phishing URL

```
http://192.168.50.40
```

The landing page was hosted on the Kali Linux system running GoPhish.

---

# Attack Stages

## Stage 1 – Email Delivery

The phishing email was successfully delivered to the Windows endpoint.

GoPhish immediately confirmed:

- Email Sent

### Screenshots

- GoPhish campaign dashboard
  <img width="1713" height="1021" alt="27 The email have been sent to the target" src="https://github.com/user-attachments/assets/a966fe5e-a432-4b56-96b5-80e4b9f16caa" />

- Email received in inbox
  <img width="1710" height="1010" alt="28 The target recevied the email" src="https://github.com/user-attachments/assets/5319a405-3fd6-4b0a-99db-6d2da07b0afe" />


---

## Stage 2 – Email Opened

The victim opened the phishing email.

GoPhish immediately updated the campaign status to indicate:

- Email Opened

This demonstrates user engagement with the phishing message.

### Screenshots

- Email opened
- GoPhish dashboard status
<img width="3437" height="1438" alt="29 the form and the gophish showm the email have been opened and link clicked" src="https://github.com/user-attachments/assets/2f97a523-7dee-40b1-bc51-470347f2499c" />

---

## Stage 3 – Link Click

The victim clicked the **Update Password** button contained within the email.

GoPhish immediately recorded:

- Link Clicked

The victim was redirected to the phishing landing page hosted on the Kali Linux system.

### Screenshots

- Email link
- Landing page
- GoPhish click event
<img width="3437" height="1438" alt="29 the form and the gophish showm the email have been opened and link clicked" src="https://github.com/user-attachments/assets/b67440a0-c863-481b-a991-63ec87dc687a" />

---

## Stage 4 – Credential Submission

The victim entered:

- Email address
- Current password
- New password
- Password confirmation

After selecting **Change Password**, the submitted data was recorded by GoPhish.

This stage simulates credential harvesting in a controlled environment.

### Screenshots

- Fake Google page
  <img width="1710" height="1332" alt="30 the victim fills the fake form" src="https://github.com/user-attachments/assets/9235d226-e3bc-4944-a479-47b57b11ecaf" />

- Credential submission
  <img width="1715" height="1017" alt="31 The Gophish show the victim have submit data using the fake form" src="https://github.com/user-attachments/assets/1eff0e4e-2ff7-4187-9a23-7bc4633d8fc8" />

- GoPhish captured data
<img width="1705" height="1277" alt="32 This is the victim informations" src="https://github.com/user-attachments/assets/7262d8b1-ed88-4b8e-a3e3-0b7372227a99" />

---

# Detection

Unlike the previous attack simulations, this exercise did not rely on a dedicated Splunk alert.

Instead, detection was performed using custom SPL searches against Windows Sysmon telemetry.

---

# Detection Data Source

Windows Sysmon

Relevant Event:

- Sysmon Event ID 3 – Network Connection

The outbound connection generated when the victim accessed the phishing landing page was collected by the Splunk Universal Forwarder and indexed within Splunk Enterprise.
<img width="1852" height="1047" alt="SPL with markup" src="https://github.com/user-attachments/assets/d46968a7-158d-4484-b8c1-98f748dfcb92" />

<img width="1852" height="602" alt="Screenshot 2026-06-27 204517" src="https://github.com/user-attachments/assets/95703c87-974f-48f3-af1c-6720da66c8e1" />

---

# Investigation

Using Splunk, the investigation confirmed:

- Outbound connection to the GoPhish server.
- Destination IP:
  ```
  192.168.50.40
  ```
- Timestamp of the connection.
- Correlation with the phishing campaign timeline.

This demonstrated that endpoint network telemetry can be used to investigate phishing activity even without a dedicated alert.

---

# Detection Summary

| Stage | Status |
|--------|--------|
| Email Delivered | ✅ Confirmed |
| Email Opened | ✅ Confirmed |
| Link Clicked | ✅ Confirmed |
| Credential Submission | ✅ Confirmed |
| Sysmon Network Connection | ✅ Detected |
| Splunk Investigation | ✅ Completed |

---

# Lessons Learned

This simulation demonstrates the importance of combining user awareness testing with endpoint telemetry.

While GoPhish provides visibility into user interactions throughout a phishing campaign, Sysmon network events provide defenders with additional evidence that can be investigated within Splunk Enterprise.

Together, these sources enable analysts to reconstruct the phishing attack timeline and validate user activity within a controlled lab environment.

---

# Evidence

The following artifacts are included with this simulation:

- GoPhish campaign configuration
- Email template
- Landing page
- Email delivery
- Email opened
- Link clicked
- Credential submission
- GoPhish dashboard
- Sysmon Event ID 3
- SPL search results
- Splunk investigation
- Screen recording of the complete phishing simulation
