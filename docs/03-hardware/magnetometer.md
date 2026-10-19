---
id: magnetometer
title: Magnetometer
sidebar_label: Magnetometer
---

# Magnetometer

## Hardware

The 305ap uses a **MEMSIC MMC5983MA** 3-axis magnetometer connected over I2C.

| Parameter | Value |
|---|---|
| Part | MEMSIC MMC5983MA |
| Bus | I2C1 |
| SCL | PB8 |
| SDA | PB9 |
| Power supply | 3.3 V LDO (quiet rail) |
| Bus | Internal only (not exposed on a connector) |

## Sensor Specifications

| Specification | Value |
|---|---|
| Full scale | ±8 G |
| Resolution | 18-bit |
| RMS noise | 0.4 mG |
| Heading accuracy | ~0.5° |
| Output data rate | Up to 1000 Hz |
| SET/RESET degaussing | Yes (built-in) |

The MMC5983MA is a high-quality onboard compass with notably lower noise and better heading accuracy than older magnetometers commonly found on hobby flight controllers. The built-in SET/RESET function periodically degausses the sensor. PX4 drives this automatically.

## I2C Bus Isolation

The onboard magnetometer sits on **I2C1** (PB8/PB9), which is dedicated to the internal compass only. The GPS connector exposes a separate **I2C4** (PD12/PD13) for an external compass integrated into the GPS module. This isolation ensures the internal and external compasses operate independently without bus contention.

## PX4 Integration

PX4 supports multiple simultaneous magnetometers and runs voting across them. With an external compass connected on I2C4 (typically via a GPS+compass combo module), PX4 will use both and prefer the external if it has better consistency.

**Relevant PX4 parameters:**

| Parameter | Description |
|---|---|
| `EKF2_MAG_TYPE` | Magnetometer fusion mode (0=auto, 1=airspeed, 2=non-moving) |
| `EKF2_MAG_NOISE` | Expected mag measurement noise (Gauss) |
| `CAL_MAG0_ROT` | Rotation offset for internal mag (if board is mounted non-standard) |
| `CAL_MAG1_ROT` | Rotation offset for external mag |

## Calibration

Compass calibration is required before first flight and should be repeated when:
- Operating in a new geographic location
- Hardware near the FC has changed (new ESCs, motors, wiring)
- Compass behavior seems erratic

To calibrate in QGroundControl:

1. Go to **Vehicle Setup → Sensors → Compass**
2. Click **Start** and rotate the vehicle through all axes as prompted
3. Confirm the calibration passes

For best results, calibrate outdoors away from metal structures, vehicles, and power lines.

## Magnetic Interference

Common sources of interference to keep away from the FC:
- High-current motor wires (carry significant dynamic magnetic fields at throttle changes)
- High-current power distribution buses
- Large permanent magnets (speaker magnets, etc.)

:::note 5 V load and the onboard compass
The onboard compass works well in typical builds. Current drawn from the board's 5 V rail (the +5V pins on the connectors) also produces a field at the compass, so a 5 V load that varies substantially in flight can throw off its readings: servos, a companion computer, a video transmitter, or other peripherals that switch on and off. A steady 5 V load is not a problem. It acts as a fixed offset that compass calibration removes, so calibrate with your usual 5 V peripherals connected and powered.

In complex systems with highly variable 5 V loads, use an external GPS + compass module on the GPS port (CN4, I2C4) and make it the primary compass: in QGroundControl under **Vehicle Setup → Sensors → Compass**, give the external compass the higher priority or disable the internal one.
:::

If using an onboard compass is insufficient for your application, use a GPS+compass module mounted on a mast away from the motors, connected via I2C4.
