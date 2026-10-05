## VertiGate

The VertiGate is a Harp device that moves a vertical gate with a Dynamixel XM430-W210 servo, on a Dynamixel Controller board with an RP2350 microcontroller running MicroPython.

[placeholder - vertigate-pcb.png]{width=450}

### Key Features

- Moves the gate to any of 256 positions along a 300 mm travel with a single command byte.
- Reports the gate state, the motor state, the position, and the servo readings as Harp events.
- Speed, torque and calibration offset can be changed at runtime and are kept over a power cycle.
- Homes itself at power-up and on command, so the lowered position is always a measured end stop.

### Specs

- Travel: 300 mm, in 250 usable steps of 1.2 mm
- Servo: Dynamixel XM430-W210, on a 1 Mbaud Dynamixel bus
- Speed range: 0–255 counts, 0.38 mm/s per count
- Torque limit range: 0–127 counts, 0.36 kgf·mm per count
- Calibration offset range: −128 to 127 encoder counts, 25 µm per count
- Position event rate: every 50 ms while the gate moves or homes
- Servo telemetry event rate: 1 Hz
- Host interface: USB CDC serial at 1 Mbaud, Harp protocol version 1.13
- WhoAmI: 3002

> [!WARNING]
> **TODO**: Add the servo supply voltage and current, the timestamp resolution and the synchronization accuracy of the MicroPython Harp core. These are not recorded in the repository and must be measured or taken from the core documentation.

> [!NOTE]
> The VertiGate is built on the MicroPython [microharp](https://github.com/SainsburyWellcomeCentre/micropython-microharp) core rather than the ATxmega core used by most Harp devices, so timing figures published for other Harp devices do not apply to it.

[!INCLUDE [](version-footer.md)]
