# 🛡️ Enterprise SIEM Laboratory

### Splunk Enterprise | Ubuntu Server 25.04 | Windows | Sysmon | Universal Forwarder

A hands-on Blue Team and Security Operations laboratory demonstrating SIEM deployment, endpoint telemetry collection, threat hunting, detection engineering, security investigation, infrastructure troubleshooting, and system recovery.

---

## 📌 Project Overview

This project documents the development of a sandboxed Security Operations laboratory using VirtualBox.

The environment was built to develop practical cybersecurity engineering skills by combining:

* SIEM deployment
* Linux infrastructure administration
* Windows endpoint monitoring
* Security telemetry collection
* Log analysis
* SPL threat hunting
* Detection engineering
* SOC dashboard development
* Infrastructure troubleshooting
* System recovery

The laboratory evolved through multiple implementation stages, including network failures, resource exhaustion, filesystem recovery, privilege-separation requirements, endpoint telemetry configuration, and security investigations.

---

## 🏗️ Current Architecture

```text
Windows Endpoint
      │
      │ Windows Event Logs
      │ + Sysmon Telemetry
      ▼
Splunk Universal Forwarder
      │
      │ TCP 9997
      ▼
Ubuntu Server 25.04
      │
      │ Splunk Enterprise
      │
      │ Web Interface :8000
      ▼
SOC Monitoring & Investigation
```

---

## 🧰 Technology Stack

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
| Security Operations | Threat Hunting, Log Analysis, Detection & Investigation |

---

## 🔧 Engineering Work

The project includes:

* Virtual machine provisioning
* Linux server configuration
* Splunk Enterprise deployment
* Network troubleshooting
* IPv6/IPv4 transport troubleshooting
* Cross-platform file transfer using SCP
* Filesystem recovery
* Package recovery
* Least-privilege service configuration
* Splunk Web deployment
* Windows endpoint integration
* Security event ingestion
* Sysmon deployment
* SPL-based investigation
* SOC dashboard development

---

## 🔍 Security Investigations

The laboratory has been used to investigate:

* Successful Windows authentication events
* Failed authentication events
* Windows security event metadata
* Endpoint telemetry availability
* Controlled reconnaissance activity
* Missing telemetry sources

Example SPL:

```spl
index=* EventCode=4624
```

```spl
index=* EventCode=4625
```

```spl
index=* | stats count by sourcetype
```

---

## 🛠️ Infrastructure Recovery

A major part of this project involved recovering the SIEM infrastructure after a system-level failure during Splunk installation.

The recovery process involved:

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
Splunk Recovery
```

This experience provided practical exposure to Linux troubleshooting, filesystem recovery, package management, and infrastructure resilience.

---

## 📊 SOC Monitoring

A Splunk dashboard was developed to visualize authentication activity and support security monitoring.

Future monitoring capabilities include:

* Authentication anomalies
* Failed logon detection
* Process activity
* Endpoint reconnaissance
* Suspicious command execution
* Additional Windows security events

---

## 📚 Repository Structure

| Directory            | Purpose                                 |
| -------------------- | --------------------------------------- |
| `architecture/`      | SIEM architecture and network design    |
| `deployment/`        | Infrastructure and Splunk deployment    |
| `telemetry/`         | Windows, Sysmon and Universal Forwarder |
| `detection/`         | SPL queries and detection logic         |
| `investigations/`    | Security investigation case studies     |
| `dashboards/`        | SOC dashboards and visualizations       |
| `troubleshooting/`   | Engineering failures and recovery       |
| `incident-response/` | Investigation methodology               |
| `evidence/`          | Screenshots and supporting evidence     |
| `scripts/`           | Reusable scripts                        |
| `documentation/`     | Detailed project documentation          |

---

## 🚀 Project Status

## 🚀 Project Status

🟢 SIEM infrastructure — Operational  
🟢 Splunk Web — Operational  
🟢 Windows Security Event ingestion — Operational  
🟢 Sysmon endpoint telemetry — Operational  
🟢 Universal Forwarder telemetry pipeline — Operational  
🟢 Authentication event monitoring — Operational  
🟢 Process creation telemetry — Operational  
🟢 SPL-based investigation — Operational  
🟢 SOC dashboard — Developed  
🟡 Additional detection use cases — In progress  
🟡 Automated alerting and correlation — Planned

---

## 🎯 Skills Demonstrated

**Security Operations**

* SIEM
* Log Analysis
* Threat Hunting
* Detection Engineering
* Security Investigation
* SOC Monitoring

**Infrastructure**

* Linux
* Ubuntu Server
* VirtualBox
* Networking
* System Troubleshooting
* Filesystem Recovery

**Security Technologies**

* Splunk Enterprise
* Sysmon
* Splunk Universal Forwarder
* Windows Event Logs

**Querying**

* SPL

---

## 👨‍💻 Project Objective

The purpose of this laboratory is to develop practical capability in Security Operations and Blue Team engineering through repeated cycles of:

**Build → Monitor → Investigate → Troubleshoot → Recover → Improve**

The project is continuously evolving as new detection, telemetry, investigation, and automation capabilities are added.
