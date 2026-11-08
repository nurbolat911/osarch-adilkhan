Lab 3 — Teardown, and the same machine from the command line
Pair: Nurbolat Adilkhan / Solo Driver first half: Nurbolat Adilkhan Machine: Laptop (Lenovo), AMD Ryzen 5 7520U, 16 GB, Samsung NVMe SSD. Windows 11 and Ubuntu on the same laptop (dual boot). Both operating systems ran on the real hardware, no virtual machine was used in this lab. Date: 08.11.2026

Part 1 — Ten parts
For every part I used the maker's own page. Each caption has three answers: what it is, what it connects to, what limits it. Values that the maker's page did not show are marked as such.

1. AMD Ryzen 5 7600
AMD Ryzen 5 7600 product page AMD Ryzen 5 7600 specifications

What it is: desktop processor, Zen 4, 6 cores / 12 threads, base 3.8 GHz, boost up to 5.1 GHz, 6 MB L2 + 32 MB L3 cache, default TDP 65 W, boxed with a Wraith Stealth cooler. Connects to: socket AM5. Memory controller: DDR5, 2 channels, UDIMM, up to 128 GB. Works with the chipsets A620, X670E, X670, B650E, B650, X870E, X870, B840, B850. PCI Express 5.0. Limit: only AM5 boards, only DDR5 (no DDR4). AMD lists the maximum memory speed as DDR5-5200 with two modules and DDR5-3600 with four modules. Source: https://www.amd.com/en/products/processors/desktops/ryzen/7000-series/amd-ryzen-5-7600.html

2. Intel Core i5-13400
Intel Core i5-13400 product page i5-13400 CPU specifications i5-13400 memory specifications i5-13400 package specifications

What it is: desktop processor (13th generation), 10 cores (6 Performance + 4 Efficient), 16 threads. P-cores 2.50 GHz base / 4.60 GHz turbo, E-cores 1.80 / 3.30 GHz, 20 MB Intel Smart Cache. Connects to: socket FCLGA1700 (LGA 1700). Memory: either DDR5 up to 4800 MT/s or DDR4 up to 3200 MT/s, 2 channels, up to 192 GB, 76.8 GB/s bandwidth. Thermal solution specification PCG 2020C, Tjunction 100 °C. Limit: only LGA 1700 boards. The type of memory is decided by the motherboard: one board takes DDR4 or DDR5, never both. The page does not say whether a cooler is in the box. Source: https://www.intel.com/content/www/us/en/products/sku/230495/intel-core-i513400-processor-20m-cache-up-to-4-60-ghz/specifications.html

3. ASUS PRIME B650M-A
ASUS PRIME B650M-A overview ASUS PRIME B650M-A tech specs: CPU and chipset ASUS PRIME B650M-A tech specs: memory and storage ASUS PRIME B650M-A CPU support list

What it is: Micro-ATX motherboard, chipset AMD B650. Socket: AM5, for AMD Ryzen 9000, 8000 and 7000 Series, up to 170 W CPU. Memory: 4 × DIMM, DDR5 4800 to 5600 MT/s (5400 and 5600 as overclock), ECC and non-ECC unbuffered, dual channel, AMD EXPO. Storage: 2 × M.2 (Key M, 2242/2260/2280): M.2_1 runs PCIe 5.0 x4 with Ryzen 9000/7000 (PCIe 4.0 x4 with Ryzen 8000), M.2_2 runs PCIe 4.0 x4 from the chipset. 4 × SATA 6 Gb/s. Video outputs: 1 × DisplayPort, 1 × HDMI, 1 × VGA (they work only with a processor that has integrated graphics). CPU generations accepted: Ryzen 7000, 8000 and 9000 desktop. In ASUS's CPU support list, searching for "7600" shows Ryzen 5 7600 (3.8 GHz, 65 W, 32 MB L3, 6 cores, stepping B2), 7600X and 7600X3D, all "validated since BIOS 0602". So my Ryzen 5 7600 is on the list; whether the board in a shop already has BIOS 0602 or newer is something I cannot see from the page. Limit: only AM5 processors and only DDR5. Source: https://www.asus.com/motherboards-components/motherboards/prime/prime-b650m-a/ · Tech Specs: https://www.asus.com/motherboards-components/motherboards/prime/prime-b650m-a/techspec/ · CPU support: https://www.asus.com/motherboards-components/motherboards/prime/prime-b650m-a/helpdesk_qvl_cpu?model2Name=PRIME-B650M-A

