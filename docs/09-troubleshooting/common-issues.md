---
id: common-issues
title: Common Issues
sidebar_label: Common Issues
---

# Common Issues

Start with the MAVLink Console (**QGroundControl → Analyze Tools → MAVLink Console**). `dmesg` shows the boot messages, and most problems leave a clue there.

## Connection and Firmware

| Symptom | Cause | Fix |
|---|---|---|
| QGroundControl doesn't see the board | Charge-only USB cable | Use a data cable |
| QGroundControl doesn't see the board (Linux) | No permission for the serial port | `sudo usermod -aG dialout $USER`, then **log out and back in**. New group membership only applies to new logins. |
| QGroundControl connects, then drops repeatedly (Linux) | ModemManager probing the port | `sudo apt remove modemmanager` |
| No 305ap firmware in QGroundControl's firmware list | The board isn't in upstream PX4 | Install a `.px4` file with **Custom firmware file**; see [Firmware Install](../getting-started/firmware-install) |
| Firmware upload never starts | Board was already powered when QGroundControl started waiting | Unplug the battery and USB, then plug in USB while QGroundControl waits |

## Parameters and Setup

| Symptom | Cause | Fix |
|---|---|---|
| Parameters reset after every reboot | No SD card, or the card isn't mounted | Insert a FAT32 card; check with `ls /fs/microsd` |
| Boot log shows `No airframe file found for SYS_AUTOSTART value` | No airframe selected yet | Select an airframe in **Vehicle Setup → Airframe** |
| Pre-arm: "Accel uncalibrated" / "Compass uncalibrated" | New board, or calibration was reset | Calibrate in **Vehicle Setup → Sensors** |
| Can't arm, no obvious error | Pre-arm check failing | Click the vehicle status icon in QGroundControl, or run `commander check` |

## Motors and ESCs

| Symptom | Cause | Fix |
|---|---|---|
| No signal on any motor output | No functions assigned | Assign motors in **Vehicle Setup → Actuators** |
| Motors 1–2 work but 3–8 don't on DShot | Only MAIN 1–2 default to DShot | Set `PWM_MAIN_TIM1`–`TIM3` to DShot for the groups you use; see [ESC Configuration](../configuration/esc-configuration#output-protocols-and-timer-groups) |
| A servo jitters or doesn't move | Servo shares a timer group with DShot or fast PWM | Move servos to their own group at 50 Hz |
| Motors don't spin on USB power | ESCs and outputs need battery power | Connect the battery |
| No ESC telemetry (RPM, temperature) | Telemetry driver not started | Add `dshot telemetry -d /dev/ttyS0 -x` to `/fs/microsd/etc/extras.txt`; see [ESC Configuration](../configuration/esc-configuration#esc-telemetry) |

## Power Monitoring

| Symptom | Cause | Fix |
|---|---|---|
| No current or mAh shown | The board has no current sensor | Connect an ESC or power module current output to CURRENT\_SENSE |
| Current reads about 11× too low | `BAT1_A_PER_V` set to the sensor's datasheet value | Use 11 × the datasheet value; the input has a 1/11 divider. See [ESC Configuration](../configuration/esc-configuration#current-sensing) |
| Battery voltage off by a few percent | Resistor tolerance | Calibrate the voltage divider against a multimeter in **Vehicle Setup → Power** |

## Sensors and GPS

| Symptom | Cause | Fix |
|---|---|---|
| Heading changes when a payload, servo or companion computer is active | Varying 5 V load near the onboard compass | Power heavy 5 V loads separately; use an external GPS + compass as primary. See [Magnetometer](../hardware/magnetometer#magnetic-interference) |
| "Compass inconsistent" | Compasses disagree: orientation or calibration | Recalibrate; check the external compass rotation (`CAL_MAGn_ROT`) |
| No GPS | TX and RX swapped, or the receiver isn't powered | Check the cable; run `gps status` |
| GPS found only after a while | The driver searches baud rates until the receiver answers | Normal; it takes up to about 20 seconds |
| High vibration warnings | Board hard-mounted | Mount on the rubber isolators |

## Radio and Telemetry

| Symptom | Cause | Fix |
|---|---|---|
| No RC input | Protocol or port mismatch | Check `listener input_rc`; see [RC Setup](../configuration/rc-setup) |
| Telemetry radio stalls or loses packets | Flow control mismatch | Set `MAV_0_FLOW_CTRL` to 1 if RTS/CTS are wired, 0 if not; see [Telemetry](../configuration/telemetry#flow-control) |
| Nothing on TELEM 2 | TELEM 2 has no function by default | Assign one (`MAV_1_CONFIG`, `UXRCE_DDS_CFG`, ...) and reboot |

## DroneCAN

| Symptom | Cause | Fix |
|---|---|---|
| No CAN devices found | DroneCAN off by default | Set `UAVCAN_ENABLE` (2 for sensors, 3 for ESCs) and reboot |
| Device seen but data missing | Subscription off for that sensor type | Enable its `UAVCAN_SUB_*` parameter; see [DroneCAN](../advanced/dronecan) |
| Bus errors in `uavcan status` | Extra termination, or wiring | Remove external terminators (the board terminates both ports); check CAN H/L |
| Two devices fight or disappear | Duplicate node IDs | Give each device a unique ID, or use dynamic allocation |

## Crashes and Lock-Ups

| Symptom | Cause | Fix |
|---|---|---|
| Board reboots unexpectedly | Hard fault | A `fault_*.log` file is written to the SD card on the next boot. See [Logs Analysis](logs-analysis#crash-logs) |
| After a crash, the board stays dark on USB until power is removed | Firmware before **20261019b** halted at boot after a hard fault | Update to 20261019b or later |

:::note For developers
`gps stop` in the MAVLink Console can lock up the board if the GPS driver is searching baud rates with no receiver connected. Avoid stopping the GPS driver in that state; reboot instead.
:::
