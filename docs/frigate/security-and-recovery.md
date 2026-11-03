# Security, Backup and Recovery Notes

## Security boundaries implemented

- Frigate runs in **Debian VM 102**, on the downstream virtual lab network rather than as a service directly on the household router.
- OPNsense VM 100 provides the lab's upstream/isolated interface boundary.
- Administrative access to Frigate's authenticated HTTPS interface on port **8971** was enabled through a **source-restricted** lab-side OPNsense NAT rule.
- This is not evidence that Frigate was exposed to the public Internet; there was no deliberate household-router Internet port forward for this experiment.
- The household network, existing smart-home devices and future camera VLANs were **not** migrated as part of this milestone.
- Network isolation results from earlier OPNsense tests are documented in [OPNsense segmentation](../opnsense/01-virtual-firewall-and-segmentation.md). No claim is made here that all possible application-layer or camera-network flows were tested.

## Saved recovery points

1. **Proxmox snapshot** of VM 102 after successful Frigate/OpenVINO person/car detection (named `frigate-openvino-working-detection` in the lab).
2. **Separate guest configuration-file copy** saved as `config.yml.working-baseline` adjacent to the active Frigate YAML file.
3. Earlier project VM backup-and-SHA-256 procedures are documented in [snapshots, storage and backups](../proxmox/06-snapshots-storage-and-backups.md); those earlier successful backups are **not** evidence of an up-to-date Frigate VM/media backup.

The in-guest config copy guards against an accidental YAML edit but is **not an off-host backup**. A VM snapshot is a rollback point, not a substitute for independent recoverable copies. In particular, verify whether the additional guest data disk containing Docker and Frigate media is included in any chosen snapshot or backup mechanism. Do not assume all disks and bind mounts are protected merely because a snapshot task returned `TASK OK`.

## Remaining recovery and hardening work

- [ ] Inspect VM backup configuration to confirm **all required virtual disks** are included.
- [ ] Create and verify a current **off-host** backup covering the Debian system, Compose/YAML, Docker persistent state, and Frigate media as applicable.
- [ ] Conduct an **isolated restore test** rather than relying solely on backup creation or archive hashes.
- [ ] Monitor Proxmox LVM-thin capacity and the 20 GB Frigate guest-data disk.
- [ ] Reassess CPU/memory/disk/network demand before attaching a real camera or enabling lengthy recordings.
- [ ] Recheck source-restriction and authentication policies after any network change.
- [ ] Review and patch the host, guest, Docker image and supporting services according to a controlled update process.

## Public publication policy

Do **not** commit: login passwords, API tokens, Frigate authentication/session details, private media of people/vehicles, full YAML with actual camera streams or secrets, OPNsense XML, firewall rule exports revealing real topology, real private/public addresses, SSH keys, MACs/serials, Proxmox backups, Docker volumes or unredacted screenshots. Replace local URLs and hostnames with placeholders in examples. Review every image corner, browser bar, clipboard capture and terminal scrollback.

The sanitized project describes the *controls and test outcomes*, not the operational values needed to reproduce the owner's private network.
