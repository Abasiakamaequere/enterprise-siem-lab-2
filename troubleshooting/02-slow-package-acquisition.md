# Troubleshooting 02 — Slow Package Acquisition

## Problem

After resolving the IPv6 connectivity issue, the Splunk Enterprise package could be downloaded, but the transfer speed inside the Ubuntu Server VM remained extremely slow.

## Symptoms

The download rate dropped to approximately:

```text
33.9 KB/s
````

The Splunk Enterprise package was approximately 1.24 GB in size.

At this transfer rate, downloading the package directly inside the virtual machine would have required many hours.

## Diagnosis

The issue was treated as a package-acquisition and network-transfer performance problem rather than an installation failure.

The Ubuntu VM had working connectivity, but the external download path was too slow for efficient package acquisition.

## Resolution

The Splunk package was acquired through the Windows host instead of continuing the slow transfer directly inside the Ubuntu VM.

The package was then transferred from the Windows host to the Ubuntu Server using SCP over SSH.

The transfer achieved approximately:

```text
13.6 MB/s
```

This reduced the transfer time to under one minute.

## Transfer Architecture

```text
Splunk Package Source
        ↓
Windows Host
        ↓
SCP / SSH
        ↓
Ubuntu Server VM
```

## Engineering Context

The solution separated package acquisition from the SIEM server installation environment.

This avoided spending several hours waiting for an unreliable or extremely slow external transfer while preserving the intended deployment architecture.

## Outcome

The Splunk Enterprise installation package was successfully staged on the Ubuntu Server and was ready for local installation.

## Lessons Learned

* Network connectivity does not necessarily mean acceptable transfer performance.
* Large packages should be staged through the most reliable available path.
* Cross-platform file transfer can be useful when direct VM downloads are impractical.
* Troubleshooting should consider both reliability and operational efficiency.
