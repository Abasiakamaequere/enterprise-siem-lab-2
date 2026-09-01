# Telemetry

## Windows Endpoint Telemetry Collection

This directory documents the endpoint telemetry architecture implemented to connect a Windows workstation to the centralized Splunk Enterprise SIEM.

The telemetry phase extends the laboratory from a functioning SIEM server into a basic Security Operations Center (SOC) environment capable of collecting, centralizing, searching, and analyzing endpoint security events.

The implementation combines **Windows Event Logs**, **Sysmon**, and the **Splunk Universal Forwarder** to provide security telemetry from the Windows endpoint to the Ubuntu-based Splunk server.

---

## Telemetry Architecture

```text
┌──────────────────────────────────┐
│       Windows Endpoint VM        │
│                                  │
│  Windows Event Logs              │
│  Security Events                 │
│  Sysmon Telemetry                │
└───────────────┬──────────────────┘
                │
                │
                ▼
┌──────────────────────────────────┐
│    Splunk Universal Forwarder    │
│                                  │
│  Collect                         │
│  Monitor                         │
│  Forward                         │
└───────────────┬──────────────────┘
                │
                │ TCP 9997
                │
                ▼
┌──────────────────────────────────┐
│       Ubuntu Server 25.04        │
│                                  │
│       Splunk Enterprise          │
│          10.4.2                  │
│                                  │
│          Indexer                 │
└───────────────┬──────────────────┘
                │
                ▼
┌──────────────────────────────────┐
│           Splunk Web             │
│                                  │
│  Search                          │
│  Investigation                   │
│  Threat Hunting                  │
│  Dashboards                      │
└──────────────────────────────────┘
````

---

## Telemetry Components

| Component                  | Role                                          |
| -------------------------- | --------------------------------------------- |
| Windows Endpoint           | Monitored host                                |
| Windows Event Logs         | Native operating-system security telemetry    |
| Sysmon                     | Enhanced Windows system and process telemetry |
| Splunk Universal Forwarder | Endpoint data collection and forwarding       |
| TCP 9997                   | Splunk receiving channel                      |
| Ubuntu Server 25.04        | SIEM infrastructure                           |
| Splunk Enterprise 10.4.2   | Central telemetry processing and indexing     |
| Splunk Web                 | Search and analysis interface                 |

---

## Telemetry Collection Model

The endpoint telemetry architecture follows a collection-and-forwarding model.

```text
Endpoint Activity
       │
       ▼
Windows Event Logs
       │
       ├──────────────┐
       │              │
       ▼              ▼
Security Events     Sysmon
       │              │
       └──────┬───────┘
              │
              ▼
    Splunk Universal
       Forwarder
              │
              │ TCP 9997
              ▼
     Splunk Enterprise
              │
              ▼
          Splunk Web
```

---

# Phase 1 — Windows Endpoint

A Windows virtual machine was introduced as the monitored endpoint.

The endpoint provides the activity from which security telemetry is generated.

The objective was not simply to collect generic Windows logs, but to establish an environment in which endpoint activity could be observed centrally through the SIEM.

The Windows endpoint therefore became the primary telemetry source for the SOC laboratory.

---

# Phase 2 — Windows Event Logs

Windows Event Logs provide the foundational operating-system telemetry used by the laboratory.

Security-related Windows events provide visibility into activities such as:

* Authentication
* Successful logons
* Failed logons
* Account activity
* Security events

These events form an important baseline for authentication monitoring and investigation.

---

# Phase 3 — Sysmon

Sysmon was introduced to provide additional endpoint telemetry beyond the standard Windows Security Event Log.

Sysmon provides enhanced visibility into endpoint activity and enables the laboratory to observe additional security-relevant events.

The telemetry architecture therefore combines:

```text
Windows Security Events
        +
Sysmon Telemetry
        ↓
Centralized SIEM
```

This provides broader visibility into endpoint behavior.

---

# Phase 4 — Splunk Universal Forwarder

The Splunk Universal Forwarder was installed on the Windows endpoint.

Its role is to:

1. Monitor configured Windows event sources
2. Collect endpoint telemetry
3. Forward collected events
4. Send the data to the central Splunk Enterprise server

The forwarder acts as the telemetry transport layer between the endpoint and the SIEM.

```text
Windows Endpoint
       │
       ▼
