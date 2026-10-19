---
id: rc-setup
title: RC Setup
sidebar_label: RC Setup
---

# RC Setup

The 305ap supports several RC protocols. Which port and protocol you use depends on your receiver type.

## Option A — RC IN Port (SBUS, CRSF/ELRS, and Others)

**Port:** UART5 (ttyS4), labeled **RC IN**

UART5 is configured as a single-wire half-duplex port. The signal line is PB13. Connect your receiver's output to this pin.

This port works with:
- SBUS (e.g. FrSky, Futaba)
- CRSF / ELRS (receiver TX to the RC IN signal pin)
- DSM/DSMX (via spektrum satellite adapter)
- Single-wire PPM

No QGC configuration is needed. With `RC_INPUT_PROTO` = Auto (the default), the RC driver detects the protocol automatically. CRSF RC input on this port has been verified on the production test jig.

:::note CRSF telemetry
CRSF telemetry back to the transmitter (link statistics, battery) needs a full-duplex UART. It has not been verified on the single-wire RC IN port. If you need it, use Option B.
:::

## Option B — CRSF / ELRS with Telemetry (Telem 1)

**Port:** UART4 (ttyS3), labeled **Telem 1**

Connect your receiver's TX to the FC's RX (PA1) and the receiver's RX to the FC's TX (PA0).

### Configuration in QGroundControl

1. Go to **Vehicle Setup → Parameters**
2. Free the port: set `MAV_0_CONFIG` = **Disabled** (MAVLink uses Telem 1 by default)
3. Set `RC_CRSF_PRT_CFG` = **TELEM 1**
4. Optionally set `RC_CRSF_TEL_EN` = 1 to send telemetry to the transmitter
5. Reboot the flight controller

The CRSF baud rate (420000) is set automatically by the driver.

:::tip ELRS
ExpressLRS receivers use CRSF protocol. The setup is identical to CRSF above.
:::

## Option C — CRSF / ELRS with Telemetry (Telem 2)

**Port:** UART7 (ttyS6), labeled **Telem 2**

Same procedure as Option B but set `RC_CRSF_PRT_CFG` = **TELEM 2** instead. Telem 2 has no function by default, so there is no MAVLink instance to disable.

## Verifying RC Input

In the MAVLink Console (QGC → Analyze Tools → MAVLink Console):

```
listener input_rc
```

This prints raw RC channel values. Confirm all channels respond correctly to stick movements.

## Flight Mode Assignment

After RC calibration in QGC, assign a switch to flight modes:

1. **Vehicle Setup → Flight Modes**
2. Assign the mode channel to your desired switch
3. Configure at least: Stabilized, Position, Return
