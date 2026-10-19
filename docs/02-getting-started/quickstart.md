---
id: quickstart
title: Quickstart
sidebar_label: Quickstart
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

## Step 2 — Flash Firmware

Boards ship with firmware installed, so a new board can skip this step. To update, see [Firmware Install](firmware-install). The quick version:

1. Open QGroundControl → **Vehicle Setup → Firmware**, then plug in USB (battery disconnected)
2. Select **PX4 Flight Stack** → **Advanced settings** → **Custom firmware file...**
3. Choose the `.px4` release file

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
