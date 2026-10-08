# Hardware

**Status:** the direction below is a recommendation and isn't final. The parts list waits on John's budget and on how big the frame should look on the wall.

## Recommended direction

| Part     | Recommendation | Why |
|----------|----------------|-----|
| Computer | Raspberry Pi 5, or a Pi 4 | Runs Chromium well, has GPIO pins for the knob, built-in Wi-Fi, and is silent with a passive cooler. The Pi 5 has a built-in real-time clock that keeps time through power cuts once its small backup battery is fitted. A Pi Zero 2 W is cheaper and cooler but may be too slow for smooth animation (an untested assumption). |
| Screen   | Matte IPS panel, 13 to 16 inches, with HDMI input | Portable USB-C monitors fit well: thin, light and often matte. |
| Frame    | Deep frame or shadow box, with a mat cut to the visible screen area | The mat is what makes a screen read as art. The frame needs a rear access panel and airflow for the Pi. |
| Knob     | Rotary encoder with a built-in push switch, and a brass or wooden knob cap | Physical and tactile. Sas prefers physical things. |
| Storage  | High-endurance microSD card, or a small USB SSD | Survives being written to every day. |
| Power    | One cable from the wall, if possible | Fewer visible cables on her wall. |

## Choosing the screen

- **Matte finish.** A glossy screen reads as "a tablet on the wall."
- **IPS panel,** for wide viewing angles across a room. **Avoid OLED:** static scenes burn in over months.
- **Real HDMI input.** The Pi outputs micro-HDMI, so the cable or adapter must match. A display that only takes video over USB-C won't work with a Pi.
- **Native resolution and aspect ratio** (16:9 or 16:10). The app adapts to either.
- **Behaviour with no signal.** It should go to sleep cleanly, then wake and select its input by itself when the signal returns.
- **Dims low enough** for an evening room.
- **Power input and draw** (USB-C or barrel) for the one-cable plan.
- **Bezel and buttons** that can hide behind the mat or be reached from the back.
- **Depth and mounting points** that fit inside the frame.

## Pitfalls to design around

1. **Glow.** An LCD's backlight glows even when the image is black. Switch the display output off on a schedule instead of drawing black.
2. **"No signal" banners.** Many monitors show a bright message when the Pi turns its output off. Test the chosen screen for this before committing to it.
3. **Power cuts.** She may unplug it to move or clean it. Plan for:
   - SD card corruption: a read-only root filesystem (overlay), or at least a high-endurance card plus a cloned spare
   - Chromium's "Restore pages?" prompt after an unclean shutdown: kiosk flags or a clean profile
   - The clock: fetched from the internet at boot, with the Pi 5's battery-backed clock as a backup
4. **Heat and noise.** A Pi 5 under load runs warm, and a fan would be audible in a bedroom. Use a passive cooler and vent the frame.
5. **Cables.** A Pi and a screen normally mean two power cables. A multi-port USB-C charger can feed both from one wall cable. A Pi 5 on a non-official supply runs at 5V/3A with limited USB current, which should be fine here (verify). Plan the route: an outlet near the spot and a paintable cord cover.
6. **Kiosk polish.** Hide the cursor, stop the OS blanking the screen (the app's schedule controls it), suppress update and keyring pop-ups, use a quiet boot with no boot text, and log in automatically.
7. **Wi-Fi at her place.** Pre-load her network if John knows the details, otherwise plan a setup step on Christmas Day. Credentials stay on the Pi, never in git.
8. **Remote access.** Tailscale or similar lets John fix things and add tiles without visiting. It's a device on her network, so tell her it's there.
9. **Working from Windows.** Flash the card with Raspberry Pi Imager, whose customisation step can preset the user, SSH and Wi-Fi. Connect with the OpenSSH client built into Windows. Keep scripts in LF line endings.
10. **Changing desktop software.** Raspberry Pi OS's desktop (compositor, autostart files, display power commands) has changed between releases. Follow current documentation rather than older guides.

## Knob wiring (sketch)

- Wire the encoder's A, B and switch pins to three GPIO pins with pull-ups, and the common pin to ground.
- Turn the knob into key presses: a device-tree overlay or a small input daemon maps rotation to Left and Right, a short press to Enter, and a long press to Space. The web app only listens for keys, so the same app runs on John's PC with a keyboard.
- Check the exact overlay or daemon against the current OS.

## Parts list

Pending John's budget and size. To be filled in this shape:

| Part | Pick | Why | Price | Link | Ordered |
|------|------|-----|-------|------|---------|
|      |      |     |       |      |         |
