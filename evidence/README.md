# Evidence

This directory contains sanitized visual and technical evidence supporting the implementation documented throughout the Security Information and Event Management (SIEM) laboratory.

The evidence is organized according to the major stages of the project:

```text
Deployment
    ↓
Telemetry
    ↓
Detection
    ↓
Troubleshooting
    ↓
Recovery
````

---

## Evidence Categories

### 01 — Deployment

Evidence supporting:

* Ubuntu Server installation
* Splunk Enterprise installation
* Splunk Web availability
* Network configuration
* Service status
* Infrastructure validation

---

### 02 — Telemetry

Evidence supporting:

* Windows endpoint configuration
* Sysmon installation
* Sysmon event generation
* Splunk Universal Forwarder
* Forwarding configuration
* TCP 9997 connectivity
* Successful telemetry ingestion
* Searchable Windows events

---

### 03 — Detection

Evidence supporting:

* Authentication monitoring
* Process execution monitoring
* Network activity monitoring
* Correlation searches
* Investigation workflows

---

### 04 — Recovery

Evidence supporting:

* Infrastructure failure
* Troubleshooting
* Recovery procedures
* Service restoration
* Post-recovery validation

---

# Evidence Standards

Evidence should demonstrate the actual implementation rather than simply reproduce documentation.

Each screenshot should answer at least one of the following questions:

> What was configured?

> What was tested?

> What happened?

> What was successfully validated?

---

# Sanitization

Before publishing evidence, sensitive information must be removed or obscured.

Do not publish:

* Passwords
* API keys
* Authentication tokens
* Private credentials
* Personal information
* Sensitive hostnames
* Unnecessary private network information

---

# Evidence Naming Convention

Evidence files should use descriptive names.

Example:

```text
01-splunk-web.png
02-sysmon-event.png
03-forwarder-status.png
04-telemetry-search.png
05-authentication-event.png
06-process-event.png
07-network-event.png
```

The filename should make it immediately clear what the evidence demonstrates.

---

# Evidence and Documentation

Evidence should be referenced from the relevant documentation.

For example:

```markdown
![Splunk telemetry search](../evidence/04-telemetry-search.png)
```

This allows a reader to move from the technical explanation directly to the supporting evidence.

---

# Portfolio Principle

The evidence directory exists to demonstrate that the documented architecture was actually implemented and validated.

The repository therefore follows:

```text
Claim
  ↓
Implementation
  ↓
Evidence
  ↓
Validation
```
This creates a more credible technical case study.
