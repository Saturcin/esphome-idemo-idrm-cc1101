# Wiring

## ESP32 ↔ CC1101

| CC1101 pin | ESP32 pin | Purpose |
|---|---|---|
| VCC | 3V3 | Power |
| GND | GND | Ground |
| SCK | GPIO18 | SPI clock |
| MOSI | GPIO23 | SPI MOSI |
| MISO | GPIO19 | SPI MISO |
| CSN / CS | GPIO5 | Chip select |
| GDO0 | GPIO32 | Asynchronous RF TX data |
| GDO2 | Not connected | Receiver/sniffer removed |

YAML:

```yaml
spi:
  clk_pin: GPIO18
  mosi_pin: GPIO23
  miso_pin: GPIO19

cc1101:
  id: radio_idrm
  cs_pin: GPIO5
  frequency: 433.92MHz
  modulation_type: ASK/OOK
  filter_bandwidth: 203kHz
  output_power: 10

remote_transmitter:
  id: idrm_tx
  pin: GPIO32
  carrier_duty_percent: 100%
  non_blocking: false
```

## Power warning

The CC1101 is a **3.3 V device**. Do not connect its VCC to 5 V.

## RF considerations

Use a CC1101 module intended for the 433 MHz band and an appropriate antenna. Range depends heavily on antenna quality, module quality, orientation and local interference.

## Mains warning

The gateway itself is low voltage. The shutter motor may be powered from mains voltage. This repository does not provide mains wiring instructions.
