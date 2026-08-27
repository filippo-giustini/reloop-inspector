# Three-Minute Functional Prototype Video

The video must use recordings of the running interface. Do not reuse the earlier concept-only video as the final technical demo.

| Time | Screen action | Voice-over |
|---:|---|---|
| 0:00–0:18 | Hero and OpenCV live badge | “A single smartphone image can hide the evidence an inspector needs. ReLoop Inspector does not guess when vision is uncertain. It asks for a better view.” |
| 0:18–0:35 | Setup screen and responsible boundary | “This is a functioning, privacy-first prototype. It supports cosmetic evidence only, excludes safety and authenticity claims, and keeps source images inside the active browser session.” |
| 0:35–0:55 | Select Run reproducible demo | “For a reproducible judge path, the browser creates clearly labelled synthetic fixtures and sends them through the same public OpenCV 5 endpoint used for uploaded captures.” |
| 0:55–1:20 | Show blurred front rejection and metrics | “The first frame fails the focus gate. OpenCV returns the measured value, threshold, `FOCUS_TOO_LOW` reason code, explainability overlay, and one corrective instruction. The workflow cannot advance.” |
| 1:20–1:42 | Show corrected front acceptance | “The next frame is compared with the rejected attempt. It must improve focus by at least fifteen percent and pass the gate. Only after verification does the front view enter the evidence set.” |
| 1:42–2:02 | Progress through oblique and back views | “The same loop verifies left oblique, right oblique, and back. Pose is computed from device-edge asymmetry, so a filename or scripted sequence cannot force acceptance.” |
| 2:02–2:25 | Review screen and evidence strip | “Four accepted views now support a transparent prototype proposal. The reviewer sees the overlays, metrics, hashes, decision trace, and limitations—not just a grade.” |
| 2:25–2:42 | Confirm, correct, defer controls | “A human remains accountable. They can confirm, correct with a written rationale, or defer when the evidence remains uncertain.” |
| 2:42–2:55 | Download evidence ZIP and show contents | “The exported package contains the audit JSON, a manifest, and one OpenCV overlay per accepted view. Original photographs are not included as separate files.” |
| 2:55–3:00 | Closing hero | Music resolves under the on-screen line: “ReLoop Inspector. When vision is uncertain, ask for a better view.” |

Record at 1920×1080, hide personal browser data, keep the cursor slow, and upload to YouTube as **Unlisted**. Include English captions and the public demo and repository links in the description.

## Produced video

The generated final cut is `ReLoop_Inspector_Functioning_Prototype_3min.mp4`. It is exactly 180 seconds, 1920×1080 at 30 fps, with H.264 video, AAC stereo audio at 48 kHz, integrated loudness of -16.0 LUFS and a clearly audible original background score. All interface imagery comes from the functioning managed prototype; no generated interface animation is used.
