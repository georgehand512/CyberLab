# exp_001_Local_Enumeration

## Experiment Title

Internal Network Enumeration of Windows 11 from Kali

---

# 1. Objective

The objective of this experiment is to perform controlled internal enumeration from the Kali Linux attacker VM against the Windows 11 lab machine.

This experiment aims to:

- Identify exposed services on the Windows host
- Understand basic reconnaissance techniques
- Observe Windows logging behaviour during enumeration
- Identify potential detection opportunities
- Build structured reporting discipline

This experiment simulates early-stage attacker reconnaissance inside a segmented network.

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

- No internet-based scanning
- No exploitation
- No credential attempts
- No configuration changes permitted

---

# 3. Pre-Experiment Baseline State

Snapshots Taken:

Windows:  
WindowsMachine_Baseline_v1.0

Kali:  
KaliLinuxMachine_Baseline_v1.0

Internal connectivity verified:

```
ping 192.168.100.10 → Successful
```

Routing table verified:

- Default route via NAT only
- Internal network isolated

---

# 4. Methodology

## Step 1 – Host Discovery

From Kali:

```
ping 192.168.100.10
```

Purpose:  
Confirm host is reachable and active.

Expected Result:

- Successful ICMP reply from the Windows system.

Evidence:

Screenshot showing successful ping response.

File location:

```
Experiments/exp_001_Local_Enumeration/Screenshots/exp001_step01_ping_success.png
```

---

## Step 2 – Basic Port Scan

Perform TCP SYN scan of common ports to quickly identify exposed services.

```
nmap -sS 192.168.100.10
```

Purpose:  
Identify open TCP ports without full connection establishment.

Evidence:

Screenshot showing open ports identified during the scan.

File location:

```
Experiments/exp_001_Local_Enumeration/Screenshots/exp001_step02_nmap_syn_scan.png
```

---

## Step 3 – Service Version Detection

```
nmap -sV 192.168.100.10
```

Purpose:  
Determine service versions exposed by the Windows host.

This identifies running services and associated version information.

Evidence:

Screenshot capturing version detection results.

File location:

```
Experiments/exp_001_Local_Enumeration/Screenshots/exp001_step03_nmap_service_scan.png
```

---

## Step 4 – Full Port Scan (Optional)

```
nmap -p- 192.168.100.10
```

Compare results with:

```
nmap -p- -T4 192.168.100.10
```

Note: `-T4` uses a faster timing template.

Purpose:  
Enumerate all TCP ports to identify non-standard or less common exposed services.

Evidence:

Screenshot showing full port scan results.

File location:

```
Experiments/exp_001_Local_Enumeration/Screenshots/exp001_step04_nmap_full_scan.png
```

---

# 5. Observed Technical Results

Document:

- Open ports discovered
  
    ```
    None
    ```

- Services identified

    ```
    None detected
    ```

- Version information returned

    ```
    No service version information available
    ```

- Unexpected exposed services (if any)

    ```
    None
    ```

- Additional Observations

    ```
    All scanned TCP ports were filtered, indicating that the Windows Defender Firewall is actively blocking unsolicited inbound connection attempts.
    The target system also revealed a MAC address associated with an Oracle VirtualBox virtual network interface, suggesting the host is operating and doing so within a virtualised environment - our environment.
    ```

Evidence Storage Locations:

- Screenshots:

    ```
    Experiments/exp_001_Local_Enumeration/Screenshots/
    ```

- Packet Captures (if taken):

    ```
    Experiments/exp_001_Local_Enumeration/PacketCaptures/
    ```

- Exported Logs:

    ```
    Experiments/exp_001_Local_Enumeration/Logs/
    ```

---

# 6. Log & Detection Analysis

Critical Analysis Section

On Windows 11:

Open Event Viewer and review:

- Windows Logs → Security
- Windows Logs → System

Key Questions:

Did the ICMP ping generate any logged events?

Did the Nmap scanning generate:

- Event ID 4625 (Failed Logon)
- Event ID 5156 (Windows Filtering Platform Allowed Connection)
- Firewall log entries

If Windows Defender Firewall logging is enabled, check:

```
%systemroot%\system32\LogFiles\Firewall\pfirewall.log
```

<br>

Findings:
```
No logs found on WindowsMachine matching Event ID's 4625, 5156, 5157.
```

Evidence of such can be found in:

