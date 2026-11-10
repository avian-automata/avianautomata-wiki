---
id: board-layout
title: Board Layout
sidebar_label: Board Layout
---

# Board Layout

## Form Factor

The 305ap is a **36.5 mm × 36.5 mm** board with **M4 mounting holes on a 30.5 mm × 30.5 mm pattern** designed around soft-mount vibration isolation. Each mounting hole accepts a standard M3 rubber vibration isolator (M4 outer diameter, M3 inner bore) so the FC floats on rubber rather than bolting rigidly to the frame. Weight is **11 g with microSD card installed**.

## Connector Placement

The 305ap connector layout is designed for clean wire routing in a typical FPV stack:

| Connector | Location | Function |
|---|---|---|
| USB-C | Side edge | USB 2.0 FS programming and GCS connection |
| Motors | Bottom | 8× PWM/DSHOT outputs + ESC telemetry + current sense |
| TELEM1 | Side/top | 6-pin JST-GH, UART4 with CTS/RTS |
| TELEM2 | Side/top | 6-pin JST-GH, UART7 with CTS/RTS |
| GPS | Side | 6-pin JST-GH, USART6 + I2C4 |
| CAN1 | Side | 4-pin JST-GH, FDCAN1 |
| CAN2 | Side | 4-pin JST-GH, FDCAN2 |
| RC IN | Side | 3-pin JST-GH, single-wire (SBUS/CRSF) |
| VTX | Side | 6-pin JST-GH, 9 V + UART2 + SBUS out |
| External I2C | Side | 4-pin JST-GH |
| External SPI | Side | 7-pin JST-GH |
| microSD | Bottom | Push-push slot, card detect supported |

## Top Side

- STM32H743VIH6 MCU (TFBGA100)
- 2× IMU slots (SPI2, SPI3) — ST LSM6DSV or TDK InvenSense ICM-45686, auto-detected at boot
- BMP581 barometer (SPI)
- MMC5983MA magnetometer (I2C, internal)
- SIT1044T CAN transceivers
- Status LEDs (red, green, blue)
- BOOT and RESET buttons

## Bottom Side

- Power components (AP63357 buck converters, LM74700-Q1, TPS2116, RT9193 LDO)
- RGB LED
- Motor output connector (U21)
- Voltage/current sense frontend (TLV9002)
- SMAJ30CA TVS, AON7246E FET
- microSD slot

## Mounting

The 305ap uses a **30.5 mm × 30.5 mm** mounting pattern with **M4 mounting holes** sized for standard M3 rubber vibration isolators. Each hole accepts one isolator (M4 OD × M3 ID) that the mounting M3 fastener passes through — the FC ends up floating on rubber with no direct metal-to-metal contact between the board and the airframe.

The mounting holes are **electrically connected to GND** — this is only relevant if the M3 screw contacts the frame directly. When using rubber isolators as intended, the rubber breaks the electrical path.

The board is a single-MCU architecture with no FMU/I/O split. All outputs and peripherals are driven directly by the STM32H743.

### Fastener recommendations

- Use **M3 screws** through the isolators into standoffs on the frame side.
- Tighten M3 fasteners until snug, then back off a quarter turn — over-torquing crushes the isolator and defeats vibration isolation. The FC should have a small amount of visible give under finger pressure.
- Rubber anti-vibration isolators (M3 hardware inside an M4 outer body) are available from Avian Automata as a 12-pack replacement; four are included with each 305ap for a full first-install.

:::tip Vibration isolation
The M4 mounting hole pattern exists specifically to accept M3 rubber vibration isolators. Do not bolt the 305ap directly to the frame with M4 screws through the mounting holes — you lose the vibration isolation the mount was designed for. Every current-generation IMU is only as good as its mount, and hard-mounting transmits motor and airframe vibration straight into the gyro and accelerometer.
:::

## Buttons

| Button | Function |
|---|---|
| BOOT | Hold at power-on to enter DFU bootloader mode |
| RESET | Hard reset the MCU |

To enter DFU mode for flashing: hold BOOT, apply power (or press RESET while powered), then release BOOT. The board will enumerate as a DFU device over USB.

## LEDs

See [Troubleshooting → LED Status](../troubleshooting/led-status) for a full LED code reference.

| LED | GPIO | PX4 Function |
|---|---|---|
| Red | PD9 | Errors, boot state |
| Green | PB12 | GPS lock, armed state |
| Blue | PE12 | Flight mode indicator |
| RGB | — | Full status (arming, modes, errors) |

Active-low: GPIO LOW = LED on.
