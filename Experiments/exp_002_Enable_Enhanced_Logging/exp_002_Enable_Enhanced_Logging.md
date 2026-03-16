# exp_002_Enable_Enhanced_Logging

## Experiment Title

Enable Enhanced Windows Security Logging for Detection Visibility

---

# 1. Objective

The objective of this experiment is to enable enhanced Windows logging on the Windows 11 lab machine in order to improve visibility of network activity and authentication events.

This experiment aims to:

- Enable additional Windows audit policies
- Increase telemetry available for security monitoring
- Prepare the environment for follow-up detection experiments
- Establish a controlled logging baseline for analysis

This experiment simulates the preparatory work performed by security teams when increasing monitoring capability within a host environment.

---

# 2. Scope & Constraints

Environment:  
Internal isolated lab only (LabNetInt – 192.168.100.0/24)

Target:

- Host: WindowsMachine
- IP Address: 192.168.100.10
- OS: Windows 11 Pro

Attacker:

- Host: KaliLinuxMachine
- IP Address: 192.168.100.20

Constraints:

- No exploitation activity
- No credential attacks
- No system hardening beyond logging configuration
- Configuration changes limited strictly to logging policies

---

# 3. Pre-Experiment Baseline State

Snapshots Taken:

Windows:  
WindowsMachine_Baseline_v1.0

Kali:  
KaliLinuxMachine_Baseline_v1.0

Baseline verification:

Windows Event Logging confirmed operational.

Existing logging level confirmed as default Windows configuration.

Internal connectivity verified:

```
ping 192.168.100.10 → Successful
```

---

# 4. Methodology

## Step 1 – Review Existing Audit Policy

On the Windows 11 host open an elevated PowerShell session and run:

```
auditpol /get /category:*
```

Purpose:

Identify current audit policy configuration and determine which categories are enabled or disabled.

Evidence:

Screenshot of audit policy output.

File location:

```
Experiments/exp_002_Enable_Enhanced_Logging/Screenshots/exp002_step01_auditpol_baseline.png
```

---

## Step 2 – Enable Advanced Audit Policies

Enable additional logging categories relevant to authentication and network activity.

Example commands:

```
auditpol /set /subcategory:"Filtering Platform Connection" /success:enable
auditpol /set /subcategory:"Filtering Platform Packet Drop" /success:enable
```

Purpose:

Increase the amount of security telemetry generated during network interactions and authentication attempts.

Evidence:

Screenshot showing successful audit policy changes.

File location:

```
Experiments/exp_002_Enable_Enhanced_Logging/Screenshots/exp002_step02_auditpol_enable.png
```

---

## Step 3 – Ensure Network is Private and Enable Windows Firewall Logging

Verify the network profile is set to Private:

Open an elevated PowerShell session and run:
```
Get-NetConnectionProfile
```

If the NetworkCategory is not Private, change it with:
```
Set-NetConnectionProfile -NetworkCategory Private
```

Confirm the change:
```
Get-NetConnectionProfile
```

Open Windows Defender Firewall with Advanced Security.

Navigate to:
```
- Open Start Menu and Search for Windows Defender Firewall and Advanced Security
- Open it and right click the Windows Defender Firewall with Advanced Security on Local Computer option in the left hand panel
- Go to Properties
- Navigate to Private Profile
- In the logging section at the bottom, click on 'Customize...'
- Alter Log dropped packets and Log successful connections accordingly
```

The above steps should enable logging for:

- Allowed connections
- Dropped packets

Firewall log location:
```
%systemroot%\system32\LogFiles\Firewall\pfirewall.log
```

### Verification of Logging

1. From a second machine on the network (or VM), generate traffic to the Windows host:
   - Ping the Windows host
   - Attempt a TCP connection to a blocked port (e.g., SMB on port 445)

```
ping 192.168.100.10 → Successful
```

```
nc -vz 192.168.100.10 445
```
Note: On Windows hosts, the equivalent PowerShell command is 
`Test-NetConnection -ComputerName 192.168.100.10 -Port 445`.

2. Open the firewall log in an elevated session:
```
notepad %systemroot%\system32\LogFiles\Firewall\pfirewall.log
```

3. Confirm log entries appear for both allowed and blocked packets corresponding to the test traffic:
   - ICMP ping should appear as `ALLOW ICMP`
   - Blocked TCP connection attempts should appear as `DROP TCP` with correct source/destination ports

Purpose:

Provide visibility into network connections interacting with the Windows host and verify that firewall logging is functional.

Evidence:

Screenshot showing:

- Network profile is Private
- Firewall logging enabled
- Sample log entries for allowed and dropped traffic

File location:
```
Experiments/exp_002_Enable_Enhanced_Logging/Screenshots/exp002_step03_firewall_logging.png
```