4. MSI PRO B760M-A WIFI DDR4
MSI PRO B760M-A WIFI DDR4 overview MSI specification: chipset and CPU MSI specification: memory and storage MSI CPU compatibility list

What it is: Micro-ATX motherboard (the "M" in the name; the form factor row is not in my screenshots), chipset Intel B760. Socket: LGA 1700, for Intel Core 14th / 13th / 12th Gen, Pentium Gold and Celeron. Memory: 4 × DDR4, up to 128 GB, 3200 MHz (JEDEC), higher speeds only as overclock (up to 5333+), dual channel, non-ECC unbuffered, Intel XMP. Storage: 2 × M.2 (M.2_1 from the CPU, PCIe 4.0 x4; M.2_2 from the chipset, PCIe 4.0 x4 or SATA mode), 4 × SATA 6G. MSI notes that one SATA port is disabled if an M.2 SATA SSD is installed in the M2_2 slot. Video outputs: 2 × HDMI and 2 × DisplayPort, only with a CPU that has integrated graphics. CPU support: in MSI's compatibility list, searching "13400" shows i5-13400 (Raptor Lake, 2.5 GHz, 65 W, 20 MB L3, steppings B0 and C0, UHD Graphics 730), as well as i5-13400F and i5-13400T. Limit: only LGA 1700 processors and only DDR4. Source: https://www.msi.com/Motherboard/PRO-B760M-A-WIFI-DDR4 · Specification: https://www.msi.com/Motherboard/PRO-B760M-A-WIFI-DDR4/Specification · CPU support: https://www.msi.com/Motherboard/PRO-B760M-A-WIFI-DDR4/support#cpu

5. Kingston FURY Beast DDR5-5600, 16 GB
Kingston FURY Beast DDR5 product page Kingston KF556C40BB-16 datasheet

What it is: DDR5 DIMM, 288 pins, 16 GB single module (1Rx8), DDR5-5600 CL40, 1.1 V. The module has an Intel XMP 3.0 profile: 5600 MT/s at 40-40-40 and 1.25 V. Without a profile it starts at the JEDEC speed of DDR5-4800 CL40-39-39. Has a heat spreader, height 34.9 mm. Connects to: a DDR5 DIMM slot, one module per channel for dual channel. Limit: fits only DDR5 boards. This is the Intel XMP version, so on an AMD board the 5600 profile may need to be loaded manually. AMD's own page for the Ryzen 5 7600 gives DDR5-5200 as the highest speed. Source: https://www.kingston.com/en/memory/gaming/kingston-fury-beast-ddr5-memory · Datasheet: https://www.kingston.com/datasheets/KF556C40BB-16.pdf

6. Crucial DDR4-3200, 16 GB DIMM
Crucial page: error 429

What I could not do: the maker's page returned "Error 429 - Too Many Requests" and "The requested URL was rejected" in my browser every time I opened it (more in "What did not work"). So I do not have a spec table from Crucial. What I can say from the task and from the DDR4 standard: the part is a 16 GB DDR4-3200 desktop DIMM (task statement: "Crucial DDR4-3200, 16 GB DIMM"). DDR4 desktop DIMMs have 288 pins and a different key notch from DDR5 (general knowledge, not read from the Crucial page). Connects to: a DDR4 DIMM slot. It matches the MSI board, whose JEDEC speed is 3200, and the i5-13400, whose maximum DDR4 speed is 3200 MT/s. Limit: only DDR4 boards. Source (page that was blocked): https://www.crucial.com/memory/ddr4/ct16g4dfra32a

7. Samsung 990 PRO 1 TB
Samsung 990 PRO product page Samsung 990 PRO specifications

What it is: NVMe SSD, model MZ-V9P1T0BW, 1,000 GB, form factor M.2 (2280), interface PCIe Gen 4.0 x4, NVMe 2.0, sequential read up to 7,450 MB/s and write up to 6,900 MB/s, Samsung V-NAND TLC, in-house controller, 1 GB LPDDR4 cache, 80 × 22 × 2.3 mm, 9 g, 5-year or 600 TBW warranty. Connects to: an M.2 slot with M key (directly to the CPU or chipset PCIe lanes, no cable). Limit: needs an M.2 NVMe slot. The speed is capped by the slot: on the ASUS board M.2_1 is PCIe 5.0 but the drive is Gen 4, so it works at Gen 4. Source: https://www.samsung.com/in/memory-storage/nvme-ssd/990-pro-1tb-nvme-pcie-gen-4-mz-v9p1t0bw/

