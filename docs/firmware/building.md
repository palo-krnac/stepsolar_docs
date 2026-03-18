# Building & Flashing

## PlatformIO Configuration

```ini title="platformio.ini"
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
board_build.filesystem = littlefs
board_build.partitions = default_8MB.csv

lib_deps =
    adafruit/Adafruit ADS1X15 @ ^2.5.0
    adafruit/Adafruit BusIO @ ^1.14.0
    https://github.com/mathieucarbou/AsyncTCP.git
    https://github.com/mathieucarbou/ESPAsyncWebServer.git
    bblanchon/ArduinoJson @ ^7.1.0

monitor_speed = 115200
upload_speed  = 921600
```

## Commands

=== "Upload firmware"
    ```bash
    pio run --target upload
    ```

=== "Upload web UI"
    ```bash
    pio run --target uploadfs
    ```

=== "Upload both"
    ```bash
    pio run -t upload -t uploadfs
    ```

=== "Serial monitor"
    ```bash
    pio device monitor --baud 115200
    ```

=== "Clean build"
    ```bash
    pio run --target clean
    ```

## Build Flags

| Flag | Default | Purpose |
|------|---------|---------|
| `CORE_DEBUG_LEVEL` | 0 | ESP-IDF log level (0=none, 5=verbose) |

Enable verbose logging during development:

```ini
build_flags =
    -DCORE_DEBUG_LEVEL=4
```

## Library Notes

- **AsyncTCP / ESPAsyncWebServer**: Uses the `mathieucarbou` fork which supports ESP32-S3 and ESP-IDF v5. The original `me-no-dev` fork does not compile on newer toolchains.
- **LittleFS**: Built into the `espressif32` platform — do **not** add it to `lib_deps`.
- **ArduinoJson v7**: Breaking changes from v6. The codebase uses v7 API (`JsonDocument` instead of `DynamicJsonDocument`).
