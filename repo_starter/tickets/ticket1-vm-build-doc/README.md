# Ticket 1 — New developer starts Monday, needs a test machine

## 1. What was asked
> From: Aigerim T. (Office Manager)  
> Subject: test machine for new dev  
>  
> Hi, we have someone starting on Monday and he needs a test computer  
> set up. Nothing special, just so he can try things without breaking  
> anything. Can you write down how to do it so I can do the next one  
> myself? Thanks  

## 2. What was actually needed
The request from the Office Manager is written from a non-technical perspective and contains critical omissions, incorrect assumptions, and missing constraints required to provision an engineering test environment:

1. **What was left out:**
   - **Hypervisor platform & Licensing:** The request does not specify which virtualization software to use. In a commercial office, using enterprise software without checking the license model can cause legal compliance violations.
   - **Operating Systems & Versions:** "A test computer" lacks specifics on whether the developer requires Linux (server backend, microservices, containerization) or Windows (desktop client, GUI testing, Active Directory integration). Both are required for realistic dual-stack testing.
   - **Hardware boundaries:** The request gives no host memory, CPU thread, or disk capacity limits.
   - **Network isolation & connectivity:** The ticket does not specify whether the machine must reach the public internet (for `apt`, `pip`, `npm` package updates) or see other local test machines, nor does it address corporate network MAC address filtering.
   - **Failure recovery:** The core requirement — *"try things without breaking anything"* — completely omits the mechanism needed to deliver this guarantee: a clean base snapshot with a documented rollback procedure.

2. **Where the request is wrong:**
   - The assumption that a single generic PC is sufficient for development is inaccurate. Modern development pipelines frequently test client-server architectures across two distinct platforms (Linux server + Windows client).
   - "Nothing special" does not exist in virtualization. Without explicit limits, default VM provisioning can exhaust host memory, causing Windows 11 host crashes.

3. **Questions I would have asked the requester:**
   - What exact programming language, tools, and services will the new developer test?
   - What are the physical hardware specifications (RAM, CPU, free disk space) of the host laptop on which this environment will be deployed?
   - Do the test machines need to communicate with each other over a private virtual LAN, or only connect outbound to the internet?
   - Does our office Wi-Fi enforce MAC address authentication or 802.1X port security?

4. **Assumptions made instead of answers:**
   - **Target Audience:** The guide must be written for Aigerim, who has standard literacy and an internet browser but has never opened a hypervisor. Technical jargon must be explained through the *reason* for each click.
   - **Hypervisor:** Oracle VM VirtualBox 7.1 under the Personal Use and Evaluation Licence (PUEL) / Free Open Source core, requiring zero purchasing approvals.
   - **Guest OS Choices:** Ubuntu Server 24.04 LTS (industry-standard headless backend) and Windows 11 Enterprise Evaluation (free 90-day official developer evaluation ISO from the Microsoft Evaluation Center).
   - **Host Specs:** Mid-range workstation laptop (AMD Ryzen 5, 4 physical cores / 8 logical threads, 16 GB DDR4 RAM, 512 GB NVMe SSD).
   - **Networking:** A private shared virtual network (`NAT Network`, subnet `10.0.2.0/24`) that provides outbound internet access through host translation and allows guest-to-guest communication without creating unauthorized MAC addresses on office Wi-Fi.
   - **Safety:** Provision of an immutable `clean-install` baseline snapshot taken when powered down.

## 3. What I did

### Host Resource Calculation & Allocation
- **Host Baseline:** 16 GB physical RAM (~15.3 GB usable in Windows 11), 4 physical cores / 8 logical threads, ~150 GB free NVMe disk space.
- **Host Idle Consumption:** Windows 11 host OS plus background processes (browser, communication tools) consume ~5.5 GB RAM at steady idle.
- **Ubuntu Server Guest:** Allocated **2 vCPUs** (25% of logical threads), **2048 MB RAM** (12.5% of host RAM), and a **25 GB** dynamically allocated virtual disk (initial footprint ~2.5 GB).
- **Windows 11 Guest:** Allocated **2 vCPUs** (25% of logical threads), **4096 MB RAM** (25% of host RAM, the strict baseline required by Windows 11 installer), and a **40 GB** dynamically allocated virtual disk (initial footprint ~12 GB).
- **Host Headroom:** When running both virtual machines concurrently, guest allocations consume 6144 MB (6 GB) RAM. Added to the ~5.5 GB host idle footprint, total memory load is ~11.5 GB out of 16 GB. This leaves **~4.5 GB of free RAM headroom and 4 unassigned logical CPU threads**, completely preventing host swapping, thermal throttling, or hypervisor freezing.
- **Storage Protection:** Thin provisioning (dynamically allocated disks) means the combined initial disk usage on the host NVMe SSD is ~14.5 GB, avoiding reserving 65 GB upfront.

