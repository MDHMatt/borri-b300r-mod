# 7. LCD Display Strings & Alarm Codes

Direct from the user manual, sections 4.1 (table 4) and 8.1.

## Operational status strings

These appear on the LCD in normal black-on-blue text:

| String | Meaning                                                            |
| ------ | ------------------------------------------------------------------ |
| `STbY` | Standby — UPS plugged in, output disabled, batteries recharging    |
| `NORM` | Normal mode — output from filtered/AVR'd mains                     |
| `AVR`  | AVR (boost or buck) actively correcting under/over-voltage         |
| `bATT` | On battery — mains lost or out of tolerance, output from inverter  |
| `TEST` | Battery self-test in progress                                      |

## Warning / fault strings (red backlight)

| String     | Meaning                                                  | Audible                            |
| ---------- | -------------------------------------------------------- | ---------------------------------- |
| `IPVL`     | Input voltage too low (below operating range)            | —                                  |
| `IPVH`     | Input voltage too high                                   | —                                  |
| `IPFL`     | Input frequency too low                                  | —                                  |
| `IPFH`     | Input frequency too high                                 | —                                  |
| `OPVH`     | Output voltage too high (during battery operation)       | —                                  |
| `OPVL`     | Output voltage too low (during battery operation)        | —                                  |
| `OPST`     | Output short circuit                                     | Continuous                         |
| `OVLD`     | Output overloaded                                        | Beep every second                  |
| `bATH`     | Battery voltage too high                                 | —                                  |
| `bATL`     | Battery voltage too low (low-battery shutdown imminent)  | Beep every second                  |
| `bTWK`     | Batteries weak — needs charging or replacement           | Continuous                         |
| `OVTP`     | UPS internal temperature too high                        | Continuous                         |
| **`FNLK`** | **Fan locked / not operating**                           | **Continuous, cannot be silenced** |
| `****`     | Generic UPS fault                                        | Continuous                         |

## Audible alarm reference (manual table 8)

| Condition                    | Audible behaviour    |
| ---------------------------- | -------------------- |
| Backup mode (on battery)     | Beep every 4 seconds |
| Low battery (`bATL`)         | Beep every second    |
| Overload (`OVLD`)            | Beep every second    |
| Battery replacement (`bTWK`) | Beep every second    |
| UPS fault                    | Continuous           |

## Alarms that cannot be silenced

Per manual section 4.1, the buzzer-silence button (🔔) does **not** disable the alarm for:

- **Low Battery** (`bATL`)
- **Fan Failed** / **`FNLK`**
- **Fan Fault Time Out**
- **Overheat** (`OVTP`)

If you've muted the buzzer for any other event and a *new* event of any kind occurs, the alarm will sound again — the silence is per-event, not global.

## Mapping to NUT `ups.status`

Approximate mapping for monitoring/scripting:

| LCD string     | NUT `ups.status` flag                 |
| -------------- | ------------------------------------- |
| `NORM`, `AVR`  | `OL` (online)                         |
| `bATT`         | `OB` (on battery)                     |
| `bATL`         | `OB LB` (on battery, low)             |
| `OVLD`         | `OL OVER`                             |
| `bTWK`         | `OL RB` (replace battery)             |
| `OVTP`         | `OL TRIM` (often surfaces as `ALARM`) |
| `FNLK`, `****` | `OL ALARM`                            |
| `STbY`         | `OFF`                                 |

The `blazer_usb` driver handles most of these correctly; `FNLK` specifically may surface only as `ALARM` since the Q1 protocol's flag bits don't have a dedicated "fan failed" bit — check `ups.alarm` for the descriptive text.

## Configurable settings (manual table 5)

These appear on the LCD when in settings mode — see [`09-lcd-operation.md`](09-lcd-operation.md) for the button procedure.

| String | Setting                                                       | Values                                       |
| ------ | ------------------------------------------------------------- | -------------------------------------------- |
| `OPV`  | Output voltage                                                | `220` / `230` / `240`                        |
| `AVR`  | Input range mode                                              | `000`=Normal / `001`=Wide / `002`=Generator  |
| `EbM`  | External battery module count                                 | `0`–`9`                                      |
| `TEST` | Auto self-test                                                | `000`=Disable / `001`=Enable                 |
| `AR`   | Automatic restart                                             | `000`=Disable / `001`=Enable                 |
| `GF`   | Green Function (auto-shutdown on light load while on battery) | `000`=Disable / `001`=Enable                 |
| `bZ`   | Buzzer control                                                | `000`=Disable / `001`=Enable                 |
| `LS1`  | Load segment 1 (first set of C13s)                            | `000`=Off / `001`=On                         |
| `LS2`  | Load segment 2 (second set of C13s)                           | `000`=Off / `001`=On                         |
