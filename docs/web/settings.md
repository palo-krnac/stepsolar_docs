# Settings Tab

All settings are saved to **24LC256 EEPROM** immediately on clicking **Save settings**.

## Parameters

### Inverter Limits

| Field | Default | Description |
|-------|---------|-------------|
| Max boiler temp (°C) | 90 | Inverter shuts down above this temperature |
| Max power (W) | 2800 | MPPT clamps output to this limit |
| VA test period (min) | 5 | How often the full I-V curve scan runs. Set to `0` to disable |

### Calibration

Both offsets are applied as a percentage of the measured value:

```
corrected = measured + (measured / 100) × (offset / 10)
```

| Field | Range | Example |
|-------|-------|---------|
| V calibration | −5.0% to +5.0% | `+10` = +1.0% |
| A calibration | −5.0% to +5.0% | `−5` = −0.5% |

### Communication

| Field | Options | Description |
|-------|---------|-------------|
| Comm type | 0 = JSON, 1 = Modbus | Selects RS485 protocol |
| Modbus Slave ID | 1–247 | Modbus device address |
| RS485 Baud rate | 1200–115200 | Serial speed |
| RS485 Parity | None / Even / Odd | |
| RS485 Stop bits | 1 / 2 | |

!!! note "Live RS485 restart"
    Changing any RS485 parameter restarts the UART immediately — no device reboot needed.

### Interface

| Field | Options |
|-------|---------|
| Language | English / Slovenčina / Polski / Čeština |

Language change takes effect immediately and is synced to all open browser tabs.

## Factory Reset

!!! danger "Destructive action"
    Factory reset erases **all settings and the total energy counter** from EEPROM. The device restarts and boots with default values.

    A confirmation dialog is shown before the reset executes.
