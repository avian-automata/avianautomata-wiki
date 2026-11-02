---
id: px4-overview
title: PX4 Overview
sidebar_label: PX4 Overview
---

# PX4 Overview

The 305ap runs [PX4](https://px4.io), the open-source autopilot. This page describes how PX4 is set up on the board and what the standard firmware includes. For general PX4 use, the [PX4 User Guide](https://docs.px4.io/main/en/) applies.

## Software Stack

| Layer | Version / detail |
|---|---|
| Autopilot | PX4, tracking the upstream `main` branch (1.18 development line) |
| Operating system | NuttX 12.12 |
| Board target | `avianautomata_305ap` (`default` and `rover` builds) |
| Bootloader | PX4 bootloader, see [Bootloader](bootloader) |

The board support lives in the [avian automata PX4 fork](https://github.com/avian-automata/PX4-Autopilot) on GitHub, under [`boards/avianautomata/305ap`](https://github.com/avian-automata/PX4-Autopilot/tree/main/boards/avianautomata/305ap). Prebuilt firmware and the bootloader are on its [releases page](https://github.com/avian-automata/PX4-Autopilot/releases). Apart from the board files, the fork follows upstream PX4 and is merged with it periodically. See the [Changelog](../reference/changelog).

## What the Default Firmware Includes

### Vehicle Control

- **Multirotor:** rate, attitude and position control, hover thrust estimation, multicopter autotune
- **Fixed-wing:** rate, attitude and lateral/longitudinal control, fixed-wing autotune
- **VTOL:** VTOL attitude control
- Control allocation (actuator mixing configured from QGroundControl), navigator (missions, RTL, geofence), land detector, landing target estimator, gimbal and camera feedback

Ground vehicles use the separate **rover** build, which drops the flying-vehicle controllers and adds the Ackermann, differential and mecanum rover modules plus the RoboClaw driver. See [Rover](../vehicle-types/rover).

### Estimation and Sensors

- EKF2 state estimator, sensor voting across the two IMUs, magnetometer bias estimation, temperature compensation, gyro calibration, gyro FFT (dynamic notch filter tuning)
- Onboard drivers: LSM6DSV and ICM-45686 IMUs, BMP581 barometer, MMC5983MA magnetometer, board ADC (battery voltage and current input)
- External drivers: GPS (u-blox and others), the common magnetometer, distance-sensor and differential-pressure (airspeed) driver sets, MS5611 barometer and PMW3901 optical flow on [External SPI](../advanced/external-spi)

### Outputs and Peripherals

- PWM, OneShot and DShot motor outputs, DShot ESC telemetry
- DroneCAN on both CAN ports (sensors, ESCs, servos)
- RC input (SBUS, CRSF, DSM, PPM and others)
- MSP OSD on the VTX port
- Tone alarm (buzzer) and RGB status LEDs

### Communication

- MAVLink on USB, TELEM 1 and TELEM 2
- uXRCE-DDS client for ROS 2 companion computers

### System Tools

`top`, `perf`, `listener`, `uorb`, `param`, `dmesg`, `ver`, `reboot`, `actuator_test`, `tune_control`, `led_control`, `gpio`, `i2cdetect`, `serial_test`, `hardfault_log` and others are available in the MAVLink Console.

## Flash Space

The default build uses about **94%** of the 1920 KB application flash, leaving roughly 116 KB. Adding large modules can exceed the space. See [px4board Config](px4board-config) for how to trade features.

## Startup Sequence

At boot, PX4 runs its standard startup script (`rcS`), which calls two board-specific scripts:

| Script | Purpose |
|---|---|
| `rc.board_defaults` | Board parameter defaults: battery divider, DShot on motors 1–2, MSP OSD on TEL3, supply-check bypass |
| `rc.board_sensors` | Starts the onboard sensors: BMP581, both IMUs (LSM6DSV, falling back to ICM-45686), MMC5983MA, board ADC, RGB LEDs |

Both scripts are in `boards/avianautomata/305ap/init/` in the firmware source. You can add your own startup commands without rebuilding by placing them on the SD card. See [Custom Firmware](../advanced/custom-firmware).

## Board-Specific Behavior

| Topic | Detail |
|---|---|
| Parameter storage | On the SD card (`/fs/microsd/params`); there is no FRAM |
| Supply check | Disabled (`CBRK_SUPPLY_CHK`) because the board has no USB VBUS sensing |
| Safety switch | None; arming is by RC stick or ground station |
| NuttX console | USART3 TX (VTX SBUS pin) at 57600 baud, output only |
| Crash logs | A hard fault is saved to backup SRAM and written to the SD card on the next boot (`fault_*.log`); see [Logs Analysis](../troubleshooting/logs-analysis) |
