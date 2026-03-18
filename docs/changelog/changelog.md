# Changelog

All notable changes to stepSOLAR are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [2.0.0] — 2024

### Added
- **Dual-core FreeRTOS architecture** — Core 0 handles control, Core 1 handles WiFi/Web
- **24LC256 EEPROM** (32 kB) with 64-byte page writes and wear-leveling for energy counter
- **ADS1115 16-bit ADC** — replaces Arduino 10-bit ADC for all measurements
- **Responsive web UI** — works on mobile, dark theme with `#3DD639` green accent
- **Real-time WebSocket** telemetry at 500 ms update rate
- **Power history chart** — 60-second canvas graph in Monitor tab
- **Multi-language support** — EN / SK / PL / CS, selection saved to EEPROM
- **NTP time synchronization** — configurable server, timezone, DST, stored in EEPROM
- **Configurable RS485 serial** — baud rate, parity, stop bits changeable via web UI without reboot
- **LittleFS web UI** — HTML/CSS/JS stored in separate flash partition, updatable without reflashing firmware
- **Modbus RTU slave** — custom implementation, no external library dependency
- **ESPHome YAML** — direct and bridge integration modes

### Changed
- Project renamed from **SITON 210** to **stepSOLAR**
- WiFi AP credentials: `stepSOLAR` / `solar2024`
- EEPROM magic changed to `0x53545053` ("STPS")
- `strida` renamed to `strieda` in web UI and documentation
- `CtrlCmd` enum values prefixed with `CMD_` to avoid conflict with Arduino `DEC` macro

### Fixed
- `EEPROM24LC256` missing `private:` section with `_addr` member
- `CtrlCmd::DEC` conflict with Arduino `Print.h` `#define DEC 10`
- `OCHRANA_RESET_PIN` undefined — corrected to `OCHRANA_RESET`
- `volatile CtrlCmd` preventing copy constructor — removed `volatile`, mutex used instead

---

## [1.1.0] — 2024

### Added
- Dual-core task separation (initial implementation)
- FreeRTOS mutex protection for shared data
- `webCmdCallback()` mechanism for Core 1 → Core 0 commands

### Fixed
- `mathieucarbou/ESP Async WebServer` registry name — replaced with git URL
- `OCHRANA_RESET_PIN` vs `OCHRANA_RESET` naming inconsistency

---

## [1.0.0] — 2024

### Initial ESP32-S3 port from Arduino Mini (SITON 210 v5.4.27)

- Ported from Arduino Mini to ESP32-S3
- `Timer1` PWM → ESP32 LEDC
- `SoftwareSerial` RS485 → `HardwareSerial`
- LCD + buttons → AsyncWebServer + WebSocket
- `SoftEasyTransfer` → Modbus RTU / JSON
- `avr/wdt.h` → `esp_task_wdt`
- Internal EEPROM → 24LC64 I2C EEPROM
- Arduino ADC → ADS1115

---

## Planned

- [ ] OTA firmware update via web UI
- [ ] mDNS `stepsolar.local` hostname
- [ ] HTTPS / basic authentication for web UI
- [ ] Energy export CSV download
- [ ] Additional language support (DE, HU, RO)
- [ ] Schematic and PCB files (KiCad)
