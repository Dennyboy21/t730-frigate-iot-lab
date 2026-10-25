# HP t730 — Frigate & IoT Security Lab

A hands-on cybersecurity and virtualization proof of concept built on an **$80 refurbished HP t730 Thin Client**. The goal is to practice secure network design, firewall policy, access control, evidence-based troubleshooting, and backup/recovery before a future managed home-network and local NVR deployment.

**Status (October 24, 2026):** Proxmox running; OPNsense and Debian test VMs operational; lab-to-home firewall restrictions validated; OPNsense administrator MFA enforced; baseline VM backups verified. **Frigate, cameras, physical IoT segmentation, and production UniFi migration remain future work.**

## Hardware baseline

| Item | Baseline |
|---|---|
| Host | HP t730 Thin Client |
| CPU | AMD RX-427BB |
| Memory | 8 GB DDR3L |
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

## Documentation

| Area | Read |
|---|---|
| Hardware and host deployment | [Proxmox documentation](docs/proxmox/README.md) |
| Network design | [Network architecture and future roadmap](docs/network-design.md) |
| Firewall implementation and isolation tests | [OPNsense segmentation](docs/opnsense/01-virtual-firewall-and-segmentation.md) |
| MFA and management troubleshooting | [OPNsense MFA and HTTPS diagnosis](docs/opnsense/02-webgui-mfa-and-https-troubleshooting.md) |
| Snapshots, backups, and storage safeguards | [Backups and storage](docs/proxmox/06-snapshots-storage-and-backups.md) |
| Evidence publication safeguards | [Sanitization checklist](docs/security-publication-checklist.md) |

Earlier dated build logs and proposed network designs are preserved as historical snapshots. Their older "pending" labels refer to the date they were originally authored, not this current project status.

## Remaining milestones

- [ ] Independently confirm configuration backup encryption and conduct an isolated restore drill.
- [ ] Expand firewall tests beyond ICMP (DNS and controlled TCP application cases).
- [ ] Assess Proxmox resource headroom, storage, and backup retention.
- [ ] Deploy Frigate NVR with a test camera and evaluate recording/object detection.
- [ ] Design and test dedicated camera and IoT segments with suitable VLAN-capable hardware.
- [ ] Evaluate a future UniFi deployment without disrupting household connectivity.

## What this lab demonstrates

Virtualization administration, least-privilege network policy, defense-in-depth, MFA deployment and validation, TCP/PF diagnostic methods, log interpretation, backup integrity assurance, and documentation of test outcomes and unresolved findings.

## Security disclaimer

This is a personal educational laboratory. Example test cases are generalized. Real configuration exports, VM backup archives, authenticator seeds, passwords, session cookies, and sensitive system identifiers are deliberately **not** stored in the public repository.
