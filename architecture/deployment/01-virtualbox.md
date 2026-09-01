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
