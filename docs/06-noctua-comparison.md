# 6. 80 mm Replacement Fan Comparison

The B300R takes **80 × 80 × 25 mm** fans. **Noctua does not make an industrialPPC fan in 80 mm** — the iPPC range starts at 120 mm (`NF-F12`, `NF-A12x25`) and goes up to 140 mm (`NF-A14`). Don't go looking for an `NF-A8 industrialPPC` — it doesn't exist.

That leaves a different trade-off than the iPPC line offers: every quiet 80 mm fan on the market has **roughly half the static pressure** of the stock Sanyo Denki 9A0812S4D01 (4.8 mmH₂O). You're trading airflow capacity for silence. This is fine for typical homelab UPS load (≤30% of rated capacity); it is *not* fine if you run the UPS at >50% sustained load in a warm environment.

The cap mod from [`../circuits/lrs-cap-mod.md`](../circuits/lrs-cap-mod.md) applies to all of the candidates below — it's a function of the UPS's locked-rotor sensor expectation, not the fan model.

## How the UPS controls fan speed

Important context for choosing a connector type:

- The B300R does **not** drive a PWM signal on the fan header. There are only three pins (12 V, GND, sensor).
- The UPS varies the **+12 V rail voltage** with internal temperature — i.e. it acts like a 3-pin voltage-controlled fan controller, not a PWM controller.
- A **4-pin PWM fan** plugged in here gets full DC voltage and free-runs at whatever speed that voltage allows. The PWM input is unused.
- A **3-pin fan with a Low-Noise Adaptor** (LNA, an inline resistor cable) gets a fixed voltage drop, capping its top speed. This is actually the most useful arrangement for a UPS because it lets you choose a hard maximum.

For desk-side silence, **3-pin Noctuas with the bundled LNAs are the strongest pick**, not the 4-pin PWM versions.

## Verified candidates (all 80 × 25 mm, 12 V)

| Fan                                         | Connector | RPM   | Airflow (CFM) | Static pressure | Noise        | Bearing | Notes                                                                              |
| ------------------------------------------- | --------- | ----- | ------------- | --------------- | ------------ | ------- | ---------------------------------------------------------------------------------- |
| **Stock**: Sanyo Denki 9A0812S4D01          | 3-pin LRS | 3400  | 42.4          | 4.8 mmH₂O       | **34.0 dB**  | Ball    | The reference. The reason you're reading this.                                     |
| **Noctua NF-A8 FLX + U.L.N.A.**             | 3-pin     | 1200  | 17.0          | ~0.79 mmH₂O     | ~7 dB        | SSO2    | **Quietest practical option.** Inaudible at desk distance.                         |
| **Noctua NF-A8 FLX + L.N.A.**               | 3-pin     | 1650  | 24.4          | ~1.41 mmH₂O     | ~13 dB       | SSO2    | **Recommended default for desk use.** Still essentially silent, more thermal headroom. |
| Noctua NF-A8 FLX (no adapter)               | 3-pin     | 2000  | 29.7          | 2.07 mmH₂O      | 16.1 dB      | SSO2    | Both LNA and ULNA are in the box — you choose.                                     |
| Noctua NF-A8 ULN                            | 3-pin     | 1400  | 22.5          | 1.34 mmH₂O      | 11.9 dB      | SSO2    | Built specifically for ultra-quiet duty. Slightly less flexible than FLX.          |
| Noctua NF-A8 PWM                            | 4-pin PWM | 2200  | 32.7          | 2.37 mmH₂O      | 17.7 dB      | SSO2    | Most-stocked Noctua 80 mm. Includes one LNA in the box.                            |
| Noctua NF-R8 redux-1800 PWM                 | 4-pin PWM | 1800  | 31.4          | 1.83 mmH₂O      | 17.1 dB      | SSO     | Cheapest Noctua 80 mm.                                                             |
| be quiet! Pure Wings 2 80 mm                | 3-pin     | 1900  | 26.3          | 1.85 mmH₂O      | 18.2 dB      | Rifle   | Cheapest "premium-quiet" non-Noctua. ~£8.                                          |
| Arctic P8 PWM PST CO                        | 4-pin PWM | 3000  | 23.4          | 1.90 mmH₂O      | ~22 dB       | Dual ball | Continuous-operation bearing — built for 24/7 — but louder.                       |
| **Arctic P8 Max**                           | 4-pin PWM | 5000  | 40.0          | **5.3 mmH₂O**   | ~26 dB at full | Dual ball | **Only 80 mm fan that exceeds stock static pressure.** Only sane choice if SP matters. Loud at full speed. 500,000 h MTTF. |

