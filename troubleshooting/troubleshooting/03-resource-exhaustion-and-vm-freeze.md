# Troubleshooting 03 — Resource Exhaustion and VM Freeze

## Problem

During installation of the Splunk Enterprise package, the Ubuntu Server virtual machine experienced severe resource exhaustion.

## Symptoms

The installation caused the virtual machine to become unresponsive.

Observed symptoms included:

* SSH access stopped responding.
* VirtualBox became difficult to interact with.
* The virtual machine appeared frozen.
* Storage operations stopped responding normally.

The laboratory VM was configured with:

* 2 vCPU
* 4 GB RAM
* 35 GB dynamic storage

## Diagnosis

The failure occurred during the local Splunk package installation using:

```text
sudo dpkg -i
````

The behavior indicated that the installation workload exceeded the practical resource capacity of the virtual machine.

The issue was therefore treated as an infrastructure resource-exhaustion event rather than a Splunk configuration problem.

## Recovery Action

Because the virtual machine was no longer responsive, a hard reset was required.

After restarting, Ubuntu entered Emergency Mode, indicating that additional system recovery was required before normal operation could resume.

The recovery process is documented separately in the recovery evidence.

## Engineering Context

This incident demonstrated that successful package acquisition does not guarantee successful installation.

Resource constraints must be considered when deploying enterprise software inside a virtualized laboratory environment.

## Outcome

The system was subsequently recovered and the Splunk environment was restored to an operational state.

Post-recovery validation confirmed that the SIEM infrastructure could again provide its required services.

## Lessons Learned

* Virtual machine resource allocation directly affects installation reliability.
* Large enterprise applications can place significant demands on constrained laboratory systems.
* A frozen VM requires infrastructure-level troubleshooting rather than application-level troubleshooting alone.
* Recovery planning is an important part of SIEM infrastructure engineering.
* Post-recovery validation should confirm that services and network endpoints are operational.
