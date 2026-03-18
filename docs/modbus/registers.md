# Modbus Register Map

Slave address: configurable (default **12**)

!!! info "Addressing convention"
    Registers are listed as **0-based** addresses (as sent in the Modbus frame). Add 40001 for the Modbus Poll / SCADA "holding register" display address.

## Holding Registers (FC03 / FC04)

| Address | MB Address | Name | Type | Unit | Description |
|---------|-----------|------|------|------|-------------|
| `0x0000` | 40001 | `nodeID` | uint16 | — | Slave ID |
| `0x0001` | 40002 | reserved | — | — | |
| `0x0002` | 40003 | reserved | — | — | |
| `0x0003` | 40004 | reserved | — | — | |
| `0x0004` | 40005 | `napeti` | uint16 | V | PV voltage |
| `0x0005` | 40006 | `proud` | uint16 | ×100 A | PV current (e.g. 856 = 8.56 A) |
| `0x0006` | 40007 | `vykon` | uint16 | W | Instant power |
| `0x0007` | 40008 | `teplota` | uint16 | °C | Boiler temperature (`0xFFFF` = sensor disconnected) |
| `0x0008` | 40009 | reserved | — | — | |
| `0x0009` | 40010 | reserved | — | — | |
| `0x000A` | 40011 | reserved | — | — | |
| `0x000B` | 40012 | reserved | — | — | |
| `0x000C` | 40013 | `vyroba_lo` | uint16 | Wh | Total energy production — low word |
| `0x000D` | 40014 | `vyroba_hi` | uint16 | Wh | Total energy production — high word |
| `0x000E–0x0013` | 40015–40020 | reserved | — | — | |

## Reconstructing Total Energy

The 32-bit energy value is split across two 16-bit registers:

```
energy_wh = (vyroba_hi << 16) | vyroba_lo
energy_kwh = energy_wh / 1000.0
```

**Example:** `vyroba_hi = 0x0001`, `vyroba_lo = 0xE240`  
→ `0x0001E240 = 123456 Wh = 123.456 kWh`

## Home Assistant Modbus Configuration

```yaml title="configuration.yaml"
modbus:
  - name: stepsolar
    type: serial
    port: /dev/ttyUSB0
    baudrate: 9600
    bytesize: 8
    parity: N
    stopbits: 1
    method: rtu

sensor:
  - platform: modbus
    name: "stepSOLAR Voltage"
    hub: stepsolar
    slave: 12
    address: 4
    unit_of_measurement: "V"
    device_class: voltage
    state_class: measurement

  - platform: modbus
    name: "stepSOLAR Current"
    hub: stepsolar
    slave: 12
    address: 5
    unit_of_measurement: "A"
    device_class: current
    state_class: measurement
    scale: 0.01

  - platform: modbus
    name: "stepSOLAR Power"
    hub: stepsolar
    slave: 12
    address: 6
    unit_of_measurement: "W"
    device_class: power
    state_class: measurement

  - platform: modbus
    name: "stepSOLAR Temperature"
    hub: stepsolar
    slave: 12
    address: 7
    unit_of_measurement: "°C"
    device_class: temperature
    state_class: measurement
```
