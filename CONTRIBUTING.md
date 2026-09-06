# Contributing

Contributions are welcome.

Useful contributions include:

- RF captures from other Idemo IDRM remotes.
- Tests with other BLU / LIMIT / IDRM motors.
- Confirmation or correction of command/channel bytes.
- Better calibration algorithms.
- ESPHome compatibility fixes.
- Documentation improvements.

## When submitting RF information

Please include:

1. Idemo motor model.
2. Remote model and number of channels.
3. ESPHome version.
4. CC1101 module and wiring.
5. Exact action performed (UP / STOP / DOWN / channel).
6. Raw capture or decoded bytes.
7. Whether the frame was only captured or physically verified on the motor.

Please label conclusions as one of:

- **Captured** — visible in RF data.
- **Physically confirmed** — tested successfully on a motor.
- **Inferred** — a hypothesis/pattern not yet independently confirmed.

## Bug reports

Please include ESPHome validation/compiler output and relevant logs.

Do not publish Wi-Fi passwords, API keys or other secrets.

## Pull requests

Keep RF protocol changes separate from calibration/UI changes when practical. This makes physical verification easier.
