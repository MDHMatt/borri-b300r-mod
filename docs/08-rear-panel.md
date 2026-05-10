# 8. Rear Panel Layout

Per model, from manual section 7.2.

## B300R-010-E (1 kVA, basic)

The "E" variant is stripped down — no SNMP slot, no EPO, no extension battery support. Connectors:

```
1. AC Output (4× IEC C13)
2. Modem/Network surge protection (RJ45 in/out)
3. AC Input (IEC C14)
4. RS232 / Dry-Contact (DB9)
5. USB Type B
6. Earth line stud
```

## B300R-010-B and B300R-015-B (1 kVA / 1.5 kVA, full-feature)

Full feature set; takes EBM, has SNMP slot and EPO:

```
1. AC Output (8× IEC C13, split into LS1 and LS2 — 4 outlets each)
2. Modem/Network surge protection (RJ45 in/out)
3. SNMP / Webpower card slot (intelligent slot)
4. AC Input (IEC C14)
5. RS232 / Dry-Contact (DB9)
6. USB Type B
7. EPO terminal
8. Earth line stud
```

The 8 output sockets are divided into two **load segments** (LS1 and LS2), each independently controllable from the LCD. Useful for separating "must stay on" loads (storage, primary servers) from "shed first" loads (monitors, secondary equipment) during prolonged battery operation.

## B300R-020-B (2 kVA)

Same connectors as the 010-B / 015-B but the chassis is deeper to fit the 6× 12 V battery string, and the rear panel adds an internal cooling fan exhaust:

```
1. AC Output (8× IEC C13, LS1 / LS2)
2. Modem/Network surge protection
3. SNMP slot
4. Fan (exhaust)
5. AC Input
6. RS232 / Dry-Contact
7. USB
8. EPO
9. Earth line stud
```

## B300R-030-B (3 kVA)

Rear layout identical in connector count to the 020-B but with a higher-current AC input (still IEC C14, but rated to handle the full 13 A draw):

```
1. AC Output (8× IEC C13, LS1 / LS2)
2. Modem/Network surge protection
3. SNMP slot
4. Fan (exhaust)
5. AC Input (IEC C14, 13A — use the supplied heavy-gauge cable)
6. RS232 / Dry-Contact
7. USB
8. EPO
9. Earth line stud
```

## EBM (External Battery Module) rear panels

Two EBM model families:

- **B3RBB36V** / **B3RBB36V6-12-7** — 36 V EBM, for 010-B and 015-B
- **B3RBB72V** / **B3RBB72V12-12-9** — 72 V EBM, for 020-B and 030-B

EBM rear is just an earth-line stud and battery terminals (the inter-EBM connections live on the front, behind the bezel).

## Dimensions and weights

From manual table 10:

| Model       | Net weight | WxHxD (mm)       |
| ----------- | ---------- | ---------------- |
| B300R-010-E | 13.0 kg    | 438 × 86.5 × 436 |
| B300R-010-B | 17.8 kg    | 438 × 86.5 × 436 |
| B300R-015-B | 17.8 kg    | 438 × 86.5 × 436 |
| B300R-020-B | 27.8 kg    | 438 × 86.5 × 608 |
| B300R-030-B | 27.8 kg    | 438 × 86.5 × 608 |
| 36 V EBM    | 20.5 kg    | 438 × 86.5 × 436 |
| 72 V EBM    | 33.3 kg    | 438 × 86.5 × 608 |

All units are **2U** in rack form. The 2/3 kVA models are deeper than the 1/1.5 kVA models — measure your rack before ordering.

## Operating environment

Per manual table 9:

- **Operating temperature**: 20–25 °C (note: this is *recommended* for battery longevity; the safety section allows 0–40 °C)
- **Humidity**: 20–80% RH non-condensing
- **Altitude**: <1500 m
- **Storage**: −15 to 45 °C
