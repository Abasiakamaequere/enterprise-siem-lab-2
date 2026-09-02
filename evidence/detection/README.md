# Detection Evidence

This directory contains evidence supporting the security detection and monitoring capabilities developed using the Splunk SIEM and Windows endpoint telemetry.

## Detection Areas

The detection evidence covers:

- Windows authentication activity
- Process creation monitoring
- Splunk-based process event detection
- Network activity monitoring
- Correlation and investigation workflows

## Evidence Chain

```text
Endpoint Activity
      ↓
Windows Event Logs / Sysmon
      ↓
Universal Forwarder
      ↓
Splunk SIEM
      ↓
Detection Search
      ↓
Security Investigation
````

## Available Evidence

The detection evidence currently includes:

* `11-auth-events.png`
* `12-process-creation.png`
* `12-splunk-eventcode1.png`

These artifacts demonstrate endpoint security events and their successful visibility within the SIEM.

## Authentication Monitoring

Windows authentication events provide visibility into successful and failed authentication activity.

Relevant Windows Security Event IDs include:

* Event ID 4624 — Successful logon
* Event ID 4625 — Failed logon

## Process Monitoring

Sysmon Event ID 1 provides process creation telemetry.

The corresponding Splunk search demonstrates that process creation telemetry was successfully indexed and made searchable within the SIEM.

## Evidence Standard

Evidence should demonstrate an observable security event and, where applicable, its successful ingestion and detection within Splunk.

Documentation should distinguish between:

* Configured capability
* Observed endpoint activity
* Successfully ingested telemetry
* Demonstrated detection

Sensitive information must be sanitized before publication.
