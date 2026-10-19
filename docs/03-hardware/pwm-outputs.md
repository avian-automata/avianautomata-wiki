---
id: pwm-outputs
title: PWM Outputs
sidebar_label: PWM Outputs
---

# PWM Outputs

## Hardware

The 305ap provides 8 PWM/DSHOT motor outputs routed to the motor connector.

| Channel | Timer | GPIO | Default |
|---|---|---|---|
| 1 | TIM1 CH1 | PA8 | Motor 1 |
| 2 | TIM1 CH2 | PE11 | Motor 2 |
| 3 | TIM2 CH1 | PA15 | Motor 3 |
| 4 | TIM2 CH3 | PA2 | Motor 4 |
| 5 | TIM3 CH2 | PA7 | Motor 5 |
| 6 | TIM3 CH4 | PB1 | Motor 6 |
| 7 | TIM4 CH2 | PB7 | Motor 7 |
| 8 | TIM4 CH3 | PD14 | Motor 8 |

Outputs 1–4 share TIM1/TIM2 and outputs 5–8 share TIM3/TIM4. Within each timer, all channels share the same timer frequency (period), so protocols must be compatible within a timer group if mixing is required.

## Motor Connector

The motor connector also carries:
- **+5 V** for ESC BEC and signal power
- **Current sense input** for ESC current monitoring, fed to ASENSE
- **Motor telemetry RX** via USART1 single-wire ESC telemetry (BLHeli32/AM32)
- **GND**

## Supported Protocols

The 305ap supports all standard PX4 output protocols:

| Protocol | Description | Notes |
|---|---|---|
| PWM | Standard 1000–2000 µs | Universal ESC compatibility |
| OneShot125 | ~125–250 µs (8× faster than PWM) | Requires ESC support |
| DSHOT150 | Digital, 150 kbps | Recommended baseline |
| DSHOT300 | Digital, 300 kbps | Typical default for modern ESCs |
| DSHOT600 | Digital, 600 kbps | High-speed DSHOT |

DSHOT is recommended over PWM for most applications. It is noise-immune and needs no calibration.

## Configuring Protocol in QGroundControl

1. Go to **Vehicle Setup → Actuators**
2. Select the output protocol for each timer group (MAIN 1–2, 3–4, 5–6, 7–8)

Or set directly in **Parameters**:

| Parameter | Description |
|---|---|
| `PWM_MAIN_TIM0`–`PWM_MAIN_TIM3` | Protocol per timer group: `-5`/`-4`/`-3` = DShot150/300/600, `-1` = OneShot, `50`–`400` = PWM rate in Hz |

Only MAIN 1–2 default to DShot300; the other groups default to PWM 400 Hz. See [ESC Configuration](../configuration/esc-configuration#output-protocols-and-timer-groups).

## Motor Order

Motor numbering follows the geometry of the selected airframe, shown in **Vehicle Setup → Actuators**. For PX4's **Quadrotor X**:

```
     Front
   3       1
     \   /
     /   \
   2       4
```

| Motor | Position | Direction |
|---|---|---|
| 1 | Front-right | Counter-clockwise |
| 2 | Rear-left | Counter-clockwise |
| 3 | Front-left | Clockwise |
| 4 | Rear-right | Clockwise |

Verify motor order in **Vehicle Setup → Actuators** with the motor test sliders (propellers off) before flying.

## Bidirectional DSHOT (BDSHOT)

PX4 supports bidirectional DShot (RPM over the signal wire) with the `PWM_MAIN_TIMn` values `-8`/`-7`/`-6` (BDShot150/300/600). It has not been verified on the 305ap yet; use serial ESC telemetry (below) for RPM data.

:::note ESC DSHOT support
Verify your ESCs support the selected DSHOT speed. BLHeli32, AM32, and most modern 4-in-1 ESCs support DSHOT300/DSHOT600.
:::

## ESC Telemetry

USART1 (ttyS0) is dedicated to **single-wire ESC serial telemetry** from BLHeli32 or AM32 ESCs. This is separate from BDSHOT and provides temperature, voltage, current, and RPM data per ESC.

To enable, configure your ESC firmware to output serial telemetry and connect the telemetry wire to the USART1 pin (PA9) on the motor connector.

USART1 has no port name in QGroundControl, so start the telemetry driver from the SD card: add `dshot telemetry -d /dev/ttyS0 -x` to `/fs/microsd/etc/extras.txt`. See [ESC Configuration](../configuration/esc-configuration#esc-telemetry).
