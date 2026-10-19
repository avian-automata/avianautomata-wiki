---
id: dronecan
title: DroneCAN
sidebar_label: DroneCAN
---

# DroneCAN

The 305ap runs PX4's DroneCAN (UAVCAN v0) driver on both CAN ports. DroneCAN connects GPS modules, compasses, airspeed sensors, optical flow, power monitors and ESCs over a robust two-wire bus. For connector wiring and termination, see [CAN Bus](../hardware/can-bus).

## CAN Ports and PX4 Interfaces

| Connector | Controller | PX4 interface | Shown in `uavcan status` as |
|---|---|---|---|
| CAN 1 (U18) | FDCAN1 | 0 | CAN1 |
| CAN 2 (U19) | FDCAN2 | 1 | CAN2 |

Both ports run as one DroneCAN network at 1 Mbit/s (`UAVCAN_BITRATE`) with the flight controller as node 1 (`UAVCAN_NODE_ID`). Devices can be on either port. A device can also be connected to both ports for redundancy, if it supports two interfaces.

## Enabling DroneCAN

DroneCAN is off by default. Set `UAVCAN_ENABLE` and reboot:

| Value | Mode | Use when |
|---|---|---|
| `0` | Disabled (default) | No CAN devices |
| `1` | Sensors, manual config | Sensor nodes with fixed node IDs |
| `2` | Sensors, automatic config | Sensor nodes; the FC also assigns node IDs (dynamic node allocation) and can update node firmware |
| `3` | Sensors and ESCs, automatic config | DroneCAN ESCs or servos |

Mode 2 suits most sensor setups. Use 3 only when the flight controller should drive actuators over CAN.

## Sensors

Each sensor type has a subscription parameter. Enable the ones you use, then reboot:

| Sensor | Parameter | Default |
|---|---|---|
| GPS | `UAVCAN_SUB_GPS` | On |
| Magnetometer | `UAVCAN_SUB_MAG` | On |
| Barometer | `UAVCAN_SUB_BARO` | Off |
| Airspeed | `UAVCAN_SUB_ASPD` | Off |
| Optical flow | `UAVCAN_SUB_FLOW` | Off |
| Rangefinder | `UAVCAN_SUB_RNG` | Off |
| Battery / power monitor | `UAVCAN_SUB_BAT` | Off |

DroneCAN sensors then appear like onboard ones: compasses in the calibration page, the GPS in the toolbar, flow and rangefinder data in the logs.

### DroneCAN GPS

A DroneCAN GPS works alongside, or instead of, a serial GPS. With two receivers, MAVLink reports one as `GPS_RAW_INT` (the preferred receiver, or else the first to publish after boot) and the other as `GPS2_RAW`. To prefer the DroneCAN GPS, set `SENS_GNSS_PRIME` to its node ID.

If the GPS module contains a compass, it is used through `UAVCAN_SUB_MAG`. A DroneCAN GPS + compass on a mast is a good way to keep the compass away from the vehicle's currents (see [Magnetometer](../hardware/magnetometer#magnetic-interference)).

## ESCs and Servos

With `UAVCAN_ENABLE` = 3, the **Actuators** page in QGroundControl shows a **DroneCAN ESCs** group (and **Servos**). Assign motors to ESC channels there (`UAVCAN_EC_FUNCn`), the same way as for the PWM outputs.

| Parameter | Default | Meaning |
|---|---|---|
| `UAVCAN_EC_MINn` / `UAVCAN_EC_MAXn` | 1 / 8191 | Command range sent to ESC *n* |
| `UAVCAN_ESC_RTMAX` | 400 Hz | Maximum ESC command rate |
| `UAVCAN_ESC_IFACE` | 255 (all) | Bitmask of CAN interfaces that carry ESC commands: bit 0 = CAN 1, bit 1 = CAN 2 |

Each ESC needs its **ESC index** set to match the channel it should follow (ESC index 0 = channel 1). This is set in the ESC's own configuration, usually from its configuration tool or as a DroneCAN parameter.

ESC status (RPM, voltage, current, temperature) is reported back to PX4 and appears in MAVLink `ESC_STATUS`. PX4 only includes reports newer than 100 ms in that message, so ESCs should send status at 10 Hz or faster (most do).

## Node IDs

Every device on the bus needs a unique node ID from 1 to 125. The flight controller is node 1.

- With `UAVCAN_ENABLE` ≥ 2, devices set to request a dynamic node ID get one from the flight controller automatically.
- Devices with fixed node IDs must not use an ID that another device already has.

## Updating Node Firmware

With `UAVCAN_ENABLE` ≥ 2, the flight controller can update DroneCAN devices. Copy the device's firmware file to the `ufw` folder on the SD card (`/fs/microsd/ufw/`) and reboot. Devices whose firmware is older than the file are updated at startup. Check the device maker's instructions for the file naming they expect.

## Checking the Bus

In the MAVLink Console:

```
uavcan status
```

shows each interface's frame counters and errors, the ESC output configuration, and the list of online nodes with their health. A device that is wired correctly but missing from the list usually has a wrong bitrate or a duplicate node ID.

## What Has Been Tested

The 305ap's DroneCAN path is tested on the production test jig, on both CAN ports, with emulated devices:

- GNSS (`Fix2`) through to MAVLink `GPS_RAW_INT` / `GPS2_RAW`
- Magnetometer (`MagneticFieldStrength2`) and optical flow (`com.hex.equipment.flow.Measurement`)
- Four ESCs: commands from PX4's motor test, and their status back in `ESC_STATUS`
