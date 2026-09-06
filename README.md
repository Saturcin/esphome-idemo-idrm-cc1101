# ESPHome Idemo IDRM Gateway (ESP32 + CC1101)

[English](README.md) | [Español](README.es.md)

Community project to control **Idemo IDRM 433.92 MHz roller shutter motors** from **Home Assistant** using **ESPHome, an ESP32 and a CC1101 radio module**.

> **Status:** experimental / community-tested.  
> **Tested setup:** Idemo BLU45 IDRM tubular motors, 6-channel IDRM remote, ESP32 and CC1101.  
> This project is **not affiliated with or endorsed by Idemo Motors, Home Assistant or ESPHome**.

## What this project does

The gateway emulates an IDRM RF transmitter and exposes six Home Assistant `cover` entities.

Current features:

- 6 independently controlled shutter channels.
- Open, close, stop and percentage positioning.
- ESPHome + native Home Assistant API.
- CC1101 at 433.92 MHz using ASK/OOK.
- Editable opening/closing travel times from Home Assistant.
- Editable **direction-dependent calibration curves** from Home Assistant.
- Direct mathematical positioning without first travelling to an end stop.
- Special channel-6 STOP/RELEASE behaviour found during RF reverse engineering.
- Built-in IDRM RF sniffer with human-readable frame logging for discovering the original remote ID.

## Hardware

- ESP32 development board.
- CC1101 433 MHz module.
- 433 MHz antenna suitable for the CC1101/module.
- Idemo IDRM motor(s) already installed and safely wired.

### Wiring

| CC1101 | ESP32 |
|---|---|
| VCC | 3V3 |
| GND | GND |
| SCK | GPIO18 |
| MOSI | GPIO23 |
| MISO | GPIO19 |
| CSN / CS | GPIO5 |
| GDO0 | GPIO32 |
| GDO2 | GPIO33 (RX/sniffer) |

**Never power the CC1101 from 5 V.**

See [docs/WIRING.md](docs/WIRING.md).

## Software requirements

- Home Assistant.
- ESPHome.
- An ESP32 supported by ESPHome.
- Current ESPHome CC1101, Remote Transmitter and Remote Receiver components.

ESPHome includes native CC1101 support and integration with the Remote Transmitter/Receiver components:

- https://esphome.io/components/cc1101/
- https://esphome.io/components/remote_transmitter/
- https://esphome.io/components/remote_receiver/
- https://esphome.io/components/cover/template/

## Installation

1. Copy `esphome/idemo-idrm-gateway.yaml` to your ESPHome configuration directory.
2. Create or update your ESPHome `secrets.yaml`:
   ```yaml
   wifi_ssid: "YOUR_WIFI_SSID"
   wifi_password: "YOUR_WIFI_PASSWORD"
   ```
3. Wire `CC1101 GDO2 -> ESP32 GPIO33` if you want to use the built-in sniffer.
4. Validate and flash the YAML.
5. Open ESPHome logs and press UP/STOP/DOWN on the legitimate original remote.
6. Look for `IDRM SNIFFER` lines. The **first four bytes** after `FRAME=` are the transmitter ID.
7. Put those four bytes into `idrm_id_1` ... `idrm_id_4`, validate and flash again.
8. Add the ESPHome device to Home Assistant.
9. Fully open or fully close each shutter once to establish a known physical reference.
10. Fine-tune travel times and calibration curves from Home Assistant.

See [docs/INSTALLATION.md](docs/INSTALLATION.md) and [docs/CALIBRATION.md](docs/CALIBRATION.md).

## Important: transmitter ID

The RF transmitter identity is now exposed at the top of the YAML:

```yaml
substitutions:
  idrm_id_1: "0x46"
  idrm_id_2: "0x84"
  idrm_id_3: "0x5D"
  idrm_id_4: "0x9C"
```

`46 84 5D 9C` is the transmitter ID captured and used during development. It is **not a universal IDRM constant**.

For now, the recommended community workflow is to obtain the four ID bytes from a **legitimate IDRM remote already paired to the motor** and enter them in these substitutions.

We experimentally tested whether an arbitrary new ID could be paired as a new transmitter. That test failed, even after adjusting the final check byte. Therefore this project currently **does not claim to generate new valid IDRM transmitter identities or implement the complete rolling-code/pairing mechanism**.

> **Checksum caveat:** the command/check-byte logic in this repository has been fully validated only with the development ID `46 84 5D 9C`. Captures from additional legitimate transmitter IDs are needed to confirm how the check byte generalises.

See [docs/PAIRING_AND_ID.md](docs/PAIRING_AND_ID.md).

