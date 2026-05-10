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

The "**S**" in `9A0812**S**4D01` is Sanyo Denki's part-number suffix specifically for the locked-rotor-sensor variant. The "G" suffix (`9A0812G4D01`) is the otherwise-identical model with a tachometer/RPM signal output instead of LRS — this is **not** what's fitted to the B300R.

## Why this matters

This determines the cooling-vs-acoustics envelope you have to fit a replacement into:

- **Static pressure (4.8 mmH₂O)** is the most important spec for a UPS fan. UPS chassis are dense — heatsinks, baffles, perforated grilles. A fan with low static pressure simply won't push air through the obstructions, no matter how high the free-air CFM rating looks.
- **Airflow (42.4 CFM)** is the secondary number — useful if you're running the UPS at high load in a warm environment.
- **Noise (34 dB(A))** is the headroom you have for improvement. Anything above ~25 dB(A) at the listening position is intrusive in a quiet room.

## Why the Noctua match needs care

Generic 80 mm Noctuas (NF-A8 PWM, NF-A8 FLX, NF-R8 redux) have static pressures around 2.0–2.4 mmH₂O — roughly **half** the stock fan's. They'll move air, but not necessarily through the chassis effectively at high load.

The **industrialPPC** versions are designed exactly for this kind of duty (industrial enclosures, high-restriction airflow, 24/7 operation, sealed bearing). The iPPC-2000 hits 2.61 mmH₂O and the iPPC-3000 hits 5.88 mmH₂O — i.e. the iPPC-3000 *exceeds* stock spec on every metric while still being significantly quieter.

See [`06-noctua-comparison.md`](06-noctua-comparison.md) for the full comparison.

## Sourcing replacements

The Sanyo Denki 9A0812S4D01 is still available from DigiKey, Mouser, Farnell, RS Components — typically £15–20 each. If you don't want to mod the UPS at all and just want fresh stock fans because yours have aged, this is the part to order.

It is also readily available second-hand from disassembled servers and appliances on eBay.
