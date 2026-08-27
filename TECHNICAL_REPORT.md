# ReLoop Inspector — Technical Report

**Status:** functioning managed prototype  
**Team:** MERAVIGLIA  
**Runtime:** React 19, Node.js 22, tRPC, Python, OpenCV 5.0.0.93

## Abstract

ReLoop Inspector demonstrates a closed-loop Agentic Vision workflow for cosmetic smartphone inspection. Unlike a passive classifier, the system can reject insufficient evidence, select one reason-specific corrective action, verify the resulting frame, and only then admit it to a four-view evidence set. A human remains responsible for the final decision.

## System boundary

The prototype assesses capture quality, requested pose, and candidate visual surface marks. It does not test functional, electrical, authenticity, battery, water-resistance, or safety properties. The cosmetic band is a transparent heuristic and cannot be interpreted as a commercial certification.

## Architecture

The React client owns the consented inspection session and sends one compressed image at a time through tRPC. Node validates MIME type, byte size, header dimensions, decompression limits, and process budgets before writing a request-scoped temporary file. A short-lived Python worker decodes the image with OpenCV, downsizes it when required, computes measurements, creates an overlay, and returns a structured decision. Temporary server files are removed in a `finally` block.

| Layer | Responsibility | Failure boundary |
|---|---|---|
| Browser | Consent, guided capture, session state, review, local export | Invalid type/size, camera or browser failure |
| Node/tRPC | Typed contract, request validation, timeout, temporary-file lifecycle | 7 MB, 12 MP, 4096 px, 15 s and output caps |
| Python/OpenCV | Detection, quality metrics, pose, candidates, overlays, decisions | Decode failure, no device, failed gate |
| Human review | Confirm, correct with rationale, or defer | No autonomous final certification |

## OpenCV pipeline

The worker detects the device boundary from grayscale edges, morphological closing, contours, minimum-area rectangles, and quadrilateral approximation. It then calculates seven explainable gates.

| Metric | OpenCV method | Prototype gate |
|---|---|---|
| Focus | Variance of Laplacian | ≥ 80 |
| Exposure | Mean grayscale luminance | 45–215 |
| Black clipping | Share of device pixels ≤ 8 | ≤ 15% |
| White clipping | Share of device pixels ≥ 247 | ≤ 10% |
| Glare | HSV high-value, low-saturation mask | ≤ 8% of device area |
| Coverage | Device contour area / frame area | 22–90% |
| Pose | Quadrilateral edge asymmetry | View-specific rule |

Candidate surface marks are short Hough line segments extracted from a contrast-enhanced, eroded device interior. These marks support review but are not labelled defects.

## Agentic orchestration

The state machine evaluates failures in a deterministic order and emits a machine-readable reason code. For example, `FOCUS_TOO_LOW` requests a stable refocus; `EXCESSIVE_GLARE` derives a left/right tilt from the glare centroid; and `POSE_NOT_VERIFIED` requests the active view again.

A correction is not accepted solely because the new frame crosses a threshold. Focus must improve by at least 15%; glare must fall by at least 20%; coverage must improve by five percentage points; and pose must improve by eight points while passing its active rule. When all four views pass, the system transitions to review rather than issuing an autonomous certificate.

## Explainability and provenance

Every attempt records the requested view, timestamp, SHA-256 source hash, OpenCV version, processing time, metrics, thresholds, reason code, instruction, verification result, and state transition. Accepted views are presented together in review. The evidence ZIP contains `audit.json`, a manifest, and one rendered OpenCV overlay per accepted view; original photographs are not included as separate files.

## Validation results

The reproducible browser path generated five synthetic captures: a rejected blurred front view, its accepted correction, and three accepted remaining views. The final audit contained four admitted views, five decisions, and sixteen timestamped audit events.

Eleven unit and integration tests pass. Twelve representative standalone worker runs averaged 81.8 ms of in-worker analysis and peaked at 76.8 MB RSS. Three near-limit 11,997,184-pixel end-to-end requests peaked at 262.7 MB combined Node-plus-Python RSS and 1.056 seconds wall time. Full measurements and scope limitations are in [`validation_report.md`](validation_report.md).

## Security and privacy safeguards

The public MVP accepts JPEG, PNG, and WebP only. The server rejects data above 7 MB, image dimensions above 12 MP or 4096 pixels per side, worker output above eight million characters, and workers exceeding fifteen seconds. Device labels explicitly exclude IMEI, serial number, owner name, and personal information. Source images remain in browser memory and request-scoped temporary storage only.

## Limitations and next work

Synthetic fixtures prove orchestration and reproducibility, not real-world accuracy. Thresholds require calibration on consented, representative devices and independent human annotations. Device detection may fail on low-contrast backgrounds, cases, severe occlusion, or unusual form factors. The managed deployment is not a concurrency benchmark. AWS persistence and audit services remain a future adapter and are not active.

## References

[1]: https://docs.opencv.org/5.x/ "OpenCV 5 documentation"
[2]: https://opencv26.devpost.com/ "OpenCV AI Competition 2026"
