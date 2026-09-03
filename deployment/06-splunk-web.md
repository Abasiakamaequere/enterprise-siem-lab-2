# 06 — Splunk Web & Service Validation

## Objective

After recovering the Ubuntu Server, completing the Splunk installation, and configuring Splunk to run under a dedicated unprivileged account, the next objective was to verify that the SIEM platform was operational.

Splunk Web provides the primary interface for interacting with the Splunk Enterprise deployment.

---

## Splunk Web

Splunk Web was configured to operate on:

```text
TCP 8000
```
The interface provides access to the Splunk platform for:

* Searching security events
* Developing SPL queries
* Investigating telemetry
* Monitoring incoming data
* Building dashboards
* Performing threat-hunting activities

## Service Architecture

The resulting server architecture was:
```text
┌──────────────────────────────────────┐
│        Ubuntu Server 25.04           │
│                                      │
│       Splunk Enterprise 10.4.2       │
│                 │                    │
│                 │                    │
│                 ▼                    │
│          Splunk Web :8000            │
└─────────────────┬────────────────────┘
                  │
                  │ HTTP/HTTPS access
                  │
                  ▼
           Analyst Workstation
```
The Windows host was used to access the Splunk Web interface running on the Ubuntu Server.

## Validation Process

The validation process followed the principle of testing each major infrastructure layer after configuration.
```text
Ubuntu Server
      ↓
Splunk Installation
      ↓
Splunk Service
      ↓
Port 8000
      ↓
Splunk Web
      ↓
Analyst Access
```
This provided a progressive validation path rather than assuming that a completed installation automatically meant that the SIEM was operational.

## Splunk Service

Splunk was configured to operate using the dedicated:
```text
User: splunk
Group: splunk
```
This maintained the least-privilege architecture established during the previous deployment stage.

The service therefore operated separately from the normal Ubuntu administrative account.

## Web Interface Validation

Once the Splunk service was initialized, Splunk Web became accessible through port:
```text
8000
```
Successful access to the web interface provided confirmation that the core Splunk platform was operational.

The validation established that:

* The Ubuntu Server was operational
* Splunk Enterprise had initialized
* The Splunk service was running
* The web interface was available
* The host workstation could reach the SIEM interface

## Core Deployment Validation

The infrastructure deployment could therefore be represented as:
```text
┌─────────────────────┐
│ VirtualBox          │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Ubuntu Server 25.04 │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Splunk Enterprise   │
│ 10.4.2              │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Splunk Service      │
│ User: splunk        │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Splunk Web :8000    │
└─────────────────────┘
```

## Deployment Milestone

At this point, the laboratory had progressed from a blank virtual machine to a functioning SIEM server.

Infrastructure completed
* VirtualBox laboratory
* Ubuntu Server 25.04
* Network configuration
* Splunk Enterprise 10.4.2
* Installation recovery
* Dedicated Splunk service account
* Least-privilege execution
* Splunk Web
* Analyst access

## Transition to Security Operations

The deployment now moved beyond infrastructure engineering.

The next objective was to introduce a monitored Windows endpoint and begin collecting security telemetry.

The architecture would therefore evolve from:
```text
SIEM Server
```
into:
```text
Windows Endpoint
        ↓
Sysmon
        ↓
Splunk Universal Forwarder
        ↓
TCP 9997
        ↓
Splunk Enterprise
        ↓
Splunk Web
```
This transition marks the beginning of the endpoint telemetry and SOC monitoring phase of the laboratory.

## Result

Splunk Enterprise was successfully brought to an operational state with Splunk Web available through TCP port 8000.

The core SIEM infrastructure was therefore ready to receive telemetry from a Windows endpoint.

## Next Stage

The next stage documents the Windows endpoint and the installation/configuration of Sysmon and the Splunk Universal Forwarder.

See the:
```text
../telemetry/
```
