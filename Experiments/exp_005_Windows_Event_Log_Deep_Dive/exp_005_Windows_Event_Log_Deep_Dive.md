# exp_005_Windows_Event_Log_Deep_Dive

## Experiment Title

Structured Analysis of Windows Security Event Logs

---

# 1. Objective

The objective of this experiment is to perform a detailed analysis of Windows Security Event Logs generated during controlled SMB authentication activity in Experiment 004 within the CyberLab environment.

This experiment aims to:

- Examine the structure and format of Windows Security events
- Analyse key fields within authentication-related log entries, specifically Event ID 4624 (successful logon) and Event ID 4625 (failed logon)
- Identify useful telemetry for incident investigation, including source IP, account name, logon type, and timestamps
- Develop familiarity with Windows Event Viewer for forensic analysis and event interpretation
- Understand how Windows log data can be interpreted by security monitoring systems (SIEM) for detection purposes
- Optionally, review related network activity events (e.g., Event ID 5156 / 5157) if firewall logging is enabled

This experiment simulates the investigative process a security analyst would follow when reviewing historical authentication and network activity logs during a potential security incident, leveraging data captured from prior controlled experiments.

---

# 2. Scope & Constraints

## Environment
- Internal isolated lab only (LabNetInt – 192.168.100.0/24)  
- All analysis performed on historical logs; no external network access or live attacks conducted

## Target System
- Host: WindowsMachine
- IP Address: 192.168.100.10
- OS: Windows 11 Pro
- Role: Generates authentication and network event logs for analysis

## Source System (Activity Generator)
- Host: KaliLinuxMachine
- IP Address: 192.168.100.20
- Role: Previously generated SMB authentication events in Experiment 004

## Constraints
- No additional attack or authentication activity performed during this experiment
- No system configuration changes permitted
- Analysis limited exclusively to logs generated in previous experiments (004 and earlier)
- Focus restricted to log interpretation, event structure, and forensic investigation
- No modification, deletion, or export of live logs beyond evidence collection for analysis

---

# 3. Pre-Experiment Baseline State

## Snapshot Taken

**WindowsMachine:**  
- WindowsMachine_Post_Exp004_v1.0 (contains all telemetry from Experiment 004)

**KaliLinuxMachine:**  
- KaliLinuxMachine_Baseline_v1.0

## Existing Telemetry Available

Logs and events generated from previous experiments are available for analysis:

- Enumeration scans (Experiment 001 & 003)  
- Enhanced logging configuration (Experiment 002)  
- Failed login attempts from controlled SMB authentication (Experiment 004)

## Environment Verification

- Windows Event Viewer operational and Security logs accessible  
- Internal network connectivity confirmed: WindowsMachine reachable from KaliLinuxMachine  
- Lab network remains isolated from external internet access  

## Purpose of Baseline

- Ensure all relevant telemetry from Experiment 004 is available for analysis  
- Establish a stable environment for detailed log inspection without performing new activity  
- Provide a controlled starting point for forensic and detection analysis

---

# 4. Methodology

This experiment focuses on structured analysis of existing Windows Security event logs generated during prior experiments, without introducing any new activity or configuration changes.

---

## Step 1 – Open Windows Event Viewer

On **WindowsMachine**:

1. Open Event Viewer.
2. Navigate to:

```
Event Viewer → Windows Logs → Security
```


**Purpose:**  
Access the primary log repository containing authentication and network-related events for analysis.

**Evidence:**  
Screenshot showing Event Viewer Security log view.

File location:

```
Experiments/exp_005_Windows_Event_Log_Deep_Dive/Screenshots/exp005_step01_eventviewer_security_log.png
```


---

## Step 2 – Filter Security Log for Failed Logon Events

Within Event Viewer:

1. Apply a filter for **Event ID 4625** to isolate failed authentication events generated during **Experiment 004**.

Example filter:

```
Event ID: 4625
```


**Purpose:**  
- Focus the analysis on failed logon events  
- Identify patterns and relevant forensic indicators

**Evidence:**  
Screenshot showing filtered event results.

File location:

```
Experiments/exp_005_Windows_Event_Log_Deep_Dive/Screenshots/exp005_step02_filter_4625.png
```

---

## Step 3 – Inspect Event Details

1. Select an Event ID 4625 entry and open its properties.
2. Review key fields presented in the log entry, such as:

- Subject  
- Account Name  
- Logon Type  
- Failure Reason  
- Source Network Address  
- Workstation Name  

**Purpose:**  
Understand the structure of Windows Security events and identify key forensic indicators for authentication failures.

**Evidence:**  
Screenshot showing detailed event information.

File location:

```
Experiments/exp_005_Windows_Event_Log_Deep_Dive/Screenshots/exp005_step03_event_details.png
```

---

## Step 4 – Review Network Connection Events

Within the Security log:

1. Identify events related to network activity.

Example Event IDs:

