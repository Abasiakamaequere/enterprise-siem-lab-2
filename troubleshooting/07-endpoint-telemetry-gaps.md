# Troubleshooting 07 — Endpoint Telemetry Gaps

## Problem

After the Splunk infrastructure was operational, endpoint telemetry had to be validated end-to-end.

The main challenge was ensuring that Windows security events and Sysmon events were not only being generated locally, but were also being forwarded through the Universal Forwarder and indexed successfully in Splunk.

## Symptoms

Initial validation required checking multiple stages of the telemetry pipeline:

- Windows was generating security events.
- Sysmon was generating process activity telemetry.
- The Universal Forwarder service was running.
- Forwarding configuration had to point to the Splunk receiver.
- TCP connectivity to port 9997 had to be confirmed.
- Splunk had to be configured to receive the forwarded data.
- Events had to become searchable in Splunk.

A failure at any stage could result in missing endpoint telemetry.

## Diagnosis

The telemetry path was validated progressively rather than assuming that a running service meant successful ingestion.

The validation chain was:

```text
Windows Event Logs
        ↓
Sysmon
        ↓
Universal Forwarder
        ↓
TCP 9997
        ↓
Splunk Receiver
        ↓
Splunk Indexing
        ↓
Splunk Search
````

Evidence was collected for the major stages of this pipeline.

## Remediation

The endpoint telemetry configuration was validated by confirming:

1. Sysmon was active on the Windows endpoint.
2. The Universal Forwarder service was running.
3. `outputs.conf` contained the forwarding destination.
4. TCP connectivity to Splunk port 9997 was available.
5. Splunk was configured to receive data on port 9997.
6. Forwarded telemetry was searchable in Splunk.

Authentication events such as Windows Event IDs 4624 and 4625 and Sysmon process creation events were then used to validate the telemetry pipeline.

## Outcome

Endpoint telemetry was successfully forwarded from the Windows endpoint to Splunk and became searchable for security monitoring and investigation.

The successful search of Sysmon process creation events in Splunk provided end-to-end confirmation that endpoint telemetry had traversed the forwarding pipeline.

## Lessons Learned

* A running forwarder service does not by itself prove successful data ingestion.
* Endpoint telemetry should be validated hop by hop.
* Network connectivity and receiver configuration are separate validation points.
* Raw endpoint events and indexed Splunk events provide stronger evidence when correlated.
* Troubleshooting telemetry requires separating event generation, forwarding, transport, receiving, indexing, and searchability.

## Engineering Principle

**Validate the complete telemetry path, not just individual components.**
