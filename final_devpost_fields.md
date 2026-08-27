# Final Devpost Fields — ReLoop Inspector

Use the values below for the final submission. Do not add AWS tags or claims: AWS is documented as a future adapter and is not active in the managed prototype.

| Devpost field | Final value |
|---|---|
| Project name | `ReLoop Inspector` |
| Elevator pitch | `When vision is uncertain, ask for a better view.` |
| Video demo link | `https://youtu.be/6PMXXPGAg_s` |
| Repository URL | `https://github.com/filippo-giustini/reloop-inspector` |
| Working web endpoint | `https://reloopinspec.com/` |
| Try it out — demo | `https://reloopinspec.com/` |
| Try it out — code | `https://github.com/filippo-giustini/reloop-inspector` |
| File upload | `Team_MERAVIGLIA_ReLoop_Inspector_Devpost_FINAL.zip` |

## Category and special-prize selections

Select **Agentic Vision** when it appears as the competition category or award consideration. Leave sponsor prizes unselected unless the option explicitly applies without requiring an active sponsor technology integration. In particular, do **not** select or claim an AWS implementation: the public prototype uses managed storage and database services, while AWS remains future work.

## Built with

Add the following tags where Devpost accepts them:

`OpenCV 5`, `Python`, `NumPy`, `Computer Vision`, `Image Processing`, `Agentic AI`, `Human-in-the-loop AI`, `Responsible AI`, `React`, `TypeScript`, `Node.js`, `tRPC`, `Tailwind CSS`, `Vitest`.

## Testing instructions

```text
Open https://reloopinspec.com/ and select “Run reproducible demo”. No upload or personal data is required. Observe the first synthetic front capture being rejected with FOCUS_TOO_LOW, then the corrective capture being measured and accepted. The same OpenCV-driven loop admits left oblique, right oblique and back views. At review, choose Confirm, Correct or Defer, then download the evidence ZIP and inspect audit.json, evidence/manifest.json and four OpenCV overlays. Real uploads remain browser-only; only the clearly labelled synthetic reference run is persisted for reproducibility.
```

## About the project

Copy the complete Markdown story from `devpost_submission_copy.md`, sections **Inspiration** through **Next steps**. Keep the stated boundary that synthetic fixtures prove orchestration and reproducibility, not real-device accuracy.

## Team contributions

### Filippo Giustini

```text
I led the design strategy and product framing for ReLoop Inspector. I designed the end-to-end inspection experience, the Agentic Vision workflow, the human-in-the-loop decision model, and the evaluation framework. I also shaped the project narrative, visual communication, and competition submission, translating a complex computer-vision system into a clear and responsible product proposition.
```

### Gaia Provvedi

```text
I led the business-design contribution for ReLoop Inspector. I mapped the refurbishment workflow and stakeholders, helped define the operator value proposition and responsible-use boundaries, and structured the assumptions, validation questions and future pilot design. I also contributed to the human-review model and to framing how explainable evidence can reduce inconsistent grading and support more defensible decisions.
```

## Final pre-submit check

Confirm that the gallery images remain in the approved numerical order, the YouTube URL resolves to the three-minute Unlisted video, the endpoint opens without authentication, the repository is public, and the uploaded ZIP matches the external delivery manifest. Do not replace measured values with estimated accuracy claims, and do not present AWS as active.
