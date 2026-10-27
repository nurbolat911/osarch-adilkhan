# Lab 1 — Your first virtual machine

Pair: Nurbolat Adilkhan / Solo   Driver first half: Nurbolat Adilkhan
Machine: Laptop running Windows 11 (AMD Ryzen 5, 4 cores/8 threads, 16 GB DDR4 RAM), guest Debian GNU/Linux 13 (trixie)
Date: 27.10.2026

## What I did
1. Installed Oracle VM VirtualBox 7.1. Licence: https://www.virtualbox.org/wiki/VirtualBox_PUEL
2. Created VM lab01-ubuntu (Debian-OS): 2 vCPU, 2048 MB RAM, 40 GB dynamic disk, NAT Network (10.0.2.0/24).
3. Installed Debian GNU/Linux 13 (trixie), set persistent hostname to `debian-os`, verified active network, powered off cleanly, and took snapshot `clean-install`.
4. Broke it persistently: commented out the interface lines in `/etc/network/interfaces` (`# auto enp0s3`, `# iface enp0s3 inet dhcp`) and rebooted (`sudo reboot`). Verified persistent failure across reboot: `cat /etc/network/interfaces`, `ip -br a` (enp0s3 DOWN), and `ping -c 2 1.1.1.1` (Network is unreachable).
5. Rolled back to clean-install: shut down VM (`sudo poweroff`), restored snapshot `clean-install` in VirtualBox, booted up, and proved successful rollback: `/etc/network/interfaces` is restored, `ip -br a` shows `enp0s3` UP with IP `10.0.2.3`, and `ping -c 4 debian.org` succeeds with 0% packet loss.

## Resource justification
Allocated 2 vCPUs out of 8 host logical threads (25%) and 4096 MB RAM out of 16 GB host physical RAM (25%). The virtual disk is dynamically allocated with a maximum size of 40 GB, initially occupying ~2.5 GB on host NVMe storage. This leaves 12 GB of RAM and 6 CPU threads fully available for host Windows 11 operations and additional guest virtual machines.

## Result
The VM boots; the break-and-rollback cycle is shown in the evidence below.

## What did not work the first time
The initial submission lacked rollback verification because the snapshot list was captured before the break while the VM was running, `guest-release.txt` was captured during network downtime, and the hostname was inconsistent (`localhost` vs `debian-os`). Furthermore, resource justifications originally mismatched the VM settings, and using `ip link down` did not survive a reboot. To resolve all issues: hostname was permanently set to `debian-os`, memory/disk allocations were aligned to 4096 MB and 40 GB, `guest-release.txt` was recaptured with `lsb_release -a`, a persistent break was implemented in `/etc/network/interfaces` and verified after reboot, and snapshot `clean-install` was restored, showing complete restoration of network connectivity (`ip -br a` UP, ping 0% loss).

## Evidence
- [evidence/vm-settings.png](evidence/vm-settings.png) — CPU, memory and disk as configured (4096 MB RAM, 40 GB dynamic disk)
- [evidence/guest-release.txt](evidence/guest-release.txt) — output of lsb_release -a inside the guest
- [evidence/broken.png](evidence/broken.png) — persistent break showing commented interfaces, DOWN status, and unreachable network after reboot
- [evidence/snapshot-list.png](evidence/snapshot-list.png) — snapshots showing clean-install restored after rollback
- [evidence/restored.png](evidence/restored.png) — guest terminal showing restored interfaces, UP link, and successful ping after rollback
