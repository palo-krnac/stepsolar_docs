# Dual-Core Design

## Why Dual-Core?

The ESP32-S3 has two Xtensa LX7 cores running at 240 MHz. stepSOLAR assigns them specific responsibilities so that WiFi activity **never blocks** the power control loop.

## Core Assignment

| | Core 0 | Core 1 |
|--|--------|--------|
| **Task name** | `Control` | `Comms` |
| **Priority** | 10 (high) | 5 (low) |
| **Stack** | 8 kB | 12 kB |
| **Responsibilities** | ADS1115 sampling, MPPT, PWM, Modbus poll, overcurrent protection | WiFi, AsyncWebServer, WebSocket, NTP, EEPROM writes |
| **Never does** | WiFi/TCP calls, EEPROM write (except VyrobaStore counter) | `ledcWrite()` directly |

!!! warning "WiFi on Core 0"
    The ESP32 WiFi/lwIP stack runs internally on Core 0's **system** task — this is managed by Espressif and cannot be moved. However, our *application* tasks on Core 0 (`taskControl`) avoid any WiFi API calls, preventing priority inversion.

## Synchronization

Three FreeRTOS mutexes protect shared resources:

| Mutex | Protects | Held for |
|-------|----------|----------|
| `xDataMutex` | `sharedData`, `sharedStrida`, `sharedRucne` | < 1 µs (struct copy) |
| `xI2CMutex` | I2C bus (ADS1115 + 24LC256) | Duration of I2C transaction |
| `xCmdMutex` | `pendingCmd` (web → control) | < 1 µs (struct copy) |

## Command Flow (Web → Motor)

```
Browser click "Increase"
    │
    ▼
HTTP POST /api/cmd  (Core 1 handler)
    │
    ▼
webCmdCallback("strida_inc")
    │  LOCK xCmdMutex
    ├─► pendingCmd = CMD_INC
    │  UNLOCK xCmdMutex
    ▼
taskControl next cycle (Core 0)
    │  LOCK xCmdMutex
    ├─► read pendingCmd → CMD_INC
    │   pendingCmd = CMD_NONE
    │  UNLOCK xCmdMutex
    ▼
mppt.incStrida() → ledcWrite()
```

The command is applied within **≤ 10 ms** of the HTTP request.

## Watchdog

Both tasks register with the ESP32 task watchdog (30s timeout):

```cpp
esp_task_wdt_init(30, true);   // setup
esp_task_wdt_add(NULL);        // register in each task
esp_task_wdt_reset();          // pet in each loop iteration
```

During the 15-second overcurrent wait, `esp_task_wdt_reset()` is called inside the delay loop.
