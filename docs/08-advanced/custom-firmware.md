---
id: custom-firmware
title: Custom Firmware
sidebar_label: Custom Firmware
---

# Custom Firmware

There are two ways to change what runs on the 305ap:

- **Startup files on the SD card:** add commands or parameter defaults without rebuilding. Good for starting an extra driver or service.
- **A custom build:** change drivers, modules or code. Needed when something isn't in the standard firmware.

Try the SD card first. It survives firmware updates, and you can go back to stock by deleting a file.

## Startup Files on the SD Card

PX4 checks the SD card for these files during boot:

| File | Runs | Use for |
|---|---|---|
| `/fs/microsd/etc/config.txt` | Before most modules start, after parameter defaults | `param set-default` lines, early configuration |
| `/fs/microsd/etc/extras.txt` | At the end of startup | Starting extra drivers and services |

The files are NSH scripts: one command per line, `#` for comments. Create the `etc` folder on the card if it doesn't exist.

Example `extras.txt` that starts ESC telemetry on the motor connector and an MS5611 barometer on the external SPI port:

```sh
# ESC serial telemetry on the motor connector (USART1, single-wire)
dshot telemetry -d /dev/ttyS0 -x

# MS5611 on external SPI, chip select 2
ms5611 -S -b 1 -c 2 start
```

You can write the file from the MAVLink Console, or put the SD card in a PC. Reboot to apply. Check the boot messages with `dmesg` to confirm the commands ran.

:::tip
Use `param set-default` (not `param set`) in `config.txt`. A default still lets you change the value from QGroundControl, and resetting parameters goes back to your default.
:::

## Custom Builds

### Get the Source

The 305ap board target is in the avian automata PX4 fork:

```bash
git clone https://github.com/aaronfuller/PX4-Autopilot.git --recursive
cd PX4-Autopilot
```

See [Building from Source](../firmware/building-from-source) for the toolchain setup.

### Where to Make Changes

| Change | File |
|---|---|
| Add or remove drivers and modules | `boards/avianautomata/305ap/default.px4board` (see [px4board Config](../firmware/px4board-config)) |
| Board parameter defaults | `boards/avianautomata/305ap/init/rc.board_defaults` |
| Onboard sensor startup | `boards/avianautomata/305ap/init/rc.board_sensors` |
| Pin assignments, LEDs, ADC | `boards/avianautomata/305ap/src/board_config.h` and `nuttx-config/include/board.h` |
| NuttX (OS) options | `boards/avianautomata/305ap/nuttx-config/nsh/defconfig` |

Prefer the board files over editing shared PX4 code. Board changes are easy to carry across PX4 updates; changes to shared drivers or modules conflict with upstream more often.

### Build and Install

```bash
make avianautomata_305ap_default
```

Install `build/avianautomata_305ap_default/avianautomata_305ap_default.px4` as described in [Quickstart, Step 2](../getting-started/quickstart#step-2--install-firmware), or upload directly:

```bash
make avianautomata_305ap_default upload
```

Commit your changes before building. The firmware records the git commit, and `ver all` then shows exactly which source a board is running.

### Watch the Flash Budget

The default build is close to full (about 94% of flash). Adding a module may need another one removed. See [px4board Config](../firmware/px4board-config#flash-budget).

## Going Back to Stock

- Delete `config.txt` and `extras.txt` from `/fs/microsd/etc`, and reboot.
- Install a release `.px4` file to replace a custom build.
- To reset all parameters: `param reset_all` in the MAVLink Console, then reboot.
