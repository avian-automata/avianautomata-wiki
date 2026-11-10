---
id: intro
title: 305ap Overview
sidebar_label: Overview
sidebar_position: 0
slug: /intro
---

# avian automata 305ap

The 305ap is a compact, high-performance flight controller for multirotor, fixed-wing, and rover platforms. It runs [PX4](https://px4.io) on NuttX RTOS, built around the STM32H743VIH6. The board is 36.5 mm × 36.5 mm with an M4 mounting hole pattern designed around M3 rubber vibration isolators, aimed at the PX4 ecosystem with dual FDCAN, dual telemetry, a dedicated VTX power rail, and a full onboard sensor suite with redundant IMUs.

## Quick Links

| I want to... | Go to |
|---|---|
| Flash firmware for the first time | [Firmware → Flashing](firmware/flashing) |
| See the connector pinout | [Hardware → Connectors & Pinout](hardware/connectors-pinout) |
| Set up QGroundControl | [Configuration → QGroundControl Setup](configuration/qgc-setup) |
| Build firmware from source | [Firmware → Building from Source](firmware/building-from-source) |
| Troubleshoot a problem | [Troubleshooting → Common Issues](troubleshooting/common-issues) |

## Specifications

### Processor

| Parameter | Value |
|---|---|
| MCU | STM32H743VIH6 (TFBGA100) |
| Architecture | Arm Cortex-M7, double-precision FPU / DSP |
| Clock | 24 MHz HSE → 480 MHz core (via PLL) |
| Flash | 2 MB |
| RAM | Up to 1 MB |

### Sensors

| Sensor | Part | Interface |
|---|---|---|
| IMU (×2, redundant) | ST LSM6DSV (primary) / TDK InvenSense ICM-45686 (fallback) | SPI2, SPI3 |
| Barometer | Bosch BMP581 | SPI1 |
| Magnetometer | MEMSIC MMC5983MA | I2C1 |

### Connectivity

| Interface | Details |
|---|---|
| UARTs | 7 (USART1/2/3, UART4/5/6/7) |
| CAN | 2× FDCAN (FDCAN1, FDCAN2) via SIT1044T FD transceivers |
| I2C | 3 buses (internal mag, GPS port, external port) |
| SPI | 4 buses (baro, IMU ×2, external expansion) |
| PWM outputs | 8 (TIM1/2/3/4) |
| RC input | UART5 single-wire (SBUS, CRSF and other serial protocols) |
| USB | USB-C, USB 2.0 FS device (OTG FS PHY) |
| SD card | SDMMC1 (native 4-bit SDIO) |

### Power

| Rail | Source | Purpose |
|---|---|---|
| VBAT | External (2S–6S) | Main input with ideal-diode protection |
| 9 V | AP63357 buck | VTX / accessories |
| 5 V | AP63357 buck | Peripherals, connectors, CAN, GPS |
| 3.3 V | AP63357 buck | Digital logic, MCU |
| 3.3 V LDO | RT9193 (quiet) | IMUs, barometer, magnetometer |
| 5 V select | TPS2116 mux | Auto-selects USB 5 V or 5 V switcher |

### Physical

| Parameter | Value |
|---|---|
| Dimensions | 36.5 mm × 36.5 mm |
| Mounting | M4 holes on a 30.5 mm × 30.5 mm pattern, sized for M3 rubber vibration isolators |
| Weight | 11 g (with microSD card installed) |
| Firmware target | `avianautomata_305ap` |

## Design Notes

- **Dual IMUs.** Two IMU slots on SPI2 and SPI3. Each slot supports the ST LSM6DSV (primary) or TDK InvenSense ICM-45686 (fallback) — firmware auto-detects at boot via WHO_AM_I. PX4 runs sensor voting across both and falls back if one diverges.
- **Low-noise sensor rail.** The IMUs, barometer, and magnetometer are powered from a separate RT9193 ultra-low-noise 3.3 V LDO, isolated from the main digital 3.3 V switcher via a ferrite bead.
- **Wide input voltage.** The VBAT path uses an LM74700-Q1 ideal-diode controller with an external N-FET, providing 2S–6S input support with low forward drop and protection against reverse polarity.
- **Dedicated 9 V rail.** A second AP63357 buck provides a stable 9 V rail for VTX/OSD power, independent of the 5 V peripheral rail.
- **Auto USB / battery power.** A TPS2116 mux selects between USB 5 V and the onboard 5 V switcher automatically, so the board powers cleanly from USB alone during bench work.
- **Onboard compass and 5 V loads.** Current on the 5 V peripheral rail can shift the onboard compass reading. Steady loads are removed by compass calibration; with highly variable 5 V loads, use an external GPS + compass module. See [Magnetometer](hardware/magnetometer#magnetic-interference).
- **No onboard current shunt.** The ASENSE pin accepts a 0–3.3 V signal from an external current sensor (power module or ESC with current output). No current data is available without one.
- **No safety switch.** Arming is by RC stick command, an RC arm switch (`RC_MAP_ARM_SW`), or the ground station. PX4's default `COM_SAFETY_MODE` (safety always off) is correct for the 305ap.
- **Parameters on SD card.** No FRAM. Parameters are stored at `/fs/microsd/params`. A card must be inserted for parameters to persist across reboots.
- **No inversion stage on RC input.** UART5 is single-wire half-duplex. SBUS compatibility depends on PX4's software inversion support for the H7 UART. CRSF and uninverted ELRS are fully supported.
- **MCU package.** TFBGA100 exposes GPIO ports A–E only. All pin assignments reflect this constraint.
