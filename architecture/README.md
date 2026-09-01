# Architecture

This directory documents the architecture of the Security Operations laboratory.

## Current Environment

The laboratory consists of a VirtualBox-based environment containing:

* Ubuntu Server 25.04
* Splunk Enterprise 10.4.2
* Windows endpoint VM
* Splunk Universal Forwarder
* Sysmon

## Data Flow

```text
Windows Endpoint
      │
      ├── Windows Event Logs
      │
      └── Sysmon Telemetry
              │
              ▼
    Splunk Universal Forwarder
              │
              │ TCP 9997
              ▼
     Splunk Enterprise
     Ubuntu Server 25.04
              │
              ▼
        Splunk Web
           :8000
              │
              ▼
       SOC Analysis
```

Detailed architecture documentation will be added as the laboratory evolves.
