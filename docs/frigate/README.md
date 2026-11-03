# Frigate NVR and OpenVINO — Working Proof of Concept

**Verified status: November 3, 2026**  
**Platform:** HP t730 / Proxmox VE 9.2.x / Debian VM / Docker / Frigate 0.18.2  
**Scope:** One looping prerecorded parking-lot video as a virtual camera. **No physical camera connected yet.**

## Objective

Determine whether the existing low-cost HP t730 can host a locally managed NVR and perform useful person/vehicle detection without purchasing additional camera or AI-accelerator hardware. The exercise also covers storage layout, network isolation, controlled access, observability, troubleshooting, and recovery.

## Outcome

**PASS for the single test video:** after motion tuning, Frigate consistently labeled the person and car in repeated loops. The working configuration was preserved in a Proxmox snapshot and a separately named configuration-file copy inside the guest.

| Test | Verified outcome |
|---|---|
| Frigate service | Running in Docker on Debian VM 102 |
| OpenVINO CPU inference | Working with SSD-Lite MobileNet v2 |
| Person detection | Confirmed |
| Car detection | Confirmed consistently in repeated video loops |
| Background-motion noise | Substantially eliminated by disabling Improve Contrast |
| Detect stream | 640 × 360 at configured 5 FPS |
| Camera / detection FPS, sampled | 5.1 / 4.7 |
| Skipped FPS, sampled | 0 |
| Host CPU, sampled in Frigate UI | 62–71% |
| Detector inference, sampled | Approximately 118–138 ms; Frigate reports slow inference |
| Physical RTSP/ONVIF camera | Not tested |
| Multiple concurrent cameras | Not tested |
| Sustained retention/restore | Not tested |

The samples above describe a particular observation window, **not a capacity guarantee**. The detector remains CPU-intensive; there is no evidence yet that multiple camera streams would fit within acceptable headroom.

## Read the documentation

1. [Deployment and architecture](deployment-and-architecture.md) — host/VM roles, storage, network access, and sanitized configuration excerpt.
2. [Troubleshooting record](troubleshooting.md) — how we separated the video/model from motion selection and found the effective change.
3. [Verification and performance](validation.md) — evidence, metrics, limitations and acceptance criteria.
4. [Security and recovery](security-and-recovery.md) — firewall scope, saved recovery points and publication controls.

For earlier phases of this project, see the [Proxmox documentation](../proxmox/README.md), [OPNsense segmentation validation](../opnsense/01-virtual-firewall-and-segmentation.md), and [public-evidence sanitization checklist](../security-publication-checklist.md).

## Skills demonstrated

- Virtual machine administration and containerized service deployment.
- Isolated laboratory networking with controlled administrative access.
- OpenVINO model compatibility and direct inference testing.
- Root-cause analysis across motion detection, inference, and object tracking.
- Baseline metrics, configuration change control, snapshots, and public-safe technical documentation.

## Outstanding work

- Connect an authorized live camera or controlled Android IP-camera feed.
- Check stability, CPU headroom, and storage growth over longer sessions.
- Verify restore of configuration/media from an independently stored backup.
- Validate recording/retention separately from detection.
- Evaluate multi-camera capacity and physical camera/IoT VLANs only when suitable hardware is available.

No genuine credentials, network addresses, camera URLs, raw configs or unredacted screenshots are included in this project documentation.
