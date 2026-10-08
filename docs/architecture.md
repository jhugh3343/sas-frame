# Architecture

How the frame is built. This is the proposed shape; refine it as the code lands, and record any real change in `docs/decisions.md`.

## Overview

- `index.html` is the frame. It runs full screen in Chromium on the Pi, and in any browser on John's PC during development.
- `studio.html` is the tile studio. It runs on John's PC only, turning photos into tiles and writing their data.
- `content/` holds the data and assets. The frame reads it; the studio writes to it.
- There's no build step. The repo is served as static files by a small local server on the Pi. ES modules don't load from `file://`, so a server is required.

## Proposed layout

```
index.html              the frame
studio.html             the tile studio
config.json             night schedule, defaults, kiosk options
version.json            written by the update script; the app reloads when it changes
src/
  app.js                boot, main loop, wiring
  state.js              pure reducer: (state, event, now) -> state
  input.js              keyboard -> events (the knob arrives as keys)
  layout.js             pure: collage grid and single-view rects for any aspect ratio
  views/
    collage.js
    single.js
    transitions.js
  time/
    clock.js            pure: local time in an IANA zone, wall time -> instant
    astro.js            pure: sun and moon position, moon phase, rise and set
    source.js           real clock, fixed instant, or time-lapse (dev only)
  weather/
    openmeteo.js        fetch, refresh, cache, fallback
    mapping.js          pure: WMO code -> render params and Dutch words
  scene/
    scene.js            composes the layers for one place
    sky.js  stars.js  sun-moon.js  clouds.js
    town-canal.js       first template: canal street
    water.js            reflections and ripples
    weather-fx.js       rain, snow, fog, lightning
    lights.js           pure: window schedules
    calendar.js         pure: which special day is active at a place and time
  tiles/
    filter.js           photo -> ink lines and washes
    tile.js             medallion, rings, corners, glaze
  util/
    rng.js              seeded random (no Math.random in scene code)
    color.js
content/
  places.json
  memories.json
  memories/             tile PNGs and web-sized photos
  fonts/                self-hosted fonts (the frame must work offline)
photos-original/        full-size originals, git-ignored
tools/
  shots.mjs             Playwright: render fixed moments to PNG
pi/
  setup.md              step-by-step Pi setup, kept current
  update.sh             nightly pull and version stamp
  *.service *.timer     systemd units
tests/
  *.test.js             node:test
```

## State and input

- **State:** `{ mode: 'live' | 'memories', view: 'single' | 'collage', placeIndex, memoryIndex, highlightUntil, transition }`.
- **Events:** `turn(+1 | -1)`, `press`, `hold`. A pure reducer applies them, so the control logic can be tested without a screen.
- **Keyboard in development:** Left and Right turn, Enter presses, Space holds. On the Pi, the knob is mapped to the same keys (see `docs/hardware.md`), so the app never knows the difference.

## Time and astronomy

- **Time source.** One function returns "now" for the whole app: the real clock, a fixed instant for screenshots, or a time-lapse for the dev panel.
- **Local time** comes from `Intl.DateTimeFormat(…, { timeZone }).formatToParts()`. Converting a local wall time back to an instant iterates on the offset, which handles daylight saving time correctly. Never store or compute with fixed offsets.
- **Astronomy** uses the standard low-precision formulas, accurate to about a minute for sunrise and sunset, which is plenty for visuals. These are the formulas behind SunCalc (BSD-2-Clause). Either port them as small pure functions in `time/astro.js`, which suits John's pure-function style, or vendor SunCalc with its licence notice.
- **Outputs:** sun altitude and azimuth; moon altitude, azimuth, illuminated fraction and waxing or waning; sunrise, sunset and twilight times for a date and place.

## Weather

Weather comes from Open-Meteo, which needs no API key for non-commercial use. There is one request per place, refreshed every 15 to 30 minutes.

```
https://api.open-meteo.com/v1/forecast?latitude=52.0116&longitude=4.3571
  &current=temperature_2m,weather_code,cloud_cover,precipitation,rain,showers,snowfall,wind_speed_10m,wind_direction_10m,is_day
  &timezone=Europe%2FAmsterdam
```

Check the current field names against the docs when implementing. Snow cover on roofs can come from recent snowfall, or from snow depth if the API offers it for the location.

**Mapping.** `weather/mapping.js` is pure: it turns a WMO weather code plus the numeric fields into render parameters `{ cloud, rain, snow, fog, storm, windX }` and a Dutch phrase. Have a native speaker who isn't Sas check the wording.

| WMO code   | Kind                    | Dutch                                    |
|------------|-------------------------|------------------------------------------|
| 0          | clear                   | onbewolkt (zonnig by day, helder by night) |
| 1, 2, 3    | mainly clear → overcast | licht bewolkt, half bewolkt, bewolkt     |
| 45, 48     | fog, rime fog           | mist, aanvriezende mist                  |
| 51, 53, 55 | drizzle                 | lichte motregen, motregen, dichte motregen |
| 56, 57     | freezing drizzle        | aanvriezende motregen                    |
| 61, 63, 65 | rain                    | lichte regen, regen, zware regen         |
| 66, 67     | freezing rain           | ijzel                                    |
| 71, 73, 75 | snow                    | lichte sneeuw, sneeuw, zware sneeuw      |
| 77         | snow grains             | korrelsneeuw                             |
| 80, 81, 82 | rain showers            | lichte buien, buien, zware buien         |
| 85, 86     | snow showers            | sneeuwbuien, zware sneeuwbuien           |
| 95         | thunderstorm            | onweer                                   |
| 96, 99     | thunderstorm with hail  | onweer met hagel                         |

