# ReLoop Inspector — Validation Record

## Interactive browser run

### Persistence regression found during final integration

The first end-to-end run after enabling managed synthetic persistence stopped after the measured `FOCUS_TOO_LOW` decision with `Expected image/png data URL`. OpenCV analysis itself succeeded, but the persistence boundary rejected the actual overlay MIME type. This is tracked as a blocking integration defect and must be corrected and re-run before the persistence items are closed.

The persistence boundary was corrected to accept the JPEG overlay emitted by `vision_worker.py`. The next browser run passed the previous failure point: the first `FOCUS_TOO_LOW` analysis completed and the runtime panel entered `Saving synthetic evidence…` without the MIME error. Full five-capture completion and database verification remain required before closing the defect.

The corrected browser run then completed all five OpenCV analyses. The review screen showed four accepted views, five total attempts and `Managed synthetic evidence` in the runtime panel, proving that each capture crossed the storage boundary without interrupting the Agentic Vision state machine. The human confirmation action was exercised; final audit-screen and database-row verification remain pending.

The indexed browser click did not visually transition the review, matching the earlier automation limitation, so the same `Confirm proposal` control was invoked directly through the page DOM and returned `clicked`. The next verification must confirm the audit screen and the final review snapshot in the database before this result is considered complete.

The subsequent browser state displayed the audit-ready `B-candidate` outcome, the four evidence thumbnails and both export controls. A database verification against the most recently updated session returned `status=completed`, four accepted views, five decision records, a non-null human review, five persisted captures, four accepted captures, five distinct source object keys, five distinct overlay object keys, sixteen audit events and one `review_completed` event. The stored session is explicitly labelled `synthetic-reference-device`; real user uploads remain request-scoped and browser-only.

The persistence regression is closed. The managed storage path is intentionally limited to declared synthetic reference fixtures. Each session stores a hashed access token and a 24-hour expiry timestamp; automated object deletion is not claimed in this MVP.

## Final persistence and container validation

The managed persistence path was hardened with server-side SHA-256 recomputation, JPEG/PNG signature validation, a seven-megabyte evidence limit and a database-backed public-endpoint rate limit of eighteen writes per hashed client key per hour. The counter is atomic and shared across cold starts and managed instances. The tRPC test exercises eighteen source-hash rejections followed by a nineteenth rate-limit rejection through the actual endpoint contract. TypeScript checks and all fifteen Vitest cases passed.

The single-container package was validated with `dockerfilelint` and reported no issues. The packaged Python worker health check returned OpenCV `5.0.0` and NumPy `2.2.6`. The production Node bundle was started with `NODE_ENV=production` on an isolated port and served the compiled 368,355-byte application shell successfully over HTTP. A full container-image build was not available in the sandbox, so the managed platform build remains the publication-time verification step.

The final disclosure copy was visually reviewed at 1280×720 and 390×844. Both layouts clearly distinguish request-scoped real uploads from managed persistence of the synthetic reference run. The desktop evidence-lab hierarchy remains intact, and the mobile layout keeps the disclosure, consent, primary capture action and reproducible-demo action readable without horizontal overflow.

The final video was rerendered after the privacy-model update. Its revised cards and narration distinguish browser-only real uploads from persistence of the declared synthetic reference evidence. Visual review passed, and the file measures 180.000 seconds, 1920×1080 at 30 fps, H.264/AAC stereo, 9,124,570 bytes and -15.9 LUFS integrated loudness with no decode errors.

A final repository-wide wording audit found no remaining occurrences of the superseded claims that all source images stay in-browser, that no source files are persisted, or that the suite contains eleven tests. README, technical report, architecture decision, evaluation protocol, testing guide, Devpost copy, video script and visual cards now state the same boundary: real uploads are browser-only and request-scoped; only the declared synthetic reference path is persisted.

The final audit was rerun across forty-one text assets, explicitly including the root `README.md`, every project Markdown/text/caption file, and the public video materials under `reloop-functional-video` such as `YouTube_upload.md`, `narration_plan.md`, subtitles and frame copy. Exact obsolete formulations returned zero matches.

The managed demo was exercised end to end with clearly labelled synthetic fixtures generated in the browser and submitted to the same public OpenCV endpoint used for uploaded images.

| Check | Observed result |
|---|---|
| Runtime health | OpenCV 5.0.0 reported live |
| Corrective loop | First blurred front view rejected with `FOCUS_TOO_LOW` |
| Corrective verification | Second front view measured against the rejected attempt and admitted only after improvement |
| Multi-view progression | Front, left oblique, right oblique and back admitted in order |
| Total analysis attempts | 5 |
| Accepted views | 4 |
| Human review | Confirm, correct and defer actions visible; confirm path exercised |
| Audit trace | 16 timestamped events in the metadata JSON run |
| Privacy boundary | Source photographs absent from the metadata export; provenance hashes retained |
| Evidence package | Browser-downloaded ZIP passed integrity checks with `README.txt`, `audit.json`, `evidence/manifest.json` and four accepted-view OpenCV overlays |
| Source-image exclusion | Audit contains no `sourceDataUrl`; overlay references resolve to packaged evidence files |

