# 01 — Authentication Monitoring

## Objective

The objective of this detection is to monitor authentication activity originating from the Windows endpoint.

Authentication events provide a fundamental source of security telemetry because abnormal successful or failed logons can indicate:

- Credential attacks
- Brute-force activity
- Password guessing
- Unauthorized access
- Account misuse
- Suspicious administrative activity

The detection uses Windows Security Event Logs ingested into Splunk Enterprise.

---

# Detection Logic

The initial monitoring logic focuses on two Windows Security Event IDs:

| Event ID | Meaning | Security Relevance |
|---|---|---|
| 4624 | Successful logon | Establishes successful authentication activity |
| 4625 | Failed logon | Can indicate failed authentication attempts |

These events provide the basis for authentication monitoring within the laboratory.

---

# Successful Logon Monitoring

A basic SPL search for successful Windows logons is:

```spl
index=* EventCode=4624
````

This search identifies events associated with successful authentication.

The search can subsequently be refined using additional fields such as:

* User
* Host
* Source IP
* Logon Type
* Timestamp

---

# Failed Logon Monitoring

A basic SPL search for failed Windows logons is:

```spl
index=* EventCode=4625
```

Failed authentication events are particularly useful for identifying patterns of repeated authentication failure.

---

# Investigative Questions

When investigating authentication activity, the analyst should ask:

```text
Who attempted to authenticate?
        ↓
Was authentication successful?
        ↓
When did it occur?
        ↓
Which host generated the event?
        ↓
What was the source?
        ↓
What type of logon occurred?
        ↓
Were there repeated failures?
```

This transforms raw event data into an investigative workflow.

---

# Failed Authentication Pattern

Repeated failed authentication events may warrant further investigation.

A simple aggregation can be performed using:

```spl
index=* EventCode=4625
| stats count by user
| sort - count
```

This groups failed authentication events by user and orders the results by event count.

The purpose is to identify accounts associated with unusually frequent failed authentication attempts.

---

# Time-Based Investigation

Authentication activity can also be examined over time.

For example:

```spl
index=* EventCode=4625
| timechart count
```

This provides a time-based view of failed authentication events.

Sudden increases in failed authentication activity may warrant additional investigation.

---

# Detection Workflow

The authentication-monitoring workflow is:

```text
Windows Authentication Activity
             ↓
Windows Security Event Log
             ↓
Splunk Universal Forwarder
             ↓
Splunk Enterprise
             ↓
SPL Search
             ↓
Authentication Analysis
             ↓
Potential Detection
             ↓
Investigation
```

---

# Analyst Triage

A high number of failed logons should not automatically be classified as malicious.

The analyst should first establish context.

Potential explanations include:

* User entering an incorrect password
* Expired credentials
* Misconfigured services
* Scheduled tasks using outdated credentials
* Administrative activity
* Password spraying
* Brute-force attempts

The detection therefore acts as an **investigative trigger**, not automatic proof of compromise.

---

# Correlation

Authentication events become more valuable when correlated with other endpoint telemetry.

For example:

```text
Failed Logons
      +
Successful Logon
      +
Process Creation
      +
Network Activity
```

This can provide a stronger basis for determining whether authentication activity represents normal behavior or potentially malicious activity.

---

# Example Investigation

A potential investigation could follow this sequence:

```text
Multiple Failed Logons
          ↓
Identify Target Account
          ↓
Identify Source
          ↓
Check Successful Logon
          ↓
Examine Process Activity
          ↓
Examine Network Activity
          ↓
Determine Whether Activity Is Suspicious
```

The objective is to build an evidence-based conclusion rather than treating an individual event as sufficient evidence.

---

# Detection Limitations

The basic searches in this document are intentionally broad.

They are suitable for initial monitoring but should not be considered production-ready detection rules.

Potential improvements include:

* Restricting searches to the known Windows endpoint
* Using the actual Splunk index
* Using normalized field names
* Adding time thresholds
* Establishing baseline authentication behavior
* Excluding known benign activity
* Correlating multiple event types
* Creating alert thresholds

These refinements can be added as the laboratory develops.

---

# MITRE ATT&CK Relevance

Authentication monitoring can contribute to investigations involving credential-access and valid-account behaviors.

However, the presence of a Windows Event ID alone does not establish a specific ATT&CK technique.

ATT&CK mapping should therefore be applied based on the complete detection logic and observed behavior rather than simply assigning a technique to an event ID.

---

# Result

The laboratory now has a basic authentication-monitoring capability based on Windows Security Event Logs.

The detection provides a foundation for:

* Authentication monitoring
* Failed-logon analysis
* Successful-logon analysis
* Account investigation
* Event correlation
* Threat hunting

---

# Next Stage

The next detection will focus on **process execution telemetry from Sysmon**.

This will extend the detection capability from:

```text
Authentication
```

to:

```text
Endpoint Process Activity
```

See:

```text
02-process-monitoring.md
```
