# 🛡️ Enterprise SIEM Laboratory

### Splunk Enterprise 10.4.2 | Ubuntu Server 25.04 | Windows | Sysmon | Universal Forwarder

A hands-on Blue Team and Security Operations engineering laboratory demonstrating the deployment, operation, troubleshooting, recovery, and continuous improvement of an enterprise-inspired SIEM environment inside a sandboxed VirtualBox infrastructure.

The project was built to move beyond theoretical cybersecurity knowledge by repeatedly applying the engineering lifecycle:

**Build → Monitor → Investigate → Troubleshoot → Recover → Validate → Improve**

---

## 📌 Project Overview

This project documents the development of a sandboxed Security Operations laboratory using VirtualBox.

The environment combines a Linux-based Splunk SIEM server with a Windows endpoint configured for security telemetry collection through Sysmon and the Splunk Universal Forwarder.

The laboratory was designed to develop practical capability across:

- SIEM deployment
- Linux infrastructure administration
- Windows endpoint monitoring
- Security telemetry collection
- Log ingestion and validation
- SPL-based investigation
- Detection engineering
- SOC monitoring
- Network troubleshooting
- Infrastructure troubleshooting
- Filesystem recovery
- Package recovery
- Service recovery
- Security investigation

Rather than documenting only the final working state, this repository preserves the engineering challenges encountered during implementation and the procedures used to diagnose, recover, and validate the environment.

---

# 🏗️ Current Architecture

```text
                         ┌─────────────────────────┐
                         │     Windows VM          │
                         │       Endpoint          │
                         │                         │
                         │ Windows Event Logs      │
                         │ Sysmon Telemetry        │
                         └────────────┬────────────┘
                                      │
                                      │ Security Telemetry
                                      ▼
                         ┌─────────────────────────┐
                         │ Splunk Universal        │
                         │ Forwarder               │
                         │                         │
                         │ Collection + Forwarding │
                         └────────────┬────────────┘
                                      │
                                      │ TCP 9997
                                      ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Ubuntu Server 25.04                          │
│                                                                 │
│                    Splunk Enterprise 10.4.2                     │
│                                                                 │
│  ┌──────────────────┐        ┌──────────────────────────────┐  │
│  │ Splunk Receiver  │        │ Splunk Search & Reporting    │  │
│  │ TCP 9997         │        │                              │  │
│  └──────────────────┘        │ SPL Investigation            │  │
│                              │ Detection & Monitoring        │  │
│                              └──────────────┬───────────────┘  │
│                                             │                   │
└─────────────────────────────────────────────┼───────────────────┘
                                              │
                                              │ TCP 8000
                                              ▼
                                   ┌─────────────────────┐
                                   │ Host Workstation    │
                                   │ Splunk Web          │
                                   └─────────────────────┘
````

---

# 🧰 Technology Stack

| Category            | Technology                                              |
| ------------------- | ------------------------------------------------------- |
| Hypervisor          | VirtualBox                                              |
| SIEM                | Splunk Enterprise 10.4.2                                |
| Server OS           | Ubuntu Server 25.04                                     |
| Endpoint            | Windows VM                                              |
| Endpoint Telemetry  | Sysmon                                                  |
| Log Forwarding      | Splunk Universal Forwarder                              |
| Query Language      | Splunk Processing Language (SPL)                        |
| Networking          | TCP/IP                                                  |
| Web Interface       | Splunk Web                                              |
| Security Operations | Threat Hunting, Log Analysis, Detection & Investigation |

---

# 🔧 Engineering Implementation

The laboratory was developed through multiple implementation stages rather than as a single installation exercise.

## Infrastructure

* Virtual machine provisioning
* Ubuntu Server configuration
* Resource allocation
* Disk and filesystem validation
* Network configuration
* IPv4/IPv6 transport troubleshooting
* Cross-platform file transfer using SCP
* Linux service management
* Package management
* Splunk Enterprise installation

## SIEM

* Splunk Enterprise deployment
* Splunk Web configuration
* Splunk service validation
* Receiving port configuration
* Forwarder-to-indexer communication
* Telemetry ingestion validation
* SPL searching
* SOC dashboard development

## Endpoint

* Windows VM configuration
* Sysmon deployment
* Windows Event Log monitoring
* Splunk Universal Forwarder deployment
* Forwarder service validation
* `outputs.conf` configuration
* TCP 9997 connectivity testing

---

# 📡 End-to-End Telemetry Pipeline

The telemetry implementation was validated across the complete path rather than assuming that a running Forwarder meant successful ingestion.

```text
Windows Security Activity
          │
          ▼
       Sysmon
          │
          ▼
