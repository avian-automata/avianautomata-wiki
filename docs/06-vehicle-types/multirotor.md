---
id: multirotor
title: Multirotor
sidebar_label: Multirotor
---

# Multirotor

Multirotors are the primary use for the 305ap and the configuration it has been flight-tested in. The standard firmware (`avianautomata_305ap_default`) includes everything needed.

## Supported Frames

With eight motor outputs, the 305ap can drive any PX4 multirotor geometry up to eight motors: quadcopter (X, +, H, wide), hexacopter, octocopter, Y6, and coaxial or custom geometries defined in QGroundControl.

## Setup

1. **Airframe:** in **Vehicle Setup → Airframe**, pick the closest frame (for example **Generic Quadcopter**) and apply. The board has no default airframe, so this step is required.
2. **Geometry and outputs:** in **Vehicle Setup → Actuators**, check the motor positions and spin directions match your frame, and assign each output (**MAIN 1–8**) to its motor.
3. **Output protocol:** set the protocol for every timer group the motors use. Only MAIN 1–2 default to DShot. See [ESC Configuration](../configuration/esc-configuration#output-protocols-and-timer-groups).
4. **Motor test:** with propellers off, spin each motor from the Actuators tab and confirm position and direction.
5. **Sensors, radio, flight modes and power:** see [QGroundControl Setup](../configuration/qgc-setup).

### Example: Quad X on DShot600

| Setting | Value |
|---|---|
| Airframe | Generic Quadcopter |
| MAIN 1–4 | Motor 1–4 (positions as in the Actuators diagram) |
| `PWM_MAIN_TIM0` (MAIN 1–2) | DShot600 |
| `PWM_MAIN_TIM1` (MAIN 3–4) | DShot600 |

## Mounting

- Mount the board on M3 rubber isolators through the M4 holes. Hard-mounting passes frame vibration straight into the IMUs (see [Board Layout](../hardware/board-layout)).
- Keep the flight controller away from the ESCs, battery leads and power distribution, which carry large, changing currents.
- For a reliable heading on a vehicle with busy 5 V loads (gimbal servos, companion computer, video), use a GPS + compass module on a mast. See [Magnetometer](../hardware/magnetometer#magnetic-interference).

## First Flight and Tuning

Follow [First Flight](../getting-started/first-flight). PX4's default gains fly most mid-size quads. For a well-tuned vehicle, run [Autotune](../tuning/autotune), then refine with [PID Tuning](../tuning/pid-tuning) and [Filter Tuning](../tuning/filter-tuning).

## Useful Parameters

| Parameter | Purpose |
|---|---|
| `MPC_THR_HOVER` | Initial hover throttle; the hover thrust estimator refines it in flight |
| `MPC_XY_VEL_MAX`, `MPC_Z_VEL_MAX_UP` | Speed limits in Position mode |
| `MC_AIRMODE` | Keep authority at zero throttle (useful for acrobatic flying) |
| `COM_DISARM_LAND` | Disarm delay after landing |

See the [PX4 multicopter documentation](https://docs.px4.io/main/en/frames_multicopter/) for frame-specific guidance.
