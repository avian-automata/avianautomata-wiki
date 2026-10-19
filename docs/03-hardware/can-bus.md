---
id: can-bus
title: CANBUS
sidebar_label: CANBUS
---

# CANBUS

The 305ap has two independent FDCAN ports supporting DroneCAN (formerly UAVCAN v1/v0) peripherals.

## Hardware

| Port | TX | RX | Enable (STB) | Transceiver |
|---|---|---|---|---|
| FDCAN1 | PD1 | PD0 | PA10 (active-low) | SIT1044T/K/3 |
| FDCAN2 | PB6 | PB5 | PE1 (active-low) | SIT1044T/K/3 |

The STB (standby) pin controls the CAN transceiver: low = normal operation, high = standby. The firmware drives both STB pins low at boot, so the transceivers are always active.

**PA10 note:** PA10 was repurposed from USART1 as the FDCAN1 STB line. It is not available as a UART pin.

### CAN Transceivers

Both CAN ports use **SIT1044T/K/3** transceivers, which are ISO 11898-2:2016 compliant CAN FD transceivers rated to 5 Mbps. Key features:

- 3.3 V / 5 V MCU I/O compatible
- Standby mode (controlled by STB pin)
- Undervoltage protection
- TX dominant timeout
- Thermal shutdown
- Unpowered-node bus disengagement (does not corrupt the bus when unpowered)

## Wiring

CAN uses a differential pair. Connect:

- CAN H to CAN H
- CAN L to CAN L
- GND to GND

The 305ap includes **two 120 Ω termination resistors onboard** for each CAN port. The bus is fully terminated at the FC. **Do not add any external termination resistors** on the cable run or at peripheral devices — extra resistors will lower the bus impedance below the ISO 11898-2 spec and degrade signal integrity.

:::warning
Do not wire CAN as a star. Run a single bus from the FC through each device in a line (daisy chain).
:::

## Enabling DroneCAN in PX4

1. In QGC → **Parameters**, set `UAVCAN_ENABLE` = **Sensors Automatic Config (2)** or higher
2. Reboot
3. PX4 will enumerate all DroneCAN devices on the bus and configure them automatically

`UAVCAN_ENABLE` covers both ports; devices can be on either. See [DroneCAN](../advanced/dronecan) for sensor subscriptions and ESC setup.

## Common DroneCAN Peripherals

| Device type | Examples |
|---|---|
| ESCs | Myxa, Zubax Kotleta20, Flame |
| GPS | Zubax GNSS, Holybro M9N DroneCAN |
| Airspeed | Zubax Baro, SDP3x via DroneCAN |
| Power monitor | Zubax Power, Pomegranate Systems |

## Troubleshooting

In the MAVLink Console:

```
uavcan status
```

Lists all enumerated DroneCAN nodes with their node IDs and health status.
