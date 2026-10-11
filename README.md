# SignBridge AI

**Two-way, real-time communication between Deaf and hearing people in Indian languages.**

A hackathon MVP/PWA prototype focused on a shared meaning layer for 25 phrases, browser speech capabilities, camera preview, emergency phrase workflows, and transparent capability reporting.

## Important scope and honesty

- This is phrase-level matching, not full ISL translation. ISL has its own grammar, spatial structure, and facial expressions.
- Hand tracking is not sign recognition. This starter includes camera preview and landmark normalization utilities, but MediaPipe model loading, live landmark overlay, sample recording, TFJS training, and trained classifier inference are **not yet integrated**. The app explicitly reports this instead of fabricating predictions.
- Sign clips are not included. Add validated and appropriately licensed clips to `public/signs/<phrase-id>.mp4`. Missing clips show a placeholder.
- Speech recognition is browser-dependent. The Web Speech API may send audio to the browser vendor's speech service. Do not describe it as offline.
- Camera preview is requested only after the user clicks Start camera. The app does not upload camera frames. Conversation history is memory-only. Emergency contact is stored in localStorage.
- Emergency SMS opens a draft in the device's messaging app; it does not silently send messages. `tel:112` opens the device dialer where supported.

## Requirements

- Node.js 20 or newer recommended
- npm
- A modern browser. Camera access generally requires HTTPS or localhost.

## Run locally

```bash
npm install
npm run dev
```

Open the local URL printed by Vite (usually `http://localhost:5173`).

## Build and tests

```bash
npm run test
npm run build
```

## Features implemented

- Responsive React + TypeScript UI, navigation, English/Hindi language toggle, light/dark theme
- 25-phrase shared meaning layer with English, Hindi, and Hinglish aliases
- Exact, containment, and Fuse.js fuzzy phrase matching
- Manual input, browser speech recognition adapter, browser text-to-speech
- Missing sign clip placeholder, sign video controls when assets exist
- Permission-gated camera preview with explicit status (no fabricated predictions)
- Emergency phrase selection, speak twice, copy/share, SMS draft with optional geolocation, `tel:112`
- Local emergency contact setting
- Capability panel, About & Limits, PWA configuration
- Unit tests for phrase matching and landmark utilities

## Not yet implemented

- MediaPipe HandLandmarker/PoseLandmarker loading and rendered landmark overlay
- Motion-based segmentation and 30-frame sequence recording
- IndexedDB dataset/model storage and TensorFlow.js GRU/1D-CNN training
- Live sign classification, confidence threshold/confirm/retry flow, correction feedback loop
- Real held-out evaluation and confusion matrix
- Offline speech recognition provider such as Vosk/Whisper

These features require additional engineering and validation; the UI deliberately states that recognition is not trained yet.

## Add a phrase

Add a phrase object in `src/data/phrases.json` with a unique `id`, `category`, `en`, `hi`, `aliases_en`, `aliases_hi`, `aliases_hinglish`, `signVideo`, and `emergency`. Add a matching MP4 clip only after validating its sign with a Deaf ISL user or qualified interpreter. Keep the shared phrase ID stable across languages.

## Add a language

Extend the `Lang` type in `src/types/index.ts`, language selector options, speech provider locale mapping, phrase data schema, and translation adapter. Only mark speech as supported when the current browser/provider actually supports it.

## Record clips

See `public/signs/README.md`. Do not invent or relabel gestures as ISL. The app will gracefully show a placeholder for absent clips.

## Architecture

- `src/services/translation/matcher.ts`: normalizer and phrase matching
- `src/services/speech/provider.ts`: swappable browser speech adapter and speech synthesis
- `src/services/sign-recognition/landmarks.ts`: basic landmark normalization/resampling helpers
- `src/services/sign-library/phrases.ts`: shared phrase library access
- `src/components/communication/CameraPanel.tsx`: permission-gated camera preview
- `src/components/sign/SignDisplay.tsx`: sign video player and missing-asset placeholder
- `src/components/emergency/EmergencyPage.tsx`: emergency actions

## 2-minute demo script

1. Open Home and explain the two communication directions.
2. Open Live Communication Studio and type “I need a doctor”. Show the matched phrase ID and the honest missing-clip placeholder.
3. Try “mujhe doctor chahiye” and “ambulance bulao” to demonstrate English/Hinglish matching.
4. Start camera to demonstrate permission-gated local camera preview; explain that live recognition is disabled until a trained model is integrated.
5. Open Emergency Mode, choose Ambulance, and demonstrate the spoken phrase, copy/share, and SMS draft (do not send a test alert).
6. Open About & Limits and Capability panel to explain privacy and scope.

## Suggested 15-day task split for 5 people

- **Person 1 — Frontend & accessibility (Days 1–5):** responsive layouts, keyboard access, contrast audit, studio UI; Days 6–10 refine mobile UX; Days 11–15 accessibility tests and polish.
- **Person 2 — Speech & language (Days 1–5):** SpeechProvider and browser compatibility; Days 6–10 add offline provider spike and language tests; Days 11–15 robustness and documentation.
- **Person 3 — Computer vision (Days 1–5):** MediaPipe integration and landmark overlay; Days 6–10 sequence recording and normalization; Days 11–15 lighting/device tests.
- **Person 4 — ML & evaluation (Days 1–5):** dataset schema and collection plan; Days 6–10 TFJS model training and held-out split; Days 11–15 calibration, confusion matrix, and error analysis.
- **Person 5 — ISL content, QA & integration (Days 1–5):** phrase validation and licensed clip sourcing; Days 6–10 integrate assets and run acceptance tests; Days 11–15 demo script, README, and final integration.

## Privacy note

The application does not upload camera frames. Browser Web Speech API behavior depends on the browser and may involve vendor-side processing. Do not claim fully offline audio processing unless an offline speech provider is actually integrated and tested.
