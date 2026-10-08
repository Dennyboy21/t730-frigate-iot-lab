# HP t730 — Initial Hardware Inspection

## Acquisition

- Purchase price: $80
- Condition: Used
- Manufacturer: HP
- Model: t730 Thin Client
- Original advertised storage: 200GB
- Installed storage found during inspection: 128GB
- Seller contacted regarding storage discrepancy

## Hardware Findings

| Component | Finding | Status |
|---|---|---|
| CPU | AMD RX-427BB | Identified from model/specifications; software verification pending |
| RAM | 8GB, two 4GB DDR3L SO-DIMMs | Visually inspected |
| SSD | 128GB M.2 SATA | Visually inspected; software verification pending |
| Network | Integrated Gigabit Ethernet | Functionality not yet tested |
| PCIe expansion | Expansion provision present | Available for future NIC evaluation |
| Video outputs | DisplayPort | Display cable required for testing |
| Cooling | Internal blower fan | Dust observed; cleaning recommended |
| Operating system | Windows 11 | Reported installed; testing pending |

## Inspection Findings

1. The computer was successfully opened for internal inspection.
2. Two memory modules were identified.
3. The installed SSD was found to have a lower capacity than advertised.
4. The memory compartment cover screws were loose when first inspected.
5. Dust was observed around the cooling fan.
6. No hardware modifications have been completed.

## Planned Validation

- [ ] Resolve SSD discrepancy with seller
- [ ] Connect monitor using DisplayPort
- [ ] Verify successful system startup
- [ ] Confirm CPU, RAM, and SSD specifications
- [ ] Check SSD health
- [ ] Test onboard Ethernet and USB ports
- [ ] Check BIOS access
- [ ] Verify AMD-V virtualization support
- [ ] Assess system temperatures and fan operation
- [ ] Document final hardware baseline

## Upgrade Decisions

No additional hardware will be purchased until initial validation is complete.

Potential future upgrades include 16GB RAM, an additional Ethernet interface, and dedicated camera-recording storage.
