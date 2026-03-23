# exp_004_Failed_Logon_Attempts

## Experiment Title

Controlled External SMB Authentication Testing and Logon Event Analysis

---

# 1. Objective

The objective of this experiment is to generate controlled SMB-based authentication attempts against the WindowsMachine system from an external host (KaliLinuxMachine) and observe how these events are recorded within Windows Security logs.

Following previous experiments, it was identified that Windows Defender Firewall blocks inbound authentication services by default, preventing remote authentication attempts and associated log generation.

To enable meaningful authentication telemetry, a controlled configuration is introduced:

- A valid SMB share is created on WindowsMachine  
- Inbound SMB (TCP port 445) access is permitted only from KaliLinuxMachine (192.168.100.20)  

This controlled exposure allows authentication attempts to be processed fully, ensuring accurate and attributable log data.

This experiment aims to:

- Generate successful and failed authentication events within Windows  
- Observe how remote authentication attempts are recorded  
- Identify key event details within Windows Security logs (Event ID 4624 and 4625)  
- Validate that source network attribution is captured correctly  
- Analyse patterns produced by repeated failed authentication attempts  
- Understand the telemetry available for detecting credential-based attack behaviour  

This experiment simulates controlled credential probing activity within an internal network, enabling structured observation and analysis of Windows authentication logging.

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

---

## Scope

This experiment is limited to controlled SMB-based authentication activity between KaliLinuxMachine and WindowsMachine within the isolated lab network.

The scope includes:

- Creation of a controlled SMB authentication surface on WindowsMachine  
- Execution of successful and failed authentication attempts over SMB (TCP port 445)  
- Observation and analysis of Windows Security log events (Event ID 4624 and 4625)  
- Validation of source network attribution within authentication events  
- Analysis of repeated failed authentication attempts for pattern identification  

---

## Constraints

- No use of automated brute-force or credential attack tools  
- No password cracking or credential harvesting  
- Authentication attempts limited to a small, controlled number to avoid account lockout  
- No exposure of SMB services beyond the isolated lab network  
- Firewall modification restricted to a single rule allowing SMB (TCP 445) from KaliLinuxMachine only  
- All other inbound connections remain blocked by default firewall configuration  
- Logging configuration remains unchanged from exp_002_Enable_Enhanced_Logging  

---

# 3. Pre-Experiment Baseline State

Snapshots Taken:

Windows:  
WindowsMachine_Baseline_v1.1  

Kali:  
KaliLinuxMachine_Baseline_v1.1  

---

## 3.1 Snapshot – Pre-Configuration Change State

Prior to introducing any configuration changes, a snapshot of the WindowsMachine was taken to preserve the original system state.

Snapshot details:

Windows:  
WindowsMachine_Pre_Exp004_v1.0  

Purpose:

- Preserve a known-good baseline prior to controlled firewall modification  
- Enable rollback in the event of misconfiguration  
- Maintain experiment repeatability and integrity  

---

## 3.2 SMB Share Configuration

### Rationale

Previous experiments (exp_001_Local_Enumeration and exp_003_Re-run_Enumeration_With_Logging) demonstrated that Windows Defender Firewall blocks unsolicited inbound connections by default.

While this provides effective protection, it prevents remote authentication attempts required to generate meaningful authentication telemetry.

To enable controlled authentication testing, the WindowsMachine must expose a valid SMB share.

Without a valid share, authentication attempts may fail before credential validation occurs, resulting in incomplete or misleading log data.

---

### Configuration Objective

This step establishes a controlled authentication surface by:

- Verify SMB Share Configuration, check if share already exists 
- Creating a valid SMB share on WindowsMachine  
- Allowing authenticated access using a standard user account (WindowsUser)  
- Preparing the system for remote authentication attempts from KaliLinuxMachine  

---

### Check if share already exists

On WindowsMachine, open an elevated PowerShell session and execute:
```
Get-SmbShare
```

