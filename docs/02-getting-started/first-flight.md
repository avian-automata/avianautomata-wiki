---
id: first-flight
title: First Flight
sidebar_label: First Flight
sidebar_position: 2
---

# First Flight

This page covers the checks between a configured vehicle and its first flight. It assumes the [Quickstart](quickstart) steps are done: firmware installed, SD card in, airframe selected, sensors and radio calibrated.

The procedure below is written for a multirotor, the vehicle type the 305ap has been flight-tested on. For fixed-wing and rover, follow the same bench checks and then the PX4 guide for your vehicle type.

## Bench Checks (Props Off)

Do every check in this section with the **propellers removed**.

### Sensors and Status

- [ ] SD card detected: no SD card warning in QGroundControl, and `ls /fs/microsd` lists files in the MAVLink Console.
- [ ] No red messages in the QGroundControl status bar. Click the vehicle status icon to see any failing pre-arm checks.
- [ ] Attitude follows the vehicle: tilt it and watch the horizon in the **Fly** view. Roll right must show as roll right, nose up as pitch up.
- [ ] Heading points the right way: compare the compass heading in QGroundControl with a phone compass. If it is off by 90° or 180°, the board or the GPS module orientation parameter is wrong (see [Sensor Calibration](../configuration/sensor-calibration)).
- [ ] GPS reaches a 3D fix outdoors with at least 8 satellites before you rely on Position mode.

### Power

- [ ] Battery voltage in QGroundControl matches a multimeter within about 0.1 V. If not, calibrate the voltage divider under **Vehicle Setup → Power**.
- [ ] If you use an external current sensor, current reads close to zero when idle. The 305ap has no onboard current sensor, so without one QGroundControl shows no current or consumed capacity.

### Radio and Failsafe

- [ ] Every stick and switch moves the right channel in **Vehicle Setup → Radio**.
- [ ] Flight mode switch positions select the modes you expect.
- [ ] Switch the transmitter off: QGroundControl must report RC loss and the failsafe action you configured (Return or Land).

### Motors

- [ ] Use **Vehicle Setup → Actuators** to spin each motor on its own at low throttle. Check that the right motor spins and in the right direction for the frame diagram.
- [ ] Reverse wrong-direction motors in the ESC configuration (or by swapping two motor wires), not in PX4.

See [ESC Configuration](../configuration/esc-configuration) for output protocols and motor ordering.

:::warning No safety switch
The 305ap has no hardware safety switch. Once armed, the motors can spin. Only arm with propellers fitted when you are ready to fly and the area is clear.
:::

## Pre-Flight

- [ ] Propellers fitted in the right direction and tight.
- [ ] Battery secured and fully charged.
- [ ] Vehicle on level ground, away from people.
- [ ] Logging on: the default `SDLOG_MODE` logs from arming to disarming, which is enough for a first flight.

## First Hover

1. Arm in **Stabilized** (or **Altitude**) mode with the throttle at minimum.
2. Raise the throttle slowly until the vehicle lifts off, and hover at about 1–2 m.
3. Make small stick inputs on each axis. The vehicle should respond smoothly and stop when the sticks are centred.
4. Watch for oscillation (fast wobble) or a slow drift that needs constant correction. Land if either is strong.
5. If the hover is stable and you have a GPS fix, switch to **Position** mode. The vehicle should hold its place with the sticks centred.
6. Land, disarm, and download the log.

## After the Flight

Upload the log to [Flight Review](https://review.px4.io) and check:

- **Vibration:** accelerometer vibration levels in the green range. High vibration hurts position hold and altitude hold. Soft-mount the board on its rubber isolators.
- **Tracking:** actual attitude rates follow the setpoints closely.
- **Magnetometer:** the heading estimate stays steady when throttle changes.

If the vehicle oscillated or felt sluggish, tune it next. See [Autotune](../tuning/autotune) and [PID Tuning](../tuning/pid-tuning). [Logs Analysis](../troubleshooting/logs-analysis) explains how to read the log.
