# GitHub Issue Draft 01

**Title:** Milestone: Validate HP t730 and establish updated Proxmox host

**Body:**

## Goal

Turn the refurbished HP t730 into a documented and updated Proxmox server, without deploying a firewall into the active Nest LAN.

## Completed

- [x] Resolve inadequate power-supply / four-red-flash startup issue
- [x] Verify CPU, dual-channel 8 GB RAM and 128 GB Lite-On SSD
- [x] Enable and re-check AMD virtualization in BIOS
- [x] Record SSD Windows health and reliability counters
- [x] Validate physical 1 Gbps link for Realtek and UE306 under Windows using isolated switch
- [x] Resolve installer USB UEFI recognition and boot Proxmox setup
- [x] Install Proxmox VE 9.2 using ext4 on internal SSD
- [x] Configure static Proxmox management IP on Realtek / `vmbr0`
- [x] Verify browser management access and first dashboard
- [x] Fix subscription-only Ceph enterprise repository `401 Unauthorized` update error
- [x] Complete first package upgrade, reboot, verify kernel `7.0.14-25-pve`
- [x] Verify `vmbr0` attached to `nic0`, management active

## Remains

- [ ] Complete Linux health check of SSD and long-duration host operation
- [ ] Record backup and recovery procedure
- [ ] Validate the USB Ethernet adapter inside Proxmox

## Acceptance criteria

Host boots from internal SSD, login works over trusted LAN, updates complete without `apt` errors, documented kernel version is active, and live home networking is unaffected.

## Evidence

See `docs/proxmox/` and `screenshots/proxmox/` in this repository.
