---
id: gpio-reference
title: GPIO Reference
sidebar_label: GPIO Reference
---

# GPIO Reference

Every STM32H7 pin used on the 305ap, sorted by port and pin. For the same information grouped by bus and peripheral, see the [Internal Bus Reference](../hardware/internal-bus-reference).

## Port A

| Pin | Function | Peripheral | Notes |
|---|---|---|---|
| PA0 | TELEM 1 TX | UART4 TX | |
| PA1 | TELEM 1 RX | UART4 RX | |
| PA2 | MOTOR 4 | TIM2 CH3 | |
| PA3 | Buzzer | TIM5 CH4 | Tone alarm |
| PA4 | — | — | Not connected |
| PA5 | Baro SCLK | SPI1 SCK | BMP581 |
| PA6 | Baro MISO | SPI1 MISO | BMP581 |
| PA7 | MOTOR 5 | TIM3 CH2 | |
| PA8 | MOTOR 1 | TIM1 CH1 | |
| PA9 | Motor telemetry RX | USART1 | Single-wire (TX pin used as RX) |
| PA10 | CAN 1 transceiver standby | GPIO output | Low = transceiver active |
| PA11 | USB D− | OTG FS | |
| PA12 | USB D+ | OTG FS | |
| PA13 | IMU 2 INT2 | GPIO | |
| PA14 | IMU 2 CS | SPI3 CS | |
| PA15 | MOTOR 3 | TIM2 CH1 | |

## Port B

| Pin | Function | Peripheral | Notes |
|---|---|---|---|
| PB0 | TELEM 1 CTS | UART4 CTS | |
| PB1 | MOTOR 6 | TIM3 CH4 | |
| PB2 | IMU 2 MOSI | SPI3 MOSI | |
| PB3 | IMU 2 SCLK | SPI3 SCK | |
| PB4 | IMU 2 MISO | SPI3 MISO | |
| PB5 | CAN 2 RX | FDCAN2 RX | |
| PB6 | CAN 2 TX | FDCAN2 TX | |
| PB7 | MOTOR 7 | TIM4 CH2 | |
| PB8 | Mag SCL | I2C1 SCL | MMC5983MA |
| PB9 | Mag SDA | I2C1 SDA | MMC5983MA |
| PB10 | External I2C SCL | I2C2 SCL | U20 connector |
| PB11 | External I2C SDA | I2C2 SDA | U20 connector |
| PB12 | Green LED | GPIO output | Active low |
| PB13 | RC input | UART5 | Single-wire |
| PB14 | TELEM 1 RTS | UART4 RTS | |
| PB15 | Baro DRDY | GPIO input | BMP581 |

## Port C

| Pin | Function | Peripheral | Notes |
|---|---|---|---|
| PC0 | — | — | Not connected |
| PC1 | IMU 1 MOSI | SPI2 MOSI | |
| PC2\_C | IMU 1 MISO | SPI2 MISO | |
| PC3\_C | Current sense input | ADC (ADC1 CH13 via PC3) | 1/11 divider; external current sensor |
| PC4 | Battery voltage | ADC1 CH4 | 1/11 divider (`BAT1_V_DIV` = 11) |
| PC5 | — | — | Not connected |
| PC6 | GPS TX | USART6 TX | |
| PC7 | GPS RX | USART6 RX | |
| PC8 | SD DAT0 | SDMMC1 | |
| PC9 | SD DAT1 | SDMMC1 | |
| PC10 | SD DAT2 | SDMMC1 | |
| PC11 | SD DAT3 | SDMMC1 | |
| PC12 | SD CLK | SDMMC1 | |
| PC13 | External SPI CS1 | GPIO output | CN7 connector |
| PC14 | External SPI CS2 | GPIO output | CN7 connector |
| PC15 | IMU 2 DRDY (INT1) | GPIO input | |

## Port D

| Pin | Function | Peripheral | Notes |
|---|---|---|---|
| PD0 | CAN 1 RX | FDCAN1 RX | |
| PD1 | CAN 1 TX | FDCAN1 TX | |
| PD2 | SD CMD | SDMMC1 | |
| PD3 | IMU 1 SCLK | SPI2 SCK | |
| PD4 | IMU 1 INT2 | GPIO | |
| PD5 | VTX TX | USART2 TX | MSP OSD (TELEM 3) |
| PD6 | VTX RX | USART2 RX | |
| PD7 | Baro MOSI | SPI1 MOSI | BMP581 |
| PD8 | VTX SBUS / console | USART3 TX | NuttX console, 57600 baud |
| PD9 | Red LED | GPIO output | Active low |
| PD10 | — | — | Not connected |
| PD11 | Baro CS | SPI1 CS | BMP581 |
| PD12 | GPS compass SCL | I2C4 SCL | GPS connector |
| PD13 | GPS compass SDA | I2C4 SDA | GPS connector |
| PD14 | MOTOR 8 | TIM4 CH3 | |
| PD15 | — | — | Not connected |

## Port E

| Pin | Function | Peripheral | Notes |
|---|---|---|---|
| PE0 | IMU 1 DRDY (INT1) | GPIO input | |
| PE1 | CAN 2 transceiver standby | GPIO output | Low = transceiver active |
| PE2 | External SPI SCLK | SPI4 SCK | CN7 connector |
| PE3 | SD card detect | GPIO input | |
| PE4 | IMU 1 CS | SPI2 CS | |
| PE5 | — | — | Not connected |
| PE6 | External SPI MOSI | SPI4 MOSI | CN7 connector |
| PE7 | TELEM 2 RX | UART7 RX | |
| PE8 | TELEM 2 TX | UART7 TX | |
| PE9 | TELEM 2 RTS | UART7 RTS | |
| PE10 | TELEM 2 CTS | UART7 CTS | |
| PE11 | MOTOR 2 | TIM1 CH2 | |
| PE12 | Blue LED | GPIO output | Active low |
| PE13 | External SPI MISO | SPI4 MISO | CN7 connector |
| PE14 | — | — | Not connected |
| PE15 | — | — | Not connected |

## Using GPIOs from the Console

The `gpio` command reads and writes pins from the MAVLink Console, which helps when checking hardware:

```
gpio read E3              # SD card detect (port letter and pin, without the "P")
gpio write B12 0 --force  # green LED on (active low)
```

`gpio` refuses pins whose setup differs from the board's startup configuration unless `--force` is given. Don't force pins that a running driver uses (motor outputs, buses, the CAN standby lines). The LEDs are safest to experiment with; the `led_control` command is the normal way to control them.
