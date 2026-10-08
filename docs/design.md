# Design

What Sas sees and does. The technical counterpart is `docs/architecture.md`.

## The idea

A window home. Sas glances up and sees her family's town as it is right now: the same sky, the same weather, the same hour. Turn the knob, and the wall becomes their story told in Delft tiles.

It should behave like a piece of art, not a gadget: no notifications, no menus on the wall, nothing that asks for attention.

## Modes and views

|                 | Collage view                                       | Single view                                              |
|-----------------|----------------------------------------------------|----------------------------------------------------------|
| **Live places** | A grid of small live scenes, one per place         | One place, full screen, with a small caption             |
| **Memories**    | A wall of Delft tiles in date order: their story   | One tile, large, with its place and month, dissolving into the photo |

At power-on it shows Live places in single view, on her family's town.

## Controls: the knob

One knob on the side of the frame.

| Gesture | Single view                      | Collage view                              |
|---------|----------------------------------|-------------------------------------------|
| Turn    | Next or previous place or tile   | Move the highlight                        |
| Press   | Switch between Live places and Memories, keeping the view | Same                    |
| Hold    | Go to the collage, with this item highlighted | Open the highlighted item in single view |

The highlight in the collage fades out after a few idle seconds, so the wall looks clean when nobody is touching it. Auto-advance in single view is off by default and can be turned on in config if John wants the frame to wander by itself.

## Live places

### Composition

The view looks across a canal at a row of houses, with the sky above and water below that mirrors it. The sky takes about half the frame, because sky and light carry the time and the weather. A small caption sits in a corner.

Places that aren't canal towns get their own template later (a polder village with a windmill, the coast, or a city skyline traced from a photo). The sky, sun, moon, weather and water are shared by every template.

### Real time, sun and moon

Everything follows the place's real clock and real astronomy. The sun moves from left to right (east to west, with the viewer facing south). At Dutch latitudes the winter sun stays low, at about 15° at noon around Christmas, so even midday light in December is low and golden. The moon shows its true phase and position. Stars appear only when it's dark and clear.

### Night is the main scene

Around Christmas the sun is up in the Netherlands for less than eight hours (in Delft it rises at about 08:50 and sets at about 16:35). Whatever time zone Sas lives in, most of what she'll see through the winter is dusk and night. If she lives in the Americas, her evenings are the small hours of the morning in the Netherlands.

So night gets the most care:
- windows going dark one by one as the town goes to bed
- street lamps and their reflections
- the moon on the water
- stars on clear nights

### Weather

| Weather          | How it looks                                                              |
|------------------|---------------------------------------------------------------------------|
| Clear            | Clean gradient sky, crisp shadows, stars at night                          |
| Some clouds      | Clouds drifting with the real wind direction and speed                     |
| Overcast         | Flat grey light, desaturated colours, no sun disc                          |
| Drizzle and rain | Streaks angled by the wind, ripples on the water, wet sheen on the quay   |
| Snow             | Drifting flakes. Roofs and the quay turn white after it has been snowing.  |
| Fog              | Thicker near the water, softening the lamps into halos                     |
| Storm            | Heavy rain, trees moving, the occasional flash of lightning                |

### Lights and life

- Each window has its own repeatable routine: early risers, late sleepers, weekend lie-ins.
- Many Dutch homes leave their curtains open in the evening, so lit rooms show a lamp or plants on the sill.
- One window flickers blue with a television.
- Bicycles lean against the canal railing.
- Rare events make it worth a glance: a cyclist crossing, a boat gliding past, a bird on a roof.

### Seasons

Trees are bare in winter, fresh green in spring, full in summer and orange in autumn. Window boxes hold tulips in spring.

### Special days

These follow Dutch local time. John picks which ones to include.

| Day                 | What appears                                                              |
|---------------------|---------------------------------------------------------------------------|
| Advent to Christmas | Paper stars and candles in windows, a Christmas tree in one window         |
| Sinterklaas (5 Dec) | A lit evening, a horse silhouette on a roof                                |
| New Year's Eve      | Fireworks over the town at midnight Dutch time, then lingering smoke      |
| King's Day          | Orange bunting and flags                                                  |
| Her birthday        | A flag out and garlands in a window                                      |
| Their anniversary   | Something only the two of them would recognise                             |

### Style: full colour or Delft blue

This is still open. The plan is to prototype both and let John choose.

- **Full colour.** Painterly flat shapes, warm lamps against a cool dusk. It carries the most information about the time of day.
- **Delft blue.** The whole frame becomes blue and white. Ink density stands for darkness, so night is a deep cobalt wash with the windows left as white glaze. This ties both modes together into one object, a tile that comes alive, but it loses sunset colour.

### Caption

Example: "Delft · 21:14 · 8° · lichte regen". It shows the place, local time, temperature and weather, all in Dutch. It's small and low-contrast, and it can be turned off.

## Memories: Delft tiles

### What makes it look like Delftware

A blue-tinted photo is not enough. Hand-painted Delft tiles have:
- ink outlines drawn with a brush
- two or three flat washes of cobalt, not a smooth gradient
- lots of white glaze left unpainted
- the scene inside a central medallion
- corner ornaments
- a glaze surface with slight variation in colour and a soft highlight

### Tile anatomy

- A square tile with a central medallion and a double ring around it
- Corner motifs drawn as quarter ornaments, so where four tiles meet in the collage they form one whole ornament. For reference, see the *ossenkop* and *spinnekop* corners on 17th-century Dutch tiles.
- A slightly bluish off-white glaze, faint mottling, a soft highlight and a bevelled edge

### Collage: the tegeltableau

- Tiles sit edge to edge with thin grout lines, and their corner ornaments meet.
- They're in date order, so the wall reads as their story from the first trip onward.
- The grid adapts to the number of tiles.

### Single view

- The tile is large, with a caption beside it showing the place and month in Dutch, for example "Rome · juni 2024", plus an optional one-line note from John.
- The tile slowly dissolves into the original photo, then back into the tile.

### Growing over time

After each trip they take together, John can add a tile, and the frame picks it up with its nightly update. A new tile could arrive with a small moment, for example sliding into its place on the wall.

## On the wall

- **It should look like art, not a screen.** That means a matte screen, a deep mat, brightness matched to the room, no cursor, no boot text and no black bars.
- **Night schedule.** The screen switches off at bedtime and back on in the morning. This matters most if it hangs in her bedroom.
- **Orientation.** Landscape is recommended, for the wide sky and water and the collage grids. Portrait would suit tall canal houses. Decide this together with the screen.
- **After a power cut**, it comes back quietly to the default view.

## Personal touches

Ideas for John to choose from or replace:
- A hidden message that appears on one particular date
- A tile that only shows on their anniversary
- Her family's house, with its windows lit at the hours they're actually home
- A boat with her name painted on the stern, drifting past now and then
- A real ceramic tile printed from their favourite memory, given alongside the frame