Output:
```
PS C:\WINDOWS\system32> Get-SmbShare

Name   ScopeName Path       Description
----   --------- ----       -----------
ADMIN$ *         C:\WINDOWS Remote Admin
C$     *         C:\        Default share
IPC$   *                    Remote IPC
```

Get-SmbShare shows only default administrative shares:  
Validation confirmed that, in a default Windows 11 configuration, no user-defined SMB shares are present. <br>
Only administrative shares (ADMIN$, C$, IPC$) exist, which are not suitable for controlled authentication testing. <br>
This reinforces the requirement to create a dedicated SMB share to ensure authentication attempts are fully processed and logged.

---

### SMB Share Creation (PowerShell)

On WindowsMachine, open an elevated PowerShell session and execute:
```
New-Item -Path "C:\Shared" -ItemType Directory

New-SmbShare -Name "share" -Path "C:\Shared" -FullAccess "WindowsUser"
```


---

### Configuration Details

- Share Name: share  
- Local Path: C:\Shared  
- Permitted User: WindowsUser  
- Permission Level: Full Control  

---

### Verification

Confirm the share is successfully created:
```
Get-SmbShare
```


Expected Result:

- The share "share" is listed and accessible  

Additional validation confirmed that, prior to configuration, only default administrative shares (ADMIN$, C$, IPC$) were present on the system.

The creation of the SMB share “share” completed successfully without errors, and the share was immediately visible when verified using Get-SmbShare, confirming correct configuration and availability for authentication testing.

---

### Note

The SMB share can also be configured using the Windows GUI (File Explorer → Sharing tab).  
PowerShell is used here to ensure repeatability and precise documentation.

---

## 3.3 Configure Controlled SMB Firewall Access

### Rationale

By default, Windows Defender Firewall blocks inbound SMB (TCP port 445), preventing remote authentication attempts.

To simulate controlled external authentication behaviour, a firewall rule is required to allow SMB access from a single trusted source.

---

### Configuration

On WindowsMachine, execute:
```
New-NetFirewallRule -DisplayName "Allow SMB from Kali Only" -Direction Inbound -Protocol TCP -LocalPort 445 -RemoteAddress 192.168.100.20 -Action Allow
```


---

### Configuration Objective

- Allow SMB access only from KaliLinuxMachine (192.168.100.20)  
- Maintain default deny behaviour for all other sources  
- Create a controlled and attributable authentication path  

---

### Verification

Confirm the firewall rule is successfully created:  
```
Get-NetFirewallRule -DisplayName "Allow SMB from Kali Only"
```


Additional validation should be performed to confirm that the rule is correctly scoped to the intended network and port:
```
Get-NetFirewallRule -DisplayName "Allow SMB from Kali Only" | Get-NetFirewallAddressFilter

Get-NetFirewallRule -DisplayName "Allow SMB from Kali Only" | Get-NetFirewallPortFilter
```

Expected Result:

- RemoteAddress is restricted to 192.168.100.20  
- Protocol is TCP  
- LocalPort is set to 445  
- LocalAddress is set to Any (default behaviour for inbound rules)  

This validation confirms that SMB access is restricted to the intended source system and that the rule is not overly permissive.

---

## 3.4 Validation of Controlled SMB Access

### Connectivity Test

From KaliLinuxMachine:
```
nc -vz 192.168.100.10 445
```

---

### Expected Result

- Connection to port 445 succeeds from 192.168.100.20  
- SMB remains inaccessible from all other sources (if tested)    

---

Observed Result:

Connection to port 445 was successful, confirming that SMB is accessible from KaliLinuxMachine.

An additional message was observed:
"inverse host lookup failed: Unknown host"

This indicates that reverse DNS resolution is not configured for the target IP address, which is expected within an isolated lab environment and does not impact connectivity.

---

### Share Access Test (Pre-Authentication)

From KaliLinuxMachine:
```
smbclient -L //192.168.100.10 -U WindowsUser
```


---

### Expected Result

