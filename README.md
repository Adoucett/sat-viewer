# Planet Constellation Viewer

Real-time 3D visualization of Planet's operational satellite constellation using live orbital ephemeris data.

## Quick Start

1. Serve this directory via any HTTP server:
   ```bash
   cd Sat-showcase
   python3 -m http.server 8080
   ```
2. Open `http://localhost:8080` in Chrome or Edge

No dependencies to install -- all libraries load from CDN.

## Features

- **Real orbital data** from Planet's own GPS/UHF ephemerides (`ephemerides.planet-labs.com`)
- **4 constellation types**: SuperDove, SkySat, Pelican, Tanager
- **Time simulation**: 1x through 3600x speed with play/pause
- **Cinema mode**: Clean view for recording (press `C`)
- **WebM recording**: Built-in capture (press `R`)
- **Planet brand compliant**: Montserrat/Source Code Pro fonts, Planet teal palette

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `C` | Toggle cinema mode |
| `Space` | Play / Pause |
| `R` | Start / Stop recording |
| `Arrow Up` | Increase speed 10x |
| `Arrow Down` | Decrease speed 10x |

## Recording a Video

1. Press `C` to enter cinema mode (hides UI, starts auto-rotation)
2. Adjust speed to desired playback (60x recommended for smooth orbital motion)
3. Press `R` to start recording -- a red REC indicator appears
4. Let it run for 30-60 seconds
5. Press `R` again to stop -- a `.webm` file downloads automatically
6. Convert to MP4 if needed: `ffmpeg -i input.webm -c:v libx264 output.mp4`

Alternatively, use OBS Studio or QuickTime Player screen recording with cinema mode active.

## Data Sources

- **Primary**: Planet Labs PBC orbital ephemerides (CC BY-NC 4.0)
- **Fallback**: CelesTrak NORAD GP elements
- **Propagation**: SGP4/SDP4 via satellite.js v6.0.2 (WGS-72 compliant)

## Technical Notes

- Satellite positions are propagated in real-time using the SGP4 algorithm
- TLE data is fetched fresh on each page load
- Trailing arcs show recent orbital path (not full orbit rings) to avoid visual clutter
- SuperDove trails are hidden at global zoom to keep the view clean
- All coordinates are WGS-84; propagation uses WGS-72 constants per SGP4 specification
