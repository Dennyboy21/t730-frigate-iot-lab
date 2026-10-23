# GitHub Issue Draft 02

**Title:** Milestone: Create isolated OPNsense VM network and validate firewall boundaries

**Body:**

## Objective

Practice firewall-based isolation on the HP t730 **without** migrating, bridging, or disrupting the existing Nest WiFi household/IoT network.

## Pending tasks

- [ ] Create `vmbr1` Linux bridge with **no physical ports, host IP or gateway**
- [ ] Verify `vmbr0` remains attached to `nic0` with the working management IP
- [ ] Upload a verified OPNsense installer ISO to Proxmox storage
- [ ] Measure host memory capacity and set a conservative VM allocation
- [ ] Create OPNsense VM with two virtual NICs: WAN on `vmbr0`, LAN on `vmbr1`
- [ ] Confirm virtual NIC identification inside OPNsense before applying any firewall policy
- [ ] Set lab-only LAN subnet and DHCP (example `192.168.50.0/24`)
- [ ] Install explicit rules blocking traffic initiated from lab to the household `192.168.86.0/24` subnet and other private networks as appropriate
- [ ] Permit only required outbound lab traffic; configure and verify DNS
- [ ] Connect an isolated VM test client; test allowed internet path and blocked household path
- [ ] Save sanitized test outputs and firewall log evidence
- [ ] Verify TP-Link UE306 detection in Proxmox before considering physical lab clients
- [ ] Document rollback for `vmbr1` and recovery access to the Proxmox host

## Security acceptance tests

1. A lab VM receives an IP on the test-only network.
2. Lab VM cannot reach trusted household devices or Proxmox management due to explicit policy.
3. Allowed update/DNS traffic works as specified.
4. Household Nest network keeps functioning normally throughout the test.
5. Evidence documents both pass and fail cases, without public MACs/serials/credentials.

**Out of scope:** Full home IoT VLAN segmentation, UniFi migration, production router replacement, and surveillance/NVR deployment.