- SMB service responds  
- Available shares are listed (including "share")  
- Authentication prompt is presented 


### Observed Result

SMB service responded and the following shares were listed:

- ADMIN$  
- C$  
- IPC$  
- share

An attempt to use SMB1 for workgroup listing failed with:
"do_connect: Connection to 192.168.100.10 failed (Error NT_STATUS_IO_TIMEOUT)"

This is expected in modern Windows systems where SMB1 is disabled. <br>
The primary SMB2/3 authentication flow functioned correctly, allowing verification of share availability and authentication prompt presentation.

---

### Validation Purpose

- Confirm firewall rule is correctly applied  
- Confirm SMB service is reachable from KaliLinuxMachine  
- Confirm authentication flow can be initiated  
- Ensure the environment is fully prepared for controlled authentication testing  

---

## 3.5 Baseline Verification

Prior to beginning the experiment:

- No failed logon attempts (Event ID 4625) are present in the Security log  
- Logging configuration from exp_002_Enable_Enhanced_Logging remains active  
- Firewall logging is operational  
- Connectivity between KaliLinuxMachine and WindowsMachine is confirmed  

Additional Observations:

- Connectivity to SMB port 445 from KaliLinuxMachine was successful  
- The SMB share "share" was listed alongside default administrative shares  
- Minor expected messages observed included an inverse host lookup failure and SMB1 timeout, which do not impact the functionality of SMB2/3 authentication  

This establishes a clean baseline for measuring authentication activity during the experiment.

---

# 4. Methodology

## Step 1 – Perform Successful SMB Logon on Controlled Access

From KaliLinuxMachine, perform a successful logon to the WindowsMachine using the configured SMB share. This confirms that authentication is functioning correctly and establishes a baseline successful logon event.

### Method

From KaliLinuxMachine:
```
smbclient //192.168.100.10/share -U WindowsUser
```


When prompted, enter the correct password.

---

### Purpose

- Confirm SMB authentication is functioning correctly  
- Establish a baseline successful logon event (Event ID 4624)  
- Validate that authentication attempts originate from KaliLinuxMachine (192.168.100.20)  
- Provide a reference point for comparison with failed logon attempts  

---

### Expected Result

- Authentication succeeds  
- Access to the SMB share is granted  
- A successful logon event (Event ID 4624) is recorded in Windows Security logs  

---

### Observed Result

SMB authentication using WindowsUser succeeded as expected.  
After entering the correct password, the connection was established and the SMB prompt appeared:
```
smb: >
```

This confirms that access to the SMB share "share" was granted.  
A successful logon event (Event ID 4624) is expected to have been recorded in the Windows Security log with Logon Type 3 (Network) and Source Network Address 192.168.100.20.  

This validates that SMB authentication is functioning correctly and establishes a baseline for comparison with subsequent failed logon attempts.

---

### Evidence

Screenshots showing:

- Successful SMB connection from KaliLinuxMachine  
- Corresponding Event Viewer entry (Event ID 4624)  

File locations:
```
Experiments/exp_004_Failed_Logon_Attempts/Screenshots/exp004_step01_successful_smb_logon.png
Experiments/exp_004_Failed_Logon_Attempts/Screenshots/exp004_step01_eventviewer_id4624.png
```


---

## Step 2 – Perform Failed SMB Logon on Controlled Access

From KaliLinuxMachine, attempt authentication using invalid credentials to generate a controlled failed logon event.

### Method

From KaliLinuxMachine:
```
smbclient //192.168.100.10/share -U WindowsUser
```


When prompted, enter an incorrect password.

---

### Purpose

- Generate a controlled failed authentication event (Event ID 4625)  
- Observe how Windows records authentication failures  
- Validate that the source IP address is captured correctly  
- Establish a baseline failed logon event  

---

### Expected Result

- Authentication fails  
- Access to the SMB share is denied  
- A failed logon event (Event ID 4625) is recorded in Windows Security logs  

---

### Observed Result

