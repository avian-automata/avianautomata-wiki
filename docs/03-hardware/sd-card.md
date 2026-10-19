---
id: sd-card
title: SD Card
sidebar_label: SD Card
---

# SD Card

## Hardware

The 305ap uses a **native SDMMC1 (SDIO)** interface for the microSD card, not a slower SPI emulation.

| Signal | Pin |
|---|---|
| CMD | PD2 |
| CLK | PC12 |
| DAT0 | PC8 |
| DAT1 | PC9 |
| DAT2 | PC10 |
| DAT3 | PC11 |
| Card Detect | PE3 |

4-bit wide SDIO operation is supported, providing significantly higher throughput than SPI-mode SD cards. This is important for logging at high data rates without dropping log entries.

## Card Requirements

| Spec | Recommendation |
|---|---|
| Format | FAT32 (formatted by PX4 on first use if blank) |
| Capacity | 4 GB – 32 GB recommended |
| Speed class | Class 10 or faster (UHS-I U1 minimum) |
| Type | microSD or microSDHC |

Avoid very large cards (> 64 GB) as they typically use exFAT, which PX4 does not support. Cards larger than 32 GB must be reformatted to FAT32 before use.

:::warning Parameters require SD card
The 305ap has no FRAM. PX4 stores parameters at `/fs/microsd/params`. Without an SD card, parameter changes will not persist across reboots. Always insert a card before powering up.
:::

## PX4 SD Card Layout

After first boot with a blank card, PX4 creates the following directory structure:

```
/fs/microsd/
├── params          ← vehicle parameters
├── log/            ← flight logs (.ulg files)
│   └── YYYY-MM-DD/
│       └── HH-MM-SS.ulg
├── etc/            ← startup scripts (optional overrides)
└── missions/       ← saved missions
```

## Logging

PX4 logs according to `SDLOG_MODE`:
- `0` (default): from arming until disarming
- `1`: from boot until disarming
- `2`: from boot until shutdown

Logs use the **.ulg** (ULog) format. They can be analyzed in:
- [Flight Review](https://review.px4.io) (online)
- [PlotJuggler](https://github.com/facontidavide/PlotJuggler) with the ULog plugin
- QGroundControl → Analyze → Log Download

To download logs via QGC:
1. Connect via USB
2. Go to **Analyze Tools → Log Download**
3. Select and download logs to your computer

## Verifying SD Card Detection

In the MAVLink Console:

```
ls /fs/microsd
```

If the SD card is detected and mounted, you will see the directory listing. A missing or unmounted card returns an error.

QGC will also show a warning in the status bar if no SD card is present.

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| `ls /fs/microsd` fails | No card, or card not seated | Re-seat card; try a different card |
| Parameters reset on reboot | No card, or write errors | Check card health; reformat to FAT32 |
| Logging stops mid-flight | Card full or write speed too slow | Use a faster card; free space on card |
| Card not recognized | exFAT format | Reformat to FAT32 |
