---
id: connectors-pinout
title: Connectors & Pinout
sidebar_label: Connectors & Pinout
description: Physical connector pinouts for the avian automata 305ap flight controller.
---

# Connectors & Pinout

All board-to-wire connectors use **JST-GH** style. Pin 1 is marked on the connector housing.

## Connector Overview

| Connector | Ref | Pins | Function |
|---|---|---|---|
| Power | CN1 | 6 | Main battery input |
| TELEM 1 | CN2 | 6 | Telemetry / MAVLink with flow control |
| TELEM 2 | CN3 | 6 | Telemetry / companion computer with flow control |
| Basic GPS | CN4 | 6 | GPS UART + I2C compass |
| CAN 1 | U18 | 4 | FDCAN1 bus |
| CAN 2 | U19 | 4 | FDCAN2 bus |
| External I2C | U20 | 4 | I2C expansion |
| External SPI | CN7 | 7 | SPI expansion |
| Motors | U21 | 12 | 8× motor outputs + ESC telemetry + current sense |
| VTX | CN6 | 6 | Video transmitter power and control |
| RC IN | U22 | 3 | RC receiver input |
| USB-C | USBC1 | — | USB 2.0 FS device / power |

:::note +5V pins and the onboard compass
The +5V pins on all connectors come from the board's 5 V rail. A load on them that varies substantially in flight (servos, companion computer, video transmitter) can throw off the onboard compass. For systems like that, use an external GPS + compass module. See [Magnetometer](../hardware/magnetometer#magnetic-interference).
:::

---

## Power — CN1

**6-pin JST-GH.** Main battery input.

| Pin | Signal |
|---|---|
| 1 | VBAT\_IN |
| 2 | VBAT\_IN |
| 3 | VBAT\_IN |
| 4 | GND |
| 5 | GND |
| 6 | GND |

Pins 1–3 are paralleled for current capacity and lower contact resistance. Pins 4–6 are paralleled ground. This connector feeds the board's protected VBAT input (fuse → TVS → ideal-diode front end).

**Input voltage:** 2S–6S LiPo / Li-ion (6.4–25.2 V). Absolute maximum 35 V.

---

## TELEM 1 — CN2

**6-pin JST-GH.** Primary telemetry port with hardware flow control.

| Pin | Signal | MCU |
|---|---|---|
| 1 | +5V | — |
| 2 | TM1\_UART\_TX | UART4 TX (PA0) |
| 3 | TM1\_UART\_RX | UART4 RX (PA1) |
| 4 | TM1\_UART\_CTS | UART4 CTS (PB0) |
| 5 | TM1\_UART\_RTS | UART4 RTS (PB14) |
| 6 | GND | — |

Default function: MAVLink (TEL1). Hardware flow control is supported. Enable via `MAV_0_FLOW_CTRL` in QGroundControl without a firmware rebuild.

---

## TELEM 2 — CN3

**6-pin JST-GH.** Secondary telemetry port with hardware flow control.

| Pin | Signal | MCU |
|---|---|---|
| 1 | +5V | — |
| 2 | TM2\_UART\_TX | UART7 TX (PE8) |
| 3 | TM2\_UART\_RX | UART7 RX (PE7) |
| 4 | TM2\_UART\_CTS | UART7 CTS (PE10) |
| 5 | TM2\_UART\_RTS | UART7 RTS (PE9) |
| 6 | GND | — |

Default function: TEL2. Commonly used for companion computers. Hardware flow control enabled by default.

---

## Basic GPS — CN4

**6-pin JST-GH.** UART GPS with I2C compass support.

| Pin | Signal | MCU |
|---|---|---|
| 1 | +5V | — |
| 2 | GPS\_UART\_TX | USART6 TX (PC6) |
| 3 | GPS\_UART\_RX | USART6 RX (PC7) |
| 4 | GPS\_I2C\_SCL | I2C4 SCL (PD12) |
| 5 | GPS\_I2C\_SDA | I2C4 SDA (PD13) |
| 6 | GND | — |

I2C lines are pulled up to 3.3 V on-board. Supports UART GPS modules with an integrated compass (connect compass via I2C4). Compatible with M8N, M9N, F9P, and similar modules using a standard 6-pin GPS cable.

:::note GPS port compatibility
This is a 6-pin port, not the full 10-pin Pixhawk GPS+safety-switch port. GPS modules that include a buzzer, safety switch, or LED typically use the 10-pin format and are not directly compatible.
:::

---

## CAN 1 — U18

**4-pin JST-GH.** FDCAN1 bus.

| Pin | Signal |
|---|---|
| 1 | +5V |
| 2 | CAN1\_H |
| 3 | CAN1\_L |
| 4 | GND |

:::note Onboard termination
The 305ap includes two 120 Ω termination resistors onboard for this port. The bus is fully terminated at the FC. **Do not add any external termination resistors** — doing so will lower bus impedance below spec and cause signal integrity issues.
:::

---

## CAN 2 — U19

**4-pin JST-GH.** FDCAN2 bus.

| Pin | Signal |
|---|---|
| 1 | +5V |
| 2 | CAN2\_H |
| 3 | CAN2\_L |
| 4 | GND |

