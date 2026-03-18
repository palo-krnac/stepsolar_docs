# Configuration Reference

All runtime parameters are stored in EEPROM and configurable via the web interface. Compile-time constants are in `include/config.h`.

## Runtime Parameters (Web UI → EEPROM)

| Parameter | Default | Range | Description |
|-----------|---------|-------|-------------|
| `teplotaMax` | 90 | 40–90 °C | Boiler temperature limit — inverter stops above this |
| `vykonMax` | 2800 | 1000–2800 W | Maximum power limit |
| `timeTestMPPT` | 5 | 0–60 min | VA curve scan interval (0 = disabled) |
| `kalibV` | 0 | −50 to +50 | Voltage calibration in 0.1% steps |
| `kalibA` | 0 | −50 to +50 | Current calibration in 0.1% steps |
| `KomTyp` | 1 | 0 or 1 | Communication: 0 = JSON UART, 1 = Modbus RTU |
| `modbusSlave` | 12 | 1–247 | Modbus slave address |
| `modbusBaud` | 9600 | 1200–115200 | RS485 baud rate |
| `modbusParity` | 0 | 0/1/2 | 0=None, 1=Even, 2=Odd |
| `modbusStop` | 0 | 0 or 1 | 0=1 stop bit, 1=2 stop bits |
| `language` | 1 | 0–3 | 0=EN, 1=SK, 2=PL, 3=CS |
| `tzOffset` | 60 | −720 to +840 min | UTC timezone offset in minutes |
| `tzDst` | 60 | 0–120 min | DST offset in minutes |
| `ntpEnabled` | 1 | 0 or 1 | Enable automatic NTP time sync |
| `ntpServer` | `pool.ntp.org` | string | NTP server hostname |

## Compile-Time Constants (`config.h`)

```cpp
// GPIO
#define I2C_SDA       8
#define I2C_SCL       9
#define PWM_PIN_A     17
#define PWM_PIN_B     18
#define RS485_RX      16
#define RS485_TX      15
#define RS485_DE_RE   14
#define ENABLE_PIN    4
#define OCHRANA_PIN   5
#define OCHRANA_RESET 6
#define RUN_LED       2

// PWM
#define PWM_FREQ       50    // Hz
#define PWM_RESOLUTION 11    // bits (2048 steps)
#define PWM_MAX_DUTY   247
#define PWM_MIN_DUTY   8

// Voltage divider
#define R1  470000.0f        // Ω upper resistor
#define R2    5600.0f        // Ω lower resistor

// Current sensor
#define MAX_CURRENT    1000  // ×100 = 10.00A overcurrent threshold
```
