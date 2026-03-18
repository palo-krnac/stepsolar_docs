# Web Interface Overview

## Architecture

The web interface is a single-page application served from LittleFS. All real-time data is delivered over **WebSocket** — the page never needs to refresh.

```
Browser                    ESP32-S3 (Core 1)
   │                              │
   │── HTTP GET /  ──────────────►│ LittleFS → index.html
   │◄─ 200 index.html ────────────│
   │                              │
   │── WS connect /ws ───────────►│ AsyncWebSocket
   │◄─ telemetry JSON 500ms ──────│ broadcastTelemetry()
   │                              │
   │── POST /api/config ─────────►│ save to EEPROM
   │── POST /api/cmd ────────────►│ → xCmdMutex → Core 0
   │── GET  /api/i18n?lang=sk ───►│ translation table
```

## Access Points

| Scenario | URL |
|----------|-----|
| AP mode (no WiFi configured) | `http://192.168.4.1` |
| STA mode (connected to network) | `http://<device-ip>` |
| AP credentials | SSID: `stepSOLAR` / Password: `solar2024` |

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/` | Serve `index.html` from LittleFS |
| `GET` | `/api/config` | Return current config as JSON |
| `POST` | `/api/config` | Update config fields, save to EEPROM |
| `POST` | `/api/wifi` | Save WiFi credentials, restart |
| `GET` | `/api/i18n?lang=sk` | Translation table for selected language |
| `POST` | `/api/cmd` | Send control command to Core 0 |
| `WS` | `/ws` | WebSocket telemetry stream |

## Telemetry JSON

Sent every 500 ms to all connected WebSocket clients:

```json
{
  "type":       "telemetry",
  "napeti":     285,
  "proud":      856,
  "vykon":      2440,
  "teplota":    58,
  "vyroba":     123456,
  "strida":     193,
  "rucne":      false,
  "enabled":    true,
  "ochrana":    false,
  "nadproud":   false,
  "teplotaMax": 90,
  "maxV":       312,
  "maxA":       980,
  "maxW":       2810,
  "uptime":     14523000,
  "timeStr":    "14:32:07 18.03.2026",
  "ntpSynced":  true,
  "nodeID":     12,
  "lang":       1,
  "fw":         "2.0.0"
}
```

## Tabs

| Tab | Content |
|-----|---------|
| **Monitor** | Live power, voltage, current, duty cycle bar, temperature, energy total, power history chart |
| **Control** | Manual duty cycle ±, AUTO MPPT button, VA test, energy set, max reset |
| **Settings** | All runtime parameters (limits, calibration, Modbus, language) |
| **WiFi** | SSID/password, connection status |
| **Time** | NTP enable, server, timezone, DST, manual sync |
