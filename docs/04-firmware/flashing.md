---
id: flashing
title: Flashing Firmware
sidebar_label: Flashing
---

# Flashing Firmware

## Firmware Files

| Source | File | When to use |
|---|---|---|
| [Firmware releases](https://github.com/avian-automata/PX4-Autopilot/releases) on GitHub | `avianautomata_305ap_default_<date>.px4` | Normal updates |
| Rover firmware (no release yet; [build it from source](building-from-source)) | `build/avianautomata_305ap_rover/avianautomata_305ap_rover.px4` | Ground vehicles (see [Rover](../vehicle-types/rover)) |
| [Building from Source](building-from-source) | `build/avianautomata_305ap_default/avianautomata_305ap_default.px4` | Custom builds |

Releases are named by date. A letter suffix (`20261019b`) marks a second release on the same day. See the [Changelog](../reference/changelog) for what each release changes.

## Via QGroundControl (Recommended)

Install a `.px4` file with **Custom firmware file...** in **Vehicle Setup → Firmware**. The steps are in [Quickstart, Step 2](../getting-started/quickstart#step-2--install-firmware).

:::tip
If QGroundControl does not detect the board, try entering DFU mode manually (see below).
:::

## Via DFU (dfu-util)

### Enter DFU Mode

The 305ap has a dedicated **BOOT** button on the board. To enter DFU mode:

1. Hold the **BOOT** button.
2. Apply power (or press and release **RESET** while already powered).
3. Release the BOOT button. The board will enumerate as an STM32 DFU device.

### Flash with dfu-util

```bash
# Install dfu-util if needed
sudo apt install dfu-util    # Debian/Ubuntu
brew install dfu-util        # macOS

# Flash PX4 firmware
dfu-util -a 0 -s 0x08020000:leave -D avianautomata_305ap_default_<date>.bin
```

Each [release](https://github.com/avian-automata/PX4-Autopilot/releases) includes the firmware as a `.bin` for this. For your own build, use `build/avianautomata_305ap_default/avianautomata_305ap_default.bin`.

:::note Flash address
`0x08020000` is the PX4 application start address, located in sector 1 immediately after the 128 KB bootloader reservation.
:::

### Flash via STM32CubeProgrammer

1. Enter DFU mode as above.
2. Open STM32CubeProgrammer.
3. Select **USB** interface and connect.
4. Set start address to `0x08020000`.
5. Browse to the `.bin` file and click **Start Programming**.

## Via PX4 Bootloader (Command Line)

If the PX4 bootloader is installed, the PX4 uploader script in the PX4 source tree can flash a `.px4` file:

```bash
python3 Tools/px_uploader.py --port /dev/ttyACM0 avianautomata_305ap_default_<date>.px4
```

Start the command, then power-cycle or plug in the board. The bootloader waits 5 seconds after power-up for an upload request, and the uploader waits for it to appear.

## Recovering a Blank or Unresponsive Board

If the board does not show up in QGroundControl at all, the bootloader may be missing or damaged. Use DFU mode to reinstall it (see [Flashing the Bootloader](#flashing-the-bootloader) and [Bootloader](bootloader)), then install firmware with QGroundControl.

## Flashing the Bootloader

If you need to re-flash the PX4 bootloader itself, download `avianautomata_305ap_bootloader_<date>.bin` from the [firmware releases page](https://github.com/avian-automata/PX4-Autopilot/releases) and flash it in DFU mode:

```bash
dfu-util -a 0 -s 0x08000000:leave -D avianautomata_305ap_bootloader.bin
```

The bootloader occupies sector 0 (`0x08000000`–`0x0801FFFF`, 128 KB). Flashing PX4 firmware does not overwrite the bootloader.

## Checking the Installed Version

In **Analyze Tools → MAVLink Console**:

```
ver all
```

The `PX4 git-hash` line identifies the exact build and matches the commit listed in the [Changelog](../reference/changelog). QGroundControl also shows the version under **Vehicle Setup → Summary**.
