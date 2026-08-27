# ReLoop Inspector

**ReLoop Inspector is a public Agentic Vision prototype for guided, multi-view smartphone cosmetic inspection.** OpenCV 5 measures whether each capture is usable, selects a reason-specific corrective action when evidence is weak, verifies the correction, and admits only validated views into a human-reviewed evidence set.

## Public demo

<https://reloopinspec.com/>

## Video demo

<https://youtu.be/6PMXXPGAg_s>

For the fastest judge path, select **Run reproducible demo**. The labelled synthetic reference sequence uses the same public OpenCV 5 endpoint as manual uploads and reaches human review and evidence export without requiring personal data.

> **Prototype boundary:** ReLoop provides cosmetic decision support. It does not certify battery condition, authenticity, water resistance, electrical safety, or resale value. Candidate surface marks are heuristic observations, not validated defects.

## What is runnable

The repository contains a functioning browser flow, a Node/tRPC API, and a Python worker running `opencv-contrib-python-headless==5.0.0.93`. The public interface supports manual image upload or camera capture and a deterministic reference run using clearly labelled synthetic fixtures.

| Capability | Implementation |
|---|---|
| Guided acquisition | Four required views: front, left oblique, right oblique, back |
| Quality gates | Focus, exposure, black/white clipping, glare, coverage, pose |
| Agentic action | OpenCV reason codes select the next corrective instruction |
| Verification | A corrective capture must both pass its threshold and improve the rejected metric |
| Explainability | Numeric metrics, thresholds, reason codes, overlays, provenance hashes |
| Human control | Confirm, correct with rationale, or defer |
| Evidence export | Privacy-safe ZIP with audit JSON, manifest, and four rendered OpenCV overlays |
| Storage boundary | Real uploads remain browser-only; declared synthetic fixtures and overlays are persisted in managed storage for reproducibility |

## Agentic Vision loop

1. **Perceive:** OpenCV measures the submitted frame and renders an explainability overlay.
2. **Decide:** A deterministic state machine maps failed gates to a reason code and one corrective instruction.
3. **Act:** The interface asks for the specific missing or corrected view.
4. **Verify:** The next frame must measurably improve the failed metric and pass the active gate.
5. **Compose:** Accepted views form a provenance-linked evidence set.
6. **Review:** A human confirms, corrects, or defers the prototype proposal.

## Architecture

```text
React capture UI
  └─ tRPC public API
      └─ Node request guard
          ├─ MIME / 7 MB / 12 MP / 4096 px validation
          ├─ temporary request-scoped file
          └─ Python OpenCV 5 worker (15 s timeout)
              ├─ quality + pose metrics
              ├─ corrective verification
              ├─ evidence overlay
              └─ next-action decision
  ├─ browser-session inspection aggregate
  │   ├─ multi-view evidence
  │   ├─ human review
  │   └─ privacy-safe ZIP export
  └─ synthetic-reference persistence only
      ├─ managed object storage for fixture images and overlays
      └─ database state, decisions, review, audit and durable rate limit
```

AWS interfaces are documented as a future adapter. **AWS is not represented as active in this managed demo.**

## Run locally

Prerequisites are Node.js 22, pnpm, Python 3.11+, and a Linux environment compatible with the pinned OpenCV wheel.

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r python/requirements.txt
pnpm install --frozen-lockfile
pnpm dev
```

Open the URL printed by the server. Select **Run reproducible demo** for the fastest judge path, or create a consented session and upload your own smartphone images.

## Verify

```bash
pnpm check
pnpm test
pnpm build
python3 scripts/profile_vision_worker.py
python3 scripts/profile_managed_path.py
```

The current suite contains fifteen passing tests covering runtime version, payload validation, decompression-bomb protection, corrective verification, four-view completion, evidence packaging, human-review decisions, file signatures, source-hash mismatch and durable rate-limit rejection through tRPC.

## Measured runtime envelope

Twelve representative worker runs peaked at 76.8 MB RSS. Three end-to-end Node→Python runs used synthetic 4096×2929 inputs immediately below the 12 MP guard and peaked at 262.7 MB combined process-tree RSS, with a maximum wall time of 1.056 seconds. These are isolated-request runtime measurements, not concurrency or accuracy claims. See [`docs/validation_report.md`](docs/validation_report.md).

## Documentation

| Document | Purpose |
|---|---|
| [`docs/testing_instructions.md`](docs/testing_instructions.md) | Judge path and expected outcomes |
| [`docs/technical_report.md`](docs/technical_report.md) | Architecture, OpenCV methods, state machine, safeguards |
| [`docs/evaluation_protocol.md`](docs/evaluation_protocol.md) | Current verification and real-device validation plan |
| [`docs/validation_report.md`](docs/validation_report.md) | Tests, browser run, runtime profiles |
| [`docs/architecture_decision.md`](docs/architecture_decision.md) | Managed-demo decision and AWS boundary |
| [`docs/devpost_submission_copy.md`](docs/devpost_submission_copy.md) | Updated submission text |
| [`docs/final_video_script.md`](docs/final_video_script.md) | Three-minute prototype demo script |
| [`docs/submission_checklist.md`](docs/submission_checklist.md) | Remaining publication steps |

## Team

**Team MERAVIGLIA** — Filippo Giustini, Design Strategy; Gaia Provvedi, Business Design.

## License

MIT. Synthetic fixtures are generated by repository code and are not customer data.
