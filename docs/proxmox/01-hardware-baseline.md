# 01 — HP t730 Hardware and Reliability Baseline

**Evidence date:** October 22, 2026; some initial inspection photos and earlier acquisition work predate this check.

## Purpose

Establish that the refurbished HP t730 hardware is suitable for a limited Proxmox/OPNsense proof of concept **before overwriting the prior Windows installation**.

## Inventory verified

| Component | Observed | Verification |
|---|---|---|
| Platform | HP t730 Thin Client | HP BIOS System Information |
| CPU | AMD RX-427BB with Radeon R7 Graphics, 2.7 GHz | BIOS; later Proxmox Summary |
| Memory | 8 GB DDR3-1600 dual channel, 2 × 4 GB | BIOS; ~6.71 GiB available in Proxmox after reservations |
| SSD | Lite-On CV8-8E128-HP, marketed 128 GB | Windows disk tools; Linux installer showed ~119.24 GiB |
| Onboard Ethernet | Realtek PCIe GbE Family Controller | Windows `Get-NetAdapter` and Proxmox `nic0` (`r8169`) |
| Secondary Ethernet | TP-Link UE306 using ASIX AX88179 chipset | Windows detected USB 3.0 Gigabit Ethernet adapter |
| BIOS | HP L43 v01.14 | BIOS System Information |

## Initial power-supply fault and recovery

- **Symptom:** Four repeating red power-button flashes with an incompatible **65 W** adapter; system did not start normally.
- **Action:** Seller arranged a replacement adapter with an appropriate connector/power capability. The exact new adapter's electrical rating was **not photographed/confirmed** in the evidence set.
- **Result:** t730 reached HP Setup Utility and subsequently booted the preexisting Windows 11 installation.
- **Conclusion:** Replacing the inadequate supply resolved the observed startup symptom. The evidence does not establish a full PSU load/long-duration stress test.

## BIOS preparation

- Located **Security → System Security → Virtualization Technology**.
- Initial setting: **Disable**.
- Changed to **Enable**, saved, restarted, and re-entered BIOS.
- Verification screenshot confirmed **Enable** persisted.
- Left data execution prevention and TPM enabled; did **not** reset TPM or clear Secure Boot keys.

![AMD virtualization enabled](../../screenshots/proxmox/01-bios-amd-virtualization-enabled.jpg)

## SSD checks before installation

PowerShell commands used on the original Windows install:

```powershell
Get-PhysicalDisk | Select-Object FriendlyName, MediaType, Size, HealthStatus
Get-Disk | Select-Object Number, FriendlyName, PartitionStyle, Size
Get-PhysicalDisk | Get-StorageReliabilityCounter | Format-List DeviceId,Temperature,Wear,PowerOnHours,ReadErrorsTotal,WriteErrorsTotal
```

| Reported parameter | Observed |
|---|---|
| Windows HealthStatus | `Healthy` |
| Size | 128,035,676,160 bytes (~119.24 GiB) |
| Physical disk number | 0 |
| Original partition style | GPT |
| Temperature during check | 44 °C |
| PowerOnHours | 7,268 |
| Wear | 0 (driver/firmware interpretation uncertain) |
| ReadErrorsTotal | 0 |
| WriteErrorsTotal | Blank / not exposed; **not** a confirmed zero |

**Limitation:** Windows storage-health reporting isn't equivalent to a complete SMART/media scan. Important data and virtual-machine configurations should be backed up independently.

## Ethernet link validation (isolated switch)

The existing Windows installation was **not connected to the live household network** for these tests. A powered, standalone Gigabit switch was used to provide link negotiation.

```powershell
Get-NetAdapter | Format-Table Name, InterfaceDescription, Status, LinkSpeed
```

1. Realtek onboard adapter: **Up / 1 Gbps** with isolated switch connection.
2. Ethernet cable moved to TP-Link UE306: ASIX AX88179 **Up / 1 Gbps**; Realtek correctly became disconnected.

**Scope:** Physical-layer link/driver recognition passed in Windows. This does **not** prove sustained 1 Gbps transfer speed, Linux/Proxmox USB adapter support, or correct packet filtering.

![Ethernet link test](../../screenshots/proxmox/02-network-adapter-validation.jpg)

## Outcome

**Passed:** Power-on, BIOS access, AMD virtualization setting, normal Windows boot prior to reinstallation, SSD detection/basic health, and initial link tests for both interfaces.

**Not yet tested:** SSD long-term reliability, Proxmox support of UE306, heat under sustained VM load, and performance of the physical camera/IoT test network.
