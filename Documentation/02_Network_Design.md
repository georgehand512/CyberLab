# 02_Network_Design

## Purpose

This document defines the detailed network configuration of the CyberLab environment.

It formally documents:

- Adapter types and roles
- Segmentation model
- Addressing decisions
- IPv6 configuration
- Default gateway design
- Validation outputs
- Security rationale

This document supports reproducibility, auditability, and structured experimentation.

---

## 1. Network Design Objectives

The CyberLab network was designed with the following objectives:

1. Strict internal segmentation
2. Controlled internet egress
3. No unintended external exposure
4. Static addressing for internal clarity
5. Reduced attack surface
6. Clear trust boundaries

The design prioritises governance and control over convenience.

---

## 2. VirtualBox Adapter Configuration

Each VM contains two network adapters:

### Adapter 1 – Internal Network (LabNetInt)

Type: Internal Network  
Name: LabNetInt  
Purpose: Controlled communication between Kali and Windows  
Internet Access: None  
Default Gateway: None configured  
IPv6: Disabled  

This adapter represents a simulated enterprise internal LAN.

---

### Adapter 2 – NAT Adapter (Controlled Egress)

Type: NAT  
Purpose: System updates and package management  
Internet Access: Yes (outbound only)  
Inbound Exposure: None  
Bridging: Disabled  

This adapter allows:

- Windows Update
- Kali package updates
- Tool maintenance

It does not expose the internal network externally.

---

## 3. Logical Segmentation Model

The lab consists of two logical segments:

### 3.1 Internal Segment – LabNet

Subnet: 192.168.100.0/24  
Addressing: Static  
IPv6: Disabled  

Characteristics:

- No default gateway configured
- No routing beyond virtual environment
- Used exclusively for controlled simulation
- Represents internal enterprise workstation segment

---

### 3.2 Controlled NAT Segment

Addressing: Automatically assigned by VirtualBox  
Purpose: Outbound update access only  

Characteristics:

- Routed via VirtualBox NAT engine
- Not bridged to host LAN
- Does not expose LabNet externally

This enforces strict egress control.

---

## 4. Addressing Configuration

### Windows 11 Pro (Target System)

Internal Adapter:
- IP Address: 192.168.100.10
- Subnet Mask: 255.255.255.0
- Default Gateway: None
- IPv6: Disabled

NAT Adapter (normally disabled):
- DHCP assigned
- Enabled only for periodic windows updates

---

### Kali Linux (Analysis & Simulation System)

Internal Adapter:
- IP Address: 192.168.100.20
- Subnet Mask: 255.255.255.0
- Default Gateway: None

NAT Adapter:
- DHCP assigned (VirtualBox default)
- Default Route present via NAT interface

---

## 5. Routing State Verification

### Kali – Route Table

ip route
```
default via 10.0.3.2 dev eth1 proto dhcp src 10.0.3.15 metric 101 
10.0.3.0/24 dev eth1 proto kernel scope link src 10.0.3.15 metric 101 
192.168.100.0/24 dev eth0 proto kernel scope link src 192.168.100.20 metric 100 
```

Expected Structure:

- Default route via NAT interface
- 192.168.100.0/24 via internal adapter
- No route from LabNet to host LAN

---

### Kali – Interface State

nmcli device status
```
DEVICE  TYPE      STATE                   CONNECTION   
eth1    ethernet  connected               lab_nat      
eth0    ethernet  connected               lab_internal 
lo      loopback  connected               lo    
```

ip -br addr
```
lo               UNKNOWN        127.0.0.1/8 ::1/128 
eth0             UP             192.168.100.20/24 
eth1             UP             10.0.3.15/24 fd17:625c:f037:3:87c:8ddd:258a:813c/64 fe80::5af5:f9fa:a084:a612
```

Expected:

- Internal interface: static 192.168.100.20
- NAT interface: DHCP assigned
- Both interfaces operational

---

### Windows – Interface Verification

ipconfig
```
Windows IP Configuration

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 192.168.100.10
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 
```


Expected:

Internal Adapter:
- 192.168.100.10
- No default gateway

Successful mutual ping between:

- 192.168.100.10
- 192.168.100.20

Confirms internal connectivity.

---

## 6. IPv6 Disablement Rationale

IPv6 was disabled on the internal LabNet segment to:

- Reduce unintended routing complexity
- Prevent automatic link-local communication paths
- Maintain deterministic addressing
- Simplify log interpretation
- Reduce unnecessary attack surface

IPv6 remains enabled on NAT interface where required by host configuration.

---

## 7. Security Design Considerations

The following controls are intentionally enforced:

- No bridged networking
- No host-only leakage
- No internal default gateway
- NAT strictly for updates
- Static internal addressing
- Segmentation validated via routing inspection

This design prevents:

- Accidental external exposure
- Cross-network contamination
- Uncontrolled routing
- Internal segment internet leakage

---

## 8. Validation Summary

The following checks confirm correct segmentation:

- Mutual ping across LabNetInt successful
- No default gateway on internal segment
- NAT connectivity confirmed
- No external IP reachable from LabNetInt
- Routing table inspection validated isolation

Baseline validated prior to OVA export.

---

## 9. Change Control

Any modification to:

- Adapter type
- Addressing
- Routing
- Segmentation
- IPv6 configuration

Must be documented in:

- 06_Change_Log.md
- 07_Snapshot_and_Version_Record.md

---

End of Document
