# Lab 0 — Inventory your own machine

**Name:** Nurbolat Adilkhan  
**Date:** 19.10.2026  
**OS:** Windows 11  

## Inventory Table

| What | Value | Where I got it |
| :--- | :--- | :--- |
| **CPU Model** | AMD Ryzen 5 7520U with Radeon Graphics | `Get-CimInstance Win32_Processor \| Select-Object Name` |
| **CPU Cores** | 4 | `Get-CimInstance Win32_Processor \| Select-Object NumberOfCores` |
| **CPU Threads** | 8 | `Get-CimInstance Win32_Processor \| Select-Object NumberOfLogicalProcessors` |
| **Total RAM** | 16 GB | `(Get-CimInstance Win32_PhysicalMemory \| Measure-Object Capacity -Sum).Sum / 1GB` |
| **RAM Modules** | 2 modules (2x8 GB) | `Get-CimInstance Win32_PhysicalMemory \| Select-Object DeviceLocator, Capacity` |
| **RAM Speed** | 5500 MHz | `Get-CimInstance Win32_PhysicalMemory \| Select-Object Speed` |
| **Disk Model & Type** | SAMSUNG MZVL4512HBLU-00BTW (NVMe SSD) | `Get-PhysicalDisk \| Select-Object FriendlyName, MediaType, BusType` |
| **Volume Free Space** | 243.98 GB free on C: | `Get-Volume -DriveLetter C \| Select-Object SizeRemaining` |
| **Firmware Type** | UEFI | `Confirm-SecureBootUEFI` |
| **Firmware Version/Date** | LENOVO JQCN32WW, 16.08.2023 | `Get-CimInstance Win32_BIOS \| Select-Object SMBIOSBIOSVersion, ReleaseDate` |
| **Virtualization Enabled** | Enabled in Firmware | `(Get-CimInstance Win32_Processor).VirtualizationFirmwareEnabled` |

## What did not work

Initially, I relied exclusively on the `systeminfo` command. While it successfully provided host firmware and basic virtualization flags, it failed to disclose the breakdown of physical CPU cores versus logical threads, the individual RAM module count and bus speed, and the physical disk bus type (NVMe/SATA). Additionally, `systeminfo` does not report remaining volume storage space.

To resolve these gaps, I switched to targeted PowerShell CIM and Storage cmdlets:
- Used `Win32_Processor` to accurately differentiate between 4 physical cores and 8 logical processors.
- Used `Win32_PhysicalMemory` to inspect each populated DIMM slot, revealing two 8 GB modules operating at 5500 MHz.
- Used `Get-PhysicalDisk` and `Get-Volume` to distinguish physical NVMe media parameters from the 243.98 GB logical partition free space on drive C:.

Lastly, I corrected the repository directory structure by moving the lab contents from `repo_starter/` to `labs/lab00-inventory/` and exporting raw CLI outputs directly into `evidence/`.

## Evidence
- [evidence/cpu.txt](evidence/cpu.txt) — Raw PowerShell output for CPU specs, core count, and threads
- [evidence/memory.txt](evidence/memory.txt) — Raw output detailing memory capacity, module count, and clock speed
- [evidence/disk.txt](evidence/disk.txt) — Raw output showing physical disk specifications and volume free space
