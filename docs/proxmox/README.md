# HP t730 — Proxmox Build Documentation

**Coverage:** October 22–26, 2026. Proxmox installed and updated; OPNsense and Debian VMs deployed; host RAM upgraded from 8 GB to 16 GB.

This section records the refurbished HP t730 setup, recovery from first-boot issues, virtualization networking, snapshots, backups, thin-pool safeguards, and the verified 16 GB memory upgrade.

**The household Nest network has not been migrated or segmented.** All segmentation testing documented to date occurred in the downstream virtual lab.

## Documents

| File | Content |
|---|---|
| [01 — Hardware baseline](01-hardware-baseline.md) | Power supply issue, BIOS, RAM, SSD and Ethernet validation |
| [02 — UEFI installation](02-uefi-installation.md) | USB boot troubleshooting and successful Proxmox installation |
| [03 — Updates and recovery](03-updates-and-recovery.md) | Ceph enterprise repository error, upgrade and reboot |
| [04 — Original network plan](04-management-network.md) | Management baseline and then-proposed internal lab bridge |
| [05 — Original build log](05-build-log-2026-10-22-23.md) | Chronological October 22–23 build history |
| [06 — Snapshots, storage and backups](06-snapshots-storage-and-backups.md) | October 24 safeguards, recovery artifacts, SHA-256 verification |
| [07 — 16 GB RAM upgrade and validation](07-ram-upgrade-2026-10-26.md) | October 26 physical upgrade, HP quick memory test, performance comparison, MFA, and logged firewall isolation verification |

**Implementation after the original planning documents:** [OPNsense virtual segmentation results](../opnsense/01-virtual-firewall-and-segmentation.md) and [MFA/HTTPS troubleshooting](../opnsense/02-webgui-mfa-and-https-troubleshooting.md).

## Earlier evidence gallery (previously sanitized)

| Milestone | Screenshot |
|---|---|
| AMD virtualization enabled | ![BIOS virtualization enabled](../../screenshots/proxmox/01-bios-amd-virtualization-enabled.jpg) |
| Ethernet adapter validation | ![Network adapter link validation](../../screenshots/proxmox/02-network-adapter-validation.jpg) |
| Proxmox installer in UEFI | ![Installer](../../screenshots/proxmox/03-uefi-proxmox-installer.jpg) |
| Proxmox initial dashboard | ![Dashboard](../../screenshots/proxmox/04-first-dashboard.png) |
| Ceph repository issue | ![Repository error](../../screenshots/proxmox/05-ceph-repository-error.png) |
| Clean repository refresh | ![Update refresh](../../screenshots/proxmox/06-repository-refresh-task-ok.png) |
| Upgrade completed | ![Upgrade](../../screenshots/proxmox/07-upgrade-finished.png) |
| Kernel verification | ![Kernel](../../screenshots/proxmox/08-kernel-after-reboot.png) |
| Initial host management bridge | ![Bridge](../../screenshots/proxmox/09-network-baseline.png) |

No additional raw October 24 console/browser screenshots were published: they require individual cropping and secret/session review before they can safely be shared.
