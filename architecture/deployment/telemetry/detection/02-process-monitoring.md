# 02 — Process Execution Monitoring

## Objective

The objective of this detection is to monitor process execution activity generated on the Windows endpoint.

Process execution is a fundamental source of endpoint security telemetry because malicious activity frequently involves the execution of processes, scripts, interpreters, or administrative utilities.

Sysmon provides enhanced process telemetry that can be collected by the Splunk Universal Forwarder and analyzed within Splunk Enterprise.

---

# Detection Data Source

The primary telemetry source for this detection is:

```text
Sysmon
````

Sysmon Process Create events are commonly associated with:

```text
Event ID 1
```

This event can provide information about process execution and associated context.

The available fields can include information such as:

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

The search should be refined to the actual index and field structure used by the laboratory once those values have been established.

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

For example, an analyst may search for process events containing command-line data:

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

For example:

```text
explorer.exe
      ↓
cmd.exe
      ↓
command.exe
```

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

# Detection Workflow

The process-monitoring workflow is:

```text
Process Execution
       ↓
Sysmon Event
       ↓
Windows Event Log
       ↓
Universal Forwarder
       ↓
Splunk Enterprise
       ↓
SPL Search
       ↓
Process Analysis
       ↓
Contextual Investigation
```

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

The laboratory can progressively introduce these controls.

---

# MITRE ATT&CK Context

Process execution telemetry can support investigations related to multiple ATT&CK techniques depending on the behavior observed.

The event itself should not automatically be mapped to a technique.

ATT&CK mapping should be based on the complete behavior and detection logic.

For example, suspicious execution of command interpreters, scripting engines, or other utilities may support technique-specific investigations when the surrounding evidence justifies the mapping.

---

# Result

The laboratory now supports process-execution monitoring in addition to authentication monitoring.

The detection capability has expanded from:

```text
Authentication
```

to:

```text
Authentication
        +
Process Execution
```

This provides additional endpoint context for threat hunting and investigation.

---

# Next Stage

The next detection will extend visibility into **network activity generated by the Windows endpoint**.

This will allow process activity to be examined alongside outbound connections and provide another dimension for correlation.

See:

```text
03-network-monitoring.md
```
