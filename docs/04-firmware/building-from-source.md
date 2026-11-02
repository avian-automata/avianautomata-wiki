---
id: building-from-source
title: Building from Source
sidebar_label: Building from Source
---

# Building from Source

## Prerequisites

Follow the [PX4 development environment setup](https://docs.px4.io/main/en/dev_setup/dev_env.html) for your OS. On Ubuntu:

```bash
bash ./Tools/setup/ubuntu.sh
```

You also need:
- `git`
- `cmake` 3.16+
- `python3` with `pip`
- ARM GCC toolchain (installed by the setup script)

## Clone PX4

The 305ap board target is in the [avian automata PX4 fork](https://github.com/avian-automata/PX4-Autopilot) on GitHub (it is not in upstream PX4):

```bash
git clone https://github.com/avian-automata/PX4-Autopilot.git --recursive
cd PX4-Autopilot
```

:::note
The `--recursive` flag is required to pull all submodules including NuttX.
:::

## Build Firmware

```bash
make avianautomata_305ap_default
```

The build output is at:
```
build/avianautomata_305ap_default/avianautomata_305ap_default.px4
build/avianautomata_305ap_default/avianautomata_305ap_default.bin
```

Use the `.px4` file for QGroundControl or `px_uploader.py`. Use the `.bin` file for `dfu-util`.

## Build Bootloader

```bash
make avianautomata_305ap_bootloader
```

Output: `build/avianautomata_305ap_bootloader/avianautomata_305ap_bootloader.bin`

## Other Build Targets

```bash
make avianautomata_305ap_rover      # rover/ground vehicle variant
```

## Modifying Kconfig

To enable or disable drivers and features:

```bash
make avianautomata_305ap_default boardconfig
```

This opens the Kconfig menu. Changes are saved to `boards/avianautomata/305ap/default.px4board`.

## Uploading After Build

Via USB (if PX4 bootloader is present):
```bash
make avianautomata_305ap_default upload
```

Via DFU:
```bash
dfu-util -a 0 -s 0x08020000:leave -D build/avianautomata_305ap_default/avianautomata_305ap_default.bin
```
