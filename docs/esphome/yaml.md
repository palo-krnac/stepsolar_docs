# ESPHome YAML Reference

## JSON Bridge (Mode 1)

Use this on a **second** ESP device that reads JSON from stepSOLAR UART:

```yaml title="stepsolar_bridge.yaml"
esphome:
  name: stepsolar-bridge

esp32:
  board: esp32dev

logger:
  baud_rate: 0

api:
  encryption:
    key: "your-key-here"

ota:
  password: "your-ota-password"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

uart:
  id: stepsolar_uart
  rx_pin: GPIO16
  baud_rate: 9600

text_sensor:
  - platform: uart
    uart_id: stepsolar_uart
    name: "stepSOLAR Raw"
    id: raw_json
    separator: "\n"
    on_value:
      - lambda: |-
          // Parse JSON and update sensors
          // Requires custom component or lambda parsing

sensor:
  - platform: template
    name: "stepSOLAR Power"
    id: ss_power
    unit_of_measurement: W
    device_class: power
    state_class: measurement

  - platform: template
    name: "stepSOLAR Voltage"
    id: ss_voltage
    unit_of_measurement: V
    device_class: voltage

  - platform: template
    name: "stepSOLAR Current"
    id: ss_current
    unit_of_measurement: A
    device_class: current

  - platform: template
    name: "stepSOLAR Temperature"
    id: ss_temp
    unit_of_measurement: "°C"
    device_class: temperature

  - platform: template
    name: "stepSOLAR Energy"
    id: ss_energy
    unit_of_measurement: kWh
    device_class: energy
    state_class: total_increasing
```

## Direct ESPHome (Mode 2)

Flash this directly to the ESP32-S3 running stepSOLAR hardware:

```yaml title="stepsolar_direct.yaml"
esphome:
  name: stepsolar

esp32:
  board: esp32-s3-devkitc-1
  framework:
    type: arduino

logger:
  baud_rate: 0

api:
  encryption:
    key: "your-key-here"

ota:
  password: "your-ota-password"

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  ap:
    ssid: "stepSOLAR-Fallback"
    password: "solar2024"

i2c:
  sda: GPIO8
  scl: GPIO9
  scan: true

ads1115:
  - address: 0x48
    id: ads1

sensor:
  - platform: ads1115
    ads1115_id: ads1
    multiplexer: 'A1_GND'
    gain: 1.024
    name: "PV Voltage raw"
    id: pv_voltage_raw
    update_interval: 500ms
    internal: true
    filters:
      - multiply: 84.93

  - platform: template
    name: "PV Voltage"
    unit_of_measurement: "V"
    accuracy_decimals: 1
    device_class: voltage
    state_class: measurement
    lambda: 'return id(pv_voltage_raw).state;'
    update_interval: 500ms

  - platform: ads1115
    ads1115_id: ads1
    multiplexer: 'A0_GND'
    gain: 2.048
    name: "PV Current raw"
    id: pv_current_raw
    update_interval: 500ms
    internal: true

  - platform: template
    name: "PV Current"
    unit_of_measurement: "A"
    accuracy_decimals: 2
    device_class: current
    state_class: measurement
    lambda: |-
      float v = id(pv_current_raw).state;
      float a = (v - 1.65) / 0.1;
      return (a < 0) ? 0 : a;
    update_interval: 500ms

  - platform: ads1115
    ads1115_id: ads1
    multiplexer: 'A2_GND'
    gain: 2.048
    name: "Boiler Temp raw"
    id: boiler_temp_raw
    update_interval: 2s
    internal: true

  - platform: template
    name: "Boiler Temperature"
    unit_of_measurement: "°C"
    accuracy_decimals: 0
    device_class: temperature
    state_class: measurement
    lambda: |-
      float vT = id(boiler_temp_raw).state;
      if (vT > 3.3 * 0.98) return -99;
      float R25 = 2000.0, Rref = 2200.0;
      float a = 0.007874 * R25, b = 0.00001874 * R25;
      float c = R25 - Rref * vT / (3.3 - vT);
      float d = a*a - 4*b*c;
      if (d < 0) return 0;
      return (-a + sqrt(d)) / (2*b) + 25.0;
    update_interval: 2s

  - platform: template
    name: "PV Power"
    unit_of_measurement: "W"
    accuracy_decimals: 0
    device_class: power
    state_class: measurement
    lambda: 'return id(pv_voltage_raw).state * id(pv_current_raw).state / 100.0;'
    update_interval: 500ms

  - platform: integration
    name: "Energy Production"
    sensor: pv_power
    time_unit: h
    unit_of_measurement: "Wh"
    state_class: total_increasing

output:
  - platform: ledc
    pin: GPIO17
    id: pwm_a
    frequency: 50Hz

number:
  - platform: template
    name: "Duty Cycle"
    min_value: 0
    max_value: 100
    step: 1
    unit_of_measurement: "%"
    optimistic: true
    set_action:
      - lambda: |-
          id(pwm_a).set_level(x / 100.0);

binary_sensor:
  - platform: gpio
    pin:
      number: GPIO4
      mode: INPUT_PULLUP
      inverted: true
    name: "Inverter Enable"

  - platform: gpio
    pin:
      number: GPIO5
      mode: INPUT
      inverted: true
    name: "Overcurrent Protection"
    on_press:
      - logger.log: "OVERCURRENT triggered!"
```
