---
id: internal-bus-reference
title: Internal Bus Reference
sidebar_label: Internal Bus Reference
description: MCU GPIO assignments and internal bus wiring for the avian automata 305ap.
---

# Internal Bus Reference

MCU-level GPIO assignments and internal bus connections. Use this as the signal-level reference when developing firmware, writing drivers, or diagnosing low-level peripheral issues.

## UARTs

| UART | `ttyS` | Function | TX Pin | RX Pin | RTS | CTS | Notes |
|---|---|---|---|---|---|---|---|
| USART1 | ttyS0 | Motor Telemetry RX | — | PA9 | — | — | SINGLEWIRE + SWAP: physical TX pin (PA9) used as RX. PA10 is CAN1 STB. |
| USART2 | ttyS1 | VTX / TEL3 | PD5 | PD6 | — | — | |
| USART3 | ttyS2 | VTX SBUS / Debug Console | PD8 | — | — | — | TX only. PD9 is RED LED — no RX possible. |
| UART4 | ttyS3 | Telem 1 | PA0 | PA1 | PB14 | PB0 | HW flow control supported (`MAV_0_FLOW_CTRL`). |
| UART5 | ttyS4 | RC Input | PB13 | — | — | — | SINGLEWIRE, no swap. PB12 is GREEN LED — cannot be used as UART5 RTS. |
| USART6 | ttyS5 | GPS | PC6 | PC7 | — | — | |
| UART7 | ttyS6 | Telem 2 | PE8 | PE7 | PE9 | PE10 | HW flow control enabled by default. |

:::note SINGLEWIRE ports
USART1 and UART5 use single-wire half-duplex mode on the physical TX pin. USART1 also uses the SWAP setting (`dshot telemetry -x`); UART5 does not. Do not wire a normal RX signal to these ports.
:::

## I2C

| Bus | Device | SCL | SDA | Notes |
|---|---|---|---|---|
| I2C1 | MMC5983MA (Mag, internal) | PB8 | PB9 | Internal only — not on any connector |
| I2C2 | External I2C connector | PB10 | PB11 | User-accessible, pulled up to 3.3 V |
| I2C4 | GPS connector | PD12 | PD13 | Paired with USART6; pulled up to 3.3 V |

## SPI

| Bus | Device | CS | SCLK | MOSI | MISO | INT1 (DRDY) | INT2 |
|---|---|---|---|---|---|---|---|
| SPI1 | BMP581 (Baro) | PD11 | PA5 | PD7 | PA6 | PB15 | — |
| SPI2 | LSM6DSV / ICM-45686 #1 (IMU) | PE4 | PD3 | PC1 | PC2\_C | PE0 | PD4 |
| SPI3 | LSM6DSV / ICM-45686 #2 (IMU) | PA14 | PB3 | PB2 | PB4 | PC15 | PA13 |
| SPI4 | External SPI connector | PC13, PC14 | PE2 | PE6 | PE13 | — | — |

## CAN

| Port | TX | RX | STB Enable | Transceiver |
|---|---|---|---|---|
| FDCAN1 | PD1 | PD0 | PA10 (active-low) | SIT1044T/K/3 |
| FDCAN2 | PB6 | PB5 | PE1 (active-low) | SIT1044T/K/3 |

## PWM Outputs

| Channel | Timer | GPIO |
|---|---|---|
| Motor 1 | TIM1 CH1 | PA8 |
| Motor 2 | TIM1 CH2 | PE11 |
| Motor 3 | TIM2 CH1 | PA15 |
| Motor 4 | TIM2 CH3 | PA2 |
| Motor 5 | TIM3 CH2 | PA7 |
| Motor 6 | TIM3 CH4 | PB1 |
| Motor 7 | TIM4 CH2 | PB7 |
| Motor 8 | TIM4 CH3 | PD14 |

## LEDs

Active-low: GPIO LOW = ON, GPIO HIGH = OFF.

| Color | GPIO | PX4 Role |
|---|---|---|
| Red | PD9 | Error / boot state |
| Green | PB12 | GPS lock / armed state |
| Blue | PE12 | Flight mode indicator |

## Buzzer

| Parameter | Value |
|---|---|
| GPIO | PA3 |
| Timer | TIM5 CH4 |

## SD Card (SDMMC1)

| Signal | Pin |
|---|---|
| CMD | PD2 |
| CLK | PC12 |
| DAT0 | PC8 |
| DAT1 | PC9 |
| DAT2 | PC10 |
| DAT3 | PC11 |
| Card Detect | PE3 |

## USB

| Signal | Pin |
|---|---|
| D+ | PA12 |
| D− | PA11 |

No VBUS detection line. Board cannot detect whether USB power is present.

## ADC / Power Sensing

| Signal | GPIO | ADC Channel | Notes |
|---|---|---|---|
| VSENSE (battery voltage) | PC4 | ADC1 INP4 | 1:11 resistor divider |
| ASENSE (current) | PC3\_C | ADC3 INP1 | External current sensor required. Read via ADC1 CH13 (PC3) using the STM32H7 SYSCFG\_PMCR analog switch (PC3SO default = closed). |

## Unconnected (NC) Pins

`PA4`, `PC0`, `PC5`, `PD10`, `PD15`, `PE5`, `PE14`, `PE15`
