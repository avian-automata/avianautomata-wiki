---
id: quickstart
title: Quickstart
sidebar_label: Quickstart
sidebar_position: 1
---

# Quickstart

This page gets you from unboxed board to a flying vehicle as quickly as possible.

## What You Need

- 305ap flight controller
- MicroSD card (Class 10 or faster, 32 GB or smaller recommended)
- USB-C cable
- [QGroundControl](https://qgroundcontrol.com) installed on your PC
- GPS module (connects to USART6 + I2C4)
- RC receiver (see [RC Setup](../configuration/rc-setup))
- ESCs connected to PWM outputs 1–8

## Step 1 — Insert SD Card

The 305ap stores parameters, logs, and mission data on the SD card. Insert the card before powering up for the first time. Parameters **will not persist** across reboots without a card.

## Step 2 — Install Firmware

The 305ap runs PX4 built from the [avian automata PX4 fork](https://github.com/avian-automata/PX4-Autopilot). The board isn't part of upstream PX4, so QGroundControl's built-in **PX4 Flight Stack** download has no firmware for it; install a `.px4` file instead.

Boards ship with the PX4 bootloader and a current firmware release already installed, so a new board can skip to Step 3. Install firmware when updating to a newer release or returning to a release after testing a custom build.

Releases are named `avianautomata_305ap_default_<date>.px4` and are on the [firmware releases page](https://github.com/avian-automata/PX4-Autopilot/releases) on GitHub. See the [Changelog](../reference/changelog) for what each release changes, and [Flashing](../firmware/flashing) for other firmware files and install methods.

1. Unplug the battery and USB, and close any other program using the board.
2. Open QGroundControl and go to **Vehicle Setup → Firmware**.
3. Plug in USB. QGroundControl catches the bootloader during its 5 second window at power-up.
4. Select **PX4 Flight Stack**, tick **Advanced settings**, and choose **Custom firmware file...** from the drop-down.
5. Click **OK** and pick the `.px4` file.
6. Wait for the upload to finish. The board reboots into the new firmware.

:::tip
The bootloader only listens for 5 seconds after power-up, so plugging in USB must be what powers the board. Keep the battery unplugged.
:::

Parameters are kept on the SD card, so updating firmware does not reset your configuration. After a major update, check **Vehicle Setup** for new warnings. **Vehicle Setup → Summary** shows the installed version.

If the board doesn't show up in QGroundControl at all, see [Flashing](../firmware/flashing#recovering-a-blank-or-unresponsive-board).

## Step 3 — Connect GPS

Plug your GPS module into the **GPS** connector. This port provides:
- USART6 (ttyS5) for NMEA/UBX serial data
- I2C4 for compass (if your GPS module includes one)

## Step 4 — Connect RC Receiver

See [RC Setup](../configuration/rc-setup) for receiver wiring options. SBUS and CRSF are both supported.

## Step 5 — QGroundControl Setup

Follow the QGC vehicle setup wizard:

1. **Airframe:** select your vehicle type and frame
2. **Sensors:** calibrate accelerometer, gyro, compass, and level
3. **Radio:** calibrate RC channels
4. **Flight Modes:** assign switches
5. **Power:** configure battery cell count and voltage divider

:::warning No safety switch
The 305ap has no hardware safety switch. PX4 will arm via the RC stick command or GCS arm button only. Ensure props are clear before arming.
:::

## Step 6 — Pre-Flight Check

- [ ] SD card inserted and detected (check QGC status bar)
- [ ] GPS lock acquired (green LED)
- [ ] No red error flags in QGC
- [ ] RC link active and calibrated
- [ ] ESC direction confirmed on bench (no props)

After passing the checklist, the vehicle is ready to fly.
