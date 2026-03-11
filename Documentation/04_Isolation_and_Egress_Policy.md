# 04_Isolation_and_Egress_Policy

## Purpose

This document formally defines the isolation model and internet egress policy of the CyberLab environment.

It establishes:

- Segmentation controls
- Internet access restrictions
- Routing limitations
- Governance intent
- Risk mitigation measures

The goal is to ensure controlled experimentation within a contained and reproducible virtual environment.

---

## 1. Isolation Philosophy

CyberLab is designed as a fully contained virtual security simulation environment.

The following principles apply:

1. No external targeting.
2. No exposure of internal segment to host LAN.
3. No bridged networking.
4. No uncontrolled routing.
5. No persistent external connectivity beyond update requirements.
6. Segmentation verified prior to experimentation.

Isolation is treated as a primary security control, not a convenience feature.

---

## 2. Segmentation Model

The environment is divided into two logical segments:

### 2.1 Internal Segment – LabNet

- Subnet: 192.168.100.0/24
- IPv6: Disabled
- Default Gateway: None
- Purpose: Controlled workstation simulation
- Exposure: Internal to VirtualBox only

Controls:

- No bridge adapters configured
- No host-only routing to external LAN
- No default route assigned on Windows internal adapter
- Static addressing for deterministic behaviour

The internal segment is not routable beyond the CyberLab environment.

---

### 2.2 Controlled Egress – NAT Segment

- VirtualBox NAT engine
- Outbound only
- Inbound unsolicited traffic blocked
- Used strictly for update and package management

Permitted Uses:

- Windows Update
- Kali package updates
- Tool maintenance
- Dependency installation

Prohibited Uses:

- External scanning
- External enumeration
- Internet-based target simulation
- Uncontrolled outbound experimentation

NAT access exists solely to maintain system integrity and patch status.

---

## 3. IPv6 Control Strategy

IPv6 is disabled on the internal LabNet segment to:

- Prevent automatic link-local communication
- Reduce unintended routing complexity
- Simplify log and packet inspection
- Minimise attack surface
- Maintain deterministic addressing

IPv6 remains enabled on the NAT adapter where required by host networking behaviour.

---

## 4. Default Gateway Policy

Internal Adapter:
- No default gateway configured.

This ensures:

- Internal traffic remains local.
- Internet routing is not possible via LabNetInt.
- Accidental external exposure is prevented.

Kali default route exists only via the NAT interface.

---

## 5. Bridging Policy

No bridged adapters are configured.

This prevents:

- Direct connection to host LAN
- Accidental interaction with external networks
- Network contamination
- Security policy violation

Bridging is explicitly prohibited unless formally documented and justified in the change log.

---

## 6. Risk Mitigation Measures

The architecture reduces the following risks:

- Unintended network exposure
- External scanning from lab systems
- Cross-network infection risk
- Misconfiguration leakage
- Ambiguous routing behaviour

All experiments occur within the controlled boundary of VirtualBox isolation.

---

## 7. Validation Controls

The following checks confirm policy compliance:

- Internal ping successful between VMs
- No default gateway on Windows internal adapter
- NAT route present only on designated interface
- No host LAN IP reachable from LabNetInt
- Routing tables inspected and documented

Validation is performed before exporting baseline versions.

---

## 8. Change Control Requirements

Any modification to:

- Adapter type
- Default gateway
- Subnet
- Routing configuration
- IPv6 state
- Bridging configuration

Must be recorded in:

- 06_Change_Log.md
- 07_Snapshot_and_Version_Record.md

Policy changes require justification and version increment.

---

## 9. Governance Statement

All activities within CyberLab are conducted in a controlled, isolated virtual environment for professional skills development.

No testing is performed against external systems.

This environment is engineered to reflect responsible security practice aligned with corporate, governmental, and defence-sector expectations.

---

End of Document