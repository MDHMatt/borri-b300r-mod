# LRS Cap Mod — Circuit and Build

The minimal circuit to convert a Noctua tachometer signal into a locked-rotor-sensor signal that the Borri B300R firmware accepts.

See [`../docs/04-fan-replacement.md`](../docs/04-fan-replacement.md) for background on why the mod is needed.

## Bill of materials

| Item                 | Spec                                                                                   | Approx cost (UK) |
| -------------------- | -------------------------------------------------------------------------------------- | ---------------- |
| Replacement fan      | **Noctua NF-A8 FLX** (3-pin, 80 mm, includes L.N.A. and U.L.N.A. adapters) — see `../docs/06-noctua-comparison.md` for trade-offs and alternatives. The B300R doesn't drive PWM, so a 4-pin fan buys you nothing. | £15–18           |
| Capacitor            | **10 µF / 50 V radial electrolytic** — Nichicon UVZ, Panasonic FC, Rubycon ZL all fine | < £0.50          |
| Connector (UPS-side) | 3-pin JST-XH 2.54 mm female housing + crimp pins                                       | £1               |
| Wire                 | Or just splice with the Noctua extension cable that ships in the box                   | £0               |
| Heatshrink           | 2.5 mm and 5 mm sizes                                                                  | £0.20            |
| Tools                | Soldering iron, crimper (optional but tidier), multimeter                              | —                |

Total parts: under £30 if you don't already have the bits in a drawer.

## Circuit

```
                    UPS PCB
                       │
  +12V ───────────────►├ JST-XH pin 1 ─────────┐
                       │                        │
   GND ───────────────►├ JST-XH pin 3 ─────────┤
                       │                        │
                       │  ┌─ ~5kΩ pull-up ──┐   │
                       │  │                  │   │
                       │  │  +12V            │   │
                       │  │                  │   │
   LRS in ◄────────────┤< pin 2 ─────────────┘   │
                                                  │
                  ─────── adapter cable ──────    │
                                                  │
   Noctua tach ───────────────────────────────────┤
   (green/yellow)                                 │
                              ┌────────┐          │
                              │  10 µF │          │
                              │   50V  │          │
                              │  cap   │          │
                              │   ─    │          │
                              │   +    │          │
                              └───┬────┘          │
                                  │               │
                                 ─┴─  GND ────────┘

   Noctua +12V (yellow) ────────────────────► UPS pin 1
   Noctua GND  (black)  ────────────────────► UPS pin 3
   Noctua tach (green/yellow) ─┬────────────► UPS pin 2  (LRS)
                               │
                            10 µF cap
                          (- leg to GND)
                               │
                               ▼
                              GND
```

## Wiring map (cleaner version)

| UPS JST-XH pin | UPS signal | Noctua wire                    | Notes                                          |
| -------------- | ---------- | ------------------------------ | ---------------------------------------------- |
| 1              | +12 V      | Yellow (4-pin) / Red (3-pin)   | Power to fan                                   |
| 2              | LRS in     | Green (4-pin) / Yellow (3-pin) | **Tach line — also where the cap goes**        |
| 3              | GND        | Black                          | Common ground                                  |
| —              | —          | Blue (4-pin only)              | **Leave disconnected** — UPS doesn't drive PWM |

The 10 µF capacitor connects between the **tach line (UPS pin 2)** and **GND**, with its **negative leg to GND** (the side with the white stripe on the can; positive leg to the tach line).

## Why 10 µF

A 10 µF cap into the UPS's ~5 kΩ internal pull-up gives an RC time constant of ~50 ms. The Noctua's tach pulse rate at 2000 RPM is ~67 Hz (15 ms period), so the cap charges much more slowly than the pulse cycles, and the line stays close to 0 V — what the firmware reads as "fan spinning fine".

If the rotor stalls and the Noctua pulls the tach line high indefinitely, the cap charges to ~12 V over a few hundred ms, and the firmware sees logic HIGH = stall.

## Tuning

If you observe issues:

