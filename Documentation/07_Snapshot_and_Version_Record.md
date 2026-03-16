# 07_Snapshot_and_Version_Record

## Purpose

This document tracks:

- VirtualBox snapshots
- OVA exports
- Version states
- Validation status

It ensures:

- Reproducibility
- Rollback capability
- Controlled experimentation
- Configuration integrity

No experiment should begin without a verified baseline reference in this file.

---

# Snapshot Naming Standard

Snapshots must follow this format:

VMName_Baseline_vX.X  
VMName_PreExp_ExpID  
VMName_PostExp_ExpID  

Examples:

WindowsMachine_Baseline_v1.0  
KaliLinuxMachine_Baseline_v1.0  
WindowsMachine_PreExp_001  
KaliLinuxMachine_PostExp_002  

---

# OVA Naming Standard

OVA files must follow:

YYYY-MM-DD_CyberLab_vX.X_Descriptor.ova  

Examples:

2026-03-09_CyberLab_v1.0_Baseline.ova  

---

# Version Record

---

## Version: v1.0 – Baseline Validated

**Date Created:** 2026-03-09  
**Status:** Stable Baseline  

### Included VMs

- WindowsMachine (Windows 11 Pro)
- KaliLinuxMachine (Kali Linux)

### Network State

Internal Network: LabNetInt  
Subnet: 192.168.100.0/24  

Windows:
- 192.168.100.10
- No default gateway on internal interface

Kali:
- 192.168.100.20
- Default route via NAT only

IPv6:
- Disabled on LabNetInt

Segmentation:
- Verified
- No bridged adapters
- No external LAN exposure

### Snapshots

WindowsMachine_Baseline_v1.0  
KaliLinuxMachine_Baseline_v1.0  

### OVA Export

2026-03-09_CyberLab_v1.0_Baseline.ova  

### Validation Checklist

- [x] Internal ping verified
- [x] Routing table inspected
- [x] No unintended gateways
- [x] Windows Update completed
- [x] Kali updated
- [x] IPv6 disabled on LabNet
- [x] Snapshot created
- [x] OVA exported

---

# Recovery Procedure

To restore baseline:

1. Power off both VMs.
2. Restore snapshot:
   - Windows11_Lab_Baseline_v1.0
   - Kali_Lab_Baseline_v1.0
3. Boot both systems.
4. Verify:
   - Internal connectivity
   - Routing table
   - No additional interfaces present

If snapshot corruption suspected:

1. Remove VMs from VirtualBox.
2. Import OVA:
   File → Import Appliance
3. Validate configuration using 05_Build_and_Configuration_Guide.md

---

# Version Increment Rules

Increase minor version (v1.1, v1.2) when:

- Logging configuration changes
- Minor hardening applied
- Documentation updated without structural network changes

Increase major version (v2.0) when:

- Network segmentation changes
- Additional VM added
- New subnet introduced
- Architectural modification occurs

---

# Governance Rule

No experiment may permanently alter the baseline.

Before experimentation:

- Snapshot must be taken.
- Experiment must be documented.
- Post-experiment review required.
- System must be reverted unless change intentionally incorporated into new version.

---

End of Document