---

# 5. Observed Technical Results

Document:

- Audit policy changes successfully applied
- Firewall logging enabled
- Additional event categories now visible within Event Viewer
- Firewall log entries verified for both allowed and blocked traffic
- Test traffic results:
    - ICMP ping: allowed
    - TCP connection to port 445: blocked

Expected observable event types include:

- Event ID 4624 – Successful logon
- Event ID 4625 – Failed logon
- Event ID 4634 – Logoff
- Event ID 5156 – Windows Filtering Platform allowed connection
- Event ID 5157 – Connection blocked by filtering platform
- Firewall log entries corresponding to test traffic (ALLOW / DROP)

Evidence Storage Locations:

Screenshots:

```
Experiments/exp_002_Enable_Enhanced_Logging/Screenshots/...
```

Logs:

```
Experiments/exp_002_Enable_Enhanced_Logging/Logs/pfirewall_log.txt
```

---

# 6. Log & Detection Analysis

After enabling enhanced logging, review Windows Event Viewer and firewall logs.

## Event Viewer Analysis

Focus on:

```
Windows Logs → Security
```

Key observations:

- Confirm that the following events are generated during network interaction and authentication activity:
    - Event ID 4624 – Successful logon
    - Event ID 4625 – Failed logon
    - Event ID 4634 – Logoff
    - Event ID 5156 – Windows Filtering Platform allowed connection
    - Event ID 5157 – Connection blocked by filtering platform

- Verify that network connections attempted from external systems (e.g., ICMP ping, blocked TCP) appear in the security logs.

## Firewall Log Analysis

- Open firewall log in an elevated session:
```
notepad %systemroot%\system32\LogFiles\Firewall\pfirewall.log
```

- Confirm entries for:
   - Allowed packets (e.g., ICMP ping) appear as `ALLOW ICMP`
   - Dropped packets (e.g., blocked TCP connection to SMB port 445) appear as `DROP TCP`
- Check timestamps to correlate traffic with actions performed from test machines
- Optional: Logs can be exported or collected centrally for automated SOC monitoring to avoid repeated host-level inspection

## Analyst Considerations

- SOC analysts usually **access logs using elevated privileges** to ensure proper visibility of system-level logs.
- Logs are often **collected centrally**, e.g., via SIEM, rather than manually opening Notepad.
- Analysts may **automate firewall and Event Viewer log collection** with PowerShell scripts, log forwarders, or scheduled tasks for continuous monitoring.

---

# 7. Detection & Mitigation Reflection

If this configuration were applied within a corporate environment:

**Would security monitoring improve?**

Yes. Enhanced audit policies and firewall logging provide significantly greater telemetry for SOC analysis. The lab tests confirm that both allowed and blocked network activity is logged, providing actionable data.

**Detection opportunities created:**

- Identification of repeated failed authentication attempts (Event ID 4625)
- Visibility of successful logons and logoffs (Event IDs 4624, 4634)
- Detection of suspicious internal network scanning behaviour (e.g., repeated blocked TCP connection attempts to SMB port 445)
- Monitoring of firewall filtering events (Event IDs 5156 and 5157)
- Correlation of network and authentication activity, with timestamps, across systems

**Example lab observations:**

- ICMP ping attempts from KaliLinuxMachine were allowed and logged in the firewall log.
- TCP connection attempts to blocked SMB port 445 were dropped and logged.
- These events appear in both Windows Security logs and the firewall log, confirming that telemetry is generated for both allowed and denied actions.

**Potential security improvements:**

- Centralised log forwarding to a SIEM for continuous monitoring and correlation
- Alert rules for excessive failed logon events
- Alerting on repeated connection attempts to blocked ports, supporting early detection of internal reconnaissance
- Integration of firewall logs with authentication logs to detect anomalous lateral movement attempts

Purpose:

- Demonstrate how enhanced logging enables SOC teams to detect and investigate suspicious activity in near real-time.
- Show how test traffic can be used to validate that logging captures both allowed and blocked actions for detection scenarios.

---

# 8. Key Takeaway

Enabling enhanced Windows audit policies and firewall logging significantly increases the visibility of security-relevant events within the operating system. The lab exercises demonstrate that both allowed and blocked network traffic generates logs that can be analyzed for detection purposes.

**Key learnings from this experiment:**

- Default Windows configurations generate limited telemetry, which may miss early reconnaissance or unauthorized access attempts.
- Advanced logging for authentication (logon, logoff, account lockout) and network filtering (firewall allowed and dropped connections) provides actionable data for SOC monitoring.
- Attempted connections to blocked ports (e.g., SMB on port 445) are logged and can be correlated with allowed traffic to detect suspicious activity.
- Viewing firewall logs requires elevated permissions. SOC analysts typically use:
  - Administrative PowerShell or command-line sessions
  - Centralized log collection systems (e.g., SIEM) to avoid direct host access
  - Automation scripts to collect, parse, and alert on key events, rather than manually opening logs in Notepad.

