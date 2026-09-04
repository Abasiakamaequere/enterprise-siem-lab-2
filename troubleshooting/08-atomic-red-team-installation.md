# Troubleshooting 08 — Atomic Red Team Installation

## Problem

To extend the laboratory from passive telemetry collection into controlled adversary emulation, Atomic Red Team was installed on the Windows endpoint to generate the T1033 (System Owner/User Discovery) test used in `detection/02-process-monitoring.md` and `investigations/LAB-002-controlled-reconnaissance.md`.

The installation did not complete in a single step. It required working through a sequence of distinct failures spanning a broken installation source, incomplete component installation, transport/TLS failures on a large download, certificate-revocation checking, and PowerShell session scoping.

## Symptoms

* The documented Red Canary installation script returned `404 Not Found`.
* After installing the core framework through the PowerShell Gallery, `Test-Path C:\AtomicRedTeam\atomics` returned `False` — the framework installed, but the test-definition content did not.
* The automated atomics-folder installer failed or hung repeatedly:

```text
  Install-AtomicsFolder : Installation of the AtomicsFolder Failed.
  Received an unexpected EOF or 0 bytes from the transport stream.
```
* A manual fallback download of the full repository archive produced a truncated file:

```text
  "End of Central Directory record could not be found."
```
* Switching to `curl.exe` for the same large download failed with a distinct transport error, stalling after a few hundred KB of a ~160 MB file:

```text
  curl: (56) schannel: server closed abruptly (missing close_notify)
```
* A later `curl` attempt against a single small file failed on certificate-revocation checking:

```text
  curl: (35) schannel: next InitializeSecurityContext failed: CRYPT_E_REVOCATION_OFFLINE
```
* After the required files were finally in place, `Import-Module` failed in a new PowerShell window:

```text
  File ...powershell-yaml.psm1 cannot be loaded because running scripts is disabled on this system.
```

## Diagnosis & Resolution

### 1. Installation source drift

The install script referenced by the standard Atomic Red Team documentation (`install-atomicredteam.ps1`, fetched from the repository's default branch) no longer resolved. This was treated as a documentation/source-stability problem rather than a local configuration error, since the URL itself returned `404`.

**Resolution:** installed the framework through the PowerShell Gallery instead:

```powershell
Install-Module -Name invoke-atomicredteam,powershell-yaml -Scope CurrentUser -Force
```

### 2. Framework vs. test-content separation

Atomic Red Team ships as two separate install artifacts: the `invoke-atomicredteam` execution framework, and the `atomics` folder containing the actual technique test definitions. Installing the module alone was not sufficient.

```powershell
Test-Path C:\AtomicRedTeam            # True  — framework present
Test-Path C:\AtomicRedTeam\atomics    # False — test content missing
```

**Resolution:** installed the atomics folder as a separate step:

```powershell
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicsfolder.ps1' -UseBasicParsing)
Install-AtomicsFolder
```

### 3. Transport/TLS failures on large downloads

Both the automated installer and a manual `Invoke-WebRequest` fallback failed to reliably pull down the ~160 MB atomics archive, terminating mid-transfer. This was diagnosed as a combination of outdated default TLS negotiation and unreliable throughput for large payloads inside the lab network — the same category of problem documented in `troubleshooting/01-network-connectivity.md` and `troubleshooting/02-slow-package-acquisition.md`, resurfacing here at the application layer.

**Attempted resolution:** forced TLS 1.2 explicitly:

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

This reduced but did not eliminate the transport instability on its own.

### 4. Scoped fetch instead of full-repository download

Rather than continuing to retry the full archive, the approach was changed to request only the single technique needed:

```powershell
New-Item -ItemType Directory -Path "C:\AtomicRedTeam\atomics\T1033" -Force

curl.exe -L "https://raw.githubusercontent.com/redcanaryco/atomic-red-team/master/atomics/T1033/T1033.yaml" `
  -o "C:\AtomicRedTeam\atomics\T1033\T1033.yaml"
```

This avoided the large-transfer failure mode entirely by reducing what needed to be downloaded, echoing the out-of-band staging approach in `troubleshooting/02-slow-package-acquisition.md`.

### 5. Certificate revocation checking

A subsequent `curl` request failed with `CRYPT_E_REVOCATION_OFFLINE` — Windows could reach the GitHub host itself but not the certificate-revocation-list (CRL) endpoint needed to validate the certificate, likely due to the lab's restricted network path.

**Resolution:** bypassed revocation checking for the request:

```powershell
curl.exe --ssl-no-revoke -L "https://raw.githubusercontent.com/redcanaryco/atomic-red-team/master/atomics/T1033/T1033.yaml" `
  -o "C:\AtomicRedTeam\atomics\T1033\T1033.yaml"
```

### 6. Execution policy is process-scoped

After the required files were in place, `Import-Module` failed with a script-execution-disabled error. `Set-ExecutionPolicy Bypass -Scope Process -Force` had been applied earlier, but in a different PowerShell session — the bypass does not persist across new windows.

**Resolution:** re-applied the bypass in the active session before importing:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force
Invoke-AtomicTest T1033 -TestNumbers 1
```

## Recovery Sequence

```text
Broken Install Script (404)
          ↓
PowerShell Gallery Install
          ↓
Framework Present / Atomics Missing
          ↓
Full-Archive Download Attempts (TLS / transport failures)
          ↓
Scoped Single-Technique Fetch
          ↓
Certificate Revocation Failure
          ↓
--ssl-no-revoke
          ↓
Execution Policy Re-Applied
          ↓
Invoke-AtomicTest T1033 Executed
          ↓
Verified in Splunk
```

## Outcome

`Invoke-AtomicTest T1033 -TestNumbers 1` executed successfully, generating a `whoami.exe` process-creation event on the Windows endpoint. The event was confirmed as searchable in Splunk:

```spl
index=* EventCode=1 (Image="*\\whoami.exe" OR CommandLine="*whoami*")
```

The returned event showed the expected parent-child relationship (`whoami.exe` spawned via the command interpreter under PowerShell), confirming the adversary-emulation activity traversed the full endpoint-to-SIEM pipeline. This event is the basis for `investigations/LAB-002-controlled-reconnaissance.md` and the controlled-detection validation described in `detection/02-process-monitoring.md`.

## Engineering Context

As with the earlier package-acquisition and telemetry-validation problems documented elsewhere in this repository, a tool reporting a clean install is not equivalent to the tool being usable. Framework installation, content installation, network transport, certificate validation, and session configuration each had to be validated as separate steps before the emulation test could run.

## Lessons Learned

* Installation instructions pinned to a repository's default branch can break independently of anything in the local environment; a documented install path should not be assumed stable.
* Some tools separate their "framework" from their "content" as distinct installable components — verify both, not just the first.
* Large downloads are a recurring failure class in this lab's network path; scoping a fetch to only the files actually needed is a more reliable workaround than retrying the same large transfer.
* TLS negotiation failures and certificate-revocation failures present similarly — both surface as connection errors — but require different fixes.
* `Set-ExecutionPolicy ... -Scope Process` is scoped to the PowerShell process it was run in and must be re-applied in each new session.
* Tool installation should be validated by its actual output, not by the absence of an install-time error — in this case, the Splunk search confirming the event was indexed.