8. Samsung 870 EVO 1 TB
Samsung 870 EVO product page Samsung 870 EVO specifications

What it is: SATA SSD, model MZ-77E1T0BW, 1,000 GB, form factor 2.5 inch (100 × 69.85 × 6.8 mm), SATA 6 Gb/s (compatible with SATA 3 Gb/s and 1.5 Gb/s), sequential read up to 560 MB/s and write up to 530 MB/s, Samsung V-NAND 3-bit MLC, MKX controller, 1 GB LPDDR4 cache, 45 g, 5-year or 600 TBW warranty. Connects to: a SATA port on the board with a SATA data cable, and a SATA power connector from the power supply (two cables, unlike M.2). Limit: the SATA bus: about 550 MB/s at most, much slower than an NVMe drive. Source: https://www.samsung.com/sg/memory-storage/sata-ssd/870-evo-1tb-sata-3-2-5-ssd-mz-77e1t0bw/

9. Corsair RM750e (750 W)
Corsair RM750e product page Corsair RM750e tech specs

What it is: ATX power supply, 750 W continuous, 80 PLUS Gold (Cybenetics Gold, noise rating A-), ATX12V 3.1 (ATX 3.1 and PCIe 5.1), fully modular with type 4 cables, Zero RPM fan mode, 140 × 150 × 86 mm, 7-year warranty. Connectors listed: 1 ATX, 7 SATA, 4 PATA. Connects to: the motherboard (ATX connector), the drives (SATA power) and, through the other cables, the processor and graphics card. The Tech Specs table in my screenshot lists only the connector counts above; the product page said "out of stock" when I looked. Limit: total wattage (750 W) and the number of cables it comes with. There is only one in my list. Source: https://www.corsair.com/us/en/p/psu/cp-9020262-na/rme-series-rm750e-fully-modular-low-noise-atx-power-supply-cp-9020262-na

10. Stock cooler: AMD Wraith Stealth
AMD cooling solutions page

Which processor ships with one: AMD's page "Ryzen Processor Cooling Solutions" lists the AMD Wraith Stealth Cooler (no LED) as the bundled thermal solution for a list of processors that includes Ryzen 5 7600; the Ryzen 5 7600 page itself says "Thermal Solution (PIB): AMD Wraith Stealth". For the Intel Core i5-13400 the specification page only gives "Thermal Solution Specification: PCG 2020C" and does not say that a cooler is in the box, so I do not count on one. What it is: a low-profile fan cooler without LED, bundled in the processor box. Connects to: the AM5 processor socket on the board (it is part of the Ryzen 5 7600 package) and to a fan header on the board. Limit: it is designed for the 65 W class of processors it is bundled with; the page does not give a cooling capacity in watts. Source: https://www.amd.com/en/products/processors/ecosystem/cpu-cooler-solutions.html

Two builds
I decided by facts: the processors need different sockets (AM5 and LGA 1700), so the boards are fixed. The memory then follows the board: the AMD board and the Ryzen 5 7600 accept only DDR5, the MSI board accepts only DDR4. The two SSDs fit both boards (both have M.2 and SATA), so I paired the fast NVMe drive with the newer AMD platform and the SATA drive with the Intel/DDR4 one. The other way round would also work.

Build 1 — AMD
Part	Model	Why it fits here
Processor	AMD Ryzen 5 7600	Socket AM5
Motherboard	ASUS PRIME B650M-A	AM5 socket, B650 is in AMD's list of chipsets for the 7600, ASUS lists the 7600 (validated since BIOS 0602)
Memory	Kingston FURY Beast DDR5-5600, 16 GB	The 7600 and the board accept only DDR5; board has 4 DDR5 slots
SSD	Samsung 990 PRO 1 TB	NVMe M.2 2280 fits the M.2_1 slot (PCIe 5.0 x4 slot, the drive uses it at PCIe 4.0)
Cooler	AMD Wraith Stealth (stock)	It ships in the Ryzen 5 7600 box and fits socket AM5
This build still needs: a power supply (the only one in my list went to build 2), a case, a keyboard, a monitor and an operating system. Graphics: the board has video outputs, but they work only if the processor has integrated graphics. I did not capture the "Graphics Capabilities" part of the AMD page, so I have to check it before I rely on it. The Kingston module is a single stick, so the board runs it in single channel; a second module would be needed for dual channel.