Universal Forwarder
          │
          ▼
     outputs.conf
          │
          ▼
     TCP 9997
          │
          ▼
 Splunk Receiver
          │
          ▼
 Indexed Events
          │
          ▼
 Splunk Search
          │
          ▼
 Detection / Investigation
```

The evidence repository documents each major validation point.

---

# 🔍 Security Monitoring & Detection

The laboratory has been used to examine security-relevant Windows activity, including:

* Successful authentication events
* Failed authentication events
* Windows security event metadata
* Sysmon process creation events
* Endpoint telemetry availability
* Process execution activity
* Telemetry source visibility
* Controlled reconnaissance activity
* Missing or incomplete telemetry sources

### Example SPL searches

#### Successful Windows Authentication

```spl
index=* EventCode=4624
```

#### Failed Windows Authentication

```spl
index=* EventCode=4625
```

#### Process Creation

```spl
index=* EventCode=1
```

#### Telemetry Source Analysis

```spl
index=* | stats count by sourcetype
```

These searches were used to validate telemetry availability and support security investigation within the SIEM.

---

# 🧪 Detection Engineering Approach

The project treats detection as a progression from raw telemetry toward validated security observations.

```text
Telemetry
    ↓
Understand Event Structure
    ↓
Identify Security-Relevant Activity
    ↓
Develop SPL Search
    ↓
Validate Results
    ↓
Detection Logic
    ↓
Investigation
```

Current detection work includes authentication monitoring and process creation visibility.

Additional detection use cases are being developed as the laboratory evolves.

---

# 📊 SOC Monitoring

A Splunk dashboard was developed to visualize authentication activity and support SOC-style monitoring.

Current and planned monitoring areas include:

* Authentication anomalies
* Failed logon activity
* Process activity
* Endpoint reconnaissance
* Suspicious command execution
* Additional Windows security events
* Telemetry source health

The dashboard component is intended to demonstrate not only data ingestion, but the transformation of security telemetry into information that can support analyst workflows.

---

# ⚠️ Engineering Challenges

A major objective of this project was to document the problems encountered during implementation rather than hiding them.

The laboratory experienced several real engineering challenges while being built and operated.

## Network Troubleshooting

Network communication problems required investigation of:

* IPv4 and IPv6 behaviour
* Network reachability
* Splunk receiving ports
* Endpoint-to-SIEM connectivity
* TCP 9997 transport
* Service-level versus network-level failures

The troubleshooting process required distinguishing between configuration problems and actual connectivity problems.

---

## Resource Exhaustion

The virtualized environment encountered resource constraints during the implementation.

Resource exhaustion contributed to system instability and eventually resulted in a virtual machine freeze.

This required moving from normal configuration work into infrastructure recovery and diagnosis.

---

## Virtual Machine Freeze & Emergency Mode

Following the infrastructure failure, the Ubuntu Server entered an emergency recovery state.

The recovery process required:

```text
Resource Exhaustion
       ↓
Virtual Machine Freeze
       ↓
Emergency Mode
       ↓
Filesystem Diagnosis
       ↓
Filesystem Repair
       ↓
Package Recovery
       ↓
Service Recovery
       ↓
Post-Recovery Validation
```

This became an important part of the laboratory because it demonstrated that SIEM engineering also requires the ability to recover the underlying infrastructure on which the security platform depends.

---

## Filesystem Recovery

Filesystem integrity had to be investigated and repaired before normal operation could resume.

This provided practical exposure to:

* Linux recovery environments
* Filesystem diagnosis
* Filesystem repair
* Boot/recovery troubleshooting
* Post-repair validation

---

## Package Recovery

The system also required package-management recovery after the infrastructure disruption.

The recovery work involved restoring package-management functionality before continuing with the SIEM deployment.

This reinforced the dependency between:

```text
Operating System
      ↓
Package Management
      ↓
Splunk Installation
      ↓
Splunk Service
      ↓
Security Operations
```

---

## Least-Privilege & Service Configuration

The deployment also required attention to execution privileges and service configuration.

Rather than treating the SIEM as an application that could simply be executed with unrestricted privileges, the environment incorporated privilege-separation considerations appropriate to a more enterprise-oriented deployment model.

---

# 🔄 Recovery Engineering

Recovery is treated as a first-class engineering capability within this project.

The objective was not simply to make the system boot again, but to validate that the SIEM infrastructure was operational after recovery.

The recovery lifecycle was:

```text
Failure
  ↓
Diagnosis
  ↓
Recovery Action
  ↓
System Restoration
  ↓
Splunk Service Validation
  ↓
Network Validation
  ↓