### Built-in sniffer log

With `GDO2` connected to `GPIO33`, the same YAML listens for the raw IDRM pulse shape and prints recognised 64-bit frames in a compact form:

```text
IDRM SNIFFER | FRAME=46 84 5D 9C 02 00 16 95 | ID=46 84 5D 9C | CH=02 00 | CMD=16 (UP) | CHECK=95
```

For configuration, note the four bytes after `ID=`. Also save complete UP/STOP/DOWN frame lines: captures from additional legitimate remotes are valuable because the check-byte/rolling-code behaviour for other transmitter identities is not yet fully understood.

## Default channels

| HA channel | IDRM channel bytes |
|---:|---|
| 1 | `02 00` |
| 2 | `04 00` |
| 3 | `08 00` |
| 4 | `10 00` |
| 5 | `20 00` |
| 6 | `40 00` |

Channel 6 has a confirmed special STOP/RELEASE variant documented in [docs/PROTOCOL.md](docs/PROTOCOL.md).

## Default travel times

| Channel | Up | Down |
|---:|---:|---:|
| 1 | 17 s | 17 s |
| 2 | 19.5 s | 19.5 s |
| 3 | 16 s | 16 s |
| 4 | 25 s | 25 s |
| 5 | 14 s | 14 s |
| 6 | 26 s | 26 s |

These are **installation-specific defaults** and should normally be adjusted.

## Default calibration curves

The current project uses independent curves for movement in each direction.

### Opening / raising

```text
25:25|50:70|70:90|80:100
```

### Closing / lowering

```text
25:3|50:7|75:45|85:60|90:75
```

Format:

```text
conceptual:physical|conceptual:physical|...
```

The values can be edited directly from Home Assistant. See [docs/CALIBRATION.md](docs/CALIBRATION.md).

## Why separate up/down curves?

These roller shutters do not behave like a perfectly linear position sensor. Slat stacking, unrolling and mechanical geometry make the visible opening differ depending on movement direction.

The gateway therefore converts between:

- **Physical position**: what the Home Assistant user wants to see.
- **Conceptual/time position**: the internal percentage used to determine motor run time.

The project performs piecewise-linear interpolation and inverse interpolation using a different curve for each direction.

## Known limitations

This is **open-loop control**: the motor does not send its actual position back to Home Assistant.

Therefore:

- Position is estimated from timing and calibration.
- Using the original physical RF remote can make Home Assistant's stored position drift.
- A full open or full close operation is the best way to re-synchronise.
- Motor speed, load, shutter geometry and installation differences can require different curves.
- RF behaviour has been reverse engineered experimentally and may differ between IDRM products or firmware generations.
- The official BLU45 family is an IDRM 433.92 MHz product; do not interpret this repository as a complete description of the proprietary IDRM protocol.

More details: [docs/LIMITATIONS.md](docs/LIMITATIONS.md).

## RF reverse-engineering notes

The project contains experimentally derived information about:

- preamble/sync timings,
- 64-bit / 8-byte frames,
- channel bytes,
- command bytes,
- checksum behaviour,
- the channel-6 special STOP/RELEASE frame.

See [docs/PROTOCOL.md](docs/PROTOCOL.md).

## Repository layout

```text
esphome-idemo-idrm-cc1101/
├── README.md
├── README.es.md
├── LICENSE
├── CHANGELOG.md
├── CONTRIBUTING.md
├── .gitignore
├── esphome/
│   ├── idemo-idrm-gateway.yaml
│   └── secrets.example.yaml
├── docs/
│   ├── INSTALLATION.md
│   ├── WIRING.md
│   ├── PAIRING_AND_ID.md
│   ├── CALIBRATION.md
│   ├── PROTOCOL.md
│   └── LIMITATIONS.md
└── .github/
    └── ISSUE_TEMPLATE/
        ├── bug_report.md
        └── rf_capture.md
```

## Contributing

RF captures, tests with other Idemo IDRM products, calibration improvements and code reviews are welcome.

Please read [CONTRIBUTING.md](CONTRIBUTING.md).

When reporting RF behaviour, clearly separate:

- experimentally captured data,
- behaviour confirmed on a physical motor,
- inferred/proposed protocol rules.

## License

MIT License. See [LICENSE](LICENSE).

## Disclaimer

This is an independent reverse-engineering and interoperability project. Use it at your own risk. Work on mains-powered roller shutter installations only if you are competent and authorised to do so. The ESP32/CC1101 side is low-voltage; the shutter motor installation may involve dangerous mains voltage.
