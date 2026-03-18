# Pin Mapping Reference

Full GPIO reference for stepSOLAR on ESP32-S3-DevKitC-1.

## I2C Bus

Both ADS1115 and 24LC256 share the same I2C bus. Ensure addresses don't conflict.

| Device | Address | ADDR pin |
|--------|---------|----------|
| ADS1115 | `0x48` | ADDR → GND |
| 24LC256 | `0x50` | A0, A1, A2 → GND |

```
ESP32-S3                        I2C Bus (400 kHz)
GPIO8 SDA ──────────────────────┬── ADS1115 SDA
GPIO9 SCL ──────────────────────┼── ADS1115 SCL
3.3V ────────────────────────── ┼── ADS1115 VDD
                                │
                                ├── 24LC256 SDA
                                ├── 24LC256 SCL
                                └── 24LC256 VCC

4.7 kΩ pull-ups on SDA and SCL to 3.3V (if not on module)
```

## ADS1115 Channel Assignment

| Channel | Signal | Sensor | Range |
|---------|--------|--------|-------|
| AIN0 | PV Current | ACS712-20A | 0–20 A |
| AIN1 | PV Voltage | Divider 470k/5.6k | 0–400 V |
| AIN2 | Boiler Temp | KTY81/210 + 2.2kΩ | 0–120°C |
| AIN3 | — | Free | — |

## RS485 Interface

```
ESP32-S3                        MAX485 / SP3485
GPIO15 TX  ──────────────────── DI
GPIO16 RX  ──────────────────── RO
GPIO14 DE  ──────────────────── DE ─┐
           ──────────────────── RE  ┘ (tied together)
3.3V       ──────────────────── VCC
GND        ──────────────────── GND
                                A ──── RS485 bus +
                                B ──── RS485 bus −
```

## Protection & Control Inputs

| GPIO | Signal | Pull | Active level |
|------|--------|------|-------------|
| 4 | Enable | Pull-up | LOW = run |
| 5 | Overcurrent flag | External | LOW = fault |
| 6 | Overcurrent reset | Hi-Z default | HIGH pulse = reset |
