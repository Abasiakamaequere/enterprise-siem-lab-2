# 06 — Incident Response Playbook

## Objective

This document is the generalized, reusable response procedure for the detection categories developed in this laboratory.

It is deliberately separate from `05-investigation-case.md`. That document is a worked, illustrative walkthrough of a single hypothetical case, built to demonstrate an investigation methodology. This document is the opposite: it contains no scenario, no fictional timeline, and no placeholder evidence. It is the checklist an analyst would actually open when a real alert fires, organized by the telemetry source that generated it.

The procedural content below was originally developed inside `05-investigation-case.md` and has been extracted and generalized here so it can be used directly, rather than read as part of a narrative.

---

# How to Use This Playbook

Each section below covers one alert category and follows the same structure:

* **Trigger** — the telemetry or SPL pattern that starts the process
* **Initial Checks** — what to look at before deciding anything
* **Common Benign Explanations** — reasons this could be nothing, so they can be ruled in or out quickly instead of treated as an afterthought
* **Escalation Criteria** — what would justify moving from monitoring to active investigation
* **If Escalating** — the response actions to take next

None of the triggers below are, on their own, proof of malicious activity. Every category in this laboratory has already been documented with that caveat, and this playbook does not change that — it exists to make triage faster, not to make triage automatic.

---

# Playbook 1 — Failed or Unusual Authentication Activity

**Trigger:** Windows Security Event ID 4625 (failed logon), or a pattern of repeated 4625 events for a single account, as covered in `01-authentication-monitoring.md`.

## Initial Checks
* How many failures occurred, over what time window, against which account(s)?
* Did a successful logon (Event ID 4624) follow the failures on the same host or account?
* Is the source host or IP expected for this account?
* Is the logon type (interactive, network, remote) consistent with how this account is normally used?

## Common Benign Explanations
* Incorrect password entry by the legitimate user
* Expired or recently changed credentials not yet updated in a saved session or scheduled task
* A misconfigured service or scheduled task retrying with stale credentials
* Routine administrative activity

## Escalation Criteria
Escalate if any of the following are true:
* **5 or more failed logon attempts for the same account within a 10-minute window.** This is a standard starting heuristic, not a value tuned against this lab's own traffic — there isn't yet enough background authentication volume here to know what a normal false-positive rate looks like, so treat it as a default to adjust once real usage patterns are established.
```spl
  index=* EventCode=4625
  | bucket _time span=10m
  | stats count by user, _time
  | where count >= 5
```
* A successful logon (Event ID 4624) immediately follows failures meeting the threshold above, on the same account
* The source host, source IP, or logon type is inconsistent with the account's normal pattern
* The account is privileged or has access to sensitive systems
* The failures continue after the affected user confirms they were not attempting to log in

## If Escalating
1. Validate the affected account directly with its owner, where possible.
2. Pivot to process telemetry (`02-process-monitoring.md`) for activity on the same host in the period immediately following any successful logon.
3. Pivot to network telemetry (`03-network-monitoring.md`) for connections from the same host in the same window.
4. Preserve the relevant raw events and any supporting screenshots.
5. Record a disposition (see Documentation Standard below) even if the conclusion is benign.

---

# Playbook 2 — Suspicious or Unexpected Process Execution

**Trigger:** Sysmon Event ID 1 (process creation) matching an unusual executable, path, command line, or parent-child relationship, as covered in `02-process-monitoring.md`.

## Initial Checks
* What process executed, from what path, and under what user context?
* What was the parent process, and is that parent-child relationship expected?
* What command-line arguments were passed?
* Did the process execute from a user-writable or temporary directory rather than a standard application path?

## Common Benign Explanations
* Legitimate software installation or update
* Scheduled system maintenance
* Administrative troubleshooting
* A known internal tool or script with an unfamiliar name

## Escalation Criteria
Escalate if any of the following are true:
* The parent-child relationship is inconsistent with normal application behavior (for example, an office application or browser spawning a command interpreter)
* The command line exceeds roughly 300 characters, or contains Base64-style encoded blocks.** Length alone doesn't prove obfuscation, but it's a workable trigger for a closer read rather than a vague impression of "looks long":
```spl
  index=* EventCode=1
  | eval cmdlen=len(CommandLine)
  | where cmdlen > 300
```
* The executable runs from a temporary or user-writable directory with no clear administrative justification
* The process is followed by unexpected network activity (see Playbook 3)

