# 05 — Splunk Privilege Separation

## Objective

Following recovery of the Ubuntu Server and package-management environment, the next stage was to initialize Splunk Enterprise.

During this process, Splunk's root-execution restriction became an important security and configuration consideration.

Rather than bypassing the restriction, the deployment was configured to run Splunk under a dedicated unprivileged service identity.

This implemented the principle of least privilege within the SIEM infrastructure.

---

## Initial Execution Attempt

The initial attempt to start Splunk used elevated privileges:

```bash
sudo /opt/splunk/bin/splunk start --accept-license
```Splunk rejected the root execution approach.
```
This demonstrated that the Splunk service should not be operated using unrestricted root privileges.

## Security Decision

Instead of using a root override or weakening the security control, the deployment was redesigned around a dedicated service account.

The objective was:
```text
Administrative Privileges
        │
        │
        ▼
Operating System Administration

        ≠

Splunk Service Execution
        │
        │
        ▼
Dedicated Unprivileged Account
```
This provides separation between system administration and SIEM service execution.

## Dedicated Splunk Group

A dedicated group was created for the Splunk service:

```Bash
sudo groupadd splunk
```
The group provides an organizational security boundary for files and processes associated with the Splunk installation.

## Dedicated Splunk User

A dedicated user account was then created:

```Bash
sudo useradd -m -g splunk splunk
```
The account was configured as the identity under which Splunk would operate.

The purpose was to prevent the Splunk service from requiring unrestricted root-level execution.

## Splunk Directory Ownership

The Splunk installation directory was reassigned to the dedicated service account:

```Bash
sudo chown -R splunk:splunk /opt/splunk
```
This allowed the Splunk process to access and manage its installation files without requiring the service itself to run as root.

The resulting ownership model was:

```text

/opt/splunk
     │
     ├── Owner: splunk
     └── Group: splunk
```

## Starting Splunk as the Service User

Splunk was subsequently started under the dedicated account:

```Bash
sudo -u splunk /opt/splunk/bin/splunk start --accept-license
```
This allowed the service to initialize without running the Splunk process directly as root.

## Privilege Model

The resulting architecture separates administrative and application privileges:

```text
┌──────────────────────────────┐
│ Ubuntu Administrator         │
│                              │
│ System administration        │
│ Package management           │
│ Filesystem recovery          │
└──────────────┬───────────────┘
               │
               │ Controlled administration
               ▼
┌──────────────────────────────┐
│ Splunk Service Account       │
│                              │
│ User: splunk                 │
│ Group: splunk                │
│                              │
│ Splunk Enterprise            │
└──────────────────────────────┘
```

## Principle of Least Privilege

The configuration follows the principle of least privilege:

Services should operate with only the permissions required to perform their intended functions.

In this laboratory, the approach reduced the need for Splunk to operate with unrestricted operating-system privileges.

The configuration also respected Splunk's built-in protection against root execution rather than attempting to circumvent it.

## Why This Matters in a SIEM Environment

A SIEM platform continuously processes security telemetry and interacts with multiple system components.

Running such a service with unnecessary administrative privileges could increase the potential impact of a compromise of the SIEM application or service account.

Separating the service identity therefore provides an additional security boundary.

The model used in this laboratory is:

```text
Administrator
     │
     │ Administrative tasks
     ▼
Ubuntu Server
     │
     │
     └──────► Splunk Service
                  │
                  ▼
             User: splunk
             Group: splunk
```

## Validation

After ownership and privilege separation were configured, Splunk was started using the dedicated service account.

The successful initialization demonstrated that Splunk could operate without unrestricted root execution.

The next validation stage involved accessing Splunk Web and confirming that the SIEM interface was operational.

## Engineering Lessons
### 1. Do not bypass security controls unnecessarily

The root-execution restriction was treated as a security requirement rather than an obstacle to be disabled.

### 2. Separate administration from service execution

A dedicated service account reduces the privileges available to the application process.

### 3. File ownership must match the service identity

Changing the service identity alone is insufficient if the application files remain inaccessible to that account.

The ```/opt/splunk``` directory was therefore reassigned to:

```text
splunk:splunk
```

### 4. Security architecture should be considered during deployment

Privilege separation was implemented as part of the deployment rather than added as an afterthought.

## Result

Splunk Enterprise was configured to operate under a dedicated unprivileged service account.

The resulting deployment established:

* Dedicated Splunk user
* Dedicated Splunk group
* Appropriate installation-directory ownership
* Non-root Splunk execution
* Separation between system administration and application execution

This completed the core Splunk service configuration.

## Next Stage

With Splunk operating under the dedicated service account, the next stage was to validate Splunk Web access and confirm that the SIEM platform was operational.

See:
```text
06-splunk-web.md
