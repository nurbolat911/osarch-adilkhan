# Lab 6 — Live processes and memory pressure

Pair: Nurbolat Adilkhan / Solo · Date: 15.11.2026 · Guests: Debian GNU/Linux 13 "trixie" (`Debian-OS`, 1.9 GiB RAM, 2 vCPU, 2.1 GiB swap) and Windows 11 Enterprise evaluation (`Windows-OS`, 4 GB RAM, 2 vCPU), both in VirtualBox

## Part 1 — One process, three tools

**Process chosen: `ping`.** It exists on both guests (`PING.EXE` on Windows, `ping` on Debian), it needs no installation, and it runs for as long as I want (`ping -t 127.0.0.1` on Windows, `ping 127.0.0.1 > /dev/null &` on Debian). `VBoxService`, the first suggestion, is not running in my Debian guest (`pgrep -a VBoxService` printed nothing), so I used `ping` on both sides. I looked at both instances at about the same time.

Screenshots: [evidence/task-manager.png](evidence/task-manager.png), [evidence/process-explorer.png](evidence/process-explorer.png), [evidence/htop.png](evidence/htop.png). The Linux numbers come from `ps` and `/proc` ([evidence/part1-linux.txt](evidence/part1-linux.txt)).

| | Task Manager (Details) | Process Explorer | htop / ps (Debian) |
|---|---|---|---|
| Process | `PING.EXE` | `PING.EXE` | `ping 127.0.0.1` |
| PID | 3336 | 3336 | 1362 |
| Parent (name and PID) | — (not shown) | `cmd.exe` (8856) | `-bash` (1334) |
| "Memory" as shown by default | Memory (active private working set) **624 K** | Private Bytes **940 K** | RES **2800 KB** |
| Private memory | Memory (private working set) **624 K** | Private Bytes **940 K** | no such column (RES includes shared pages) |
| Working set / RES | Working set **5,464 K** | Working Set **5,480 K** | RES **2800 KB** (SHR 2636 KB) |
| Virtual size / commit | Commit size **892 K** | Private Bytes 940 K (committed private) | VIRT / VSZ **7912 KB** |
| Handles / file descriptors | Handles **82** | Handles **82** | **5** file descriptors |
| Threads | **3** | **5** | **1** (NLWP) |

The five file descriptors of the Linux `ping` (`sudo ls -l /proc/1362/fd`): `0` and `2` point to `/dev/pts/0`, `1` to `/dev/null`, `3` and `4` are sockets. In Process Explorer the handle pane of `PING.EXE` lists files (`\Device\ConDrv\…`, `ping.exe.mui`), registry keys, a mutant, semaphores and threads.

### Where the tools disagree

1. **The word "memory" means different numbers.** The same process has "Memory" 624 K in Task Manager (the active private working set), Private Bytes 940 K in Process Explorer (committed private memory), Commit size 892 K, a Working Set of about 5.4 MB, and in htop RES 2.8 MB and VIRT 7.9 MB. Each column measures something else: pages in RAM that belong only to the process, memory the process has asked the system to guarantee, pages in RAM including shared libraries, and the whole address space. VIRT in htop is not memory in use.
2. **Parent process.** Task Manager's Details tab has no parent column; Process Explorer shows it as the tree line above (`cmd.exe`, 8856) and htop/`ps` show it (`-bash`, 1334). Windows also has an extra helper, `conhost.exe`, under `cmd.exe`; Linux has nothing like it.
3. **Handles versus file descriptors.** Windows counts 82 handles for the same tiny program, because a handle can be a file, a registry key, a mutex, a semaphore or a thread. Linux counts only 5 file descriptors (files and sockets) in `/proc/<PID>/fd`.
4. **Thread count differs between the tools.** Task Manager said 3 threads and Process Explorer 5 for the same PID 3336, about a minute apart (4:02 PM and 4:04 PM in the VM): the thread count of a process changes, and each tool refreshes at its own moment.
5. **Names of the same thing.** Windows "Working set" is htop's RES; Windows "Commit size" is roughly Process Explorer's "Private Bytes"; htop has no column for either.

## Part 2 — Memory pressure

Guest: Debian, 1.9 GiB RAM, 2 vCPU, swap 2.1 GiB (`/dev/sda5`). Before starting I took the snapshot `before-lab6` on the powered-off guest (see "What did not work").

