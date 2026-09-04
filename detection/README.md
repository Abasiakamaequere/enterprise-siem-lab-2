# Detection

## Splunk-Based Security Detection Engineering

This directory documents the detection engineering phase of the Security Operations laboratory.

Following the deployment of Splunk Enterprise, Windows endpoint telemetry, Sysmon, and the Splunk Universal Forwarder, the project progressed from infrastructure and telemetry collection toward the identification of potentially suspicious security activity.

The detection layer uses the collected endpoint telemetry to develop, test, validate, and document security detections using Splunk Search Processing Language (SPL).

---

## Detection Objective

The objective of this phase is to demonstrate the ability to transform raw endpoint telemetry into actionable security detections.

The detection workflow is:

```text
Endpoint Telemetry
       ↓
Data Normalization
       ↓
SPL Search
       ↓
Detection Logic
       ↓
Validation
       ↓
Security Finding
       ↓
Investigation
````

The focus is therefore not simply on writing SPL queries.

Each detection should answer:

* What behavior is being detected?
* Why is the behavior security-relevant?
* Which telemetry supports the detection?
* What fields or event characteristics are used?
* What SPL logic identifies the behavior?
* How was the detection validated?
* What are the detection's limitations?
* What additional investigation should follow?

---

# Detection Architecture

The detection layer builds on the telemetry architecture established previously.

```text
┌──────────────────────────────┐
│       Windows Endpoint       │
│                              │
│ Windows Event Logs           │
│ Sysmon                       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Splunk Universal Forwarder   │
└──────────────┬───────────────┘
               │
               │ TCP 9997
               ▼
┌──────────────────────────────┐
│ Splunk Enterprise 10.4.2     │
│ Ubuntu Server 25.04          │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        SPL Detection         │
│                              │
│ Search                       │
│ Filtering                    │
│ Aggregation                  │
│ Correlation                  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Security Finding             │
│                              │
│ Investigation                │
│ Validation                   │
│ Response Decision            │
└──────────────────────────────┘
```

---

# Detection Categories

The laboratory's detection work is organized around security behaviors observable through the available endpoint telemetry.

## 1. Authentication Activity

Authentication telemetry can be used to identify:

* Successful logons
* Failed logons
* Repeated authentication failures
* Unusual authentication patterns

Relevant Windows Security Event IDs include:

```text
4624 — Successful logon
4625 — Failed logon
```

These events provide a foundation for authentication-focused detections.

---

## 2. Process Activity

Sysmon telemetry provides additional visibility into process execution.

Process-focused detections can examine:

* Process creation
* Parent-child process relationships
* Unusual process execution
* Suspicious command-line activity
* Processes associated with other suspicious events

The objective is to move from:

```text
A process executed
```

toward:

```text
Is this process execution unusual or security-relevant?
```

---

## 3. Network Activity

Endpoint network telemetry can provide visibility into connections initiated by processes.

Network-focused detection logic can investigate:

* Unexpected outbound connections
* Unusual destinations
* Suspicious process-to-network relationships
* Connections associated with potentially malicious activity

Network detections become more useful when correlated with process telemetry.

---

## 4. Reconnaissance Activity

The laboratory can also use endpoint telemetry to investigate reconnaissance-related behavior.

Examples include:

* Network discovery
* Host discovery
* Repeated connection attempts
* Suspicious command execution associated with reconnaissance

The purpose is to identify behavioral patterns rather than automatically classify every discovery event as malicious.

---

# Detection Engineering Methodology

Each detection follows a structured process:

```text
1. Define the security behavior
           ↓
2. Identify available telemetry
           ↓
3. Identify relevant fields/events
           ↓
4. Develop SPL logic
           ↓
5. Execute the search
           ↓
6. Review returned events
           ↓
7. Validate the detection
           ↓
8. Document limitations
```

This approach separates detection development from arbitrary query writing.

---

# Detection Lifecycle

The detection lifecycle used in this project is:

```text
Security Hypothesis
        ↓
Telemetry Mapping
        ↓
SPL Development
        ↓
Testing
        ↓
Validation
        ↓
Tuning
        ↓
