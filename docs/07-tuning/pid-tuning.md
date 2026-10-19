---
id: pid-tuning
title: PID Tuning
sidebar_label: PID Tuning
---

# PID Tuning

The 305ap uses PX4's standard controllers, so PX4's tuning guides apply unchanged. This page summarizes the order and the tools, with notes specific to the board. For most vehicles, start with [Autotune](autotune) and use manual tuning to refine.

## Controller Structure (Multirotor)

PX4 multirotor control is a cascade, tuned from the inside out:

| Loop | Runs at | Main parameters |
|---|---|---|
| Rate (angular velocity) | Gyro rate, 400 Hz by default (`IMU_GYRO_RATEMAX`) | `MC_ROLLRATE_P/I/D/K`, `MC_PITCHRATE_*`, `MC_YAWRATE_*` |
| Attitude | With the rate loop | `MC_ROLL_P`, `MC_PITCH_P`, `MC_YAW_P` |
| Velocity and position | Slower | `MPC_XY_VEL_*`, `MPC_Z_VEL_*`, `MPC_XY_P`, `MPC_Z_P` |

Tune the rate loop first. It sets how well everything above it can work.

## Tuning in QGroundControl

**Vehicle Setup → PID Tuning** shows live setpoint and response plots for each axis while you fly, with sliders for the main gains.

1. Hover in **Stabilized** or **Altitude** mode.
2. Make quick stick inputs on one axis and watch the response plot.
3. Adjust:
   - **P** up until the response is fast without overshoot.
   - **D** up to damp overshoot and oscillation after a step.
   - **I** to remove slow drift and hold attitude under disturbances.
4. Repeat for roll, pitch and yaw. Yaw usually needs less D.

Make small changes (10–20%) and fly between each. Save your parameters before starting so you can return to a known state.

## Signs to Look For

| Symptom | Likely cause |
|---|---|
| Fast, high-pitched oscillation | Rate P or D too high, or D-term noise (check filters) |
| Slow wobble after a stick input | Rate P too low, or I too high |
| Hot motors after a hover with no visible oscillation | Too much D or too little filtering; noise reaching the motors |
| Attitude drifts in wind | Rate I too low |
| Bounces in altitude | `MPC_Z_VEL_P_ACC` too high, or `MPC_THR_HOVER` far off |

## Using Logs

A log from each tuning flight shows more than the live plots. In [Flight Review](https://review.px4.io), check:

- **Angular rate** setpoint vs. estimate on each axis: they should overlap closely.
- **Actuator controls** and **motor outputs**: should not be noisy or saturated.
- **Vibration**: accelerometer vibration and gyro noise levels.

See [Logs Analysis](../troubleshooting/logs-analysis).

## Board Notes

- **Gyro rate:** the rate loop runs at 400 Hz by default. Small, agile multirotors can benefit from a faster loop (`IMU_GYRO_RATEMAX` = 800 or 1000). Check CPU load with `top` in the MAVLink Console after changing it, and re-tune: the same gains act differently at a different rate.
- **Two IMUs:** PX4 votes between the two IMUs and uses the selected one for control. Both are on the same board with the same filtering, so no per-IMU tuning is needed.
- **Mounting:** gains that work on a soft-mounted board can oscillate on a hard-mounted one. Use the rubber isolators.

For the full procedures, see PX4's [multicopter PID tuning guide](https://docs.px4.io/main/en/config_mc/pid_tuning_guide_multicopter.html) and [fixed-wing PID tuning guide](https://docs.px4.io/main/en/config_fw/pid_tuning_guide_fixedwing.html).
