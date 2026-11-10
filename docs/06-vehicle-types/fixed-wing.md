---
id: fixed-wing
title: Fixed Wing
sidebar_label: Fixed Wing
---

# Fixed Wing

The standard firmware (`avianautomata_305ap_default`) includes PX4's fixed-wing and VTOL controllers, so the 305ap can fly planes, flying wings and VTOLs.

:::note Supported, not yet flight-tested
Fixed-wing and VTOL use the standard PX4 controllers and are supported by the firmware, but avian automata has not flight-tested the 305ap in these configurations yet. Bench-test thoroughly and take extra care on first flights.
:::

## Outputs: Servos and Motors

Control surfaces use servos at 50 Hz PWM, and the motor uses an ESC. The eight outputs are in four timer groups, and **all outputs in a group share one protocol and rate**. Plan the wiring so servos and ESCs sit in different groups:

| Group | Outputs | Example use | `PWM_MAIN_TIMn` |
|---|---|---|---|
| TIM1 | MAIN 1–2 | Throttle (ESC) | DShot300 or PWM 400 Hz |
| TIM2 | MAIN 3–4 | Ailerons | PWM 50 Hz |
| TIM3 | MAIN 5–6 | Elevator, rudder | PWM 50 Hz |
| TIM4 | MAIN 7–8 | Flaps, other servos | PWM 50 Hz |

All groups default to DShot300, so set the servo groups to PWM 50 Hz. Assign the functions in **Vehicle Setup → Actuators** after selecting the airframe. Check each surface moves the right way with the actuator sliders before flying.

:::warning Servo power
Power servos from a separate BEC sized for them, not from the board's 5 V pins. Servos draw large, changing currents, and current on the board's 5 V rail disturbs the onboard compass (see [Magnetometer](../hardware/magnetometer#magnetic-interference)).
:::

## Airspeed Sensor

An airspeed sensor is strongly recommended for fixed-wing flight. Supported connections:

- I2C sensors (MS4525DO, SDP3x, and others) on the **External I2C** connector (I2C2)
- DroneCAN airspeed sensors on a CAN port (`UAVCAN_SUB_ASPD` = 1; see [DroneCAN](../advanced/dronecan))

Calibrate it in **Vehicle Setup → Sensors → Airspeed**.

## Setup Steps

1. Select the airframe (**Vehicle Setup → Airframe**), for example **Standard Plane** or **Flying Wing**.
2. Assign outputs and set the timer group protocols as above.
3. Calibrate sensors, including airspeed.
4. Set up radio, flight modes (at least Manual, Stabilized and Return) and the battery.
5. Check control surface directions in Manual mode and in Stabilized mode (tilt the plane; the surfaces must counteract the tilt).

## Tuning

PX4 includes fixed-wing autotune (`FW_AT_APPLY`). See [Autotune](../tuning/autotune) and the [PX4 fixed-wing tuning guide](https://docs.px4.io/main/en/config_fw/pid_tuning_guide_fixedwing.html).

## VTOL

VTOL frames (standard VTOL, tiltrotor, tailsitter) use the same firmware. A VTOL needs both multirotor motors and fixed-wing surfaces, so check the output count: eight outputs cover, for example, four lift motors, a pusher and three servos. DroneCAN ESCs or servos can add outputs. See the [PX4 VTOL documentation](https://docs.px4.io/main/en/frames_vtol/).
