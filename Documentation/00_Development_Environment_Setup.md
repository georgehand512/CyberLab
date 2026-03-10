# 00_Development_Environment_Setup

## Purpose

This document defines the host machine software configuration required to support the CyberLab environment.

It ensures:

- Reproducibility
- Version control discipline
- Structured documentation
- Script development capability
- Long-term maintainability

The host environment is treated as part of the controlled lab ecosystem.

---

## 1. Host Platform

**Host Operating System:** Windows 11 (Home Edition)  
**Virtualisation Platform:** Oracle VirtualBox  
**Lab Version:** CyberLab v1.0 (Baseline Validated)

The full architecture and network design of the CyberLab environment is documented in:  **01_Lab_Architecture_Overview.md**

The host system is configured to support isolated virtual networking, controlled egress, and structured experiment documentation.

---

## 2. Core Development Tooling

### 2.1 Visual Studio Code (Stable)

Purpose:
- Markdown documentation
- Experiment reporting
- Python scripting
- Git integration
- Structured repository management

Installation Configuration:
- Added to system PATH
- Registered as default editor for supported file types
- "Open with Code" context menu enabled

Version Verified: 

```
    1.111.0
    ce099c1ed25d9eb3076c11e4a280f3eb52b4fbeb
    x64
```

---

### 2.2 Git

Purpose:
- Version control
- Change tracking
- Documentation governance
- Portfolio publishing

Installation Configuration:
- Default branch set to `main`
- Git Bash integration enabled
- CLI accessible via PATH

Version Verified:
```
    git version 2.53.0.windows.1
```
---

### 2.3 Python 3.x (Latest Stable)

Purpose:
- Log parsing
- Automation scripting
- Security tooling experimentation
- Structured data analysis

Installation Configuration:
- Installed system-wide
- Added to PATH
- pip verified

Version Verified:
```
    Python 3.13.12

    pip 25.3 from C:\Users\username\AppData\Local\Programs\Python\Python313\Lib\site-packages\pip (python 3.13)
```
---

## 3. VSCode Extensions

### Required Extensions

- Markdown All in One
- GitLens
- Python (Microsoft)
- Pylance
- YAML

### Optional (Installed As Needed)

- PowerShell
- Jupyter
- Remote SSH
- Docker
- Draw.io Integration

The extension footprint is intentionally minimal to reduce complexity and maintain environment stability.

---

## 4. Repository Structure Standard

```
CyberLab/
│
├── Documentation/      # Architecture, configuration, governance
├── Experiments/        # Structured experiment subfolders containing; reports, Logs, packet captures, screenshots
├── Scripts/            # Supporting analysis scripts
└── Exports/            # Versioned baseline OVA files
```

All documentation is maintained in Markdown format.

Word documents are not used for final documentation artifacts to ensure portability, versioning compatibility, and repository transparency.

---

## 5. Configuration Standards

The development environment follows these principles:

- Markdown over proprietary formats
- Version-controlled documentation
- Clear file naming conventions
- Date-based export versioning
- Minimal extension usage
- Structured change logging

---

## 6. Rebuild Procedure

In the event of host system rebuild:

1. Install Git
2. Install Python
3. Install Visual Studio Code
4. Clone repository
5. Verify tooling versions
6. Import CyberLab OVA baseline

This ensures environment reproducibility within minimal time.

---

## 7. Status

Environment Setup Version: v1.0  
Last Reviewed: 2026-03-10   
Maintained By: George Hand

---

End of Document
