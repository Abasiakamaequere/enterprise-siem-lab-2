# 03 — Network Configuration & Troubleshooting

## Objective

Reliable network connectivity was required for the acquisition of Splunk Enterprise and for communication between the laboratory components.

During the deployment, the Ubuntu Server encountered a connectivity problem while attempting to download the Splunk Enterprise installation package.

This became the first significant network troubleshooting exercise in the laboratory.

---

# Initial Network Problem

The initial attempt to download the Splunk Enterprise package from the Ubuntu Server did not complete successfully.

The connection attempted to communicate with the Splunk download infrastructure using IPv6.

The connection repeatedly timed out.

The observed pattern was:

```text
Ubuntu Server
      ↓
wget
      ↓
Splunk Download Infrastructure
      ↓
IPv6 Connection
      ↓
Connection Timeout
```
The problem prevented the installation package from being reliably acquired directly from the Ubuntu Server.

## Diagnosis

The failure was investigated as a network connectivity problem rather than immediately treating it as a Splunk installation problem.

The important observation was that the connection was attempting to use an IPv6 route that was not functioning reliably within the laboratory environment.

This distinction was important because the Splunk package itself had not yet been installed. The failure occurred during the package-acquisition stage.

## IPv4 Workaround

The download command was modified to explicitly force IPv4.

wget -4 -O splunk-10.4.2.deb "https://download.splunk.com/..."

The -4 option forced the connection to use IPv4 rather than IPv6.

This bypassed the problematic IPv6 connection path.

## Secondary Problem — Download Speed

Although forcing IPv4 addressed the connection problem, the download remained extremely slow.

The observed transfer rate dropped to approximately:
33.9 KB/s

The Splunk Enterprise package was approximately:
1.24 GB

At this transfer rate, continuing to download the package directly inside the Ubuntu VM would have been highly inefficient.

This created a second engineering problem:

The network path was technically usable, but the available transfer rate made direct package acquisition impractical.

## Alternative Package Acquisition Strategy

Rather than continuing with the extremely slow transfer inside the Ubuntu environment, the package was acquired through the Windows host.

The workflow was changed to:
Splunk Download
      ↓
Windows Host
      ↓
SCP
      ↓
Ubuntu Server
This separated the external package acquisition problem from the internal laboratory transfer.

## Cross-Platform Transfer

After acquiring the Splunk Enterprise package on the Windows host, the package was transferred to the Ubuntu Server using SCP.

The transfer used the Ubuntu server's SSH service.

Example PowerShell command:
scp "$env:USERPROFILE\Downloads\splunk-10.4.2-linux-amd64.deb" `
equere_splunkadmin@<LAB-IP>:/home/equere_splunkadmin/

The local laboratory IP address is intentionally represented as:
<LAB-IP>

in the public documentation rather than exposing the actual private network address.

## Transfer Performance

The cross-platform transfer achieved approximately:
13.6 MB/s

This represented a major improvement over the approximately 33.9 KB/s download speed experienced directly from the Ubuntu VM.

The package transfer therefore became practical within the laboratory workflow.

## Final Network Workflow

The resulting acquisition workflow was:
                 External Network
                       │
                       ▼
              Splunk Download
                       │
                       ▼
                 Windows Host
                       │
                       │ SCP / SSH
                       ▼
              Ubuntu Server 25.04
                       │
                       ▼
             Splunk Installation

## Engineering Analysis

The problem demonstrated the importance of separating different parts of a network troubleshooting process.

The original failure could have been incorrectly interpreted as:
"Splunk download is broken."

However, the troubleshooting process identified multiple distinct factors:

1. IPv6 connectivity failure
2. Successful use of IPv4 as a workaround
3. Extremely low transfer performance
4. Alternative package acquisition through the Windows host
5. Efficient SCP transfer into the Ubuntu environment
This resulted in a more reliable deployment workflow.

## Lessons Learned
1. Test connectivity before troubleshooting the application

The failure occurred before Splunk was installed, so the network path had to be investigated first.

2. Distinguish connectivity from performance

Forcing IPv4 resolved the connection problem, but it did not resolve the extremely slow transfer rate.

These were separate problems.

3. Use the available infrastructure strategically

The Windows host already had a functioning network path capable of obtaining the large installation package.

Using it as an intermediate staging point avoided spending hours waiting for the Ubuntu download.

4. SCP provides a practical cross-platform transfer mechanism

Once the package was available on the Windows host, SCP provided an efficient way to move it into the Linux environment.

## Result

The networking problem was successfully worked around.

The final process allowed the approximately 1.24 GB Splunk Enterprise package to be transferred into the Ubuntu Server environment at approximately 13.6 MB/s.

This enabled the project to proceed to the Splunk Enterprise installation stage.

## Next Stage

With the installation package successfully staged on Ubuntu Server 25.04, the next stage involved installing Splunk Enterprise and dealing with the resource and system-recovery problems that occurred during the installation process.

See:
04-splunk-installation.md