Documented Detection
```

A detection is considered useful only when its logic can be connected to a defined security behavior.

---

# Detection Documentation Standard

Each individual detection should document:

| Field              | Description                   |
| ------------------ | ----------------------------- |
| Detection Name     | Name of the detection         |
| Security Objective | Behavior being identified     |
| Data Source        | Telemetry used                |
| Event ID           | Relevant event identifier     |
| Detection Logic    | Logic behind the detection    |
| SPL Query          | Search implementation         |
| Expected Result    | What should be returned       |
| Validation         | How the detection was tested  |
| False Positives    | Potential benign explanations |
| Limitations        | Known weaknesses              |
| Investigation      | Recommended next steps        |

This structure keeps the detection work reproducible and analytically defensible.

---

# Detection vs Investigation

Detection and investigation are treated as separate stages.

### Detection asks:

> **What potentially suspicious behavior occurred?**

### Investigation asks:

> **What happened, why did it happen, and is it actually malicious?**

The workflow therefore becomes:

```text
Telemetry
    ↓
Detection
    ↓
Potential Finding
    ↓
Investigation
    ↓
Security Assessment
```

A detection should not automatically be treated as proof of compromise.

---

# Validation Philosophy

Detection validation is based on observable telemetry.

Where possible, detections should be tested against controlled activity inside the laboratory.

The validation process should establish:

```text
Expected Activity
       ↓
Telemetry Generated
       ↓
Telemetry Ingested
       ↓
Detection Triggered
       ↓
Analyst Reviews Result
```

This provides evidence that the detection operates as intended.

---

# False Positives

Detection engineering must account for legitimate activity that may resemble suspicious behavior.

For each detection, potential benign explanations should therefore be documented.

Examples may include:

* Administrative activity
* Software updates
* Normal system processes
* Automated Windows services
* Legitimate network connections
* Routine authentication behavior

The purpose of detection engineering is not to maximize the number of alerts.

The objective is to produce **useful security signals**.

---

# Detection Quality

Detection quality will be evaluated using several considerations:

```text
Coverage
   +
Accuracy
   +
Context
   +
Validation
   +
Actionability
```

A detection that generates large numbers of alerts without useful context may require tuning.

A strong detection should provide enough information to support the next stage of investigation.

---

# Current Detection Scope

The initial detection development focuses on:

```text
Authentication
      ↓
Process Execution
      ↓
Network Activity
      ↓
Reconnaissance
      ↓
Suspicious Endpoint Behavior
```

These categories build directly on the Windows and Sysmon telemetry collected during the previous stage of the project.

---

# Relationship to Threat Hunting

Detection engineering and threat hunting are closely related but serve different purposes.

### Detection

Attempts to identify defined suspicious behavior consistently.

### Threat Hunting

Begins with a hypothesis and actively searches the telemetry for evidence of potentially malicious activity.

The laboratory will therefore use detections as structured monitoring logic while also developing separate hunting workflows.

---

# Relationship to Investigation

The detection layer provides the starting point for the investigation phase.

```text
Detection
    ↓
Alert / Finding
    ↓
Evidence Collection
    ↓
Event Correlation
    ↓
Timeline Construction
    ↓
Analyst Assessment
```

This allows the project to demonstrate the complete progression from telemetry to security analysis.

---

# Detection Engineering Outcome

The detection phase transforms the laboratory from a system that merely collects logs into a platform capable of identifying potentially significant security activity.

The progression is:

```text
Infrastructure
      ↓
Telemetry
      ↓
Detection
      ↓
Threat Hunting
      ↓
Investigation
      ↓
Security Analysis
```

---

# Directory Structure

The detection documentation will be organized as follows:

```text
detection/
│
├── README.md
│
├── 01-authentication-monitoring.md
├── 02-process-monitoring.md
├── 03-network-monitoring.md
├── 04-correlation-detection.md
└── 05-investigation-case.md
```

---

# Next Stage

The next stage defines the detection methodology used to convert the available Windows and Sysmon telemetry into practical SPL-based detections.

See:

```text
01-detection-strategy.md
```

---

## Related Project Phases

```text
Deployment
    ↓
Telemetry
    ↓
Detection
    ↓
Threat Hunting
    ↓
Investigation
```

The detection phase therefore represents the transition from **collecting security data** to **using that data to identify security-relevant behavior**.
