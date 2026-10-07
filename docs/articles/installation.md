## Installation

This page covers the software you'll need to interact with the VertiGate, as well as how to update the firmware on the device.

## Software Packages

These steps are only required the first time you connect the device to a new computer, and you can install just the packages for the functionality you need.

# [Bonsai](#tab/bonsai)

[Bonsai](https://bonsai-rx.org/) is a visual reactive programming language that provides flexible and comprehensive control of the VertiGate.

[placeholder - installation-bonsaipackage.png]{width=650}

- Download and install [Bonsai](https://bonsai-rx.org/docs/articles/installation.html).
- Launch Bonsai and install the `Aeon.VertiGate` package by searching for it in the [Bonsai package manager](https://bonsai-rx.org/docs/articles/packages.html). Tick the "Show advanced" checkbox if it does not appear.
- (Optional) Install the `Bonsai.Windows.Input` package to follow along with the examples in this user guide.
- (Optional) Install the `Bonsai.Gui` package to use the [Gate Control Panel](gate-control-panel.md).

> [!NOTE]
> The `Aeon.Vertigate` package has not been released yet.

# [Python](#tab/python)

The [Harp](https://harp-tech.org/python/) library provides a Python interface for controlling Harp devices and [loading](logging-analysis.md) recorded data. To install the full library, install it in a Python environment with:

```cmd
pip install harp
```

To install only the package necessary for loading data:

```cmd
pip install harp-data
```

The `swc-aeon-vertigate` package adds the register names and payload types of the VertiGate, generated from its `device.yml`:

```cmd
pip install swc-aeon-vertigate
```

> [!NOTE]
> Substitute `uv add` for `pip install` if you are using the [uv](https://docs.astral.sh/uv/) package manager.

> [!NOTE]
> The `swc-aeon-vertigate` package is not released on PyPi yet, it can be found in `software/python`, but it not actually be necessary for loading data if the save folder includes a `device.yml`. Anyway keep the `swc-aeon-vertigate` section for now and revise later if necessary.

***

## Firmware

New features are added and bugs are fixed with firmware updates which are published on the [release page](https://github.com/SainsburyWellcomeCentre/aeon_vertigate/releases) in the VertiGate repository. Each release attaches one firmware image named `VertiGate-fw<x.y>-harp1.13-hw<x.y>-ass0.uf2`, where `fw` is the firmware version and `hw` is the hardware version it targets.

> [!WARNING]
> **TODO**: State where the hardware version is printed on the Dynamixel Controller board.

The VertiGate does not use the Harp bootloader, so the "Bootloader" button in the Bonsai device setup dialog does not apply. Flash the `.uf2` through the RP2350 USB boot drive instead:

1. Hold the **BOOTSEL** button on the board and plug it in, or press reset while holding **BOOTSEL**. A drive named `RP2350` appears on the computer.
2. Copy the `.uf2` from the release to the `RP2350` drive. The board reboots by itself.
3. Check that **two** serial ports appear. The first is the MicroPython console and the second is the Harp interface. Use the second one in Bonsai.

To update, flash the new `.uf2` the same way. The stored settings survive, because the image only replaces the firmware region.

After the update, the device reboots with the new firmware and homes the gate.

[!INCLUDE [](version-footer.md)]