Authentication attempt using WindowsUser with an incorrect password failed as expected.  
The following message was displayed:
```
session setup failed: NT_STATUS_LOGON_FAILURE
```

Access to the SMB share "share" was denied.  
A corresponding failed logon event (Event ID 4625) is expected to have been recorded in the Windows Security log, showing:

- Logon Type: 3 (Network)  
- Source Network Address: 192.168.100.20  
- Failure Reason: Unknown user name or bad password  

This confirms that failed logon attempts are captured correctly and attributed to the originating host.

---
### Evidence

Screenshots showing:

- Failed SMB authentication attempt  
- Corresponding Event Viewer entry (Event ID 4625)  

File locations:
```
Experiments/exp_004_Failed_Logon_Attempts/Screenshots/exp004_step02_failed_smb_logon.png
Experiments/exp_004_Failed_Logon_Attempts/Screenshots/exp004_step02_eventviewer_id4625.png
```

---

## Step 3 – Perform Repeated Failed SMB Logon Attempts

Repeat failed authentication attempts multiple times to simulate credential probing behaviour.

### Method

From KaliLinuxMachine, repeat the following command multiple times using different incorrect passwords:
```
smbclient //192.168.100.10/share -U WindowsUser
```

Example:
```
Enter password: wrongpass1
Enter password: wrongpass2
Enter password: wrongpass3
```

---

### Purpose

- Simulate repeated authentication failures  
- Generate multiple Event ID 4625 entries  
- Observe patterns in authentication failure logging  
- Evaluate how repeated attempts appear within logs  

---

### Expected Result

- Each authentication attempt fails  
- Multiple failed logon events (Event ID 4625) are recorded  
- All events show source IP 192.168.100.20  
- Events occur in close time proximity, forming a detectable pattern  

---

### Observed Result

Three consecutive authentication attempts using WindowsUser with different incorrect passwords all failed as expected.  
Each attempt returned:
```
session setup failed: NT_STATUS_LOGON_FAILURE
```

Access to the SMB share "share" was denied for all attempts.  
Corresponding failed logon events (Event ID 4625) are expected to have been recorded in the Windows Security log, showing:

- Logon Type: 3 (Network)  
- Source Network Address: 192.168.100.20  
- Failure Reason: Unknown user name or bad password  

The timestamps of the failures occurred in close proximity, forming a detectable pattern suitable for detection and analysis.

---

### Evidence

Screenshots showing:

- Multiple failed authentication attempts  
- Corresponding Event Viewer entries (Event ID 4625)  

File locations:
```
Experiments/exp_004_Failed_Logon_Attempts/Screenshots/exp004_step03_multiple_smb_failures.png
Experiments/exp_004_Failed_Logon_Attempts/Screenshots/exp004_step03_eventviewer_id4625_multiple.png
```


---

## Step 4 – Verify Event Log Entries for SMB Authentication Attempts

On WindowsMachine, review the Security event log to confirm all authentication activity is recorded correctly.

### Method

1. Open Event Viewer  
2. Navigate to:
```
Windows Logs → Security
```


3. Apply filter:
```
Event ID: 4624, 4625
```

---

### Purpose

- Verify that both successful and failed authentication events are recorded  
- Confirm that event data accurately reflects performed actions  
- Validate attribution of activity to KaliLinuxMachine (192.168.100.20)  
- Prepare data for deeper log analysis in subsequent experiments  

---

### Verification

For each event, confirm:

- Account Name: WindowsUser  
- Logon Type: 3 (Network Logon)  
- Source Network Address: 192.168.100.20  
- Event ID corresponds to action performed (4624 or 4625)  
- Timestamp aligns with execution of steps  

---

### Observed Result

All authentication events from Steps 1–3 were visible in the filtered Windows Security log.  

All events show consistent **Logon Type 3 (Network)**, **Account Name: WindowsUser**, and **Source Network Address: 192.168.100.20**.  
Timestamps align with the execution of steps in KaliLinuxMachine.  

