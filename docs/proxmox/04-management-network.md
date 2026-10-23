# 04 — Network Baseline and Proposed OPNsense Isolation

**Verified:** October 23, 2026  
**Important:** `vmbr1`, OPNsense, and physical IoT segmentation were **planned** at the end of this session; there is **no evidence they have been created or tested yet**.

## Live Proxmox interfaces (observed)

From **pve-t730 → System → Network**:

| Name | Type | Active | Autostart | Ports/slaves | IP / gateway | Status |
|---|---|---|---|---|---|---|
| `nic0` | Physical Realtek NIC (`r8169`) | Yes | No | — | No host IP on physical NIC | Verified |
| `vmbr0` | Linux Bridge | Yes | Yes | `nic0` | `192.168.86.10/24`; gateway `192.168.86.1` | Verified |
| `vmbr1` | Linux Bridge | Not shown | — | — | — | **Not yet created** |
| TP-Link UE306 | USB Ethernet | Not shown in Proxmox screenshot | — | — | — | Recognized in Windows, **not yet verified in Proxmox** |

VLAN-aware setting on the existing `vmbr0` was **No**, which is adequate for this initial non-VLAN lab. The physical port `nic0` should be kept attached to `vmbr0` so management stays online.

![Bridge baseline; MAC-derived alternative name redacted](../../screenshots/proxmox/09-network-baseline.png)

## Phase 1 — Proposed virtual firewall lab (not yet deployed)

```text
Home/Nest LAN 192.168.86.0/24
           |
  Realtek nic0 (physical)
           |
  Proxmox vmbr0 (192.168.86.10)
           |
        OPNsense VM
    WAN vNIC on vmbr0
    LAN vNIC on vmbr1
           |
 vmbr1: internal-only bridge, no physical ports or host IP
           |
   Lab test clients (VMs only)
```

The intended isolated test subnet is an **example** `192.168.50.0/24`, with OPNsense LAN at `192.168.50.1` after setup. Nothing on the live Nest network is reassigned.

### Important security caveat

- A second bridge with no physical ports isolates **Layer 2**, but that does not automatically restrict traffic routed by OPNsense.
- OPNsense's WAN VM NIC attached to `vmbr0` is **on the trusted household LAN**, not on the public internet. Treat it as untrusted for lab traffic nevertheless.
- Before attaching lab test clients with Internet access, configure firewall rules to **deny lab-originated traffic to `192.168.86.0/24` and any other internal networks**, and allow only explicitly required traffic (DNS, updates, etc.). Test both expected blocks and permitted paths.
- Avoid any DHCP server, IPv6 router advertisements, bridge loops, or routing that could affect `vmbr0` or the household LAN.
- Do not modify `vmbr0` while connected through it without local console/recovery access.

## Proposed `vmbr1` creation values

In Proxmox **System → Network → Create → Linux Bridge**:

| Field | Planned value |
|---|---|
| Name | `vmbr1` |
| IPv4/CIDR / Gateway | Blank |
| IPv6/CIDR | Blank |
| Bridge ports | Blank |
| Autostart | Enabled |
| VLAN aware | Disabled for this simple lab |
| Comment | `Isolated OPNsense lab LAN` |

**Pending action:** Create, verify that `vmbr0` remains unchanged, and only then apply network configuration with console access available. The screen as of October 23 showed only `nic0` and `vmbr0`.

## Phase 2 — Possible physical test network (future)

After verifying UE306 support in Proxmox, a dedicated bridge and a disconnected-from-home-network test switch could be introduced for physical test clients or cameras. The UE306 should **not** be used as an Internet-facing production WAN by default without considering link stability and driver reliability.

## Scope limit: existing Nest IoT devices

The Google Nest WiFi Pro home environment does not offer the VLAN/SSID-to-VLAN mapping needed to independently segment existing Wi-Fi smart-home devices. This Proxmox lab is a **proof of concept**, not a home-wide IoT security migration. Broader implementation remains part of the future UniFi/managed-switch project.
