# 06 — VM Snapshots, Thin-Pool Monitoring, and Verified Backups

**Date:** October 24, 2026  
**Host:** HP t730 with Proxmox VE

## Baseline snapshots

- Created a security-baseline snapshot for **VM 101 (Debian lab)**.
- Created a security-baseline snapshot for **VM 100 (OPNsense lab)**.
- Both snapshot tasks reported `TASK OK`.
- Proxmox warned that combined thin-volume virtual sizes exceeded available physical backing capacity, so snapshots must not be confused with full independent backups.

## LVM-thin monitoring and protective threshold

The host uses a relatively small internal SSD and an LVM-thin data pool. Observed values during investigation included:

| Check | Observed result |
|---|---|
| Thin pool virtual size | Approximately 54.84 GiB |
| Thin pool data usage | Approximately 24% at check |
| Thin pool metadata usage | Approximately 2% at check |
| Volume group remaining free | Approximately 14.63 GiB at check |
| Snapshot/task warnings | Thin provisioning overcommit and absent/ineffective auto-extension threshold |

The LVM configuration file was backed up locally before editing. The host's thin-pool auto-extension settings were changed and then confirmed with `lvmconfig`:

```text
thin_pool_autoextend_threshold=80
thin_pool_autoextend_percent=10
```

The `lvm2-monitor` service was confirmed active and the thin pool monitored. These settings reduce one operational risk, **but cannot create free disk space**; continued monitoring and capacity planning are required. A threshold setting is not proof that future auto-extension will succeed.

## VM backups

- Initial Debian VM backup using **Stop** mode failed because the guest shutdown timed out. That failed job was not treated as an actual backup.
- Subsequent VM 101 backup completed successfully and produced a compressed archive of approximately **4 GiB**.
- VM 100 backup completed successfully and produced a compressed archive of approximately **545 MiB**.
- The two successful backups were copied from Proxmox storage to a separate Windows workstation using `scp` over SSH.
- File sizes were inspected and **SHA-256 hashes were calculated on both Windows and the Proxmox host**. Each transferred archive's hash matched its original.

Example transfer procedure (use your own host identity and destination):

```powershell
scp "root@<PROXMOX_MGMT_IP>:/var/lib/vz/dump/vzdump-qemu-*.vma.zst" "$env:USERPROFILE\LabBackups\HP-t730\"
Get-ChildItem "$env:USERPROFILE\LabBackups\HP-t730\*.vma.zst" |
  Get-FileHash -Algorithm SHA256
```

Compare to the hash output on the Proxmox host rather than posting the entire private archive. Repository examples intentionally omit account-specific Windows paths and host addresses.

## OPNsense configuration backup

After the MFA security baseline was verified, an updated OPNsense XML configuration backup was uploaded to **private Google Drive**.

**Encryption status:** Not independently confirmed from the available evidence. Verify that the XML was exported with configuration-file encryption enabled before treating the stored copy as protected. If uncertain, re-export an encrypted version and securely replace the unencrypted copy.

## Recovery posture / limitations

- Snapshots are convenient rollback points but depend on the same local storage as their VM.
- Copied VM archives provide separate-host backups; restore success still needs an isolated test.
- Configuration XML may contain credentials, password hashes, OTP metadata/secrets, device identifiers, and internal topology. **Never upload a real configuration backup to a public GitHub repository.**
- Keep backup encryption passwords separate from backup files.
- Monitor disk use, thin-pool data/metadata and free space before adding Frigate storage workloads.
- A complete restore drill (VM and OPNsense XML) remains pending.

## Suggested validation

- [x] Take security-baseline VM snapshots
- [x] Verify successful VM backups
- [x] Transfer backups outside the virtualization host
- [x] Compare SHA-256 hashes for archive integrity
- [x] Save updated firewall configuration in private cloud storage
- [ ] Independently confirm that the cloud configuration backup is encrypted
- [ ] Test restoring a backup into an isolated environment
- [ ] Establish periodic backup rotation and retention limits