## If Escalating
1. Identify the user account associated with the execution and confirm whether that activity was expected.
2. Trace the parent process chain as far back as available telemetry allows.
3. Cross-reference authentication telemetry (Playbook 1) for the same host and time window.
4. Cross-reference network telemetry (Playbook 3) for connections initiated by the same process.
5. Preserve the relevant raw events and any supporting screenshots.
6. Record a disposition even if the conclusion is benign.

---

# Playbook 3 — Suspicious Network Connection

**Trigger:** Sysmon Event ID 3 (network connection) involving an unexpected destination, port, or originating process, as covered in `03-network-monitoring.md`.

## Initial Checks
* Which process initiated the connection?
* Which destination IP and port were involved, and is that destination known or expected?
* Is the destination port consistent with the service it claims to be (for example, unexpected traffic on a non-standard port)?
* Did the connection immediately follow a suspicious process execution or authentication event?

## Common Benign Explanations
* Normal operating-system or background-service traffic
* Software updates or licensing checks
* Security tooling or monitoring agents
* DNS resolution and other routine connectivity

## Escalation Criteria
Escalate if any of the following are true:
* The initiating process is itself already flagged under Playbook 2
* The destination is external and not part of any known or expected service
* The port or protocol is inconsistent with the process making the connection
* **3 or more connections to the same external destination within a 60-minute window.** Same caveat as the authentication threshold: a reasonable starting point, not a number derived from this lab's own traffic yet.
```spl
  index=* EventCode=3
  | bucket _time span=1h
  | stats count by DestinationIp, _time
  | where count >= 3
```

## If Escalating
1. Identify the process and user account responsible for the connection.
2. Cross-reference process telemetry (Playbook 2) for the process's own origin and command line.
3. Cross-reference authentication telemetry (Playbook 1) for the same host and time window.
4. Preserve the relevant raw events and any supporting screenshots.
5. Record a disposition even if the conclusion is benign.

---

# General Escalation Decision Tree

This decision tree applies across all three playbooks above once an event has been reviewed against its Initial Checks.

```text
                 Event Identified
                       │
                       ▼
               Is it unusual?
                  /       \
                No         Yes
                │           │
                ▼           ▼
             Monitor     Investigate
                            │
                            ▼
                     Identify User
                            │
                            ▼
                     Identify Host
                            │
                            ▼
              Cross-Reference Other Playbooks
                            │
                            ▼
                     Assess Evidence
                       /         \
                  Expected      Suspicious
                     │              │
                     ▼              ▼
                  Close       Escalate / Contain
```

Containment is the last step, not the default response. For an isolated, low-severity event with no corroborating signal from the other playbooks, containment is typically not warranted — escalation to closer monitoring is. Containment becomes appropriate once multiple signals correlate against the same host, account, or timeframe.

---

# Documentation Standard

Every case worked through this playbook — regardless of outcome — should be recorded using the same fields, so that closed cases remain reviewable later:

```text
Incident / Case ID
Detection Source (which playbook, which SPL search)
Date / Time
Affected Host
Affected User
Observed Activity
Evidence (file paths or screenshot references)
Correlation (which other playbooks were checked, and what they showed)
Analyst Assessment
Actions Taken
Final Disposition
```

A disposition of "expected activity, no further action" is a valid and complete outcome. The goal of this documentation is a closed, reviewable record — not a bias toward finding something wrong.

---

# Relationship to Other Documents

```text
Detection logic and SPL queries → 01-authentication-monitoring.md, 02-process-monitoring.md, 03-network-monitoring.md, 04-correlation-detection.md
Worked example / methodology walkthrough → 05-investigation-case.md
Real applied cases → ../investigations/LAB-001-failed-authentication.md, ../investigations/LAB-002-controlled-reconnaissance.md
Reusable response procedure → this document
```

This playbook does not replace the earlier detection documents or the real investigation case files. It is the procedural layer that sits between them: written once, and referenced whenever a new case needs to move from a raw alert to a documented disposition.