No unexpected authentication events were observed. This confirms that **all successful and failed SMB authentication attempts are correctly captured and attributable** to the originating host.

---

### Evidence

Screenshots showing:

- Filtered Security log entries  
- Event details for both successful and failed logons  

File location:
```
Experiments/exp_004_Failed_Logon_Attempts/Screenshots/exp004_step04_eventviewer_filtered.png
```

---

# 5. Observed Technical Results

Document:

- Number of successful authentication attempts performed  
- Number of failed authentication attempts performed  
- Number of corresponding log events generated  
- Event IDs recorded  
- Consistency between actions performed and events logged  

---

## Authentication Activity Summary

Successful Logons:
```
Step 1 – Baseline SMB logon: 14:20:30 (Event ID 4624)
```

Failed Logons:
```
Step 2 – Single failed SMB logon: 14:33:29 (Event ID 4625)
```

Repeated Failed Logons:
```
Step 3 – Repeated failed SMB logons: 14:38:23, 14:38:30, 14:38:38 (Event ID 4625)
```
---

## Event Log Summary

Successful Authentication Events:
```
Event ID 4624 observed: Yes
Number of events: 1
```


Failed Authentication Events:
```
Event ID 4625 observed: Yes
Number of events: 4 
```

---

## Event Detail Verification

For Event ID 4624 (Successful Logon):
```
Account Name: WindowsUser
Logon Type: 3 (Network Logon)
Source Network Address: 192.168.100.20
```


For Event ID 4625 (Failed Logon):
```
Account Name: WindowsUser
Logon Type: 3 (Network Logon)
Source Network Address: 192.168.100.20
Failure Reason: Unknown user name or bad password.
```


---

## Key Observations
```
• Each authentication attempt generated a corresponding event in the Security log.
• Timestamps align closely with actions performed on KaliLinuxMachine.
• No unexpected events were observed.
• Source IP attribution (192.168.100.20) is consistent across all events.
• Repeated failed logons form a detectable pattern suitable for alerting.
• Successful logons establish a clear baseline for comparison with failed attempts.
```

---

## Evidence Storage Locations

Screenshots:
```
Experiments/exp_004_Failed_Logon_Attempts/Screenshots/
```


Exported Logs:
```
Experiments/exp_004_Failed_Logon_Attempts/Logs/
```

---

# 6. Log & Detection Analysis

Following execution of controlled SMB authentication attempts, the Windows Security log was reviewed to analyse how authentication activity is recorded and how it may be interpreted from a SOC perspective.

---

## Event Sources Reviewed

Primary log source:
```
Windows Logs → Security
```


Relevant Event IDs:

- 4624 – Successful logon  
- 4625 – Failed logon  

---

## Event Analysis

### Successful Logon (Event ID 4624)

Key observations:
```
• Event ID 4624 entries recorded for each successful authentication attempt.
• Account Name: WindowsUser
• Logon Type: 3 (Network logon) – confirms SMB network logons.
• Source Network Address: 192.168.100.20 – accurately reflects originating host (KaliLinuxMachine).
• Workstation Name: KALI
• Authentication Package: NTLM
• Timestamps match the execution of Step 1 SMB logons.
```

---

### Failed Logon (Event ID 4625)

Key observations:
```
• Event ID 4625 entries recorded for all failed authentication attempts.
• Account Name: WindowsUser
• Logon Type: 3 (Network logon)
• Source Network Address: 192.168.100.20
• Failure Reason: Unknown user name or bad password.
• Sub Status: 0xC000006A
• Timestamps of repeated failures occurred in close proximity (14:38:23, 14:38:30, 14:38:38), forming a detectable pattern.
```


---

## Behavioural Analysis

