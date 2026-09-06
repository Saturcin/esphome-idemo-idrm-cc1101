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
| GDO2 | GPIO33 | Asynchronous RF RX data / built-in sniffer |

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

remote_receiver:
  id: idrm_rx
  pin: GPIO33
  tolerance: 30%
  filter: 50us
  idle: 4ms

remote_transmitter:
  id: idrm_tx
  pin: GPIO32
  carrier_duty_percent: 100%
  non_blocking: false

  on_transmit:
    then:
      - cc1101.begin_tx

  on_complete:
    then:
      - cc1101.begin_rx
```

## Power warning

The CC1101 is a **3.3 V device**. Do not connect its VCC to 5 V.

## RF considerations

Use a CC1101 module intended for the 433 MHz band and an appropriate antenna. Range depends heavily on antenna quality, module quality, orientation and local interference.

## Mains warning

The gateway itself is low voltage. The shutter motor may be powered from mains voltage. This repository does not provide mains wiring instructions.

## Built-in sniffer

The public YAML uses the recommended CC1101 dual-pin arrangement: GDO0 for TX and GDO2 for RX. The custom `on_raw` decoder prints recognised IDRM frames as `IDRM SNIFFER` lines. If GDO2 is not connected, transmission can still work but the sniffer cannot receive the original remote.
