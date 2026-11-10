---
id: esc-configuration
title: ESC Configuration
sidebar_label: ESC Configuration
---

# ESC Configuration

ESCs connect to the **Motors** connector (U21): eight outputs, 5 V for ESC logic, a serial telemetry input and an analog current input. DroneCAN ESCs connect to the CAN ports instead; see [DroneCAN](../advanced/dronecan).

## Motors Connector

| Pin | Signal | Use |
|---|---|---|
| 1 | +5V | ESC logic / signal power (not motor power) |
| 2 | CURRENT\_SENSE | Current output from the ESC or a power module |
| 3 | MOTOR\_TELEM\_RX | ESC serial telemetry (BLHeli32 / AM32) |
| 4–11 | MOTOR1–MOTOR8 | Motor signals |
| 12 | GND | Signal ground |

See [Connectors & Pinout](../hardware/connectors-pinout) and [PWM Outputs](../hardware/pwm-outputs) for pin details.

## Assigning Motors

Outputs carry no signal until a function is assigned to them. Assign them in QGroundControl under **Vehicle Setup → Actuators**:

1. Select your airframe first (**Vehicle Setup → Airframe**). It sets the geometry and motor count.
2. In **Actuators**, set each output (**MAIN 1–8**) to the motor it drives (**Motor 1**, **Motor 2**, ...). This writes `PWM_MAIN_FUNCn`.
3. Use the motor test sliders, with **propellers off**, to confirm each motor spins at the position shown in the frame diagram.

## Output Protocols and Timer Groups

The eight outputs are driven by four timers. All outputs on one timer share one protocol and rate:

| Timer group | Outputs | Protocol parameter | Stock setting |
|---|---|---|---|
| TIM1 | MAIN 1–2 | `PWM_MAIN_TIM0` | DShot300 |
| TIM2 | MAIN 3–4 | `PWM_MAIN_TIM1` | DShot300 |
| TIM3 | MAIN 5–6 | `PWM_MAIN_TIM2` | DShot300 |
| TIM4 | MAIN 7–8 | `PWM_MAIN_TIM3` | DShot300 |

:::warning Servos and PWM ESCs
Every group defaults to DShot300. Set a group to PWM if it drives servos or PWM-only ESCs. In QGroundControl the protocol is selected per timer group in the **Actuators** tab.
:::

Values for `PWM_MAIN_TIMn`:

| Value | Protocol |
|---|---|
| `-5` / `-4` / `-3` | DShot150 / DShot300 / DShot600 |
| `-8` / `-7` / `-6` | Bidirectional DShot150 / 300 / 600 (not yet verified on the 305ap) |
| `-1` | OneShot |
| `50` / `100` / `200` / `400` | PWM at that rate in Hz (`400` default) |

A change takes effect after a reboot. Servos and DShot motors can't share a timer group, so put servos on their own group at `50` Hz (see [Fixed Wing](../vehicle-types/fixed-wing)).

## DShot

DShot is the recommended protocol for current ESCs. It needs no calibration, and the command range is fixed.

| Parameter | Meaning |
|---|---|
| `DSHOT_MIN` | Minimum throttle when armed (fraction of full scale, default `0.055`). Raise it if motors stutter at idle. |
| `DSHOT_3D_ENABLE` | Reversible (3D) mode, for ESCs and frames that use it |

Set motor direction in the ESC itself, with its configuration tool (BLHeli32 Suite, the AM32 configurator), or by swapping two motor wires. Don't reverse motors by changing the airframe.

:::note Bidirectional DShot
Bidirectional DShot (RPM telemetry on the signal wire) has not been verified on the 305ap yet. Use serial ESC telemetry (below) for RPM, voltage and current.
:::

## PWM and OneShot ESCs

PWM ESCs need a throttle range calibration. Run it in **Vehicle Setup → Power → ESC PWM Minimum and Maximum Calibration**, with **propellers removed**, and follow the prompts. Disarmed, PWM outputs hold `PWM_MAIN_DISn` (default 1000 µs).

## ESC Telemetry

ESCs with serial telemetry (BLHeli32, AM32) report RPM, voltage, current and temperature on the **MOTOR\_TELEM\_RX** pin. Connect the ESC telemetry wire to pin 3.

This input is on USART1 (`/dev/ttyS0`). The firmware starts ESC telemetry on it automatically, so no setup is needed. ESCs without a telemetry wire are not affected. PX4 publishes the values in the `esc_status` topic and in MAVLink `ESC_STATUS`. Check with:

```
listener esc_status
```

Telemetry needs DShot outputs with motors assigned.

## Current Sensing

The **CURRENT\_SENSE** pin accepts a 0–3.3 V analog current signal from an ESC or power module. The 305ap has no onboard current shunt.

The input passes through a **1/11 divider** before the ADC, so PX4 sees one eleventh of the sensor's voltage. Set the scale accordingly:

| Parameter | Value |
|---|---|
| `BAT1_A_PER_V` | **11 ×** the sensor's amps per volt (e.g. a 40 A/V sensor → `440`) |
| `BAT_V_OFFS_CURR` | The sensor's zero-current offset as seen at the ADC (its output at 0 A ÷ 11) |

Then check against a clamp meter or a known load, and adjust `BAT1_A_PER_V` until PX4's reading matches. Battery voltage needs no wiring: it is measured on the board from the battery input (`BAT1_V_DIV` = 11).
