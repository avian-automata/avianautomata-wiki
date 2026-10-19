---
id: gps-setup
title: GPS Setup
sidebar_label: GPS Setup
---

# GPS Setup

## Connecting a GPS

Connect the GPS module to the **GPS** connector (CN4) with a 6-pin JST-GH cable:

| Pin | Signal | Connects to GPS |
|---|---|---|
| 1 | +5V | Power |
| 2 | GPS\_UART\_TX | GPS RX |
| 3 | GPS\_UART\_RX | GPS TX |
| 4 | GPS\_I2C\_SCL | Compass SCL |
| 5 | GPS\_I2C\_SDA | Compass SDA |
| 6 | GND | Ground |

Most GPS + compass modules with a 6-pin JST-GH cable plug straight in. Modules with a 10-pin connector (with buzzer, safety switch and LED) need an adapter cable. See [Connectors & Pinout](../hardware/connectors-pinout).

A GPS can also be connected over DroneCAN. See [DroneCAN](../advanced/dronecan).

## u-blox Receivers (Default)

The firmware is set up for u-blox receivers out of the box. No configuration is needed.

| Parameter | Default | Meaning |
|---|---|---|
| `GPS_1_CONFIG` | GPS 1 | GPS driver on the GPS connector |
| `GPS_1_PROTOCOL` | u-blox | Receiver protocol |
| `SER_GPS1_BAUD` | 0 (auto) | Find the receiver's baud rate automatically |
| `GPS_UBX_DYNMODEL` | 7 (airborne, < 2 g) | Receiver dynamic model |

At startup PX4 searches the common baud rates until the receiver answers, identifies the model, configures its messages and update rate, and switches it to 115200 baud. This works with M8, M9, M10 and F9P receivers. A receiver plugged in after boot is found within one search cycle (up to about 20 seconds).

For other receiver brands, set `GPS_1_PROTOCOL` to the matching protocol, or **NMEA (generic)**, and set `SER_GPS1_BAUD` to the receiver's baud rate.

## Checking the GPS

- QGroundControl's toolbar shows satellite count and fix type.
- In the MAVLink Console:

  ```
  gps status
  ```

  shows the protocol, the baud rate in use and the update rate.

A first fix outdoors with a clear sky view usually takes under a minute. Indoors or next to buildings it may never come.

## Compass in the GPS Module

The compass in a GPS module connects to I2C4 on the same connector. PX4 probes the external I2C buses at boot (`SENS_EXT_I2C_PRB` = 1) and starts any supported compass it finds, such as the IST8310, QMC5883 or RM3100.

- The compass is calibrated together with the onboard one (see [Sensor Calibration](sensor-calibration)).
- If the GPS module is not mounted facing forward, set its rotation (`CAL_MAGn_ROT`). QGroundControl detects the rotation during calibration for most mounts.
- Mount the module on a mast, away from motors, power wiring and the flight controller.

:::note External compass for variable 5 V loads
The onboard compass works well in typical builds, but a 5 V load that varies a lot in flight can throw off its readings. In complex systems with highly variable 5 V loads, use a GPS + compass module and make its compass the primary one. See [Magnetometer](../hardware/magnetometer#magnetic-interference).
:::

## RTK GPS

RTK receivers such as the u-blox ZED-F9P give centimetre-level positions when they receive correction data from a base station. The 305ap supports RTK through the standard PX4 path:

1. Connect the F9P to the GPS connector. PX4 recognises it and configures it as an RTK rover.
2. Give QGroundControl a correction source: a base station receiver (such as a second F9P) plugged into the ground computer, or an NTRIP correction service. Both are set up in QGroundControl's **Application Settings** (RTK section; the layout varies between QGroundControl versions).
3. QGroundControl sends the corrections to the vehicle over the telemetry link (MAVLink `GPS_RTCM_DATA`), and PX4 forwards them to the receiver.

QGroundControl shows the fix type as **RTK Float** and then **RTK Fixed** once the receiver has resolved the corrections.

:::tip
Corrections take telemetry bandwidth. Use a radio link with spare capacity (at least 57600 baud) when sending RTK corrections.
:::

## Two GPS Receivers

A second serial GPS can run on another serial port, for example TELEM 2:

| Parameter | Value |
|---|---|
| `GPS_2_CONFIG` | TELEM 2 |
| `SER_TEL2_BAUD` | Receiver baud rate, or `0` for auto |

PX4 then selects or blends between the two. `SENS_GNSS_PRIME` sets the preferred receiver: `0` for the main serial GPS, `1` for the secondary serial GPS, or a DroneCAN node ID. With two receivers, MAVLink reports them as `GPS_RAW_INT` (the preferred receiver, or else the first to publish) and `GPS2_RAW` (the other).

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| No GPS in QGroundControl | TX/RX swapped, or no power | Check the cable pinout against the table above |
| Never gets a fix | No sky view, or interference | Test outdoors; move the GPS away from the VTX and USB cables |
| Fix drops when the video transmitter is on | Radio interference | Increase distance between the GPS and the VTX antenna |
| "Compass inconsistent" warnings | Compass orientation or calibration | Recalibrate; check `CAL_MAGn_ROT` for the GPS compass |
