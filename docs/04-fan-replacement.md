# 4. Fan Replacement

The B300R is loud because the stock fans are 80 mm Sanyo Denki San Cooler 80s rated 34 dB(A) at 3400 RPM, and the firmware happily runs them at full whack. Swapping them for Noctuas seems like a 5-minute job. It is not.

The fault you'll hit is the **FNLK** alarm code on the LCD ("UPS fan is locked / not operating"). Per the manual (table 2, section 4.1), the buzzer for this alarm **cannot be silenced** through the normal alarm-silence button — Borri specifically excluded fan, fan-fault-timeout, and overheat alarms from the silence list. The 5–15 minute delay-then-shutdown you observe is the fan-fault timeout window working through.

## Why a straight swap fails

The stock fan (`Sanyo Denki 9A0812S4D01`) has a **locked-rotor sensor** on the third wire, not a tachometer. The "S" in the part number is Sanyo Denki's suffix specifically for the LRS variant — the otherwise-identical "G" suffix (`9A0812G4D01`) is the tach-pulse version, which Borri did not specify.

The signal convention on the LRS wire is:

- Logic **LOW** (~0 V) = fan spinning normally
- Logic **HIGH** (~12 V) = rotor stalled → trip alarm

Inside, the fan has a Hall-driven transistor that pulls the line continuously to ground while the rotor turns. When it stalls, the transistor closes and the line floats up to +12 V via a ~5 kΩ pull-up on the UPS PCB.

A Noctua's third wire is a **tachometer** — a square wave, two pulses per revolution, swinging between low and high. The UPS firmware sees those high pulses and, after enough samples cross threshold, reads it as a stalled rotor. The 5–15 minute delay-before-alarm is the firmware's averaging/hysteresis window working through; it's not a thermal problem.

## The fix: 10 µF cap on the tach line

A simple low-pass filter converts the Noctua's tach square-wave into a near-DC low, which the UPS reads correctly as "spinning OK". If the rotor *does* genuinely stall, the Noctua holds the line high indefinitely and the cap charges to ~12 V — so genuine stall detection still works.

This was originally documented for the Eaton 9PX 1500VA (which has the same Sanyo Denki LRS architecture) at <https://blog.flobernd.de/2024/04/eaton-fan-mod/> — same circuit applies here.

See [`../circuits/lrs-cap-mod.md`](../circuits/lrs-cap-mod.md) for the wiring diagram and parts list.

## Pinout

### Stock Sanyo Denki fan

| Wire           | Signal                                                     |
| -------------- | ---------------------------------------------------------- |
| Red            | +12 V                                                      |
| Black          | GND                                                        |
| Yellow / White | Locked Rotor Sensor (open collector, pulled up internally) |

### Noctua 4-pin (PWM models)

| Wire   | Signal                                                              |
| ------ | ------------------------------------------------------------------- |
| Yellow | +12 V                                                               |
| Black  | GND                                                                 |
| Green  | Tachometer (open collector, 2 pulses/rev)                           |
| Blue   | PWM input (5 V, 25 kHz) — leave disconnected; UPS doesn't drive PWM |

### Noctua 3-pin (FLX/redux)

| Wire   | Signal     |
| ------ | ---------- |
| Red    | +12 V      |
| Black  | GND        |
| Yellow | Tachometer |

### Connector on the UPS

3-pin **JST-XH 2.54 mm** female header on the PCB. Crimp pins are `JST SXH-001T-P0.6` or equivalent; pre-crimped jumper kits work fine.

## Probe the existing pinout before you cut anything

With the stock fan still in place and the UPS running:

1. **Red → black**: should read ~12 V (sometimes ramped, 9–12 V depending on temperature).
2. **LRS → black** (yellow/white wire, with stock fan spinning): should sit at ≤0.5 V (logic LOW).
3. Stop the rotor briefly with a finger: LRS line should jump to ~12 V.

If you don't see this LRS behaviour, *don't* assume the cap mod will work. Some later production runs of these UPSes use proper tach pulses instead — in which case you don't need the cap, but you do need a fan that hits the firmware's expected RPM range.

