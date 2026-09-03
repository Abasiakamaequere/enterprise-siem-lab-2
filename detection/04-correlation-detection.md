# 04 — Correlation-Based Detection

## Objective

The objective of this detection is to correlate multiple endpoint telemetry sources within Splunk to identify activity that may warrant further investigation.

Individual security events often provide limited context.

Correlation allows the analyst to examine relationships between:

- Authentication activity
- Process execution
- Network connections

The purpose of this detection is therefore not to classify a single event as malicious, but to identify combinations of activity that present a stronger investigative signal.

---

# Detection Concept

The correlation model used in this laboratory is:

```text
Authentication
      +
Process Execution
      +
Network Activity
      ↓
Correlated Endpoint Activity
      ↓
Analyst Investigation
````

This represents a progression from individual event monitoring toward behavioral analysis.

---

# Why Correlation Matters

A single failed authentication event may be normal.

A single process execution may be normal.

A single network connection may also be normal.

However, the combination of multiple events occurring within a short period may provide additional context.

For example:

```text
Multiple Failed Logons
        ↓
Successful Authentication
        ↓
New Process Execution
        ↓
Network Connection
```

This sequence would justify further investigation because multiple telemetry sources are associated with the same period of endpoint activity.

The sequence itself does not automatically prove compromise.

---

# Correlation Model

The investigation model is:

```text
┌──────────────────────┐
│ Authentication      │
│                      │
│ 4624 / 4625          │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Process Execution    │
│                      │
│ Sysmon Event 1       │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Network Activity     │
│                      │
│ Sysmon Event 3       │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Correlation          │
│                      │
│ Time + Host + User   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Analyst Investigation │
└──────────────────────┘
```

---

# Correlation Dimensions

Events can be related using common contextual attributes.

The primary dimensions are:

```text
Host
+
User
+
Timestamp
```

Additional attributes can include:

```text
Process
+
Source
+
Destination
+
Command Line
```

The more contextual information available, the stronger the investigation can become.

---

# Authentication Baseline

Authentication activity provides the starting point.

Successful authentication:

```spl
index=* EventCode=4624
```

Failed authentication:

```spl
index=* EventCode=4625
```

These searches establish the authentication activity occurring on the endpoint.

---

# Process Baseline

Process execution can then be examined using Sysmon Process Create events.

```spl
index=* EventCode=1
```

A simplified investigation view can be created with:

```spl
index=* EventCode=1
| table _time host user Image ParentImage CommandLine
```

This provides process context that can be compared against authentication events.

---

# Network Baseline

Network connections can be examined using Sysmon network events.

```spl
index=* EventCode=3
```

A simplified investigation view can be created with:

```spl
index=* EventCode=3
| table _time host user Image SourceIp SourcePort DestinationIp DestinationPort
```

This provides network context for the endpoint activity.

---

# Time-Based Correlation

The timestamp is an important correlation dimension.

An investigation can examine events occurring within the same period:

```text
Authentication
      │
      │
      ├── Process Creation
      │
      └── Network Connection
```

For example:

```text
10:15:01  Failed authentication
10:15:04  Successful authentication
10:15:12  New process created
10:15:15  Network connection
```

The close temporal relationship does not automatically indicate malicious activity, but it creates an investigative sequence.

---

# Example Correlation Workflow

```text
1. Identify unusual authentication activity
             ↓
2. Identify the affected account
             ↓
3. Identify the endpoint
             ↓
4. Examine process activity around the same time
             ↓
5. Identify newly executed processes
             ↓
6. Examine network activity associated with those processes
             ↓
7. Determine whether the combined behavior is expected
```

This approach allows the analyst to move from an isolated alert to a broader behavioral investigation.

---

# Example SPL Investigation

A broad search can begin with authentication events:

```spl
index=* EventCode=4625
```

The analyst can then identify the relevant:

* Host
* User
* Timestamp

The resulting context can be used to investigate nearby process events:

```spl
index=* EventCode=1
```

and network events:

```spl
index=* EventCode=3
```

This staged approach is intentionally simple and transparent.

The analyst first identifies a potentially interesting event and then pivots into additional telemetry.

---

# Investigative Pivoting

The investigation can be represented as:

```text
Authentication Event
        ↓
