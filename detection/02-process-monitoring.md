# 02 — Process Execution Monitoring

## Objective

The objective of this detection is to monitor process execution activity generated on the Windows endpoint and identify process activity that may be security-relevant.

Process execution is a fundamental source of endpoint security telemetry because malicious activity frequently involves the execution of processes, scripts, interpreters, or administrative utilities.

Sysmon provides enhanced process telemetry that can be collected by the Splunk Universal Forwarder and analyzed within Splunk Enterprise.

The laboratory progressed from basic process-event visibility to controlled validation using Atomic Red Team activity.

---

# Detection Data Source

The primary telemetry source for this detection is:

```text
Sysmon
````

Sysmon Process Create events are associated with:

```text
Event ID 1
```

These events can provide information about process execution and associated context.

Available fields can include:

* Process name
* Process path
* Command line
* Parent process
* User
* Process ID
* Parent process ID
* Timestamp

The exact fields available depend on the Sysmon version and configuration used by the endpoint.

---

# Basic Process Search

A basic search for Sysmon Process Create events can be structured as:

```spl
index=* EventCode=1
```

This provides a starting point for examining process execution telemetry.

The search was subsequently validated against controlled endpoint activity to confirm that Sysmon process events were successfully reaching Splunk.

---

# Telemetry Validation

Process monitoring required validation beyond confirming that Sysmon was installed and running.

The laboratory initially generated controlled process activity on the Windows endpoint and attempted to locate the resulting telemetry in Splunk.

The initial search did not return the expected process-creation event.

Further analysis of the available telemetry sources identified that the expected Sysmon data was not initially present in the Splunk search results.

This demonstrated an important distinction between:

```text
Sysmon running
       ≠
Sysmon telemetry successfully indexed
```

The telemetry pipeline was subsequently investigated and the endpoint forwarding configuration was corrected.

After remediation, Sysmon process creation events became searchable in Splunk using:

```spl
EventCode=1
```

This established the complete process-telemetry path:

```text
Windows Process Activity
        ↓
Sysmon Event ID 1
        ↓
Universal Forwarder
        ↓
TCP 9997
        ↓
Splunk Enterprise
        ↓
EventCode=1 Search
```

The corresponding validation evidence is maintained in:

```text
evidence/detection/
```

---

# Controlled Process Execution Testing

Controlled endpoint activity was used to validate process monitoring.

The laboratory used Atomic Red Team to generate a controlled reconnaissance-style activity on the Windows endpoint.

The activity associated with:

```text
T1033 — System Owner/User Discovery
```

was executed in the isolated laboratory environment.

The test resulted in execution of:

```text
whoami.exe
```

The purpose of the test was not to simulate an uncontrolled attack, but to generate a known security-relevant process event that could be traced through the telemetry pipeline.

---

# Process Creation Validation in Splunk

After the telemetry pipeline was corrected, the controlled `whoami.exe` execution became visible within Splunk.

The process-creation search used:

```spl
EventCode=1
```

The resulting event provided process execution context that could be examined within Splunk Search & Reporting.

This established that the process generated on the Windows endpoint successfully:

```text
Generated
   ↓
Captured by Sysmon
   ↓
Forwarded by Universal Forwarder
   ↓
Received by Splunk
   ↓
Indexed
   ↓
Returned by SPL search
```

This is stronger validation than simply demonstrating that Sysmon or the Forwarder service is running.

The corresponding validation evidence is maintained in: `evidence/detection/13-whoami-detection-search.png`

---

# Process Analysis

A process event should not automatically be considered malicious.

The analyst should examine the surrounding context.

Important questions include:

```text
What process executed?
        ↓
Where was it executed from?
        ↓
Which user executed it?
        ↓
What command line was used?
        ↓
Which process spawned it?
        ↓
What happened immediately before and after?
```

This approach helps distinguish normal administrative activity from potentially suspicious execution.

---

# Command-Line Analysis

Command-line information can provide valuable investigative context.

For example:

```spl
index=* EventCode=1
| table _time host user Image ParentImage CommandLine
```

This can provide a simplified view of process execution.

The exact field names should be validated against the events ingested by the laboratory.

---

# Parent-Child Process Relationships

One of the most useful aspects of process telemetry is the ability to examine parent-child relationships.

The basic relationship is:

```text
Parent Process
      ↓
