# 04 — Telemetry Ingestion & Validation

## Objective

The objective of this stage was to verify the complete endpoint-to-SIEM telemetry pipeline.

The laboratory was no longer focused only on whether Sysmon and the Splunk Universal Forwarder were installed.

The critical question became:

> Can security events generated on the Windows endpoint successfully reach Splunk Enterprise and become searchable?

---

# End-to-End Telemetry Pipeline

The completed telemetry architecture is:

```text
┌──────────────────────────┐
│   Windows Endpoint       │
│                          │
│ Windows Security Logs    │
│ Sysmon                   │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Splunk Universal         │
│ Forwarder                │
└────────────┬─────────────┘
             │
             │ TCP 9997
             ▼
┌──────────────────────────┐
│ Ubuntu Server 25.04      │
│                          │
│ Splunk Enterprise 10.4.2 │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Splunk Web               │
│                          │
│ SPL Search               │
│ Investigation            │
└──────────────────────────┘
````

---

# Validation Philosophy

Telemetry ingestion was validated as a complete pipeline rather than treating each component independently.

The validation model was:

```text
Event Generation
      ↓
Event Logging
      ↓
Event Collection
      ↓
Event Transmission
      ↓
Event Reception
      ↓
Event Search
```

Each stage represents a potential failure point.

---

# Event Generation

The Windows endpoint generates native Windows security and system events through normal operating-system activity.

Sysmon provides additional endpoint telemetry.

The resulting data sources include:

```text
Windows Security Events
        +
Sysmon Events
```

These provide the raw security telemetry used by the SIEM.

---

# Event Collection

The Splunk Universal Forwarder operates on the Windows endpoint and provides the collection and transport layer.

Its role is to collect the configured Windows event data and forward it toward the central Splunk Enterprise instance.

```text
Windows Event Logs
        +
Sysmon
        ↓
Universal Forwarder
```

---

# Event Transmission

The Universal Forwarder transmits the collected events to the Splunk Enterprise server.

The forwarding path uses:

```text
TCP 9997
```

The communication path is:

```text
Windows Endpoint
       │
       │
       ▼
Universal Forwarder
       │
       │ TCP 9997
       ▼
Splunk Enterprise
```

---

# Event Reception

The Ubuntu Server hosts Splunk Enterprise and acts as the central destination for the endpoint telemetry.

Once the events reach Splunk, they become available to the SIEM's search and analysis capabilities.

The server therefore acts as the centralized telemetry repository for the laboratory.

---

# Search Validation

The final validation stage is performed through Splunk search.

A telemetry pipeline cannot be considered operational simply because the Universal Forwarder reports that it is running.

The stronger validation is:

```text
Endpoint Event
      ↓
Forwarder
      ↓
Splunk
      ↓
Searchable Event
```

A successfully searchable event provides evidence that the complete pipeline is functioning.

---

# Authentication Telemetry

Authentication events provide an important initial validation source.

Windows Security Event ID:

```text
4624
```

represents a successful logon event.

Windows Security Event ID:

```text
4625
```

represents a failed logon event.

These events can be useful for investigating authentication behavior within the laboratory.

Example SPL:

```spl
index=* EventCode=4624
```

Example failed-logon search:

```spl
index=* EventCode=4625
```

These searches can be adapted to the actual index and field names used by the laboratory.

---

# Authentication Investigation Model

Authentication telemetry can be examined using:

```text
Timestamp
+
Username
+
Source Host
+
Logon Type
+
Authentication Result
```

This allows an analyst to move beyond simply identifying that an event occurred.

The objective is to understand:

* Who authenticated?
* When did the authentication occur?
* Was the authentication successful?
* Which system generated the event?
* What surrounding activity occurred?

---

# Correlation With Endpoint Telemetry

The value of the telemetry architecture increases when multiple data sources can be examined together.

For example:

```text
Authentication Event
        │
        │
        ▼
Endpoint Activity
        │
        │
        ▼
Process Telemetry
        │
        │
        ▼
Network Activity
```

This provides a more complete view of endpoint behavior.

Instead of investigating an authentication event in isolation, an analyst can examine activity occurring around the same time.

---

# Telemetry Troubleshooting

If events are not visible in Splunk, troubleshooting should proceed through the pipeline systematically.

```text
1. Generate endpoint activity
          ↓
2. Check Windows Event Logs
          ↓
3. Check Sysmon events
          ↓
4. Verify Universal Forwarder
          ↓
5. Verify forwarding configuration
          ↓
6. Test connectivity to TCP 9997
          ↓
7. Verify Splunk receiving configuration
          ↓
8. Search Splunk
```

This layered approach helps identify whether the problem exists at the endpoint, collection, network, receiving, or search layer.

---

# Telemetry Integrity

The project treats telemetry ingestion as an engineering pipeline.

A successful deployment therefore requires more than installing individual components.

The components must work together:

```text
Source
  ↓
Collector
  ↓
Transport
  ↓
Receiver
  ↓
Search
```

Failure at any stage can prevent useful security analysis.

---

# SOC Relevance

Successful telemetry ingestion is the foundation for the subsequent SOC workflows in this project.

Once events are searchable, the analyst can perform:

* Security monitoring
* Threat hunting
* Detection development
* Authentication analysis
* Reconnaissance investigation
* Incident investigation
* Dashboard development

The telemetry pipeline therefore connects infrastructure engineering with practical security operations.

---

# Result

The laboratory architecture establishes a centralized endpoint telemetry pipeline:

```text
Windows Endpoint
      ↓
Sysmon + Windows Event Logs
      ↓
Splunk Universal Forwarder
      ↓
TCP 9997
      ↓
Splunk Enterprise
      ↓
Splunk Web
      ↓
SPL Analysis
```

This provides the foundation for the detection and investigation workflows developed in the later stages of the project.

---

# Evidence

Where available, screenshots and command output should be added to the repository to demonstrate:

* Windows event generation
* Sysmon telemetry
* Universal Forwarder status
* Splunk receiving configuration
* Searchable events
* Authentication events
* Successful telemetry ingestion

Evidence should be sanitized before publication to remove:

* Private IP addresses
* Hostnames that identify personal infrastructure
* Usernames where unnecessary
* Authentication secrets
* Tokens or credentials

---

# Next Stage

With endpoint telemetry available in Splunk, the project moves from infrastructure and ingestion toward **security analysis**.

The next stage focuses on developing searches and detections from the collected telemetry.

See:

```text
../detection/
```