**Failure.** Keep the last good reading per place. If there has been none since boot, use a neutral partly cloudy look and leave the weather words out of the caption. If Chromium runs in incognito mode to avoid restore prompts, `localStorage` doesn't survive restarts, so either keep the cache in memory or let the local server persist a small cache file. Decide this during implementation.

## Scene rendering

- **Deterministic.** `renderScene(ctx, rect, { place, instant, weather, seed })` draws a place. Randomness comes only from seeded generators keyed by place and element, so the same inputs always give the same picture apart from the animation phase.
- **Layers, back to front:** sky gradient, stars, sun or moon, clouds, distant tower and skyline, houses and trees, quay, lamps, reflection of everything above the waterline, water surface, weather particles, fog, caption.
- **Caching.** Static layers such as houses, tower and quay are drawn to an offscreen canvas. They're redrawn only when their lighting key changes (sun-altitude bucket, cloud bucket, window-state hash) or when the size changes. Sky, stars, clouds, water and particles animate on every frame.
- **One offscreen canvas per visible place,** sized to its current rect (a collage cell or the full screen).
- **Reflections:** draw the region above the waterline flipped, in horizontal strips with small sine offsets for ripples, then tint and darken.
- **Particles:** counts scale with intensity and with the area of the rect, so a collage cell isn't overloaded.
- **Templates:** `canal-street` comes first. Later templates (`polder-village`, `coast`, `city-silhouette`) reuse the sky, astronomy, weather, water and lights, and differ only in their land layer.

## The Delft tile pipeline

All of this runs in `studio.html` on John's PC. The frame only shows finished images.

1. **Crop** the photo to a square around the subject. The default is centred, and John adjusts it.
2. **Luminance,** then two Gaussian blurs to build an XDoG edge image for the ink lines (Winnemöller, Kyprianidis and Olsen, "XDoG: An eXtended difference-of-Gaussians compendium", Computers & Graphics, 2012).
3. **Tone:** blur, then quantise into two or three cobalt washes, with noisy thresholds so the edges look brushed.
4. **Composite** onto the glaze: washes, lines, the medallion mask, double ring, corner motifs and glaze texture with a bevel.
5. **Export** the tile PNG (around 1024 px square), a web-sized copy of the photo for the dissolve, and the `memories.json` entry.

The tunable parameters (line strength, line width, tone thresholds, crop) are saved with each entry, so a tile can be re-rendered later.

## Content formats

`content/places.json` (see `content/places.example.json`):

```json
{
  "places": [
    {
      "id": "family-town",
      "name": "Delft",
      "lat": 52.0116,
      "lon": 4.3571,
      "timeZone": "Europe/Amsterdam",
      "template": "canal-street",
      "seed": 7,
      "features": { "tower": "spire", "trees": true, "bridge": false }
    }
  ]
}
```

`content/memories.json` (see `content/memories.example.json`):

```json
{
  "memories": [
    {
      "id": "rome-2024-06",
      "tile": "memories/rome-2024-06.tile.png",
      "photo": "memories/rome-2024-06.jpg",
      "place": "Rome",
      "date": "2024-06",
      "note": "",
      "studio": { "crop": [0.1, 0.05, 0.8], "lines": 0.6, "tones": [0.35, 0.65] }
    }
  ]
}
```

Months are displayed through `Intl.DateTimeFormat('nl-NL', { month: 'long', year: 'numeric' })`, which gives "juni 2024".

`config.json` holds the night schedule (screen off and on times in the frame's local time), the default mode, view and place, the idle highlight timeout, auto-advance, caption on or off, and kiosk mode.

## Updates and deployment

- A systemd timer on the Pi runs `pi/update.sh` nightly. It does `git pull --ff-only`, then writes the commit hash to `version.json`.
- The app polls `version.json` and reloads itself when the hash changes. A nightly reload is also a safety net against slow memory leaks.
- Secrets never go in git. Wi-Fi credentials and remote-access keys live only on the Pi.
- The repo holds personal photos, so it stays private.

## Testing

- **Unit** (`node:test`): the clock (including the 2026 window when Europe has left daylight saving time and the US hasn't), astronomy against published sunrise and sunset times for a Dutch town, weather mapping, the reducer, layout, light schedules and the calendar.
- **Visual:** `tools/shots.mjs` renders a fixed matrix of places, times of day and weather kinds to PNG. Review the sheet after each scene change.
- **Soak:** the multi-day run on John's wall, with the dev panel's frame-rate readout and Chromium's memory watched.

## Performance

- Measure on the Pi itself, not on the PC.
- If a view stutters, try these in order: lower the offscreen resolution of scenes (painterly art tolerates upscaling), cache more layers, reduce particle counts, then move post-processing to WebGL.
