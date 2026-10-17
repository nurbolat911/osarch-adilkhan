# Lab 1 — Your first virtual machine

Pair: Nurbolat Adilkhan (Solo)   Driver first half: Nurbolat Adilkhan
Machine: Laptop running Windows 11 (8 cores, 16 GB RAM), guest Debian GNU/Linux 13.7.0 (approved by instructor)
Date: 16.10.2026

## What I did
1. Installed Oracle VirtualBox 7.2.20. Licence: https://www.gnu.org/licenses/gpl-3.0.html
2. Created VM lab01-ubuntu: 2 vCPU, 4 GB RAM, 40 GB dynamic disk, NAT.
3. Installed Debian (approved alternative to Ubuntu), took snapshot clean-install.
4. Broke it: `sudo mv /etc/network/interfaces /etc/network/interfaces.broken && sudo systemctl restart networking && ping -c 4 8.8.8.8`
5. Rolled back to clean-install: restored snapshot clean-install in VirtualBox; verified with `ping -c 4 8.8.8.8`.

## Resource justification
My physical host machine is equipped with an 8-core CPU and 16 GB of physical RAM running Windows 11. I allocated 2 vCPUs (25% of host cores) and 4 GB of RAM (25% of total system memory) to the virtual machine. This allocation is optimal for a headless Linux server running minimal CLI utilities and background daemons without risking memory starvation or paging on the host OS, leaving 6 physical cores and 12 GB of RAM entirely free for Windows 11 background processes. A 40 GB dynamic virtual disk was selected so that the VM only consumes disk space on demand while providing ample capacity for OS files and temporary packages.

## Result
The VM boots; the break-and-rollback cycle is shown in the evidence below.

## What did not work the first time
The manual instructed breaking the network configuration via Netplan (`/etc/netplan/`), which is standard for Ubuntu Server. Because I installed Debian (with permission), the `/etc/netplan` directory was missing, and related commands failed to execute. I realized Debian manages network interfaces natively through `/etc/network/interfaces` rather than Netplan. To replicate the intended fault, I moved `/etc/network/interfaces` to `/etc/network/interfaces.broken` and restarted the `networking` service, successfully rendering the network unreachable before performing the rollback.

## Evidence
- [evidence/vm-settings.png](evidence/vm-settings.png) — CPU, memory and disk as configured
- [evidence/guest-release.txt](evidence/guest-release.txt) — Debian GNU/Linux is running in the guest
- [evidence/broken.png](evidence/broken.png) — the guest in its broken state
- [evidence/snapshot-list.png](evidence/snapshot-list.png) — the snapshot, after the rollback
