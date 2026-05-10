# 3. Communications

## Ports on the back

| Port                                              | Notes                                                                                             |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **RS232 DB9** (standard, all models)              | Combined RS232 + dry contact. Megatec/Q1 protocol. Pinout below.                                  |
| **USB Type B** (standard, all models)             | Same Q1 protocol. Common VID/PID is `0001:0000` ("MEC0003" identifier).                           |
| **Intelligent slot** (optional, all -B models)    | Takes Borri's "Webpower" SNMP/HTTP card. Not available on the -010-E.                             |
| **EPO** (Emergency Power Off) (-B models)         | Dry-contact remote shutdown. 24 Vdc / 20 mA latching switch, 20 ms minimum signal. Not on -010-E. |
| **Modem/Network surge** (RJ45 in/out, all models) | Pass-through surge suppression for telephone or Ethernet — *not* a network management port.       |

## DB9 pinout (RS232 + dry contact, combined)

From the manual section 5.1, table 6:

| Pin | Description | I/O    | Function                         |
| --- | ----------- | ------ | -------------------------------- |
| 1   | BATLOW      | Output | Battery low signal (dry contact) |
| 2   | RXD         | Input  | Serial receive                   |
| 3   | TXD         | Output | Serial transmit                  |
| 4   | DTR         | Input  | (N/A — reserved)                 |
| 5   | Common      | —      | Common, tied to chassis          |
| 6   | DTR         | Input  | (N/A — reserved)                 |
| 7   | RING        | Output | Ring (dry contact)               |
| 8   | LNFAIL1     | Output | Line fail signal (dry contact)   |
| 9   | —           | —      | Unused                           |

Pins 1, 7 and 8 are usable as dry-contact outputs without any UPS power management software — useful if you want to drive a relay or microcontroller directly.

Serial interface runs at **2400 8N1** with the Megatec/Q1 protocol.

## Protocol

Megatec/Voltronic **Q1** family. Send ASCII commands terminated with `\r`:

| Command      | Returns                                                  |
| ------------ | -------------------------------------------------------- |
| `Q1\r`       | Status string (Vin, Vout, load, Vbatt, Tin, freq, flags) |
| `F\r`        | UPS rated values (V, A, Vbatt, Hz)                       |
| `I\r`        | UPS identification (manufacturer, model, version)        |
| `T\r`        | 10-second self-test                                      |
| `TL\r`       | Test until battery low                                   |
| `CT\r`       | Cancel test                                              |
| `Q\r`        | Beeper toggle                                            |
| `S<n>R<m>\r` | Shutdown in `<n>` minutes, restore after `<m>` minutes   |

Quick sanity check from the command line:

```bash
sudo picocom -b 2400 -d 8 -p n -y n /dev/ttyUSB0
# Type: Q1<Enter> — should return something like:
# (220.0 220.0 230.0 005 50.0 27.0 25.0 00001000
```

NUT parses the status string for you.

## Network UPS Tools (NUT)

The B300R isn't explicitly listed in NUT's hardware compatibility list, but every neighbouring Borri product (B400, B400R, B500, B500R, B500EVO) is, all using the **`blazer_usb`** driver. The B300R is the same OEM platform — it works.

See [`../nut/ups.conf.example`](../nut/ups.conf.example) for a drop-in config with per-model battery voltages.

If `blazer_usb` won't enumerate cleanly, try in order:

1. `langid_fix = 0x0409` (most common fix)
2. `subdriver = phoenix`, then `cypress`, then `ippon`, then `krauler` (with `vendorid` + `productid` set)
3. `nutdrv_qx` instead of `blazer_usb` — newer/more flexible
4. Fall back to RS232 with `blazer_ser`

### Battery voltage settings by model

NUT estimates `battery.charge` from `battery.voltage`. Per the manual's specification tables (section 7.1), set the high/low thresholds per your specific UPS:

| Model       | Battery pack | Nominal | `voltage.high` (float) | `voltage.low` (cutoff) |
| ----------- | ------------ | ------- | ---------------------- | ---------------------- |
| B300R-010-E | 2× 12 V/9 Ah | 24 V    | 27.6 V                 | 22.0 V                 |
| B300R-010-B | 3× 12 V/7 Ah | 36 V    | 41.4 V                 | 33.0 V                 |
| B300R-015-B | 3× 12 V/9 Ah | 36 V    | 41.4 V                 | 33.0 V                 |
| B300R-020-B | 6× 12 V/7 Ah | 72 V    | 82.8 V                 | 66.0 V                 |
| B300R-030-B | 6× 12 V/9 Ah | 72 V    | 82.8 V                 | 66.0 V                 |

Derived from 12 V × N strings at 2.30 V/cell float and 1.83 V/cell cutoff. Tune from observed values once it's online. Note that **2 kVA and 3 kVA share the same 72 V battery configuration** — only cell capacity differs.

## EPO wiring (-B models only)

From manual section 5.2:

- 4–0.32 mm² (12–22 AWG) terminal wire size; 0.82 mm² (18 AWG) suggested
- 24 Vdc / 20 mA minimum, latching-type switch, dedicated circuit
- Signal must remain active ≥20 ms
- Cabling must be isolated from mains-supply interference sources
- Leave the EPO connector installed in the EPO port even when not used (otherwise the UPS won't start)

## SNMP / Webpower card (optional)

The intelligent slot on -B models takes Borri's "Webpower" card — a Megatec/NetAgent-platform SNMP module. If yours has one fitted:

- Comes up on a configurable IP, default DHCP or 192.168.1.1
- Default credentials usually `admin/admin`
- Serves SNMP v1/v2c on UDP/161 with the Megatec/RFC1628 UPS-MIB
- Has an HTTP web UI for configuration

NUT supports it via the `snmp-ups` driver:

```ini
[borri-snmp]
    driver = snmp-ups
    port = 192.168.1.50
    community = public
    snmp_version = v1
    mibs = mge        # try this first; fall back to "ietf" or "rfc1628"
```

Manuals call this card "Webpower" — this is generic and can be supplied by Borri or any Megatec-compatible NetAgent. Borri Ltd UK was the contact for ordering at the time of the manual.

## Borri's own software: Winpower

The B300R ships compatible with **Winpower** (rebadged ViewPower from the Voltronic platform) — *not* Borri Power Guardian, which is for the Galileo/Leonardo line. From manual section 9:

- Download URL: <http://www.borri.co.uk/softwaredownload>
- Activation key: `S11C1-01220-0100-478DF2A`
- Available for Windows, Linux, macOS
- Provides graphical UPS status monitor, automated shutdown of multiple machines on the same LAN, scheduled tests, event logging

Skip unless you specifically need it — NUT does the same job, integrates with everything else in your stack, and isn't going to be silently abandoned.
