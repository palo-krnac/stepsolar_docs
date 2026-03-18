# ESPHome Integration

stepSOLAR supports two ESPHome integration modes:

## Mode 1 — JSON UART Bridge (Recommended)

Run stepSOLAR firmware on your ESP32-S3. Set **Communication** to `0 (JSON)` in Settings. The device sends a JSON line on UART0 every 3 seconds:

```json
{"device":"stepSOLAR","fw":"2.0.0","V":285,"A":8.56,"W":2440,"T":58,"kWh":123.46}
```

A second ESP32 (or ESP8266) running ESPHome reads this line and exposes all values as Home Assistant entities.

**Advantages:**
- Full MPPT firmware running on the controller
- ESPHome just bridges data — no control logic needed
- Firmware updates independent of ESPHome

## Mode 2 — Pure ESPHome

Flash `siton210_esphome.yaml` (included in the repository) directly to the ESP32-S3. ESPHome handles all sensor readings via ADS1115 integration.

**Advantages:**
- Native HA integration, auto-discovery
- OTA updates via ESPHome

**Limitations:**
- No MPPT algorithm — duty cycle must be controlled manually via HA automations
- No web dashboard
- No energy accumulation in EEPROM

## Mode 3 — WiFi + Modbus to HA

Use the built-in Modbus RTU slave and connect via USB-RS485 adapter to the HA server:

```yaml
# See Modbus → Register Map for full configuration
modbus:
  - name: stepsolar
    type: serial
    port: /dev/ttyUSB0
    baudrate: 9600
```

This is the most reliable approach for permanent installations — no WiFi dependency for data logging.

## Choosing the Right Mode

| | Mode 1 (JSON Bridge) | Mode 2 (Pure ESPHome) | Mode 3 (Modbus) |
|--|---------------------|----------------------|-----------------|
| MPPT | ✅ Full P&O | ❌ Manual only | ✅ Full P&O |
| Web UI | ✅ Built-in | ❌ None | ✅ Built-in |
| HA integration | Via bridge | ✅ Native | Via Modbus |
| WiFi required | Yes | Yes | No |
| Cable required | No | No | RS485 cable |