Operational SIEM
```

Post-recovery validation included checking the underlying server, Splunk processes, network listeners, Splunk Web, and telemetry functionality where applicable.

The repository deliberately distinguishes **recovery actions** from **post-recovery validation evidence**.

---

# 🧾 Evidence & Validation

The repository contains a dedicated evidence structure supporting the documented implementation.

```text
evidence/
├── deployment/
├── telemetry/
├── detection/
└── recovery/
```

The evidence follows the principle:

```text
Claim
  ↓
Implementation
  ↓
Evidence
  ↓
Validation
```

### Deployment Evidence

Demonstrates:

* Ubuntu Server platform
* Splunk service operation
* Network listeners
* Splunk Web availability
* Infrastructure baseline

### Telemetry Evidence

Demonstrates:

* Sysmon activity
* Universal Forwarder operation
* Forwarder configuration
* TCP 9997 connectivity
* Splunk receiving configuration
* Successful telemetry ingestion

### Detection Evidence

Demonstrates:

* Authentication activity
* Sysmon process creation
* Splunk process-event searching
* Endpoint-to-SIEM detection validation

### Recovery Evidence

Documents the recovery methodology and distinguishes recovery procedures from visual evidence.

No recovery screenshot is fabricated where a dedicated recovery artifact was not captured.

---

# 📚 Repository Structure

```text
.
├── architecture/
│   └── SIEM architecture and network design
│
├── deployment/
│   └── Infrastructure and Splunk deployment
│
├── telemetry/
│   └── Windows, Sysmon and Universal Forwarder
│
├── detection/
│   └── SPL queries and detection logic
│
├── investigations/
│   └── Security investigation case studies
│
├── dashboards/
│   └── SOC dashboards and visualizations
│
├── troubleshooting/
│   └── Engineering failures and troubleshooting
│
├── incident-response/
│   └── Investigation and response methodology
│
├── evidence/
│   ├── deployment/
│   ├── telemetry/
│   ├── detection/
│   └── recovery/
│
├── scripts/
│   └── Reusable scripts
│
└── documentation/
    └── Detailed project documentation
```

---

# 🚦 Project Status

| Capability                             | Status         |
| -------------------------------------- | -------------- |
| SIEM infrastructure                    | 🟢 Operational |
| Splunk Web                             | 🟢 Operational |
| Windows Security Event ingestion       | 🟢 Operational |
| Sysmon endpoint telemetry              | 🟢 Operational |
| Universal Forwarder telemetry pipeline | 🟢 Operational |
| Authentication event monitoring        | 🟢 Operational |
| Process creation telemetry             | 🟢 Operational |
| SPL-based investigation                | 🟢 Operational |
| SOC dashboard                          | 🟢 Developed   |
| Additional detection use cases         | 🟡 In progress |
| Automated alerting and correlation     | 🟡 Planned     |

---

# 🎯 Skills Demonstrated

## Security Operations

* SIEM
* SOC Monitoring
* Log Analysis
* Threat Hunting
* Detection Engineering
* Security Investigation
* Security Telemetry Analysis

## Infrastructure

* Linux
* Ubuntu Server
* VirtualBox
* Networking
* TCP/IP troubleshooting
* Filesystem recovery
* Package recovery
* System troubleshooting
* Service management

## Security Technologies

* Splunk Enterprise
* Sysmon
* Splunk Universal Forwarder
* Windows Event Logs

## Querying

* Splunk Processing Language (SPL)

---

# 💡 What This Project Demonstrates

This laboratory demonstrates more than the ability to install Splunk.

It demonstrates the ability to:

* Build a security monitoring environment
* Configure an enterprise-inspired SIEM architecture
* Integrate Windows endpoint telemetry
* Validate telemetry end-to-end
* Investigate security events
* Develop SPL-based searches
* Troubleshoot network and infrastructure failures
* Recover a damaged Linux environment
* Validate services after recovery
* Document engineering decisions and evidence
* Continuously improve a security operations environment

The most important lesson from the project is that **security engineering depends on both security knowledge and infrastructure engineering capability**.

---

# 🚀 Project Objective

The purpose of this laboratory is to develop practical capability in Security Operations and Blue Team engineering through repeated cycles of:

**Build → Monitor → Investigate → Troubleshoot → Recover → Validate → Improve**

The project remains actively evolving as additional telemetry sources, detection use cases, investigations, dashboards, alerting capabilities, and automation are developed.

---

## ⚠️ Disclaimer

This laboratory is a sandboxed learning environment created for defensive cybersecurity, security operations, SIEM engineering, troubleshooting, and detection development.

All testing and security activity is performed within controlled virtual machines and authorized laboratory infrastructure.
