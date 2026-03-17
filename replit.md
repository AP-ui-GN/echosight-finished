# EchoSight — AI Visual Guide for the Blind

EchoSight is a voice-activated accessibility app for blind and visually impaired users. It uses the phone camera to capture images, analyzes them with OpenAI Vision (GPT-5.2), and provides spoken descriptions via expo-speech.

## Architecture

### Stack
- **Frontend**: Expo React Native (Expo Router, file-based routing)
- **Backend**: Express + TypeScript on port 5000
- **AI**: OpenAI Vision via Replit AI Integrations (no API key needed — billed to Replit credits)
- **State**: useReducer + React Context (AppContext)
- **TTS**: expo-speech with priority queue (lib/useAudioPlayer.ts)
- **Storage**: AsyncStorage with @echosight_settings and @echosight_history keys

### Key Files
- `app/index.tsx` — Main camera/scan screen
- `app/settings.tsx` — Settings screen (modal)
- `app/history.tsx` — History screen (modal, SectionList grouped by date)
- `app/_layout.tsx` — Root layout (AppProvider + fonts + navigation)
- `lib/AppContext.tsx` — Central state with useReducer, AppProvider, useApp/useSettings hooks
- `lib/constants.ts` — 12 MODES, mode prompts, CORRECTIONS (13 items), ONBOARDING_STEPS, VOICE_COMMANDS, DEFAULT_SETTINGS
- `lib/storage.ts` — AsyncStorage helpers, ScanRecord type (id/description/mode/timestamp/type/flagged/verbosity)
- `lib/useAudioPlayer.ts` — Priority TTS queue with speak/stop/replay/isSpeaking
- `lib/imageAnalysis.ts` — Base64 image quality analysis (dark/bright/blurry/blocked/etc)
- `lib/useDeviceOrientation.ts` — Device orientation detection (facing_down/facing_up/normal)
- `server/routes.ts` — /api/vision/scan (mode + verbosity), /api/vision/followup

### Components
- `components/ModeBar.tsx` — Horizontal scroll bar for 12 scan modes
- `components/OnboardingOverlay.tsx` — 8-step TTS-guided first-launch onboarding
- `components/WaveformIndicator.tsx` — 7-bar animated TTS playback indicator
- `components/ScanOverlay.tsx` — Animated scanning rings while processing
- `components/ActionBar.tsx` — (legacy, replaced by bottom bar in index.tsx)

## Features

### 12 Scan Modes
describe, text (Read Text), people, hazards, navigate, color, currency, face, product, food, document, medicine

### Camera Corrections
Before sending to AI, the app checks for: dark image, too bright (ceiling), blurry, blocked lens, finger over camera, facing down, facing up, glare, screen glare, distortion, water droplets, flash artifacts, pocket/bag detection

### Emergency Mode
Long-press the scan button (2s) or tap the lightning bolt — activates red overlay, auto-rescans every 8 seconds in hazards mode

### Follow-up Questions
After any scan result, a text input appears to ask follow-up questions about the same captured image using /api/vision/followup

### Onboarding
8-step TTS-guided tutorial on first launch, auto-advances after each step's speech completes

## Theme
- Background: `#0A0E1A`
- Accent: `#00D4AA`
- Danger: `#FF4757`
- Warning: `#FFB347`
- All interactive buttons: minimum 64×64px

## Workflows
- `Start Backend`: `npm run server:dev` → port 5000
- `Start Frontend`: `npm run expo:dev` → port 8081 (web preview)

## Bundle ID
`com.visionbridge` (do NOT change per Expo rules)

## API Endpoints
- `POST /api/vision/scan` — `{ image: base64, mode: string, verbosity: "brief"|"detailed"|"ultra" }` → `{ description: string }`
- `POST /api/vision/followup` — `{ image: base64, question: string, conversation: Message[], mode: string }` → `{ description: string }`
