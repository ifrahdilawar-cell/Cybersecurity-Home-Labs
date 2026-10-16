# Kali Linux Installation and Troubleshooting

This document records how I installed Kali Linux in Oracle VirtualBox for my cybersecurity home lab, including the error I hit and how I fixed it.

## Environment

| Component | Details |
| --- | --- |
| Host | HP PC, Windows, 16 GB RAM |
| Hypervisor | Oracle VirtualBox 7.2.20 |
| Guest OS | Kali Linux 2026.2 (64-bit) |
| Method | Pre-built Kali VirtualBox image |

## Installation Steps

### 1. Download the image

1. Went to [kali.org/get-kali](https://www.kali.org/get-kali/#kali-platforms) and opened **Virtual Machines**.
2. Downloaded the **VirtualBox 64-bit** image: `kali-linux-2026.2-virtualbox-amd64.7z` (about 3.7 GB).

### 2. Extract and import

1. Extracted the `.7z` file with 7-Zip
2. The folder contains two files that must stay together: 
   - `.vbox`: the VM configuration
   - `.vdi`: the virtual hard disk
     
   <img width="443" height="77" alt="Linux Kali" src="https://github.com/user-attachments/assets/0a3152a2-2434-4c93-adb6-9da90d11b1a3" />

3. Double-clicked the `.vbox` file. Kali appeared in VirtualBox with the status **Powered Off**.

### 3. Adjust settings

| Setting | Default | Changed to |
| --- | --- | --- |
| Base Memory | 2048 MB | 4096 MB |
| Processors | 2 | 2 |
| Video Memory | 128 MB | 128 MB |
| Network | NAT | NAT |

I increased the memory because Kali runs better with 4 GB, and my host has 16 GB.

## Troubleshooting

### Error: VT-x is disabled in the BIOS

When I first started the VM, it failed and its status changed to **Aborted**.

<img width="959" height="496" alt="VirtualBox VT-x error" src="https://github.com/user-attachments/assets/d8353b78-88b5-49a5-8d7b-f1a579483c5f" />


**Error message:**

```
Not in a hypervisor partition (HVP=0) (VERR_NEM_NOT_AVAILABLE).
VT-x is disabled in the BIOS for all CPU modes (VERR_VMX_MSR_ALL_VMX_DISABLED).
Result Code: E_FAIL (0x80004005)
```

**Cause:** Hardware virtualization was turned off in the BIOS, so VirtualBox could not run a 64-bit guest.

**Fix:**

1. Confirmed in Task Manager > Performance > CPU that **Virtualization** showed **Disabled**.
2. Restarted into the BIOS (on this HP PC: press `Esc` at startup, then `F10`).
3. Enabled **Virtualization Technology (VTx)** under **Advanced > System Options**.
4. Saved and exited with `F10`.
5. Started the VM again. Kali booted successfully.

## Result

Kali Linux now boots and runs in VirtualBox and is ready for lab work.
