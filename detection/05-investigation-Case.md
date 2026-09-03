# 05 — Endpoint Investigation Case

## Objective

This case study demonstrates how endpoint telemetry collected by the laboratory can be used to conduct a structured security investigation.

The investigation combines:

- Windows authentication telemetry
- Sysmon process telemetry
- Sysmon network telemetry
- Splunk search
- Event correlation
- Evidence-based analysis

The objective is to demonstrate an analyst workflow rather than claim that a confirmed real-world compromise occurred.

---

# Investigation Philosophy

Security events should be investigated in context.

A single event rarely provides enough information to determine whether activity is malicious.

The investigation therefore follows:

```text
Event
  ↓
Context
  ↓
Correlation
  ↓
Evidence
  ↓
Assessment
````

---

# Investigation Scenario

The laboratory uses a controlled endpoint environment to examine a potentially suspicious sequence of activity.

The hypothetical investigative sequence is:

```text
Multiple Authentication Failures
          ↓
Successful Authentication
          ↓
Process Execution
          ↓
Network Connection
```

This sequence provides a starting point for investigation.

It does not, by itself, establish that the endpoint was compromised.

---

# Phase 1 — Identify Authentication Activity

The investigation begins with Windows authentication telemetry.

### Failed Authentication

```spl
index=* EventCode=4625
```

The analyst reviews:

* Timestamp
* Host
* User
* Source information
* Number of attempts

The purpose is to determine whether repeated authentication failures are present.

---

# Phase 2 — Identify Successful Authentication

Successful authentication events can be examined using:

```spl
index=* EventCode=4624
```

The analyst compares successful and failed authentication events.

Questions include:

* Did a successful logon follow multiple failures?
* Which account authenticated?
* Which host was involved?
* Was the activity expected?

---

# Phase 3 — Pivot to Process Activity

After identifying a relevant authentication event, the analyst pivots to process telemetry.

Sysmon Process Create events can be searched using:

```spl
index=* EventCode=1
```

The analyst examines process activity around the relevant timestamp.

Example investigation view:

```spl
index=* EventCode=1
| table _time host user Image ParentImage CommandLine
```

The objective is to identify processes executed around the time of the authentication event.

---

# Phase 4 — Process Analysis

For each potentially relevant process, the analyst examines:

```text
Process Name
Process Path
User
Parent Process
Command Line
Timestamp
```

The analyst then asks:

```text
Was the process expected?
        ↓
Was it launched by an expected parent?
        ↓
Was the command line normal?
        ↓
Was the execution path expected?
```

---

# Phase 5 — Pivot to Network Activity

The investigation then moves to network telemetry.

Sysmon network connection events can be searched using:

```spl
index=* EventCode=3
```

A simplified view can be created with:

```spl
index=* EventCode=3
| table _time host user Image SourceIp SourcePort DestinationIp DestinationPort
```

The analyst examines network activity around the process execution timestamp.

---

# Phase 6 — Network Analysis

The analyst investigates:

```text
Source Host
User
Process
Source IP
Destination IP
Source Port
Destination Port
Timestamp
```

The key question becomes:

> Did the process identified during the investigation establish network communication?

The destination is then assessed against the expected behavior of the laboratory environment.

---

# Phase 7 — Timeline Construction

The investigation can be reconstructed as a timeline.

Example:

```text
10:15:01
Failed authentication
        ↓
10:15:04
Successful authentication
        ↓
10:15:12
Process execution
        ↓
10:15:15
Network connection
```

The exact timestamps and events used in a final investigation should come from actual Splunk evidence.

The timeline above is an illustrative investigation model and should not be interpreted as a claim that these exact events occurred.

---

# Evidence Correlation

The investigation combines the three main telemetry categories:

```text
┌─────────────────────┐
│ Authentication      │
│                     │
│ 4624 / 4625         │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Process Execution   │
│                     │
│ Sysmon Event 1      │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Network Activity    │
│                     │
│ Sysmon Event 3      │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Timeline + Context  │
└─────────────────────┘
```

This creates a structured evidence chain.

---

# Evidence Table

An investigation should document relevant events in a structured format.

| Time          | Source           | Event              | User     | Host     | Process     | Network         | Assessment |
| ------------- | ---------------- | ------------------ | -------- | -------- | ----------- | --------------- | ---------- |
| `<timestamp>` | Windows Security | Failed logon       | `<user>` | `<host>` | N/A         | N/A             | Review     |
| `<timestamp>` | Windows Security | Successful logon   | `<user>` | `<host>` | N/A         | N/A             | Review     |
| `<timestamp>` | Sysmon           | Process creation   | `<user>` | `<host>` | `<process>` | N/A             | Review     |
| `<timestamp>` | Sysmon           | Network connection | `<user>` | `<host>` | `<process>` | `<destination>` | Review     |

The placeholders should be replaced only when corresponding evidence has been collected from the laboratory.

---

# Analyst Assessment

The analyst should avoid making a conclusion based solely on event volume.

Instead, the evidence should be evaluated using:

```text
Authentication Context
        +
