# Linux and Systems Networking Labs Portfolio

Welcome. This repository serves as a technical portfolio documenting practical solutions and troubleshooting workflows for various Linux administration and systems networking challenges. 

The primary objective is to demonstrate methodical approach, root cause analysis, and resolution verification across different infrastructure scenarios.

---

## Labs Directory

### 1. Domain Name System (DNS) and Name Resolution
| Lab ID | Challenge Name | Difficulty | Documentation Link |
| :---: | :--- | :---: | :--- |
| 01 | Stockholm: DNS Health Check | Medium | [View Documentation](./01-stockholm-portal/) |
| 02 | Jakarta: It's always DNS | Hard | [View Documentation](./02-jakarta-dns/) |

### 2. Remote Access and Security (SSH)
* *Scenarios and documentation will be added upon completion.*

### 3. Service Management and Init Systems (Systemd)
* *Scenarios and documentation will be added upon completion.*

---

## Troubleshooting Methodology

Every logged scenario follows a structured engineering workflow to ensure systematic resolution:

1. **Information Gathering:** Inspecting system logs, status outputs, and relevant configuration files directly from the CLI.
2. **Root Cause Analysis (RCA):** Isolating the exact point of failure, whether it originates from misconfigured ports, symlink breaks, or missing directory permissions.
3. **Remediation:** Implementing the technical fix using administrative tools and text editors under proper privilege levels.
4. **Verification:** Validating functionality post-fix through networking utilities or HTTP requests to confirm target status.

---

## Core Technologies and Tools

The environments involve working with standard Linux tools and networking components, including:
* **System Tools:** `cat`, `grep`, `nano`, `getent`
* **Network Testing & Analysis:** `curl`, `ping`, network interface debugging
* **Service Control:** `systemd` init framework (`systemctl`)
* **Configuration Management:** `/etc/resolv.conf`, `/etc/nsswitch.conf`, and local deployment environments.

---
**Maintained by:** Samer Almarwani 