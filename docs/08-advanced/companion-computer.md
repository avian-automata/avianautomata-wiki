---
id: companion-computer
title: Companion Computer
sidebar_label: Companion Computer
---

# Companion Computer

A companion computer (Raspberry Pi, Jetson, and similar) runs higher-level software next to the flight controller: computer vision, mapping, mission logic, or ROS 2. The 305ap connects to it over **TELEM 2** (recommended) or USB.

## Connecting over TELEM 2

TELEM 2 (CN3, UART7) has RTS/CTS flow control and defaults to 921600 baud, which suits a companion link.

| 305ap pin | Connect to companion |
|---|---|
| 2 TX | UART RX |
| 3 RX | UART TX |
| 4 CTS | RTS (if available) |
| 5 RTS | CTS (if available) |
| 6 GND | GND |

Pin 1 (+5V) is an output from the flight controller. Don't connect it to the companion's 5 V.

The companion's UART must be 3.3 V logic. A USB-to-serial adapter (FTDI or similar) at 3.3 V also works.

## MAVLink (MAVSDK, MAVROS)

Set in QGroundControl and reboot:

| Parameter | Value |
|---|---|
| `MAV_1_CONFIG` | TELEM 2 |
| `MAV_1_MODE` | Onboard (default) |
| `SER_TEL2_BAUD` | 921600 (default) |
| `MAV_1_FLOW_CTRL` | Force on (`1`) if RTS/CTS are wired, otherwise Force off (`0`) |

On the companion, connect your MAVLink software to the serial device at 921600 baud, for example `serial:///dev/ttyAMA0:921600` in MAVSDK.

## ROS 2 (uXRCE-DDS)

The firmware includes PX4's uXRCE-DDS client, which publishes PX4 topics directly to ROS 2.

| Parameter | Value |
|---|---|
| `UXRCE_DDS_CFG` | TELEM 2 |
| `SER_TEL2_BAUD` | 921600 |

Then run the agent on the companion:

```bash
MicroXRCEAgent serial --dev /dev/ttyAMA0 -b 921600
```

Use either MAVLink or uXRCE-DDS on TELEM 2, not both. See the [PX4 ROS 2 guide](https://docs.px4.io/main/en/ros2/user_guide.html) for the ROS 2 side.

## Connecting over USB

The companion can also use the 305ap's USB-C port. The flight controller appears as a USB serial device (`/dev/ttyACM0` on Linux) carrying MAVLink.

:::note USB power
USB from the companion also feeds the 305ap's 5 V power mux. When the battery is connected, the board's own 5 V supply normally takes over. The board can't tell whether USB is connected, and USB is less robust to vibration than a JST-GH connector, so TELEM 2 is preferred in flight.
:::

## Powering the Companion

Power the companion computer from its own regulator, not from the 305ap's 5 V pins. Companion computers can draw several amps, and their load changes constantly with CPU and radio activity. A varying load on the board's 5 V rail can throw off the onboard compass; see [Magnetometer](../hardware/magnetometer#magnetic-interference). For vehicles with a companion computer, an external GPS + compass module is recommended.

## Checking the Link

In the MAVLink Console:

```
mavlink status          # MAVLink instances, rates, flow control
uxrce_dds_client status # if using ROS 2
```
