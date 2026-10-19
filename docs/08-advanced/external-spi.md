---
id: external-spi
title: External SPI
sidebar_label: External SPI
---

# External SPI

The **External SPI** connector (CN7) brings out SPI4 with two chip selects, for sensors that need SPI's speed, such as optical flow and barometers.

## Connector

| Pin | Signal | MCU |
|---|---|---|
| 1 | +5V | — |
| 2 | EX\_SPI\_SCK | SPI4 SCLK (PE2) |
| 3 | EX\_SPI\_MISO | SPI4 MISO (PE13) |
| 4 | EX\_SPI\_MOSI | SPI4 MOSI (PE6) |
| 5 | EX\_SPI\_CS1 | SPI4 CS1 (PC13) |
| 6 | EX\_SPI\_CS2 | SPI4 CS2 (PC14) |
| 7 | GND | — |

The signal lines are **3.3 V logic**. Pin 1 supplies 5 V for the device's power; use a device with its own 3.3 V regulator, or a level-compatible module. Keep the cable short for clean signals at sensor clock rates.

## Drivers in the Standard Firmware

| Device | Driver | Typical chip select |
|---|---|---|
| PMW3901 optical flow | `pmw3901` | CS1 |
| MS5611 barometer | `ms5611` | CS2 |

Both have been tested on this port on the production test jig. Other PX4 SPI sensor drivers can be used with a [custom build](custom-firmware) that includes them.

## Starting a Device

External SPI devices are not started automatically. Start them with the driver's external-bus options:

```sh
pmw3901 -S -b 1 -c 1 start      # optical flow on CS1
ms5611 -S -b 1 -c 2 start       # barometer on CS2
```

| Option | Meaning |
|---|---|
| `-S` | External SPI bus |
| `-b 1` | First external SPI bus (SPI4, the only one on the 305ap) |
| `-c 1` / `-c 2` | Chip select CS1 / CS2 |

The driver prints the device and bus when it starts, or `no instance started` if the device didn't answer.

To start a device at every boot, add the line to `/fs/microsd/etc/extras.txt` on the SD card (see [Custom Firmware](custom-firmware)).

## Checking the Device

```
listener sensor_optical_flow
listener sensor_baro
```

The device's `device_id` decodes to bus type SPI, bus 4. A second barometer is used by PX4's sensor voting alongside the onboard BMP581.

## Optical Flow Setup

After the PMW3901 is running, set it up for position estimation:

- Mount it facing down, and set `SENS_FLOW_ROT` if it is rotated.
- Enable optical flow fusion in EKF2 (`EKF2_OF_CTRL` = 1).
- A downward rangefinder improves flow-based position hold considerably.

See the [PX4 optical flow documentation](https://docs.px4.io/main/en/sensor/optical_flow.html).
