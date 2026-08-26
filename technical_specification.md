# ReLoop Inspector — MVP Technical Specification

**Team:** MERAVIGLIA  
**Version:** 1.0  
**Target:** OpenCV AI Competition 2026 — Agentic Vision and Overall Awards

## Product objective

ReLoop Inspector must prove one technical and product thesis: **a visual system can improve a grading decision by recognising insufficient evidence and actively requesting a more useful view**. The MVP is successful when OpenCV 5 outputs alter the next system action, the requested action is verified, a human approves the result and AWS preserves a reproducible trace.[1] [2]

## Scope contract

| MVP commitment | Acceptance boundary |
|---|---|
| One smartphone form factor | One selected reference family; other devices are out of distribution. |
| Front screen and rear cover | Edges may be captured for pose verification but are not graded. |
| Scratch and crack candidates | No battery, electrical, camera or water-resistance diagnosis. |
| A/B/C proposal | Project-specific policy; not a universal market standard. |
| Guided multi-view capture | Front, left tilt, right tilt and one targeted rescan maximum. |
| Human-approved output | No autonomous final grade. |
| Judge-accessible web demo | Mobile-first PWA plus cloud endpoint and fallback screen-share. |

## Primary user story

> As a refurbishment operator, I want the system to tell me when a view is unusable and what capture action to perform next, so that I can produce a repeatable, evidence-backed cosmetic grade without surrendering the final decision.

## End-to-end acceptance scenario

| Step | Observable behaviour | Pass condition |
|---:|---|---|
| 1 | Operator starts an inspection and places the phone on the reference mat. | Session ID and consent state created. |
| 2 | First frame is blurred or reflective. | OpenCV rejects it and displays one specific corrective action. |
| 3 | Operator follows the instruction. | Pose/quality change is verified, not assumed. |
| 4 | Left and right tilt views are captured. | Coverage state contains all required views. |
| 5 | Multi-view evidence is fused. | Candidate is stable across registered views or marked uncertain. |
| 6 | System proposes a defect label and A/B/C grade. | Confidence and evidence are visible. |
| 7 | Operator confirms or overrides. | Final decision includes actor, time and override reason. |
| 8 | AWS generates a certificate. | Report, masks, metrics and trace are judge accessible. |

## Agentic Vision state machine

![Agentic Vision state machine](reloop_agent_state_machine.png)

The state machine must be explicit and testable. The language layer may explain instructions, but it must not bypass `QualityCheck`, `EvidenceFusion` or `HumanReview`.

## OpenCV 5 pipeline

| Module | Method | Output | Failure action |
|---|---|---|---|
| Device ROI | Contours, quadrilateral scoring, perspective transform | Rectified device crop and corners | Ask operator to align phone inside guide. |
| Focus | Variance of Laplacian or calibrated sharpness score | `focus_score` | Ask operator to stabilise or move to target distance. |
| Exposure | Histogram percentiles and clipped-pixel ratio | `exposure_score` | Ask for more/less light. |
| Glare | Highlight mask, saturation/value thresholds, connected components | `glare_ratio`, mask | Request ±15° tilt away from reflection. |
| Pose | Homography from device corners and/or mat fiducials | yaw/pitch proxy, transform | Verify requested tilt and reject wrong direction. |
| Coverage | Required-view occupancy map | completed/missing views | Select next-best view. |
| Candidate detection | Compact scratch/crack detector or segmenter | class, mask, confidence | Mark uncertain below threshold. |
| Multi-view persistence | Register masks into reference coordinates | stability score | Distinguish stable defect from moving glare. |
| Grade policy | Explicit defect extent and location rules | proposed A/B/C grade | Defer if evidence or confidence is insufficient. |
| Visual explanation | Overlay, mask, quality bars and trace | operator evidence panel | Never display label without source evidence. |

The most important technical novelty is **multi-view persistence**. A physical scratch should remain spatially stable after registration, while a reflection usually changes with viewing angle. The MVP will test this rule as an interpretable feature even if the learned detector remains modest.

## Provisional evidence-sufficiency policy

A view is accepted only when the phone ROI is detected, focus exceeds a calibrated threshold, exposure clipping is below a limit, glare does not obscure the target region and the requested pose is achieved. A grading proposal is emitted only when all mandatory views are accepted and either a candidate persists across registered views or the no-defect evidence is sufficiently complete.

The thresholds will be selected on a calibration subset and frozen before the held-out comparison. Threshold values must appear in configuration files and in the technical report; they must not be silently tuned on test data.

## Provisional grading policy

| Grade | Project definition | Safeguard |
|---|---|---|
| A | No visible crack; no detected scratch above the configured extent threshold in accepted views. | Report `A — project policy`, not “as new”. |
| B | No structural crack; one or more visible scratches within the configured extent/location threshold. | Show masks and estimated extent. |
| C | Any confirmed crack or scratch extent above the Grade B threshold. | Human confirmation required. |
| Deferred | Evidence incomplete, out-of-distribution device or unresolved disagreement. | No grade issued. |

