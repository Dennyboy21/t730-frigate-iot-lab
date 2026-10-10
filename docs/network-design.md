# HP t730 — Network Architecture and Segmentation Design

## 1. Project Overview

This document describes the proposed network architecture for a low-cost cybersecurity and surveillance proof-of-concept using an HP t730 Thin Client.

The lab will demonstrate network virtualization, firewall administration, IoT segmentation, IP-camera isolation, and local video surveillance.

The initial design will operate independently of the existing household network to avoid disrupting internet access or smart-home functionality.

**Project status:** Planning and preparation

## 2. Project Objectives

- Deploy Proxmox VE as the virtualization host.
- Deploy OPNsense as a virtual firewall.
- Establish an isolated test network.
- Control communication between trusted and untrusted devices.
- Validate firewall rules through practical testing.
- Deploy Frigate NVR for local IP-camera monitoring.
- Document configuration changes and test results.
- Apply lessons learned to a future UniFi deployment.

## 3. Existing Network Environment

The existing household network uses:

- AT&T Fiber internet service
- AT&T residential gateway
- Google Nest WiFi Pro router and mesh access points
- Ethernet switches and wired connections
- Smart-home and traditional computing devices

The household network will remain unchanged during the initial proof-of-concept.

The HP t730 will connect to the existing LAN as a downstream laboratory system.

## 4. Hardware Inventory

| Component | Role |
|---|---|
| HP t730 Thin Client | Virtualization host |
| AMD RX-427BB | Host processor |
| 8GB DDR3L RAM | Initial system memory |
| 128GB M.2 SATA SSD | Initial operating system storage |
| Integrated Gigabit Ethernet | Upstream/home network interface |
| TP-Link UE306 USB 3.0 Gigabit Ethernet | Isolated laboratory interface |
| Google Nest WiFi Pro | Existing household router |
| Test laptop | Firewall validation endpoint |
| Future PoE camera | Frigate surveillance testing |

## 5. Proposed Network Architecture

```text
             INTERNET
                |
        AT&T Fiber Gateway
                |
        Google Nest WiFi Pro
                |
       Existing Household LAN
                |
        Integrated Ethernet
                |
       +------------------+
       |     HP t730      |
       |    Proxmox VE    |
       |                  |
       |  OPNsense VM     |
       |  Frigate (later) |
       +------------------+
                |
        TP-Link UE306
                |
       Isolated Lab Network
                |
        Test Laptop / Switch
                |
       Future IoT / Cameras
```

**Design principle:** The existing household network remains the primary production network. OPNsense provides routing and filtering for the downstream laboratory network.

## 6. Proposed IP Addressing

The following addresses are proposed examples and will be validated before deployment.

| Network | Example subnet | Purpose |
|---|---|---|
| Household LAN | 192.168.86.0/24 | Existing trusted household network |
| Initial lab LAN | 192.168.50.0/24 | Isolated testing environment |
| Future IoT VLAN | To be assigned | Smart-home devices |
| Future Camera VLAN | To be assigned | Surveillance cameras |
| Future Guest VLAN | To be assigned | Guest internet access |

Initial OPNsense configuration:

- WAN: DHCP address from existing household router
- LAN: 192.168.50.1/24
- Lab DHCP pool: 192.168.50.100–192.168.50.199
- Lab DNS: OPNsense LAN interface, with appropriately configured upstream resolvers

Network addresses must be checked for overlap before installation.

## 7. Proxmox Network Design

Two Linux bridges are planned.

| Bridge | Physical interface | Purpose |
|---|---|---|
| vmbr0 | Integrated Ethernet | Proxmox management and OPNsense WAN |
| vmbr1 | TP-Link UE306 | Isolated OPNsense LAN |

### Security Requirements

- Proxmox management remains on the household-side bridge.
- The lab bridge does not receive a host management IP address.
- The bridges must not be directly joined together.
- OPNsense controls routed traffic between networks.
- The USB adapter must be tested for driver compatibility and stability.

For the first experiment, a laptop can connect directly to the USB Ethernet interface, avoiding the need to purchase a managed switch.

## 8. OPNsense Firewall Design

The initial firewall policy will use least-privilege access.

