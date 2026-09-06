# Changelog

## Unreleased

### Changed

- Exposed the four IDRM transmitter-ID bytes as YAML `substitutions`.
- Documented that the ID should currently come from a legitimate paired remote capture.
- Documented failed arbitrary-ID pairing experiments and current rolling-code/check-byte uncertainty.
- Return CC1101 to IDLE after each transmission.

## 0.1.0-beta

Initial public community release.

### Included

- ESP32 + CC1101 433.92 MHz ASK/OOK gateway.
- Six Idemo IDRM shutter channels.
- Home Assistant template cover entities.
- Direct mathematical percentage positioning.
- Editable travel times.
- Editable directional calibration curves.
- Current default raising curve:
  `25:25|50:70|70:90|80:100`
- Current default lowering curve:
  `25:3|50:7|75:45|85:60|90:75`
- Experimentally confirmed channel-6 STOP/RELEASE special handling.
- RF sniffer removed from normal runtime configuration.
- English and Spanish documentation.

### Status

Beta / experimental. Position is open-loop and depends on timing/calibration.
