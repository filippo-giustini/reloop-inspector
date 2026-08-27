# ReLoop Inspector — Validation Record

## Interactive browser run

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
