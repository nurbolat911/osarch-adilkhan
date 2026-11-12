# Lab 4 — Partition, mirror, fail, recover

Pair: Nurbolat Adilkhan / Solo · Date: 12.11.2026 · Hypervisor: Oracle VirtualBox 7.1
Machine: laptop with Windows 11. Guests: Debian GNU/Linux 13 "trixie" (VM `Debian-OS`, my Linux guest) and Windows 11 Enterprise evaluation (VM `Windows-OS`).

## Windows

I added a 1 GB dynamically allocated virtual disk `lab04-win.vdi` to the Windows guest (SATA controller, port 1) and worked in PowerShell as administrator. `Get-Disk` showed disk 0 (64 GB, GPT, the system disk) and disk 1 (1 GB, RAW), so I used only disk 1.

```powershell
PS> Get-Disk
Number Friendly Name Serial Number        HealthStatus OperationalStatus Total Size Partition Style
------ ------------- -------------        ------------ ----------------- ---------- ---------------
0      VBOX HARDDISK VB1d442c83-158450d0  Healthy      Online                  64 GB GPT
1      VBOX HARDDISK VB35c5d61f-d4a25e6d  Healthy      Online                   1 GB RAW

PS> Initialize-Disk -Number 1 -PartitionStyle GPT
PS> New-Partition -DiskNumber 1 -UseMaximumSize -AssignDriveLetter | Format-Volume -FileSystem NTFS -NewFileSystemLabel LAB04

DriveLetter FriendlyName FileSystemType DriveType HealthStatus OperationalStatus SizeRemaining       Size
----------- ------------ -------------- --------- ------------ ----------------- -------------       ----
D           LAB04        NTFS           Fixed     Healthy      OK                    991.06 MB 1007.93 MB

PS> Get-Disk | Select Number, FriendlyName, PartitionStyle, Size

Number FriendlyName  PartitionStyle        Size
------ ------------  --------------        ----
     0 VBOX HARDDISK GPT            68719476736
     1 VBOX HARDDISK GPT             1073741824

PS> Get-Volume | Where FileSystemLabel -eq 'LAB04'

DriveLetter FriendlyName FileSystemType DriveType HealthStatus OperationalStatus SizeRemaining       Size
----------- ------------ -------------- --------- ------------ ----------------- -------------       ----
D           LAB04        NTFS           Fixed     Healthy      OK                    989.98 MB 1007.93 MB
```

**MBR or GPT: GPT.** Windows 11 boots through UEFI, and UEFI works with GPT; the system disk of this VM is also GPT, so the new disk matches it. GPT keeps a backup copy of the partition table at the end of the disk and checks it with checksums, while MBR has one copy and no checks, and MBR is limited to 2 TiB and four primary partitions. For a 1 GB disk the size limits do not matter, so the reason for GPT is consistency with UEFI and the safer table.

## Linux — layout before and after

I worked in the Debian guest. I added three 1 GB virtual disks to it (they appeared as `sdb`, `sdc`, `sdd`); the system disk is `sda` and I never used it in a command.

### Before ([evidence/lsblk-before.txt](evidence/lsblk-before.txt))
```
$ lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
NAME    SIZE TYPE FSTYPE  MOUNTPOINTS
sda    40,3G disk
├─sda1 38,2G part ext4    /
├─sda2    1K part
└─sda5  2,1G part swap    [SWAP]
sdb       1G disk
sdc       1G disk
sdd       1G disk
sr0    50,7M rom  iso9660
```

### After
```
$ lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
NAME     SIZE TYPE  FSTYPE            MOUNTPOINTS
sda     40,3G disk
├─sda1  38,2G part  ext4              /
├─sda2     1K part
└─sda5   2,1G part  swap              [SWAP]
sdb        1G disk
└─sdb1  1022M part  linux_raid_member
  └─md0 1021M raid1 ext4              /mnt/mirror
sdc        1G disk
└─sdc1  1022M part  linux_raid_member
sdd        1G disk
└─sdd1  1022M part  linux_raid_member
  └─md0 1021M raid1 ext4              /mnt/mirror
sr0     50,7M rom   iso9660
```
The mirror `md0` is built from `sdb1` and `sdd1` and mounted on `/mnt/mirror`. `sdc1` is the disk I failed on purpose; it is no longer in the array (it still carries an old RAID label, which is normal).

## What I did

I connected to the Debian guest from my host with SSH (port forwarding 127.0.0.1:2222 to the guest, because the guest is on a NAT Network), pasted the commands, saved every output with the command above it, and copied the files to the host with `scp`.

