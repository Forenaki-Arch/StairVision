# StairVision

**Computer Vision Step Counter** — a progressive web app that uses your phone camera to automatically detect and count stair steps in real-time.

## Features

- **Real-Time Step Detection** — Uses adaptive motion detection at a configurable horizontal line across the video feed to count steps automatically.
- **Auto-Calibration** — Walk in front of the camera during a 7-second calibration phase and the app finds the optimal detection line position using center-of-mass analysis.
- **Manual Adjustments** — Drag the detection line to reposition it, tap to add or undo steps, and adjust sensitivity from 0.6× to 3.5×.
- **Session Timer & HUD** — Set a time limit and target step count. The on-screen HUD shows remaining time, steps/min rate, ETA, and a progress bar.
- **Time-Lapse Recording** — Optionally record your session as a sped-up video (WebM) with the HUD overlay included.
- **Audio & Haptic Feedback** — A short tick sound and vibration on each detected step.
- **Screen Wake Lock** — Keeps the screen on during tracking so you never lose your session.
- **Installable PWA** — Install on your home screen for a full-screen, app-like experience with offline support.
- **Zero Dependencies** — Pure vanilla JavaScript, no frameworks or build tools needed.

## How It Works

1. **Setup** — Enter a session name, target step count, time limit, and sensitivity.
2. **Calibrate** — Point the camera at the staircase. Tap **Start Calibration** and walk 6–8 steps so the app detects where to place the detection line, or skip to set it manually.
3. **Track** — The app renders the live camera feed on a canvas with a detection line. Motion in a band around the line is compared frame-by-frame; when motion rises above an adaptive threshold and then falls, a step is counted.
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

The algorithm compares RGB pixel differences between consecutive frames within the detection band. An adaptive threshold is computed from a rolling average of recent motion values multiplied by the sensitivity factor. A state machine transitions from idle → active (motion above threshold) → counted (motion drops below 45% of threshold), with a 700 ms cooldown to prevent double-counting.

## Try It

The app is live at **[https://stair-vision.vercel.app/](https://stair-vision.vercel.app/)** — open it on your phone and install it to your home screen for the best experience. No installation or build step required.

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

## Browser Requirements

- A modern browser with `getUserMedia` support (Chrome, Safari, Firefox, Edge)
- HTTPS connection (required for camera access; localhost is exempt)
- A device with a camera

## License

This project is open source.
