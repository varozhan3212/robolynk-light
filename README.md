# RoboLynk Light — a talking desk companion on an ESP32-S3

A palm-sized desk robot you hold to talk to. Press the screen, ask a question,
let go — it answers out loud, shows an animated face while it speaks, and can set
alarms and timers or control your smart lights. Everything runs on one
ESP32-S3 board with a 2.8" colour screen, and it updates itself over Wi-Fi.

This is the first working prototype of RoboFoxy. It is built from an off-the-shelf
dev board, so you can reproduce it in an afternoon without a soldering iron.

> **Photo slot 1 — hero shot.** Robot on a desk, screen showing the face, lit from
> one side, dark background. Landscape.

---

## What it does

- **Hold-to-talk voice chat.** Press and hold the screen, speak, release. It
  remembers the last six messages, so follow-up questions work.
- **Animated face** while it speaks, drawn from frames held in RAM.
- **Alarms and timers** by voice or from the settings screen.
- **Philips Hue control** — "turn on the lights", "dim the bedroom",
  "lights to 50%".
- **Wi-Fi setup from your phone.** The robot raises its own hotspot and serves a
  small page; you type your network details there. No keyboard needed.
- **Updates itself.** On boot it checks for new firmware and installs it.
- Optional GPS module for "where am I", and Bluetooth LE scanning.

Reminders and alarms here are a convenience feature. Do not rely on them for
anything that matters medically.

> **Video slot — 45-60 seconds.** One unbroken take: press, ask "why is the 4th of
> July important?", release, wait for the spoken answer. Then one voice command
> that visibly changes something in the room, e.g. the lights. Real audio from the
> robot's own speaker, no voice-over, no captions needed.

---

## Parts

| Part | Notes |
|---|---|
| ES3C28P (or ES3N28P) ESP32-S3 board | 2.8" 240×320 ILI9341 display, capacitive touch, ES8311 audio codec, speaker amp, microSD slot, USB-C. This single board is most of the build. |
| microSD card (optional) | Only needed for the higher-frame-count face animation and music. The robot works without one. |
| Speaker | Small 8Ω unit; most of these boards ship with one. |
| GPS module (optional) | UART, 3.3V. Skip unless you want location answers. |
| 3D-printed case | Four printed parts. Confirmed to fit this board. Files below. |
| 630 mAh LiPo battery | 1-cell (3.7 V) with a JST-PH connector. Charges over the board's USB-C. |
| M2 heat-set inserts x4 | Pressed into the printed shell. |
| M2 screws x4 | Secure the case. Length to suit your print; 6-8 mm is typical. |
| Hot glue | Holds the board in the shell. |

No custom PCB, and no wiring at all unless you add GPS.

> **Photo slot 2 — the parts laid flat**, board, card, speaker, side by side on a
> plain surface, shot from directly above.

---

## Build

### 1. Flash the firmware

The image contains the runtime, the application and its files in one write.
If you are reflashing a board that has joined a network before, erase it first:
Wi-Fi credentials live in a flash region a plain overwrite leaves behind, so
skipping the erase passes your network password on with the board.

The whole firmware is a single file. **In a browser**, with nothing installed:

1. Open **https://espressif.github.io/esptool-js/** in Chrome or Edge
2. **Connect**, pick the board's serial port
3. Add `robolynk_light_1.3.199.bin`, set the **flash address to `0`**
4. **Program** — about 70 seconds

Or from a terminal, if you prefer:

```bash
esptool --port /dev/cu.usbmodemXXXX write_flash 0x0 robolynk_light_1.3.199.bin
```

**When it finishes, unplug the board and plug it back in.** The flasher reports a
reset, but on this chip that does not actually restart the application — without a
physical replug the screen stays dark and it looks like the flash failed.

### 2. First boot

Power it on. It shows a splash, then **TAP TO BEGIN**.

1. Tap the screen and accept the terms.
2. Choose **Phone Setup**. The robot starts a hotspot called `RoboLynk-XXXX` and
   shows the password on screen.
3. Join that network from your phone, open `192.168.4.1`, pick your Wi-Fi from the
   list and enter the password. Only 2.4 GHz networks appear — the robot cannot use
   5 GHz.
4. The robot saves them, restarts, and connects.
5. Go to **Settings → Info** and note the **Setup Code** (six characters, like
   `GT2-UQK`).