## Automated checks

`pnpm check` passes. Vitest executes eleven tests across authentication, OpenCV/state transitions, API payload validation, decompression-bomb protection, evidence packaging and human-review decisions. The ZIP unit test verifies the presence of `audit.json`, `evidence/manifest.json` and a rendered accepted-view overlay.

The production build completes successfully for the React client and bundled Node server. Runtime log review found no browser-console errors and no failed network requests during the end-to-end inspection run.

## Managed-runtime profile

The reproducible profiler in `scripts/profile_vision_worker.py` executed twelve isolated analyses across the four synthetic reference views. Average wall time was **0.462 s**, maximum wall time **0.481 s**, average in-worker processing **81.8 ms**, and maximum in-worker processing **98 ms**. Peak observed Python worker RSS was **76.8 MB**, leaving substantial headroom within the 512 MB managed-runtime envelope for one request at a time. Average JSON response size was **68.1 KB** and the maximum was **71.7 KB**.

These results are a single-request runtime profile, not a concurrency benchmark or an accuracy claim. Raw measurements are stored in `docs/runtime_profile.tsv` and the computed summary in `docs/runtime_profile_summary.json`.

Runtime guards reject payloads above 7 MB, decoded images above 12 megapixels or 4096 pixels on either side, worker runs above 15 seconds, and worker output above 8 million characters. The Python worker downsizes accepted images to a maximum 1800-pixel side before expensive analysis.

### Near-limit end-to-end benchmark

The full `analyseCapture` path was also profiled in an isolated Node process together with its descendant Python worker. Three synthetic JPEG requests were generated at **4096×2929 pixels (11,997,184 pixels)**, immediately below the 12-megapixel guard. OpenCV downscaled each accepted input to 1800×1287 before analysis.

Maximum end-to-end wall time was **1.056 s**, maximum application-measured time **627 ms**, maximum worker processing time **207 ms**, maximum combined Node-plus-Python process-tree RSS **262.7 MB**, and maximum serialized response **113.1 KB**. This leaves approximately **249 MB of measured headroom** below the 512 MB container limit and more than **13.9 s of wall-time headroom** below the 15-second worker timeout for the tested single-request path.

Raw results are stored in `docs/managed_path_profile.tsv` and `docs/managed_path_profile_summary.json`. The benchmark is deliberately limited to isolated requests and does not claim concurrency capacity or production accuracy.

## Responsive visual review

Final full-page screenshots were captured at 1440×900 and 390×844. The editorial evidence-lab hierarchy, form controls, calls to action, runtime evidence and decision-trace sections remain legible without horizontal overflow. On mobile, the workflow stacks into a single reading order while preserving the responsible-boundary and prototype disclosures.

## Declared limitations

The synthetic fixture path proves orchestration and reproducibility, not production accuracy. Candidate surface marks are heuristic outputs and are not validated defects. The prototype does not certify battery condition, authenticity, water resistance or safety. AWS is documented as a future adapter and is not represented as active in the managed demo.

## Final video asset validation

The final submission asset is `ReLoop_Inspector_Functioning_Prototype_3min_FINAL.mp4`. The exported MP4 is exactly 180.000 seconds at 1920×1080 and 30 fps, with H.264 video, AAC stereo audio at 48 kHz, 8.8 MB file size and -16.0 LUFS integrated loudness. A full decode completed without errors.

The video contains a 29.5-second deterministic recording of the live browser prototype between 00:35 and 01:04.5. Timestamped frames extracted from the final MP4 verify the sequence: at 00:40 the first image is rejected with the red focus failure state and explainability metrics; at 00:42 the corrected front capture shows the green admission state and verification row; frames at 00:44 and 00:46 show the subsequent measured pose/evidence transitions; at 00:48 the workflow has four accepted views and enters human review; frames at 00:56 and 00:58 show confirm, correct and defer controls; at 01:02 the confirmed outcome exposes the audit package and download controls.

The complete-timeline contact sheet and a dedicated live-section contact sheet were reviewed against the exported final MP4. Stable editorial frames are used outside the live recording, with no generated interface animation or interpolated zoom, eliminating the vibration reported in the earlier concept video.

Additional sub-second extraction at 00:47.25, 00:47.50 and 00:47.75 confirms the last measured capture state immediately before the transition; at 00:48.00 the review screen is visible with all four evidence thumbnails admitted. This verifies that the front, two oblique and back evidence set was completed in the exported video before human review.
