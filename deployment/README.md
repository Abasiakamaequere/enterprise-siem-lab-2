# Deployment

## Splunk Enterprise SIEM Infrastructure Deployment

This directory documents the infrastructure provisioning, operating-system configuration, Splunk Enterprise installation, network engineering, troubleshooting, recovery, privilege separation, and operational validation performed during the construction of the Security Operations laboratory.

The deployment was performed inside a sandboxed VirtualBox environment.

---

## Current Environment

| Component      | Configuration                    |
| -------------- | -------------------------------- |
| Hypervisor     | VirtualBox                       |
| Server OS      | Ubuntu Server 25.04              |
| SIEM           | Splunk Enterprise 10.4.2         |
| CPU            | 2 vCPU                           |
| Memory         | 4 GB RAM                         |
| Storage        | 35 GB dynamically allocated disk |
| Network        | Bridged virtual adapter          |
| Web Interface  | Splunk Web :8000                 |
| Receiving Port | TCP :9997                        |

> **Note:** Ubuntu Server 25.04 represents the current laboratory environment. Earlier project documentation contains references to previous Ubuntu versions used during the development of the laboratory.

---

## Deployment Architecture

```text
                    VirtualBox Host
                          │
            ┌─────────────┴─────────────┐
            │                           │
            ▼                           ▼
    Ubuntu Server VM              Windows Endpoint VM
       25.04                            │
            │                           │
            │ Splunk Enterprise        │
            │                           │
            │◄──── TCP 9997 ───────────┘
            │
            ▼
       Splunk Web
          :8000
```

---

# Deployment Phases

## Phase 1 — VirtualBox Provisioning

The laboratory began with the creation of a dedicated VirtualBox virtual machine.

The initial resource profile was deliberately constrained to simulate a small security-monitoring environment while remaining suitable for a home laboratory.

### Allocated resources

* **2 vCPUs**
* **4 GB RAM**
* **35 GB dynamically allocated storage**

The network adapter was configured using a bridged networking model to allow the virtual machine to participate directly in the local network.

---

## Phase 2 — Ubuntu Server Installation

The Splunk server was deployed using a headless Ubuntu Server environment.

The server was configured as the Linux foundation for the SIEM platform.

Initial configuration activities included:

* Operating-system provisioning
* User configuration
* Network validation
* Connectivity testing
* Storage validation
* Package-management preparation

The current laboratory server runs **Ubuntu Server 25.04**.

---

# Phase 3 — Network Troubleshooting

The first major engineering challenge occurred during acquisition of the Splunk Enterprise installation package.

An initial `wget` operation repeatedly timed out while attempting to communicate with the Splunk download infrastructure.

The connection attempted an IPv6 endpoint before failing.

Example:

```text
Connecting to download.splunk.com
(IPv6 address)
... failed: Connection timed out.
```

### Diagnosis

The problem was investigated as a network-layer issue rather than immediately assuming that the Splunk package or operating system was defective.

The IPv6 communication path was identified as unreliable in the laboratory environment.

### Remediation

The download operation was modified to explicitly use IPv4:

```bash
wget -4 -O splunk-10.4.2-33c3bf42cd73-linux-amd64.deb "https://download.splunk.com/..."
```

This successfully bypassed the problematic IPv6 route.

---

# Phase 4 — Out-of-Band Package Staging

Although forcing IPv4 resolved the routing problem, the download speed through the VM remained extremely low.

The transfer rate dropped to approximately:

```text
33.9 KB/s
```

At that rate, downloading the approximately **1.24 GB** Splunk Enterprise package directly inside the VM would have taken many hours.

### Engineering decision

Instead of continuing with an unreliable external download path, the installation package was acquired through the Windows host and transferred into the Ubuntu VM.

### Transfer architecture

```text
Splunk Vendor Repository
          │
          ▼
Windows Host
          │
          │ SCP / SSH
          ▼
Ubuntu Server VM
```

The Windows host was used to acquire the large installation package, after which PowerShell and SCP were used to transfer the package to the Ubuntu server.

Example:

```powershell
scp "$env:USERPROFILE\Downloads\splunk-10.4.2-33c3bf42cd73-linux-amd64.deb" `
equere_splunkadmin@<LAB-IP>:/home/equere_splunkadmin/
```

The transfer achieved approximately **13.6 MB/s**, reducing the transfer time to under one minute.

---

# Phase 5 — Splunk Installation Failure

The next major problem occurred during installation of the Splunk Enterprise package.

The installation was initiated using:

```bash
sudo dpkg -i splunk-10.4.2-33c3bf42cd73-linux-amd64.deb
```

During package processing, the virtual machine became severely resource constrained.

The system eventually became unresponsive.

Observed symptoms included:

* Package installation stalled
* Terminal became unresponsive
* SSH connectivity failed
* VirtualBox execution stalled
* Storage operations stopped responding

This transformed the installation problem into an infrastructure-recovery problem.

---

# Phase 6 — Emergency Recovery

Following the system freeze, the virtual machine required a hard reset.

After restarting, Ubuntu detected filesystem inconsistencies and entered Emergency Mode.

The recovery process involved:

```text
System Freeze
      ↓
Hard VM Reset
      ↓
Emergency Mode
      ↓
Filesystem Diagnosis
      ↓
Filesystem Repair
      ↓
Package Database Recovery
      ↓
System Reboot
```

### Filesystem repair

The affected filesystem was checked using:

```bash
fsck -y /dev/sda5
```

The filesystem check repaired structural inconsistencies.

Following filesystem recovery, the package-management system still required remediation.

### Package recovery

```bash
sudo dpkg --configure -a
```

This allowed the interrupted package configuration process to be completed.

The system subsequently returned to an operational state.

---

# Phase 7 — Splunk Privilege Restriction

After recovering the operating system and package-management environment, the next issue occurred during Splunk initialization.

An attempt to start Splunk through root privileges triggered Splunk's root-execution protection.

The initial approach was:

```bash
sudo /opt/splunk/bin/splunk start --accept-license
```

Splunk rejected the standard root execution approach.

### Security decision

Rather than bypassing the restriction using a root override, the deployment was redesigned around a dedicated unprivileged service identity.

This follows the **Principle of Least Privilege**.

---

# Phase 8 — Dedicated Splunk Service Account

A dedicated group and user were created:

```bash
sudo groupadd splunk
sudo useradd -m -g splunk splunk
```

Ownership of the Splunk installation directory was then reassigned:

```bash
sudo chown -R splunk:splunk /opt/splunk
```

Splunk was subsequently started within the restricted service-user context:

```bash
sudo -u splunk /opt/splunk/bin/splunk start --accept-license
```

The Splunk daemon initialized successfully.

---

# Phase 9 — Splunk Web Validation

After successful initialization, Splunk Web was made available through port:

```text
8000
```

The service was then accessed from the Windows host through the laboratory network.

The web interface provided the primary analyst interface for:

* Searching events
* Developing SPL queries
* Investigating security events
* Creating dashboards
* Monitoring incoming telemetry

---

# Deployment Outcome

The infrastructure stage successfully established a functioning Splunk SIEM environment.

### Completed

* [x] VirtualBox environment
* [x] Ubuntu Server deployment
* [x] Network configuration
* [x] Splunk Enterprise installation
* [x] Network troubleshooting
* [x] Cross-platform package transfer
* [x] Filesystem recovery
* [x] Package recovery
* [x] Least-privilege service configuration
* [x] Splunk initialization
* [x] Splunk Web access

---

# Engineering Lessons

This deployment demonstrated that SIEM implementation involves more than installing security software.

The build required troubleshooting across multiple layers:

```text
Network
   ↓
Operating System
   ↓
Storage
   ↓
Package Management
   ↓
Application Security
   ↓
Service Execution
   ↓
Web Access
```

The experience reinforced the importance of:

* Root-cause analysis
* Resource planning
* Least-privilege architecture
* Network troubleshooting
* Recovery procedures
* Cross-platform administration
* Incremental validation

---

## Next Stage

The infrastructure was subsequently extended beyond the SIEM server to incorporate a Windows endpoint, Sysmon, and the Splunk Universal Forwarder.

This enabled the laboratory to progress from:

**SIEM Infrastructure → Security Telemetry → Threat Hunting → Detection → Investigation**
