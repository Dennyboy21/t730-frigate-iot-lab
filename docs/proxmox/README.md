# HP t730 — Proxmox Build Documentation

**Reporting period:** October 22–23, 2026.  
**Build stage:** Proxmox host installed and updated; network-lab bridge not yet confirmed created.

This section records bringing a secondhand HP t730 Thin Client online as a virtualized security and home-server lab. The eventual proof of concept will include OPNsense, IoT segmentation tests, and optional self-hosted services. **This work did not migrate or segment any existing Nest/Google Home IoT devices.**

## Documentation

| File | What it covers |
|---|---|
| [01 — Hardware baseline](01-hardware-baseline.md) | Power supply issue, BIOS, RAM, SSD and Ethernet validation |
| [02 — UEFI installation](02-uefi-installation.md) | Failed USB boot attempt and successful Proxmox installation |
| [03 — Updates and troubleshooting](03-updates-and-recovery.md) | Enterprise Ceph 401 error, repository configuration, package upgrade and reboot |
| [04 — Management network and isolation design](04-management-network.md) | Observed `nic0`/`vmbr0`, management IP, planned `vmbr1` and safeguards |
| [05 — Build log and next steps](05-build-log-2026-10-22-23.md) | Chronological log, completed vs pending work |

## Evidence gallery

| Milestone | Screenshot |
|---|---|
| AMD virtualization enabled | ![BIOS virtualization enabled](../../screenshots/proxmox/01-bios-amd-virtualization-enabled.jpg) |
| Built-in and USB Ethernet validation | ![Network adapter link validation](../../screenshots/proxmox/02-network-adapter-validation.jpg) |
| Proxmox installation media started in UEFI | ![Proxmox installer menu](../../screenshots/proxmox/03-uefi-proxmox-installer.jpg) |
| Proxmox initial dashboard | ![Dashboard](../../screenshots/proxmox/04-first-dashboard.png) |
| Ceph repository update failure | ![Ceph repository error](../../screenshots/proxmox/05-ceph-repository-error.png) |
| Package database refresh succeeds | ![Update refresh success](../../screenshots/proxmox/06-repository-refresh-task-ok.png) |
| Full upgrade completed | ![Upgrade completed](../../screenshots/proxmox/07-upgrade-finished.png) |
| New kernel running | ![Kernel confirmed](../../screenshots/proxmox/08-kernel-after-reboot.png) |
| Management bridge and physical NIC | ![Proxmox bridge state](../../screenshots/proxmox/09-network-baseline.png) |

> Images were cropped/redacted for public documentation. Values visible in the screenshots represent a specific lab and are not instructions to apply those addresses to another network.
