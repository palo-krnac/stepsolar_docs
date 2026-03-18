# Schematic

!!! info "Work in progress"
    Full KiCad schematic files will be published in a future release. The information below covers the essential connections.

## Minimum Viable Wiring

```
                    ┌─────────────────────────────────────┐
                    │           ESP32-S3-DevKitC-1         │
                    │                                       │
PV Panel ──[Div]── │GPIO──AIN1   GPIO8──SDA──┬── ADS1115  │
ACS712 out ──────── │GPIO──AIN0   GPIO9──SCL──┘   24LC256  │
KTY81+2k2 ──────── │GPIO──AIN2                            │
                    │                                       │
Gate Driver A ───── │GPIO17   GPIO15──TX──MAX485            │
Gate Driver B ───── │GPIO18   GPIO16──RX──MAX485            │
                    │         GPIO14──DE+RE─MAX485          │
Enable relay ─────  │GPIO4                                  │
OC flag ──────────  │GPIO5    GPIO2──LED                   │
OC reset ─────────  │GPIO6                                  │
                    └─────────────────────────────────────┘
```

## Voltage Divider for PV Voltage

```
PV+ ──┤ 470kΩ 1% ├──┬──┤ 5.6kΩ 1% ├── GND
                    │
                  ADS1115 AIN1 (max 4.096V)
```

Use metal film resistors (1% tolerance or better) for accurate voltage measurement.

## Current Sensor

```
PV+ ──────[ACS712-20A]────── Load
              │
          AIN0 (ADS1115)
          Mid = 1.65V @ 0A
```

## Temperature Sensor

```
3.3V ──┤ 2.2kΩ ├──┬── ADS1115 AIN2
                  │
              [KTY81/210]
                  │
                 GND
```
