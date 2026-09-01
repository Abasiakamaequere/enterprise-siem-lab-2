# 01 — VirtualBox Laboratory Provisioning

## Objective

The first stage of the SIEM laboratory was the provisioning of a dedicated virtualized environment using VirtualBox.

The objective was to create an isolated and resource-constrained environment capable of supporting a Linux-based Splunk SIEM server while also allowing communication with a Windows endpoint.

---

## Virtualization Platform

| Configuration | Value |
|---|---|
| Hypervisor | VirtualBox |
| Server VM | Ubuntu Server |
| CPU | 2 vCPU |
| Memory | 4 GB RAM |
| Storage | 35 GB dynamically allocated |
| Network | Bridged Adapter |

---

## Resource Allocation

The Ubuntu Server virtual machine was provisioned with:

### CPU

**2 virtual CPUs**

The configuration provided sufficient processing capacity for the laboratory while maintaining a relatively small virtual-machine footprint.

### Memory

**4 GB RAM**

The laboratory was intentionally operated with limited memory resources. This later became relevant during the Splunk installation and troubleshooting process.

### Storage

**35 GB dynamically allocated virtual disk**

Dynamic allocation allowed the virtual disk to grow as required rather than immediately consuming the full allocated capacity on the physical host.

---

## Network Configuration

The virtual machine was configured using a **Bridged Adapter**.

This configuration allowed the Ubuntu Server VM to communicate with other systems on the laboratory network and provided a practical environment for later integration with the Windows endpoint.

The network design ultimately supported communication between:

```text
Windows Endpoint
       │
       │
       ▼
Ubuntu Server
       │
       ▼
Splunk Enterprise
```

### Initial Laboratory Architecture

At the infrastructure-provisioning stage, the environment consisted primarily of the virtualized Ubuntu Server that would later host Splunk Enterprise.

```text
    Physical Host
          │
          │
     VirtualBox
          │
          ▼
┌───────────────────┐
│   Ubuntu Server   │
│       VM          │
│                   │
│  2 vCPU           │
│  4 GB RAM         │
│  35 GB Disk       │
│  Bridged Network  │
└───────────────────┘
```

The environment was subsequently expanded to include a Windows endpoint and Splunk Universal Forwarder.

### Design Considerations

The laboratory was designed around several practical constraints:

1. Resource efficiency

The SIEM environment was deployed within a limited home-laboratory resource budget rather than enterprise hardware.

2. Network accessibility

Bridged networking was selected to facilitate communication between the virtual machines and the host environment.

3. Isolation

Virtualization provided a controlled environment in which security monitoring, troubleshooting, and controlled testing could be performed without directly affecting the host operating system.

4. Expandability

The initial Ubuntu Server deployment was intended to become the central SIEM component of a larger laboratory containing endpoint telemetry and security-analysis capabilities.

### Result

The VirtualBox infrastructure provided the foundation for the subsequent deployment of:

Ubuntu Server
Splunk Enterprise
Splunk Web
Splunk Universal Forwarder
Windows endpoint telemetry
Sysmon
Security investigations
Detection and threat-hunting workflows

### Next Stage

The next stage was the installation and initial configuration of the Ubuntu Server operating system, which became the foundation for the Splunk SIEM deployment.