Universal Forwarder
       │
       ▼
Splunk Enterprise
```

---

# Phase 5 — Receiving Telemetry

The Universal Forwarder was configured to send telemetry to the Ubuntu-based Splunk server.

The primary receiving channel is:

```text
TCP 9997
```

The resulting communication path is:

```text
Windows Endpoint
       │
       │ TCP 9997
       ▼
Ubuntu Server 25.04
       │
       ▼
Splunk Enterprise
```

---

# Phase 6 — Telemetry Validation

After configuring the endpoint and forwarder, telemetry was validated within Splunk.

The validation process focused on determining whether events successfully traversed the entire pipeline:

```text
Windows Activity
       ↓
Windows Event Log / Sysmon
       ↓
Universal Forwarder
       ↓
TCP 9997
       ↓
Splunk Enterprise
       ↓
Splunk Search
```

This end-to-end validation was important because a successful forwarder installation does not automatically demonstrate that useful security telemetry is reaching the SIEM.

---

# Security Events

The telemetry pipeline enabled investigation of Windows authentication activity.

Particular attention was given to:

### Event ID 4624

Successful account logon.

### Event ID 4625

Failed account logon.

These events provide a foundation for authentication monitoring and later detection engineering.

---

# Telemetry Troubleshooting

The telemetry phase also involved troubleshooting data-collection problems.

Potential failure points in the pipeline include:

```text
Windows Event Log
        ↓
Sysmon
        ↓
Universal Forwarder
        ↓
Network
        ↓
Splunk Receiver
        ↓
Indexer
        ↓
Search
```

Troubleshooting therefore required checking the pipeline progressively rather than assuming that an absence of events represented a Splunk search problem.

The detailed troubleshooting process is documented in the repository's troubleshooting section.

---

# Data Flow Validation

The completed telemetry architecture can be represented as:

```text
┌───────────────┐
│ Windows VM    │
│               │
│ Endpoint      │
│ Activity      │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│ Windows Logs  │
│ + Sysmon      │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│ Universal     │
│ Forwarder     │
└───────┬───────┘
        │
        │ TCP 9997
        ▼
┌───────────────┐
│ Splunk        │
│ Enterprise    │
│ 10.4.2        │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│ Splunk Web    │
│               │
│ Search        │
│ Analysis      │
└───────────────┘
```

---

# Telemetry Objectives

The endpoint telemetry implementation was designed to support:

* Centralized log collection
* Windows authentication monitoring
* Endpoint visibility
* Security-event searching
* Threat hunting
* Detection development
* Incident investigation
* SOC dashboard development

---

# Engineering Lessons

The telemetry phase demonstrated several important SOC engineering principles.

## 1. Visibility depends on the complete pipeline

Security monitoring is only effective when telemetry successfully moves from the endpoint to the analyst interface.

## 2. Collection and analysis are separate functions

The Universal Forwarder is responsible for collecting and forwarding data.

Splunk Enterprise provides centralized processing, indexing, searching, and analysis.

## 3. Multiple telemetry sources improve visibility

Combining Windows Event Logs with Sysmon provides a broader endpoint view than relying on a single event source.

## 4. Validate data flow end-to-end

A forwarder service running successfully does not necessarily mean that events are being indexed correctly.

Validation must therefore occur at multiple stages.

---

# Telemetry Outcome

The laboratory progressed from a standalone SIEM server to a centralized endpoint-monitoring architecture.

The resulting flow is:

**Windows Endpoint → Windows Event Logs + Sysmon → Universal Forwarder → TCP 9997 → Splunk Enterprise → Splunk Web**

This created the telemetry foundation required for the subsequent threat-hunting, detection, and investigation phases.

---

# Next Stage

The next stage documents the individual endpoint components:

1. Windows endpoint preparation
2. Sysmon installation and configuration
3. Splunk Universal Forwarder installation
4. Forwarder configuration
5. Splunk receiving configuration
6. End-to-end telemetry validation

These components are documented in the files within this directory.
