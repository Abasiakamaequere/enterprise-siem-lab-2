# 03 — Splunk Universal Forwarder

## Objective

The Splunk Universal Forwarder (UF) provides the collection and transport layer between the Windows endpoint and the central Splunk Enterprise server.

Its purpose within the laboratory is to collect Windows and Sysmon telemetry and forward the resulting events to Splunk Enterprise for centralized analysis.

---

# Architecture

The Universal Forwarder sits between the endpoint telemetry sources and the central SIEM.

```text
┌──────────────────────────────┐
│       Windows Endpoint       │
│                              │
│ Windows Event Logs           │
│ Sysmon Telemetry             │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ Splunk Universal Forwarder   │
│                              │
│ Collect                      │
│ Process                      │
│ Forward                      │
└──────────────┬───────────────┘
               │
               │ TCP 9997
               ▼
┌──────────────────────────────┐
│ Ubuntu Server 25.04          │
│                              │
│ Splunk Enterprise 10.4.2     │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│          Splunk Web          │
│                              │
│ Search / Detection /         │
│ Investigation / Dashboards   │
└──────────────────────────────┘
````

---

# Role of the Universal Forwarder

The Universal Forwarder is responsible for moving endpoint telemetry from the Windows system to the central Splunk platform.

This separates the endpoint collection function from the central SIEM processing function.

The architecture can therefore be divided into three layers:

```text
Telemetry Generation
        ↓
Windows + Sysmon

Telemetry Collection & Transport
        ↓
Splunk Universal Forwarder

Centralized Analysis
        ↓
Splunk Enterprise
```

---

# Data Sources

The endpoint provides multiple telemetry sources.

## Windows Event Logs

Windows native event logging provides information useful for monitoring activities such as:

* Authentication
* Account activity
* System events
* Security events

## Sysmon

Sysmon provides additional endpoint telemetry that can support:

* Process analysis
* Network activity analysis
* Threat hunting
* Detection development
* Investigation

The Universal Forwarder provides the transport mechanism for these event sources.

---

# Destination

The Universal Forwarder forwards collected telemetry to the Splunk Enterprise server running on Ubuntu Server 25.04.

The receiving path uses:

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

# Forwarding Architecture

The complete endpoint-to-SIEM data flow is:

```text
                    WINDOWS ENDPOINT
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
      Windows Event Logs             Sysmon
             │                           │
             └─────────────┬─────────────┘
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

# Collection Principle

The Universal Forwarder is intentionally positioned close to the telemetry source.

Instead of requiring the central Splunk server to continuously access the Windows endpoint, the endpoint itself forwards its relevant telemetry to the SIEM.

This architecture provides a clear separation between:

```text
Endpoint
```

and:

```text
Central SIEM
```

---

# Operational Validation

Installing the Universal Forwarder is only one part of the deployment.

The important validation question is:

> Can endpoint events successfully travel from Windows to Splunk and become searchable?

The validation chain is:

```text
1. Windows generates an event
          ↓
2. Sysmon / Windows Event Log records it
          ↓
3. Universal Forwarder collects it
          ↓
4. Forwarder sends it to TCP 9997
          ↓
5. Splunk Enterprise receives it
          ↓
6. Event becomes searchable
```

Successful telemetry ingestion therefore validates the complete pipeline.

---

# Troubleshooting Model

If events do not appear in Splunk, the problem should be investigated systematically.

```text
Windows Activity
       ↓
Is an event generated?
       ↓
Windows Event Log
       ↓
Is the event present?
       ↓
Universal Forwarder
       ↓
Is the Forwarder running?
       ↓
Forwarding Configuration
       ↓
Is the correct destination configured?
       ↓
Network
       ↓
Can the endpoint reach TCP 9997?
       ↓
Splunk Enterprise
       ↓
Is the receiving service available?
       ↓
Splunk Search
       ↓
Is the event searchable?
```

This approach avoids assuming that a missing event is automatically a Splunk problem.

---

# Security Operations Value

The Universal Forwarder enables the laboratory to operate as a centralized security-monitoring environment.

Without forwarding, endpoint telemetry would remain isolated on the Windows machine.

With forwarding:

```text
Endpoint Activity
       ↓
Centralized Collection
       ↓
Centralized Search
       ↓
Correlation
       ↓
Threat Hunting
       ↓
Detection
       ↓
Investigation
```

This is the key transition from endpoint logging to SIEM-based security operations.

---

# Telemetry Pipeline

The completed architecture can be represented as:

```text
┌─────────────────────────────────────────┐
│              DATA SOURCE                │
│                                         │
│ Windows Security Events                 │
│ Sysmon                                  │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│             COLLECTION                  │
│                                         │
│ Splunk Universal Forwarder              │
└────────────────────┬────────────────────┘
                     │
                     │ TCP 9997
                     ▼
┌─────────────────────────────────────────┐
│             SIEM PLATFORM               │
│                                         │
│ Splunk Enterprise 10.4.2                │
│ Ubuntu Server 25.04                     │
└────────────────────┬────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────┐
│              ANALYSIS                   │
│                                         │
│ SPL                                     │
│ Threat Hunting                          │
│ Detection                               │
│ Investigation                           │
│ Dashboards                              │
└─────────────────────────────────────────┘
```

---

# Result

The Splunk Universal Forwarder establishes the transport layer required to move endpoint telemetry into the central Splunk Enterprise environment.

The resulting laboratory architecture now contains:

* Windows endpoint
* Windows Event Logs
* Sysmon
* Splunk Universal Forwarder
* Ubuntu Server 25.04
* Splunk Enterprise 10.4.2
* TCP 9997 forwarding
* Splunk Web

This creates the foundation for centralized endpoint monitoring.

---

# Next Stage

The next stage is to document the **actual telemetry ingestion and validation process**.

This will demonstrate that endpoint events were not merely configured to be forwarded, but were successfully received and searched within Splunk.

See:

```text
04-data-ingestion.md.
```
