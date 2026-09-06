# IDRM RF protocol notes

> These notes are based on RF captures and physical tests performed during development.  
> They are **reverse-engineering notes**, not an official IDRM specification.

## Radio

Tested configuration:

```text
Frequency: 433.92 MHz
Modulation: ASK/OOK
```

Official Idemo material identifies BLU45 as an IDRM radio motor in the 433.92 MHz family:

https://idemomotors.com/blu-45

## Pulse timing observed

Approximate nominal timings:

```text
PREAMBLE:
8 × (+300 / -600 µs)

SYNC:
+5000 / -650 µs

START:
+590 / -290 µs

BIT 0:
+265 / -625 µs

BIT 1:
+590 / -290 µs

Frame:
64 bits / 8 bytes, MSB first

Inter-frame/final low:
~5000 µs
```

## Frame layout

Observed 8-byte frame:

```text
[ID0] [ID1] [ID2] [ID3] [CHANNEL] [00] [COMMAND] [CHECK]
```

Development transmitter ID:

```text
46 84 5D 9C
```

## Channels

| Channel | Bytes |
|---:|---|
| 1 | `02 00` |
| 2 | `04 00` |
| 3 | `08 00` |
| 4 | `10 00` |
| 5 | `20 00` |
| 6 | `40 00` |

## Normal command bytes

For channels 1–5:

```text
UP      = 0x16
STOP    = 0x46
DOWN    = 0x87
RELEASE = 0x48
```

Checksum pattern observed:

```text
UP      = 0x93 + channel   (mod 256)
STOP    = 0xC3 + channel   (mod 256)
DOWN    = 0x03 + channel   (mod 256)
RELEASE = 0xC5 + channel   (mod 256)
```

## Channel 6 special case

Physical capture and motor tests showed:

```text
UP:
46 84 5D 9C 40 00 16 D3

DOWN:
46 84 5D 9C 40 00 87 43

STOP:
46 84 5D 9C 40 00 47 03

RELEASE:
46 84 5D 9C 40 00 49 05
```

Important observations:

- channel-6 STOP uses command byte `0x47`, not `0x46`;
- channel-6 RELEASE uses `0x49`, not `0x48`;
- channel-6 STOP was observed without a following RELEASE sequence.

This behaviour is implemented explicitly in the YAML.

## Transmission sequence

The gateway sends:

1. preamble;
2. six copies of the command frame;
3. normally another preamble;
4. six copies of the RELEASE frame.

Exception: confirmed channel-6 STOP does not send RELEASE.

## Rolling-code caution

Some official/legacy Idemo material describes BLU radio products as using IDRM / rolling-code technology. The frames used in this project were experimentally repeatable and accepted during the tests performed, but this repository **does not claim that the complete IDRM rolling-code mechanism has been decoded**.

Do not generalise the observed frame behaviour to every Idemo IDRM product without captures and physical tests.
