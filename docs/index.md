---
title: stepSOLAR Documentation
description: Solar Water Heater Inverter Controller based on ESP32-S3
hide:
  - navigation
  - toc
---

<div class="hero">
  <div class="hero-title">stepSOLAR</div>
  <div class="hero-sub">Solar Water Heater Inverter Controller — ESP32-S3</div>
  <div class="hero-badges">

[![GitHub](https://img.shields.io/badge/GitHub-palo--krnac%2FstepSOLAR-3DD639?style=flat&logo=github&logoColor=white)](https://github.com/palo-krnac/stepSOLAR)
[![Version](https://img.shields.io/badge/firmware-v2.0.0-3DD639?style=flat)](changelog/changelog.md)
[![License](https://img.shields.io/badge/license-MIT-3DD639?style=flat)](https://github.com/palo-krnac/stepSOLAR/blob/main/LICENSE)
[![Platform](https://img.shields.io/badge/platform-ESP32--S3-3DD639?style=flat&logo=espressif)](hardware/overview.md)
[![PlatformIO](https://img.shields.io/badge/built%20with-PlatformIO-3DD639?style=flat)](firmware/building.md)

  </div>
</div>

---

**stepSOLAR** is an open-source solar water heater inverter controller that converts DC power from photovoltaic panels into AC rectangular waveform with variable duty cycle (MPPT). It runs on ESP32-S3, measures voltage, current and boiler temperature via ADS1115 ADC, stores configuration in 24LC256 EEPROM, and provides a responsive web interface with real-time monitoring.

## Key Features

<div class="grid cards" markdown>

-   :fontawesome-solid-solar-panel: **MPPT Control**

    ---

    Hill-climbing P&O algorithm with automatic VA curve scan. Maximizes power extraction from any PV panel configuration.

-   :fontawesome-solid-globe: **Web Interface**

    ---

    Responsive dashboard with real-time WebSocket telemetry, power history chart and multi-language support (EN / SK / PL / CS).

-   :fontawesome-solid-clock: **NTP Time Sync**

    ---

    Automatic time synchronization over WiFi with configurable timezone and DST offset. Falls back gracefully when offline.

-   :fontawesome-solid-network-wired: **Modbus RTU**

    ---

    RS485 Modbus slave with fully configurable baud rate, parity and stop bits. Compatible with SCADA, Home Assistant and industrial systems.

-   :fontawesome-solid-microchip: **Dual-Core FreeRTOS**

    ---

    Control loop (Core 0) is fully isolated from WiFi/TCP stack (Core 1). PWM and protection never blocked by network activity.

-   :fontawesome-solid-home: **ESPHome / HA**

    ---

    Native ESPHome YAML for direct Home Assistant integration. All sensors exposed as HA entities.

</div>

## Hardware at a Glance

| Component | Part | Interface |
|-----------|------|-----------|
| MCU | ESP32-S3-DevKitC-1 | — |
| ADC | ADS1115 (16-bit) | I2C 0x48 |
| EEPROM | 24LC256 (32kB) | I2C 0x50 |
| PWM output | 2× LEDC 50Hz 11-bit | GPIO 17, 18 |
| RS485 | MAX485 / SP3485 | UART2 |
| Temperature | KTY81/210 NTC | ADS1115 ch2 |

## Quick Start

```bash
# 1 — Clone
git clone https://github.com/palo-krnac/stepSOLAR.git
cd stepSOLAR

# 2 — Flash firmware
pio run -t upload

# 3 — Flash web UI
pio run -t uploadfs

# 4 — Connect
# AP: stepSOLAR / solar2024  →  http://192.168.4.1
```

[Full Quick Start Guide →](getting-started/quickstart.md){ .md-button .md-button--primary }
[Hardware Overview →](hardware/overview.md){ .md-button }
