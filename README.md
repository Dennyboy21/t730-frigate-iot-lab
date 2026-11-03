# HP t730 — Frigate & IoT Security Lab

A hands-on cybersecurity and virtualization proof of concept built on an **$80 refurbished HP t730 Thin Client**. The goal is to practice secure network design, firewall policy, access control, evidence-based troubleshooting, and backup/recovery before a future managed home-network and local NVR deployment.

**Status (November 3, 2026):** Proxmox running on the HP t730 with **16 GB upgraded RAM**; OPNsense and Debian VMs operational; lab-to-home firewall restrictions, administrator MFA and earlier baseline VM backups verified. **Frigate 0.18.2 now runs in Docker on Debian VM 102 with OpenVINO CPU detection: person and car labels are consistent on a looping test video.** A working VM snapshot and in-guest YAML copy were saved. **A physical camera, multi-camera performance, real camera/IoT VLANs, long-term recording and independent Frigate restore testing remain future work.**

## Hardware baseline

| Item | Baseline |
|---|---|
| Host | HP t730 Thin Client |
| CPU | AMD RX-427BB |
| Memory | 16 GB DDR3L (upgraded from 8 GB on October 26, 2026) |
| Storage | 128 GB M.2 SATA SSD |
| Platform | Proxmox VE 9.2.x |
| Initial investment | $80 |

## Lab architecture

```text
Existing household Internet and router
              |
   HP t730 / Proxmox VE
      /            \
 OPNsense VM     Debian 13 VM
 (WAN + LAN)     (isolated client)
      \____________/
        virtual lab LAN
```

OPNsense provides a routed and filtered downstream laboratory network. Management access remains on the upstream side. The household network was not migrated and its smart-home devices were not reconfigured during this phase.

## Completed milestones

- [x] Inspect refurbished hardware, resolve power-on issue, and verify virtualization support.
- [x] Install Proxmox VE, repair update repositories, upgrade, and reboot successfully.
- [x] Deploy OPNsense virtual firewall and Debian 13 virtual client.
- [x] Prove lab-to-firewall and lab-to-Internet reachability.
- [x] Verify logged firewall denials for protected household and private destinations.
- [x] Create baseline VM snapshots and configure monitored LVM-thin auto-extension.
- [x] Create successful VM backups, transfer copies outside Proxmox, and verify matching SHA-256 hashes.
- [x] Enroll administrator TOTP, verify positive and negative login tests, and enforce Web GUI MFA.
- [x] Upload updated OPNsense configuration backup to private Google Drive (encryption verification pending).
- [x] Upgrade HP t730 memory from 8 GB to 16 GB; verify 16 GB in BIOS, pass HP UEFI Memory Quick Check, and measure host memory availability increasing from 1.3 GiB to 9.0 GiB with both VMs running.
- [x] Revalidate Debian lab-to-gateway and Internet access, OPNsense administrator MFA, and logged firewall blocking of lab-to-home traffic after the hardware change.
- [x] Deploy Docker and Frigate 0.18.2 on Debian VM 102 with a separate 20 GB guest data disk.
- [x] Validate OpenVINO SSD-Lite MobileNet v2 CPU inference and consistently identify person and car in looping parking-lot footage.
- [x] Resolve excess background motion by disabling Improve Contrast while keeping motion threshold 30 and contour area 10.
- [x] Record working single-stream metrics (5.1 camera FPS, 4.7 detection FPS, 0 skipped FPS at sampling) and save a Frigate VM snapshot plus in-guest configuration copy.

## Documentation

| Area | Read |
|---|---|
| Hardware and host deployment | [Proxmox documentation](docs/proxmox/README.md) |
| Network design | [Network architecture and future roadmap](docs/network-design.md) |
| Firewall implementation and isolation tests | [OPNsense segmentation](docs/opnsense/01-virtual-firewall-and-segmentation.md) |
| MFA and management troubleshooting | [OPNsense MFA and HTTPS diagnosis](docs/opnsense/02-webgui-mfa-and-https-troubleshooting.md) |
| Snapshots, backups, and storage safeguards | [Backups and storage](docs/proxmox/06-snapshots-storage-and-backups.md) |
| October 26 hardware upgrade and post-change testing | [16 GB RAM upgrade and verification](docs/proxmox/07-ram-upgrade-2026-10-26.md) |
| Evidence publication safeguards | [Sanitization checklist](docs/security-publication-checklist.md) |
| November Frigate deployment, diagnosis and validation | [Frigate/OpenVINO case study](docs/frigate/README.md) |

Earlier dated build logs and proposed network designs are preserved as historical snapshots. Their older "pending" labels refer to the date they were originally authored, not this current project status.

## Remaining milestones

- [ ] Independently confirm configuration backup encryption and conduct an isolated restore drill.
- [ ] Expand firewall tests beyond ICMP (DNS and controlled TCP application cases).
- [x] Reassess host memory headroom after RAM upgrade.
- [ ] Reassess storage capacity and backup retention before enabling sustained Frigate recordings or additional cameras.
- [ ] Optional extended RAM stability test beyond HP's passed UEFI Memory Quick Check.
- [x] Deploy Frigate NVR with a looping prerecorded virtual camera and verify person/car detection.
- [ ] Attach an authorized live IP camera; validate stream handling, recording, retention and resource limits.
- [ ] Perform an independent Frigate guest/media backup and isolated restore test.
- [ ] Design and test dedicated camera and IoT segments with suitable VLAN-capable hardware.
- [ ] Evaluate a future UniFi deployment without disrupting household connectivity.

## What this lab demonstrates

Virtualization administration, least-privilege network policy, defense-in-depth, MFA deployment and validation, TCP/PF diagnostic methods, log interpretation, backup integrity assurance, **containerized NVR deployment, OpenVINO CPU inference diagnostics, motion-versus-object detection troubleshooting,** and documentation of test outcomes and unresolved findings.

## Security disclaimer

This is a personal educational laboratory. Example test cases are generalized. Real configuration exports, VM backup archives, authenticator seeds, passwords, session cookies, and sensitive system identifiers are deliberately **not** stored in the public repository.
