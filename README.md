# CyberLab – Detection-Aware Cyber Security Foundations Environment

## Overview

CyberLab is a controlled, segmented virtual environment built to develop and demonstrate entry-level cyber security competency in a structured, professional manner.

This lab is not a "hacking playground".  
It is a governed simulation environment designed to build practical capability in:

- Networking fundamentals
- Network segmentation
- Controlled attack simulation
- Logging awareness
- Event analysis
- Documentation discipline
- Analytical thinking
- Reproducibility and governance

The environment is designed to reflect responsible security engineering practices suitable for corporate, governmental, and defence-oriented roles.

---

## Development Roadmap

This CyberLab is part of a structured professional development plan focused on entry-level SOC and defensive security readiness.

The roadmap outlines phased progression through:

- Logging and visibility foundations  
- Credential abuse simulation  
- Investigation workflow development  
- Defensive hardening and tuning  

The full structured plan can be found here:

**Development_Roadmap.md**

The emphasis is on detection awareness, structured experimentation, and disciplined documentation rather than unstructured offensive activity.

---

## Purpose

The objective of CyberLab is to:

1. Develop practical cyber security skills beyond academic study.
2. Demonstrate structured experimentation and evidence capture.
3. Build confidence in investigating alerts and explaining technical findings.
4. Maintain strict isolation and controlled egress.
5. Produce professional documentation suitable for portfolio review.

This repository serves as technical documentation and evidence of disciplined lab engineering and experimentation.

---

## Architecture Summary

CyberLab consists of:

- Windows 11 Pro (Target System)
- Kali Linux (Analysis & Simulation System)
- Segmented Internal Network (LabNet)
- Controlled NAT interface for Windows updates only
- IPv6 disabled on internal segment
- Baseline snapshot and export version control

The environment is designed with:

- Explicit network segmentation
- No unintended external exposure
- Controlled update pathways
- Reproducible configuration

Full architectural documentation is provided in the `/Documentation` directory.

---

## Governance Principles

The lab is built and maintained under the following principles:

- Controlled simulation only
- No unauthorised external targeting
- Isolation by default
- Minimal egress policy
- Reproducibility over convenience
- Documentation-first methodology
- Change logging and version tracking

This reflects professional standards expected in regulated and security-sensitive environments.

---

## Repository Structure

```
CyberLab/
│
├── README.md
├── Development_Roadmap.md
├── LICENSE
│
├── Documentation/      # Architecture, configuration, governance
├── Experiments/        # Structured experiment subfolders (report + evidence)
├── Scripts/            # Supporting analysis scripts
└── Exports/            # Versioned baseline OVA files
```


Each experiment follows a structured format:

- Objective
- Environment state
- Actions performed
- Logs generated
- Detection analysis
- Observations
- Reflection

---

## Skills Demonstrated

CyberLab supports development of competencies expected in entry-level roles such as:

- SOC Analyst
- Junior Security Analyst
- Cyber Security Technician
- Security Operations
- Infrastructure Security Support

Key capability areas:

- Network configuration and validation
- Static IP management
- Routing analysis
- Log interpretation
- Windows Event investigation
- Attack artefact identification
- Segmentation verification
- Change documentation
- Professional reporting

---

## Version Control & Baselines

The lab environment is versioned and exported as controlled baseline OVA images.

Each version is documented in:

- Snapshot and version records
- Change log history
- Configuration tables

This ensures the environment can be rebuilt, audited, or reviewed consistently.

---

## Professional Development Intent

This lab represents ongoing structured development toward a professional cyber security career.

The goal is to:

- Build operational confidence
- Strengthen analytical thinking
- Improve documentation discipline
- Demonstrate governance awareness
- Align with industry and defence-sector expectations

Experiments are conducted in a controlled, ethical manner with emphasis on understanding detection and response impact.

---

## Disclaimer

All activities conducted within this lab are performed exclusively inside a controlled virtual environment.

No testing is performed against external systems.

This lab exists solely for professional skills development and educational purposes.

---

## Status

Environment Version: v1.0 (Baseline Validated)  
Platform: VirtualBox  
Host OS: Windows 11 Pro  
Maintained by: George Hand  
Location: United Kingdom  

---

## Future Development

Planned expansion includes:

- Structured detection mapping
- Event ID correlation studies
- Controlled privilege escalation simulations
- Basic SIEM-style log analysis workflows
- Formal experiment reporting discipline

---

End of README