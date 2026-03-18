# Control Tab

## Manual Duty Cycle

Use the **−** and **+** buttons to manually adjust the duty cycle (strieda) in 1% steps.

- Entering manual mode pauses MPPT for **10 seconds**
- After 10 seconds of no button presses, MPPT resumes automatically
- The **AUTO MPPT** button immediately returns to automatic control

!!! tip
    Manual control is useful for testing gate drivers or verifying wiring before enabling full MPPT operation.

## VA Test

Clicking **VA Test** triggers an immediate full sweep of the I-V curve, regardless of the configured interval. The sweep takes a few seconds and finds the global maximum power point.

## Set Energy (Wh)

If you replace the device or reset EEPROM, you can restore the previous energy reading:

1. Enter the value in **Wh** (whole number, e.g. `1234560` for 1234.56 kWh)
2. Click **Save**
3. The value is immediately written to EEPROM wear-leveling store

## Reset Maximums

Clears the recorded peak values (Max V, Max A, Max W) from both RAM and EEPROM.
