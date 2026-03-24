# Development_Roadmap

## Purpose

This document outlines a structured development plan to transition from academic computer science study into an entry-level cyber security role, with a focus on SOC Analyst and defensive security pathways.

The roadmap aligns practical lab work, certification study, guided learning platforms, and personal project development into a controlled, phased progression.

The emphasis is on:

- Detection awareness
- Log analysis competence
- Structured investigation workflow
- Networking confidence
- Professional documentation discipline

This is not a fixed script, but a structured progression framework.

---

# Current Position

Academic Background:
- First Class BSc (Hons) Computer Science

Certifications:
- CompTIA Security+

Practical Training:
- TryHackMe SOC Level 1 Path (in progress)
- Personal CyberLab (VirtualBox, Windows 11 + Kali)
- Experiments exp_001 through exp_005 complete

Objective:
Secure an entry-level cyber security role (SOC Analyst / Junior Security Analyst) with strong defensive foundations and professional discipline.

---

# Development Philosophy

This roadmap is built on four principles:

1. Depth over volume
2. Detection before exploitation
3. Documentation discipline
4. Iterative improvement

Offensive activity is used only to generate realistic telemetry for defensive analysis.

---

# Phase 1 – Logging & Visibility Foundations

Objective:
Develop confidence analysing Windows logs and understanding what attacker reconnaissance looks like in telemetry.

Focus Areas:
- Windows Event Viewer
- Authentication events
- Firewall logging
- Process creation logging
- Basic Nmap scanning

Planned Experiments:

- exp_001_Local_Enumeration
- exp_002_Enable_Enhanced_Logging
- exp_003_Re-run_Enumeration_With_Logging
- exp_004_Failed_Login_Attempts
- exp_005_Windows_Event_Log_Deep_Dive

Outcomes:

- Understand Event IDs (4624, 4625, etc.)
- Recognise reconnaissance behaviour
- Identify logging gaps
- Explain findings clearly

---

# Phase 2 – Credential Abuse & Access Simulation

Objective:
Understand how authentication abuse and access attempts appear in logs.

Focus Areas:
- Password spraying simulation
- RDP authentication analysis
- SMB access attempts
- Basic credential exposure concepts

Planned Experiments:

- exp_006_SMB_Enumeration
- exp_007_Credential_Access_Simulation
- exp_008_Password_Spray_Simulation
- exp_009_RDP_Login_Analysis

Outcomes:

- Recognise brute-force behaviour
- Distinguish normal vs abnormal RDP traffic
- Correlate authentication attempts across logs
- Map behaviour to MITRE ATT&CK techniques

---

# Phase 3 – Investigation Workflow & Incident Simulation

Objective:
Simulate entry-level SOC alert triage and investigation processes.

Focus Areas:
- Timeline building
- Log correlation
- Alert reasoning
- Structured reporting

Planned Experiments:

- exp_010_Privilege_Escalation_Simulation
- exp_011_Simulated_Security_Alert_Triage
- exp_012_Process_Creation_Analysis
- exp_013_Lateral_Movement_Simulation
- exp_014_Incident_Report_Writeup

Outcomes:

- Build 2–3 formal investigation writeups
- Demonstrate incident lifecycle understanding
- Improve analytical confidence
- Practice technical explanation skills

---

# Phase 4 – Hardening & Defensive Engineering Awareness

Objective:
Develop awareness of preventative controls and configuration tuning.

Focus Areas:
- Service hardening
- Firewall rule adjustments
- Logging policy tuning
- Segmentation validation

Planned Experiments:

- exp_015_Service_Hardening
- exp_016_Firewall_Tuning
- exp_017_Logging_Tuning_Review
- exp_018_Segmentation_Revalidation

Outcomes:

- Understand defence-in-depth principles
- Demonstrate improvement mindset
- Reinforce change management discipline

---

# Parallel Project – Windows Log Analysis Tool

Project: Project_001_Windows_Log_Analyzer

Objective:
Develop a simple Python tool that:

- Reads exported Windows log data
- Identifies repeated failed login attempts
- Detects suspicious source IP patterns
- Outputs a structured report

Skills Demonstrated:

- Python programming ability
- Log parsing logic
- Security pattern recognition
- Structured output generation
- Practical application of SOC concepts

This project will be developed after sufficient log data has been generated in Phase 1 and 2 experiments.

---

# Learning Integration

The CyberLab experiments will reinforce:

Security+ domains:
- Networking
- Access control
- Incident response lifecycle
- Logging and monitoring
- Common attack patterns

TryHackMe SOC Path:
- Alert triage
- Investigation methodology
- Detection logic
- Blue team fundamentals

---

# Timeline Estimate

Post-Security+ focus period:

4–8 weeks structured execution:
- 1–2 experiments per week
- Continuous TryHackMe progression
- Python project development after Phase 2
- 2–3 polished investigation reports

Quality is prioritised over speed.

---

# Professional Output Goals

By completion of this roadmap:

- Security+ certified
- TryHackMe SOC Level 1 completed
- 6–10 documented lab experiments
- 2–3 investigation writeups
- 1 clean Python security project
- Structured GitHub repository
- Clear defensive security positioning

---

# Long-Term Direction

The primary focus is defensive security and SOC readiness, while maintaining foundational understanding of adversarial techniques.

Future direction will be guided by:

- Enjoyment of log analysis vs exploitation
- Comfort with detection engineering tasks
- Exposure to real job descriptions
- Interview feedback

---

End of Document
