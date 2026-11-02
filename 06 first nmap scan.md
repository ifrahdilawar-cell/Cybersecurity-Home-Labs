# First Nmap Scan: Kali Against Windows 11

This document records my first Nmap scans from Kali Linux against my Windows 11 VM, with Windows Firewall on and off, and what the results show.

> **Ethics note:** All testing was done on virtual machines that I own, inside an isolated lab network.

## Objective

I wanted to see what a basic Nmap scan reveals about my Windows 11 VM, and how much of that Windows Firewall hides. This gives me a baseline for the defender's-side review in the next lab.

## Environment

| Component | Details |
| --- | --- |
| Host | HP PC, Windows, 16 GB RAM |
| Hypervisor | Oracle VirtualBox 7.2.20 |
| Machines involved | Kali Linux 2026.2 (attacker, `10.0.3.3`), Windows 11 (target, `10.0.3.4`, build 10.0.26300.9457) |
| Network | NAT Network `LabNet`, `10.0.3.0/24` |
| Tools | Nmap 7.99, `ping`, Windows Security (Firewall & network protection) |

## Steps

### 1. Confirmed the target address and connectivity

I ran `ipconfig` on Windows to check the IP address. It was `10.0.3.4`.

<img width="549" height="220" alt="06-windows-ip-before-scan" src="https://github.com/user-attachments/assets/36feeaf4-673e-4722-b51b-6c953de2887b" />

*Figure 1: Windows 11 at 10.0.3.4.*

From Kali I pinged the target. All 4 packets were answered with `ttl=128`. The ICMP firewall rule from the networking lab was still in place.

```bash
ping -c 4 10.0.3.4
```

<img width="353" height="119" alt="06-ping-before-scan" src="https://github.com/user-attachments/assets/596867a0-e2c1-4aaf-a938-7197725447d9" />

*Figure 2: Ping to the target before scanning, 0% packet loss.*

---

### 2. Checked the firewall state

In Windows Security, under **Firewall & network protection**, the firewall was on for the Domain, Private and Public profiles. **Public network** was the active profile.

---

### 3. Scanned with the firewall on

I ran a default Nmap scan from Kali. It tests the 1,000 most common TCP ports.

```bash
nmap 10.0.3.4
```

Nmap reported the host as up, and all 1,000 ports as filtered with no response. The scan took 21.90 seconds. The MAC address showed an Oracle VirtualBox virtual NIC.

<img width="362" height="95" alt="06-nmap-basic-scan" src="https://github.com/user-attachments/assets/aa4af577-36da-41bc-8a4d-944935d1ad29" />

*Figure 4: All 1,000 ports filtered with the firewall on.*

---

### 4. Turned the firewall off and scanned again

In Windows Security, I switched Microsoft Defender Firewall off for the network profiles, then ran the same scan.

<img width="508" height="327" alt="06-firewall-off png" src="https://github.com/user-attachments/assets/1688a987-1656-4162-bf31-c5f2f394c380" />

*Figure 5: Firewall turned off in Windows Security.*

```bash
nmap 10.0.3.4
```

Nmap found three open TCP ports (135, 139 and 445), and 997 closed ports. The scan took 1.98 seconds.

<img width="363" height="112" alt="06-nmap-firewall-off" src="https://github.com/user-attachments/assets/7d85f33d-5ede-4dcf-b415-3e11fd09eeb7" />

*Figure 6: Ports 135, 139 and 445 open with the firewall off.*

---

### 5. Ran a service version scan

With the firewall still off, I used `-sV` to identify the services on the open ports.

```bash
nmap -sV 10.0.3.4
```

Nmap identified port 135 as Microsoft Windows RPC and port 139 as Microsoft Windows netbios-ssn. Port 445 was listed as `microsoft-ds?`, so Nmap could not confirm that service and was guessing from the port number. The service info reported the OS as Windows, with no more detail. The scan took 8.69 seconds.

<img width="398" height="132" alt="06-nmap-version-scan" src="https://github.com/user-attachments/assets/fa5977f2-0dd5-4087-8058-be565230795d" />

*Figure 7: Service detection results with the firewall off.*

---

## Findings

| What I observed | What it means |
| --- | --- |
| With the firewall on, all 1,000 ports were `filtered` and the scan took 21.90 seconds | The firewall silently dropped the probes, so an attacker learns only that the host exists |
| With the firewall off, ports 135, 139 and 445 were open and 997 were closed | Windows exposes RPC, NetBIOS and file-sharing related services when nothing blocks them |
| The scan took 1.98 seconds with the firewall off | Closed ports answer immediately, while filtered ports make the scan wait for timeouts |
| The MAC address was identified as an Oracle VirtualBox virtual NIC | A scanner can tell the target is a VirtualBox VM even when every port is hidden |
| `-sV` confirmed RPC on 135 and netbios-ssn on 139, but port 445 stayed `microsoft-ds?` | Version detection gives more than a port number, but it is not always certain |
| The OS was reported only as Windows | A single service scan did not reveal the Windows 11 build |
| Public was the active firewall profile | The Public profile is the strictest, which may help explain why Windows hid everything |

## What I Learned

- A firewall changes what a scan can learn. With it on, Nmap saw nothing, and with it off the same machine exposed three services.
- `filtered` and `closed` mean different things: filtered means no reply (usually a firewall), and closed means the machine answered that nothing is listening.
- A question mark in Nmap output, like `microsoft-ds?`, means the result is a guess, so I should not report it as confirmed.
- Turning off real-time protection would not have changed these results. The firewall is what filters the scan, not the antivirus.
- I turned the firewall off only on my own isolated VM, and turned it back on after the experiment so later results start from the same state.

## Result

I scanned my Windows 11 VM from Kali and compared the results with the firewall on and off. With it on, no ports were visible, and with it off, ports 135, 139 and 445 were open.