**Implications for SOC operations:**

- Enhanced telemetry allows detection of suspicious behavior, including lateral movement, reconnaissance, and authentication anomalies.
- Integration of host-based logs with centralized monitoring improves incident response efficiency.
- Proper configuration and verification of logging policies are essential before relying on collected data for security analysis.

---

# 9. Lessons Learned

**Technical:**

- Windows audit policies are granular; by default many critical categories are not enabled.
- Firewall logging captures both allowed and blocked traffic, providing essential network-level visibility.
- Attempted connections to blocked ports (e.g., SMB 445) generate events that are immediately observable in logs.
- Viewing system logs directly often requires administrative privileges; PowerShell or other elevated tools are standard for SOC workflows.

**Detection:**

- Enhanced logging improves detection opportunities for authentication anomalies, network scanning, and policy violations.
- Network filtering events (allowed and dropped) can indicate reconnaissance, lateral movement attempts, or misconfigured services.
- Correlating blocked port attempts with allowed traffic can help identify abnormal patterns.

**Operational:**

- Logging changes must be documented carefully and verified before being relied upon.
- Baseline snapshots are essential to differentiate normal vs. abnormal activity.
- SOC analysts often automate log collection and parsing to avoid manual host-level inspection, ensuring consistent monitoring and timely alerts.
- Elevated access is required for log inspection, highlighting the need for proper permissions management and secure workflow practices.

---

# 10. Post-Experiment Actions

**System reverted to baseline?**

[ ] Yes  
[x] No (Logging configuration intentionally retained for future experiments)

**Snapshot restored:**  

Not required – baseline snapshots were captured prior to enabling enhanced logging.

**Verification of functionality:**  

- Ping from Kali to Windows host succeeded and generated log entries.  
- Attempted TCP connection to blocked port 445 was dropped and recorded in firewall logs.  
- Audit policies verified via `auditpol /get /category:*` to confirm enabled subcategories.  
- Firewall logs inspected in elevated session confirmed entries for allowed and dropped traffic.

**Documentation updated?**  

[x] Yes – screenshots and log evidence included.

**Change Log updated?**  

[x] Yes – configuration changes applied and logged for lab continuity.

**Operational Notes:**  

- Logs should be centralized or exported for automated SOC monitoring in production environments.  
- Elevated access is required to view firewall logs; SOC teams typically use automated collection scripts or SIEM integration to avoid manual host-level inspection.  
- Documenting changes ensures reproducibility for follow-up experiments and supports training or auditing requirements.

---

# 11. Professional Reflection

## Technical Development

This experiment strengthened:

- Understanding of Windows security logging and audit policy configuration.  
- Practical use of `auditpol` and PowerShell for enabling and verifying audit subcategories.  
- Awareness of Windows Filtering Platform (WFP) events and how firewall logs capture allowed vs. dropped traffic.  
- Insight into elevated access requirements for host-level log inspection and the importance of proper permissions.

## Detection & SOC Perspective

From a defensive perspective, this experiment demonstrated:

- The criticality of enabling appropriate audit policies to provide SOC analysts with actionable telemetry.  
- How host-based logging directly impacts detection capability for internal reconnaissance, authentication activity, and blocked network connections.  
- The role of automated collection or SIEM integration to centralize firewall and security logs, minimizing the need for manual elevated access.

## Adversary Perspective

Improved logging increases the likelihood that reconnaissance, unauthorized network probes, or authentication attempts will generate observable events.  

- Attackers are more likely to be detected when logs capture failed connections, blocked ports, and authentication events.  
- Internal scanning and unauthorized SMB access attempts are now visible in both audit and firewall logs.

## Governance Discipline

- Logging changes were documented and captured with screenshots.  
- Lab integrity was preserved, and changes were applied in a controlled environment.  
- Operational procedures for log verification and elevated access were followed and recorded.

## Follow-Up Recommendation

**Recommended Action:**  

- [ ] No Action Required  
- [x] Proceed to Follow-Up Experiment – Re-run Enumeration With Logging Enabled  
- [ ] Propose Security Hardening Change  
- [ ] Simulated Escalation Report

**Rationale:**  

With enhanced logging verified via ping and blocked TCP connection tests, the next experiment can safely re-run internal enumeration to observe the difference in telemetry generated by the Windows host.  

- SOC analysts can correlate allowed and blocked events with authentication activity to assess detection coverage.  
- Automation of log collection (via PowerShell, scripts, or SIEM agents) is recommended for operational realism and scalability.

---

End of Report