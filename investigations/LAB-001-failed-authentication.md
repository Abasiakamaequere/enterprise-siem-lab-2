# LAB-001 — Failed Authentication Investigation

## 1. Detection

Windows failed authentication activity was identified through Windows Security Event ID 4625.

The event was observed within the isolated SIEM laboratory and was used as the starting point for investigation.

## 2. Evidence

The investigation is based on Windows authentication telemetry forwarded from the Windows endpoint into Splunk.

Relevant event:

- Windows Security Event ID: `4625`
- Activity type: Failed authentication
- SIEM: Splunk Enterprise
- Data source: Windows Security Event Log

## 3. Analysis

Event ID 4625 indicates that an authentication attempt failed.

The investigation focused on the available account and authentication metadata associated with the event.

The purpose was to determine whether the activity represented an isolated authentication failure or a pattern requiring additional investigation.

## 4. Context

This activity was generated and investigated within a controlled, isolated laboratory environment.

The investigation therefore demonstrates the SIEM workflow of:

**Telemetry → Detection → Investigation → Assessment**

rather than representing a real-world production incident.

## 5. Assessment

The observed event confirms that failed authentication activity was successfully collected and made available for investigation within Splunk.

Additional contextual analysis would be required before classifying the activity as malicious in a production environment.

## 6. Conclusion

LAB-001 demonstrates the use of Windows Security Event ID 4625 as an investigation starting point within the SIEM laboratory.

The case connects endpoint authentication telemetry with SIEM-based investigation and demonstrates how authentication events can be used for security monitoring and further analysis.
