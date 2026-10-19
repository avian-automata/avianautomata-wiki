---
id: filter-tuning
title: Filter Tuning
sidebar_label: Filter Tuning
---

# Filter Tuning

Gyro filters remove motor and propeller noise before it reaches the rate controller. Too little filtering sends noise to the motors (hot motors, rough flight); too much adds delay and limits how high the gains can go. Tune filters before or together with the rate loop.

## Default Filters

| Parameter | Default | Filter |
|---|---|---|
| `IMU_GYRO_CUTOFF` | 40 Hz | Low-pass on the gyro data used by the rate loop |
| `IMU_DGYRO_CUTOFF` | 20 Hz | Low-pass on the D term (angular acceleration) |
| `IMU_GYRO_NF0_FRQ` | 0 (off) | Fixed notch filter |
| `IMU_GYRO_DNF_EN` | 0 (off) | Dynamic notch filters from ESC RPM or FFT |

The defaults are conservative and suit most mid-size multirotors.

## Find the Noise First

1. Fly a short hover with some throttle changes. Adding the **High rate** logging profile (`SDLOG_PROFILE` bit 4) gives better spectra.
2. Open the log in [Flight Review](https://review.px4.io) and look at the **gyro spectral density** plots.
3. Look for peaks: a band that moves with throttle is motor/propeller noise; a fixed peak is usually a frame resonance.

## Options

### Low-Pass Cutoffs

Lower `IMU_GYRO_CUTOFF` and `IMU_DGYRO_CUTOFF` if noise is broad and high. Raise them (gradually) on a clean, well-mounted vehicle to reduce delay and allow higher gains.

### Fixed Notch

For a resonance at one frequency (frame or arm flex), set `IMU_GYRO_NF0_FRQ` to its centre and `IMU_GYRO_NF0_BW` to its width.

### Dynamic Notch from ESC RPM

The most effective filter tracks the actual motor speed. `IMU_GYRO_DNF_EN` with the ESC RPM option places notches on each motor's frequency and its harmonics (`IMU_GYRO_DNF_HMC`).

It needs RPM data from the ESCs. On the 305ap that comes from either:

- **Serial ESC telemetry** on the motor connector (BLHeli32/AM32), started from the SD card (see [ESC Configuration](../configuration/esc-configuration#esc-telemetry)), or
- **DroneCAN ESCs** reporting status (see [DroneCAN](../advanced/dronecan)).

Serial telemetry updates each motor in turn, so its RPM data is slower than bidirectional DShot (which has not been verified on the 305ap yet).

### Dynamic Notch from FFT

Without RPM data, PX4 can find the dominant noise frequency itself with an FFT of the gyro data. Enable it with `IMU_GYRO_FFT_EN` = 1 and the FFT option in `IMU_GYRO_DNF_EN`. It works best when one noise peak dominates.

## Checking the Result

Compare the gyro spectra before and after: the peaks should be reduced, and motor outputs in the log should look smoother. Then revisit the rate gains, since less filter delay often allows higher D.

See the [PX4 filter tuning guide](https://docs.px4.io/main/en/config_mc/filter_tuning.html) for details.
