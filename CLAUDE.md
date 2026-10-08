# Sas's Frame

A Christmas 2026 gift from John to his girlfriend Sas: a framed screen for her wall. It shows two things:

1. **Live places.** Animated scenes of places that matter to her, starting with her family's town in the Netherlands, in that place's real time, sun, moon and weather.
2. **Memories.** John's photos of their time together, turned into Delft Blue tiles by code.

This file is the project memory. Read it at the start of every session, then open `docs/plan.md` to find the next task.

## The people

- **Sas** receives the gift. She is Dutch, very proud of her heritage and culture, and close to her family in the Netherlands, where she has spent a lot of time. She loves travelling and visiting new places. She prefers physical gifts to digital ones.
- **John** is building it, and he is your user. He's a software developer, comfortable with most stacks and happy to learn. He has been making animations with pure functions in code, so that style is welcome here.

## Ground rules

- **It's a surprise.** Never contact Sas or anyone else. Never put her name, photos or details anywhere public. The repo stays private.
- **Who does what.** John chose to have Claude write most of the code. John decides the look, the places, the photos and the personal touches. Bring him each decision with a recommendation, and don't ask him to write code.
- **Ask John first** before anything that costs money, anything that touches Sas's home network or accounts, and any change to the agreed concept in `docs/decisions.md`.
- **Plans are tasks and deliverables.** Never add dates, schedules, timelines or time estimates. John asked for this explicitly.
- **Keep the record.** Log each decision in `docs/decisions.md`. Tick tasks in `docs/plan.md` as they finish. Keep the open questions below in sync.

## The product at a glance

|                 | Collage view                                  | Single view                                  |
|-----------------|-----------------------------------------------|----------------------------------------------|
| **Live places** | A grid of small live scenes, one per place    | One place, full screen                       |
| **Memories**    | A wall of Delft tiles (a *tegeltableau*)      | One tile, large, with its place and month    |

A knob on the side of the frame controls it: turn for next or previous, press to switch mode, hold to switch view. The full experience is described in `docs/design.md`.

## Stack and conventions

- **A static web app.** Plain HTML, CSS and JavaScript ES modules, with no framework and no build step, so the Pi can serve the repo as it is. Start with Canvas 2D, and use WebGL only if profiling on the Pi demands it.
- **The frame** is a Raspberry Pi running Chromium in kiosk mode behind a matte screen. This is the recommended direction and isn't final yet (see `docs/hardware.md`).
- **Pure core, thin edges.** Astronomy, time zones, weather mapping, light schedules and layout are pure functions with unit tests. A scene renders the same way for the same place, time, weather and seed, so any moment can be previewed and screenshotted. Only the clock, network, input and canvas have side effects.
- **Content is data.** A place or a memory is an entry in `content/*.json` plus its assets. Adding one never needs a code change.
- **Time** always uses IANA zone names through `Intl` (for example `Europe/Amsterdam`), never fixed offsets.
- **Never show an error on the wall.** If the network is down, use the last known weather. If data is bad, skip it quietly and log to the console.
- **Text Sas sees is Dutch:** place names, weather words, months. Developer UI and code are in English.
- **Dev controls** (time scrubber, time-lapse, weather override) are hidden in kiosk mode.
- **Performance:** keep animation smooth on the Pi at the panel's native resolution, including the collage. Cache static layers, and never run per-pixel JavaScript on every frame.
- **Cross-platform:** development happens on John's Windows PC, and the frame runs Linux. Keep line endings LF (`.gitattributes` enforces it) and keep scripts portable.

## Commands

None exist yet. The first skeleton task in `docs/plan.md` sets these up:

- `npm run dev` serves the app and the tile studio locally.
- `npm test` runs `node:test` unit tests for the pure modules.
- `npm run shots` uses Playwright to render fixed moments (place × time × weather) to PNG for visual review.

Update this section as they land.

## Where things are

- `docs/plan.md`: deliverables, tasks and scope. Start here each session.
- `docs/design.md`: what Sas sees and does, covering modes, views, the knob, live scenes, tiles and special days.
- `docs/architecture.md`: modules, data formats, rendering, weather, updates and testing.
- `docs/hardware.md`: the Pi, screen, frame, knob, power and network, plus known pitfalls. The parts list is pending.
- `docs/decisions.md`: the decision log and open questions.
- `content/`: places, memories and their assets. `*.example.json` files show the data shapes.

## Open questions

These block work. Keep this list in sync with `docs/decisions.md`.

- **Her family's town.** John is finding out. This blocks the town-specific scene, not the engine.
- **Hardware budget, and how big it should look on the wall.** These block the parts list.
- **Where Sas lives (her time zone).** This decides which hours of the Dutch day she'll see most.
- **What John writes his animations in.** His existing work might seed the live scenes.
- **Full colour or Delft blue for the live scenes.** Decide from side-by-side prototypes.
