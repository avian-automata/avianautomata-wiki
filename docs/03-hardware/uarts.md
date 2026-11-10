---
id: uarts
title: UARTs
sidebar_label: UARTs
---

# UARTs

The 305ap exposes 7 hardware UARTs. Each maps to a NuttX `ttyS` device and a PX4 serial port name.

## Port Map

| UART | `ttyS` | PX4 Port | Default Function | Baud |
|---|---|---|---|---|
| USART1 | ttyS0 | — | Motor Telemetry RX (started from SD card) | 115200 |
| USART2 | ttyS1 | TEL3 | VTX / MSP OSD | 115200 |
| USART3 | ttyS2 | — | Debug console (VTX connector, `VTX_SBUS` pin) | — |
| UART4 | ttyS3 | TEL1 | Telemetry 1 / MAVLink | 57600 |
| UART5 | ttyS4 | RC | RC Input | — |
| USART6 | ttyS5 | GPS1 | GPS | Auto |
| UART7 | ttyS6 | TEL2 | Telemetry 2 (off by default) | 921600 |

## Port Details

### USART1 — Motor Telemetry RX (ttyS0)

**Mode:** SINGLEWIRE + SWAP

The physical TX pin (PA9) is reconfigured as the RX line. This is a receive-only port used to collect ESC telemetry (e.g. BLHeli32/AM32 serial telemetry).

- RX pin: PA9
- PA10 is repurposed as the FDCAN1 standby (STB) enable and is **not** available as USART1 RX/TX

### USART2 — VTX / TEL3 (ttyS1)

Standard full-duplex UART. Assigned as TEL3 by default and pre-configured for the MSP OSD driver at 115200 baud, which paints an OSD overlay onto goggles that support MSP DisplayPort (HDZero, Walksnail, most analog VTX-with-MSP setups).

- TX: PD5, RX: PD6
- Defaults set in firmware: `MSP_OSD_CONFIG=103` (TEL3), `SER_TEL3_BAUD=115200`
- To disable the OSD driver at runtime, set `MSP_OSD_CONFIG=0` in QGroundControl and reboot

### USART3 — VTX SBUS / Debug Console (ttyS2)

TX-only port. PD9 (the red LED) occupies the physical RX pin position, so no RX is possible.

- TX: PD8
- Used as the NuttX debug console (boot messages appear here)

### UART4 — Telem 1 (ttyS3)

Primary telemetry port. Supports hardware flow control.

- TX: PA0, RX: PA1
- RTS: PB14, CTS: PB0
- Hardware flow control can be toggled from QGroundControl via **`MAV_0_FLOW_CTRL`** (no rebuild required)

### UART5 — RC Input (ttyS4)

**Mode:** SINGLEWIRE (no swap)

Single-wire half-duplex RC input port. The physical TX pin (PB13) is the signal line. PB12 is the green LED and is not available.

- Signal pin: PB13
- Supports SBUS (inverted), CRSF, and other single-wire protocols via PX4's RC driver

:::tip CRSF/ELRS on Telem 1
CRSF/ELRS RC input works on this port. For CRSF telemetry back to the transmitter, which needs a full-duplex UART, connect to **UART4 (Telem 1)** instead and set `RC_CRSF_PRT_CFG` = TELEM 1 (see [RC Setup](../configuration/rc-setup)).
:::

### USART6 — GPS (ttyS5)

Standard full-duplex UART. Paired with I2C4 on the GPS connector for combined GPS+compass modules.

- TX: PC6, RX: PC7
- I2C4 (PD12/PD13) shares the same connector

### UART7 — Telem 2 (ttyS6)

Secondary telemetry port with hardware flow control enabled by default. Intended for companion computers.

- TX: PE8, RX: PE7
- RTS: PE9, CTS: PE10

## Changing Port Functions

Port functions are configurable from QGroundControl without a firmware rebuild. In PX4 each function has a parameter that selects its port (TELEM 1, TELEM 2, TELEM 3, GPS 1, RC):

| Function | Port parameter |
|---|---|
| MAVLink | `MAV_0_CONFIG`, `MAV_1_CONFIG`, `MAV_2_CONFIG` |
| GPS | `GPS_1_CONFIG`, `GPS_2_CONFIG` |
| CRSF RC input | `RC_CRSF_PRT_CFG` |
| ROS 2 (uXRCE-DDS) | `UXRCE_DDS_CFG` |
| MSP OSD | `MSP_OSD_CONFIG` |

1. Go to **Vehicle Setup → Parameters**
2. Set the function's port parameter (for example `RC_CRSF_PRT_CFG` = TELEM 1 for CRSF on Telem 1), and disable any other function on that port
3. Reboot. Port settings such as the baud rate (`SER_TEL1_BAUD`, ...) appear after the reboot.

USART1 (motor telemetry) and USART3 (console) have no port name; see [ESC Configuration](../configuration/esc-configuration#esc-telemetry) for starting ESC telemetry on USART1.
