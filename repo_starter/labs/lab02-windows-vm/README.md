
# Lab 2 — A Windows guest, and a network between them

Pair: Nurbolat Adilkhan / Solo 
Driver first half: Nurbolat Adilkhan
Machine: Laptop running Windows 11 (AMD Ryzen 5, 4 cores/8 threads, 16 GB DDR4 RAM), guests Debian GNU/Linux (lab01-ubuntu) + Windows 11 Enterprise eval (lab02-windows)
Date: 26.10.2026

## What I did
1. Downloaded Windows 11 Enterprise evaluation. Licence: https://www.microsoft.com/en-us/evalcenter/download-windows-11-enterprise
2. Created lab02-windows: 2 vCPU, 4 GB, 64 GB thin, UEFI + Secure Boot, vTPM. Installed VirtualBox Guest Additions for display resizing and clipboard sharing.
3. Network mode: NAT Network (`NatNetwork`, 10.0.2.0/24), both guests. Why: Standard NAT in VirtualBox isolates each guest behind an independent router so they cannot communicate with each other, while Bridged mode fails on campus Wi-Fi because unknown MAC addresses are blocked. NAT Network connects both guests to a shared virtual network switch, enabling direct guest-to-guest communication while preserving outbound internet access.
4. Ping Ubuntu → Windows timed out because Windows Defender Firewall blocks incoming ICMPv4 echo requests by default on public network profiles. Fixed with: `New-NetFirewallRule -DisplayName "Allow ping in (ICMPv4)" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow`
5. Overcommitment: set 10 GB + 10 GB on a 16 GB host.

## Network diagram
```text
                  Internet (ubuntu.com)
                            |
           Host laptop (Windows 11, 16 GB RAM)
                            |
        [VirtualBox NAT Network · 10.0.2.0/24]
                /                        \
       lab01-ubuntu (Debian)      lab02-windows (Win 11)
             10.0.2.3                    10.0.2.4

```

## Result

|  | Before | After |
| --- | --- | --- |
| Host RAM in use | 7.0 GB | 15.1 GB (99%) |
| Available | 8.3 GB | 189 MB |
| Committed / Swap used | 10.3 / 17.9 GB | 31.4 / 32.1 GB |
| What I noticed | — | Host system experienced severe UI freezing and mouse stutter; committed memory reached 31.4 GB (exceeding physical RAM), triggering heavy paging to SSD swap; `sudo apt update` took significantly longer. |

## What did not work the first time

When pinging Windows from Debian (`ping -c 4 10.0.2.4`), packets timed out with 100% loss because Windows Defender Firewall blocks ICMP echo requests by default. Instead of disabling the firewall completely, an inbound rule permitting ICMPv4 Type 8 was added via PowerShell as Administrator, which immediately allowed two-way ICMP communication.

## Evidence

* [evidence/ipconfig-windows.txt](https://www.google.com/search?q=evidence/ipconfig-windows.txt) — Windows address
* [evidence/ip-addr-linux.txt](https://www.google.com/search?q=evidence/ip-addr-linux.txt) — Ubuntu address
* [evidence/ping-both-ways.txt](https://www.google.com/search?q=evidence/ping-both-ways.txt) — both directions and the internet
* [evidence/host-memory-before.png](https://www.google.com/search?q=evidence/host-memory-before.png) — host memory, guests off
* [evidence/host-memory-after.png](https://www.google.com/search?q=evidence/host-memory-after.png) — host memory, both guests overcommitted

```

```
