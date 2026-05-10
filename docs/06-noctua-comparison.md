# 6. Noctua Replacement Comparison

All 80 × 25 mm, 12 V. The cap mod from [`../circuits/lrs-cap-mod.md`](../circuits/lrs-cap-mod.md) applies to all of them — it's a function of the LRS-vs-tach mismatch in the UPS, not the fan model.

| Fan                                     | RPM  | CFM  | Static Pressure | Noise | Bearing | MTTF @ 40 °C | Notes                                                                                                               |
| --------------------------------------- | ---- | ---- | --------------- | ----- | ------- | ------------ | ------------------------------------------------------------------------------------------------------------------- |
| **Stock**: Sanyo Denki 9A0812S4D01      | 3400 | 42.4 | 4.8 mmH₂O       | 34.0  | Ball    | ~50,000 h    | The reference.                                                                                                      |
| Noctua NF-A8 PWM                        | 2200 | 32.7 | 2.37 mmH₂O      | 17.7  | SSO2    | 150,000 h    | Cheapest viable option. **Static pressure is half stock — only OK for light loads.**                                |
| Noctua NF-A8 FLX                        | 2000 | 29.7 | 2.07 mmH₂O      | 17.1  | SSO2    | 150,000 h    | 3-pin, slightly quieter than PWM. Same SP issue as above.                                                           |
| Noctua NF-R8 redux-1800 PWM             | 1800 | 31.4 | 1.83 mmH₂O      | 17.1  | SSO     | 150,000 h    | Cheapest in the range. SP too low for serious UPS duty.                                                             |
| **Noctua NF-A8 industrialPPC-2000 PWM** | 2000 | 32.5 | **2.61 mmH₂O**  | 19.4  | SSO2    | 150,000 h    | **Recommended.** Built for this kind of duty, IP52, 6-yr warranty.                                                  |
| Noctua NF-A8 industrialPPC-3000 PWM     | 3000 | 50.2 | **5.88 mmH₂O**  | 28.4  | SSO2    | 150,000 h    | Exceeds stock spec on all metrics. ~6 dB quieter than stock at full speed. Best for heavy load / warm environments. |

## My pick

**Noctua NF-A8 industrialPPC-2000 PWM** for a typical homelab B300R running ≤30% of rated load in a normal-temperature room. Quiet enough you'll forget the UPS is there, properly suited to the application, and outlives the UPS itself.

Step up to the **iPPC-3000 PWM** if any of these are true:

- Load runs above 50% sustained
- Ambient is warm (>25 °C average)
- The UPS lives in a closed cabinet
- You want margin

## Why not regular NF-A8?

Static pressure. A UPS isn't a CPU heatsink, it's a high-restriction enclosure with grilles, baffles, dense PCBs, and battery packs in the airflow path. A fan with 2.0–2.4 mmH₂O static pressure will move plenty of air in free space, but in the actual UPS chassis its effective airflow drops sharply. The iPPC-2000 has slightly higher SP than the regular NF-A8 (2.61 vs 2.37) and that small difference matters under back-pressure.

## Why not non-Noctua?

Plenty of other quality options exist (Arctic, Be Quiet!, Phanteks). The reason to default to Noctua specifically:

- The iPPC line's specs are honest and the parts behave to spec for years
- Noctua publishes a tach-locked-rotor whitepaper that confirms behaviour when stalled — important for the cap mod, since the stall-detection only works if the fan holds tach high on stall
- They're easy to source in the UK (Scan, OcUK, Amazon, Quiet PC)

## Voltage warning

If your UPS's fan rail is **24 V** rather than 12 V (probe before installing), don't use a standard Noctua. Either:

- Use the **NF-A8 industrialPPC-2000 24V PWM** (specifically the 24 V variant)
- Drop a **LM7812** voltage regulator inline to step 24 V → 12 V

The B300R 80 mm fans are typically 12 V — but check before committing. Older or higher-rating units in the same family sometimes use 24 V.
