# Deployment Evidence

This directory contains evidence supporting the provisioning, configuration, and operational validation of the Splunk SIEM infrastructure.

## Evidence Included

- Ubuntu Server 25.04 platform verification
- System resource and disk baseline
- Splunk service and process validation
- Splunk listening-port verification
- Splunk Web operational verification

## Evidence Chain

```text
Ubuntu Server
      ↓
Splunk Enterprise
      ↓
Splunk Services
      ↓
Network Listeners
      ↓
Splunk Web
````

## Available Evidence

The deployment evidence currently includes:

* `02-ubuntu-server-version.png`
* `03-splunk-status.png`
* `04-splunk-ports.png`
* `D03-splunk-web.png`
* `14-system-baseline.png`
* `15-splunk-status.png`
* `16-port-listener.png`

Duplicate or weaker evidence will be excluded where a stronger artifact demonstrates the same validation.

## Evidence Standard

Screenshots are used to demonstrate actual implementation and validation rather than simply reproduce configuration documentation.

All published evidence should be reviewed for sensitive information before publication.
