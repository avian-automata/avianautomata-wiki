---
id: firmware-install
title: Firmware Install
sidebar_label: Firmware Install
---

# Firmware Install

The 305ap runs PX4 built from the avian automata PX4 fork, which carries the `avianautomata_305ap` board target. The board is not part of upstream PX4, so QGroundControl's built-in **PX4 Flight Stack** download does not include firmware for it. Install firmware from a `.px4` file instead.

## Out of the Box

Boards ship with the PX4 bootloader and a current firmware release already installed. A new board only needs an SD card and setup in QGroundControl. Continue with the [Quickstart](quickstart).

Install firmware yourself when you want to update to a newer release, return to a release after testing a custom build, or recover a board.

## Getting Firmware

| Source | File | When to use |
|---|---|---|
| Firmware releases from avian automata (download location to be announced) | `avianautomata_305ap_default_<date>.px4` | Normal updates |
| Rover releases | `avianautomata_305ap_rover_<date>.px4` | Ground vehicles (see [Rover](../vehicle-types/rover)) |
| [Building from Source](../firmware/building-from-source) | `build/avianautomata_305ap_default/avianautomata_305ap_default.px4` | Custom builds |

Releases are named by date. A letter suffix (`20261019b`) marks a second release on the same day. See the [Changelog](../reference/changelog) for what each release changes.

## Installing with QGroundControl

1. Disconnect the board from USB and close any other program using it.
2. Open QGroundControl and go to **Vehicle Setup → Firmware**.
3. Connect the board over USB. QGroundControl catches the bootloader during its 5 second window at power-up.
4. In the dialog, select **PX4 Flight Stack**, tick **Advanced settings**, and choose **Custom firmware file...** from the drop-down.
5. Click **OK** and pick the `.px4` file.
6. Wait for the upload to finish. The board reboots into the new firmware.

:::tip
QGroundControl needs the board to power up while it is waiting, because the bootloader only listens for 5 seconds after power-up. Unplug the battery first, so that plugging in USB is what powers the board.
:::

Parameters are kept on the SD card, so updating firmware does not reset your configuration. After a major update, check **Vehicle Setup** for any new warnings.

## Installing from the Command Line

With the PX4 source tree checked out, the PX4 uploader script can flash a `.px4` file:

```bash
python3 Tools/px_uploader.py --port /dev/ttyACM0 avianautomata_305ap_default_<date>.px4
```

Start the command, then power-cycle or plug in the board. The uploader waits for the bootloader to appear.

## Blank or Unresponsive Boards

If the board does not show up in QGroundControl at all, the bootloader may be missing or damaged. Use DFU mode to reinstall it, then install firmware as above. See [Bootloader](../firmware/bootloader) and [Flashing](../firmware/flashing).

## Checking the Installed Version

In **Analyze Tools → MAVLink Console**:

```
ver all
```

The `PX4 git-hash` line identifies the exact build. QGroundControl also shows the version under **Vehicle Setup → Summary**.
