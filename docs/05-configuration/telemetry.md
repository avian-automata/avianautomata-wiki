---
id: telemetry
title: Telemetry
sidebar_label: Telemetry
---

# Telemetry

The 305ap carries MAVLink on USB and on two telemetry ports, both with RTS/CTS flow control.

| Port | Connector | UART | Stock function | Baud |
|---|---|---|---|---|
| USB | USB-C | — | MAVLink to QGroundControl | — |
| TELEM 1 | CN2 | UART4 (`ttyS3`) | MAVLink (instance 0) | 57600 |
| TELEM 2 | CN3 | UART7 (`ttyS6`) | Off | 921600 |
| TELEM 3 | VTX (CN6) | USART2 (`ttyS1`) | MSP OSD | 115200 |

Port pinouts are in [Connectors & Pinout](../hardware/connectors-pinout).

## Telemetry Radio on TELEM 1

TELEM 1 is ready for a telemetry radio (SiK, RFD900 and similar) with no configuration:

| Parameter | Default | Meaning |
|---|---|---|
| `MAV_0_CONFIG` | TELEM 1 | MAVLink instance 0 on TELEM 1 |
| `SER_TEL1_BAUD` | 57600 | Must match the radio's serial (air-side) baud rate |
| `MAV_0_MODE` | Normal | Message set for a ground station |
| `MAV_0_RATE` | 1200 B/s | Bandwidth budget; raise it for faster radios |
| `MAV_0_FLOW_CTRL` | Auto | RTS/CTS flow control |

Wire TX to the radio's RX and RX to its TX. Connect CTS and RTS too if the radio has them (cross them the same way: FC CTS ← radio RTS, FC RTS → radio CTS).

## Flow Control

Flow control stops data being lost when the radio's buffer is full. With `MAV_n_FLOW_CTRL` = **Auto**, PX4 starts with flow control on and turns it **off until the next reboot** if the CTS line holds off its writes for more than half a second. A radio that doesn't drive CTS (or isn't connected yet at boot) therefore ends up with flow control off. That is usually what you want.

Set it explicitly when you know the wiring:

| Value | Use when |
|---|---|
| `1` (Force on) | The device drives RTS/CTS correctly (most SiK radios with flow control enabled) |
| `0` (Force off) | Only TX, RX and GND are connected |

Changing the setting needs a reboot.

## TELEM 2

TELEM 2 has no function by default. Typical uses:

- **Companion computer:** MAVLink or uXRCE-DDS (ROS 2). See [Companion Computer](../advanced/companion-computer).
- **Second telemetry radio:** set `MAV_1_CONFIG` = TELEM 2, `MAV_1_MODE` = Normal, `SER_TEL2_BAUD` to the radio's baud rate, and `MAV_1_RATE` to a bandwidth limit (`0` sets it from the baud rate).
- **Second GPS:** see [GPS Setup](gps-setup#two-gps-receivers).
- **CRSF / ELRS receiver with telemetry:** see [RC Setup](rc-setup).

Reboot after assigning a function. The port-specific parameters only appear after the reboot.

## MAVLink over ELRS

ExpressLRS radios with MAVLink support can carry RC and telemetry on one link. Set the receiver to its MAVLink mode and connect it to a telemetry port as a MAVLink instance, with the baud rate the receiver uses. See the ExpressLRS documentation for the receiver side.

## USB

The USB port always carries MAVLink. It is the connection for QGroundControl on the bench and for firmware updates. USB has no baud rate setting.

## Power for Radios

Pin 1 of each telemetry connector supplies 5 V from the board. High-power radios that draw large, pulsed current are better powered separately. A pulsing 5 V load also affects the onboard compass; see [Magnetometer](../hardware/magnetometer#magnetic-interference).

## Checking a Link

In the MAVLink Console:

```
mavlink status
```

lists each MAVLink instance with its port, mode, data rate, and whether flow control is on.
