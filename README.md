# VERIFACE

One static browser-local video forensics website. Use **Use camera**, upload a video or choose **Share screen** to inspect a tab/window with a playing face video. No Python server, API keys, video uploads, accounts, fees or cloud inference.

**Untrained research prototype, not a validated real/fake detector.** Five hand-weighted checks: POS pulse/spatial agreement, eye/blink symmetry, frequency-domain ROI differences, texture/noise differences and temporal landmark motion. Combined anomaly index is not a calibrated probability. Quality failures produce inconclusive results.

## Use

Open the GitHub Pages site. File input needs a video the browser can decode, preferably H.264 MP4. First 30 seconds, 250 MB limit. At least 10 seconds, one reasonably large face, good light and low movement are needed for pulse extraction. Processing can take longer than the clip on phones. Keep the tab open; Cancel stops work.

Screen sharing is a browser-native permission prompt on HTTPS. Desktop Chrome/Edge recommended; most mobile browsers do not implement getDisplayMedia. Choose the relevant tab/window, keep one face video playing for 30 seconds and keep VERIFACE visible beside it. No audio capture. Whole-screen video can make the face too small. Capture tracks stop on completion, cancellation or errors. Screen-share denial produces a normal explanatory message. Protected media may be blacked out by the browser.

The video never leaves the browser. Pinned MediaPipe 0.10.21 JS/WASM loads from jsDelivr and the face model loads from Google storage. Those services see asset requests, not video. JSON exports contain a face preview and should be treated as sensitive. There is no analytics or persistent storage.

## Camera input

Use camera requests video only, preferring the front camera at about 640x480/30fps. Allow browser permission and keep one face well lit and still for 30 seconds. A live preview and progress appear. Frames are analyzed locally, not recorded or uploaded; camera tracks stop on completion, cancel or errors, and the preview is cleared. Camera permission denial, missing camera and camera busy each have separate messages. Browser/OS permission and HTTPS are required. Actual phone hardware has not been tested.

Chromium camera test used an authorized prerecorded UBFC face fixture through native getUserMedia: 30 seconds, 100% face coverage, all five branches and honest weak-pulse abstention. Automatic track stop, cancel, denied permission, no-camera, busy-camera and 320/390/1280px layout tests passed. This validates input plumbing, not detector accuracy.

## Live BPM

Camera capture shows Measuring until at least 10 seconds have been sampled. Every 2 seconds it recomputes a rolling up-to-15-second pulse estimate. A BPM number appears only if all report quality gates pass. Weak/unstable pulse, low face coverage, multiple faces, motion or lighting failures show Low signal - BPM withheld. The live number is not a medical measurement. Capture ends at 30 seconds, and the readout explicitly says capture stopped.

Synthetic known-72-BPM signals returned 72 BPM when quality passed; a short window showed Measuring and a multiple-face failure withheld the number. Actual prerecorded face camera fixtures returned low signal rather than a false BPM. These are implementation checks, not medical or detector validation.

## Engineering and evidence

The standalone `index.html` is the deployed app. MediaPipe Tasks FaceLandmarker uses CPU/image mode. RGB samples are extracted from landmark polygons via Canvas. Original JS POS, detrending, FFT spectral band-pass and Welch peak extraction, plus spatial correlations. Eye-aspect ratios detect relative closures. 2D FFT high-frequency ROI spread, local high-pass texture spread and translation/scale-normalized landmark displacement supply the other checks. This differs from the Python reference's Butterworth/texture implementation; reference numbers are not browser benchmark numbers.

Four JS signal tests passed (known-frequency green/POS, FFT round-trip, constant input). Browser file decode, local landmark/signal analysis, quality-based abstention and JSON download passed. Network inspection observed only code/model GETs and local blob reads, no video upload. Screen capture and cancel were exercised with Chromium's synthetic test capture, producing a genuine inconclusive no-face report; real desktop tab/window video capture needs user-device testing. Permission-denial handling was also tested. Responsive output inspected at 320/390/1280px.

Python reference: three original uncompressed UBFC-rPPG clips gave 5.53 BPM pulse MAE, including one 15.97 BPM miss. Three short DeepfakeTIMIT clips all abstained. A genuine H.264 derivative also failed pulse quality. No learned classifier ships. Real/fake accuracy, fake recall and AUC are unavailable. This is not medical validation. It is not suitable for accusations or authenticity certification.

A dependable detector needs longer labeled genuine/fake videos, subject/source-safe training, untouched cross-dataset tests and error rates under compression, motion, lighting and varied skin appearance. Pulse can be inherited by face swaps. Blink/texture checks can fail on genuine videos.

## Sources

- https://sites.google.com/view/ybenezeth/ubfcrppg
- https://zenodo.org/records/4068245
- https://conradsanderson.id.au/vidtimit/
- https://sstuijk.estue.nl/publications/tbme16-algorithmic-rppg.pdf
- https://www.frontiersin.org/journals/imaging/articles/10.3389/fimag.2025.1504551/full
- https://ai.google.dev/edge/mediapipe/solutions/vision/face_landmarker/web_js

Code MIT. Third-party models and dataset media retain their own terms. No dataset media is published in this repository.
