# 04 — Correlation-Based Detection

## Objective

The objective of this detection is to correlate multiple endpoint telemetry sources within Splunk to identify activity that may warrant further investigation.

Individual security events often provide limited context.

Correlation allows the analyst to examine relationships between:

- Authentication activity
- Process execution
- Network activity

The purpose of this detection is not to classify a single event as malicious, but to identify combinations of activity that provide a stronger investigative signal.

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

However, multiple events occurring within a related timeframe can provide additional investigative context.

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

Such a sequence may justify further investigation because multiple telemetry sources are associated with the same period of endpoint activity.

The sequence itself does not automatically prove compromise.

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

Additional attributes may include:

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

# Authentication Context

Authentication activity provides an important starting point for correlation.

Successful authentication:

```spl
index=* EventCode=4624
```

Failed authentication:

```spl
index=* EventCode=4625
```

These searches establish authentication activity occurring on the monitored endpoint.

Authentication events can then be used as investigative pivots into process and network activity.

---

# Process Context

Process execution can be examined using Sysmon Process Create events.

```spl
index=* EventCode=1
```

A simplified investigation view can be created with:

```spl
index=* EventCode=1
| table _time host user Image ParentImage CommandLine
```

This provides process context that can be compared with authentication activity.

The laboratory successfully validated Sysmon Event ID 1 telemetry within Splunk, establishing process execution as a usable correlation source.

---

# Network Context

Network connections can be examined using Sysmon network events where the relevant telemetry is available.

```spl
index=* EventCode=3
```

A simplified investigation view can be created with:

```spl
index=* EventCode=3
| table _time host user Image SourceIp SourcePort DestinationIp DestinationPort
```

The exact field names and event availability should be validated against the telemetry actually ingested by the environment.

Network activity is therefore treated as an additional correlation dimension rather than assumed evidence of malicious behavior.

---

# Time-Based Correlation

Timestamp is an important correlation dimension.

An investigation can examine events occurring within the same period:

```text
Authentication
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

This approach allows the analyst to move from an isolated event toward a broader behavioral investigation.

---

# Example SPL Investigation

A broad search can begin with authentication events:

```spl
index=* EventCode=4625
```

The analyst can identify the relevant:

* Host
* User
* Timestamp

The resulting context can then be used to pivot into nearby process events:

```spl
index=* EventCode=1
```

and, where available, network events:

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

# Controlled Investigation Context

The laboratory also contains a controlled reconnaissance investigation.

The documented workflow demonstrates how endpoint telemetry can be used to move from observed process activity toward security analysis.

The controlled activity involved process execution that was investigated through the SIEM and associated with:

```text
T1033 — System Owner/User Discovery
```

This demonstrates the transition from:

```text
Raw Telemetry
      ↓
Observable Behavior
      ↓
Detection
      ↓
Investigation
      ↓
ATT&CK Context
```

The ATT&CK mapping applies to the observed behavior and investigation context rather than to the existence of a generic process event alone.

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

Therefore, the analyst should consider:

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

The controlled reconnaissance investigation documented in the project provides an example of this principle:

```text
Observed Behavior
      ↓
Process Telemetry
      ↓
Investigation
      ↓
Behavioral Assessment
      ↓
T1033 — System Owner/User Discovery
```

The technique mapping should be based on the complete behavioral evidence rather than on a generic event type.

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

The detection framework combines three telemetry categories:

```text
Authentication
      +
Process Execution
      +
Network Activity
```

These signals can be related using:

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

This provides a structured foundation for identifying suspicious endpoint behavior and conducting security investigations.

---

# Detection Progression

The detection section has progressed through:

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

The next stage documents a complete investigation case.

The investigation phase demonstrates how an analyst can move from an observed security event through:

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
