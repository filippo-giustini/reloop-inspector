# Testing Instructions

Public demo: <https://reloopinspec-tvqagq6k.manus.space/>

## Fast judge path

1. Open the public ReLoop Inspector endpoint.
2. Select **Run reproducible demo**. No personal data or device images are required. The interface explicitly declares that this synthetic reference evidence is persisted in managed storage; manual uploads remain browser-only.
3. Observe the first synthetic front capture being rejected with `FOCUS_TOO_LOW`.
4. Observe the next front capture being verified against the rejected metric and accepted.
5. Follow the automatic progression through left oblique, right oblique, and back.
6. Review the four-view evidence set and the prototype cosmetic proposal.
7. Select **Confirm proposal**, **Correct**, or **Defer**. Correction and deferral require rationale.
8. Download **evidence ZIP** and verify `audit.json`, `evidence/manifest.json`, and four overlay images.

Expected runtime evidence is OpenCV 5.0.0, four accepted views, five total attempts, and a decision trace containing one rejection plus the corrective verification.

## Manual image path

Create a non-identifying device label, accept the consent notice, and upload JPEG, PNG, or WebP images. Capture the requested view on a contrasting background under soft light. The system should reject weak evidence with one reason-specific instruction and should not advance until the correction is measured.

Do not upload an IMEI, serial label, owner name, personal photograph, or confidential information.

## Local verification

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r python/requirements.txt
pnpm install --frozen-lockfile
pnpm check
pnpm test
pnpm dev
```

For production validation, run `pnpm build`. Runtime benchmarks are reproducible with `python3 scripts/profile_vision_worker.py` and `python3 scripts/profile_managed_path.py`.

## Known limitations

Synthetic fixtures validate orchestration, not accuracy. Candidate surface marks are not validated defects. The system does not test functionality, battery, authenticity, water resistance, or safety. AWS is not active in the managed prototype.
