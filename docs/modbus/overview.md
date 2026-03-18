# Modbus RTU Overview

stepSOLAR implements a **Modbus RTU slave** over RS485. It supports Function Codes 0x03 (Read Holding Registers), 0x04 (Read Input Registers) and 0x06 (Write Single Register).

## Enabling Modbus

In the **Settings** tab, set **Communication** to `1 (Modbus RTU)`. The RS485 transceiver is activated and the device begins responding to Modbus requests.

## Frame Timing

The Modbus frame gap detection uses a **5 ms inter-character timeout** — frames arriving with gaps smaller than 5 ms are treated as a single frame.

## Error Handling

| Error Code | Meaning |
|-----------|---------|
| `0x02` | Illegal Data Address — register out of range |

## Supported Function Codes

| FC | Name | Description |
|----|------|-------------|
| `0x03` | Read Holding Registers | Read 1–20 registers |
| `0x04` | Read Input Registers | Same as FC03 |
| `0x06` | Write Single Register | Not used in normal operation |

## Testing with Modbus Poll

1. Connect a USB-RS485 adapter to your PC
2. Open [Modbus Poll](https://www.modbuspoll.com/) or similar tool
3. Set the same baud/parity/stop as configured in stepSOLAR
4. Slave address = `modbusSlave` (default: **12**)
5. Read registers starting at `0x0004` (40005 in Modbus addressing)