Partitions (one GPT partition per disk, marked for RAID) and the mirror from two disks:
```bash
sudo apt install -y mdadm parted
for d in sdb sdc sdd; do sudo parted -s /dev/$d mklabel gpt mkpart primary 1MiB 100% set 1 raid on; done
sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 --run --bitmap=internal /dev/sdb1 /dev/sdc1
cat /proc/mdstat        # state 1: healthy
```
File system, proof file and checksums before the failure:
```bash
sudo mkfs.ext4 -L mirror /dev/md0
sudo mkdir -p /mnt/mirror && sudo mount /dev/md0 /mnt/mirror && sudo chown $USER: /mnt/mirror
echo "Lab 4 proof file — Nurbolat Adilkhan — $(date)" > /mnt/mirror/proof.txt
head -c 20M /dev/urandom > /mnt/mirror/random.bin
cd /mnt/mirror && sha256sum proof.txt random.bin
```
Failing one disk and checking the data (state 2: degraded):
```bash
sudo mdadm /dev/md0 --fail /dev/sdc1
sudo mdadm /dev/md0 --remove /dev/sdc1
cat /proc/mdstat
cat /mnt/mirror/proof.txt
cd /mnt/mirror && sha256sum proof.txt random.bin
```
Adding the third disk and rebuilding (state 3: rebuilt), then keeping the array after a reboot:
```bash
sudo mdadm /dev/md0 --add /dev/sdd1
cat /proc/mdstat
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
sudo update-initramfs -u
```

## Three states

| State | What `/proc/mdstat` and `mdadm --detail` showed | File |
|---|---|---|
| Healthy | `[2/2] [UU]`, `State : clean`, active sync on `sdb1` and `sdc1` | [evidence/array-healthy.txt](evidence/array-healthy.txt) |
| Degraded | `[2/1] [U_]`, `State : clean, degraded`, `sdb1` active, second slot `removed` | [evidence/array-degraded.txt](evidence/array-degraded.txt) |
| Rebuilt | `[2/2] [UU]`, `State : clean`, active sync on `sdb1` and `sdd1` (`sdd1` replaced `sdc1`) | [evidence/array-rebuilt.txt](evidence/array-rebuilt.txt) |

I also kept a capture taken while the rebuild was still running (`recovery = 99.9%`, `[2/1] [U_]`): [evidence/array-recovering.txt](evidence/array-recovering.txt).

**Checksums before and after the failure: they match.** The two files were:

| File | SHA-256 (before and after the failure) |
|---|---|
| `proof.txt` | `4c3148a62590018f708e0dc88a464e3a5bce70247f763bd5dd889a0996319c98` |
| `random.bin` (20 MB of random data) | `1ccc2aa9b621b3a3e2fd4ee3872d6a93b2fa57f2db79c21ad3f10ceae93ee66c` |

Evidence: [evidence/checksum-before.txt](evidence/checksum-before.txt) and [evidence/checksum-after-failure.txt](evidence/checksum-after-failure.txt). My `diff` of the two files printed `checksums match`, and `proof.txt` was still readable while the array was degraded.

## RAID is not a backup

No, RAID 1 does not help if a colleague deleted the wrong file an hour ago. RAID 1 writes every change to both disks at the same moment, so the deletion was copied to the second disk too and no old copy is left to restore. RAID protects against a disk failing (my test showed the data survives with one disk removed), not against mistakes, malware or damaged files; for that I need a separate backup on another device that keeps older versions.

## What did not work the first time

1. **Two of the new virtual disks would not attach.** In the VirtualBox settings window `Debian-OS_2.vdi` and `Debian-OS_3.vdi` gave the error `VBOX_E_OBJECT_IN_USE`: "medium is already associated with the current state of machine". `VBoxManage showvminfo` showed that they were not attached at all (only the system disk and `Debian-OS_1.vdi`), so VirtualBox was keeping a stale link. `VBoxManage storageattach` gave the same error. I did not try to repair the old files; I created two new 1 GB disks with `VBoxManage createmedium` (`lab04-d2.vdi`, `lab04-d3.vdi`) and attached them to SATA ports 2 and 3. After that `lsblk` showed `sdb`, `sdc`, `sdd`.
2. **The first `mdadm --create` was aborted.** The new `mdadm` asked two questions one after another (about a write-intent bitmap and then "Continue creating array?"). My answer went to the first question only and the second one got `mdadm: assuming no` and `create aborted`, so `/proc/mdstat` stayed empty (the partitions had been created already). I ran the command again as one line with `--run --bitmap=internal`, which answers both questions, and the array started.
3. **My first `array-rebuilt.txt` was taken too early.** `mdadm --detail` already said `clean` with two active devices, but `/proc/mdstat` in the same file still showed `recovery = 99.9%` and `[2/1] [U_]`. That is not the "rebuilt" state, so after the rebuild finished I saved the file again (`[2/2] [UU]`) and kept the early capture as `array-recovering.txt`.

## Evidence

- [evidence/lsblk-before.txt](evidence/lsblk-before.txt) — three empty 1 GB disks before any change.
- [evidence/array-healthy.txt](evidence/array-healthy.txt) — state 1: `[UU]`, clean, two active devices.
- [evidence/checksum-before.txt](evidence/checksum-before.txt) — SHA-256 of both files before the failure.
- [evidence/array-degraded.txt](evidence/array-degraded.txt) — state 2: `[U_]`, clean, degraded.
- [evidence/checksum-after-failure.txt](evidence/checksum-after-failure.txt) — the same SHA-256 sums after the failure (identical).
- [evidence/array-recovering.txt](evidence/array-recovering.txt) — the rebuild caught at 99.9%.
- [evidence/array-rebuilt.txt](evidence/array-rebuilt.txt) — state 3: `[UU]` again, with `sdd1`.
