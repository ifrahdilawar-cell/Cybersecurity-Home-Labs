# VirtualBox Installation and Troubleshooting

This document records how I installed Oracle VirtualBox on Windows for my cybersecurity home lab, including the issues I hit and how I fixed them.

## Environment

| Component | Details |
| --- | --- |
| Host | HP PC, Windows, 16 GB RAM |
| Hypervisor | Oracle VirtualBox 7.2.20 |

## Installation Steps

### 1. Download VirtualBox

Downloaded VirtualBox 7.2.20 for Windows hosts from the [official VirtualBox website](https://www.virtualbox.org/).

---

### 2. SHA-256 Checksum Verification

To verify the downloaded installer's checksum, I located the `.exe` file in the Downloads folder and opened PowerShell by entering `powershell` in File Explorer's address bar.

I ran the following command to calculate the installer's SHA-256 hash:

```powershell
Get-FileHash ".\VirtualBox-7.2.20-175154-Win.exe" -Algorithm SHA256
```
<img width="959" height="201" alt="Installer&#39;s SHA-256 hash" src="https://github.com/user-attachments/assets/12d11614-8038-44d8-b709-92c7960bf491" />

I compared the calculated hash with the checksum listed in Oracle's [official SHA-256 checksums](https://download.oracle.com/virtualbox/7.2.20/SHA256SUMS). Both values matched.

<img width="811" height="332" alt="published SHA-256 checksum" src="https://github.com/user-attachments/assets/ee8a53c7-61e2-4178-8b5a-860116488f8c" />

---

### 3. Run the Installer

Ran the installer. It stopped with the Visual C++ error described under [Troubleshooting](#installation-issue-microsoft-visual-c-redistributable). After fixing it, I ran the installer again and completed the installation with the default options.

---

## Troubleshooting

### Installation Issue: Microsoft Visual C++ Redistributable

**Issue:** Encountered an installation error related to the Microsoft Visual C++ Redistributable, a runtime component required by some Windows applications.

<img width="275" height="164" alt="Microsoft Visual C++ Redistributable Error" src="https://github.com/user-attachments/assets/46e21914-bdfc-424c-a222-08b70411d132" />

**Resolution:**

1. Downloaded `vc_redist.x64.exe` from Microsoft's [official Visual C++ Redistributable downloads page](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist).
2. Installed the redistributable package.
3. Restarted the computer to complete the process.
4. Ran the VirtualBox installer again.

---

### Installation Issue: Missing Dependencies (Python Bindings)

**Issue:** During installation, a **Missing Dependencies** screen said the VirtualBox Python bindings require the Python Core package and the `win32api` bindings.

**Resolution:** This only affects the optional Python bindings, which are used to control VirtualBox with Python scripts. They are not needed to create and run virtual machines, so I continued the installation without them.

## Result

VirtualBox 7.2.20 is installed and verified. The next step is installing the Kali Linux virtual machine. See [Kali Linux Installation and Troubleshooting](Kali-Linux.md).