```
Experiments/exp_001_Local_Enumeration/Screenshots/

Specifically:

   `exp001_step06_Windows_Security_Logs.png`
   `exp001_step06_Windows_System_Logs.png`
```

Analysis:

```
Default Windows 11 logging and firewall configurations do not provide observable events for ping or SYN scans. Without enabling enhanced firewall logging or a SIEM ingesting network telemetry, such reconnaissance would likely go undetected, as it has done here.
```

---

# 7. Detection & Mitigation Reflection

If this were a corporate environment:

Would this scanning trigger an alert? 
```
Likely, yes
```

Why or why not?
```
Organisations would usually deploy additional monitoring systems like an EDR, IDS/IPS and/or SIEM to detect any abnormal/suspicious network activity across hosts and infrastructure. 
These systems analyse traffic patterns and generate logs or alerts when predefined detection rules are triggered, such as repeated connection attempts or port scanning behaviour.
```

What detection tools might detect this activity?
```
- SIEM ingesting firewall logs (if logging enabled)
- EDR network telemetry that captures packet-level activity
- Network-based IDS/IPS (signature-based detection for SYN scans)
```

Would segmentation limit impact?
```
Yes – scanning would remain confined to the internal subnet.
```

Mitigation considerations:
```
- Disable unnecessary services
- Restrict SMB exposure
- Harden firewall rules
- Implement alerting thresholds
- Monitor abnormal internal scanning behaviour
```

---

# 8. Key Takeaway

This experiment demonstrates that internal network enumeration against a default Windows 11 host generated minimal observable telemetry.

Although connection attempts were effectively blocked by the firewall, the host did not create clear Security or System events, and no firewall log existed.

This indicates that basic reconnaissance can occur silently without enhanced logging or monitoring.

From a defensive perspective, the experiment shows that effective detection requires centralized log correlation, SIEM ingestion, or explicit firewall logging to make such reconnaissance visible.

The key insight from this experiment is that **internal reconnaissance can occur with very limited visibility unless logging policies, monitoring tools, and alerting mechanisms are deliberately configured to detect scanning behaviour.**

This reinforces the importance of:

- Internal network monitoring
- Enhanced Windows audit policies
- Centralised log aggregation
- Behaviour-based detection mechanisms

---

# 9. Lessons Learned

Technical:

- Improved understanding of default Windows network service exposure
- Practical experience performing TCP port scanning from Kali using nmap
- Familiarity with service fingerprinting techniques

Detection:

- Many basic scans may not trigger alerts in default Windows configurations
- Logging visibility depends heavily on firewall and audit policy configuration
- Detection may require event correlation rather than single-event alerts

Operational:

- Snapshot discipline maintained
- No baseline configuration drift occurred
- Documentation process validated

---

# 10. Post-Experiment Actions

System reverted to baseline?

[ ] Yes  
[x] No (No changes made)

Snapshot restored:

Not required (read-only activity)

Documentation updated?

[x] Yes

Change Log updated?

Not required (no structural change)

---

# 11. Professional Reflection

## Technical Development

This experiment strengthened:

- Internal network enumeration skills
- Practical understanding of TCP port scanning
- Exposure analysis of default Windows services
- Awareness of reconnaissance behaviour patterns

## Detection & SOC Perspective

From a defensive viewpoint, this experiment demonstrated:

- Basic network scanning may not generate obvious alerts in default configurations
- Visibility is dependent on firewall and audit policy settings
- Windows Event Logging requires tuning to provide actionable telemetry
- Enumeration activity can appear benign without correlation

This reinforces the importance of:

- Proper logging configuration
- SIEM integration
- Alert threshold tuning
- Baseline network behaviour understanding

## Adversary Perspective

The experiment highlighted:

- The amount of service information exposed pre-authentication
- The value of segmentation in limiting lateral discovery
- The importance of minimising unnecessary open services

## Governance Discipline

- No configuration changes were made
- Baseline integrity preserved
- Snapshots validated
- Documentation updated

## Follow-Up Recommendation

Recommended Action:

- [ ] No Action Required
- [x] Create Follow-Up Experiment – Enable Enhanced Windows Logging
- [ ] Propose Security Hardening Change
- [ ] Simulated Escalation Report

Rationale:

Default Windows logging provides limited visibility into reconnaissance activity.

A follow-up experiment will enable advanced audit policies to measure telemetry improvement.

---

End of Report