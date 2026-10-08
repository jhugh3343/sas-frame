# Plan

The goal is to give Sas the finished frame at Christmas 2026. This plan lists deliverables and tasks, ordered by what depends on what. By John's request it contains no dates or time estimates.

Tick a task when it's done. Each deliverable ends with something John can look at.

## Scope

**Must.** The gift works if only these ship.
- Her family's town as a live scene in single view, with real Dutch time, sun, moon and weather, and lights at night
- About a dozen memory tiles made from John's photos, in collage and single view
- Mode and view switching from the knob
- Screen off at night, and calm behaviour when offline
- Framed, mounted, and running unattended through a multi-day soak test

**Should.**
- Three or four places, and the live collage
- A December moment: window stars and candles, and a Christmas tree in a window over Christmas
- Dutch captions, and the tile slowly dissolving into its photo in single view
- Nightly updates from the repo, so John can add a tile after each future trip
- Remote access for fixes

**Could.**
- Delft-blue style for the live scenes, if John picks it over full colour
- More special days: Sinterklaas, New Year's Eve fireworks at midnight Dutch time, King's Day, her birthday, their anniversary
- A light sensor for automatic brightness

## 1 · Design settled and parts ordered

Deliverable: open questions answered, the parts list agreed, and the parts on order. Ordering comes first because delivery can take a while and every hardware task depends on it.

- [ ] John: find out her family's town (and ideally the street or view she'd recognise)
- [ ] John: decide the hardware budget and how big it should look on the wall
- [ ] John: say where Sas lives (her time zone)
- [ ] John: say what he writes his animations in, and whether any of it should be reused
- [ ] Claude: draft the parts list for that budget and size in `docs/hardware.md`, with a recommendation for each part
- [ ] John: order the parts
- [ ] John: start a shortlist of photos, noting the place and month of each (the final dozen get picked later)

## 2 · Working skeleton

Deliverable: the app runs in a browser with both modes and both views, using placeholder art.

- [ ] Repo setup: `package.json` scripts (`dev`, `test`, `shots`), a static dev server, Playwright. Update the Commands section of `CLAUDE.md`.
- [ ] `time/clock.js`: local time in an IANA zone, and building an instant from a local wall time. Test across the 2026 gap when Europe has left daylight saving time and the US hasn't yet (Europe changes on Oct 25, the US on Nov 1).
- [ ] `time/astro.js`: sun and moon position, moon phase, sunrise and sunset. Test against published times for a Dutch town to within a couple of minutes.
- [ ] `weather/`: Open-Meteo client with periodic refresh, cache and fallback, plus a pure mapping from WMO weather codes to render parameters and Dutch words, with tests.
- [ ] Content loading: `places.json` and `memories.json` with schema checks (bad entries are skipped and logged).
- [ ] App shell: state (mode, view, selection), collage and single layouts, transitions (zoom between collage and single, crossfade otherwise), and keyboard input standing in for the knob.
- [ ] Dev panel: time scrubber, time-lapse, weather override, caption toggle, frame-rate readout. Hidden with `?kiosk`.
- [ ] `tools/shots.mjs`: render a grid of fixed moments to PNG.

Done when John opens it locally, flips through the modes and views, and scrubs through a day.

## 3a · The live scene

Deliverable: a time-lapse of a day in her family's town, in several kinds of weather, that feels right to John.

- [ ] Sky driven by the sun's altitude: day, golden hour, blue hour, night, plus overcast grading
- [ ] Sun, and the moon with its real phase. Stars fade in at dusk and hide behind clouds.
- [ ] Clouds drifting with the real wind. Rain, drizzle, snow, fog and storms.
- [ ] Town layer, first version: a procedural Dutch canal street (gable types, brick, white window frames, hoist beams), a church tower, trees that change with the seasons, a quay, bicycles and street lamps
- [ ] Water with reflections and rain ripples
- [ ] Lit windows on repeatable schedules (weekday and weekend, people going to bed)
- [ ] Caption, for example "Delft · 21:14 · 8° · lichte regen"
- [ ] Style prototypes side by side, full colour and Delft blue. John picks one.
- [ ] Her family's town: shape the scene after what it really looks like, from John's photos or street-level imagery
- [ ] The December moment
- [ ] More places, and the live collage

## 3b · Delft tiles

Deliverable: the tile wall of their real memories on screen.

- [ ] `studio.html`: drop in a photo, crop, tune, preview, then export the tile PNG and its `memories.json` entry
- [ ] Filter: XDoG ink lines, two or three cobalt washes, brush texture
- [ ] Tile: glaze base, a central medallion with a double ring, corner motifs that join across neighbouring tiles, glaze texture and bevel
- [ ] Collage: tiles edge to edge with grout lines, where the corner motifs meet
- [ ] Single view: a large tile with a Dutch caption (place, month and year), slowly dissolving into the photo and back
- [ ] John: pick about a dozen photos, add each one's place and month, and make the tiles in the studio

## 4 · The finished frame

Deliverable: the frame on Sas's wall, working on her Wi-Fi.

Hardware (needs the parts):
- [ ] Pi set up: OS, user, SSH, auto-login, Chromium kiosk at boot, a local static server as a service, hidden cursor, no pop-ups
- [ ] Screen: native resolution, display switches off and on with the night schedule, no "No signal" banner
- [ ] Knob: rotary encoder with push button on the GPIO pins, mapped to arrow keys and Enter
- [ ] Profile on the Pi and optimise until every view animates smoothly
- [ ] Power: one cable from the wall if possible, and a plan for routing it
- [ ] Frame: shadow box with a mat cut to the screen, airflow for the Pi, a rear access panel, the knob mount, hanging hardware
- [ ] Network: her Wi-Fi, and calm behaviour when it fails
- [ ] Updates: nightly `git pull` and reload, with a version check
- [ ] Remote access (for example Tailscale)
- [ ] A spare SD card image

Finishing:
- [ ] John: personal touches (a hidden message, special dates)
- [ ] Feature freeze
- [ ] Soak test on John's wall: several days untouched, including a power cut and a Wi-Fi drop
- [ ] Fix everything the soak test finds
- [ ] A small card explaining the knob (John decides whether it's in Dutch)
- [ ] Wrap it
- [ ] At her place: Wi-Fi setup and mounting. Tell Sas that the frame updates over the internet and that John can add tiles remotely.