Steps (two SSH sessions from my host: A records, B puts the load):
```bash
# session A: record free -h every 3 seconds
cd ~/lab06
{ echo "=== BEFORE $(date) ==="; free -h; cat /proc/swaps; } > memory-pressure.txt
while true; do echo "=== $(date +%T) ==="; free -h; sleep 3; done | tee -a memory-pressure.txt

# session B: load, with swap on
stress-ng --vm 2 --vm-bytes 70% --timeout 60s
stress-ng --vm 2 --vm-bytes 90% --timeout 60s
stress-ng --vm 2 --vm-bytes 120% --timeout 90s

# session B: swap off, then 120% again
sudo swapoff -a
stress-ng --vm 2 --vm-bytes 120% --timeout 90s
sudo swapon -a

# session A: Ctrl+C, then the "after" record
{ echo "=== AFTER $(date) ==="; free -h; cat /proc/swaps; } >> memory-pressure.txt
```
All figures below are from [evidence/memory-pressure.txt](evidence/memory-pressure.txt).

| | Before | During (worst) | After |
|---|---|---|---|
| Time | 20:23:45 (first run) / 20:42:49 (OOM run) | 20:24:05 (swap run) / 20:43:19 (OOM run) | 20:46:32 |
| Used | 287 Mi / 233 Mi | 1.5 Gi / 1.5 Gi | 280 Mi |
| Available | 1.6 Gi / 1.7 Gi | 487 Mi / 445 Mi | 1.7 Gi |
| Swap used | 0B of 2.1 Gi | 91 Mi at 20:24:05, 357 Mi at 20:24:14 (swap on); 0B of 0B at 20:43:19 (swap off) | 0B of 2.1 Gi (swap on again) |

### What happened

- **Runs with swap on (20:23–20:25).** Total `available` dropped from 1.6 Gi to 487 Mi at 20:24:05, when `stress-ng` was filling memory, and swap started to grow: 91 Mi, 179 Mi, 268 Mi, 357 Mi in four samples three seconds apart (about 90 Mi every 3 s). At 20:24:18 swap fell to 15 Mi and then there is **no sample for 15 seconds** (20:24:18 to 20:24:33): the machine was frozen, my session B typed nothing and responded slowly. At 20:24:40 it was back (used 940 Mi, swap 424 Ki). `stress-ng` runs with a 60 s timeout took **2 min 42 s** (70%) and **2 min 41 s** (90%): the work was slowed about three times, which is thrashing (the system moves pages between RAM and swap all the time). The 120% run asked for 2.01 G with 1.67 G available, but the swap of 2.1 Gi absorbed the difference: it took 1 min 36 s, `passed: 2`, and **no process was killed**.
- **Run with swap off (20:42–20:45).** I disabled swap (`sudo swapoff -a`) and ran 120% again (2.03 G requested, 1.69 G available). At 20:42:53 `used` jumped to 1.4 Gi; from then on it swings between 0.9 and 1.5 Gi and `available` stays between 445 and 873 Mi, because the kernel kills workers and `stress-ng` starts new ones. Then the recording stops for 82 seconds (20:43:22 to 20:44:44): the system did not respond (the kernel log has a soft lockup of 76 s at 20:44:44). At 20:44:47 everything is back at `used` 280 Mi, `available` 1.7 Gi. The run lasted **1 min 52 s** against a 90 s timeout, and `stress-ng` still printed `passed: 2`, because it restarts killed workers.

### What the kernel did

The kernel log ([evidence/kernel-log-extract.txt](evidence/kernel-log-extract.txt), 69 lines, taken with `sudo journalctl -k --since "20:40:00" --until "20:46:00" | grep -E "invoked oom-killer|Out of memory: Killed process|oom_reaper|soft lockup"`) starts like this:

```
ноя 15 20:42:52 debian-os kernel: stress-ng-vm invoked oom-killer: gfp_mask=0x140cca(GFP_HIGHUSER_MOVABLE|__GFP_COMP), order=0, oom_score_adj=1000
ноя 15 20:42:52 debian-os kernel: Out of memory: Killed process 1917 (stress-ng-vm) total-vm:1122468kB, anon-rss:1068084kB, file-rss:56kB, shmem-rss:0kB, UID:1000 pgtables:2164kB oom_score_adj:1000
```

