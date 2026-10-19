---
id: flashing
title: Flashing Firmware
sidebar_label: Flashing
---

# Flashing Firmware

## Via QGroundControl (Recommended)

The 305ap isn't in upstream PX4, so QGroundControl's built-in PX4 release download doesn't include it. Flash a `.px4` file instead:

1. Unplug the battery and USB, and open QGroundControl.
2. Go to **Vehicle Setup → Firmware**.
3. Plug in USB.
4. Select **PX4 Flight Stack**, tick **Advanced settings**, and choose **Custom firmware file...**.
5. Select the `avianautomata_305ap_default_<date>.px4` file.

See [Firmware Install](../getting-started/firmware-install) for where to get firmware files.

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
dfu-util -a 0 -s 0x08020000:leave -D avianautomata_305ap_default.bin
```

:::note Flash address
`0x08020000` is the PX4 application start address, located in sector 1 immediately after the 128 KB bootloader reservation.
:::

### Flash via STM32CubeProgrammer

1. Enter DFU mode as above.
2. Open STM32CubeProgrammer.
3. Select **USB** interface and connect.
4. Set start address to `0x08020000`.
5. Browse to the `.bin` file and click **Start Programming**.

## Via PX4 Bootloader (USB or Serial)

If the PX4 bootloader is already installed:

1. Power cycle the board. The bootloader waits 5 seconds for an upload request.
2. Use QGroundControl or `px_uploader.py` to upload within that window.

```bash
python3 Tools/px_uploader.py --port /dev/ttyACM0 build/avianautomata_305ap_default/avianautomata_305ap_default.px4
```

## Flashing the Bootloader

If you need to re-flash the PX4 bootloader itself:

```bash
dfu-util -a 0 -s 0x08000000:leave -D avianautomata_305ap_bootloader.bin
```

The bootloader occupies sector 0 (`0x08000000`–`0x0801FFFF`, 128 KB). Flashing PX4 firmware does not overwrite the bootloader.
