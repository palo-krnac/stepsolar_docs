# WiFi & Time

## WiFi Tab

### Connecting to a Network

1. Enter your WiFi **SSID** and **password**
2. Click **Save & Restart**
3. The device restarts and connects to your network
4. The new IP address appears in the serial monitor

!!! tip "Finding the IP after restart"
    ```
    [WiFi] IP: 192.168.1.XX
    ```
    Or check your router's connected devices list.

### AP Fallback

If no SSID is configured, or if the configured network is unreachable, stepSOLAR automatically starts an **Access Point**:

| Parameter | Value |
|-----------|-------|
| SSID | `stepSOLAR` |
| Password | `solar2024` |
| IP address | `192.168.4.1` |

### Connection Status

The WiFi tab shows the current connection state:

```
IP:   192.168.1.44
RSSI: -62 dBm
SSID: MyHomeNetwork
```

---

## Time Tab

### NTP Synchronization

When connected to WiFi, stepSOLAR can synchronize its clock via NTP:

- Sync runs at **boot** (after WiFi connects)
- Re-syncs every **60 minutes**
- Uses ESP32 `configTime()` / `getLocalTime()` functions

### Configuration

| Field | Default | Description |
|-------|---------|-------------|
| Enable NTP | On | Toggle automatic time sync |
| NTP server | `pool.ntp.org` | Any reachable NTP hostname |
| UTC offset (min) | 60 | Timezone offset in minutes. `60` = UTC+1, `120` = UTC+2, `-300` = UTC−5 |
| DST offset (min) | 60 | Summer time adjustment. `60` = +1h, `0` = no DST |

### Common UTC Offsets

| Timezone | UTC offset | DST offset |
|----------|-----------|-----------|
| UTC | 0 | 0 |
| CET (SK, CZ, PL) | 60 | 60 |
| EET (Eastern Europe) | 120 | 60 |
| GMT (UK) | 0 | 60 |
| EST (US Eastern) | −300 | 60 |
| PST (US Pacific) | −480 | 60 |

### Manual Sync

Click **Sync now** to trigger an immediate NTP sync without waiting for the hourly interval. The current time updates in the display below.

!!! note "Offline operation"
    Without WiFi or NTP, the ESP32 internal clock starts from 00:00:00 01.01.1970. Time displays as `--:--:--` until a valid sync is received.
