---
id: imu-sensors
title: IMU Sensors
sidebar_label: IMU Sensors
---

# IMU Sensors

The 305ap has two 6-axis IMUs (accelerometer + gyroscope) mounted on the top of the board. Each IMU slot supports two pin-compatible parts — the firmware probes at boot and starts whichever part is populated.

| | IMU 1 | IMU 2 |
|---|---|---|
| Primary part | LSM6DSV (ST) | LSM6DSV (ST) |
| Fallback part | ICM-45686 (InvenSense) | ICM-45686 (InvenSense) |
| Bus | SPI2 | SPI3 |

## Hardware

| Signal | IMU 1 | IMU 2 |
|---|---|---|
| CS | PE4 | PA14 |
| SCLK | PD3 | PB3 |
| MOSI | PC1 | PB2 |
| MISO | PC2\_C | PB4 |
| DRDY (INT1) | PE0 | PC15 |
| INT2 | PD4 | PA13 |

The LSM6DSV and ICM-45686 use the same footprint and share the same CS, DRDY, and SPI bus lines on each slot. No hardware rework is needed to swap between parts.

## Auto-detection

At boot, the firmware tries to start the LSM6DSV on each bus first. If the WHO\_AM\_I read fails (part not populated), it falls back to the ICM-45686. This happens independently for each IMU slot, so mixed builds (one slot each) are supported.

To confirm which parts were detected, check the MAVLink console after boot:

```
lsm6dsv status
icm45686 status
```

Or watch the boot log — each driver prints its device name and bus on successful start.

## Orientation

Both IMU slots have the same physical orientation, whichever part is fitted, and the firmware applies the same rotation to both parts: `YAW_90` (driver option `-R 2`).

No setting is needed for the onboard IMUs. Forward is the edge with the power connector; the SD card is at the rear. If the board is mounted at a different angle in the frame, set the board rotation instead: **`SENS_BOARD_ROT`** (QGroundControl: **Vehicle Setup → Sensors → Autopilot Orientation**). It applies to all onboard sensors.

## Redundancy

PX4 runs sensor voting across both IMUs. If one IMU reports data that diverges significantly from the other, PX4 will flag it and fall back to the healthy sensor. This is logged in flight logs as an IMU voting event.

## Checking IMU Data in QGroundControl

To verify both IMUs are reporting:

1. Open QGC → **Analyze Tools → MAVLink Inspector**
2. Look for `RAW_IMU` and `SCALED_IMU2` messages
3. Both should show non-zero gyro and accelerometer values

Or in the MAVLink Console:

```
listener sensor_accel
listener sensor_gyro
```

Multiple instances (`instance 0` and `instance 1`) confirm both IMUs are active.

## Sensor Specifications

### ST LSM6DSV (primary)

| Specification | Value |
|---|---|
| Axes | 3-axis accelerometer + 3-axis gyroscope |
| Host interface | SPI, I2C, I3C |
| FIFO | Up to 4 KB |
| Interrupts | 2 programmable interrupt pins (INT1 / INT2) |
| Accelerometer range | ±2 / ±4 / ±8 / ±16 g |
| Gyroscope range | ±125 / ±250 / ±500 / ±1000 / ±2000 / ±4000 dps |

### TDK InvenSense ICM-45686 (fallback)

| Specification | Value |
|---|---|
| Axes | 3-axis accelerometer + 3-axis gyroscope |
| Host interface | SPI (primary), I2C, I3C |
| FIFO | Up to 8 KB |
| Interrupts | 2 programmable interrupt pins (INT1 / INT2) |
| Accelerometer range | Configurable |
| Gyroscope range | Configurable |

Both IMUs are powered from the quiet 3.3 V LDO rail (RT9193), isolated from the main digital 3.3 V switcher via a ferrite bead.

## Vibration

Use soft-mount standoffs between the FC and the frame for best results. Review vibration levels post-flight in **QGC → Analyze → Log Files → Vibration**.