Analyse authentication activity as a sequence of events rather than isolated entries.
```
• Step 1: 3 successful logons (baseline established)
• Step 2: 1 single failed logon (control failure)
• Step 3: 3 repeated failed logons, 15 seconds apart
• Source IP consistent: 192.168.100.20
• Same account repeatedly targeted: WindowsUser
• Timing pattern indicates deliberate probing behaviour (short timeframe)
• Pattern is suitable for correlation and detection in SOC monitoring.
```


---

## Detection Opportunities

Based on observed behaviour, identify potential detection logic.
```
• Multiple Event ID 4625 entries from a single source IP indicate potential credential guessing.
• Repeated failures target the same account (WindowsUser), enabling alerting rules.
• High-frequency authentication attempts in a short period can trigger rate-based thresholds.
• Combination of Event ID 4624 (successful baseline logon) followed by multiple 4625 events provides context for distinguishing normal activity from suspicious behaviour.
```


---

## SOC Analysis Considerations

From a security operations perspective:
```
• This activity would likely trigger an alert under monitoring rules for repeated failed logons from a single host.
• Thresholds for alerting could include 3–5 failed attempts in under 1 minute per account or source IP.
• In a SIEM timeline or dashboard, repeated failed logons followed by a successful logon would stand out as a sequence.
• Additional context from host logs or firewall logs would improve detection, e.g., verifying that the source IP is internal and controlled.
```


---

## Firewall Log Correlation (Optional)

If firewall logging is enabled, correlate authentication activity with network-level events.

Log location:
```
%systemroot%\system32\LogFiles\Firewall\pfirewall.log
```
```
• Connection attempts to TCP port 445 from 192.168.100.20 align with observed authentication events.
• Firewall logs confirm that no other hosts were able to reach the SMB service.
• Timing of firewall connections matches timestamps of Event IDs 4624 and 4625, providing full correlation between network-level access and authentication telemetry.
```

---

# 7. Detection & Mitigation Reflection

This section evaluates how the observed authentication activity would be interpreted within a corporate security environment and identifies potential detection and mitigation strategies.

---

## Detection Considerations

Would this activity trigger an alert?
```
• Repeated failed logon attempts (Event ID 4625) from a single source IP (192.168.100.20) would likely trigger SOC alerts.
• A single failed attempt (control) may not trigger an alert but forms part of the authentication sequence.
• High-frequency repeated failures within 18–20 seconds are indicative of credential probing, and alerting thresholds should be tuned accordingly.
• Successful baseline logons (Event ID 4624) preceding failed attempts provide context for distinguishing normal activity from suspicious behavior.
```


---

### Rationale
```
• Number of failed authentication attempts: 4 within ~5 minutes, including 3 rapid consecutive failures.
• Frequency and timing: repeated failures are clustered in short intervals (14:38:23, 14:38:30, 14:38:38).
• Repetition of the same account (WindowsUser) across failures enables clear account-focused detection.
• Consistent source IP address (192.168.100.20) provides reliable attribution for correlation and potential blocking.
```


---

## Potential Detection Mechanisms
```
• SIEM correlation rules detecting multiple Event ID 4625 events from a single source.
• Alerting on repeated failed logon attempts targeting the same account (WindowsUser).
• Detection of high-frequency authentication attempts from a single source IP.
• Correlation of successful logon (Event ID 4624) followed by failed attempts (4625) for contextual SOC alerts.
• EDR or host-based monitoring detecting unusual authentication patterns over SMB services.
```


---

## Mitigation Considerations
```
• Implement account lockout policies after a defined number of failed attempts (e.g., 3–5 attempts within 5–10 minutes).
• Monitor and alert on repeated authentication failures for early detection of credential probing.
• Restrict SMB access strictly to trusted hosts only (as configured in this lab).
• Enforce strong password policies for all accounts to reduce likelihood of successful guessing.
• Implement multi-factor authentication (MFA) for accounts exposed to network logons.
```


---

## Risk Perspective
```
• Likelihood of credential guessing or brute-force behavior is moderate in a controlled environment; in production, repeated failures from a single IP indicate high risk.
• Potential for lateral movement exists if authentication succeeds for privileged accounts.
• Visibility of such activity without enhanced logging would be limited; enabling detailed SMB authentication logging ensures detection opportunities.
```


