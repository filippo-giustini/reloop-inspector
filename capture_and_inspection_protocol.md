# ReLoop Inspector — Capture and Inspection Protocol

## Purpose

This protocol standardises the collection of evidence for development, evaluation and the live judge demonstration. Use it with `ReLoop_Capture_Mat_A4_Print.pdf`, printed at 100% scale on matte A4 paper.

## Physical setup

Place the printed mat on a rigid, neutral surface. Confirm that the 50 mm scale bar measures 50 mm after printing. Use diffuse illumination when collecting reference images; introduce directional glare only in the controlled reflective condition. Keep all four ArUco markers visible. Mount or hold the camera approximately perpendicular to the mat and disable beauty filters or automatic image enhancement where possible.

The central guide is 78 × 160 mm and is intended as a generic alignment frame, not a device specification. Select one supported smartphone form factor for the MVP and document its actual dimensions. Do not force an oversized device into the guide.

## Device preparation

The device must be owned by the team, borrowed with permission or provided by a partner with written consent. Power it off or display a blank, non-personal screen. Remove notifications, personal images and account information. Record a random study ID; do not record IMEI, serial number or owner identity in the image file name.

Clean the surface only when the study condition requires a clean baseline. Do not repair, polish or intentionally damage a device. Protective films must be recorded because they can create scratches and reflections that do not belong to the device surface.

## Standard capture sequence

| Sequence | Instruction | Verification |
|---:|---|---|
| 1 | Place device front-side up, aligned with `CAMERA / TOP`. | Four markers visible; ROI inside guide; focus/exposure pass. |
| 2 | Capture the front reference view. | Pose labelled `front`; glare below calibrated limit. |
| 3 | Tilt the device approximately 15° to the operator’s left. | Marker/device homography confirms direction and tolerance. |
| 4 | Tilt approximately 15° to the operator’s right. | Requested pose achieved; useful region remains visible. |
| 5 | Follow one targeted instruction if evidence is insufficient. | Rescan addresses the recorded reason code. |
| 6 | Return device to the flat reference position. | Final overview and trace completeness confirmed. |

For the rear-cover workflow, repeat the same sequence with the device rear-side up. Front and rear sessions must remain separately identifiable.

## Controlled lighting conditions

| Condition | Setup | Purpose |
|---|---|---|
| Diffuse | Large soft source or indirect daylight, no hard reflection | Reference evidence and annotation. |
| Directional | Single overhead/side light | Generalisation and moderate reflection. |
| Reflective challenge | Deliberate narrow light reflection across the device | Trigger Agentic Vision rescan and test glare recovery. |

Do not mix lighting conditions within a condition record. Photograph the setup once per collection day and record camera model, approximate distance and light description.

## File naming

Use the pattern:

```text
RL_<device-id>_<surface>_<condition>_<view>_<attempt>.<ext>
```

Example:

```text
RL_D012_front_reflective_tilt-left_02.jpg
```

The manifest, not the filename, stores labels, operator, timestamps and consent/provenance.

## Live judge demonstration setup

Use one phone with a known visible defect and one pre-validated lighting arrangement. Start with a reflective frame that reliably triggers `GLARE_HIGH`. Rehearse the corrective tilt so OpenCV verifies the requested pose. Prepare a second device or a recorded trace as a fallback, but disclose when the fallback is used.

The live demonstration must show the raw frame, OpenCV quality overlay, reason code, next action, pose verification, defect evidence, human approval and AWS certificate. Do not hide latency or switch to an unlabelled pre-recorded result.

## Safety and ethics

Only cosmetic exterior inspection is performed. Do not open batteries, expose electrical components or handle swollen/damaged lithium-ion batteries. Do not imply that the resulting grade certifies electrical or battery safety. Keep damaged devices in accordance with local safety guidance and partner procedures.

## Quality checklist

| Check | Pass condition |
|---|---|
| Print scale | 50 mm bar measures 50 mm. |
| Marker integrity | IDs 0–3 are sharp, unobstructed and not cropped. |
| Privacy | No personal content, serial number or owner identifier is visible. |
| Provenance | Consent/ownership and licence status recorded. |
| Device split | Device ID belongs to only one train/validation/test split. |
| Capture metadata | Surface, condition, view and attempt recorded. |
| Failure honesty | Rejected frames and abandoned sessions retained in logs. |
