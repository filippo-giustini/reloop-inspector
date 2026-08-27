# ReLoop Inspector — Managed Demo Architecture

## Decision

The public demo runs as one managed application container. React provides the guided capture and human-review experience, the Node server exposes typed procedures, and a short-lived Python worker executes OpenCV 5 for every submitted image. The worker finishes within the request lifecycle and never runs as a detached process.

The first release is deliberately **privacy-first**. Real uploaded source images remain in the browser and are sent to the server only for immediate analysis. Temporary server files are removed before the response completes. The browser retains the accepted multi-view evidence for the active session and can download an audit package. Only the clearly declared synthetic reference run persists source fixtures and OpenCV overlays in managed object storage, with database records for state, decisions and audit events. The public demo does not persist personal images or claim that AWS is active.

## Runtime options considered

| Approach | Benefit | Constraint | Decision |
|---|---|---|---|
| Managed single-container demo | Fast public delivery, one reproducible runtime, typed frontend/backend integration | 1 vCPU, 512 MB, request-scoped compute | Selected for the MVP |
| Direct AWS implementation | Strongest match to the competition infrastructure requirement | Requires AWS account, IAM, credits, deployment and observability setup | Prepared as a later adapter, not claimed as active |

## Request lifecycle

1. The browser validates file type and size, then downsizes the capture to a bounded JPEG.
2. A public typed procedure receives the image, requested view, accepted-view state and optional prior measurements.
3. The Node server writes the bytes to a random directory under `/tmp` and starts the Python worker.
4. OpenCV calculates focus, luminance, clipping, glare, device coverage, orientation and perspective evidence.
5. A deterministic state machine selects one result: reject with a corrective instruction, accept the requested view, or complete the evidence set.
6. The worker returns numeric measurements, thresholds, reason codes and an annotated overlay.
7. Temporary files are deleted before the procedure returns.
8. The browser stores the session evidence locally and requires a human confirm, correct or defer decision.

## Agentic Vision rule

The next action must be a deterministic consequence of OpenCV output. A failed focus metric requests a steadier, closer capture. Excessive glare requests a directional tilt based on the glare centroid. Exposure failures request a lighting correction. Pose or coverage failures request alignment. A corrective capture is accepted only when the failed metric crosses its threshold or improves by the minimum verification margin.

## Explainability contract

Every decision includes the OpenCV version, measured values, thresholds, pass/fail state, a stable reason code, a human-readable explanation and an overlay that shows relevant evidence. The proposed cosmetic band is explicitly labelled as an unvalidated prototype heuristic and can never bypass human review.

## Supported and unsupported scope

| Supported in the MVP | Explicitly unsupported |
|---|---|
| Front, left-oblique, right-oblique and back captures | Battery or electrical safety |
| Focus, exposure, clipping, glare, pose and coverage checks | Water resistance or hidden functionality |
| Reason-specific corrective instructions | Authenticity, ownership or regulatory certification |
| Multi-view evidence composition | Automatic resale pricing |
| Human confirm, correction and deferral | Autonomous final certification |

## AWS readiness

The analysis contract is transport-independent. A later deployment can replace the in-process worker with an AWS-hosted analysis service and persist consented evidence to S3, session state to DynamoDB and traces to CloudWatch. Until that deployment exists and is tested, the product and submission must describe AWS only as a planned architecture.
