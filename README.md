# borri-b300r-mod

Notes, official manual, and modifications for the **Borri B300R** Rotation-series UPS — 1000–3000 VA tower/rack convertible **line-interactive** UPS, EOL.

## TL;DR

- **What it is:** **Line-interactive** UPS with AVR (boost/buck), 1/1.5/2/3 kVA, RT/T form factor, end-of-life. Block diagram in the manual confirms: input filter → AVR → output, with separate charger/battery/DC-DC/inverter path that only feeds output during outages. Despite some reseller pages calling it "online double-conversion," it isn't.
- **Talking to it:** RS232 + USB on the back, optional SNMP slot card. Speaks Megatec/Voltronic Q1 protocol — works with NUT's `blazer_usb` or `blazer_ser` driver. Borri's own software is **Winpower** (not Power Guardian — that's the Galileo/Leonardo software).
- **Quiet fans:** Stock Sanyo Denki San Cooler 80 fans are 34 dB(A) screamers. Noctua iPPC-2000 is the right replacement, but the UPS uses a **locked-rotor sensor**, not a tachometer, so a Noctua trips the FNLK alarm 5–15 min in. Fix is a **10 µF cap** between the tach line and GND, ~50p.

## Repository layout

```
borri-b300r-mod/
├── README.md                       — this file
├── docs/
│   ├── 01-overview.md              — what the B300R is, variants, operating modes
│   ├── 02-manuals-and-resources.md — where to find the manual and related references
│   ├── 03-communications.md        — RS232, USB, Q1 protocol, NUT, EPO, SNMP
│   ├── 04-fan-replacement.md       — silent-fan mod and the FNLK problem
│   ├── 05-stock-fan-spec.md        — Sanyo Denki 9A0812S4D01 specifications
│   ├── 06-noctua-comparison.md     — Noctua replacement comparison
│   ├── 07-alarm-codes.md           — LCD strings, alarm codes, NUT mapping
│   ├── 08-rear-panel.md            — rear panel layout per model
│   └── 09-lcd-operation.md         — buttons, settings menu, configuration
├── nut/
│   └── ups.conf.example            — drop-in NUT config
├── circuits/
│   └── lrs-cap-mod.md              — LRS cap mod circuit and build instructions
└── manual/
    ├── B300R-User-Manual.pdf       — official user manual (48 pages)
    └── NOTICE.md                   — copyright notes for the PDF
```

## Quick links

- [Overview & model variants](docs/01-overview.md)
- [Manuals and resources](docs/02-manuals-and-resources.md)
- [Communications (NUT, RS232, USB, SNMP)](docs/03-communications.md)
- [Fan replacement guide](docs/04-fan-replacement.md)
- [Alarm codes reference](docs/07-alarm-codes.md)
- [LRS cap mod build](circuits/lrs-cap-mod.md)
- [Drop-in NUT config](nut/ups.conf.example)
- [Official user manual (PDF)](manual/B300R-User-Manual.pdf)

## License & Copyright

Reference notes only. The PDF in this repo (`manual/B300R-User-Manual.pdf`) is © **Borri Ltd**, Systems House, Eckington Business Park, Rotherside Road, Eckington, Sheffield, S21 4HL. It's archived here for personal reference. See [`manual/NOTICE.md`](manual/NOTICE.md).

Brand names and trademarks belong to their respective owners (Borri S.p.A., Sanyo Denki, Noctua, etc.). Manuals linked from third-party sources, not redistributed here.

If you're forking this repo, consider whether you have the right to redistribute the vendor PDF. Borri's official site at <https://www.borri.it/download-pdf/> no longer hosts the B300R manual, but the company still exists and still owns the copyright.

Mods at your own risk. UPSes contain capacitors that stay charged after disconnection. Don't open one with the battery and mains still attached.
