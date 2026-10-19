---
id: qgc-setup
title: QGroundControl Setup
sidebar_label: QGroundControl Setup
---

# QGroundControl Setup

[QGroundControl](https://qgroundcontrol.com) (QGC) is the recommended ground station application for configuring and operating the 305ap.

## Connecting

Connect the board via USB. QGC will detect it automatically and show a green connected indicator in the toolbar. If it does not connect:

- Ensure firmware is flashed (see [Flashing](../firmware/flashing))
- Check that the USB cable supports data (not charge-only)
- On Linux, add your user to the `dialout` group: `sudo usermod -aG dialout $USER`

## Vehicle Setup Wizard

After connecting for the first time, complete the setup wizard in order:

### 1. Airframe

Select your vehicle type and the closest matching airframe. For a custom build, choose the generic frame for your vehicle type (e.g. **Generic Quadcopter X** for a quad).

Reboot after changing airframe. This resets PID gains and mixer to defaults for that frame.

### 2. Sensors

Calibrate all sensors before flying. Follow the on-screen instructions:

| Calibration | Notes |
|---|---|
| Accelerometer | Hold the board in each of 6 orientations as prompted |
| Gyroscope | Keep the board completely still on a flat surface |
| Compass | Rotate the board in all axes as prompted |
| Level Horizon | Place the board level, then calibrate |

:::note IMU orientation
Both IMU slots are mounted flat on the top of the board with the same orientation, regardless of which part is populated (LSM6DSV or ICM-45686). No rotation offset is needed if the board is mounted flat and forward-facing. Forward is the edge with the power connector; the SD card is at the rear.
:::

### 3. Radio

With your RC receiver connected and bound to your transmitter:

1. Click **Calibrate** and follow the prompts
2. Move all sticks and switches to their extremes
3. Confirm channel assignments (throttle, roll, pitch, yaw)

### 4. Flight Modes

Assign your transmitter switches to flight modes. At minimum configure:
- **Manual / Stabilized** for direct control
- **Position** for GPS-assisted flight (requires GPS lock)
- **Return** for RTL failsafe

### 5. Power

Configure battery monitoring:

| Parameter | Value for 305ap |
|---|---|
| Number of cells | Match your battery |
| Full voltage (per cell) | 4.20 V (LiPo) |
| Empty voltage (per cell) | 3.50 V (LiPo) |
| Voltage divider | Calibrate against a multimeter reading |
| Amps per volt | **11 ×** your current sensor's A/V (the input has a 1/11 divider; see [ESC Configuration](esc-configuration#current-sensing)) |

:::note No onboard current shunt
The 305ap requires an external current sensor. If no sensor is connected, current and consumed mAh will not be reported.
:::

## Key Parameters

Access parameters via **Vehicle Setup → Parameters**. Useful ones to know:

| Parameter | Description |
|---|---|
| `MAV_0_CONFIG` | Port for MAVLink instance 0 (Telem 1 by default) |
| `MAV_1_CONFIG` | Port for MAVLink instance 1 (off by default; e.g. Telem 2) |
| `MAV_0_FLOW_CTRL` | Hardware flow control for MAVLink instance 0 |
| `COM_SAFETY_MODE` | Safety switch behavior; the default (always off) is correct for the 305ap, which has no safety switch |
| `EKF2_MAG_TYPE` | Magnetometer fusion mode |
| `PWM_MAIN_TIM0`–`TIM3` | Output protocol (DShot, PWM) per timer group |
