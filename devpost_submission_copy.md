# Devpost Submission Copy

## Elevator pitch

**When vision is uncertain, ask for a better view.**

## About the project

### Inspiration

Cosmetic grading of refurbished smartphones depends on visual evidence, yet a single image can hide the very detail an operator needs to judge. Blur, clipped highlights, glare, framing, and pose are not merely image-quality issues: they determine whether the evidence is trustworthy.

We built ReLoop Inspector around a simple principle: **when visual evidence is insufficient, the system should not guess—it should request and verify the missing evidence.**

### What it does

ReLoop guides an operator through four views of a smartphone. OpenCV 5 measures focus, exposure, clipping, glare, device coverage, and pose. When a frame fails, a deterministic Agentic Vision loop selects one corrective action. The next frame must measurably improve the rejected condition before it can enter the evidence set.

After front, left-oblique, right-oblique, and back views are accepted, ReLoop composes the overlays, provenance hashes, metrics, and decisions into a review screen. A human confirms, corrects with rationale, or defers the proposal. The final privacy-safe ZIP contains an audit record, a manifest, and one rendered OpenCV overlay for each accepted view.

### How we built it

The interface uses React, TypeScript, Tailwind CSS, tRPC, and Node.js. Each image is guarded by MIME, byte-size, decoded-dimension, timeout, and output limits before a short-lived Python process runs OpenCV 5.0.0.93. The worker uses contours and quadrilateral geometry for device and pose checks, Laplacian variance for focus, grayscale and HSV statistics for exposure and glare, and Hough line candidates for reviewable surface evidence.

The public MVP is privacy-first: real uploaded images are request-scoped and remain in the browser session. Only the clearly labelled synthetic reference run persists fixture images and OpenCV overlays in managed object storage, with database-backed state, decision, audit and review records. AWS is described as a future adapter and is not presented as active.

### Challenges

The hardest challenge was proving that the loop is genuinely agentic rather than a scripted slideshow. We implemented measurable correction rules, view-specific perspective checks, an auditable state transition history, and failure-first tests. We also separated runtime evidence from accuracy evidence: synthetic fixtures prove orchestration, while real-device accuracy remains future validation work.

### What we learned

The strongest output is not a grade; it is an evidence trail that explains why a frame was accepted, why a new capture was requested, and who made the final decision. Human control and explicit uncertainty make the workflow more credible, not less automated.

### Accomplishments

The repository contains fifteen passing tests, an end-to-end reproducible browser run, managed persistence of the declared synthetic evidence, an evidence ZIP verified after download, and measured single-request runtime headroom below the managed 512 MB limit. Near-limit 11,997,184-pixel requests peaked at 262.7 MB combined Node-plus-Python RSS and 1.056 seconds wall time.

### Next steps

The next stage is a consented real-device study with independent reviewer annotations, device-level train/test separation, reason-code agreement, corrective-verification precision, failure analysis, and calibration of cosmetic proposal thresholds. AWS storage and audit adapters will be activated only when real credentials and deployment evidence are available.

## Built with

OpenCV 5, Python, NumPy, React, TypeScript, Node.js, tRPC, Tailwind CSS, Vitest, Agentic Vision, Computer Vision, Human-in-the-loop AI, Responsible AI.

## Repository

`https://github.com/filippo-giustini/reloop-inspector`

## Working endpoint

`https://reloopinspec.com/`

## Video demo

`https://youtu.be/6PMXXPGAg_s`

## Testing instructions

Open the endpoint and choose **Run reproducible demo**. The first front capture is rejected for low focus; the next is verified and accepted. The system then completes the remaining three requested views, opens human review, and exports a ZIP containing the audit, manifest, and four OpenCV overlays. No upload or personal data is required for this reference path.
