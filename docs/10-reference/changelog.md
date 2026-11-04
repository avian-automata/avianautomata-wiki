---
id: changelog
title: Changelog
sidebar_label: Changelog
---

# Changelog

Firmware releases are named `avianautomata_305ap_default_<date>.px4` and are downloaded from the [firmware releases page](https://github.com/avian-automata/PX4-Autopilot/releases) on GitHub, together with the bootloader. A letter suffix marks a second (or third) release on the same day. Check which build a board runs with `ver all` in the MAVLink Console: the `PX4 git-hash` line matches the commit listed below.

## Firmware

### 20261104

Commit `7c2aa6ee93`

- **Fixed:** boards with the LSM6DSV16X IMU read as upside down when level, because the IMU rotation was wrong. Accelerometer calibration detected the wrong side, and the attitude was inverted. Boards with the ICM-45686 were not affected.

### 20261019b

Commit `7bee2faa81` · PX4 1.18 development line, NuttX 12.12

- **Fixed:** after a hard fault, the board no longer stops at boot waiting at a console prompt it can't answer. It now boots normally and writes the crash log (`fault_*.log`) to the SD card. Previously the board stayed unresponsive until power was removed, and the crash log was lost.

### 20261018b

Commit `02e62d444c`

- **Fixed:** the PMW3901 optical flow sensor now reports its device ID, so it can be told apart from other flow sensors.

### 20261018

Commit `d6919fff40`

- **Added:** MS5611 barometer and PMW3901 optical flow drivers, for sensors on the [External SPI](../advanced/external-spi) connector.
- **Removed:** on-target test commands, to free flash space.

### 20261017c

Commit `2d92898f41`

- **Fixed:** RC input on the RC IN connector (single-wire, without the RX/TX swap).
- **Fixed:** the MAVLink Console shows error messages again (for example "command not found"), which went missing with the NuttX 12.12 update.

### 20261017b

Commit `dfb78c8dd2`

- **Fixed:** both CAN ports. The CAN pins were not connected to the CAN controllers at boot, so DroneCAN did not work.

### 20261017

Commit `23cbe44f59`

- **Updated:** PX4 to the current upstream `main` (1.18 development line) and NuttX 12.12.

### Earlier releases

| Release | PX4 base | Notes |
|---|---|---|
| 20260819 | 1.18 beta (`77211f0b79`) | **Do not use.** MAVLink over USB stops after the first message. |
| 20260429 | 1.17 alpha (`a26f3bd16e`) | |
| 20260426, 20260418 | 1.17 alpha (`73ce5edcb5`) | |

## Bootloader

| Release | Notes |
|---|---|
| 20260429 | Current bootloader. |
| 20260308 | Earlier bootloader. |

Updating the bootloader is not needed for any firmware release listed above. See [Bootloader](../firmware/bootloader).

## Hardware

**v1.1** is the production revision. Each IMU slot is fitted with either an ST LSM6DSV16X or a TDK ICM-45686. The firmware detects which part is fitted at boot, so one firmware supports every board.
