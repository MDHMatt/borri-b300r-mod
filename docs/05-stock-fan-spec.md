# 5. Stock Fan Specification

## Sanyo Denki San Cooler 80, model 9A0812S4D01

| Parameter       | Value                                    |
| --------------- | ---------------------------------------- |
| Form factor     | 80 × 80 × 25 mm                          |
| Voltage         | 12 VDC (operating range 6.0–13.8 V)      |
| Current         | 0.18 A                                   |
| Power           | 2.16 W                                   |
| Speed           | 3400 RPM                                 |
| Airflow         | 42.4 CFM (1.20 m³/min)                   |
| Static pressure | 4.8 mmH₂O                                |
| Noise           | 34 dB(A)                                 |
| Bearing         | Ball                                     |
| Wires           | 3 (Red +12 V, Black GND, Yellow LRS)     |
| Sensor type     | **Locked Rotor Sensor (open-collector)** |
| Approvals       | UL, CSA, TÜV                             |
| Origin          | Made in the Philippines                  |

In Sanyo Denki's part-number scheme, the trailing **`D01`** is the sensor-type code = *locked rotor sensor*. Pulse-tach variants of a fan family end in `01` (no leading `D`); no-sensor variants end in `02`. The single inner letter (`S`, `G`, etc.) is a *speed code*, not a sensor-type code:

- `9A0812**S**4D01` — speed code `S` (3400 RPM, 42.4 CFM), **LRS** (`D01`). This is what's fitted to the B300R.
- `9A0812**G**4D01` — speed code `G` (higher RPM, ~53 CFM), also **LRS** (`D01`).

There is no drop-in tach-pulse equivalent in the 9A0812 family — both common variants are LRS. This is why the cap mod (see [`../circuits/lrs-cap-mod.md`](../circuits/lrs-cap-mod.md)) is the standard fix for adapting a Noctua tach line to this UPS.

## Why this matters

This determines the cooling-vs-acoustics envelope you have to fit a replacement into:

- **Static pressure (4.8 mmH₂O)** is the most important spec for a UPS fan. UPS chassis are dense — heatsinks, baffles, perforated grilles. A fan with low static pressure simply won't push air through the obstructions, no matter how high the free-air CFM rating looks.
- **Airflow (42.4 CFM)** is the secondary number — useful if you're running the UPS at high load in a warm environment.
- **Noise (34 dB(A))** is the headroom you have for improvement. Anything above ~25 dB(A) at the listening position is intrusive in a quiet room.

## Why the replacement match needs care

Every quiet 80 mm fan on the market has lower static pressure than this one. Noctua's 80 mm range (NF-A8 PWM, NF-A8 FLX, NF-A8 ULN, NF-R8 redux-1800 PWM) sits around 1.3–2.4 mmH₂O — roughly **half** the stock fan's 4.8 mmH₂O. They'll move air, but not necessarily through the chassis effectively at high load.

Noctua does **not** make an industrialPPC variant in 80 mm — that line starts at 120 mm (`NF-F12`, `NF-A12x25`, `NF-A14`). The only 80 mm fan with static pressure that meets or exceeds stock is the **Arctic P8 Max** (5.3 mmH₂O, dual-ball bearing) — at the cost of higher noise.

See [`06-noctua-comparison.md`](06-noctua-comparison.md) for the full comparison.

## Sourcing replacements

The Sanyo Denki 9A0812S4D01 is still available from DigiKey, Mouser, Farnell, RS Components — typically £15–20 each. If you don't want to mod the UPS at all and just want fresh stock fans because yours have aged, this is the part to order.

It is also readily available second-hand from disassembled servers and appliances on eBay.
