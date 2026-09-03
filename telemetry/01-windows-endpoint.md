# 01 — Windows Endpoint

## Objective

The Windows virtual machine serves as the monitored endpoint within the Security Operations laboratory.

After establishing the Ubuntu Server and Splunk Enterprise infrastructure, the laboratory was expanded to introduce an endpoint capable of generating realistic Windows security telemetry.

The endpoint provides the data source required for subsequent SIEM monitoring, threat hunting, detection, and investigation activities.

---

## Endpoint Role

The Windows VM functions as the endpoint under observation.

Its role is to:

- Generate Windows security events
- Generate system activity
- Generate process and network telemetry through Sysmon
- Forward relevant events to Splunk
- Provide realistic data for SOC investigations

---

## Telemetry Architecture

The endpoint is connected to the central Splunk infrastructure through the Splunk Universal Forwarder.

```text
┌──────────────────────────────┐
│       Windows Endpoint       │
│                              │
│  Windows Event Logs          │
│  Sysmon Telemetry            │
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
│ Ubuntu Server 25.04          │
│                              │
│ Splunk Enterprise 10.4.2     │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│         Splunk Web           │
│                              │
│ Search / Detection /         │
│ Investigation / Dashboards   │
└──────────────────────────────┘
````

---

# Windows Event Telemetry

Windows generates a wide range of events that can be useful for security monitoring.

The laboratory focuses on telemetry that can support analysis of:

* Authentication activity
* Account activity
* Process execution
* System activity
* Network-related activity
* Potential reconnaissance
* Suspicious endpoint behavior

Windows Security Event Logs provide an important baseline for authentication and account-related investigations.

---

# Sysmon Telemetry

Sysmon extends the native Windows event telemetry available to the laboratory.

It provides additional visibility into endpoint activity that can be useful for security operations and threat hunting.

The laboratory uses Sysmon as an additional telemetry source rather than relying exclusively on the standard Windows Security log.

This creates a richer endpoint dataset for Splunk analysis.

---

# Endpoint Data Sources

The resulting endpoint telemetry model is:

```text
Windows
   │
   ├── Windows Security Events
   │
   ├── Windows System Events
   │
   └── Sysmon Events
           │
           ▼
   Splunk Universal Forwarder
           │
           ▼
      TCP 9997
           │
           ▼
   Splunk Enterprise
```

---

# Why Endpoint Telemetry Matters

A SIEM is only as useful as the telemetry available to it.

The Ubuntu Server provides the infrastructure for the SIEM, but the Windows endpoint provides the security events that analysts can investigate.

This distinction can be represented as:

```text
Infrastructure
      ↓
Splunk Enterprise
      ↓
Telemetry
      ↓
Analysis
      ↓
Detection
      ↓
Investigation
```

The Windows endpoint therefore transforms the project from a standalone SIEM installation into a small security-monitoring environment.

---

# Laboratory Scope

The endpoint exists inside the controlled VirtualBox laboratory.

The environment is intended for:

* Security monitoring practice
* Log-analysis practice
* Threat-hunting exercises
* Detection engineering
* Controlled reconnaissance testing
* Investigation workflows

Activities are performed within the sandboxed laboratory environment.

---

# Validation Objective

Before moving to detection and investigation, the endpoint must successfully provide telemetry to the Splunk server.

The validation chain is:

```text
Windows Event Generated
        ↓
Sysmon / Windows Event Log
        ↓
Universal Forwarder
        ↓
TCP 9997
        ↓
Splunk Enterprise
        ↓
Searchable Event
```

Successful completion of this chain demonstrates that the endpoint is contributing usable security telemetry to the SIEM.

---

# Next Stage

The next stage focuses on **Sysmon installation and endpoint telemetry configuration**.

This will establish the additional process, network, and system visibility required for later threat-hunting and detection activities.

See:

```text
02-sysmon.md
```