---

## Defensive Improvement Opportunities
```
• Centralized log collection and correlation via SIEM to detect patterns across multiple hosts and accounts.
• Alert threshold tuning for failed authentication attempts to minimize false positives while catching credential probing.
• Integration of host and network telemetry for comprehensive monitoring of authentication events.
• Continuous monitoring and analysis of repeated authentication patterns to detect early-stage credential attacks.
```

---

# 8. Key Takeaway

This experiment demonstrates that controlled SMB-based authentication attempts generate clear and structured security telemetry within the Windows Security log.

Successful and failed logon events (Event ID 4624 and 4625) provide detailed information about authentication activity, including:

- The account used (WindowsUser)  
- The source network address (192.168.100.20)  
- The outcome of each attempt (success or failure)  
- Timestamps and frequency of attempts  

By introducing a controlled SMB authentication surface, it becomes possible to produce consistent and attributable authentication events, enabling accurate analysis of log data.

Key observations from this lab execution include:

- Authentication attempts are fully logged, with both successes and failures clearly recorded.  
- Repeated failed logons generate clustered Event ID 4625 entries, forming patterns that are easily detectable.  
- Source IP attribution is reliable, allowing identification of the originating host.  
- Even a small number of controlled failed attempts is sufficient to validate detection logic and SOC monitoring thresholds.  

The results highlight that authentication activity provides significantly stronger detection signals compared to basic network reconnaissance, particularly when repeated failed attempts create identifiable patterns.

This experiment reinforces that effective detection of credential-based attack behavior relies on:

- Proper configuration of authentication services  
- Controlled exposure of network services for testing  
- Accurate logging of authentication events  
- Analysis of repeated activity patterns rather than isolated events  

From a defensive perspective, authentication failures represent high-value security events that can be used to detect early-stage credential probing, especially when monitored and correlated within a SIEM or similar security platform.

The lab results confirm that structured authentication testing provides actionable insights for SOC operations, threat detection tuning, and defensive improvements.

---

# 9. Lessons Learned

## Technical

- Windows logs both successful and failed SMB authentication attempts with detailed metadata, allowing clear tracking of authentication activity.  
- Event ID 4624 and 4625 provide key information including account name, logon type, source network address, and timestamp.  
- SMB-based authentication generates network logon events (Logon Type 3), confirming remote access attempts.  
- A valid SMB share is required to ensure authentication attempts are fully processed and logged correctly.  
- Controlled firewall rules (allowing only KaliLinuxMachine) enable precise testing of authentication behaviour while maintaining a restricted attack surface.  
- Observed lab results confirmed that even low-volume repeated failed logins create detectable patterns, validating detection logic.

---

## Detection

- Failed authentication attempts (Event ID 4625) are strong indicators of potential credential-based attacks.  
- Repeated failed logons from a single source create identifiable patterns suitable for SOC alerting.  
- Source IP address attribution (192.168.100.20) reliably identifies the originating host for correlation.  
- Clustering of failed logons over short time intervals provides actionable threat intelligence.  
- Correlation of multiple authentication events is more effective than analysing single events in isolation.  
- Enhanced logging configuration (from exp_002) significantly improves visibility and detection accuracy.

---

## Operational

- Controlled configuration changes must be applied and validated prior to testing to ensure a reliable baseline.  
- Snapshot discipline ensures safe rollback and repeatability of experiments.  
- Separating environment setup (Section 3) from execution (Section 4) improves clarity and structure.  
- Limiting authentication attempts prevents unintended account lockout while still allowing meaningful detection testing.  
- Structured documentation ensures reproducibility, supports SOC analysis, and aids future lab iterations.  
- Observed lab activity demonstrates the value of performing controlled experiments to validate assumptions in existing reports.

---

# 10. Post-Experiment Actions

## System reverted to baseline?