| Source | Destination | Planned action |
|---|---|---|
| Lab network | Internet | Allow |
| Lab network | Household LAN | Block |
| Lab network | OPNsense DHCP/DNS | Allow required services |
| Lab network | OPNsense administration | Restrict |
| Household network | Proxmox management | Authorized access only |
| Future Camera network | Internet | Block unless explicitly required |
| Future Trusted network | IoT services | Allow only necessary connections |

Because OPNsense WAN receives a private IP address from the Nest router, the WAN setting that blocks private networks will need to be evaluated and disabled for this downstream lab arrangement.

Firewall rules will be tested before moving beyond a single test endpoint.

## 9. Network Segmentation Strategy

### Initial Phase

Create a single isolated laboratory LAN behind OPNsense.

Verify that devices connected to this network can obtain DHCP addresses and access the internet while being prevented from initiating unauthorized connections into the household LAN.

### Future IoT Segmentation

Introduce separate networks for:

- Trusted computing devices
- IoT and smart-home equipment
- Surveillance cameras
- Guest devices

A VLAN-capable switch and suitable wireless access point may be required for multi-VLAN and wireless segmentation testing.

Google Home, Nest, Chromecast, and Philips Hue functionality may depend on multicast discovery protocols such as mDNS. Any cross-network discovery and communication will be evaluated through controlled firewall exceptions.

## 10. Frigate NVR Integration

Frigate will be added after Proxmox and OPNsense networking are operational.

Initial objectives:

- Connect one test IP camera.
- Establish a video stream.
- Configure local recording.
- Evaluate object detection performance.
- Test CPU, memory, and storage usage.
- Verify the camera's network isolation.

Frigate and OPNsense may share the virtualization host during the proof-of-concept, subject to performance testing.

Dedicated surveillance storage and additional hardware resources will be considered after initial validation.

## 11. Validation Test Plan

| Test | Expected result | Status |
|---|---|---|
| Proxmox management access | Accessible from authorized household device | Pending |
| OPNsense WAN connectivity | Receives upstream DHCP address | Pending |
| Lab DHCP | Test device receives lab IP address | Pending |
| Lab internet access | Successful | Pending |
| Lab-to-household connection | Blocked by firewall | Pending |
| Firewall logging | Denied test connections recorded | Pending |
| USB network adapter stability | No unexpected disconnections | Pending |
| Frigate video stream | Camera displays video | Pending |
| Frigate recording | Local recording succeeds | Pending |
| Camera internet restriction | Unauthorized internet traffic blocked | Pending |

A blocked ping alone will not be considered sufficient validation. Firewall logs and controlled connection tests will be used to confirm segmentation.

## 12. Security Considerations

- Do not expose Proxmox or OPNsense management interfaces directly to the internet.
- Use strong administrative credentials.
- Apply appropriate software and firmware updates.
- Use separate management and laboratory network interfaces.
- Disable unnecessary services.
- Back up sanitized configuration information.
- Exclude passwords, API tokens, serial numbers, MAC addresses, public IP addresses, and other identifying information from the public repository.

## 13. Future UniFi Migration

The proof-of-concept will inform the permanent home network design.

Potential future components include:

- UniFi gateway and firewall
- Managed PoE switch
- UniFi wireless access points
- VLAN-based network segmentation
- UniFi Protect surveillance cameras
- Dedicated storage and UPS protection

The final architecture will separate trusted devices, IoT equipment, surveillance cameras, guests, and lab systems.

## 14. Current Progress

- [x] Acquire HP t730 Thin Client
- [x] Inspect internal hardware
- [x] Obtain TP-Link UE306 USB Ethernet adapter
- [x] Prepare Proxmox installation USB
- [x] Verify Proxmox ISO checksum
- [x] Download and extract OPNsense ISO
- [x] Verify OPNsense archive checksum
- [ ] Resolve SSD discrepancy
- [ ] Validate hardware and BIOS
- [ ] Install Proxmox
- [ ] Configure OPNsense
- [ ] Validate network segmentation
- [ ] Deploy Frigate
- [ ] Test isolated IP camera

## 15. Lessons Learned

This section will be updated throughout implementation to document:

- Hardware compatibility findings
- Network interface and driver issues
- Firewall configuration challenges
- Virtualization performance
- Camera connectivity and detection results
- Security improvements and future design decisions

---

**Project:** HP t730 Frigate & IoT Security Lab

**Document:** Network Design

**Status:** Initial design — subject to testing and revision

**Created:** October 2026
