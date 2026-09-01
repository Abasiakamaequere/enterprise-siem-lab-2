# 02 — Ubuntu Server Deployment

## Objective

The Ubuntu Server VM provides the Linux infrastructure layer on which the Splunk Enterprise SIEM is deployed.

The operating system was configured as a server-focused environment within the VirtualBox laboratory.

---

## Operating System

| Component | Configuration |
|---|---|
| Operating System | Ubuntu Server |
| Current Version | 25.04 |
| Deployment Type | Virtual Machine |
| Hypervisor | VirtualBox |
| Primary Role | Splunk SIEM Server |

> **Important:** Ubuntu Server 25.04 is the current operating-system configuration of the laboratory.

---

## Server Role

The Ubuntu Server functions as the central SIEM infrastructure within the laboratory.

Its primary responsibilities are:

- Hosting Splunk Enterprise
- Receiving endpoint telemetry
- Processing security events
- Providing Splunk Web
- Supporting security investigations
- Supporting detection and threat-hunting activities

The server therefore forms the bridge between the infrastructure layer and the Security Operations layer.

---

## Server Architecture

```text
┌──────────────────────────────────────┐
│        Ubuntu Server 25.04           │
│                                      │
│          Splunk Enterprise            │
│                 │                    │
│        ┌────────┴────────┐           │
│        │                 │           │
│        ▼                 ▼           │
│   Splunk Web        Event Processing │
│      :8000                         │
│                         │            │
│                         ▼            │
│                  Security Data       │
└──────────────────────────────────────┘
```
## Initial Configuration

The Ubuntu Server was prepared as the foundation for the Splunk deployment.

The configuration process included:

* Operating-system installation
* Network configuration
* Connectivity validation
* Package-management operations
* Storage validation
* Preparation for Splunk installation

The server was subsequently used to acquire, transfer, install, recover, and operate Splunk Enterprise.

## Network Validation

Network connectivity was an important prerequisite for the Splunk installation.

During the deployment process, external connectivity was tested from the Ubuntu environment.

The laboratory later encountered a connectivity problem involving IPv6 when attempting to acquire the Splunk Enterprise package.

This issue is documented separately in:

03-networking.md

The network troubleshooting process ultimately resulted in an IPv4-based workaround and an alternative package-transfer approach.

## Storage Considerations

The Ubuntu Server was deployed using the previously documented:

* 35 GB dynamically allocated virtual disk

Storage became particularly important during the Splunk installation because the system experienced resource pressure and subsequently required filesystem recovery.

The recovery process is documented in the troubleshooting section of the repository.

## Resource Considerations

The server operated within the constrained laboratory configuration of:

* 2 vCPUs
* 4 GB RAM
* 35 GB dynamically allocated storage

These limitations were important engineering considerations when deploying Splunk Enterprise.

The installation process demonstrated that SIEM platforms can impose significant resource requirements even within a small laboratory environment.

## Security Considerations

The Ubuntu Server was not treated simply as an application host.

Security considerations included:

## Least privilege

Splunk was ultimately configured to operate using a dedicated unprivileged service account rather than unrestricted root execution.

## Controlled environment

The server operates within a sandboxed VirtualBox laboratory.

## Service separation

The Splunk service identity was separated from normal administrative privileges.

The detailed privilege-separation process is documented in:

05-privilege-separation.md

### Deployment Challenges

The Ubuntu Server became the point at which several infrastructure challenges became visible.

These included:

1. IPv6 connectivity problems
2. Slow package acquisition
3. Large Splunk installation package transfer
4. Resource exhaustion during installation
5. Virtual machine unresponsiveness
6. Filesystem inconsistencies following recovery
7. Interrupted package configuration
8. Splunk privilege restrictions

These challenges were not treated as reasons to abandon the deployment. Instead, they became part of the troubleshooting and recovery process.

## Recovery Dependency

Following a system-level failure during the Splunk installation, the Ubuntu Server entered an emergency recovery state.

The recovery sequence involved:

System Failure
      ↓
Virtual Machine Restart
      ↓
Emergency Mode
      ↓
Filesystem Check
      ↓
Filesystem Repair
      ↓
Package Configuration Recovery
      ↓
System Restoration

The detailed recovery procedure is documented separately under:

troubleshooting/

## Result

The Ubuntu Server successfully became the Linux foundation for the SIEM laboratory.

The completed infrastructure enabled:

* Splunk Enterprise deployment
* Splunk Web access
* Network communication
* Endpoint telemetry reception
* Security-event searching
* SPL-based investigation
* Dashboard development

## Next Stage

With the Ubuntu Server prepared, the next stage focused on establishing reliable network connectivity and acquiring the Splunk Enterprise installation package.

This process revealed an IPv6 connectivity problem and led to the development of an alternative package acquisition and transfer workflow.

See:

03-networking.md
