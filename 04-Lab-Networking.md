# Lab Networking: Isolated NAT Network

This document records how I put my Kali Linux and Windows 11 VMs on one isolated NAT Network, confirmed they could reach each other, and worked out why one ping direction was blocked by Windows Firewall.

> **Ethics note:** All testing was done on virtual machines that I own, inside an isolated lab network.

## Objective

Both VMs used VirtualBox's default NAT mode, so they could not see each other. I wanted them on the same isolated network so Kali could later be used against the Windows 11 VM, while keeping both away from my home network.

## Environment

| Component | Details |
| --- | --- |
| Host | HP PC, Windows, 16 GB RAM |
| Hypervisor | Oracle VirtualBox 7.2.20 |
| Machines involved | Kali Linux 2026.2 (attacker), Windows 11 (target, 80 GB disk) |
| Network | NAT Network `LabNet`, `10.0.3.0/24`, DHCP enabled |
| Tools | `ip`, `ping` (Kali); `ipconfig`, `ping`, `netsh` (Windows) |

## Steps

### 1. Confirmed the problem on default NAT

With both VMs on the default NAT setting, I checked their addresses.

```bash
ip a
```

```
ipconfig
```

Both VMs reported `10.0.2.15`, with `10.0.2.2` as the default gateway. In default NAT mode each VM sits in its own private network, so the same address is reused.

<img width="257" height="95" alt="04-kali-ip-default-nat" src="https://github.com/user-attachments/assets/b91304ab-c98c-40bd-bddd-39c4bd2a77dd" />

*Figure 1: Kali on default NAT with address 10.0.2.15.*

<img width="511" height="251" alt="04-windows-ip-default-nat" src="https://github.com/user-attachments/assets/9b6250c9-f514-4c83-a8d6-6746d7e6f824" />

*Figure 2: Windows 11 on default NAT with the same address, 10.0.2.15.*

---

### 2. Pinged the shared address

I pinged `10.0.2.15` from both VMs. Both pings succeeded, but the replies came from each VM's own address, so they proved nothing about reaching the other VM. The replies took under 1 ms. The TTL values also showed it: Windows replied with `TTL=128` and Kali with `ttl=64`, which are the default values for each OS.

<img width="302" height="135" alt="04-ping-default-nat-windows" src="https://github.com/user-attachments/assets/68ed7af1-07cf-4126-ae05-51bf38bb9319" />

*Figure 3: Windows pinging 10.0.2.15 and getting its own reply (TTL=128).*

<img width="302" height="98" alt="04-ping-default-nat-kali" src="https://github.com/user-attachments/assets/dc63525a-e0b6-4646-98d7-5e66916ead0c" />

*Figure 4: Kali pinging 10.0.2.15 and getting its own reply (ttl=64).*

---

### 3. Created the LabNet NAT Network

In VirtualBox's Network Manager, on the **NAT Networks** tab, I created a network with these settings:

| Setting | Value |
| --- | --- |
| Name | `LabNet` |
| IPv4 Prefix | `10.0.3.0/24` |
| DHCP | Enabled |
| IPv6 | Disabled |

I chose `10.0.3.0/24` so it would not overlap the default NAT range `10.0.2.0/24`. A NAT Network lets VMs reach each other and the internet, while devices on my home network cannot start connections into them.

<img width="920" height="457" alt="04-nat-network-created" src="https://github.com/user-attachments/assets/20d8493c-4c0e-40c8-af5c-39f9ea57b05d" />

*Figure 5: LabNet created with prefix 10.0.3.0/24 and DHCP enabled.*

---

### 4. Attached both VMs to LabNet

With both VMs powered off, I opened each VM's **Settings > Network > Adapter 1**, changed **Attached to** from `NAT` to `NAT Network`, and selected `LabNet`.

<img width="582" height="381" alt="04-kali-adapter-labnet" src="https://github.com/user-attachments/assets/bb2784b9-39b7-41a6-b010-66fa03dd1373" />

*Figure 6: Kali Adapter 1 attached to the NAT Network LabNet.*

<img width="579" height="382" alt="04-windows-adapter-labnet" src="https://github.com/user-attachments/assets/409779ee-516f-418d-b558-a93f60dd6fb0" />

*Figure 7: Windows 11 Adapter 1 attached to the NAT Network LabNet.*

---

### 5. Found the new IP addresses

I started both VMs and checked their addresses again.

| VM | IP address | Default gateway |
| --- | --- | --- |
| Kali Linux | `10.0.3.3` | not recorded |
| Windows 11 | `10.0.3.4` | `10.0.3.1` |