6. On **robolynk.cloud**, create an account, then **Add Robot** → *RoboLynk Lite* and
   enter that code.

Until you add it to an account the robot will say so rather than answering — it is
online, just not linked to anyone yet.

> **Photo slot 3 — the hotspot screen**, showing the network name and password as
> the robot displays them. Photograph the screen directly, straight on.

### 3. Talk to it

Press and hold the screen, ask something, release. There is a few seconds' pause
while it transcribes and thinks, then it speaks the answer.

---

## Enclosure

A four-part printed case, screwed together. Verified against this board.

| Part | Qty | Notes |
|---|---|---|
| `front_shell_v22.stl` | 1 | Holds the display; the screen bezel sits flush |
| `back_cover_v22.stl` | 1 | Closes the rear; USB-C and microSD stay accessible |
| `clips_v22.stl` | 1 plate | Internal clips |
| `buttons_v22.stl` | 1 plate | Caps for the reset and boot buttons |

You also need **4 x M2 heat-set inserts**, **4 x M2 screws**, and a glue gun.

### Printing

- **Material:** PETG or PLA — both work
- **Layer height:** 0.2 mm
- **Infill:** 20%
- **Supports:** **none needed.** The parts are designed to print unsupported.
- **Orientation:** as the files load on the plate; no reorienting required

Printed on a Bambu Lab with stock profiles. Print the clips and buttons at the same
settings — they are small and fit alongside either shell.

### Assembly

1. **Press the four M2 inserts** into the shell. A soldering iron on low, held
   straight down, seats them cleanly — go slowly and let each one cool.
2. Drop the board into the front shell, display first, and seat the bezel.
3. **Hot-glue the board in place.** It needs it: nothing else stops the board
   shifting, and a loose board puts strain on the USB-C port every time you plug in.
   A few beads at the corners is enough — keep glue off the connectors and clear of
   the battery.
4. Connect the battery and tuck it in so it is not pinched by the shell.
5. Push the button caps into their openings from outside.
6. Fit the back cover and secure with the **four M2 screws**.

### Battery

A single-cell **630 mAh LiPo (3.7 V)** with a JST-PH connector. It charges over the
board's USB-C, so no separate charger is needed.

Check the polarity against your board before plugging it in — JST-PH connectors are
not standardised between suppliers, and a reversed cell will damage the board. If
the housing is wired backwards, move the pins rather than forcing it.

> **Photo slot 4 — the four printed parts** laid out before assembly, plus one of
> the finished case closed, three-quarter view.

---

## How it works

Audio is captured through the ES8311 codec over I²S at 16 kHz and streamed to a
server over a WebSocket while you hold the screen. The server transcribes it,
generates a reply, and streams the spoken audio back, which the robot plays while
advancing the face animation between audio writes.

Three things turned out to matter much more than expected:

**Start the microphone before anything else.** The original code updated the
screen and ran a garbage collection before switching the microphone on. Users
start speaking the moment the screen says LISTEN, so the opening word was simply
never recorded — "why is the 4th of July important" arrived as "July is
important". The microphone now starts first and the housekeeping happens behind
it, buffering.

**Don't sip from the audio buffer.** The capture loop read one 30 ms slice per
network send. Each send costs around 110 ms, so it captured barely a quarter of
real time and the rest piled up until the buffer overflowed. Reading until the
buffer is empty, and sending larger batches, fixed it.

**Give streamed audio somewhere to wait.** Playing straight from the network into
a 170 ms hardware buffer means any late packet is an audible gap. Holding ~600 ms
back before starting playback, into a larger buffer, removes it.

None of these were hardware limits. All three looked like one.

---

## Things worth knowing before you build

- **2.4 GHz Wi-Fi only.** No 5 GHz.
- **The face animation is the memory budget.** Frames are held in RAM; the loader
  reserves headroom for the TLS handshake and audio buffers and loads however many
  frames fit. Take that headroom away and it boots to a white screen.
- **A microSD card on a bit-banged SPI bus is slow** — around 93 KB/s here, which
  means a multi-megabyte animation takes over a minute to load at boot. Worth
  planning for if you store large assets.
- **It needs a server.** The speech and language work does not happen on the
  board. This project is the device half.

---

## Status

Working prototype, in daily use. Radio streaming, camera vision and wake-word
detection are present in earlier code but are switched off here — they are not
finished and are not part of this build.

Built by RoboLynk LLC, Los Angeles.
