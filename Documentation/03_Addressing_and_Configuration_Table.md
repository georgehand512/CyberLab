# 03_Addressing_and_Configuration_Table

## Purpose

This document provides a structured reference table for all network addressing,
adapter roles, and interface mappings within the CyberLab environment.

It exists to:

- Provide a single source of truth for configuration
- Support troubleshooting
- Enable environment rebuild
- Maintain version accuracy
- Prevent configuration drift

This table reflects the validated v1.0 baseline state.

---

## 1. Logical Network Segments

| Segment Name | Subnet             | Purpose                     | Internet Access | IPv6 Status |
|--------------|--------------------|-----------------------------|-----------------|-------------|
| LabNetInt    | 192.168.100.0/24   | Internal simulation segment | No              | Disabled    |
| NAT Network  | DHCP (VirtualBox)  | Controlled update egress    | Yes (Outbound)  | Enabled     |

---

## 2. Virtual Machine Addressing

### Windows 11 Pro (Target System)

| Adapter Role      | VirtualBox Type | Interface Name (OS) | IP Address      | Subnet Mask      | Default Gateway |   IPv6   |
|-------------------|-----------------|---------------------|-----------------|------------------|-----------------|----------|
| lab_internal      | Internal        | Ethernet (Internal) | 192.168.100.10  | 255.255.255.0    | None            | Disabled |
| lab_nat           | NAT             | Ethernet (NAT)      | DHCP Assigned   | DHCP Assigned    | DHCP Assigned   | Enabled  |

Notes:
- No default gateway configured on internal adapter.
- Internal interface not routable externally.
- NOTE: NAT Adapter not usually used except for periodic Windows updates.

---

### Kali Linux (Analysis & Simulation System)

| Adapter Role      | VirtualBox Type | Interface Name (OS) | IP Address      | Subnet Mask     | Default Gateway | IPv6     |
|-------------------|-----------------|---------------------|-----------------|-----------------|-----------------|----------|
| lab_internal      | Internal        | eth0                | 192.168.100.20  | 255.255.255.0   | None            | Disabled |
| lab_nat           | NAT             | eth1                | 10.0.3.15*      | 255.255.255.0*  | 10.0.3.2*       | Enabled  |

\* NAT values may change depending on VirtualBox DHCP assignment.

Notes:
- Default route exists only via lab_nat.
- 192.168.100.0/24 reachable via lab_internal.
- No routing from LabNetInt to host LAN.

---

## 3. Routing Summary (Kali)

Baseline expected output structure:

ip route
```
default via 10.0.3.2 dev eth1 proto dhcp src 10.0.3.15 metric 101 
10.0.3.0/24 dev eth1 proto kernel scope link src 10.0.3.15 metric 101 
192.168.100.0/24 dev eth0 proto kernel scope link src 192.168.100.20 metric 100 
```

Expected logical result:

- default via 10.0.3.2 dev eth1 (lab_nat)
- 10.0.3.0/24 dev eth1
- 192.168.100.0/24 dev eth0 (lab_internal)

This confirms:
- Internet access only via NAT
- Internal segment isolated
- No unintended external routes

---

## 4. Connectivity Validation Matrix

| Source         | Destination       | Expected Result | Status (v1.0) |
|---------------|------------------|----------------|--------------|
| Kali (192.168.100.20) | Windows (192.168.100.10) | Ping Successful | ✅ |
| Windows | Kali | Ping Successful | ✅ |
| Kali | Internet via NAT | Successful | ✅ |
| Windows | Internet via NAT | Successful | ✅ |
| LabNetInt | Host LAN | Not Reachable | ✅ |

---

## 5. Interface Naming Standard

Within documentation, interfaces are referred to as:

- lab_internal → Internal LabNetInt interface
- lab_nat → NAT update interface

This naming avoids confusion between:

- Internal vs Internet
- Simulation vs Egress
- Segmented vs Routed traffic

---

## 6. Baseline Reference

This configuration corresponds to:

Environment Version: CyberLab_v1.0_Baseline  
Export File: 2026-03-09_CyberLab_v1.0_Baseline.ova  

Any change to:

- IP addressing
- Adapter type
- Default gateway
- Subnet
- IPv6 state

Must be recorded in:

- 06_Change_Log.md
- 07_Snapshot_and_Version_Record.md

---

End of Document



