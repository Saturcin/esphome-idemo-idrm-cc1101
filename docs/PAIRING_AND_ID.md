# Transmitter ID, capture and pairing status

## Current project status

The gateway transmits using a 4-byte IDRM transmitter identity.

The public YAML exposes those bytes as:

```yaml
substitutions:
  idrm_id_1: "0x46"
  idrm_id_2: "0x84"
  idrm_id_3: "0x5D"
  idrm_id_4: "0x9C"
```

The development identity was:

```text
46 84 5D 9C
```

This value came from a legitimate physical IDRM remote used during reverse engineering.

## What community users should do today

At the current stage of the project, the recommended approach is:

1. Connect CC1101 `GDO2` to ESP32 `GPIO33`.
2. Flash the public YAML and open the ESPHome logs.
3. Press UP, STOP and DOWN on a legitimate IDRM remote already paired to the target motor (2-3 presses each is recommended).
4. Look for `IDRM SNIFFER` log lines.
5. The first four bytes shown after `ID=` are the transmitter-ID bytes.
6. Put those four bytes into the YAML substitutions.
7. Save the complete 8-byte UP/STOP/DOWN frame lines for troubleshooting/community validation.
8. Validate, flash and test with a single channel before deploying all channels.

The public YAML now includes the receiver/sniffer. It returns the CC1101 to RX mode after gateway transmissions, so listening remains available during normal operation.

## What to look for in the log

Example:

```text
IDRM SNIFFER | FRAME=46 84 5D 9C 02 00 16 95 | ID=46 84 5D 9C | CH=02 00 | CMD=16 (UP) | CHECK=95
```

Interpretation used by this project:

- bytes 1-4: transmitter identity;
- bytes 5-6: channel;
- byte 7: command;
- byte 8: observed check/CRC byte.

For the YAML substitutions, use bytes 1-4. Keep the complete frame captures because only the development identity has been fully characterised so far.

## Why not generate a random ID?

We experimentally tested a second identity:

```text
46 84 5D 9D
```

and attempted to add it as a new transmitter using the normal IDRM programming procedure.

The motor did not accept it.

A second test also adjusted the final check byte according to the additive pattern observed in the development captures. Pairing still failed.

Therefore, the project currently does **not** claim that:

- arbitrary 4-byte IDs are valid IDRM transmitter identities;
- changing only the 4 ID bytes is enough to pair a new transmitter;
- the complete IDRM rolling-code/pairing mechanism has been decoded.

## Check-byte caveat

The command/check-byte rules implemented by the project are strongly validated for the development transmitter ID `46 84 5D 9C`.

However, we currently have captures from only that one legitimate transmitter identity. We therefore cannot yet guarantee that the final check byte generalises unchanged to every other IDRM transmitter identity.

Captures from additional legitimate IDRM remotes are especially valuable.

## Pairing information

Idemo documentation describes adding an additional transmitter by putting the motor into programming mode with an already-associated transmitter and then pressing UP on the new transmitter.

That tells us the motors can store multiple transmitters, but it does **not** reveal how a valid new IDRM identity / rolling-code state is generated.

This repository therefore documents the official user-level pairing concept but does not claim to reproduce the full transmitter-enrolment algorithm.

## How to contribute a new transmitter capture

Please open an RF capture issue and include:

- motor model;
- remote model;
- channel;
- UP, STOP and DOWN frames;
- several repeated presses of the same command if possible;
- whether the remote was already paired or was being enrolled.

See `.github/ISSUE_TEMPLATE/rf_capture.md`.
