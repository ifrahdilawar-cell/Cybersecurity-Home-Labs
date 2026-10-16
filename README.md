
# Cybersecurity Home Lab

This repository documents the setup and configuration of a cybersecurity home lab using Oracle VirtualBox, Kali Linux, and Windows 11.

**Purpose:** To build a controlled virtual environment for hands-on cybersecurity practice, experimentation, and skill development.

## Objectives

- Set up a virtual lab environment using VirtualBox.
- Install and configure Kali Linux and Windows 11.
- Learn virtual networking and isolate lab systems safely.
- Practice cybersecurity techniques in an authorized environment.
- Document troubleshooting, experiments, and lessons learned.

## Lab Environment

| Component | Details |
|---|---|
| Virtualization | Oracle VirtualBox 7.2.20 |
| Host Operating System | Windows |
| Linux Virtual Machine | Kali Linux |
| Windows Virtual Machine | Windows 11 |
| Networking | To be configured and tested |

## Setup and Documentation

### 1. VirtualBox Installation

Downloaded VirtualBox 7.2.20 for Windows hosts from the [official VirtualBox website](https://www.virtualbox.org/).

### 2. SHA-256 Checksum Verification

To verify the downloaded installer's checksum, I located the `.exe` file in the Downloads folder and opened PowerShell by entering `powershell` in File Explorer's address bar.

I ran the following command to calculate the installer's SHA-256 hash:

```powershell
Get-FileHash ".\VirtualBox-7.2.20-175154-Win.exe" -Algorithm SHA256
```
<img width="959" height="201" alt="Virtual Box Hash" src="https://github.com/user-attachments/assets/43403499-6619-4873-af2f-f075c0e6d026" />

The calculated hash was compared with the checksum published by Oracle, and both values matched.

<img width="811" height="332" alt="Oracale(Virtual Box) official hash" src="https://github.com/user-attachments/assets/41423385-805d-4196-9e3b-e25969c5f4ec" />

**Note:** Checksum verification was performed after installation.

### 3. Installation Issue: Microsoft Visual C++ Redistributable

**Issue:** Encountered an installation error related to the Microsoft Visual C++ Redistributable, a runtime component required by some Windows applications.

<img width="275" height="164" alt="Screenshot 2026-10-11 203043" src="https://github.com/user-attachments/assets/6360d3c7-d595-49d9-b080-fe47d49623ab" />

**Resolution:**

1. Downloaded `vc_redist.x64.exe` from Microsoft's [official Visual C++ Redistributable downloads page](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist).
2. Installed the redistributable package.
3. Restarted the computer to complete the process.

### 4. Virtual Machine Setup

The lab environment includes Kali Linux and Windows 11 virtual machines running on VirtualBox.


### 5. Networking and Isolation

The goal is to configure a controlled virtual network that allows safe cybersecurity practice without unintentionally exposing lab systems to external networks.

Network settings will be configured and tested before documenting the environment as isolated.

## Learning Goals

- Develop Linux and Windows administration skills.
- Understand virtual networking and system isolation.
- Build practical experience in security monitoring and log analysis.
- Explore ethical penetration testing within authorized lab systems.
- Maintain clear documentation of configurations, troubleshooting, and practical exercises.


