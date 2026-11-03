# Frigate Deployment and Architecture

**Implemented:** November 2026 proof-of-concept; this is not a production surveillance deployment.

## Hardware and virtualization

| Layer | Implemented role |
|---|---|
| Physical host | Refurbished HP t730 thin client, AMD RX-427BB, upgraded 16 GB RAM |
| Hypervisor | Proxmox VE 9.2.x |
| VM 100 | OPNsense lab firewall with upstream and isolated-lab network interfaces |
| VM 101 | Debian test client used in previous firewall validation |
| VM 102 | Debian guest running Docker and Frigate 0.18.2 |
| Detector | Intel OpenVINO runtime on the **AMD host CPU**, without a GPU/NPU accelerator |
| Video input | Short prerecorded parking-lot file played in a loop as virtual camera `test_pattern` |

### Logical data and access flow

```text
Existing household network (unchanged)
            |
  HP t730 / Proxmox VE host
            |
   +--------+----------------------+
   |                               |
OPNsense VM 100                 Other lab VMs
WAN: upstream-side interface     |
LAN: isolated lab interface      |
   |                       Debian VM 102
   +--- isolated lab network --- Docker / Frigate
                                      |
                           Looping parking-lot clip
                                      |
                          Motion -> regions -> OpenVINO
                                      |
                          Person / vehicle tracking
```

A narrowly **source-restricted** OPNsense WAN-side NAT rule enables authenticated administrative access to Frigate's HTTPS application port **8971** from a trusted upstream client. This was a lab-side access arrangement, **not** a router/Internet port forward. The interface is not documented as publicly exposed. Actual addresses, source rules, and credentials are intentionally omitted here.

The household router and existing smart-home setup have **not** been migrated behind OPNsense. Earlier lab-to-home isolation tests are recorded separately and should not be interpreted as physical IoT VLAN deployment.

## Storage

The Frigate guest was given a dedicated **20 GB** additional virtual disk, mounted at `/mnt/docker-data`. Within that mount:

```text
/mnt/docker-data/
├── docker/           # Docker data root
├── containerd/       # Container runtime data
└── frigate-media/    # Persistent Frigate media
```

The working Compose project and YAML configuration reside in the Debian guest under the Frigate administrator's home directory. The looping video is available inside the container at `/media/frigate/people-cars-test.mp4`.

**Important:** The 20 GB media/runtime disk is a **lab capacity limit**, not evidence of sufficient production recording retention. Check hypervisor disk allocation, guest mount persistence, media growth and backup inclusion before enabling longer-term recording.

## Detector compatibility

The guest CPU exposed `sse4_2` and `avx` but not `avx2`. The original CPU TFLite detector showed roughly **669 ms** inference per frame during troubleshooting. OpenVINO CPU inference was tested with the installed `ssdlite_mobilenet_v2.xml` model, and model compilation succeeded. Synthetic-image direct inference gave roughly 104–142 ms during early checks; live Frigate later reported roughly 118–138 ms.

The CPU architecture and slow warning mean this is a resource-constrained proof of concept, not that person/car detection has failed.

## Sanitized working configuration excerpt

The following preserves **observed functional settings** and the public model paths. It deliberately excludes the environment-specific FFmpeg input, secrets, access URLs, recording settings, volume paths and any other application configuration. It is **not a complete deployable `config.yml`**:

```yaml
detectors:
  ov:
    type: openvino
    device: CPU

model:
  width: 300
  height: 300
  input_tensor: nhwc
  input_pixel_format: bgr
  path: /openvino-model/ssdlite_mobilenet_v2.xml
  labelmap_path: /openvino-model/coco_91cl_bkgr.txt

cameras:
  test_pattern:
    detect:
      enabled: true
      width: 640
      height: 360
      fps: 5
      min_initialized: 2
    motion:
      threshold: 30
      contour_area: 10
      improve_contrast: false
    objects:
      track:
        - person
        - car
        - bicycle
      filters:
        car:
          min_score: 0.3
          threshold: 0.5
```

These relatively permissive car thresholds remained in place after testing. The temporary `bus` tracking and filter were removed once reliable `car` classification had been verified. Do **not** apply these values uncritically to a different camera scene.

## Reproduction boundaries

The stream is a prerecorded loop, not a live camera. No claim is made for tested RTSP/ONVIF compatibility, exterior lighting changes, simultaneous cameras, intrusion alerts, surveillance accuracy, or 24/7 recording reliability.

Reference: [Frigate motion detection documentation](https://docs.frigate.video/configuration/motion_detection/) and [configuration guide](https://docs.frigate.video/configuration/config/).
