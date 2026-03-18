# Monitor Tab

The Monitor tab shows all live measurements updated every 500 ms via WebSocket.

## Power Card

Displays instant power in large format with sub-readings for voltage and current:

| Value | Source | Notes |
|-------|--------|-------|
| Power (W) | `V × I` | Calculated from ADS1115 readings |
| PV Voltage (V) | ADS1115 AIN1 | After divider correction |
| PV Current (A) | ADS1115 AIN0 | From current sensor, shown as X.XX A |

## Duty Cycle Card

Shows the current PWM duty cycle as a percentage with a progress bar:

- **0%** = PWM off (strida = 0)
- **100%** = maximum strida (strida = 247)
- Formula: `pct = round(strida / 2.47)`
- Mode pill shows: `AUTO MPPT` or `MANUAL MODE`

## Temperature Card

Boiler temperature from KTY81/210 sensor with a color-gradient bar:

- **Green** zone: normal operation
- **Amber** zone: approaching limit (within 10°C)
- **Red** zone: at or above limit — inverter stops

If the sensor is disconnected, displays `--`.

## Power History Chart

A 60-point canvas chart showing the last 60 seconds of power output:

- Updates on every telemetry packet (500 ms interval)
- Y-axis auto-scales to peak value (minimum 100W scale)
- Grid lines at 25%, 50%, 75%
- Current value label in top-right corner

## Energy Card

Cumulative energy production loaded from EEPROM wear-leveling store:

- Displayed in **kWh** with 2 decimal places
- Updates in real-time as Wh increments
- Maximum stored value: 99,999.99 kWh

## Status Chips

| Chip | Green (OK) | Amber (Warning) | Red (Error) |
|------|-----------|-----------------|-------------|
| Enable | ON | — | OFF |
| Overcurrent | OK | — | FAULT! |
| Modbus | ID shown | — | — |
| Temperature | Normal | Within 10°C of limit | — |
| NTP | Synced + time | Not synced | — |

## Maximum Values

Peak values recorded since last restart (or manual reset):

- **Max V** — highest PV voltage seen
- **Max A** — highest current seen (×100 format, shown as X.XX A)
- **Max W** — highest power seen

Persistent maximums are saved to EEPROM every 5 minutes.
