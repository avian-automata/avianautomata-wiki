---
id: parameter-reference
title: Parameter Reference
sidebar_label: Parameter Reference
---

# Parameter Reference

The 305ap uses PX4's standard parameters. This page lists the ones the board sets differently from PX4's defaults, and the ones you are most likely to need. For the complete list with descriptions, see **Vehicle Setup → Parameters** in QGroundControl or the [PX4 parameter reference](https://docs.px4.io/main/en/advanced_config/parameter_reference.html).

Parameters are stored on the SD card (`/fs/microsd/params`). Without a card, changes are lost at reboot.

## Board Defaults

The firmware sets these defaults for the 305ap (`rc.board_defaults`). A parameter reset returns to these values.

| Parameter | 305ap default | Why |
|---|---|---|
| `BAT1_V_DIV` | 11.0 | Battery voltage divider on the board (1/11) |
| `BAT1_A_PER_V` | 0 | No onboard current sensor; set it when you connect one |
| `SYS_HAS_MAG` | 1 | Onboard MMC5983MA compass |
| `PWM_MAIN_TIM0`–`TIM3` | -4 (DShot300) | DShot on all outputs |
| `RC_INPUT_PROTO` | -1 (auto) | Detect the RC protocol on RC IN |
| `CBRK_SUPPLY_CHK` | 894281 | Supply check bypassed: the board has no USB power sensing |
| `MSP_OSD_CONFIG` | TELEM 3 | MSP OSD on the VTX connector |
| `SER_TEL3_BAUD` | 115200 | MSP OSD baud rate |

## Power

| Parameter | Default | Notes |
|---|---|---|
| `BAT1_N_CELLS` | — | Set to your battery's cell count |
| `BAT1_V_DIV` | 11.0 | Calibrate against a multimeter in **Vehicle Setup → Power** |
| `BAT1_A_PER_V` | 0 | **11 ×** the current sensor's A/V (the input has a 1/11 divider) |
| `BAT_V_OFFS_CURR` | 0 | Sensor zero-current output ÷ 11 |

## Outputs

| Parameter | Default | Notes |
|---|---|---|
| `PWM_MAIN_FUNC1`–`8` | 0 (disabled) | Function per output; set in **Actuators** |
| `PWM_MAIN_TIM0`–`TIM3` | DShot300 | Protocol per timer group (MAIN 1–2, 3–4, 5–6, 7–8) |
| `PWM_MAIN_DIS1`–`8` | 1000 µs | PWM value when disarmed |
| `DSHOT_MIN` | 0.055 | Minimum DShot throttle when armed |

See [ESC Configuration](../configuration/esc-configuration).

## Serial Ports

| Parameter | Default | Notes |
|---|---|---|
| `MAV_0_CONFIG` | TELEM 1 | MAVLink on TELEM 1 |
| `SER_TEL1_BAUD` | 57600 | |
| `MAV_0_FLOW_CTRL` | 2 (auto) | `1` force on, `0` force off |
| `MAV_1_CONFIG` | 0 (off) | Set to TELEM 2 for a companion computer or second radio |
| `SER_TEL2_BAUD` | 921600 | |
| `UXRCE_DDS_CFG` | 0 (off) | ROS 2 bridge port |
| `GPS_1_CONFIG` | GPS 1 | |
| `SER_GPS1_BAUD` | 0 (auto) | |
| `GPS_1_PROTOCOL` | 1 (u-blox) | |

See [Telemetry](../configuration/telemetry), [GPS Setup](../configuration/gps-setup) and [Companion Computer](../advanced/companion-computer).

## Sensors

| Parameter | Default | Notes |
|---|---|---|
| `SENS_BOARD_ROT` | 0 | Board mounting rotation |
| `CAL_MAGn_PRIO` | 50 internal, 75 external | Compass priority, set at calibration; `0` disables a compass |
| `CAL_MAGn_ROT` | per sensor | External compass rotation |
| `SENS_EXT_I2C_PRB` | 1 | Probe external I2C buses for compasses at boot |
| `SENS_GNSS_PRIME` | 0 | Preferred GPS: `0` main serial, `1` secondary serial, or a DroneCAN node ID |
| `IMU_GYRO_RATEMAX` | 400 Hz | Rate-loop rate |
| `IMU_GYRO_CUTOFF` | 40 Hz | Gyro low-pass |
| `IMU_DGYRO_CUTOFF` | 20 Hz | D-term low-pass |

See [Sensor Calibration](../configuration/sensor-calibration) and [Filter Tuning](../tuning/filter-tuning).

## DroneCAN

| Parameter | Default | Notes |
|---|---|---|
| `UAVCAN_ENABLE` | 0 (off) | `2` sensors, `3` sensors and ESCs |
| `UAVCAN_NODE_ID` | 1 | Flight controller node ID |
| `UAVCAN_BITRATE` | 1000000 | Bus bitrate |
| `UAVCAN_SUB_GPS` / `UAVCAN_SUB_MAG` | 1 | Other sensor types default to 0 |
| `UAVCAN_ESC_IFACE` | 255 | CAN interfaces carrying ESC commands |
| `UAVCAN_ESC_RTMAX` | 400 Hz | Maximum ESC command rate |

See [DroneCAN](../advanced/dronecan).

## Logging and System

| Parameter | Default | Notes |
|---|---|---|
| `SDLOG_MODE` | 0 | Log from arming to disarming |
| `SDLOG_PROFILE` | Default set | Add profiles for tuning |
| `COM_SAFETY_MODE` | 0 (always off) | Correct for the 305ap, which has no safety switch |
| `MC_AT_EN` | 0 | Enable multicopter autotune |

## Saving and Restoring

- **QGroundControl:** **Parameters → Tools → Save to file / Load from file**.
- **MAVLink Console:** `param save` writes the current values to the SD card; `param reset_all` returns everything to the defaults above.
