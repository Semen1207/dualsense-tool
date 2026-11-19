# dualsense-tool

A single-file, zero-dependency web utility for the **Sony DualSense** (and DualSense Edge), built on **WebHID**.

Double-click the HTML file, connect your controller, and go. No build step, no `npm install`, no native driver.

> **"And it all works."** — USB and Bluetooth, touchpad, clicks, battery, lightbar, and adaptive triggers.

---

## Features

| Feature | Status |
|---|---|
| **Touchpad** (2 fingers, position + pressure-proximity) | ✅ |
| **Touchpad click** | ✅ (lightbar flashes red) |
| **Battery level + charging status** | ✅ |
| **Lightbar control** | ✅ (battery-gradient idle, touch-brightness, click-red, trigger-hold-blue) |
| **Adaptive triggers L2 / R2** (force + start) | ✅ |
| **USB + Bluetooth auto-detection** | ✅ (report `0x02` vs `0x31` + CRC32) |
| **Verbose live log** (packet/s, color-coded) | ✅ |

---

## Quick start
Open THIS link:

> **Browser support:** WebHID is required. Firefox and Safari do **not** support it. Chrome/Edge on desktop only.

---

## How it works

The utility subscribes to HID input reports and parses:

- **Touch points** — packed 4-byte blocks (id/touch flag, X, Y)
- **Battery** — level + status nibble from the battery byte
- **Buttons / triggers** — analog L2/R2 values and digital fallback bits
- **Connection type** — inferred from the input report bit length (504 = USB, 616 = Bluetooth)

Output reports are sent for the lightbar + adaptive triggers:

- **USB** → report `0x02`, no CRC
- **Bluetooth** → report `0x31`, with a sequence nibble and a CRC32 (`0xA2` prefix)

Trigger state is re-sent with **every** output report, so lightbar updates never wipe your trigger effect.

---

## Adaptive triggers

Most DualSense web utilities skip adaptive triggers. This one doesn't.

Each trigger (L2 / R2) exposes:

- **Force** — resistance strength (`0–255`, `0` = free travel)
- **Start** — where along the pull the resistance begins (`0–230`)

Both are independent, and a **Reset** button returns them to free travel.

> Note: trigger effects are write-only — the controller gives no readback. You have to physically pull the trigger to feel the effect.

---

## Known limitations

- No `disconnect` handler yet — if the controller sleeps or the cable is pulled, sliders stay enabled until reload.
- Adaptive trigger effects use a single resistance band (no multi-zone / vibration effects yet).
- No haptics/rumble control yet.

---

## Roadmap / ideas

- [ ] Handle `device.disconnect` — reset UI state cleanly
- [ ] Trigger presets (Soft, Resistance, Pulse, Bow tension…)
- [ ] Visual arc showing `start` vs `force` per trigger
- [ ] Multi-zone trigger effects
- [ ] Rumble / haptics section

---

## License

MIT — do whatever you want, a link back is nice.

---

## Acknowledgements

Built with the WebHID API and a lot of trial-and-error on the DualSense output report layout.
Shoutout to the HTML5 beagle. 🐶
