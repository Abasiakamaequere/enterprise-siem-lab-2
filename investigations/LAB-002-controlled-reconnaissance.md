# LAB-002 — Controlled Reconnaissance Investigation

## 1. Detection

Controlled reconnaissance-style activity was observed within the isolated Windows endpoint laboratory.

The activity was investigated using the endpoint telemetry available through the Splunk SIEM.

## 2. Evidence

The investigation is based on endpoint telemetry collected through:

- Windows Event Logs
- Sysmon
- Splunk Universal Forwarder
- Splunk Enterprise

The telemetry pipeline allowed endpoint activity to be searched centrally within Splunk.

## 3. Analysis

The observed activity was examined as potential reconnaissance behavior.

The investigation focused on identifying observable endpoint activity and determining whether the available telemetry provided sufficient context to understand the behavior.

The analysis followed:

**Endpoint Activity → Telemetry → Splunk Search → Security Assessment**

## 4. Context

The activity occurred within the controlled and isolated laboratory environment.

The reconnaissance behavior was intentionally investigated as part of the security-monitoring exercise.

It should therefore not be interpreted as evidence of a real-world compromise.

## 5. Assessment

The investigation demonstrates that endpoint telemetry can be used to identify and analyze reconnaissance-style activity.

However, identifying reconnaissance behavior alone does not establish malicious intent.

Additional context, event correlation, process information, network telemetry, and timeline analysis would be required for a production investigation.

## 6. Conclusion

LAB-002 demonstrates the investigation of controlled reconnaissance-style activity using centralized endpoint telemetry.

The case demonstrates the progression from observable endpoint behavior to SIEM-based analysis and security assessment.

This investigation also highlights the importance of combining multiple telemetry sources when evaluating potentially suspicious activity.
