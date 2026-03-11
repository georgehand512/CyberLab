# 01_Lab_Architecture_Overview

## Purpose

This document provides a high-level architectural overview of the CyberLab environment.

It defines:

- System roles
- Network segmentation
- Trust boundaries
- Controlled egress design
- Security posture
- Version baseline state

This overview is intended to provide clarity before detailed network and configuration documentation.

---

## 1. Architectural Objectives

CyberLab was designed with the following principles:

1. Strict network segmentation
2. Controlled internet egress
3. Reproducibility through exportable baselines
4. Detection-aware experimentation
5. Clear separation of roles
6. Minimal attack surface exposure
7. No unintended external connectivity

The architecture prioritises governance and controlled simulation over convenience.

---

## 2. Virtual Machine Roles

### 2.1 Windows 11 Pro – Target System

Role:
- Simulated enterprise workstation
- Credential store
- Log generation source
- Event analysis subject

Network Interfaces:
- Internal Lab Network (Static IP)
- NAT Adapter (Windows Updates only)

Security Posture:
- IPv6 disabled on internal segment
- No default gateway on internal network
- Controlled NAT access for update management only

---

### 2.2 Kali Linux – Analysis & Simulation System

Role:
- Controlled attack simulation platform
- Network analysis system
- Log generation initiator
- Tool experimentation environment

Network Interfaces:
- Internal Lab Network (Static IP)
- NAT Adapter (For tool updates and package management)

Security Posture:
- Internal network isolated from host LAN
- No bridging enabled
- Segmentation preserved by design

---

## 3. Network Segmentation Model

The environment contains two distinct network segments:

### 3.1 Lab Internal Network (LabNetInt)

Purpose:
- Controlled communication between Windows and Kali
- Simulated internal enterprise segment
- Static addressing

Characteristics:
- No default gateway
- IPv6 disabled
- No direct internet access
- Isolated within VirtualBox

This segment represents a controlled enterprise LAN.

---

### 3.2 NAT Network (Controlled Egress)

Purpose:
- Windows Update
- Kali package updates
- Tool maintenance

Characteristics:
- Routed through VirtualBox NAT
- Not bridged to host LAN
- Not exposed externally
- Used strictly for update management

This ensures systems remain patched without exposing the internal lab segment.

---

## 4. Trust Boundaries

The architecture establishes clear trust boundaries:

## Network Architecture Diagram (Logical View)

```
                            ┌─────────────────────┐
                            │      Host OS        │
                            │   Windows 11 Pro    │
                            └──────────┬──────────┘
                                       │
                        (VirtualBox Isolation Boundary)
                                       │
     ──────────────────────────────────┼──────────────────────────────────
                                       │
                          ┌────────────┴────────────┐
                          │  CyberLab Virtual Env   │
                          │                         │
                          │  ┌───────────────┐      │
                          │  │  Kali Linux   │      │
                          │  │ 192.168.100.20│      │
                          │  └───────┬───────┘      │
                          │          │ LabNet       │
                          │  ┌───────┴───────┐      │
                          │  │ Windows 11    │      │
                          │  │ 192.168.100.10│      │
                          │  └───────────────┘      │
                          │                         │
                          │     (NAT Adapter)       │
                          │           │             │
                          │      VirtualBox NAT     │
                          └───────────┼─────────────┘
                                      │
                                   Internet
                                (Updates Only)
```

Key Boundary Controls:

- No bridged adapters
- No host-only leakage
- No external routing from LabNet
- NAT strictly for updates

---

## 5. Addressing Model (High Level)

Internal Segment:
- Windows 11: 192.168.100.10
- Kali Linux: 192.168.100.20
- Subnet: 192.168.100.0/24

NAT Segment:
- Dynamically assigned via VirtualBox
- Used only for update connectivity

Full addressing details are documented in:
`03_Addressing_and_Configuration_Table.md`

---

## 6. Baseline Version State

Current Validated Baseline:
- CyberLab_v1.0_Baseline
- IPv6 disabled on LabNet
- Static IP configuration confirmed
- Segmentation validated
- Successful mutual ping over internal network
- NAT connectivity verified
- OVA export completed

This baseline represents a stable foundation prior to structured experimentation.

---

## 7. Architectural Intent Going Forward

Future experimentation will occur within this controlled framework and will:

- Preserve segmentation integrity
- Capture logs systematically
- Document configuration changes
- Record event artefacts
- Maintain version tracking discipline

The architecture will not be modified without change log documentation.

---

## 8. Security & Governance Statement

All activities within CyberLab are conducted inside a controlled, isolated virtual environment.

No testing is performed against external systems.

The architecture reflects professional discipline aligned with corporate, governmental, and defence-sector expectations.

---

End of Document