# ReLoop Inspector — Data and Evaluation Protocol

**Purpose:** Produce credible evidence for both the Overall and Agentic Vision rubrics without overstating performance.

## Evaluation question

The primary question is not “Can a model detect every scratch?” It is:

> **Does an agent-guided multi-view workflow recover usable evidence and improve decision quality compared with a single fixed image, when the detector and grading policy are held constant?**

This comparison isolates the value of Agentic Vision from the value of the underlying detector.

## Dataset strategy

The final evaluation must use **real images collected by Team MERAVIGLIA** or supplied with explicit permission by a partner. Public datasets may support method development, pre-training or external sanity checks, but they must not substitute for a held-out evaluation that reflects the actual capture workflow.

| Dataset layer | Purpose | Allowed use | Exclusion |
|---|---|---|---|
| Team-collected ReLoop set | Calibration, development and final held-out evaluation | Owned/consented devices, documented capture conditions | No synthetic images in final test split |
| York St John screen-damage set | Crack/non-crack exploration and external sanity check | CC BY 4.0 with attribution; 621.49 MB public dataset[1] | Do not present its reported 92% as ReLoop performance |
| MSD mobile-phone surface dataset | Scratch segmentation pre-training or method exploration | 1,200 defect images plus 20 good images; GPL-3.0; industrial capture[2] [3] | Do not mix the same images across train and test; review GPL implications before distribution |
| Synthetic augmentations | Robustness to glare, blur, perspective and contrast | Training only; transformation parameters logged | Never use as proof of real-world effectiveness |
| Datacluster sample images | Visual inspiration only unless licensed | Public samples may be reviewed | Full set requires commercial contact and has no verified public reuse licence[4] |

## Minimum and target collection plan

| Stage | Devices | Conditions | Frames | Goal |
|---|---:|---|---:|---|
| Calibration pilot | 6 devices: 2 intact, 2 scratched, 2 cracked | 2 lighting conditions × 4 views | 48 | Set acquisition rules and debug failure codes. |
| Development set | 12 additional devices | 3 lighting conditions × 4 views | 144 | Build quality, glare and persistence baselines. |
| Held-out evaluation | 6 additional devices | 3 lighting conditions × 4 views | 72 | Freeze thresholds and compare workflows. |
| **Target total** | **24 unique devices** | Controlled but varied | **264 primary frames** | Credible competition-scale evidence. |

If 24 unique devices are not available, reduce claims rather than duplicating the same devices. Repeated frames of one phone improve robustness testing but do not increase the number of independent units.

## Capture protocol

Each device receives a random study ID unrelated to its serial number. The phone is powered off or shows a blank, non-personal screen. Serial numbers, notifications and personal content must be absent or masked before capture.

For each condition, capture four intended views: front, approximately −15° tilt, approximately +15° tilt and one targeted close-up or edge view. Record device ID, condition, intended view, achieved pose proxy, light source, distance, camera model, timestamp, operator and whether the frame was accepted by the system.

Lighting conditions should include diffuse light, directional overhead light and a deliberately reflective condition. Do not create physical damage for the project. Use already damaged devices or non-destructive removable overlays only for interface testing, never as ground-truth physical defects.

## Ground truth and grading policy

Two graders independently review the accepted reference images using the frozen ReLoop policy. They label `no visible defect`, `scratch`, `crack` or `uncertain`, and annotate a mask or region for visible defects. They also propose A/B/C/Deferred. Graders must not see the system prediction during annotation.

Disagreements are adjudicated in a separate session. Both the initial disagreement and the adjudicated outcome remain in the record. This enables three distinct measures: model-to-adjudicated agreement, model-to-each-grader agreement and human inter-grader agreement.

## Leakage prevention

The split must be performed at **device level**, never at image level. Every view and lighting condition of one physical device belongs to exactly one split. If public datasets are used, near-duplicate checks and source tracking are required. Thresholds and policy rules are frozen before opening the held-out evaluation labels.

| Split | Share by device | Permitted activity |
|---|---:|---|
| Development/train | 60% | Model fitting, feature design and augmentation. |
| Calibration/validation | 20% | Threshold selection and workflow tuning. |
| Held-out test | 20% | One final evaluation after freeze; no retuning. |

With only 24 devices, results are illustrative rather than production estimates. Report device counts alongside frame counts and use bootstrap intervals over devices where feasible.

## Experimental conditions

