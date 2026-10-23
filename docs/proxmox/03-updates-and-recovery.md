# 03 — Initial Repository Repair, Upgrade, and Kernel Verification

**Period:** October 22–23, 2026

## Initial failure

After the first boot, **Updates → Refresh** failed. Task Viewer showed an attempt to fetch:

```text
https://enterprise.proxmox.com/debian/ceph-squid ... 401 Unauthorized
E: The repository ... is not signed.
TASK ERROR: command 'apt-get update' failed: exit code 100
```

The active **Ceph enterprise repository** requires a subscription. The lab has no paid enterprise subscription and doesn't use Ceph distributed storage.

![Ceph enterprise repository error](../../screenshots/proxmox/05-ceph-repository-error.png)

## Correction

1. Opened **pve-t730 → Updates → Repositories**.
2. Disabled the Ceph enterprise source (subscription-only).
3. Kept standard Debian sources and the official Proxmox **no-subscription** source enabled.
4. Repeated **Updates → Refresh**.
5. Confirmed the output ended with **`TASK OK`**; no further 401 error.

![Successful repository refresh](../../screenshots/proxmox/06-repository-refresh-task-ok.png)

## Initial upgrade

- The GUI listed numerous available Debian and Proxmox packages, including kernel, firmware, `pve-manager`, and security-related libraries.
- Launched **Updates → Upgrade**, reviewed the console output, and proceeded.
- A microcode package change notice about AMD `EntrySign` displayed in a pager. Pressed **Q** to close the notice and continue package installation. **This was general package release information and was not proof the t730's RX-427BB is affected.**
- Console completed with **`Your System is up-to-date`** and advised a reboot because a new kernel had been installed.
- The output included `No /etc/kernel/proxmox-boot-uuids found, skipping ESP sync.` The full upgrade still completed; no boot issue was observed afterward.

![Upgrade completed](../../screenshots/proxmox/07-upgrade-finished.png)

## Restart and verification

- Rebooted the node from the Proxmox web UI.
- Reconnected to `https://192.168.86.10:8006`.
- Opened **pve-t730 → Shell** and ran:

```bash
uname -r
```

Observed output:

```text
7.0.14-25-pve
```

![Kernel verification](../../screenshots/proxmox/08-kernel-after-reboot.png)

## Final status

**PASS:** Proxmox node booted from internal SSD, package refresh completed cleanly, upgrade finished, reboot succeeded, and active kernel matched the updated package.

**Future maintenance:** Periodically refresh/upgrade packages, review prompts and any proposed removals, plan downtime before kernel reboots, and back up host/VM configuration.
