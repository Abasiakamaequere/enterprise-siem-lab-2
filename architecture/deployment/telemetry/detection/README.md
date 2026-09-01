# Detection Engineering

This directory contains SPL queries and detection-development work conducted within the SIEM laboratory.

## Current Detection Areas

* Successful authentication
* Failed authentication
* Authentication trends
* Data-source validation
* Endpoint activity
* Reconnaissance activity

## Example

```spl
index=* EventCode=4625
```

This query searches for Windows failed authentication events.

Additional detections will be added as endpoint telemetry coverage expands.