Child Process
```

During the controlled process-execution test, the resulting Sysmon event provided parent and child process context.

This allows the analyst to investigate not only what executed, but also which process initiated the execution.

The existence of a particular parent-child relationship does not automatically indicate malicious behavior.

However, unusual relationships can provide valuable investigative leads.

---

# Process Frequency

The frequency of process execution can also be examined.

Example:

```spl
index=* EventCode=1
| stats count by Image
| sort - count
```

This can help establish which processes occur most frequently within the monitored environment.

Frequency analysis can contribute to baseline development.

---

# Unusual Execution Paths

The location from which a process executes can provide additional context.

An analyst may investigate processes executing from unexpected locations such as:

```text
User-writable directories
Temporary directories
Downloads directories
Unusual application paths
```

These locations are not inherently malicious.

They become more useful when combined with additional indicators such as:

* Unusual command lines
* Suspicious parent processes
* Unexpected users
* Network connections
* Authentication anomalies

---

# Suspicious Process Investigation

A potentially suspicious process should be investigated using multiple dimensions.

```text
Process
  +
User
  +
Parent Process
  +
Command Line
  +
Execution Path
  +
Timestamp
  +
Network Activity
```

This creates a more complete picture of endpoint behavior.

---

# Authentication-to-Process Correlation

Process telemetry can be correlated with the authentication monitoring developed in Detection 01.

For example:

```text
Failed Authentication
        ↓
Successful Authentication
        ↓
Process Execution
        ↓
Network Activity
```

This sequence can help an analyst determine whether process activity occurred following suspicious authentication behavior.

The individual events are not necessarily malicious in isolation.

The correlation provides the investigative context.

---

# Detection Validation

The controlled process-execution test demonstrated the complete progression from endpoint activity to SIEM-visible telemetry.

```text
Controlled Activity
        ↓
Sysmon Event ID 1
        ↓
Universal Forwarder
        ↓
Splunk Ingestion
        ↓
EventCode=1
        ↓
Search Result
        ↓
Detection Validation
```

This validation is important because it demonstrates that the detection logic is operating on actual telemetry generated within the laboratory rather than on assumed or manually constructed data.

---

# Automated Detection

The validated process telemetry was subsequently used as the basis for an automated Splunk detection.

A Splunk alert was created around the controlled reconnaissance-related process activity.

The alert was tested against the laboratory-generated event to verify that the detection could identify the expected activity.

This represents a progression from:

```text
Manual Search
      ↓
Validated Telemetry
      ↓
Detection Logic
      ↓
Automated Alert
```

The alerting workflow provides the foundation for moving from analyst-driven searches toward continuous SOC monitoring.

---

# Triage Questions

When reviewing an unusual process, the analyst should consider:

### Process

* What executable was launched?
* Is it expected on the system?
* Where is the executable located?

### User

* Which account executed the process?
* Is that account expected to perform the action?

### Parent Process

* Which process launched it?
* Is the parent-child relationship normal?

### Command Line

* What arguments were supplied?
* Does the command line indicate unusual behavior?

### Timing

* When did the process execute?
* Did it occur shortly after an authentication event?

### Network Activity

* Did the process establish network connections?
* Were those connections expected?

---

# Example Investigation Logic

A process investigation can follow:

```text
Suspicious Process
        ↓
Identify User
        ↓
Inspect Parent Process
        ↓
Review Command Line
        ↓
Check Execution Path
        ↓
Check Authentication Events
        ↓
Check Network Activity
        ↓
Determine Risk
```

This demonstrates an analyst-driven approach rather than relying on a single indicator.

---

# Detection Limitations

The basic process search is intentionally broad.

A production detection would require additional logic such as:

* Known-good process baselines
* Parent-child process analysis
* Command-line patterns
* User context
* Execution-path analysis
* Frequency thresholds
* Network correlation
* Asset context
* Allowlisting
* False-positive suppression

The controlled `whoami.exe` detection demonstrates detection validation, but execution of `whoami.exe` by itself is not proof of malicious activity.

Context and correlation are required before making a security determination.

---

# MITRE ATT&CK Context

The controlled reconnaissance activity used during validation was associated with:

```text
T1033 — System Owner/User Discovery
```

The technique mapping describes the **behavior being tested**, not the mere existence of a Sysmon Event ID 1 event.

This distinction is important because a generic process-creation event can support many different investigations depending on the executable, command line, parent process, user, and surrounding activity.

---

# Result

The laboratory now supports process-execution monitoring with validated endpoint-to-SIEM telemetry and automated detection capability.

The detection capability has expanded from:

```text
Authentication
```

to:

```text
Authentication
        +
Process Execution
        +
Controlled Detection Validation
        +
Automated Alerting
```

The process-monitoring workflow therefore demonstrates both telemetry validation and detection engineering.

---

# Next Stage

The next detection extends visibility into **network activity generated by the Windows endpoint**.

This allows process activity to be examined alongside outbound connections and provides another dimension for correlation and investigation.

See:

```text
03-network-monitoring.md
