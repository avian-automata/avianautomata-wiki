---
id: led-status
title: LED Status
sidebar_label: LED Status
---

# LED Status

The 305ap has three LEDs (red, green, and blue), all active-low (GPIO LOW = on).

| Color | GPIO | Pin |
|---|---|---|
| Red | PD9 | — |
| Green | PB12 | — |
| Blue | PE12 | — |

PX4 controls these LEDs to communicate system state. The patterns below reflect standard PX4 LED behavior.

## Patterns

### Boot Sequence

| Pattern | Meaning |
|---|---|
| Blue blink (first 5 s after power-up) | Bootloader active, waiting for firmware upload |
| Green solid (during an upload) | Bootloader writing firmware |
| All LEDs on briefly | NuttX initializing |
| Blue slow blink | PX4 starting up, sensors initializing |

### Normal Operation

| Pattern | Meaning |
|---|---|
| Blue breathing (slow pulse) | Disarmed, no GPS |
| Green solid | GPS lock acquired, ready to arm |
| Green + Blue | Disarmed, GPS lock, ready |
| Green fast blink | Armed |
| Red + Blue alternating | Error condition — check QGC for details |
| Red solid | Critical error |

### Arming States

| LED state | Meaning |
|---|---|
| Green slow blink | Pre-arm checks passing, waiting for arm command |
| Green fast blink | **Armed** — props may be spinning |
| Red blink | Pre-arm check failing |

## Diagnosing Errors

If the red LED is blinking, connect to QGroundControl and check:

1. **Status bar:** error flags appear at the top
2. **MAVLink Console → `commander status`:** shows what is blocking arming
3. **MAVLink Console → `listener vehicle_status`:** detailed system state

## No LEDs at All

If no LEDs illuminate after power-on:

- Verify power input voltage is within spec
- Check the SD card is inserted (missing SD card does not prevent boot but triggers an error)
- Try re-flashing firmware via DFU mode
