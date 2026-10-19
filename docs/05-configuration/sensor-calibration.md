---
id: sensor-calibration
title: Sensor Calibration
sidebar_label: Sensor Calibration
---

# Sensor Calibration

Calibrate the sensors in QGroundControl under **Vehicle Setup → Sensors** before the first flight. Calibrate with the flight controller installed in the vehicle, so the results include the final mounting.

## Board Orientation

The firmware already applies the correct rotation for each onboard sensor. If the board is mounted flat and forward-facing, no setting is needed. Forward is the edge with the power connector; the SD card is at the rear.

If the board is mounted rotated or upside down, set **Autopilot Orientation** in the Sensors page before calibrating (`SENS_BOARD_ROT`). Do not change the per-sensor rotations of the onboard sensors.

## Accelerometer

1. Click **Accelerometer** and follow the prompts.
2. Place the vehicle still in each of the six orientations shown (level, on each side, nose up, nose down, upside down).

Both IMUs are calibrated together. Repeat after a crash or after re-mounting the board.

## Gyroscope

Click **Gyroscope** with the vehicle completely still. It takes a few seconds. PX4 also re-estimates gyro bias at every boot while the vehicle is still, so this rarely needs repeating.

## Compass

1. Click **Compass** and rotate the vehicle through each orientation shown until every side is complete.
2. Do it outdoors or away from steel structures, cars and power cables, with the battery connected and your usual peripherals powered.

The onboard MMC5983MA and any external compass (for example in a GPS module) are calibrated at the same time.

Calibration removes the fixed magnetic offsets of the vehicle: magnetised parts, and steady currents on the board. Before calibration the onboard compass typically reads 2–3 times the Earth's field because of these offsets. That is normal and is what calibration corrects.

### Choosing the Primary Compass

With more than one compass, the Sensors page lists each one with a priority. Set the external compass higher, or switch a compass off, there (`CAL_MAGn_PRIO`; `0` disables it).

:::note 5 V loads and the onboard compass
Current drawn from the board's 5 V rail affects the onboard compass. Steady loads are removed by calibration, so calibrate with them powered. A 5 V load that varies substantially in flight can still throw off the onboard compass; in complex systems with highly variable 5 V loads, make an external GPS + compass the primary compass. See [Magnetometer](../hardware/magnetometer#magnetic-interference).
:::

## Level Horizon

Place the vehicle on a level surface in its normal flying attitude and click **Level Horizon**. This removes small mounting tilts so the vehicle hovers without drifting.

## Barometer

The BMP581 needs no calibration. Altitude is relative to the take-off point.

## Airspeed (Fixed-Wing)

With an airspeed sensor connected, run **Airspeed** calibration with the pitot tube covered from wind, then blow into it when prompted.

## Temperature Calibration (Optional)

IMU and barometer offsets drift with temperature. For vehicles that fly across a wide temperature range, PX4 can learn and correct this drift (`TC_A_ENABLE`, `TC_G_ENABLE`, `TC_B_ENABLE`). It needs the board to warm up slowly from cold, and is only worth doing for demanding applications. See the [PX4 thermal calibration guide](https://docs.px4.io/main/en/advanced_config/sensor_thermal_calibration.html).

## Checking the Result

- The **Fly** view horizon follows the vehicle's tilt in the right direction.
- The heading matches a phone compass within a few degrees, also with the motors' power wiring connected.
- No "Accel", "Gyro" or "Compass" warnings in the QGroundControl status bar.