The addresses were different and in the same `/24`, so the duplicate-address problem was gone.

<img width="426" height="121" alt="04-kali-ip-labnet" src="https://github.com/user-attachments/assets/6f711795-d1b9-4c85-b1b1-14f0303880c1" />

*Figure 8: Kali with address 10.0.3.3.*

<img width="488" height="154" alt="04-windows-ip-labnet" src="https://github.com/user-attachments/assets/27cc846c-390d-41eb-80ca-b285753c13a8" />

*Figure 9: Windows 11 with address 10.0.3.4.*

---

### 6. Tested ping in both directions

From Windows to Kali, the ping worked: 4 sent, 4 received, 0% loss. The replies showed `TTL=64`, which is Kali's default, so this time the other VM answered.

```
ping 10.0.3.3
```

<img width="509" height="353" alt="04-ping-windows-to-kali" src="https://github.com/user-attachments/assets/6b8ecb48-853f-428b-8b59-156a1af1f120" />

*Figure 10: Windows to Kali, 4 of 4 replies.*

From Kali to Windows, the ping failed (see Troubleshooting).

```bash
ping -c 4 10.0.3.4
```

<img width="434" height="329" alt="04-ping-kali-to-windows" src="https://github.com/user-attachments/assets/f6c42bbf-5e65-447b-808e-b15b28e25e3c" />

*Figure 11: Kali to Windows, 100% packet loss.*

---

### 7. Allowed ICMP echo through Windows Firewall

In a Command Prompt opened as administrator on the Windows VM, I added an inbound rule that allows ICMPv4 echo requests only.

```
netsh advfirewall firewall add rule name="Lab - Allow ICMPv4 Echo In" protocol=icmpv4:8,any dir=in action=allow
```

The command returned `Ok.`

<img width="503" height="121" alt="04-firewall-rule-added" src="https://github.com/user-attachments/assets/7fb54fc4-e6ba-4694-a6d6-0ebc37dbacf0" />

*Figure 12: Firewall rule added.*

I then repeated the ping from Kali. It succeeded, with `ttl=[PLACEHOLDER: TTL value from my Kali ping output]` in the replies.

```bash
ping -c 4 10.0.3.4
```

<img width="330" height="144" alt="04-ping-kali-to-windows-allowed" src="https://github.com/user-attachments/assets/e1e996b4-7e50-4931-9843-499693515165" />

*Figure 13: Kali to Windows after adding the rule.*

## Troubleshooting

### Issue: Kali could not ping Windows

**Issue:** After both VMs were on LabNet, pinging Windows from Kali got no replies, while pinging Kali from Windows worked.

```
4 packets transmitted, 0 received, 100% packet loss, time 3075ms
```

**Cause:** Windows Firewall blocks inbound ICMP echo requests by default. The request from Kali was dropped silently. When Windows pinged Kali, Windows started the exchange, so the reply was allowed back in. Kali printed no "Destination Host Unreachable" message, which suggested the packets arrived and were dropped rather than never arriving.

**Resolution:**

1. Added an inbound firewall rule for ICMPv4 echo requests with `netsh` (Step 7).
2. Pinged Windows from Kali again and got replies.

## Findings

| What I observed | What it means |
| --- | --- |
| Both VMs had `10.0.2.15` on default NAT | Each VM was in its own private network, so they could not reach each other |
| Self-pings on default NAT succeeded in under 1 ms | A successful ping does not prove the other machine is reachable; the VM was answering itself |
| TTL was 128 on Windows replies and 64 on Kali replies | TTL can hint at the OS of the machine that answered |
| On LabNet the VMs got `10.0.3.3` and `10.0.3.4` | Both are on the same network and can communicate |
| Windows to Kali worked, Kali to Windows timed out | Windows Firewall blocks unsolicited inbound ICMP by default |
| Kali to Windows worked after the ICMP rule | The firewall, not the network, was the cause of the failed ping |

## What I Learned

- A host that does not answer ping is not necessarily offline. A firewall can hide it, which will matter when I scan the Windows VM with Nmap.
- A successful ping can be misleading. Checking the IP address and the TTL showed my first test was a machine pinging itself.
- Firewalls treat inbound and outbound traffic differently, which is why the ping worked in one direction only.
- The ICMP rule is still active on the Windows VM, so my scan results in later labs will reflect that.

## Result

Kali (`10.0.3.3`) and Windows 11 (`10.0.3.4`) are now on the isolated `LabNet` NAT Network and can ping each other in both directions.

 
