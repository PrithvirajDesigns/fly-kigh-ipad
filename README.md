# Fly Kigh

A three-lane sky runner rendered entirely in dithered ASCII, played on a tablet you wake up and unlock in the browser.

Steer a kite up through the sky and dodge the birds and drones diving down at you. It starts calm and keeps getting faster.

No libraries, no build step, no external assets. Graphics and sound are all generated in code, and the whole thing ships as one HTML file.

---

## Play

1. Open `index.html`. The tablet starts with its screen off.
2. **Double-tap the screen** to wake it and show the lock screen.
3. **Swipe up** (or press Space / Enter / ↑) to unlock and open the game.
4. **Tap, click or press Space** to start flying.

`fly-kigh.html` is the same game on its own, without the tablet, if you want it full screen.

### Controls

| Action | Keyboard | Mouse | Touch |
|---|---|---|---|
| Move left / right | ← → or A D | Point at a lane | Swipe, or tap the left / right half |
| Start / retry | Space or Enter | Click | Tap |
| Pause / resume | P, Esc, or the ⏸ button | Click ⏸ / ▶ | Tap ⏸ / ▶ |
| Sound on / off | M | — | — |

### Tablet

| Part | What it does |
|---|---|
| Screen (off) | Double-tap to wake |
| Lock screen | Swipe up to unlock; a tap shows "Swipe up to open" |
| Top button | Lock and turn the screen off |
| Volume buttons | Volume up / down (hold to repeat); a volume bar appears on screen |
| Portrait / Landscape toggle | Rotates the tablet; the game reflows to fit |

---

## How the game works

- **Three lanes.** The kite flies up the screen and can only be in one of three lanes. Birds and drones appear at the top edge and grow as they come down their lane toward you.
- **Hazards**
  - **Birds:** one size, wings flapping.
  - **Drones:** appear after 30 seconds. While still far away they hop lanes to follow you. Their red camera eye glows when locked onto your lane.
- **Hits.** You lose a life the moment a hazard touches the kite's edge. You have 3 lives (hearts), and after a hit there are 1.5 seconds of flashing invulnerability.
- **Fair waves.** Every wave leaves at least one lane open, and drones always arrive on their own.
- **Pacing (Chrome Dino style).** Speed rises with time and with every hazard you dodge, and jumps up every 300 points with a "FASTER!" flash. Speed gains taper toward the cap, so the game keeps getting harder well past 2,000 points.
- **Scoring**
  - distance flown (worth more the faster you go)
  - +5 for every hazard dodged
  - +100 for a close call ("CLOSE!"): within 2 characters of the kite's edge without touching it
  - the score blinks every 100 points
- **Best score** is saved in your browser.

## Look and sound

- **Rendering.** Each frame is drawn as a small grayscale image, then converted into characters with an 8×8 Bayer dither and density ramps (`. , : ; x X % # @`).
  - Rounded shapes get a checker/halftone texture.
  - Flat panels (kite panels) use horizontal lines (`- = ≡`).
  - Thin details like the kite string and drone struts are 1-character hairlines.
  - The kite, birds, drones, titles and hearts are drawn on a 2–4× finer grid of tiny characters for detail.
- **Sound.** Everything is synthesised live with the Web Audio API:
  - **Game:** wind that rises with speed, lane-change whooshes, bird chirps and a drone whine as they approach, close-call chimes, hit crunches, and level-up jingles.
  - **Tablet:** the wake / lock tick, the unlock swoosh and the volume clicks.
  - Browsers only allow audio after a tap or key press, so sound starts with the double-tap that wakes the tablet.

---

## Files

```
index.html     The tablet experience: device, lock screen, volume, rotation, sound.
               The full game is embedded inside it, so this file runs on its own.
fly-kigh.html  The game by itself (optional).
README.md      This file.
build/
  index.template.html  Source of the tablet page (the game gets embedded at __GAME_B64__).
  build.py             Rebuilds index.html after you edit fly-kigh.html.
```

Only `index.html` needs to be deployed; `build/` is for editing.

## Deploy (Vercel)

It's a static site, so there's no framework, build command or config.

**Dashboard**
1. Put `index.html` (and optionally `fly-kigh.html`) in a folder or a Git repo.
2. In Vercel, choose **Add New → Project** and import the repo (or upload the folder).
3. Set **Framework Preset** to **Other**, and leave the build command and output directory empty.
4. Deploy. The tablet is at `/`, and the standalone game at `/fly-kigh.html`.

**CLI**
```bash
npm i -g vercel
cd fly-kigh
vercel        # preview
vercel --prod # production
```

Any static host works the same way (Netlify, GitHub Pages, Cloudflare Pages, S3).

## Tuning

All the gameplay and look settings are in the `CONFIG` object at the top of the game script.

| Setting | Default | What it changes |
|---|---|---|
| `speedStart` / `speedMax` | 13 / 60 | Starting speed and top speed |
| `accel` | 0.25 | Speed gained per second just by surviving |
| `passBoost` | 0.4 | Extra speed for every hazard dodged |
| `levelEvery` / `levelBoost` | 300 / 3.5 | Points per "FASTER!" step, and how big the step is |
| `droneTime` | 30 | Seconds before drones appear |
| `lives`, `invulnTime` | 3, 1.5 | Lives, and invulnerability after a hit (seconds) |
| `scorePass` / `scoreNear` | 5 / 100 | Points per dodge / per close call |
| `hitShape` | — | Hit outlines for birds and drones (smaller = more forgiving) |
| `ditherStrength`, `rampDots`, `rampBands` | — | The ASCII look |

**Editing the tablet page.** `index.html` contains the game as an embedded (base64) copy. After changing `fly-kigh.html`, run `python3 build/build.py` to rebuild `index.html` from `build/index.template.html`.

## Browser support

Works in current Chrome, Edge, Safari and Firefox, on desktop and mobile. The lock-screen clock uses custom-drawn numerals, so it looks the same everywhere.

## Notes

The tablet and lock screen are an original recreation inspired by iPadOS. The wallpaper, clock numerals and sounds are all made for this project. Fly Kigh isn't affiliated with or endorsed by Apple Inc. iPad and iPadOS are trademarks of Apple Inc.
