# Lab Basics: Guest Additions and Snapshots

This document records how I checked and installed VirtualBox Guest Additions on my Kali Linux and Windows 11 VMs and took clean snapshots of both.

## Environment

| Component | Details |
| --- | --- |
| Host | HP PC, Windows, 16 GB RAM |
| Hypervisor | Oracle VirtualBox 7.2.20 |
| Machines involved | Kali Linux 2026.2, Windows 11 (build 10.0.26300.9457, 80 GB disk) |
| Network | NAT Network `LabNet`, `10.0.3.0/24` |
| Tools | VirtualBox Guest Additions 7.2.20, VirtualBox Snapshots |

## Steps

### 1. Checked Guest Additions on Kali

I ran the following command in a Kali terminal to see whether the VirtualBox guest modules were loaded.

```bash
lsmod | grep vbox
```

The output listed `vboxsf` and `vboxguest`, so Guest Additions was already present on the pre-built Kali image and I did not install anything.

<img width="207" height="71" alt="05-kali-guest-additions-check" src="https://github.com/user-attachments/assets/cff4962b-3ce3-4563-8a60-e2a5f83b6500" />

*Figure 1: Guest Additions modules already loaded on Kali.*

---

### 2. Checked Guest Additions on Windows 11

In **Settings > Apps > Installed apps** I searched for `Virtual Box` and then `Oracle`. Neither search found anything, and the VM screen did not fill the VM window, so I concluded Guest Additions was not installed.

<img width="503" height="380" alt="05-windows-guest-additions-check" src="https://github.com/user-attachments/assets/34d19646-780c-44ba-98bb-6093804d8d3a" />

*Figure 2: No Guest Additions entry in Windows 11 Installed apps.*

---

### 3. Installed Guest Additions on Windows 11

I used these steps:

1. In the VM window, I chose **Devices > Insert Guest Additions CD image...**
2. In File Explorer, the drive **CD Drive (D:) VirtualBox Guest Additions** appeared.
3. I ran `VBoxWindowsAdditions.exe` from that drive and accepted the default components.
4. I restarted Windows when the installer asked.

<img width="509" height="381" alt="05-windows-guest-additions-cd" src="https://github.com/user-attachments/assets/c53a6f37-2183-49a5-88e2-6ac91bc03851" />

*Figure 3: The Guest Additions CD mounted as drive D:.*

After the restart, **Oracle VirtualBox Guest Additions 7.2.20** appeared in Installed apps.

<img width="409" height="322" alt="05-windows-guest-additions-installed" src="https://github.com/user-attachments/assets/82a8364f-a1e9-40b2-abd3-38adb4274f5a" />

*Figure 4: Guest Additions 7.2.20 installed on Windows 11.*

---

### 4. Turned on display auto-resize

The Windows screen did not fill the VM window after the restart. In the VM window I opened the **View** menu and turned **Auto-resize Guest Display** off and on again. After that, the Windows screen resized to fill the window.

---

### 5. Took clean snapshots

I shut down each VM from inside the guest, then took a snapshot from the **Snapshots** tab in VirtualBox Manager, so I can roll back to a known-good state before any scans.

| VM | Snapshot name |
| --- | --- |
| Windows 11 | `Clean - LabNet + Guest Additions` |
| Kali Linux | `Clean - LabNet` |

<img width="959" height="276" alt="05-windows-snapshot-taken" src="https://github.com/user-attachments/assets/1ab7641d-5a51-493a-8413-d3f986e4d2fd" />

*Figure 5: Windows 11 snapshot created.*

<img width="959" height="304" alt="05-kali-snapshot-taken" src="https://github.com/user-attachments/assets/9a945003-9d04-4ff1-953d-0f1b88d5e172" />

*Figure 6: Kali snapshot created.*

## Result

Both VMs now have Guest Additions, and I have a clean snapshot of each, taken with `LabNet` working. These are my restore points before the scanning labs.