Host
        ↓
User
        ↓
Timestamp
        ↓
Process Events
        ↓
Process Identity
        ↓
Network Events
        ↓
Destination
```

This pivot-based methodology reflects a practical SOC investigation workflow.

---

# Example Behavioral Scenario

Consider the following hypothetical sequence:

```text
Multiple failed authentication attempts
                ↓
Successful authentication
                ↓
Previously uncommon process executes
                ↓
Process establishes a network connection
```

An analyst would not immediately classify this as confirmed malicious activity.

Instead, the analyst would investigate:

### Authentication

* Which account was targeted?
* Where did the attempts originate?
* How many failures occurred?
* Was the successful authentication expected?

### Process

* What process executed?
* Which user launched it?
* What was the parent process?
* What command line was used?
* Where was the executable located?

### Network

* Which process initiated the connection?
* What destination was contacted?
* Which port was used?
* Was the destination expected?

---

# Detection Decision

The correlation process can be summarized as:

```text
                    ┌───────────────────┐
                    │ Authentication    │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Process Execution │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Network Activity  │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ Context Analysis  │
                    └─────────┬─────────┘
                              │
                   ┌──────────┴──────────┐
                   ▼                     ▼
             Expected Activity     Suspicious Activity
                   │                     │
                   ▼                     ▼
                Close Case          Investigate
```

This emphasizes that detection is a decision-support process rather than simply generating alerts.

---

# False Positive Management

Correlation can reduce false positives by requiring multiple contextual signals.

However, correlation does not eliminate false positives.

Normal sequences can include:

```text
User Login
    ↓
Application Launch
    ↓
Network Connection
```

This may be completely legitimate.

Therefore, the analyst must consider:

* User expectations
* Host role
* Process legitimacy
* Destination legitimacy
* Timing
* Frequency
* Historical baseline

---

# Detection Limitations

This laboratory implementation is designed primarily for learning and demonstrating investigative methodology.

A production implementation would require more advanced controls, including:

* Data-model normalization
* CIM-compliant fields
* Risk scoring
* Threshold tuning
* Lookup tables
* Asset and identity context
* Known-good baselines
* Automated alerting
* False-positive suppression
* Case management

The laboratory provides the foundation for progressively developing these capabilities.

---

# MITRE ATT&CK Context

Correlation does not represent a single ATT&CK technique.

The relevant ATT&CK mapping depends on the behavior observed in the correlated events.

For example, authentication anomalies, suspicious process execution, and network behavior may contribute evidence to investigations involving different techniques.

ATT&CK mapping should therefore be performed after analyzing the complete behavioral sequence.

---

# SOC Investigation Value

This detection demonstrates an important shift in analytical maturity.

The investigation is no longer:

```text
"Find Event ID X."
```

It becomes:

```text
"What happened?"
        ↓
"When did it happen?"
        ↓
"Which user was involved?"
        ↓
"What process executed?"
        ↓
"What network activity followed?"
        ↓
"Does the combined behavior make sense?"
```

This is the foundation of behavioral security analysis.

---

# Result

The detection framework now combines three telemetry categories:

```text
Authentication
      +
Process Execution
      +
Network Activity
```

These signals can be correlated using:

```text
Host
+
User
+
Time
+
Process
+
Network Context
```

This provides a foundation for identifying suspicious endpoint behavior and conducting structured investigations.

---

# Detection Progression

The detection section has now progressed through:

```text
01 — Authentication Monitoring
             ↓
02 — Process Execution Monitoring
             ↓
03 — Network Activity Monitoring
             ↓
04 — Correlation-Based Detection
```

This progression demonstrates the evolution from individual event monitoring to multi-source behavioral analysis.

---

# Next Stage

The next stage will document a complete **investigation case**.

Rather than describing detection concepts only, the case study will demonstrate how an analyst can move from an observed event through:

```text
Alert
 ↓
Triage
 ↓
Evidence Collection
 ↓
Correlation
 ↓
Analysis
 ↓
Conclusion
 ↓
Recommended Response
```

See:

```text
05-investigation-case.md
```
