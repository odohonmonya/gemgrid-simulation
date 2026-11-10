# GemGrid network simulation

A browser-based simulation of GemGrid, a solar-powered offline early-warning network for North-East Nigeria. It shows emergency alerts and community notices hopping between LoRa radio nodes on a real map of Yola and Jimeta, including store-and-forward delivery when a node is offline, responder acknowledgements, and a gateway that syncs only when internet is available.

Built with plain HTML, CSS and JavaScript. No frameworks, no build step and no server code.

**Live demo:** _add your Vercel link here_

## What's in this repository

| Path | What it is |
|---|---|
| `index.html` | The whole simulation: HTML, CSS and JavaScript in one file. |
| `assets/yola-map.jpg` | The map image (OpenStreetMap data, CyclOSM style). |

## Run it locally

Open `index.html` in a browser, or serve the folder:
```
python3 -m http.server 8000
```
then visit http://localhost:8000

## Deploy

The site is static, so it needs no build settings. On Vercel, import this repository and keep the defaults (Framework Preset: Other, no build command, output directory = repository root). Every push to `main` redeploys automatically.

## Useful link options

- `#present` hides the controls, for clean screen recording.
- `#dark` opens the dark map. Combine them: `#present#dark`.

## How the code is organised

Everything is in one `<script>` block at the bottom of `index.html`.

- **Map geometry:** the constants `Z`, `CLAT`, `CLON`, `DPR`, `OX`, `OY`, `CX`, `CY` describe the OpenStreetMap view the image came from (zoom 13, centred on 9.22474 N, 12.48528 E). `PX_PER_KM` and `toLatLon()` convert between pixels, kilometres and GPS.
- **Assumptions:** `RANGE_KM` (radio range, 2.5 km), `HOP` (seconds per hop, 1.2) and `ACK_DELAY` (responder reaction time, 2 s).
- **Nodes:** the `NODES` array. Each node has an id, name, short label, `x`/`y` position on the map image, an icon, and `responder: true` for nodes that acknowledge alerts. Neighbours are worked out automatically from distance.
- **Network logic:**
  - `send()` creates an alert or notice at a node.
  - `deliver()` handles a node receiving a message: ignores duplicates, logs it, triggers responder acknowledgements, and syncs to the dashboard at the gateway.
  - `offerAll()` and `offer()` pass messages to neighbours that don't have them (store-and-forward).
  - `transmit()` animates a message along a link.
  - `setOnline()` powers a node off or on; on reconnect, neighbours exchange what they hold and fill the gaps.
  - `setGrid()` and `setNet()` switch grid power and gateway internet.
- **Guided demo:** `runDemo()` plays the six scripted steps.
- **Drawing:** `render()` redraws the map layer and side panels; `frame()` runs the clock and animation.
- **Theme:** colours are CSS variables at the top of the file, with light as the default and a dark set under `[data-theme="dark"]`.

## Changing the map or nodes

1. To move a node, change its `x`/`y` in `NODES`. Positions are pixels on the 1165 × 1780 map image.
2. To use a different map, replace `assets/yola-map.jpg` with an image of the same size, and update the map geometry constants so distances stay correct.

## Simplifications

This is a model, not a radio simulator. Inside the assumed range every message arrives and outside it none do; each hop takes the same time; and routing is simple flooding. Range, timing and battery figures will be replaced by field measurements in Milestone 1.

## Credits and licence

Map data © OpenStreetMap contributors, available under the Open Database Licence (openstreetmap.org/copyright). Tile style by CyclOSM. Keep this credit visible wherever the map is shown.

© My Gem Gadget. GemGrid simulation by Abel Odoh.