- **5156** – Windows Filtering Platform allowed connection  
- **5157** – Connection blocked by filtering platform  

**Purpose:**  
- Observe how Windows logs inbound network connections from previous enumeration scans  
- Correlate network-level activity with authentication events for investigative insight

**Evidence:**  
Screenshot showing relevant filtering platform events.

File location:

```
Experiments/exp_005_Windows_Event_Log_Deep_Dive/Screenshots/exp005_step04_network_events.png
```

---

# 5. Observed Technical Results

This section documents findings from analysis of Windows Security logs generated during previous experiments, particularly **Experiment 004 – Failed Logon Attempts**. No new activity was performed in this step; all results are derived from existing telemetry.

---

## Authentication Events Observed

- **Event ID 4625** – Failed logon attempts recorded for **WindowsUser** on **WindowsMachine**  
- Consistent source IP: **192.168.100.20 (KaliLinuxMachine)**  
- Logon Type: **3 (Network Logon)**  
- Failure Reason: **Unknown user name or bad password**

---

## Network Activity Events Observed (if present)

- **Event ID 5156** – Windows Filtering Platform allowed connection  
- **Event ID 5157** – Connection blocked by filtering platform  

These events may correlate with network activity observed during prior enumeration and authentication testing (Experiments 001–004).

---

## Key Event Attributes Identified

For investigative and SOC analysis purposes, the following attributes are relevant:

- **Source Network Address**  
- **Account Name**  
- **Logon Type**  
- **Failure Reason**  
- **Process Information**

These fields provide context for attribution, authentication analysis, and detection logic.

---

## Evidence Storage Locations

Screenshots:

```
Experiments/exp_005_Windows_Event_Log_Deep_Dive/Screenshots/
```

Exported Logs:

```
Experiments/exp_005_Windows_Event_Log_Deep_Dive/Logs/
```

---

# 6. Log & Detection Analysis

This section analyses the usefulness of Windows Security log data for security monitoring, investigation, and SOC operations. All findings are based on events generated during **Experiment 004 – Failed Logon Attempts**.

---

## Key Analysis Questions

- Does the event clearly identify the source system responsible for the activity?  
- Are authentication failures distinguishable from legitimate activity?  
- Is sufficient contextual information present for incident investigation?

---

## Example Event Analysis Structure

| Field               | Observed Value                    | Security Relevance                            |
|---------------------|-----------------------------------|-----------------------------------------------|
| Event ID            | 4625                              | Failed authentication attempt                 |
| Source              | 192.168.100.20 (KaliLinuxMachine) | Identifies originating host for attribution   |
| Account Name        | WindowsUser                       | Determines targeted account                   |
| Logon Type          | 3 (Network)                       | Confirms network-based logon via SMB          |
| Failure Reason      | Unknown user name or bad password | Indicates unsuccessful authentication attempt |
| Workstation Name    | KALI                              | Provides host-level context                   |
| Process Information | smbclient / SMB service           | Assists in tracing the method of access       |

This format can be applied to other relevant events, such as successful logons (Event ID 4624) or network connection events (Event IDs 5156/5157).

---

## Relevance to SOC Monitoring

- **SIEM platforms** can ingest Windows Security logs to detect suspicious authentication patterns.  
- **Repeated Event ID 4625 failures** from a single source IP highlight potential credential probing activity.  
- **Correlating network events with authentication entries** provides additional context for identifying suspicious behavior.  
- Detailed event fields, such as source IP, logon type, and failure reason, support attribution and incident investigation.  
- Patterns observed (e.g., consecutive failed logons) can be used to tune alert thresholds and detection rules.

---

## Summary

Windows Security logs provide **high-value telemetry** for detecting and investigating authentication activity. Even in a controlled lab environment, the structure and metadata of these events allow SOC analysts to:

- Identify the origin of failed logon attempts  
- Differentiate normal activity from suspicious sequences  
- Correlate authentication attempts with network-level activity  
- Establish detection rules and response strategies based on observed patterns

---

# 7. Detection & Mitigation Reflection

This section evaluates how the Windows Security logs observed in **Experiment 004** could be interpreted in a corporate SOC environment, and identifies potential detection and mitigation strategies.

---

## Investigation Sufficiency

- The recorded events provide clear evidence of authentication activity.  
- Failed logon attempts (Event ID 4625) are fully attributable to **KaliLinuxMachine (192.168.100.20)**.  
- Detailed metadata, including Account Name, Logon Type, and Workstation Name, supports forensic investigation.  
- Repeated failures create detectable patterns suitable for alerting.

---

## Potential Detection Opportunities

- Generate alerts for multiple **Event ID 4625** entries from a single source IP.  
- Correlate authentication events with network connection events (e.g., Event IDs 5156/5157) for context.  
- Identify abnormal patterns, such as repeated failures targeting the same account (WindowsUser).  
- Establish threshold-based detection rules for rapid or clustered authentication failures.  

---

