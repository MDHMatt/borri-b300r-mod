# 9. LCD Operation

From manual section 4.1, table 2.

## Buttons

The LCD panel has four buttons. Their behaviour depends on press duration:

| Button                 | Action                                     | Effect                               |
| ---------------------- | ------------------------------------------ | ------------------------------------ |
| **🔌 Power** (ON/OFF)  | Press & hold ≥3 sec                        | Turn UPS on / off                    |
| **🔌 Power**           | Press & hold ≥2 sec (mains disconnected)   | Recover from fault state             |
| **🔌 Power**           | Tap (0.5 sec) while in settings            | Exit settings mode                   |
| **🔔 Test/Silence**    | Press & hold 3 sec                         | Run basic functional test            |
| **🔔 Test/Silence**    | Press & hold 10 sec                        | Run battery life test                |
| **🔔 Test/Silence**    | Tap (1 sec)                                | Silence current alarm buzzer         |
| **↕ Select**           | Tap                                        | Cycle through menu options           |
| **↩ Enter**            | Press & hold ≥3 sec                        | Enter settings mode                  |
| **↩ Enter**            | Press & hold ≥1 sec                        | Enter the currently-selected setting |
| **↩ Enter**            | Tap (1 sec)                                | Confirm setting value                |
| **↩ Enter**            | Press & hold 3 sec                         | Exit settings mode                   |

## Display fields

LCD shows simultaneously:

- **Input frequency and voltage** (left side)
- **Output frequency and voltage** (right side)
- **Input plug indicator** — lit when charging from mains
- **Output plug indicator** — lit per active load segment (LS1 / LS2)
- **Battery capacity bar** — 5 segments, each = 20% capacity
- **Load capacity bar** — 5 segments, each = 20% rated output
- **Status string** (4-char alphanumeric — see [`07-alarm-codes.md`](07-alarm-codes.md))
- **Warning indicator** — lit on alarm or fault
- **Settings indicator** — lit when in settings mode

The backlight is normally blue with black text. On critical alarms (UPS fault, low battery, overload), it switches to **red**.

## Configuring settings — generic procedure

1. **Enter settings mode**: hold ↩ Enter for 3+ seconds. Settings indicator lights up.
2. **Select setting**: tap ↕ Select to cycle through (`OPV`, `AVR`, `EbM`, `TEST`, `AR`, `GF`, `bZ`, `LS1`, `LS2`).
3. **Enter the setting**: hold ↩ Enter for 1+ second. The value flashes.
4. **Choose value**: tap ↕ Select.
5. **Confirm**: hold ↩ Enter for 1 second.
6. **Exit**: hold ↩ Enter for 3 seconds, or tap 🔌 Power.

## Common configurations

### Set up after initial install

1. Plug in to mains, hold 🔌 Power 3 sec to turn on (display goes from `STbY` to `NORM`)
2. If you have EBMs, set `EbM` count to match (0–9, see [`07-alarm-codes.md`](07-alarm-codes.md))
3. If using generator power, set `AVR` to `002` (Generator mode) — opens the transfer-frequency window to 40–70 Hz
4. Decide on `GF` (Green Function): with `001` enabled, UPS auto-shuts when load is insignificant *while on battery* — useful for homelab to preserve battery, annoying if you want max runtime regardless

### Configure load segments

The 8 output sockets are split into two banks (LS1 = top 4, LS2 = bottom 4). Default both `001` (on). You can shed LS2 (e.g. monitors, secondary equipment) during long battery runs by setting it to `000`.

LS1 / LS2 settings can be changed while the UPS is running.

### Battery test procedure

Before testing:

- Battery must be fully charged (let it sit on mains 48 hours after installing or replacing batteries)
- UPS must be in `NORM` mode with no active alarms
- Don't connect/disconnect equipment during the test

Then: hold 🔔 Test for **10 seconds**. Status display changes to `TEST`. The UPS will discharge the battery briefly to verify it can support the connected load.

For a 10-second basic functional test instead: hold 🔔 Test for **3 seconds**.

## Default factory settings

Per the manual, factory defaults are:

| Setting                 | Default            |
| ----------------------- | ------------------ |
| `OPV` (output voltage)  | 230 V (UK/EU)      |
| `AVR` (input range)     | `000` Normal range |
| `EbM` (battery modules) | `0` (no EBMs)      |
| `TEST` (auto self-test) | `001` Enable       |
| `AR` (auto restart)     | `001` Enable       |
| `GF` (green function)   | `000` Disable      |
| `bZ` (buzzer)           | `001` Enable       |
| `LS1` / `LS2`           | `001` On           |