| Symptom                                                                | Try                                                                                               |
| ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| Fan alarm still trips after 5–15 min                                   | Increase to **22 µF**, then **47 µF** if needed                                                   |
| Stall detection no longer works (fan held still doesn't trigger alarm) | You've over-filtered — drop to **4.7 µF**                                                         |
| Alarm trips intermittently when the load changes                       | Increase to **22 µF**                                                                             |
| Alarm trips at startup but not after                                   | Add a small **10 kΩ resistor in series** with the tach line — slows the signal further on startup |

## Build tips

- **Insulate the cap leads.** Heatshrink each leg before tucking the cap inside the UPS housing. The leads are bare metal and will short to chassis or PCB if they wander.
- **Cap orientation matters.** Electrolytics are polarised. The negative side (white stripe) goes to GND. Wrong way round, the cap will work briefly then either go open-circuit or pop messily.
- **Keep the cap close to the JST connector**, not at the fan end. This puts the filter on the long-wire side where it does the most good.
- **Don't extend wires beyond what's needed**. Long unshielded runs near the UPS's switching electronics will pick up noise that defeats the filter.
- **Test on the bench first** if you can. Power the fan from a separate 12 V supply, scope the LRS line: should sit at <0.5 V steady when running, jump to >5 V when you stop the rotor.

## Tidy perfboard build

If you'd rather not have a cap with bare legs floating in heatshrink, mount the components on a small offcut of perfboard or stripboard (~15 × 20 mm is plenty) and Kapton-wrap the finished module. Mechanically more robust, easier to keep tidy, and easier to revisit if you want to retune.

### Splice approach (what most people end up doing)

The factory fan connector on the PCB is small and often hot-glued in place — don't wrestle with it. Instead:

1. Cut the **stock fan's** wire close to the fan end, leaving the connector attached to the UPS PCB. You now have three short wires (red/+12 V, black/GND, yellow-or-white/LRS) coming out of the UPS, ready to splice.
2. Take the **Noctua extension cable** that ships in the box and cut it at one end so you have four short wires (yellow/+12 V, black/GND, green/tach, blue/PWM) emerging.
3. The perfboard sits between these two cut ends.

This way the fan stays unmodified (you can return or repurpose it), the UPS PCB connector is left undisturbed, and the only thing you've sacrificed is the Noctua extension cable (~£5 to replace).

### Layout

```
   UPS side                              Fan side
   ────────                              ────────
   +12 V ───────────────────────────── +12 V  (Noctua yellow)

   GND ───────┬─────────────────────── GND    (Noctua black)
              │
             ─┴─  negative leg (white stripe)
              ─
             10 µF / 50 V
              +   positive leg
             ─┬─
              │
   LRS ───────┴─────────────────────── Tach   (Noctua green)

                                       (Noctua blue/PWM — cut & insulated)
```

Three pads in, three pads out, one component bridging the LRS row to the GND row. The cap polarity is the only thing to get right — negative leg (white stripe / shorter lead) to GND.

### Optional: speed reduction on the same board

If you want to add a series resistor on the +12 V line to drop the fan speed (instead of using the bundled `NA-RC7` L.N.A. cable), add it inline on the +12 V row:

| Resistor on +12 V | Approx fan voltage | Approx RPM (NF-A8) | Approx noise |
| ------------------ | ------------------ | ------------------ | ------------ |
| **0 Ω / wire link** | 12 V              | 2200               | 17.7 dB(A)   |
| **~33 Ω, 1 W**     | ~10 V             | ~1750              | ~13 dB(A) — equivalent to L.N.A. |
| **~56 Ω, 1 W**     | ~7 V              | ~1100              | ~7 dB(A) — equivalent to U.L.N.A. |

Use **1 W** parts (or 2 W to be safe) — at 0.18 A through the resistor the dissipation peaks around 0.5 W on the U.L.N.A. value, and a quarter-watt part will run hot and brown the board over time.

If you're going to fit the bundled L.N.A. cable on the +12 V line anyway, skip the resistor — just don't do both.

### Wrap and mount

1. Trim the perfboard so there are no exposed copper traces or component leads sticking proud — flush-cut everything.
2. Wrap **two layers** of Kapton tape (30 µm amber polyimide), overlapping by half. Heatshrink also works if you have a tube big enough; Kapton handles the warm UPS interior better.
3. Bring the three input and three output wires out of one short edge. Strain-relieve with a small zip-tie around the bundle just past the wrap.
4. Mount inside the UPS with 3M VHB foam tape or a dab of hot glue, away from heatsinks and bus capacitors. The empty space behind the front bezel is usually generous.

## Reference

The original write-up of this approach was for an Eaton 9PX 1500VA, which uses an identical Sanyo Denki LRS architecture. Florian Bernd captured the circuit simulation, scope traces, and reasoning at:

<https://blog.flobernd.de/2024/04/eaton-fan-mod/>

The B300R is the same circuit with different branding.

## Tested values

If you're documenting your own build, fork this repo and add your observed values. Useful data to capture:

- UPS load during test (% of rating)
- Ambient temperature
- Fan speed under load (`upsc` doesn't expose this on B300R, so you'd need a tach reader on the spare wire)
- UPS internal temperature reported by NUT
- Cap value used and observed alarm behaviour
- Time-to-alarm if it does still trip
