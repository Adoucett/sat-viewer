# Planet Constellation Viewer

Real-time Three.js visualization of Planet's public satellite TLE entries using SGP4 orbital propagation, custom day/night Earth shaders, and Planet global monthly texture assets.

## Quick Start

1. Serve this directory via any HTTP server:
   ```bash
   cd sat-viewer
   python3 -m http.server 8080
   ```
2. Open `http://localhost:8080` in Chrome or Edge

No build step is required. Three.js, satellite.js, and fonts load from CDN.

## Required Runtime Files

- `index.html`
- `planet_mc.tle`
- `WorldView/day_earth_16k_may2025.jpg`
- `WorldView/day_earth_8k.jpg`
- `WorldView/night_earth_4k.jpg`

## Features

- **Real public TLE data** from Planet's own GPS/UHF ephemerides (`ephemerides.planet-labs.com`)
- **4 constellation groups**: SuperDove, SkySat, Pelican (EDDA grouped with Pelican), Tanager
- **Time simulation**: 1x through 7200x speed with play/pause
- **Three.js globe** with custom GLSL day/night cycle and atmosphere
- **Coverage Wrap mode** (non-default): persistent ground tracks that accumulate and wrap the globe
- **Hands-off camera paths**: fly between framed views, slow orbit, push-in / pull-back for no-input recording
- **Presentation presets**: one-click setups for common stakeholder asks
- **TLE freshness badge**: shows how old the loaded orbital data is
- **Capture-quality Earth texture** from Planet global monthly mosaic (`?texture=high`, default)
- **Standard texture fallback** for lighter loads (`?texture=standard`)
- **Cinema mode**: Clean view for recording (press `C`)
- **WebM recording**: Built-in capture (press `R`)
- **Planet brand compliant**: Montserrat/Source Code Pro fonts, Planet teal palette

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `C` | Toggle cinema mode |
| `Space` | Play / Pause |
| `R` | Start / Stop recording |
| `S` | Toggle settings |
| `L` | Toggle labels |
| `H` | Hide/show timeline |
| `F` | Fullscreen |
| `1`-`5` | Camera presets |
| `P` | Play / Stop hands-off camera path |
| `Arrow Up` | Increase speed |
| `Arrow Down` | Decrease speed |
| `Arrow Left/Right` | Scrub +/- 10 minutes |

## Coverage Wrap Mode

A non-default presentation mode (Settings -> Ground Tracks -> Mode -> Coverage Wrap) that projects each
satellite's recent path onto the surface and keeps it for a long window, so coverage visibly accumulates
and wraps the planet. It is seeded instantly on activation by back-propagating real TLEs, so you do not
have to wait for it to build up. Controls:

- **Coverage Window**: how many hours of ground track to retain
- **Coverage Persistence**: how brightly older tracks linger before fading
- **Rebuild Coverage**: re-seed from the current sim time

Best paired with a high time speed (e.g. 900x-1800x) and a slow camera orbit.

## Hands-Off Camera Paths

Settings -> Hands-Off Camera. Designed for clean 30-60s Camtasia captures with no mouse input.

- **Set A / Set B**: capture the current view as the start (A) and end (B)
- **Path types**: Slow Orbit, Push In, Pull Back, Fly A -> B, Coverage Loop
- **Duration / Easing / Loop**: control timing and motion feel
- **Play** (or press `P`): runs the path; OrbitControls are disabled during playback to avoid jitter

## Presentation Presets

Settings -> Presentations. One-click setups: Executive Overview, Coverage Wrap, Pelican + SkySat,
PlanetScope Daily, Booth Loop.

## URL Launch Parameters

Open a link that lands directly in the desired state (great for booth displays):

| Param | Example | Effect |
|-------|---------|--------|
| `texture` | `?texture=standard` | `high` (default) or `standard` Earth texture |
| `mode` | `?mode=coverage` | Start in Coverage Wrap mode |
| `speed` | `?speed=900` | Set simulation speed |
| `labels` | `?labels=1` | Show satellite labels |
| `cinema` | `?cinema=1` | Start in clean cinema view |
| `path` | `?path=coverageLoop` | Start a camera path (`orbit`, `pushIn`, `pullBack`, `flyBetween`, `coverageLoop`) |
| `duration` | `?duration=60` | Camera path duration in seconds |
| `loop` | `?loop=1` | Force loop on/off for the path |
| `preset` | `?preset=Booth%20Loop` | Apply a named presentation preset |

Example booth loop: `?preset=Booth%20Loop` or `?cinema=1&mode=coverage&path=coverageLoop&duration=75&speed=900`

## Recording a Video

1. Press `C` to enter cinema mode (hides UI, starts auto-rotation)
2. Adjust speed to desired playback (60x recommended for smooth orbital motion)
3. Press `R` to start recording -- a red REC indicator appears
4. Let it run for 30-60 seconds
5. Press `R` again to stop -- a `.webm` file downloads automatically
6. Convert to MP4 if needed: `ffmpeg -i input.webm -c:v libx264 output.mp4`

Alternatively, use OBS Studio or QuickTime Player screen recording with cinema mode active.

## Data Sources

- **Primary**: Planet Labs PBC public orbital ephemerides
- **Fallback**: CelesTrak NORAD GP elements
- **Offline fallback**: bundled `planet_mc.tle`
- **Propagation**: SGP4/SDP4 via satellite.js v6.0.2 (WGS-72 compliant)

Counts shown in the app are public TLE entries currently loaded. They should not be described as operational, commercially taskable, or exhaustive spacecraft counts without a separate authoritative validation.

## Technical Notes

- Satellite positions are propagated in real-time using the SGP4 algorithm
- TLE data is fetched fresh on each page load
- Trailing arcs show recent orbital path
- `?texture=high` loads `WorldView/day_earth_16k_may2025.jpg`
- `?texture=standard` loads `WorldView/day_earth_8k.jpg`
- All coordinates are WGS-84; propagation uses WGS-72 constants per SGP4 specification

## Deploy / Promote Checklist

Development happens in `Firehose/sat-showcase/v3-coverage.html`. This repo (`sat-viewer`) is the
deploy mirror that GitHub Pages serves. The previous known-good build is kept here as
`index-v2-stable.html` for instant rollback.

To promote a new build:

1. Smoke-test the working copy locally (globe textures load, TLEs populate, coverage + camera paths run).
2. Copy the required runtime files into `sat-viewer`:
   - `v3-coverage.html` -> `index.html`
   - `planet_mc.tle` (refresh from the public feed on each promote)
   - `WorldView/day_earth_16k_may2025.jpg`, `WorldView/day_earth_8k.jpg`, `WorldView/night_earth_4k.jpg`
3. Keep the prior `index.html` as `index-v2-stable.html` (rollback by copying it back over `index.html`).
4. Commit and push `main`, then smoke-test the live Pages URL.

Rollback: `cp index-v2-stable.html index.html` then commit/push.
