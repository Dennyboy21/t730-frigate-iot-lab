# OPNsense Virtual Firewall and Segmentation — Implementation and Validation

**Lab platform:** HP t730 / Proxmox VE 9.2.x  
**Test period:** October 23–24, 2026  
**Status:** Initial virtual firewall and segmentation lab operational; household IoT devices have **not** been migrated or segmented.

## Purpose

Build and validate a downstream security lab without changing the household router's role. The OPNsense VM routes isolated test-client traffic, records firewall decisions, and protects household/upstream networks from unsolicited lab-originated connections.

## Implemented architecture

```text
Upstream household LAN and Internet
        |
Proxmox host (management retained upstream)
        |
OPNsense VM 100
  - WAN: upstream-side virtual NIC (DHCP/private upstream address)
  - LAN: isolated virtual lab NIC
        |
Debian 13 VM 101 (test client)
```

The production Nest/household network stayed operational during these tests. This is **virtual-machine isolation**, not a claim that production IoT VLANs or physical camera isolation have been deployed.

## Implementation observations

- Proxmox successfully ran OPNsense and Debian test VMs with separate upstream and lab-facing network paths.
- OPNsense provided a lab LAN address and gateway; the Debian VM received an address through DHCP and used OPNsense as its default route.
- The firewall maintained a `HOME_NETWORKS` alias of protected private/upstream ranges using the **Networks** alias type (not a single-host alias).
- An explicit logged LAN rule blocked lab-originated access to protected home/private destinations. A separate management-access rule was configured for the firewall's LAN address; subsequent HTTPS troubleshooting is recorded separately.
- Permitted lab Internet connectivity remained operational after the isolation policy.

## Observed validation

| Control | Test | Observation |
|---|---|---|
| Lab gateway | Debian ICMP to OPNsense LAN gateway | PASS — replies received |
| Protected household destination | Debian ICMP to upstream household gateway | BLOCK — no replies; corresponding OPNsense block log |
| Additional protected private test destination | Debian ICMP to another RFC1918 destination | BLOCK — no replies; corresponding OPNsense block log |
| External connectivity | Debian ICMP to public resolver address | PASS — replies received |
| Rule visibility | OPNsense Firewall Live View filtered by test client | PASS — block/pass entries correlated to tests |
| Proxmox management | Household-side management access | Verified separately during setup; not moved to the lab LAN |

**Evidence standard:** A failed ping alone cannot establish firewall enforcement. These results were cross-checked against OPNsense firewall log entries identifying lab-source traffic, intended destination, and matching rule label. The external ping verifies ICMP connectivity, not comprehensive application-layer reachability.

## Safety and limitations

- No household IoT, camera, or guest VLANs have been implemented.
- The private upstream WAN address is expected in this downstream lab architecture; WAN private-network blocking requires context-specific review rather than copying typical direct-Internet WAN settings.
- The lab uses NAT for outbound traffic, but NAT alone is **not** a segmentation control; explicit destination restrictions are needed.
- Do not expose the management interfaces directly to the public Internet.
- Monitor host resources before adding Frigate or additional VMs.

## Follow-up

- Confirm the exact Proxmox virtual bridge inventory and publish a **sanitized** final network diagram.
- Exercise DNS, HTTP(S), and specific allowed/denied TCP cases beyond ICMP.
- Add a second isolated network/VLAN only when appropriate switch/AP hardware is available.
- Deploy Frigate and a test IP camera as a **future** milestone.

**Evidence handling:** This file deliberately omits actual internal IPs, MAC addresses, host credentials, rule exports, and screenshots containing tokens or session identifiers.
