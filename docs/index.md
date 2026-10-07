## Overview

The [VertiGate](articles/vertigate-overview.md) is a Harp device that raises and lowers a vertical gate, for controlling access between arenas in behavioral experiments.

[placeholder - vertigate-with-servo.svg]{width=450}

Many behavioral setups need a barrier that opens and closes on cue, with the exact time of each movement on the same clock as the rest of the rig. The VertiGate drives a Dynamixel XM430-W210 servo along a 300 mm vertical travel, reports the state of the gate as Harp events, and keeps its speed, torque and calibration settings across power cycles.

The VertiGate provides:

- Position control with a single command byte, from fully lowered (0) to fully raised (255), in 1.2 mm steps.
- Gate state, motor state, position, and servo telemetry reported as timestamped events.
- Speed and torque limits that can be changed at runtime, and an offset register for fine calibration of the raised position.
- Hardware timestamping and synchronization with other [Harp](https://harp-tech.org/articles/about.html) devices.
- [Bonsai](https://bonsai-rx.org/) integration for flexible experiment acquisition and control.

## Getting a Device

The VertiGate is designed and built by the [Sainsbury Wellcome Centre FabLabs](https://www.sainsburywellcome.org/content/fablab) for the [Aeon](https://aeon.swc.ucl.ac.uk/) project. Firmware, interfaces and documentation are in the [VertiGate](https://github.com/SainsburyWellcomeCentre/aeon_vertigate) repository.

## Acknowledgments

Hardware and firmware by the [Sainsbury Wellcome Centre FabLabs](https://www.sainsburywellcome.org/content/fablab).

> [!WARNING]
> **TODO**: Confirm the list of contributors to acknowledge.

[!INCLUDE [](./articles/version-footer.md)]