Process Context
        +
Network Context
        +
User Context
        +
Time Context
```

The resulting assessment should classify the activity according to the available evidence.

Possible outcomes include:

```text
Benign / Expected
        OR
Requires Further Investigation
        OR
Suspicious
```

A definitive compromise determination should only be made when sufficient evidence supports it.

---

# False Positive Analysis

Potentially suspicious activity can have legitimate explanations.

For example:

```text
Multiple Failed Logons
```

may result from:

* Incorrect password entry
* Expired credentials
* Misconfigured applications
* Scheduled tasks
* Administrative activity

Likewise:

```text
Unusual Process
```

may result from:

* Software installation
* System maintenance
* Administrative troubleshooting
* Application updates

Network connections can similarly originate from legitimate applications and services.

Therefore, context is essential.

---

# Investigation Decision Tree

```text
                 Event Identified
                       │
                       ▼
               Is it unusual?
                  /       \
                No         Yes
                │           │
                ▼           ▼
             Monitor     Investigate
                            │
                            ▼
                     Identify User
                            │
                            ▼
                     Identify Host
                            │
                            ▼
                    Examine Process
                            │
                            ▼
                    Examine Network
                            │
                            ▼
                     Correlate Events
                            │
                            ▼
                     Assess Evidence
                       /         \
                  Expected      Suspicious
                     │              │
                     ▼              ▼
                  Close       Escalate / Investigate
```

---

# Recommended Response

If the investigation produces sufficiently strong evidence of suspicious activity, an analyst could recommend actions such as:

1. Validate the affected account.
2. Review the endpoint for additional suspicious activity.
3. Examine related authentication events.
4. Review process execution history.
5. Review network connections.
6. Preserve relevant evidence.
7. Determine whether containment is appropriate.
8. Document the investigation and findings.

The specific response should depend on the severity and confidence of the evidence.

---

# Investigation Documentation Standard

Each investigation should record:

```text
Incident / Case ID
Detection Source
Date / Time
Affected Host
Affected User
Observed Activity
Evidence
Correlation
Analyst Assessment
Actions Taken
Final Disposition
```

This provides a repeatable structure for future SOC investigations.

---

# MITRE ATT&CK Mapping

MITRE ATT&CK mapping should be performed only after analyzing the complete observed behavior.

The investigation may potentially provide evidence relevant to:

* Credential access
* Valid account usage
* Process execution
* Command and scripting activity
* Network-related techniques

However, an event ID alone should not be used as proof of an ATT&CK technique.

The final mapping should be based on the actual observed behavior and evidence.

---

# Limitations

This investigation framework is designed for a controlled laboratory environment.

It does not represent a production SOC case-management implementation.

Production environments would typically incorporate:

* SIEM risk scoring
* Asset inventory
* Identity context
* Threat intelligence
* Automated enrichment
* Case management
* Alert prioritization
* Incident response procedures
* Evidence retention requirements

The laboratory provides the foundation for developing these capabilities.

---

# Result

The project now demonstrates an end-to-end security investigation methodology:

```text
Telemetry
   ↓
Detection
   ↓
Triage
   ↓
Correlation
   ↓
Evidence
   ↓
Assessment
   ↓
Response Recommendation
```

This represents the transition from simply operating a SIEM platform to using the platform for security operations.

---

# Evidence Requirements

Actual investigation evidence should be added to the repository when available.

Recommended evidence includes:

* Splunk search screenshots
* Event details
* Process execution evidence
* Network connection evidence
* Timeline evidence
* Detection output

All evidence must be sanitized before publication.

Remove or redact:

* Passwords
* API keys
* Authentication tokens
* Private credentials
* Unnecessary personal information
* Sensitive infrastructure information

---

# Next Stage

The detection section can now progress toward documenting the actual evidence produced by the laboratory.

Future detection work can build on this investigation methodology by introducing:

```text
Detection
    ↓
Evidence
    ↓
Triage
    ↓
Correlation
    ↓
Investigation
    ↓
Conclusion
```

The objective is to demonstrate not only that the SIEM was deployed, but that it can be used as an operational security-analysis platform.