## Recommended fan

Don't use a regular Noctua NF-A8 — its static pressure (~2.4 mmH₂O) is roughly half the Sanyo Denki's (4.8 mmH₂O), and a UPS chassis is a high-restriction airflow path. Use the **industrialPPC** range:

| Fan                              | RPM  | CFM  | Static pressure | Noise      | Notes                                                               |
| -------------------------------- | ---- | ---- | --------------- | ---------- | ------------------------------------------------------------------- |
| **NF-A8 industrialPPC-2000 PWM** | 2000 | 32.5 | 2.61 mmH₂O      | 19.4 dB(A) | **Recommended.** Quiet, IP52, 150,000 h MTTF.                       |
| **NF-A8 industrialPPC-3000 PWM** | 3000 | 50.2 | 5.88 mmH₂O      | 28.4 dB(A) | If you want to *exceed* stock spec. Still ~6 dB quieter than stock. |

Full comparison in [`06-noctua-comparison.md`](06-noctua-comparison.md).

## Mod procedure

> ⚠ **Mains and battery off, both physically disconnected. Wait 5+ minutes before opening for capacitors to discharge. UPSes can hold lethal voltage on their bus caps.**

1. Disconnect AC, then unplug the internal battery quick-connector behind the front panel.
2. Remove the top cover (typically T10 screws).
3. Locate the fan's 3-pin JST-XH connector on the main PCB.
4. Build the adapter cable per [`../circuits/lrs-cap-mod.md`](../circuits/lrs-cap-mod.md) — Noctua extension cable + 10 µF cap.
5. Mount the Noctua in the existing fan position. The screw spacing is standard 80 mm so M3/4 mount screws fit; if the holes are M4, use Noctua's NA-AVP1 anti-vibration mount kit or thread an M3-to-M4 adapter.
6. Connect via the adapter cable. Tuck the cap leg insulation away from the fan blades.
7. Replace cover, reconnect battery, reconnect AC, power on.

## Test sequence

1. Watch through self-test (LCD shows the Borri logo, fans spin up briefly).
2. **Run for at least 30 minutes idle** — the original failure window was 5–15 min. If it makes 30 min without alarm, the cap mod is doing its job.
3. **Confirm stall detection still works**: stop the fan briefly with a finger or a folded card. Within a few seconds, the UPS should display a fan/over-temp alarm. If it doesn't, you've over-filtered — drop the cap to 4.7 µF. If the alarm fires too eagerly during normal running, raise to 22 µF or 47 µF.
4. **Load test**: throw a real load on it (a kettle is around 2–3 kW, perfect for a 3 kVA unit) and watch UPS-internal temperature via NUT (`upsc borri@localhost ups.temperature`). It should stabilise well below 40 °C in a typical home environment.

## If the cap mod doesn't work

Two fallback paths:

### Option A — NE555 dummy tach generator

If your UPS firmware really wants to see actual tach pulses (rare on these), build a 555 oscillator producing ~120–140 Hz square wave (≈4000 RPM-equivalent, two pulses per rev) into the UPS tach pin. Power the Noctua independently. Trade-off: you lose all real fan-fault detection.

Reference: <https://www.ispcolohost.com/2015/02/27/quieting-down-the-noisy-cyberpower-ol3000rtxl2u-ups/>

### Option B — Tach donor

Power the Noctua independently from a small 12 V supply outside the UPS. Plug **only the Sanyo Denki's LRS wire** (yellow/white) into the UPS header, sharing GND. The Sanyo donor fan can sit in a box outside the UPS — it's only there to produce the LRS signal. Ugly but bulletproof.

## What you don't gain

- **PWM control.** The UPS doesn't drive a PWM line. Even a 4-pin Noctua runs at the speed dictated by whatever DC voltage the UPS pushes onto the +12 V rail. That's typically 80–100% duty in practice.
- **Variable airflow under load.** The firmware ramps the +12 V rail with thermal load, so you get *some* speed variation, just less than a stock setup with proper voltage ramping.

## Voids warranty

Yes, obviously. The UPS is also EOL, so warranty is moot anyway.
