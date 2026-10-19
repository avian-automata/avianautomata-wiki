---
id: px4board-config
title: px4board Config
sidebar_label: px4board Config
---

# px4board Config

PX4 selects the drivers and modules in a build with **Kconfig**. Each build target has a `.px4board` file listing its options. For the 305ap these are in `boards/avianautomata/305ap/` in the firmware source:

| File | Build target | Purpose |
|---|---|---|
| `default.px4board` | `avianautomata_305ap_default` | Standard firmware: multirotor, fixed-wing, VTOL |
| `rover.px4board` | `avianautomata_305ap_rover` | Ground vehicles; applied on top of `default.px4board` |
| `bootloader.px4board` | `avianautomata_305ap_bootloader` | PX4 bootloader |

## Serial Port Names

The board file maps NuttX serial devices to the PX4 port names used by the `*_CONFIG` parameters in QGroundControl:

| Kconfig option | Device | PX4 port name | Connector |
|---|---|---|---|
| `CONFIG_BOARD_SERIAL_GPS1` | `/dev/ttyS5` | GPS 1 | GPS (CN4) |
| `CONFIG_BOARD_SERIAL_TEL1` | `/dev/ttyS3` | TELEM 1 | TELEM 1 (CN2) |
| `CONFIG_BOARD_SERIAL_TEL2` | `/dev/ttyS6` | TELEM 2 | TELEM 2 (CN3) |
| `CONFIG_BOARD_SERIAL_TEL3` | `/dev/ttyS1` | TELEM 3 | VTX (CN6) |
| `CONFIG_BOARD_SERIAL_RC` | `/dev/ttyS4` | RC | RC IN (U22) |

The motor telemetry UART (`/dev/ttyS0`) and the console UART (`/dev/ttyS2`) have no port name, so they can't be chosen from QGroundControl. ESC telemetry on `/dev/ttyS0` is started with a startup command instead (see [ESC Configuration](../configuration/esc-configuration)).

## Other Board Options

| Option | Value | Meaning |
|---|---|---|
| `CONFIG_BOARD_PARAM_FILE` | `/fs/microsd/params` | Parameters live on the SD card |
| `CONFIG_BOARD_UAVCAN_INTERFACES` | `2` | DroneCAN on both CAN ports |
| `CONFIG_NUM_MISSION_ITEMS_SUPPORTED` | `1000` | Mission size |

## Drivers and Modules in the Default Build

| Area | Options |
|---|---|
| Onboard sensors | `DRIVERS_IMU_ST_LSM6DSV`, `DRIVERS_IMU_INVENSENSE_ICM45686`, `DRIVERS_BAROMETER_BMP581`, `COMMON_MAGNETOMETER` (includes MMC5983MA), `DRIVERS_ADC_BOARD_ADC` |
| External sensors | `DRIVERS_GPS`, `DRIVERS_BAROMETER_MS5611`, `DRIVERS_OPTICAL_FLOW_PMW3901`, `COMMON_DISTANCE_SENSOR`, `COMMON_DIFFERENTIAL_PRESSURE` |
| Outputs | `DRIVERS_PWM_OUT`, `DRIVERS_DSHOT`, `DRIVERS_UAVCAN`, `DRIVERS_TONE_ALARM` |
| Inputs and links | `COMMON_RC`, `DRIVERS_RC_INPUT`, `COMMON_TELEMETRY`, `DRIVERS_CDCACM_AUTOSTART`, `DRIVERS_OSD_MSP_OSD` |
| Control | `MODULES_MC_*`, `MODULES_FW_*`, `MODULES_VTOL_ATT_CONTROL`, `MODULES_CONTROL_ALLOCATOR`, `MODULES_FLIGHT_MODE_MANAGER`, `MODULES_NAVIGATOR` |
| Estimation | `MODULES_EKF2`, `MODULES_SENSORS`, `MODULES_MAG_BIAS_ESTIMATOR`, `MODULES_TEMPERATURE_COMPENSATION`, `MODULES_GYRO_FFT` |
| Companion | `MODULES_MAVLINK`, `MODULES_UXRCE_DDS_CLIENT` |

The full list is in `default.px4board`.

## Changing the Configuration

Open the Kconfig menu for a target:

```bash
make avianautomata_305ap_default boardconfig
```

Saving writes the changes back to `default.px4board`. You can also edit the file directly: add a line such as `CONFIG_DRIVERS_DISTANCE_SENSOR_LIGHTWARE_LASER_SERIAL=y`, or set an option to `n` to remove it.

Rebuild after any change:

```bash
make avianautomata_305ap_default
```

## Flash Budget

The application flash is 1920 KB. The default build uses about 94%, leaving roughly 116 KB. The build prints a memory summary at the end:

```
           FLASH:     1849668 B      1920 KB     94.08%
```

If a build no longer fits, the link step fails with a region overflow error. To make room, remove features you don't use. Good candidates, depending on the vehicle:

| Option | Remove if |
|---|---|
| `MODULES_VTOL_ATT_CONTROL` | Not flying VTOL |
| `MODULES_FW_*` | Multirotor only |
| `MODULES_MC_*` | Fixed-wing only |
| `MODULES_UXRCE_DDS_CLIENT` | No ROS 2 companion |
| `MODULES_GIMBAL`, `MODULES_CAMERA_FEEDBACK` | No gimbal or camera triggering |

Compare the `FLASH` line before and after to see what each change saves.

The rover build already removes the flying-vehicle controllers, which is how it fits the rover modules.

:::note
Keep the board ID, the serial port mapping and `CONFIG_BOARD_PARAM_FILE` unchanged. Changing the board ID stops QGroundControl from accepting the firmware on this board.
:::
