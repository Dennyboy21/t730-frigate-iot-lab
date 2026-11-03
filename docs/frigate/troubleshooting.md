# Troubleshooting: Motion Detection Versus Object Classification

## Initial symptom

Frigate displayed red **motion** boxes over the moving vehicle but would often show **no car label** or object-list entry. Some clips generated red boxes over parking stripes and apparently stationary pavement. An initial CPU-based TFLite detector was slow, so the lab was transitioned to OpenVINO CPU inference.

The initial symptom was **not** sufficient evidence that the object detection model could not recognize cars.

## Method and evidence

| Diagnostic step | Observation | Interpretation / action |
|---|---|---|
| Check service logs and decoder | Container operational; incidental `go2rtc_stream_proxy` warnings | No confirmed fatal detector fault from these warnings |
| Confirm CPU features | Guest had AVX, not AVX2 | Test model compatibility rather than assume hardware acceleration |
| Compile OpenVINO model directly | SSD-Lite MobileNet v2 compiled and inferred successfully on CPU | Detector runtime usable on the hardware |
| Inspect first live metrics | Inference around 129 ms, high CPU load and sometimes skipped frames | Performance constrained; not necessarily model incompatibility |
| Relax `car` confidence | `min_score: 0.3`, `threshold: 0.5` | By itself, did not fix missing car tracks |
| Add temporary `bus` tracking | Still did not fix missed car labels | Misclassification as `bus` alone was insufficient explanation |
| Test direct inference on video frames | Car detected on full frames and a cropped frame | Model and COCO labels could identify the target |
| Analyze consecutive raw frames | Car outputs present in **11 consecutive frames (85–95)** | Recognition was not merely a one-frame fluke |
| Set `min_initialized: 1` | Not accepted by installed Frigate version | Retained `min_initialized: 2` |
| Adjust contour area 10→30 | Little meaningful reduction in false background boxes | Reverted |
| Raise motion threshold 30→45→60→maximum | Little meaningful improvement | Reverted to 30 |
| Disable **Improve Contrast** | Substantial reduction in spurious red boxes; moving vehicle retained motion box | Saved `improve_contrast: false` |
| Verify after saving and returning to Debug | Car and person consistently labeled on repeated loops | Working single-stream baseline established |
| Remove temporary `bus` class | Person and car still consistently recognized; background noise remained minimal | Simplified tracked classes |

### Independent video-frame inference

A read-only Python/OpenVINO test used the same installed model on decoded frames from the local 100-frame sample video. It did not go through Frigate's motion-region selection, tracking, or confidence aggregation.

Recorded `car` detections in the final segment:

| Frame | Confidence |
|---|---:|
| 85 | 0.63 |
| 86 | 0.84 |
| 87 | 0.79 |
| 88 | 0.62 |
| 89 | 0.36 |
| 90 | 0.86 |
| 91 | 0.40 |
| 92 | 0.90 |
| 93 | 0.87 |
| 94 | 0.74 |
| 95 | 0.41 |

Earlier cropped-image testing also produced `bus` and `car` classifications, motivating the temporary `bus`-tracking experiment. Because `bus` tracking did not fix the live problem, it was subsequently removed.

**Caveat:** This independent test used its own full-frame resize/preprocessing and is not pixel-for-pixel equivalent to Frigate's internal image regions. It verifies that the model can recognize the car, not that Frigate was feeding identical inputs every time.

## Effective change

The strongest operational before/after observation was **disabling Improve Contrast** in the Motion Tuner, then saving that change. Motion noise became minimal, the moving car continued to trigger motion detection, and person/car classification became consistent in normal Debug operation.

This supports Improve Contrast as a contributor to excessive background-motion activity **in this test scene**. We did not capture internal Frigate region/queue traces that prove an exclusive causal mechanism; avoid describing this as a general Frigate defect.

## Interpreting the debugging overlays

- **Red boxes:** image changes identified as motion.
- **Green regions:** candidate image regions for object inference in relevant debug views; their absence in a momentary screenshot is not conclusive by itself.
- **Object labels:** tracked classifications that have passed Frigate's applicable confidence/tracking rules.
- **`Ov is very slow`:** warning about detector inference latency; in this lab, it coexisted with successful live person/car identification.

## Lessons learned

1. Separate **motion detection**, **detector inference**, and **object tracking** when diagnosing missing labels.
2. Verify model behavior directly before blaming the model or replacing hardware.
3. Use one-variable-at-a-time experiments, revert ineffective changes, and save successful settings.
4. Judge success from repeated object labels **and** performance metrics, not a lone screenshot.
5. Treat resource consumption as a capacity concern even when the single-camera test passes.

Reference: [Frigate motion-tuning guidance](https://docs.frigate.video/configuration/motion_detection/).
