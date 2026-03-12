# StairVision

**Computer Vision Step Counter** — a progressive web app that uses your phone camera to automatically detect and count stair steps in real-time.

## 🚀 Try It Now — No Installation Required

**[https://stair-vision.vercel.app/](https://stair-vision.vercel.app/)**

Open the link on your phone — that's it. No install, no build step, no account needed. The app runs entirely in your browser. You can optionally add it to your home screen for a full-screen experience with offline support.

## Features

- **Real-Time Step Detection** — Uses adaptive motion detection at a configurable horizontal line across the video feed to count steps automatically.
- **Manual Line Positioning** — Before tracking begins, drag the detection line to your desired height so the camera captures your full body crossing it. Start tracking whenever you are ready.
- **Duplicate-Free Counting** — A step is only registered when the band of motion at the line clears *and* the area below the line is also quiet, meaning both feet have fully crossed and your body is completely above the line.
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
3. **Track** — The app renders the live camera feed on a canvas with a detection line. Motion in a band around the line is compared frame-by-frame; a step is counted when motion at the line rises above an adaptive threshold, then falls, *and* the area below the line is also quiet — confirming both feet have crossed.
4. **Review** — When time runs out or you reach your goal, see your results (steps, completion %, time used, steps/min) and download the time-lapse video if recorded.

### Computer Vision Details

| Parameter | Value |
|---|---|
| Analysis resolution | 160 × 90 pixels |
| Detection band | ±9 pixels around the line |
| Frame sampling | Every 2 frames |
| Adaptive threshold buffer | 18-frame history |
| Cooldown between steps | 700 ms |
| Shake rejection | Global motion > 30 is ignored |
| Below-line threshold | Average pixel motion < 20 (`MIN_THRESH` × `BELOW_THRESH_FACTOR`) |

The algorithm compares RGB pixel differences between consecutive frames within the detection band. An adaptive threshold is computed from a rolling average of recent motion values multiplied by the sensitivity factor. A state machine transitions from idle → active (motion above threshold) → counted (motion drops below 45% of threshold). A step is only finalised when the below-line region is also quiet (average pixel change < 20), ensuring both feet have fully crossed the line and preventing duplicate or triplicate counts. A 700 ms cooldown provides an additional guard against double-counting.

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