Same termination applies: two 120 Ω resistors onboard, bus fully terminated at the FC. Do not add external terminators. See [CANBUS](can-bus) for DroneCAN configuration.

---

## External I2C — U20

**4-pin JST-GH.** General-purpose I2C expansion.

| Pin | Signal | MCU |
|---|---|---|
| 1 | +5V | — |
| 2 | EX\_I2C\_SCL | I2C2 SCL (PB10) |
| 3 | EX\_I2C\_SDA | I2C2 SDA (PB11) |
| 4 | GND | — |

Signal lines pulled up to 3.3 V on-board. I2C data is 3.3 V logic. Do not connect 5 V I2C devices without level shifting.

---

## External SPI — CN7

**7-pin JST-GH.** SPI expansion with two chip selects.

| Pin | Signal | MCU |
|---|---|---|
| 1 | +5V | — |
| 2 | EX\_SPI\_SCK | SPI4 SCLK (PE2) |
| 3 | EX\_SPI\_MISO | SPI4 MISO (PE13) |
| 4 | EX\_SPI\_MOSI | SPI4 MOSI (PE6) |
| 5 | EX\_SPI\_CS1 | SPI4 CS1 (PC13) |
| 6 | EX\_SPI\_CS2 | SPI4 CS2 (PC14) |
| 7 | GND | — |

---

## Motors — U21

**12-pin JST-GH.** 8 motor outputs, ESC telemetry, and current sense.

| Pin | Signal | Notes |
|---|---|---|
| 1 | +5V | ESC logic / signal power |
| 2 | CURRENT\_SENSE | Analog current input (0–3.3 V) — connect to ESC or power module current output |
| 3 | MOTOR\_TELEM\_RX | USART1 RX (PA9) — ESC serial telemetry (BLHeli32 / AM32) |
| 4 | MOTOR1 | TIM1 CH1 (PA8) |
| 5 | MOTOR2 | TIM1 CH2 (PE11) |
| 6 | MOTOR3 | TIM2 CH1 (PA15) |
| 7 | MOTOR4 | TIM2 CH3 (PA2) |
| 8 | MOTOR5 | TIM3 CH2 (PA7) |
| 9 | MOTOR6 | TIM3 CH4 (PB1) |
| 10 | MOTOR7 | TIM4 CH2 (PB7) |
| 11 | MOTOR8 | TIM4 CH3 (PD14) |
| 12 | GND | — |

Motor outputs support PWM, OneShot, and DSHOT. ESC telemetry (pin 3) is single-wire receive-only via USART1 SINGLEWIRE+SWAP mode.

---

## VTX — CN6

**6-pin JST-GH.** Dedicated video transmitter power and control.

| Pin | Signal | Notes |
|---|---|---|
| 1 | +9V | Regulated from onboard AP63357 9 V buck, independent of 5 V rail |
| 2 | GND | — |
| 3 | VTX\_UART\_TX | USART2 TX (PD5) — MSP DisplayPort OSD to VTX/goggles (default) |
| 4 | VTX\_UART\_RX | USART2 RX (PD6) |
| 5 | GND | — |
| 6 | VTX\_SBUS | USART3 TX (PD8) — debug console output (TX only; PD9 is the red LED) |

The 9 V rail is available whenever the board is powered from battery. It is not available from USB power alone. On 2S, the battery is below 9 V, so the rail follows the battery voltage instead of regulating to 9 V.

:::warning Leave the SBUS wire unconnected
On common 6-pin digital VTX harnesses, pin 6 carries SBUS from the air unit into the flight controller. On the 305ap this pin is a USART3 output driven by the console, so both ends would drive the same wire. Do not connect that wire.
:::

:::note MSP OSD ships enabled
Stock firmware runs `msp_osd` on USART2 at 115200 baud (`MSP_OSD_CONFIG=103`, TELEM 3). Compatible goggles receive an OSD overlay as soon as the FC boots — no additional configuration needed. Note that the on-screen battery reading is **average per-cell voltage**, not pack voltage (3.7-3.8 V/cell is normal at rest).
:::

---

## RC IN — U22

**3-pin JST-GH.** RC receiver input.

| Pin | Signal | Notes |
|---|---|---|
| 1 | +5V | Receiver power |
| 2 | RC\_IN | UART5 (PB13) — SBUS, CRSF, or other single-wire serial protocol (PPM is not supported) |
| 3 | GND | — |

Single-wire input via UART5 in half-duplex mode on its TX pin, with no RX/TX swap. CRSF and uninverted ELRS are fully supported. For SBUS, PX4 uses hardware UART inversion on the STM32H7. For CRSF/ELRS receivers that need a full-duplex UART, connect to TELEM 1 or TELEM 2 and reassign the port function in QGroundControl.

---

## USB-C — USBC1

Standard USB-C receptacle. USB 2.0 Full Speed device.

| Signal | Notes |
|---|---|
| D+ / D− | USB 2.0 FS data (PA12 / PA11) |
| VBUS | Feeds 5 V power mux (TPS2116) — board can run from USB alone |
| CC1 / CC2 | 5.1 kΩ to GND — identifies board as a standard 5 V USB sink |
| GND / Shield | — |

ESD protection via USBLC6-4SC6. No USB Power Delivery. 5 V only. No VBUS detection line in firmware.
