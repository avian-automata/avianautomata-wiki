---
id: rover
title: Rover
sidebar_label: Rover
---

# Rover

Ground vehicles use the **rover firmware**, a separate build for the 305ap. It replaces the flying-vehicle controllers with PX4's rover modules.

:::note Supported, not yet tested
The rover build uses the standard PX4 rover modules and is supported by the firmware, but avian automata has not tested the 305ap on a rover yet. Bench-test thoroughly before driving.
:::

## Rover Firmware

| Build target | File |
|---|---|
| `avianautomata_305ap_rover` | `avianautomata_305ap_rover_<date>.px4` |

Install it like any other firmware (see [Firmware Install](../getting-started/firmware-install)), or build it with `make avianautomata_305ap_rover`.

Compared with the default firmware, the rover build:

- adds the **Ackermann**, **differential** and **mecanum** rover modules
- adds the **RoboClaw** motor controller driver
- removes multirotor, fixed-wing and VTOL control

## Rover Types

| Type | Steering | Typical vehicle |
|---|---|---|
| Ackermann | Steering servo + drive motor | RC car |
| Differential | Left and right motors at different speeds | Skid-steer, tracked |
| Mecanum | Four mecanum wheels | Holonomic platform |

Select the airframe for your type in **Vehicle Setup → Airframe**.

## Outputs

- **ESCs** connect to the motor outputs like on a multirotor. Car ESCs usually expect PWM at 50 Hz.
- **Steering servos** need PWM at 50 Hz.
- **RoboClaw** controllers connect over a serial port instead of PWM.

All outputs in a timer group share one protocol and rate, so set every group you use (see [ESC Configuration](../configuration/esc-configuration#output-protocols-and-timer-groups)). Power servos and the ESC's BEC separately from the board's 5 V rail.

## Setup

Follow the [PX4 rover documentation](https://docs.px4.io/main/en/frames_rover/) for your rover type. The steps are the same as for other vehicles: airframe, actuators, sensors, radio, then tuning of the speed and steering controllers.
