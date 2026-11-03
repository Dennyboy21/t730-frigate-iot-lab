# Verification and Performance Baseline

**Date of recorded evidence:** November 2026  
**Workload:** One looping prerecorded parking-lot video, virtual camera `test_pattern`, configured 640 × 360 at 5 FPS.  
**Detector:** OpenVINO CPU using SSD-Lite MobileNet v2.

## Acceptance observations

| Criterion | Observed result | Result |
|---|---|---|
| Application and virtual camera accessible | Frigate 0.18.2 operational in Debian Docker VM | PASS |
| Person is identified | Person label confirmed in the live Debug view | PASS |
| Car is identified on repeated loops | User confirmed consistent recognition after motion tuning | PASS |
| Background motion is controlled | Unwanted red boxes nearly eliminated after disabling Improve Contrast | PASS for this clip |
| Temporary `bus` class not needed | Removed; person/car recognition remained consistent | PASS |
| No frame-skipping at sample time | Camera statistics showed skipped FPS = 0 | PASS at sampling time |
| Detector has adequate headroom for additional cameras | No multi-stream test performed | NOT TESTED |
| Live RTSP/ONVIF camera | No physical camera available | NOT TESTED |
| Long-term recording retention / storage failure behavior | No verified retention endurance test | NOT TESTED |

## Measured values

These values came from two Frigate System screenshots taken during the working single-stream test and should **not** be presented as constant values.

| Metric | Sample |
|---|---:|
| Camera FPS | 5.1 |
| Detection FPS | 4.7 |
| Skipped FPS | 0 |
| Detector inference speed | Approximately 118–138 ms across sampled displays |
| Detector CPU usage | 110.2% of one CPU core equivalent |
| Detector memory usage | 7.8% reported by the Frigate UI |
| Frigate overall CPU indicator | 62–71% in the two screenshots |
| FFmpeg CPU indicator | 1.3% |
| Capture CPU indicator | 1.1% |

**Interpretation:** A detector process exceeding 100% CPU can reflect usage of more than one logical CPU; it is not a claim that the entire host is above 100%. The overall UI CPU readings showed less pressure than some earlier troubleshooting observations above 90%, but the setup still has limited spare capacity. The Frigate slow-detector warning remains relevant.

**Limitations:** Detection FPS does not have to equal camera FPS on every sample. A zero skipped-FPS sample is favorable, but does not prove there were no drops at any time before or afterward.

## Follow-up test plan

- [ ] Recheck detector speed, CPU and skipped FPS over a longer uninterrupted period.
- [ ] Run a second prerecorded clip with different lighting and multiple vehicles, without changing baseline settings first.
- [ ] When available, add a live camera and evaluate frame cadence, decode load, detection accuracy and network isolation.
- [ ] Confirm disk consumption and retention behavior under an explicitly enabled recording workload.
- [ ] Confirm that a full recovery from independently backed-up VM/container/configuration/media data is possible.

## Evidence and publication

Raw Proxmox console, Frigate browser and test-video screenshots were reviewed interactively but **not committed** to this repository; their browser chrome and URL bar can expose internal network details. Evidence in this page is a manually transcribed and generalized summary of observed readings. See the [sanitization checklist](../security-publication-checklist.md) before adding imagery.
