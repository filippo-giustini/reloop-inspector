# Testing Instructions

## Review path for judges

This repository documents a prototype concept and technical design. A runnable build is not yet included. Please review the project in the following order:

1. Read the [project proposal](project_proposal.pdf) for the problem, solution and implementation plan.
2. Review the [technical specification](technical_specification.md) for the OpenCV 5 pipeline and Agentic Vision state.
3. Inspect the [data and evaluation protocol](data_and_evaluation_protocol.md) for baseline comparison, split discipline, metrics and failure handling.
4. Review the [capture protocol](capture_and_inspection_protocol.md) and print the [A4 capture mat](ReLoop_Capture_Mat_A4.pdf) if evaluating acquisition reproducibility.
5. Consult [PROJECT_STATUS.md](PROJECT_STATUS.md) for current limitations and evidence not yet claimed.

## Expected future golden path

A competition-ready build should guide an operator through one intentionally reflective capture, reject the frame with a reason code, request a left or right tilt, verify that the requested pose was achieved, register multiple views, show interpretable evidence and require a human confirm, override or defer action.

## Current limitation

There is no working web endpoint in this repository yet. The linked video is a prototype concept walkthrough, not evidence of a deployed application. Testing should therefore focus on internal consistency, reproducibility and implementation readiness of the supplied design and protocol.

## Text for the Devpost field

```text
Prototype concept submission. Please review the public repository, starting with README.md and docs/PROJECT_STATUS.md. The repository contains the project proposal, OpenCV 5 technical specification, data and evaluation protocol, reproducible capture procedure, architecture and printable capture mat. A runnable build and working web endpoint are not yet available; the linked video is explicitly labelled as a prototype concept walkthrough.
```
