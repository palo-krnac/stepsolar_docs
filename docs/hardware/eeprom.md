# 24LC256 EEPROM

## Specifications

| Parameter | Value |
|-----------|-------|
| Capacity | 32 kB (32,768 bytes) |
| Page size | 64 bytes |
| Write endurance | ~1,000,000 cycles/byte |
| Write time | 5 ms max |
| I2C speed | Up to 400 kHz |
| Address (A0/A1/A2 = GND) | `0x50` |

## Address Map

| Address | Size | Content |
|---------|------|---------|
| `0x0000` | 4B | Magic `0x53545053` ("STPS") |
| `0x0004` | 2B | Node ID |
| `0x0006` | 2B | Max boiler temperature (°C) |
| `0x0008` | 2B | Max power limit (W) |
| `0x000A` | 2B | VA test period (minutes) |
| `0x000C` | 2B | VA test enabled flag |
| `0x000E` | 2B | Voltage calibration offset |
| `0x0010` | 2B | Current calibration offset |
| `0x0012` | 2B | Communication type (0=JSON, 1=Modbus) |
| `0x0014` | 2B | Modbus slave ID |
| `0x0016` | 4B | Modbus baud rate |
| `0x001A` | 1B | Modbus parity (0=None, 1=Even, 2=Odd) |
| `0x001B` | 1B | Modbus stop bits (0=1bit, 1=2bits) |
| `0x001C` | 2B | Max recorded voltage |
| `0x001E` | 2B | Max recorded current |
| `0x0020` | 2B | Max recorded power |
| `0x0022` | 1B | UI Language (0=EN, 1=SK, 2=PL, 3=CS) |
| `0x0024` | 2B | Timezone UTC offset (minutes) |
| `0x0026` | 2B | DST offset (minutes) |
| `0x0028` | 1B | NTP enabled |
| `0x0030` | 32B | WiFi SSID |
| `0x0050` | 64B | WiFi password |
| `0x0090` | 64B | NTP server hostname |
| `0x0200–0x0FFF` | 512×4B | Energy production (wear-leveling) |

## Wear-Leveling

Energy production (Wh) is written frequently. To extend EEPROM life, `VyrobaStore` implements a rotating slot scheme:

- **512 slots** × 4 bytes each, starting at `0x0200`
- Every **500 writes**, the slot index advances by 1
- Total writes before rotation repeats: **512,000**
- At 1 write/hour: ~58 years of operation

On boot, `VyrobaStore::begin()` scans all slots and picks the one with the highest value (most recent).

## Factory Reset

Writing `0x00000000` to address `0x0000` (magic field) clears the configuration. On next boot, all defaults are written automatically.

```bash
# Via web UI: Settings → Factory Reset
# Via serial: the firmware writes 0 to magic and calls ESP.restart()
```
