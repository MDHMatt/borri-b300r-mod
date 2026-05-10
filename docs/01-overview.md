# 1. Overview

## What it is

The **Borri B300R** is a single-phase, **line-interactive** UPS with AVR (boost/buck Automatic Voltage Regulation), in a 2U rack-or-tower convertible chassis. Available in 1, 1.5, 2, and 3 kVA ratings across five sub-variants. Sold by Borri Ltd (UK) and Borri S.p.A. (Italy) through the early-to-mid 2010s. Long discontinued.

The "R" suffix means **R**otation — Borri's term for the rack/tower convertible mechanical design where the LCD panel itself rotates so it reads correctly in either orientation.

The product manual on its cover page describes it explicitly as "**Line Interactive Uninterruptible Power Supply System**", and the internal block diagram (manual page 4) confirms the topology:

```
                 ┌─────────────┐    ┌──────┐
   AC mains ────►│ INPUT FILTER├───►│ AVR  ├───────┬──► OUTPUT 1
                 └─────────────┘    └──────┘       │
                       │                            └──► OUTPUT 2
                       ▼
                 ┌─────────┐    ┌─────────┐    ┌─────────┐
                 │ CHARGER ├───►│ BATTERY ├───►│  DC/DC  │
                 └─────────┘    └─────────┘    └────┬────┘
                                                     │
                                                ┌────▼────┐
                                                │  DC/AC  │── (kicks in on outage)
                                                └─────────┘
```

In normal operation, the output comes from the input via a filter and AVR — it is not double-conversion. The DC/AC inverter path only feeds output when mains is absent or out of tolerance. This makes the B300R cheaper, more efficient, and slightly less protective than a true online UPS — it cannot continuously regenerate the output waveform, but it does provide:

- **Boost/buck AVR**: corrects under-voltage and over-voltage without going to battery
- **Pure sinewave output** in battery mode
- **Generator-friendly transfer point** of 40–70 Hz (40–80 Hz on the -010-E variant)
- **Wide voltage tolerance modes** for dirty mains

Some UK reseller pages mislabel it as "online double-conversion." They're wrong.

## Model variants

There are five distinct part numbers, with two electrical generations:

| Model           | VA / W      | Input range           | Battery                 | Recharge   | EBM support |
| --------------- | ----------- | --------------------- | ----------------------- | ---------- | ----------- |
| **B300R-010-E** | 1000 / 900  | 0–300 VAC (very wide) | 2× 12 V/9 Ah = **24 V** | 8 h to 90% | No          |
| **B300R-010-B** | 1000 / 900  | 161–276 VAC           | 3× 12 V/7 Ah = **36 V** | 3 h to 90% | Yes         |
| **B300R-015-B** | 1500 / 1350 | 161–276 VAC           | 3× 12 V/9 Ah = **36 V** | 4 h to 90% | Yes         |
| **B300R-020-B** | 2000 / 1800 | 161–276 VAC           | 6× 12 V/7 Ah = **72 V** | 3 h to 90% | Yes         |
| **B300R-030-B** | 3000 / 2700 | 161–276 VAC           | 6× 12 V/9 Ah = **72 V** | 4 h to 90% | Yes         |

The "**E**" variant of the 1 kVA is the long-input-range version (designed for very dirty mains or as a basic stabiliser); it has fewer features (no SNMP, no EPO, no extension battery support). The "**B**" variants are the standard line.

Note that **2 kVA and 3 kVA both share the same 72 V battery string** — only the cell capacity differs.

## Operating modes

From the manual (section 4.2):

| Mode                       | Behaviour                                                                                |
| -------------------------- | ---------------------------------------------------------------------------------------- |
| **Normal range** (default) | Accepts AC input within ±20% of nominal                                                  |
| **Wide range**             | Accepts AC input within −30% / +20%                                                      |
| **Generator mode**         | Transfer-to-battery threshold opens up to 40–70 Hz frequency window (40–80 Hz on -010-E) |

These are user-selectable via the LCD `AVR` setting:

- `[000]` = Normal range mode
- `[001]` = Wide range mode
- `[002]` = Generator mode

## Lineage

The B300R was Borri's mid-2010s 1–3 kVA line-interactive product. It's part of the OEM Voltronic-platform family — same firmware, same Megatec/Q1 protocol, same chassis layout, same NUT compatibility, same Sanyo Denki LRS-fan story as Eaton 9PX and CyberPower OL-series of the same era.

Successor in Borri's catalogue:

```
B300R (line-interactive)  →  Galileo RT/T 1-3 kVA (online double-conversion, 2015)
                          →  Galileo Plus RT (current)
```

Note that Borri **moved from line-interactive to true online** with the Galileo RT — different topology, similar form factor and very similar firmware/menu structure.

Sister products built on the same firmware/protocol family — useful when you can't find B300R-specific docs:

- **B400 / B400R** — online double-conversion 1–3 kVA, very similar menu and alarm-code structure (closest current product family member with a freely viewable online manual)
- **B500 / B500-R** — 6–10 kVA, same protocol family
- **B500EVO** — 10–20 kVA, similar firmware
- **Galileo RT/T 1-3 kVA** — direct B300R successor in form factor

## Why it's a homelab gem (and a problem)

Pros: cheap on the second-hand market, line-interactive efficiency (>96% in normal mode means low parasitic draw), 40–70 Hz transfer point makes it generator-tolerant, hot-swappable batteries, scalable runtime via EBMs.

Cons: **loud as hell**. The stock Sanyo Denki fans are 34 dB(A) at full whack and the firmware can ramp them to 100% even at low loads. The fan controller monitors a locked-rotor sensor not a tach signal — this is the source of the Noctua-swap-trips-FNLK problem. See [`04-fan-replacement.md`](04-fan-replacement.md).
