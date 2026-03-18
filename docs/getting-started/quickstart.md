# Quick Start

This guide gets stepSOLAR running from zero in under 10 minutes.

## Prerequisites

- ESP32-S3-DevKitC-1 board (8 MB Flash)
- [PlatformIO](https://platformio.org/) (VS Code extension or CLI)
- ADS1115 module wired to I2C
- 24LC256 EEPROM wired to I2C

## 1 — Clone the Repository

```bash
git clone https://github.com/palo-krnac/stepSOLAR.git
cd stepSOLAR
```

## 2 — Connect Hardware

Minimum required connections before first flash:

| ESP32-S3 Pin | Connects to |
|---|---|
| GPIO 8 (SDA) | ADS1115 SDA + 24LC256 SDA |
| GPIO 9 (SCL) | ADS1115 SCL + 24LC256 SCL |
| 3.3V | ADS1115 VDD, 24LC256 VCC |
| GND | ADS1115 GND, 24LC256 GND, ADDR pins |

!!! tip "I²C pull-ups"
    Add 4.7 kΩ pull-up resistors from SDA and SCL to 3.3V if your modules do not have them on-board.

## 3 — Flash Firmware

```bash
# Flash the C++ firmware
pio run --target upload

# Flash the web UI (HTML/CSS/JS to LittleFS)
pio run --target uploadfs
```

Both commands can be combined:

```bash
pio run -t upload -t uploadfs
```

## 4 — First Boot

Open the serial monitor to confirm startup:

```
[BOOT] stepSOLAR v2.0.0
[OK] ADS1115
[EE] First run — writing defaults
[WiFi] AP: stepSOLAR / solar2024  IP: 192.168.4.1
[FS] LittleFS OK
[OK] WebServer ready
[BOOT] Starting tasks...
[C0] Control task started
[C1] Comms task started
```

## 5 — Open Web Interface

Connect to WiFi AP **`stepSOLAR`** with password **`solar2024`**, then open:

```
http://192.168.4.1
```

## 6 — Configure WiFi

Go to the **WiFi** tab, enter your home network SSID and password, click **Save & Restart**. The device will connect to your network and show its IP in the serial monitor.

!!! note "Finding the IP"
    After connecting to your network, the IP is visible in the serial monitor:
    ```
    [WiFi] IP: 192.168.1.XX
    ```
    You can also check your router's DHCP list.

## 7 — Set Timezone & NTP

Go to the **Time** tab, set your UTC offset (e.g. `60` for UTC+1), enable NTP sync, and click **Save**. Time will synchronize automatically.

## Next Steps

- [Hardware wiring details →](../hardware/overview.md)
- [MPPT algorithm explained →](../firmware/mppt.md)
- [Modbus register map →](../modbus/registers.md)
- [ESPHome integration →](../esphome/integration.md)