This policy is intentionally local to the prototype because cosmetic grades vary across channels.[3]

## System architecture

![ReLoop Inspector architecture](reloop_architecture.png)

| Layer | Minimum implementation | Stretch implementation |
|---|---|---|
| Capture | React/PWA camera, overlay guide, local feedback | Offline-capable session queue |
| OpenCV runtime | Python OpenCV 5 service in container | Selected preprocessing in browser/WASM |
| Defect model | Lightweight detector or segmenter | Multi-task defect and severity model |
| Orchestration | Deterministic state machine | Constrained tool-using agent with explicit policy |
| AWS compute | Lambda where package limits allow; otherwise ECS/Fargate | Graviton/COOL benchmark as stretch goal |
| Storage | S3 evidence objects; DynamoDB session records | Lifecycle policies and versioned certificates |
| Observability | CloudWatch logs, metrics and request IDs | Dashboard and automated failure alerts |
| Reporting | HTML/JSON certificate | Signed PDF certificate and export API |

## API contract

| Method and path | Purpose | Required response |
|---|---|---|
| `POST /sessions` | Create an inspection | `session_id`, policy version, required views |
| `POST /sessions/{id}/frames` | Upload frame and view intent | Quality scores, accepted flag, next action |
| `GET /sessions/{id}` | Retrieve state | Completed views, candidates, trace, next action |
| `POST /sessions/{id}/review` | Confirm or override | Final label, grade, actor and reason |
| `POST /sessions/{id}/certificate` | Generate artefact | Certificate URL and evidence manifest |
| `GET /health` | Judge readiness | Version, dependencies and service status |

## Evidence event schema

```json
{
  "session_id": "uuid",
  "timestamp": "ISO-8601",
  "state_before": "QualityCheck",
  "opencv": {
    "focus_score": 0.0,
    "exposure_score": 0.0,
    "glare_ratio": 0.0,
    "pose_label": "front|tilt_left|tilt_right|targeted",
    "roi_detected": true,
    "candidate_defects": []
  },
  "decision": {
    "evidence_sufficient": false,
    "reason_code": "GLARE_HIGH",
    "next_action": "TILT_LEFT_15"
  },
  "human": {
    "approval_required": false,
    "override": null
  },
  "model_version": "string",
  "policy_version": "string"
}
```

## Non-functional requirements

| Area | Requirement |
|---|---|
| Latency | Quality feedback should feel immediate; cloud steps must expose measured p50/p95 latency. |
| Reliability | Every state transition is idempotent or recoverable; uploads use retry and request IDs. |
| Security | No hard-coded credentials; least-privilege roles; signed URLs; secrets outside repository. |
| Privacy | No serial numbers or unrelated personal screen content stored; documented retention policy. |
| Reproducibility | Pinned dependencies, Dockerfile, seed where relevant, configuration snapshot and test commands. |
| Observability | Logs include session, state, reason code, model/policy version and latency without personal data. |
| Accessibility | Instructions use text, icons and contrast; critical feedback is not colour-only. |
| Responsible use | Every final grade requires human approval and carries scope/limitations. |

## Repository structure

```text
reloop-inspector/
├── apps/
│   └── capture-pwa/
├── services/
│   ├── vision-api/
│   └── certificate-service/
├── packages/
│   ├── inspection-policy/
│   └── shared-schemas/
├── infrastructure/
│   └── aws/
├── data/
│   ├── README.md
│   └── manifests/
├── evaluation/
│   ├── protocols/
│   ├── notebooks/
│   └── results/
├── tests/
├── docs/
└── README.md
```

## Technical risks and fallbacks

| Risk | Early test | Fallback that preserves the thesis |
|---|---|---|
| Scratch dataset too small | Baseline by end of Week 2 | Use classical anomaly cues plus manual candidate marking; keep agentic acquisition test. |
| Glare detector unstable | Controlled lighting matrix | Use fiducial mat and relative before/after score rather than universal threshold. |
| Model cannot grade reliably | Inter-grader baseline and error review | Limit system to evidence guidance; let human set grade from system evidence. |
| Serverless package too large | Cold-start and deployment test | Move vision service to ECS/Fargate. |
| Live demo network failure | Full rehearsal with throttling | Local capture + cached trace + arranged screen-share. |
| Team capacity overload | Weekly scope gate | Protect one golden path; cut COOL, Bedrock and extra device families first. |

## Definition of Done

The MVP is done only when a judge can open the endpoint, complete the golden inspection path, observe a low-quality frame being rejected, see the requested rescan verified, inspect the OpenCV trace, approve or override the proposal, receive a cloud-backed certificate and reproduce the deployment/test process from the repository instructions.

## References

[1]: https://opencv26.devpost.com/ "OpenCV AI Competition 2026 — Devpost"
[2]: https://opencv26.devpost.com/rules "OpenCV AI Competition 2026 — Official Rules"
[3]: https://www.apkudo.com/post/decoding-cosmetic-grading-process-used-mobile-phones "Decoding the Cosmetic Grading Process for Used Mobile Phones"