Build 2 — Intel
Part	Model	Why it fits here
Processor	Intel Core i5-13400	Socket LGA 1700
Motherboard	MSI PRO B760M-A WIFI DDR4	LGA 1700, B760; MSI lists the i5-13400 (steppings B0 and C0)
Memory	Crucial DDR4-3200, 16 GB	The board has 4 DDR4 slots (3200 JEDEC); the i5-13400 supports DDR4 up to 3200 MT/s
SSD	Samsung 870 EVO 1 TB	SATA 6 Gb/s; the board has 4 SATA ports (SATA data cable and power cable are needed)
Power supply	Corsair RM750e (750 W)	There is only one power supply in the list, and the AMD build already has its cooler, so I put it here; 750 W, modular, ATX 3.1
This build still needs: a cooler (the Intel page does not say there is one in the box), a case, a keyboard, a monitor and an operating system, and a SATA data cable for the 870 EVO. The processor has Intel UHD Graphics 730 according to MSI's list, so the board's HDMI/DisplayPort outputs can be used without a graphics card. A second memory module would be needed for dual channel.

Is there enough of everything for two computers? No. In the ten parts there is only one power supply, only one cooler (from the AMD processor box), no case, no cables for the SATA drive, no operating system and no input devices. So both builds are incomplete until those are added.

Part 2 — Same facts, two operating systems
Both columns were measured on the same laptop. Linux is Ubuntu from my dual boot, on the real hardware, not in a virtual machine. I did not have a Mac, so there is no macOS column.

Fact	Windows (PowerShell)	Linux (Ubuntu)
CPU model	Get-CimInstance Win32_Processor | Select Name → AMD Ryzen 5 7520U with Radeon Graphics	lscpu → "Имя модели": AMD Ryzen 5 7520U with Radeon Graphics
Cores / threads	... NumberOfCores, NumberOfLogicalProcessors → 4 / 8	lscpu → "Ядер на сокет": 4, "Потоков на ядро": 2, CPU(s): 8
Clock speed	MaxClockSpeed → 2801 MHz	lscpu → CPU max MHz 4386, min 423
Total memory	Get-CimInstance Win32_PhysicalMemory → 2 × 8,589,934,592 B = 16 GiB	free -h → 14 GiB total (14Gi)
Memory modules and speed	Win32_PhysicalMemory → 2 devices, Speed 5500, ConfiguredClockSpeed 5500, SMBIOSMemoryType 26, FormFactor 1	sudo dmidecode -t memory → 2 devices (P0 CHANNEL A and B), 8 GB each, 5500 MT/s, "Type: DDR4", "Form Factor: Other", 0.5 V
Disk model and type	Get-PhysicalDisk → SAMSUNG MZVL4512HBLU-00BTW, SSD, NVMe, 512,110,190,592 B	lsblk -d -o NAME,MODEL,ROTA,TRAN,SIZE → nvme0n1, same model, ROTA 0, TRAN nvme, 476,9G
Free space	Get-Volume C → 221,199,261,696 B free of 405,099,507,712 B (about 206 GiB of 377 GiB)	df -h / → 58G free of 96G (root partition nvme0n1p5)
Architecture	$env:PROCESSOR_ARCHITECTURE → AMD64	uname -m → x86_64
How the names differ. The same facts have different names and units. Windows says NumberOfLogicalProcessors, Linux says CPU(s); Windows prints sizes in bytes, Linux in human units (GiB); Windows names the architecture AMD64, Linux names it x86_64 (the same 64-bit x86 architecture). My Linux is in Russian, so lscpu printed the labels in Russian. Two numbers differ for a real reason: MaxClockSpeed is 2801 MHz in Windows but CPU max MHz is 4386 in Linux (they look like different values: 2801 MHz looks like the base clock, 4386 MHz is the boost maximum; I did not check which clock Windows reports in this field), and the free space differs because the Windows C: volume and the Ubuntu root partition are two different partitions on the same NVMe disk (the disk size is the same: 512,110,190,592 B = 476.9 GiB, which lsblk shows as 476,9G). Linux sees 14 GiB of memory against the 16 GiB that Windows adds up; I did not check why. A possible reason is that part of the memory is taken by the integrated graphics, but I have not verified this.