## Mitigation Considerations

- Enforce account lockout policies after a defined number of failed attempts (e.g., 3–5 attempts within 5–10 minutes).  
- Centralise log collection and correlation using a SIEM for broader visibility across multiple hosts.  
- Continuously monitor authentication patterns to detect unusual activity early.  
- Maintain baseline authentication behaviour for comparison with abnormal events.  
- Restrict access to network services (e.g., SMB) to trusted hosts only.  
- Enforce strong password policies and consider multi-factor authentication for all network-accessible accounts.

---

## Summary

The observed event logs provide actionable intelligence for both detection and mitigation:

- **Detection:** High-frequency failed logons, consistent source IP, and repeated targeting of the same account are clear indicators of credential-probing behavior.  
- **Mitigation:** Account lockouts, centralized monitoring, and access restrictions reduce risk and improve responsiveness to potential attacks.

---

# 8. Key Takeaway

Windows Security Event Logs provide detailed, structured telemetry that enables comprehensive analysis of authentication activity and network interactions.

Key insights from this experiment:

- **Authentication Monitoring:** Failed logon attempts (Event ID 4625) and successful logons (Event ID 4624) offer clear evidence of account activity.  
- **Network Context:** Connection events (e.g., Event IDs 5156/5157) help correlate authentication attempts with network activity.  
- **Threat Detection:** Patterns of repeated failed logons, source IP consistency, and account targeting are strong indicators of credential-probing behavior.  
- **Investigation Readiness:** Detailed event metadata—including Account Name, Logon Type, Source Network Address, and Workstation Name—supports forensic analysis and SOC investigations.  
- **Operational Value:** Understanding log structure and event fields improves the accuracy of alerts, incident response, and detection tuning.

This experiment reinforces that structured log analysis is critical for detecting suspicious activity, attributing events to specific systems, and responding effectively to potential security incidents.

---

# 9. Lessons Learned

## Technical

- Windows Security logs provide structured and consistent event data suitable for forensic and investigative analysis.  
- Authentication-related events (Event IDs 4624, 4625) capture detailed contextual information, including Account Name, Logon Type, Source Network Address, and Workstation Name.  
- Event metadata fields are essential for tracing activity origins and correlating events across systems.  
- Network-related events (Event IDs 5156/5157) complement authentication telemetry by providing context for inbound/outbound connections.  

## Detection

- Failed authentication events are high-value indicators for detecting credential-probing or brute-force behavior.  
- Correlation of authentication and network events enhances investigative capability and reduces false positives.  
- Recognizing patterns such as repeated failed logons, consistent source IPs, and targeted accounts is critical for SOC alerting and monitoring.  

## Operational

- Effective log analysis requires familiarity with Windows event structures, filtering, and metadata interpretation.  
- Maintaining structured documentation of observed events ensures repeatability and supports investigation tracking.  
- Proper preparation of the environment and consistent evidence capture improves the reliability and utility of lab experiments.  

---

# 10. Post-Experiment Actions

## System Reversion

System reverted to baseline?  

- [ ] Yes  
- [x] No (No configuration changes were applied during this experiment)

## Snapshot Restoration

- Not required (baseline snapshots from previous experiments remain valid)

## Documentation Update

- [x] Yes – all screenshots and logs captured and stored in the repository

## Change Log

- Not required (no configuration changes performed)

---

# 11. Professional Reflection

## Technical Development

This experiment strengthened:

- Familiarity with Windows Event Viewer and navigation of Security logs
- Understanding of Windows Security log structure and field-level metadata
- Ability to interpret authentication and network event data for forensic analysis
- Confidence in identifying relevant telemetry for incident investigation

## Detection & SOC Perspective

From a defensive viewpoint, this experiment demonstrated:

- The value of detailed authentication and network telemetry for detection
- The importance of log analysis skills in monitoring and SOC operations
- How correlation of authentication and network events improves detection capability
- Contextual interpretation of event sequences for identifying suspicious behavior

## Adversary Perspective

- Authentication activity leaves detailed evidence in Windows Security logs
- Credential-based attacks or probing attempts generate patterns visible during log analysis
- Low-volume or repeated activity can be detected when logging and monitoring are enabled
- Understanding these patterns helps anticipate adversary behavior in a corporate environment

## Governance Discipline

- No configuration changes were applied
- Baseline environment preserved and maintained
- Evidence captured and stored in a structured and repeatable manner
- Documentation updated and integrated within the lab repository

## Follow-Up Recommendation

Recommended Action:

- [x] No Action Required
- [ ] Create Follow-Up Experiment
- [ ] Propose Security Hardening Change
- [ ] Simulated Escalation Report

Rationale:

- This experiment completes the initial Windows Security logging and detection analysis sequence within the CyberLab environment
- Provides a foundation for future investigations or deeper log analysis if required
- Confirms assumptions regarding telemetry availability, event interpretation, and SOC monitoring readiness

---

End of Report