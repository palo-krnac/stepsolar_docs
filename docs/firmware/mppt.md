# MPPT Algorithm

## Overview

stepSOLAR implements the **Perturb and Observe (P&O)** hill-climbing MPPT algorithm. It continuously adjusts the PWM duty cycle to find the maximum power point on the PV panel's I-V curve.

## P&O Logic

```
       Power
         │     MPP
         │    ╱ ╲
         │   ╱   ╲
         │  ╱     ╲
         │ ╱       ╲
         │╱         ╲
         └────────────── Duty cycle
              ↑
         Current operating point
```

Each control cycle:

1. Measure current power `W = V × I`
2. Compare with previous cycle power `W_prev`
3. Adjust duty cycle based on direction and result:

```
if direction == UP:
    if W >= W_prev:  duty += 4   (still climbing → continue)
    else:            direction = DOWN
if direction == DOWN:
    if W >= W_prev:  duty -= 3   (still climbing from below → continue)
    else:            direction = UP
```

Step sizes (+4 / −3) are asymmetric for stability — slower descent avoids oscillation around the MPP.

## Power Limits

| Condition | Action |
|-----------|--------|
| `W == 0` | Reset duty to 24, direction = UP |
| `W >= vykonMax` | Force duty down by 4, direction = DOWN |
| `nadproud` (I ≥ 10A) | Force duty down by 25 |

## VA Curve Scan

Periodically (configurable via `timeTestMPPT`), the algorithm performs a full sweep of the I-V curve to escape local maxima caused by partial shading:

```
for duty = 10 to 247 (step 5):
    apply PWM
    wait 10 ms
    measure power
    if power > best_power:
        best_power = power
        best_duty  = duty
set duty = best_duty
```

The scan only runs when:

- VA test is enabled (`onVA = 1`)
- Current duty is between 50 and 185 (avoids extremes)
- Not in manual mode
- Configured interval has elapsed

## Manual Mode

When a user adjusts duty cycle via the web UI, the controller enters **manual mode** for 10 seconds, suspending MPPT. After the timeout, it automatically returns to automatic P&O control.

## PWM Output

```cpp
// Map duty (0–247) to 11-bit LEDC (0–2047)
uint32_t dA = map(duty, 0, 247, 0, 2047);
uint32_t dB = 2047 - dA;   // complementary

// Apply deadtime (4 ticks ≈ 2 ms at 50Hz/11bit)
dA -= 4; dB -= 4;

ledcWrite(CHANNEL_A, dA);
ledcWrite(CHANNEL_B, dB);
```