What the memory output showed (and why I do not trust the label). Both operating systems read the same firmware table (SMBIOS), and both say the type is DDR4 (SMBIOSMemoryType 26 in Windows, "Type: DDR4" in dmidecode). But the same output gives 5500 MT/s and a configured voltage of 0.5 V, which are not possible for DDR4 (DDR4 is 1.2 V and does not reach 5500). The two devices are called DIMM 0 with form factor "Other" and sit on separate channels (A and B), and the part number is MT62F2G32D4DS-026 WT. My conclusion is that the memory is soldered onto the board (not two removable sticks) and is probably a low-power LPDDR5 type, with the firmware filling in "DDR4" wrongly. I have not checked the part number on the manufacturer's page, so this is my interpretation, not a confirmed fact.

The same output also shows HealthStatus: Warning and OperationalStatus: Scan Needed for the Windows C: volume; I have not run a disk check yet.

What did not work the first time
Crucial's page would not open. I opened https://www.crucial.com/memory/ddr4/ct16g4dfra32a and got the plain error page "The requested URL was rejected" with "Error 429 - Too Many Requests" (support ID 6f13c84b-4d27-4fd4-ac79-5fa74ba0cb5f, F5 site fr4-fra). A 429 means the site refused me for sending too many requests, and the rejection comes from the site's protection, not from my network settings. I tried again later and the page was still rejected. I did not use a shop page instead, because the lab says facts copied from a shop page are not evidence. I saved a screenshot of the error and wrote the Crucial caption only with what I can support (see part 6).
A confusing memory type. I expected dmidecode to confirm the memory type, but it printed "Type: DDR4" next to 5500 MT/s and 0.5 V. This did not fit together, so I compared it with the Windows output (same code 26) and with the part number and the form factor. It turned out to be a label in the firmware table that cannot be trusted. I describe this above in part 2.
Screenshots I had to redo. My first AMD screenshot was a whole page shrunk into one image, so the text was too small to read. I took it again as two normal windows with the specification rows in view. While checking this folder I also found that the two files named samsung-870-evo*.png actually contained the Samsung 990 PRO pages. I renamed them to samsung-990-pro*.png and took the real 870 EVO pages.
Evidence
evidence/photos/ryzen5-7600.png, ryzen5-7600-specs.png — AMD's page: socket AM5, DDR5, 65 W, Wraith Stealth cooler.
evidence/photos/i5-13400.png, i5-13400-cpu-specs.png, i5-13400-memory.png, i5-13400-socket.png — Intel's page: cores, DDR4 and DDR5, socket FCLGA1700.
evidence/photos/asus-b650m-a.png, asus-b650m-a-techspec-cpu-chipset.png, asus-b650m-a-techspec-memory-storage.png, asus-b650m-a-cpu-support.png — ASUS board: AM5, B650, DDR5 slots, M.2 and SATA, CPU support list with the 7600.
evidence/photos/msi-b760m-a-wifi-ddr4.png, msi-specs-cpu-chipset.png, msi-specs-memory-storage.png, msi-cpu-support.png — MSI board: LGA 1700, B760, DDR4 slots, M.2 and SATA, compatibility list with the i5-13400.
evidence/photos/kingston-fury-beast-ddr5.png, kingston-fury-beast-ddr5-datasheet.png — Kingston product page and datasheet: DDR5, 288 pins, 5600 CL40.
evidence/photos/crucial-error-429.png — proof that the Crucial page was rejected.
evidence/photos/samsung-990-pro.png, samsung-990-pro-specs.png — M.2 2280, PCIe 4.0 x4.
evidence/photos/samsung-870-evo.png, samsung-870-evo-specs.png — 2.5 inch, SATA 6 Gb/s.
evidence/photos/corsair-rm750e.png, corsair-rm750e-specs.png — 750 W, 80 PLUS Gold, ATX 3.1, fully modular.
evidence/photos/stock-cooler-wraith-stealth.png — AMD's cooler page: Wraith Stealth is bundled with the Ryzen 5 7600.
evidence/windows-specs.txt — raw PowerShell output, each command above its output (real hardware).
evidence/linux-specs.txt — raw output of the six commands from my Ubuntu terminal (real hardware, not a VM). The file is the whole terminal session, so it also contains the prompt lines.
