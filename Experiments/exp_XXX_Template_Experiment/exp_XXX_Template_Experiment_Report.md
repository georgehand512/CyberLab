# Experiment Report Template

## Experiment ID

exp_XXX  

## Experiment Title

Clear, specific description of activity performed.

Example:
Local Windows Enumeration from Kali  
Credential Dump Simulation  
SMB Service Enumeration  
Privilege Escalation Attempt  

---

# 1. Objective

Clearly define:

- What question is being answered?
- What technique is being tested?
- What skill is being developed?

Example:

The objective of this experiment is to perform basic network enumeration from Kali against the Windows host to observe:
- What information is exposed
- What logs are generated
- What potential detection points exist

---

# 2. Scope & Constraints

Internal Lab Only.

Target:
- IP Address:
- Operating System:

Attacker:
- Host:
- Tools used:

Constraints:
- No external systems
- No internet-based testing
- Baseline must be restorable

---

# 3. Pre-Experiment Baseline State

Snapshot Taken:

Windows Snapshot:
Kali Snapshot:

Routing Verified:
[ ] Yes  
[ ] No  

Internal Connectivity Verified:
[ ] Yes  
[ ] No  

Notes:

---

# 4. Methodology

Step-by-step description of what was performed.

Commands executed:

```
Insert commands here ....


```


Explain what each command does and why it was used.

Do not just paste commands — explain intent.

---

# 5. Observed Technical Results

Document:

- Output observed
- Services discovered
- Credentials exposed
- Errors encountered

Screenshots:
Stored in:
/Evidence/Screenshots/

Packet Captures:
Stored in:
/Evidence/PacketCaptures/

Logs:
Stored in:
/Evidence/Logs/

---

# 6. Log & Detection Analysis

Critical Section (SOC relevance)

Questions to answer:

What Windows Event IDs were generated?

Did Defender log anything?

Were there authentication attempts logged?

Was there suspicious process creation?

Was there network traffic that could be detected?

Example structure:

Windows Security Log:
Event ID:

Source:
Description:

Relevance:

Kali Logs (if relevant):

Network Artefacts:

---

# 7. Detection & Mitigation Reflection

If this were a real organisation:

- Would this trigger an alert?
- What control would stop it?
- What logging improvement would help?
- Would segmentation reduce risk?

Proposed Mitigations:

---

# 8. Key Takeaway

Concise analytical conclusion from the experiment.

This should summarise the most important security or detection insight
identified during the experiment.

Example:

Internal network scanning generated minimal high-value telemetry on
a default Windows host.

Although connection attempts were recorded by Windows Filtering Platform
events, the activity did not produce clear security alerts. Effective
detection would require correlation within a SIEM or additional
network-based monitoring.

The experiment highlights the importance of enhanced audit policies
and centralised log analysis for detecting reconnaissance behaviour.

---

# 9. Lessons Learned

Technical:

Operational:

Documentation:

Unexpected findings:

---

# 10. Post-Experiment Actions

System reverted to baseline?
[ ] Yes
[ ] No

Snapshot restored:
Name:

Permanent configuration changes?
If yes, document in:
06_Change_Log.md

---

# 11. Professional Reflection

## Competency Development

This experiment contributed to development in the following areas:

- Networking comprehension
- Service exposure awareness
- Adversarial thinking
- Log interpretation
- Detection awareness
- Structured documentation discipline
- Controlled change management mindset

## SOC & Defensive Relevance

From a defensive perspective, this experiment reinforces:

- Recognition of early-stage reconnaissance behaviour
- Ability to correlate network activity with host logs
- Understanding of what is and is not logged by default
- Identification of detection visibility gaps
- Structured triage thinking

## Adversarial Insight

From an attacker simulation perspective, this experiment demonstrates:

- How much information can be gathered without authentication
- How segmentation limits blast radius
- How default configurations expose services
- Where stealth techniques may or may not trigger logs

## Governance & Process Awareness

This experiment was conducted under:

- Snapshot control
- Version tracking
- Change log discipline
- Evidence preservation

This reinforces professional operational standards aligned with security engineering and defence environments.

## Follow-Up Actions & Recommendations

Does this experiment require further action?

- [ ] No Action Required
- [ ] Create Follow-Up Experiment
- [ ] Propose Security Improvement
- [ ] Escalate as Simulated Incident

If action required, reference:

Security_Improvement_Record ID:
Related Change_Log entry:
New Experiment ID:

---

End of Report
