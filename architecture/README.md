# Architecture

## Overview

This directory documents the architecture and data flow of the Security Operations laboratory.

The laboratory was developed as an isolated VirtualBox environment to provide hands-on experience with SIEM infrastructure, endpoint telemetry, centralized log collection, and security analysis.

## Current Environment

| Component                  | Role                                       |
| -------------------------- | ------------------------------------------ |
| VirtualBox                 | Virtualization / laboratory infrastructure |
| Ubuntu Server 25.04        | SIEM server operating system               |
| Splunk Enterprise 10.4.2   | Central SIEM platform                      |
| Windows VM                 | Monitored endpoint                         |
| Sysmon                     | Endpoint telemetry                         |
| Splunk Universal Forwarder | Endpoint log forwarding                    |
| Splunk Web                 | SIEM analyst interface                     |

## Current Data Flow

```text
┌─────────────────────────────┐
│       Windows Endpoint      │
│                             │
│ Windows Event Logs          │
│ Sysmon Telemetry            │
└──────────────┬──────────────┘
               │
               │
               ▼
┌─────────────────────────────┐
│ Splunk Universal Forwarder  │
└──────────────┬──────────────┘
               │
               │ TCP 9997
               ▼
┌─────────────────────────────┐
│      Ubuntu Server 25.04    │
│                             │
│    Splunk Enterprise        │
└──────────────┬──────────────┘
               │
               │ TCP 8000
               ▼
┌─────────────────────────────┐
│       Splunk Web            │
│                             │
│ SOC Monitoring              │
│ Threat Hunting              │
│ Investigation               │
│ Dashboards                  │
└─────────────────────────────┘
```

## Infrastructure Design

The original laboratory was provisioned using VirtualBox with a constrained resource profile designed to simulate a small enterprise security-monitoring environment.

The baseline allocation included:

* 2 vCPUs
* 4 GB RAM
* 35 GB dynamically allocated storage
* Bridged networking

The current server operating system is **Ubuntu Server 25.04**.

## Network Architecture

The laboratory uses virtualized networking to allow communication between the monitored Windows endpoint and the Splunk server.

The key communication paths are:

| Connection                    | Purpose                       |
| ----------------------------- | ----------------------------- |
| Windows → Universal Forwarder | Endpoint telemetry collection |
| Universal Forwarder → Splunk  | Security event forwarding     |
| Forwarder → Splunk            | TCP 9997                      |
| Host → Splunk Web             | Analyst access                |
| Host → Splunk Web             | TCP 8000                      |

## Security Architecture

The deployment incorporates the principle of least privilege by running the Splunk service under a dedicated non-root service account rather than relying on unrestricted root execution.

This provides separation between:

* Administrative operating-system privileges
* Splunk service execution
* Security monitoring activity

## Architecture Evolution

The laboratory was developed incrementally.

```text
VirtualBox Infrastructure
          ↓
Ubuntu Server
          ↓
Network Configuration
          ↓
Splunk Enterprise
          ↓
Infrastructure Recovery
          ↓
Least-Privilege Service
          ↓
Splunk Web
          ↓
Windows Endpoint
          ↓
Universal Forwarder
          ↓
Security Telemetry
          ↓
Threat Hunting
          ↓
Detection & Investigation
```

The architecture will continue to evolve as additional telemetry sources, detections, dashboards, and security-monitoring capabilities are implemented.
