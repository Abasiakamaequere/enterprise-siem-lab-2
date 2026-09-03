# Troubleshooting 04 — Filesystem Recovery

## Problem

Following the virtual machine freeze and hard reset during Splunk installation, the Ubuntu Server did not return directly to normal operation.

The system entered Emergency Mode and required filesystem recovery.

## Symptoms

After restarting the virtual machine:

* Ubuntu entered Emergency Mode.
* Filesystem inconsistencies were detected.
* Normal system operation could not continue until the affected filesystem was checked and repaired.

## Diagnosis

The failure was treated as a filesystem-integrity problem resulting from the abnormal interruption of the virtual machine.

The filesystem was checked using:

```bash
fsck -y /dev/sda5
````

## Recovery Action

The filesystem check repaired the detected filesystem inconsistencies.

The recovery sequence was:

```text
Virtual Machine Freeze
        ↓
Hard Reset
        ↓
Ubuntu Emergency Mode
        ↓
Filesystem Diagnosis
        ↓
fsck
        ↓
Filesystem Repair
        ↓
System Recovery
```

## Engineering Context

The incident demonstrated that an infrastructure failure during application installation can affect the underlying operating system and filesystem.

The recovery process therefore had to address the operating system before Splunk could be returned to normal operation.

## Outcome

The filesystem was successfully repaired and the Ubuntu Server was able to continue through the recovery process.

Additional package-management recovery was subsequently required before the Splunk deployment could be fully restored.

## Lessons Learned

* Unexpected VM shutdowns can result in filesystem inconsistencies.
* Filesystem integrity should be checked after an abnormal system interruption.
* Infrastructure recovery should proceed from the operating-system layer upward.
* Application recovery should not begin until the underlying system is stable.
* Recovery procedures should be documented separately from normal deployment procedures.
