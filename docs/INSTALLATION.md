# Installation

## 1. Prerequisites

You need:

- Home Assistant.
- ESPHome.
- ESP32 board.
- CC1101 433 MHz module.
- A correctly installed Idemo IDRM shutter motor.

ESPHome currently provides an official CC1101 component and Remote Transmitter support:

- https://esphome.io/components/cc1101/
- https://esphome.io/components/remote_transmitter/
- https://esphome.io/components/cover/template/

## 2. Copy the YAML

Copy:

```text
esphome/idemo-idrm-gateway.yaml
```

into your ESPHome configuration directory.

## 3. Wi-Fi secrets

Copy the values from `esphome/secrets.example.yaml` into your own ESPHome `secrets.yaml`.

Do not commit your real Wi-Fi password to GitHub.

## 4. Review installation-specific values

Before flashing, review:

- Wi-Fi secrets.
- ESP32 board type.
- CC1101 wiring.
- RF transmitter ID (`46 84 5D 9C` in the development configuration).
- Number of channels actually used.
- Opening/closing times.
- Calibration curves.

## 5. Validate

Run ESPHome **Validate** before installation.

## 6. Flash the ESP32

Use the normal ESPHome installation workflow.

## 7. Add to Home Assistant

After the node is online, Home Assistant should discover the ESPHome device. Add it and check that the six cover entities and calibration/time entities are available.

## 8. Pairing

If the motor does not recognise the gateway as a transmitter, pairing is required. See `PAIRING_AND_ID.md`.

## 9. Establish a known position

Because there is no position feedback, start by fully opening or fully closing each shutter once.

## 10. Calibrate

Measure travel time and then adjust the up/down curves. See `CALIBRATION.md`.