| Condition | Capture policy | Detector/policy | Human review |
|---|---|---|---|
| **A — Single shot** | First front frame only; no rescan request | Same detector and grade policy as B | Same approval interface |
| **B — Agent-guided** | Quality gate, left/right tilt and targeted rescan when required | Same detector and grade policy as A | Same approval interface |

Randomise condition order where practical. The operator receives equivalent task instructions, except for the agent guidance that defines Condition B. Log all failed attempts and abandoned sessions.

## Primary metrics

| Metric | Calculation | Interpretation |
|---|---|---|
| Evidence recovery rate | Low-confidence sessions that become sufficient after a verified rescan ÷ sessions receiving a rescan request | Direct Agentic Vision contribution. |
| Task completion rate | Sessions with human-approved certificate within two rescans ÷ started sessions | End-to-end effectiveness. |
| Human–system agreement | Cohen’s kappa or percent agreement with adjudicated grade | Decision consistency; disclose small-sample limits. |
| Defect precision | True positive defect predictions ÷ all positive defect predictions | False-alarm control. |
| Defect recall | True positive defect predictions ÷ all ground-truth defects | Missed-defect control. |
| Median time to certificate | Median elapsed time from first frame to approval | Operational usability. |
| Override rate | Human-overridden proposals ÷ proposals shown | Human-control demand and failure signal. |
| Rescan usefulness | Rescans that improve the blocked quality/evidence score ÷ verified rescans | Quality of next-action selection. |

## Secondary and cloud metrics

Report p50 and p95 latency for frame analysis, state transition and certificate generation. Record AWS error rate, cold-start behaviour if relevant, storage per session and estimated cost for the measured evaluation workload. Do not extrapolate industrial cost without stating assumptions.

## Failure taxonomy

| Code | Failure | Evidence to retain | Intended mitigation |
|---|---|---|---|
| `ROI_NOT_FOUND` | Device boundaries not detected | Raw frame and overlay | Better mat contrast or manual alignment guide. |
| `FOCUS_LOW` | Motion or distance blur | Focus score and prompt | Stabilise, move closer or recapture. |
| `GLARE_HIGH` | Reflection obscures region | Glare mask and ratio | Request opposite tilt or diffuse light. |
| `POSE_NOT_REACHED` | Operator did not perform action | Requested vs measured pose | Animated guidance and tolerance band. |
| `DEFECT_AMBIGUOUS` | Candidate unstable across views | Registered masks | Targeted close-up or defer. |
| `OOD_DEVICE` | Unsupported size/shape/surface | ROI geometry and model score | Stop and disclose unsupported input. |
| `HUMAN_DISAGREEMENT` | Graders or operator disagree | Independent labels and rationale | Adjudication; do not hide disagreement. |
| `CLOUD_FAILURE` | Upload/inference/report error | Request ID and latency trace | Retry, local queue and screen-share fallback. |

## Analysis rules

All metrics must be generated from versioned logs, not copied manually into the report. Publish the metric definitions before the test run. Show counts and denominators. Present confusion matrices and representative successes and failures. If the sample is too small for a stable interval, say so directly.

No target accuracy should appear in promotional material before measurement. The 2025 academic paper’s 70.4% precision and 8 ms YOLOv8x inference result provide feasibility context only and are not comparable without the same data and protocol.[5]

## Required evaluation artefacts

The repository must contain a data card, consent/provenance log, annotation guide, split manifest, frozen configuration, evaluation command, raw metric JSON, summary CSV, confusion matrix image, latency table, state traces for at least three sessions and a documented folder of failure cases. Public distribution may omit raw images when consent or licence terms require judge-only access.

## References

[1]: https://yorksj.figshare.com/articles/dataset/Data_Set_Smartphone_Screen_Damage_Detection_zip/29108471 "York St John University — Smartphone Screen Damage Dataset"
[2]: https://github.com/jianzhang96/MSD "MSD — Mobile Phone Screen Surface Defect Segmentation Dataset"
[3]: https://datasetninja.com/mobile-phone-defect-segmentation "Dataset Ninja — MSD Dataset Analysis"
[4]: https://github.com/datacluster-labs/Cracked-Screen-Image-Dataset "Datacluster Labs — Cracked Screen Image Dataset"
[5]: https://link.springer.com/article/10.1007/s13243-024-00147-2 "Deep learning enabled computer vision in remanufacturing and refurbishment applications"
