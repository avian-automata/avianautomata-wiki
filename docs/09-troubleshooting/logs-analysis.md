---
id: logs-analysis
title: Logs Analysis
sidebar_label: Logs Analysis
---

# Logs Analysis

The 305ap writes three kinds of logs: **flight logs** (ULog) on the SD card, **crash logs** after a hard fault, and the **boot log** (the console messages since power-up).

## Flight Logs

PX4 writes flight logs as `.ulg` files in `/fs/microsd/log/`.

| Parameter | Default | Meaning |
|---|---|---|
| `SDLOG_MODE` | 0 | When to log: `0` arm to disarm, `1` boot to disarm, `2` boot to shutdown |
| `SDLOG_PROFILE` | Default set | What to log; add profiles for tuning or system identification |

Set `SDLOG_MODE` = 1 to capture problems that happen before arming, such as sensor or GPS issues on the bench.

### Downloading

- **QGroundControl:** **Analyze Tools → Log Download**, select logs, **Download**. Over USB this is quick; over a telemetry radio it is slow.
- **SD card:** remove the card and copy the `log` folder on a PC.

### Analysing

| Tool | Use |
|---|---|
| [Flight Review](https://review.px4.io) | Upload a log for standard plots: attitude tracking, vibration, power, GPS, estimator status |
| [PlotJuggler](https://github.com/facontidavide/PlotJuggler) | Any topic, with your own plots; good for comparing signals |
| [pyulog](https://github.com/PX4/pyulog) | Command-line and Python access (`ulog_info`, `ulog2csv`) |

### What to Check First

- **Vibration:** accelerometer vibration in the green range. If high, check the rubber isolators and propeller balance.
- **Rate tracking:** angular rate setpoint vs. estimate on each axis.
- **Power:** battery voltage sag under throttle. Current is only logged with an external current sensor.
- **Estimator:** EKF2 innovation test ratios below 1. Persistent high values point at a sensor problem.
- **Magnetometer:** field strength over the flight. If it changes with throttle or when a 5 V device switches, the compass is picking up current; see [Magnetometer](../hardware/magnetometer#magnetic-interference).
- **GPS:** satellites used, horizontal accuracy (eph), and fix type.

## Crash Logs

If the firmware hits a hard fault, the board saves the fault details (registers, stack, task) to backup SRAM, which survives a reset but not a power-off, and resets. On the next boot PX4 writes them to the SD card as:

```
/fs/microsd/fault_<date>_<time>.log
```

The date comes from the board's clock, which may be wrong if it has had no GPS fix since power-up.

Useful commands in the MAVLink Console:

```
hardfault_log check     # is there a fault not yet written to the SD card?
hardfault_log commit    # write it to the SD card now
ls /fs/microsd          # list fault files
```

When reporting a crash, include the `fault_*.log` file, the flight log, and the firmware version (`ver all`).

:::note
Firmware before **20261019b** could stop at boot after a hard fault, waiting at a console prompt that the board can't answer, until power was removed. That also lost the crash log. Update to 20261019b or later.
:::

## Boot Log

`dmesg` in the MAVLink Console prints the messages since power-up: firmware version, sensors found, and any startup errors.

```
dmesg
```

### Hardware Console

The same messages, including crash dumps, are also sent out of the **VTX** connector, pin 5 (VTX\_SBUS, USART3 TX), at **57600 baud, 8N1**. This works even when USB is not available, which makes it the best way to see what happens during a reset.

1. Connect a 3.3 V USB-to-serial adapter: adapter RX to VTX pin 5, adapter GND to VTX pin 4 or 6.
2. Open the port at 57600 baud (for example `picocom -b 57600 /dev/ttyUSB0`).
3. Power the board.

The console is output-only on the 305ap: there is no console input pin.
