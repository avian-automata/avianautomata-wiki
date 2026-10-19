---
id: autotune
title: Autotune
sidebar_label: Autotune
---

# Autotune

PX4's autotune flies a short series of small, automatic attitude disturbances, measures how the vehicle responds, and computes rate and attitude gains. It is the fastest way to a good tune and the recommended first step before manual [PID Tuning](pid-tuning).

The standard 305ap firmware includes autotune for both **multirotor** and **fixed-wing**.

## Before You Start

- The vehicle flies stably with the default gains in **Altitude** or **Position** mode. Autotune refines a working tune; it can't rescue an unstable one.
- Vibration levels are acceptable (check a log in [Flight Review](https://review.px4.io)). Soft-mount the board on its rubber isolators.
- Filters are reasonable for your frame (see [Filter Tuning](filter-tuning)).
- Calm weather, plenty of open space, and a full battery.

## Multirotor

1. Enable the autotune module: set `MC_AT_EN` = 1 (it is off by default) and reboot.
2. Assign a switch or use QGroundControl's **Autotune** button (**Vehicle Setup → PID Tuning → Autotune**).
3. Take off and hover in **Altitude** or **Position** mode at a safe height.
4. Start autotune. The vehicle twitches on each axis in turn for about 20–40 seconds. Keep the sticks centred and be ready to take over.
5. When QGroundControl reports success, land and disarm.

`MC_AT_APPLY` controls when the new gains are used:

| Value | Behavior |
|---|---|
| `0` | Log only; gains not applied |
| `1` | Apply after disarm (default) |
| `2` | Apply in the air (not recommended) |

After a successful run, fly again and check the result. If it oscillates, restore the previous gains (save your parameters before autotuning) and tune manually. `MC_AT_SYSID_AMP` sets how hard autotune excites the vehicle; see the PX4 documentation before changing it.

## Fixed-Wing

1. Fly in a stabilized mode (**Stabilized**, **Altitude** or **Position**) at a safe altitude with room to fly straight.
2. Start autotune from QGroundControl or an RC switch. The plane makes small roll, pitch and yaw manoeuvres.
3. Keep the plane level and in the air while it runs.

`FW_AT_APPLY` controls when the gains are used: `0` log only, `1` after disarm, `2` in the air (the default for fixed-wing).

See the [PX4 autotune documentation](https://docs.px4.io/main/en/config/autotune.html) for details on both vehicle types.

## After Autotune

- Download the log and check rate tracking in Flight Review.
- Fine-tune with [PID Tuning](pid-tuning) if needed (for example a softer yaw, or faster altitude response).
- Re-run autotune after significant changes: different propellers, motors, battery weight, or a new frame.
