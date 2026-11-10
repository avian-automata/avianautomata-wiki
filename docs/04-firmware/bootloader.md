---
id: bootloader
title: Bootloader
sidebar_label: Bootloader
---

# Bootloader

The 305ap uses the standard **PX4 bootloader**, built for this board. It lives in the first flash sector and starts the PX4 application. It also accepts firmware uploads from QGroundControl and `px_uploader.py`.

## Behavior at Power-Up

1. The bootloader starts and enumerates on USB as **"PX4 BL 305AP"** (VID `0x1B8C`, PID `0x0036`).
2. It waits **5 seconds** for an upload request. The **blue** LED blinks while it waits.
3. If no upload starts, it jumps to the PX4 application. The board then re-enumerates on USB as **"305AP"** (same VID/PID), and the application's MAVLink port appears.

During an upload the **green** LED is on solid.

The bootloader runs on every power-up, so a board with working firmware never needs DFU mode for an update.

## Memory Layout

| Region | Address | Size |
|---|---|---|
| Bootloader | `0x08000000`–`0x0801FFFF` | 128 KB (sector 0) |
| PX4 application | `0x08020000` | up to 1920 KB |

Uploading firmware erases and writes the application area only. The bootloader is never overwritten by a normal firmware update.

## Board Identity

| Field | Value |
|---|---|
| Board ID | `6700` |
| USB product (bootloader) | `PX4 BL 305AP` |
| USB product (application) | `305AP` |
| USB VID / PID | `0x1B8C` / `0x0036` |

The board ID is stored in every `.px4` file and reported by the bootloader. QGroundControl and `px_uploader.py` compare the two and refuse firmware built for another board.

## Reinstalling the Bootloader

You only need this for a blank board or if the bootloader itself is damaged (the board never enumerates as `PX4 BL 305AP`).

1. Put the board in DFU mode: hold **BOOT**, apply power (or press **RESET**), release **BOOT**. It enumerates as an STM32 DFU device.
2. Flash the bootloader binary to the start of flash:

   ```bash
   dfu-util -a 0 -s 0x08000000:leave -D avianautomata_305ap_bootloader.bin
   ```

3. Power-cycle. The board should enumerate as `PX4 BL 305AP` and blink blue for 5 seconds.
4. Install firmware as described in [Quickstart, Step 2](../getting-started/quickstart#step-2--install-firmware).

The bootloader binary is built with `make avianautomata_305ap_bootloader` (see [Building from Source](building-from-source)). A prebuilt copy is attached to each [firmware release](https://github.com/avian-automata/PX4-Autopilot/releases) as `avianautomata_305ap_bootloader_<date>.bin`, and kept in the board directory at [`boards/avianautomata/305ap/extras/avianautomata_305ap_bootloader.bin`](https://github.com/avian-automata/PX4-Autopilot/blob/main/boards/avianautomata/305ap/extras/avianautomata_305ap_bootloader.bin).

:::note
The bootloader changes rarely. Updating PX4 firmware does not require a new bootloader.
:::

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Blue LED blinks, then no `305AP` USB device | Application missing or crashing at start | Reinstall firmware from QGroundControl |
| Nothing enumerates on USB, no LEDs | No bootloader, or no power | Check power; reinstall the bootloader via DFU |
| QGroundControl never finds the board | Board already running when QGroundControl started waiting | Unplug the battery and USB, then plug in USB while QGroundControl waits |
| `px_uploader.py` reports a board ID mismatch | Firmware built for a different board | Use an `avianautomata_305ap` `.px4` file |
