# Endpoint Telemetry

This directory documents endpoint telemetry collection and delivery into Splunk.

## Components

* Windows endpoint
* Windows Security Event Logs
* Sysmon
* Splunk Universal Forwarder

## Telemetry Pipeline

```text
Windows Endpoint
       ↓
Windows Event Logs
       +
Sysmon
       ↓
Universal Forwarder
       ↓
TCP 9997
       ↓
Splunk Enterprise
```

The telemetry pipeline is being developed and validated incrementally.
