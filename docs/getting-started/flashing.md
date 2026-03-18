# Flashing

stepSOLAR uses two separate flash partitions: **firmware** (C++ application) and **LittleFS** (web UI files). Both must be flashed for full functionality.

## Partition Layout

```
┌─────────────────────────────────┐  8 MB Flash
│  bootloader        (0x1000)     │
│  partition table   (0x8000)     │
│  otadata           (0xD000)     │
│  app0 — firmware   ~1.5 MB      │  ← pio run -t upload
│  spiffs/littlefs   ~1.5 MB      │  ← pio run -t uploadfs
│  nvs, coredump...               │
└─────────────────────────────────┘
```

## Flash Firmware

```bash
pio run --target upload
```

## Flash Web UI

The web UI lives in the `/data` folder and is uploaded separately to LittleFS:

```bash
pio run --target uploadfs
```

!!! tip "Upload both at once"
    ```bash
    pio run -t upload -t uploadfs
    ```

## Erase Flash (Factory Reset via CLI)

If you want a completely clean start:

```bash
# Erase entire flash
esptool.py --chip esp32s3 erase_flash

# Then re-flash everything
pio run -t upload -t uploadfs
```

## OTA Update (Future)

OTA support is planned for v2.1. Until then, USB flashing is required.

## Serial Monitor

```bash
pio device monitor --baud 115200
```

Expected output on first boot:

```
[BOOT] stepSOLAR v2.0.0
[OK] ADS1115
[EE] First run — writing defaults
[WiFi] AP: stepSOLAR / solar2024  IP: 192.168.4.1
[FS] LittleFS OK
[OK] WebServer ready
[INFO] Energy: 0.00 kWh
[BOOT] Starting tasks...
[C0] Control task started
[C1] Comms task started
```

!!! note "LittleFS not flashed"
    If you forgot `uploadfs`, the web interface shows:
    ```
    LittleFS not flashed.
    Run: pio run -t uploadfs
    ```
