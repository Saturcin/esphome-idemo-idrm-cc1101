# Transmitter ID and pairing

## The current transmitter ID

The development YAML uses:

```text
46 84 5D 9C
```

It is embedded in both command and release frames.

This should be treated as an **installation-specific transmitter identity**, not as a universal constant of IDRM.

## Where it appears

Search the YAML for:

```cpp
const uint8_t frame[8]
```

and:

```cpp
const uint8_t release_frame[8]
```

The first four bytes are currently:

```cpp
0x46, 0x84, 0x5D, 0x9C
```

## Community recommendation

For a new installation:

1. Choose/review the transmitter identity.
2. Flash the gateway.
3. Put the target motor into the mode for adding an additional transmitter.
4. Send an UP command from the relevant gateway channel.
5. Verify that the motor confirms the new transmitter and responds normally.

Idemo documentation for IDRM products commonly describes adding an additional transmitter by holding **STOP** on an already-associated transmitter until the motor indicates programming mode, then pressing **UP** on the new transmitter. Exact behaviour can vary by product, so check the official instructions for your motor.

Official Idemo site:

https://idemomotors.com/blu-45

## Important

Do not change transmitter bytes randomly after pairing unless you intend to pair the motor again. From the motor's perspective, a different transmitter identity may be a different remote.
