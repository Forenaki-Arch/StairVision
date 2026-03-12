# StairVision

**Computer Vision Step Counter** — a progressive web app that uses your phone camera to automatically detect and count stair steps in real-time.

## 🚀 Try It Now — No Installation Required

**[https://stair-vision.vercel.app/](https://stair-vision.vercel.app/)**

Open the link on your phone — that's it. No install, no build step, no account needed. The app runs entirely in your browser. You can optionally add it to your home screen for a full-screen experience with offline support.

## Features

- **Real-Time Step Detection** — Uses adaptive motion detection at a configurable horizontal line across the video feed to count steps automatically.
- **Manual Line Positioning** — Before tracking begins, drag the detection line to your desired height so the camera captures your full body crossing it. Start tracking whenever you are ready.
- **5-Second Positioning Countdown** — After tapping START TRACKING, a 5-second countdown (4 beeps/second) gives you time to get into position. A distinct short–short–long "GO!" tone signals the exact moment tracking begins.
- **Direction-Aware Counting** — When motion at the detection line activates, the app snapshots whether there is more motion *below* the line (body approaching from below = going **up**) or *above* (body approaching from above = going **down**). Only upward crossings are counted; descending passes are silently ignored.
- **Duplicate-Free Counting** — A step is only registered when the band of motion at the line clears *and* the area below the line is also quiet, meaning both feet have fully crossed and your body is completely above the line.
- **Clean Recorded Video** — The detection line and all CV overlays are drawn exclusively on the live-view canvas; the recorded video contains only the camera feed and the HUD.
- **Manual Adjustments** — Drag the detection line to reposition it during tracking, tap to add or undo steps, and adjust sensitivity from 0.6× to 3.5×.
- **Session Timer & HUD** — Set a time limit and target step count. The on-screen HUD shows remaining time, steps/min rate, ETA, and a progress bar.
- **Time-Lapse Recording** — Optionally record your session as a sped-up video (WebM) with the HUD overlay included.
- **Milestone & Goal Sounds** — A tick sound plays on each step. A distinct chime plays every time you hit a progress milestone (default every 10%, adjustable in Settings). A triumphant fanfare plays when you reach your goal.
- **Haptic Feedback** — Vibration on each detected step (where supported).
- **Screen Wake Lock** — Keeps the screen on during tracking so you never lose your session.
- **Installable PWA** — Install on your home screen for a full-screen, app-like experience with offline support.
- **Zero Dependencies** — Pure vanilla JavaScript, no frameworks or build tools needed.

## How It Works

1. **Setup** — Enter a session name, target step count, time limit, and sensitivity.
2. **Position Line** — Point the camera at the staircase so your full body is visible. Drag the detection line to your mid-body height (roughly waist level). When the line is where you want it, tap **START TRACKING**.
3. **Countdown** — After tapping START TRACKING the app counts down 5 seconds (4 crisp beeps/second) so you can step into position. A short–short–long "GO!" signal marks the start of tracking.
4. **Track** — The app renders the live camera feed on a canvas with a detection line (CV overlays are never written to the recorded video). Motion in a band around the line is compared frame-by-frame. When motion rises above an adaptive threshold, the app snapshots whether the body is approaching from *below* the line (going **up**) or from *above* (going **down**). A step is only counted when: the crossing was upward, motion at the line then falls, *and* the area below the line is also quiet — confirming both feet have fully cleared the line.
5. **Review** — When time runs out or you reach your goal, see your results (steps, completion %, time used, steps/min) and download the time-lapse video if recorded.

### Computer Vision Details

| Parameter | Value |
|---|---|
| Analysis resolution | 160 × 90 pixels |
| Detection band | ±9 pixels around the line |
| Frame sampling | Every 2 frames |
| Adaptive threshold buffer | 18-frame history |
| Cooldown between steps | 700 ms |
| Pre-tracking countdown | 5 s, 4 beeps/s (20 total) + GO! signal |
| Shake rejection | Global motion > 30 is ignored |
| Below-line threshold | Average pixel motion < `MIN_THRESH` × `BELOW_THRESH_FACTOR` |
| Direction detection | Both up and down steps are counted; `D.belowSignal > D.aboveSignal` at activation determines direction |
| Recorded video | Camera feed + HUD only; detection line and CV overlays excluded |

The algorithm compares RGB pixel differences between consecutive frames within the detection band. An adaptive threshold is computed from a rolling average of recent motion values multiplied by the sensitivity factor. A state machine transitions from idle → active (motion above threshold) → counted (motion drops below 45% of threshold).

When a step activates, the app also samples motion above the line (`D.aboveSignal`) and below the line (`D.belowSignal`). If the below region has more motion, the body is rising (going **up**) and `S.stepGoingUp` is set to `true`. A step is finalised when the band motion drops below 45 % of the adaptive threshold *and* either the below-line or above-line region is quiet (average pixel change < `MIN_THRESH` × `BELOW_THRESH_FACTOR`). This allows counting steps in **both directions** (up and down), which is essential for stair exercises. A 700 ms cooldown provides an additional guard against duplicate counts.

## Browser Requirements

- A modern browser with `getUserMedia` support (Chrome, Safari, Firefox, Edge)
- HTTPS connection (required for camera access; localhost is exempt)
- A device with a camera

## Project Structure

```
├── index.html          # Main application (HTML + CSS + JS)
├── manifest.json       # PWA web app manifest
├── sw.js               # Service worker for offline caching
├── vercel.json         # Vercel deployment configuration
├── icons/
│   ├── icon-192.svg          # App icon 192×192
│   ├── icon-512.svg          # App icon 512×512
│   ├── icon-maskable-192.svg # Maskable icon 192×192
│   └── icon-maskable-512.svg # Maskable icon 512×512
└── README.md
```

## License

This project is open source.