- **Which process and why:** the OOM killer killed process 1917, `stress-ng-vm`, which held about 1.07 GB of anonymous memory (`anon-rss:1068084kB`), the biggest user of memory. Its `oom_score_adj` is 1000, the highest value: `stress-ng` marks its own workers as the preferred victims.
- **When:** the first kill was at **20:42:52**, one second before my recording shows `used` jumping to 1.4 Gi (20:42:53). This matches `memory-pressure.txt`.
- **How often:** `grep -c "Out of memory: Killed process"` gives **33 kills** between 20:42:52 and 20:44:44: `stress-ng` started a new worker after each kill and the kernel killed it again. Not only `stress-ng-vm` triggered the OOM killer: `gmain`, `systemd-journal` and `sleep` also invoked it when they could not get a page. One line shows the cleanup, `oom_reaper: reaped process 1947 (stress-ng-vm)`.
- **The freeze:** `watchdog: BUG: soft lockup - CPU#0 stuck for 76s! [sshd-session:934]` at 20:44:44 explains why my SSH session and the recording stopped for 82 seconds.

### Windows

On the Windows guest I looked for the same class of event with:
```powershell
Get-WinEvent -FilterHashtable @{LogName='System'; ProviderName='Microsoft-Windows-Resource-Exhaustion-Detector'} -MaxEvents 5 | Format-List TimeCreated, Id, Message
```
Result: `Get-WinEvent : No events were found that match the specified selection criteria.` My Windows guest never ran low on memory: the status bar of Process Explorer showed Commit Charge 47.68–48.93% when I looked at it. I would expect an event with **ID 2004** from `Resource-Exhaustion-Detector` when the commit charge of the system is nearly used up. Windows does **not** kill a process like the Linux OOM killer: it warns, new memory allocations fail so the applications get errors, and event 2004 names the processes that used the most memory. I did not push the Windows guest to that point.

## What did not work the first time

1. **My first `htop` screenshot did not show `ping`.** The guest had restarted (htop showed `Uptime: 00:09:58`), so the earlier `ping` (PID 1102) was gone, there was also a stopped second `htop`, and the row of `ping` was not on the screen. I ran `pkill htop`, started `ping` again (PID 1362), saved the numbers again, filtered htop with F4 (`ping`), added the PPID column (F2) and widened the window so the command is visible.
2. **The live snapshot froze everything.** I took the snapshot with `VBoxManage snapshot Debian-OS take before-lab6 --live`; the guest and the SSH tab stopped responding for minutes, the VM window could not be closed, and I ended a task in Task Manager and restarted the PC. `snapshot list` then showed only `clean-install`, so the snapshot had not been created. I shut the guest down with `controlvm acpipowerbutton`, took the snapshot on the powered-off VM (`Snapshot taken`, 100% in seconds), and started the VM again.
3. **The load with swap did not trigger the OOM killer.** `stress-ng` at 70%, 90% and 120% only made the machine slow and `stress-ng` printed `passed: 2`; a journal search for `oom` found only an unrelated RCU warning at 20:19:20 ("OOM is now expected behavior", not a kill). I turned swap off with `sudo swapoff -a` and ran 120% again, and then the kernel started killing processes. Even then `stress-ng` said `passed: 2`, so the proof is the kernel log, not the output of the tool.
4. **The log file was in the wrong folder.** I ran the `journalctl … > kernel-log-extract.txt` command from my home directory, so `scp lab06/kernel-log-extract.txt` failed with "No such file or directory". I moved it to `~/lab06/` with `mv` and copied it again.

## Evidence

- [evidence/task-manager.png](evidence/task-manager.png) — Details tab, `PING.EXE` (PID 3336) with the memory, commit, handle and thread columns.
- [evidence/process-explorer.png](evidence/process-explorer.png) — the tree `cmd.exe` → `PING.EXE`, memory columns, and the handles pane.
- [evidence/htop.png](evidence/htop.png) — `ping 127.0.0.1` (PID 1362, PPID 1334, VIRT 7912, RES 2800, SHR 2636).
- [evidence/part1-linux.txt](evidence/part1-linux.txt) — `ps` and `/proc/1362/fd` of the Linux `ping` (PID, parent, RSS, VSZ, threads, five file descriptors).
- [evidence/memory-pressure.txt](evidence/memory-pressure.txt) — `free -h` before, every 3 seconds during, and after the load, each with the time.
- [evidence/kernel-log-extract.txt](evidence/kernel-log-extract.txt) — the OOM killer lines (33 kills) and the soft lockup from the kernel log.
