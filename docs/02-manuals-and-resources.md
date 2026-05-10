# 2. Manuals and Resources

## In this repo

The official **Borri B300R user manual** is archived in this repo at:

📄 **[`manual/B300R-User-Manual.pdf`](../manual/B300R-User-Manual.pdf)** — 48 pages

It covers all five model variants (010-E, 010-B, 015-B, 020-B, 030-B), with sections on safety, installation (tower and rack), EBM chaining, LCD operation, communication ports, EPO wiring, alarm codes, battery replacement, electrical specs, and rear panel diagrams. Also includes the Winpower software install procedure with the activation key.

See [`../manual/NOTICE.md`](../manual/NOTICE.md) for copyright notes.

## Honest note on Borri-hosted sources

**Borri does not host the B300R user manual on their site any more.** I checked their current download portal — `borri.it/download-pdf/` only lists in-production product brochures and software manuals. Their `/pdf/manuali-utente/` directory only has the Galileo/Leonardo software manuals. The B300R is genuinely orphaned on the vendor side.

What Borri *does* still host that's relevant:

| What                                         | Where                                                                            | Notes                                                               |
| -------------------------------------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| Borri Critical Power Catalogue (current, EN) | <https://www.borri.it/pdf/brochure/en/Catalogue_Borri_EN_OMG60326revD_01-25.pdf> | Current product line in one PDF                                     |
| Galileo 1-3 kVA datasheet                    | <https://www.borri.it/pdf/specs/Galileo_EN_Borri_01_17_OMG60106revB.pdf>         | The B300R's online double-conversion successor                      |
| Winpower software                            | <http://www.borri.co.uk/softwaredownload>                                        | The B300R-compatible UPS software (key: `S11C1-01220-0100-478DF2A`) |

## Other places the manual exists

If you've cloned this repo without the PDF (e.g. you forked and stripped it for copyright reasons), the manual is also reproduced at the following third-party document aggregators:

### No sign-up (captcha only)

- **all-guidesbox.com** — full Galileo RT/T 1-3 kVA manual (B300R's successor with similar structure): <https://all-guidesbox.com/manual/1471206/borri-galileo-rt-1-kva-operation-user-s-manual-56.html>
- **docplayer.org** — same content, German version: <https://docplayer.org/177537157-Borri-galileo-rt-t-usv-1-3kva-benutzerhandbuch.html>

### Sign-up required (avoid if possible)

- ManualsLib: B400R-010-B(C) at <https://www.manualslib.com/manual/922443/Borri-B400r-010-B-C.html> — the closest current product manual that's freely viewable in browser
- Manualzz: B300R 1000VA-3000VA listing at <https://manualzz.com/doc/2839163/borri-b400-1kva-datasheet> — Google sign-in required
- Scribd: "Rotation 1-3kVA User Manual BORRI V2" at <https://www.scribd.com/document/291085637> — sign-up required

### Aggregator HTML transcripts (free, browseable)

- vdocuments.mx, dokumen.tips, exguidess.com, guidessimo.com — text content searchable, formatting variable. Useful for grepping for specific alarm codes when you don't have the PDF in front of you.

## Searching like-for-like

When you need cross-references, search using these alternative names — the underlying document is often the same OEM manual:

```
"Borri B300R"
"Borri Rotation" 1-3kVA
"Borri Galileo RT" / "Galileo T"
"Voltronic" 1-3kVA "line interactive" rebadge
```

The OEM platform was also sold by other brands (Mecer/Mustek, ABB, various private-label) under different model numbers. Manuals for those usually cover identical menu structures.

## Useful references for the platform family

- **NUT hardware compatibility list (Borri entries)**: <https://github.com/networkupstools/nut/blob/master/data/driver.list.in>
- **NUT `blazer_usb` driver man page**: <https://networkupstools.org/docs/man/blazer_usb.html>
- **Megatec/Q1 protocol notes**: e.g. <https://networkupstools.org/protocols/megatec.html>
- **Voltronic Power**: the OEM platform vendor — <https://www.voltronicpower.com/> (their UPS platform shows up rebadged across dozens of brands)
