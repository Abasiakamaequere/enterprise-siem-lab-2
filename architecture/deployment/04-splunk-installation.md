# 04 — Splunk Enterprise Installation

## Objective

The objective of this stage was to install and initialize Splunk Enterprise on the Ubuntu Server 25.04 virtual machine.

Splunk serves as the central SIEM platform for the laboratory and provides the infrastructure required for centralized security-event collection, searching, analysis, threat hunting, and dashboard development.

---

## Splunk Version

| Component | Version |
|---|---|
| SIEM Platform | Splunk Enterprise |
| Version | 10.4.2 |
| Installation Package | Linux `.deb` package |
| Server OS | Ubuntu Server 25.04 |

The approximately 1.24 GB installation package was first acquired and transferred to the Ubuntu Server using the network workflow documented in `03-networking.md`.

---

## Installation Package

After the package had been successfully transferred to the Ubuntu Server, the installation was initiated using Debian package management.

The installation command was:

```bash
sudo dpkg -i splunk-10.4.2-33c3bf42cd73-linux-amd64.deb
```
The use of dpkg allowed the Splunk Enterprise package to be installed directly on the Ubuntu Server.

## Installation Challenge

The installation process did not proceed normally.

During package processing, the virtual machine experienced significant resource pressure.

The installation eventually stalled and the Ubuntu Server became unresponsive.

Observed symptoms included:

* Installation process stopped responding
* Terminal became unresponsive
* SSH connectivity was lost
* VirtualBox became unresponsive
* The virtual machine required a forced restart

This converted what initially appeared to be a software-installation problem into an infrastructure-recovery problem.

## Resource Constraints

The Splunk server was operating with the laboratory's constrained resource configuration:

CPU:     2 vCPU
Memory:  4 GB RAM
Storage: 35 GB dynamically allocated

The resource limitations became an important consideration during installation.

This experience demonstrated that deploying a SIEM platform requires consideration of not only software compatibility but also the available compute, memory, and storage resources.

## System Freeze

The virtual machine eventually became unresponsive during the installation process.

The recovery sequence was:
```text
Splunk Installation
       ↓
Resource Pressure
       ↓
VM Unresponsive
       ↓
Forced VirtualBox Reset
       ↓
Ubuntu Recovery
```
A hard reset was required to regain control of the virtual machine.

## Emergency Mode

Following the restart, Ubuntu detected filesystem inconsistencies and entered Emergency Mode.

The installation failure had therefore progressed beyond the application layer.

The recovery process required investigation and repair of the underlying filesystem before normal package-management operations could continue.

## Filesystem Recovery

The affected filesystem was checked using:

```bash
fsck -y /dev/sda5
```
The filesystem check was used to identify and repair filesystem inconsistencies resulting from the interrupted system state.

The recovery sequence was:
```text
VM Freeze
    ↓
Forced Restart
    ↓
Emergency Mode
    ↓
Filesystem Check
    ↓
Filesystem Repair
```
After the filesystem was repaired, further package-management recovery was required.

## Package Management Recovery

Because the package installation had been interrupted, the Debian package database required configuration recovery.

The following command was used:

```bash
sudo dpkg --configure -a
```
This allowed pending package configuration operations to be completed.

The recovery sequence therefore became:

```text
Filesystem Repair
       ↓
dpkg Recovery
       ↓
Package Configuration
       ↓
System Restoration
```

## Installation Recovery

Once the Ubuntu Server and package-management system had been recovered, the Splunk deployment could continue.

The failure provided an important distinction between:

```text
Application Installation
```
and:

```text
Underlying Infrastructure Health
```
A software installation cannot be treated independently from the operating system, filesystem, available resources, and package-management environment supporting it.

## Engineering Response

The incident was approached as a layered infrastructure problem.

```text
Layer 1 — Application
        ↓
Splunk installation

Layer 2 — Package Management
        ↓
dpkg

Layer 3 — Operating System
        ↓
Ubuntu Server

Layer 4 — Filesystem
        ↓
Filesystem integrity

Layer 5 — Virtual Infrastructure
        ↓
VirtualBox resources
```
This layered approach helped isolate the failure and establish an appropriate recovery sequence.

## Lessons Learned
1. SIEM deployment is resource-sensitive

Even a laboratory deployment of a SIEM platform can place significant demands on virtualized infrastructure.

The 4 GB RAM configuration was sufficient for continued laboratory development but became a relevant constraint during installation.

2. Hard resets can create secondary failures

The forced restart did not simply resolve the original freeze.

It resulted in a filesystem recovery requirement, demonstrating why uncontrolled shutdowns can introduce additional system-level problems.

3. Recovery should proceed from the infrastructure layer upward

The correct recovery sequence was not to immediately retry the Splunk installation.

Instead:

```text
Filesystem
   ↓
Operating System
   ↓
Package Management
   ↓
Splunk
```
The underlying system was stabilized before continuing with the application.

4. Installation failures can provide valuable engineering experience

The failed installation provided practical experience with:

* Linux recovery
* Filesystem checking
* fsck
* Debian package management
* dpkg
* Virtual machine recovery
* Resource troubleshooting

## Result

The Ubuntu Server was successfully recovered following the installation failure.

The package-management environment was restored, allowing the Splunk deployment to continue toward a functioning SIEM platform.

The next stage focused on resolving Splunk's service-execution privilege requirements and implementing a dedicated unprivileged service account.

Related Documentation
* 01-virtualbox.md — Virtual machine provisioning
* 02-ubuntu-server.md — Ubuntu Server deployment
* 03-networking.md — Network troubleshooting and package transfer
* 05-privilege-separation.md — Splunk least-privilege configuration
* troubleshooting/filesystem-recovery.md — Detailed recovery procedure