[ ] Yes  
[x] No (Controlled SMB firewall rule retained for continued testing)

---

## Snapshot Restored

Not required.

---

## Verification of System State

- SMB share remains accessible only from KaliLinuxMachine (192.168.100.20)  
- Firewall rule allowing TCP port 445 is restricted to a single source IP  
- No unintended exposure to other hosts within the network  
- Logging configuration from exp_002_Enable_Enhanced_Logging remains active  
- Observed SMB connectivity and authentication tests confirmed correct configuration and attribution  

---

## Documentation Updated

[x] Yes – screenshots and log evidence captured and stored.

---

## Change Log Updated

[x] Yes – controlled configuration changes documented for lab continuity.

---

## Operational Notes

- The controlled SMB exposure should be removed or reverted after completion of all related experiments  
- Firewall rules should be reviewed to ensure no unnecessary services remain exposed  
- Logs should be exported or centralised if further analysis is required  
- The environment is now prepared for deeper analysis in subsequent experiments  
- Observed repeated failed logon patterns validate SOC detection testing and provide reference data for future experiments

---

# 11. Professional Reflection

## Technical Development

This experiment strengthened:

- Understanding of Windows authentication mechanisms over SMB, including remote network logons and credential validation.  
- Practical experience generating and analysing authentication events (Event ID 4624 and 4625) from a controlled external host.  
- Ability to create a dedicated SMB authentication surface for safe and repeatable testing.  
- Familiarity with interpreting key event log fields such as Logon Type, Source Network Address, Workstation Name, and Authentication Package.  
- Validation that repeated failed authentication attempts produce clear, detectable patterns in Windows Security logs.

---

## Detection & SOC Perspective

From a defensive perspective, this experiment demonstrated:

- Authentication events provide high-value telemetry for detecting suspicious activity.  
- Failed logon attempts (Event ID 4625) are strong indicators of potential credential attacks.  
- Repeated authentication failures create identifiable patterns suitable for alerting in a SOC environment.  
- Source attribution allows correlation of activity to a specific host within the network (192.168.100.20).  
- Effective detection relies on analysing sequences of events rather than isolated log entries, supporting SIEM alert tuning and threshold development.  
- Observed lab activity validates assumptions in previous reports and provides baseline data for operational monitoring.

---

## Adversary Perspective

This experiment highlights that credential-based probing activity generates observable telemetry when authentication services are exposed:

- Repeated failed authentication attempts are likely to be detected in monitored environments.  
- Consistent targeting of a single account (WindowsUser) indicates potential enumeration or credential guessing behaviour.  
- Even low-volume attempts are visible when logging and monitoring are properly configured, reinforcing the value of enhanced auditing.  
- Structured, repeated testing helps understand how an adversary’s behavior would appear in a corporate environment.

---

## Governance Discipline

- Controlled configuration changes were applied and documented to maintain lab integrity.  
- Baseline system state preserved through snapshot management, ensuring safe rollback if required.  
- Environment preparation (setup) and execution phases clearly separated to improve clarity and reproducibility.  
- Evidence collected in a structured and reproducible manner, supporting report accuracy and operational continuity.  
- Observed results provide verification of previous report assumptions and offer additional insights for future experiments.

---

## Follow-Up Recommendation

Recommended Action:

- [ ] No Action Required  
- [x] Proceed to Follow-Up Experiment – Windows Event Log Deep Dive  
- [ ] Propose Security Hardening Change  
- [ ] Simulated Escalation Report

---

## Rationale

The next experiment will focus on detailed analysis of Windows Security log events, including field-level breakdown, event structure, and interpretation from an incident response perspective.  

- Builds on authentication telemetry generated in this experiment.  
- Deepens understanding of how SMB authentication events are analysed within a SOC environment.  
- Provides structured data for refining detection rules, SOC alert thresholds, and operational procedures.  
- Validates and improves upon assumptions made in previous reports by using controlled lab data.

---

End of Report