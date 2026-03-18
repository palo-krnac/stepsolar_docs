# Requirements

## Hardware

| Item | Specification | Notes |
|------|--------------|-------|
| MCU board | ESP32-S3-DevKitC-1-N8 | 8 MB Flash, no PSRAM required |
| ADC | ADS1115 (16-bit, 4-ch) | I2C address 0x48 (ADDR→GND) |
| EEPROM | 24LC256 (32 kB) | I2C address 0x50 (A0/A1/A2→GND) |
| RS485 transceiver | MAX485, SP3485, or equivalent | Half-duplex, DE+RE combined |
| Temperature sensor | KTY81/210 NTC | With 2.2 kΩ series resistor to 3.3V |
| Current sensor | ACS712-20A | Or shunt + op-amp, adjust `CURRENT_SENSITIVITY` |
| Gate driver | IR2104 or equivalent | Deadtime ~2 ms at 50 Hz |

## Software

| Tool | Version | Link |
|------|---------|------|
| PlatformIO | ≥ 6.x | [platformio.org](https://platformio.org) |
| VS Code | Any | [code.visualstudio.com](https://code.visualstudio.com) |
| espressif32 platform | 2023.6.2+ | Auto-installed by PlatformIO |

## Libraries (auto-installed)

```ini
lib_deps =
    adafruit/Adafruit ADS1X15 @ ^2.5.0
    adafruit/Adafruit BusIO @ ^1.14.0
    https://github.com/mathieucarbou/AsyncTCP.git
    https://github.com/mathieucarbou/ESPAsyncWebServer.git
    bblanchon/ArduinoJson @ ^7.1.0
```

## Power Supply

- ESP32-S3 board: 5V via USB or 3.3V regulated
- ADS1115 + 24LC256: 3.3V from ESP32 (< 10 mA total)
- RS485 transceiver: 3.3V or 5V depending on module

!!! warning "Voltage levels"
    The ADS1115 GAIN_ONE setting allows ±4.096V input. The PV voltage divider (470 kΩ / 5.6 kΩ) scales panel voltage down to the ADC input range. Verify divider ratio matches your panel's maximum open-circuit voltage before connecting.
