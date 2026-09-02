# Telemetry Evidence

This directory contains evidence supporting the configuration, transport, and successful ingestion of endpoint telemetry into the Splunk SIEM.

## Evidence Included

- Windows Sysmon endpoint telemetry
- Splunk Universal Forwarder service
- Universal Forwarder output configuration
- TCP 9997 connectivity
- Splunk receiver configuration
- Successful telemetry ingestion into Splunk

## Evidence Chain

```text
Windows Endpoint
      ↓
Sysmon
      ↓
Universal Forwarder
      ↓
outputs.conf
      ↓
TCP 9997
      ↓
Splunk Receiver
      ↓
Splunk Index
      ↓
Searchable Events
````

## Available Evidence

The telemetry evidence currently includes:

* `05-windows-sysmon.png`
* `06-universal-forwarder-service.png`
* `07-forwarder-outputs-conf.png`
* `08-tcp-connectivity.png`
* `09-splunk-receiver-conf.png`
* `10-splunk-telemetry-search.jpg`

These artifacts demonstrate the progression from endpoint event generation through forwarding, transport, reception, and successful indexing.

## Evidence Standard

The evidence should demonstrate actual telemetry flow and validation rather than simply document configuration.

Sensitive information should be sanitized before publication.

Do not publish:

* Passwords
* API keys
* Authentication tokens
* Private credentials
* Unnecessary personal information
* Sensitive infrastructure details
