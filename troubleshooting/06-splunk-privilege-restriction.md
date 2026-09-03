# Troubleshooting 06 — Splunk Privilege Restriction

## Problem

After recovering the Ubuntu Server and package-management environment, the Splunk service could not be started using the initial root-based approach.

## Symptoms

The initial startup command was:

```bash
sudo /opt/splunk/bin/splunk start --accept-license
````

Splunk rejected the root execution approach because of its root-execution protection.

## Diagnosis

The problem was identified as an application privilege and service-execution restriction rather than a filesystem, package, or network failure.

The initial installation and recovery work had restored the operating system, but Splunk still needed to be executed under an appropriate service identity.

## Security Decision

Rather than bypassing the protection with a root override, the deployment was redesigned to use a dedicated unprivileged Splunk service account.

This follows the **Principle of Least Privilege**.

The approach separates:

* Operating-system administration
* Splunk service execution
* Security monitoring activity

## Remediation

A dedicated Splunk group and user were created:

```bash
sudo groupadd splunk
sudo useradd -m -g splunk splunk
```

Ownership of the Splunk installation was then reassigned:

```bash
sudo chown -R splunk:splunk /opt/splunk
```

Splunk was subsequently started using the dedicated account:

```bash
sudo -u splunk /opt/splunk/bin/splunk start --accept-license
```

## Recovery Sequence

```text
Splunk Root Execution Attempt
          ↓
Root-Execution Protection
          ↓
Privilege Model Review
          ↓
Dedicated Service Account
          ↓
Ownership Adjustment
          ↓
Unprivileged Splunk Startup
          ↓
Operational Splunk Service
```

## Outcome

Splunk successfully initialized using the dedicated unprivileged service account.

The privilege restriction therefore became an opportunity to improve the security architecture rather than something to bypass.

## Lessons Learned

* Application security restrictions should be understood before attempting to bypass them.
* SIEM services should follow least-privilege principles where supported.
* Dedicated service identities provide clearer separation between administration and application execution.
* Infrastructure recovery should include security validation, not only restoration of functionality.