Numbers are manufacturer figures. The "+ L.N.A. / U.L.N.A." rows are Noctua's published values for the FLX with its bundled adapter cables; airflow and SP are derived from voltage scaling so they're approximate.

## Recommendation by use case

### Desk-side, want it inaudible (your case)

**Noctua NF-A8 FLX with the U.L.N.A. fitted** (`NF-A8 FLX` part number — both adapters are in the box). At 1200 RPM it is genuinely inaudible past 1 m. After install, monitor `ups.temperature` via NUT for a week with realistic load — if it stays below ~40 °C internal, you're done. If it climbs past 45 °C, swap to the L.N.A. (1650 RPM) instead; that's still ~13 dB and well under any rack-server fan.

If you'd rather not think about adapters, the **Noctua NF-A8 ULN** is the same idea pre-baked: 1400 RPM, 11.9 dB.

### Inaudible isn't possible because UPS load is high

If you've measured high internal temperatures or you regularly draw >50% of rated load, the only fan that will both fit and match stock cooling is the **Arctic P8 Max** (5.3 mmH₂O — actually exceeds stock). It is louder than the Noctuas at full speed but quieter than the stock Sanyo Denki it replaces, and the dual-ball bearing is rated for 500,000 h continuous duty, which is what a UPS actually does. The cap mod still applies; it doesn't drive its own LRS.

### Don't want to mod at all

Buy a fresh **Sanyo Denki 9A0812S4D01** from DigiKey/Mouser/Farnell. It's still in production. ~£15–20. Drop-in, no cap mod needed, still 34 dB. (Or just leave the existing fans in unless they've actually failed.)

## Trade-off summary

| You optimise for | Pick | Trade-off |
|---|---|---|
| Maximum quiet | NF-A8 FLX + ULNA, or NF-A8 ULN | Lowest airflow & SP — only safe at low UPS load |
| Quiet + thermal margin | NF-A8 FLX + LNA | Mild compromise both ways |
| Default Noctua | NF-A8 PWM | Slightly more airflow than FLX-no-adapter; PWM connector is wasted on this UPS |
| Static pressure ≈ stock | Arctic P8 Max | Louder, but the only honest match for the airflow path |
| Drop-in identical | Sanyo Denki 9A0812S4D01 | No quieter than what you have now |

## Why not fan X?

- **Phanteks T30 / NF-A12x25** etc.: 120 mm. Won't fit the 80 mm cut-out without chassis modification.
- **Generic "silent" Amazon-brand fans**: bearings rated 30–50 k h. A UPS runs continuously for years; sleeve-bearing fans die early.
- **Sanyo Denki 9GA0812 series**: louder than stock (41–45 dB), built for higher-airflow duty, not what you want.
- **Sanyo Denki 9A0812G4D01**: same physical fan as stock, just spec'd for higher airflow (~53 CFM). Even louder. Also LRS — the `D01` suffix is the sensor-type code (= LRS); the letter (`S` vs `G`) is a *speed* code, not a sensor code. There is no drop-in tach-pulse equivalent in the 9A0812 family.

## Buy links

- Noctua NF-A8 FLX: <https://noctua.at/en/nf-a8-flx>
- Noctua NF-A8 ULN: <https://noctua.at/en/nf-a8-uln>
- Noctua NF-A8 PWM: <https://noctua.at/en/nf-a8-pwm>
- Noctua NF-R8 redux-1800 PWM: <https://noctua.at/en/products/nf-r8-redux-1800-pwm>
- be quiet! Pure Wings 2 80 mm: <https://www.bequiet.com/en/casefans/pure-wings-2/783>
- Arctic P8 PWM PST CO: <https://www.arctic.de/us/P8-PWM-PST-CO/ACFAN00151A>
- Arctic P8 Max: <https://www.arctic.de/us/P8-Max/ACFAN00286A>
- Sanyo Denki 9A0812S4D01 (stock): DigiKey, Mouser, Farnell — search the part number directly.

All Noctua and Arctic fans listed are also stocked by Amazon, Scan, Overclockers UK, Quiet PC. Noctua list `https://noctua.at/en/dealers` for current local sources.