### Non-Default Settings Table
| Setting | Hypervisor Default | My Configuration | Why Differed from Default |
| :--- | :--- | :--- | :--- |
| **Network Mode** | Single NAT | **NAT Network** (`DevNet`, `10.0.2.0/24`) | Default NAT isolates each guest behind an independent private NAT router; VMs cannot ping or communicate with each other. NAT Network creates an internal virtual switch with outbound internet. |
| **Disk Provisioning** | Fixed Size (on some wizards) | **Dynamically Allocated** (Thin Provisioning) | Fixed size allocates the entire 25 GB/40 GB on physical disk immediately. Thin provisioning consumes host storage only as files are written. |
| **Windows Firmware** | Legacy BIOS | **EFI (Special OSes only) + Secure Boot** | Windows 11 setup aborts with *"This PC can't run Windows 11"* if modern UEFI and Secure Boot are not exposed by the hypervisor. |
| **Security Hardware** | None / Disabled | **Virtual TPM 2.0 Enabled** | Windows 11 requires a Trusted Platform Module 2.0 to boot and install. |
| **Windows Firewall** | Block Inbound ICMP | **Allowed ICMPv4 Echo Rule** | Windows Defender Firewall drops inbound ICMP echo requests by default, preventing the Linux guest from verifying network connectivity to Windows. |

### Problem Encountered and Resolution
- **Issue:** Pinging the Ubuntu guest from the Windows guest succeeded immediately (`reply from 10.0.2.3`), but pinging Windows (`10.0.2.4`) from Ubuntu resulted in 100% packet loss (`Request timed out`).
- **Investigation:** Both machines were verified to hold valid IP addresses within the same subnet on interface `DevNet` via `ip -br a` (Ubuntu: `10.0.2.3/24`) and `ipconfig` (Windows: `10.0.2.4/24`). Both guests successfully pinged public DNS (`1.1.1.1`). This confirmed that Layer 2 switching, Layer 3 routing, and the VirtualBox DHCP server were operating properly. The failure occurred at Layer 4/Host filtering: default Windows Defender Firewall profiles silently drop inbound ICMPv4 Echo requests.
- **Resolution:** Opened an elevated Command Prompt inside the Windows guest and ran:
  `netsh advfirewall firewall add rule name="Allow-ICMPv4-In" protocol=icmpv4:8,any dir=in action=allow`
  Immediately afterward, Ubuntu's `ping -c 4 10.0.2.4` returned 0% packet loss with sub-millisecond round-trip times.

## 4. Evidence
- [evidence/01-new-vm-wizard.png](evidence/01-new-vm-wizard.png) — Hypervisor VM creation wizard displaying baseline guest name, OS type, and RAM allocation.
- [evidence/02-disk-settings.png](evidence/02-disk-settings.png) — Storage configuration demonstrating thin-provisioned (dynamically allocated) VDI disks.
- [evidence/03-network-adapter.png](evidence/03-network-adapter.png) — Network adapter settings proving connection to the shared `NAT Network` (`DevNet`).
- [evidence/04-guest-uname.txt](evidence/04-guest-uname.txt) — CLI outputs confirming OS distribution, kernel version, and active IP leases for the Linux guest.
- [evidence/05-ping-between-guests.txt](evidence/05-ping-between-guests.txt) — Terminal logs proving outbound internet access (1.1.1.1) and bidirectional guest-to-guest ping connectivity.
- [evidence/06-snapshot-list.png](evidence/06-snapshot-list.png) — Hypervisor snapshot manager displaying the baseline `clean-install` snapshot.

## 5. What I would do differently
Instead of manually navigating the graphical user interface (GUI) of VirtualBox to configure memory, disks, and network adapters, I would write a declarative automation script using **Vagrant** or `VBoxManage` CLI commands. Manual GUI configuration is susceptible to human error (such as forgetting to enable TPM 2.0 or misconfiguring the NAT Network name). Providing Aigerim with a single automated script and a `Vagrantfile` would allow her to type `vagrant up` and deploy both fully configured, interconnected test VMs in minutes with zero manual mouse clicks.
