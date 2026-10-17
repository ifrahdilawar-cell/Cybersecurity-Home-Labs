# Windows 11 Installation and Troubleshooting

This document records how I installed Windows 11 in Oracle VirtualBox as the target machine for my cybersecurity home lab, including the issue I hit and how I fixed it.

## Environment

| Component | Details |
| --- | --- |
| Host | HP PC, Windows, 16 GB RAM |
| Hypervisor | Oracle VirtualBox 7.2.20 |
| Guest OS | Windows 11 (64-bit) |
| Method | Official Microsoft ISO installed in a new VM |

## Installation Steps

### 1. Download the Windows 11 ISO

Downloaded the **Windows 11 (multi-edition ISO for x64 devices)** from Microsoft's [Windows 11 download page](https://www.microsoft.com/software-download/windows11).

---

### 2. Create the virtual machine

In VirtualBox, clicked **New** and selected the Windows 11 ISO. I used these settings:

| Setting | Value |
| --- | --- |
| Base Memory | 4096 MB |
| Processors | 2 |
| Virtual disk | 60 GB, dynamically allocated |

I used 4 GB of memory because my host has 16 GB, which leaves enough for Kali to run alongside it.

---

### 3. Install Windows

Started the VM and let the installer run until the setup screens appeared. During setup, Windows asked for a Microsoft account, which is covered under Troubleshooting.

## Troubleshooting

### Issue: Setup requires a Microsoft account

**Issue:** Windows 11 setup asked me to sign in with a Microsoft account and offered no option to continue without one.

**Cause:** Windows 11 Home and Pro setup requires a Microsoft account and an internet connection, and Microsoft has been removing the known bypasses.

**Resolution:**

1. Pressed `Shift + F10` on the account screen to open a Command Prompt.
2. Ran the following command:

```
start ms-cxh:localonly
```

3. This opened the **Create a user for this PC** window, where I created a local account with a username, a password, and three security questions.

## Result

Windows 11 is installed in VirtualBox with a local account and is ready to be used as the target machine for attack and defense labs.
