## Indicators and Errors

This article covers how to resolve common errors on the VertiGate.

### COM Port Errors

**Q: In Bonsai, running the workflow throws an error "The port `ComX` does not exist."**

A: Either the wrong communications port in the `PortName` property in [`Device`] was selected, or the [USB](connections.md) cable is not properly connected. Try selecting a different communications port and checking the connection.

**Q: In Bonsai, running the workflow throws an error "Access to the port `ComX` is denied"**

A: Only one interface connection to the VertiGate can be opened at one time. Check that multiple instances of Bonsai are not running. Sometimes, the port can also be locked by a program that did not terminate correctly; restarting the computer fixes it.

**Q: The board shows two COM ports. Bonsai reports a WhoAmI mismatch, or times out, on the one I picked.**

A: The first port is the MicroPython console and the second is the Harp interface. Set the `PortName` property in [`Device`] to the second one.

**Q: The Harp port disappeared, or only one COM port appears after a reset.**

A: The USB stack on the board can stay down after several resets in a row. Unplug the board, wait a few seconds, and plug it in again. The Harp port comes back about 10 seconds after the board starts.

### Device Errors

**Q: [`GateState`] reads `Error` as soon as the device starts.**

A: The servo did not answer when the board started. Check that the servo is powered, that it is connected to the [Servo](connections.md?tabs=servo#connections) port, and that it is set to ID 1 at 1 Mbaud. Then send a `Calibrate` command as described in [Calibrate the Gate](calibrate-gate.md#home-the-gate). The gate also reports `Error` when homing takes longer than 10 seconds, for example when the mechanism binds.

**Q: The workflow stops with an error when I write to `TargetPosition`, or when I send `Calibrate`.**

A: The motor is disabled. `DisableMotor` is a state, not a one-off command, and the device refuses every movement until it receives `EnableMotor`. See [Enable and Disable the Motor](move-gate.md#enable-and-disable-the-motor). The device also refuses a write to [`Control`] that sets both bits of a pair, for example `EnableMotor` together with `DisableMotor`.

**Q: The gate did not home itself when I powered the device on.**

A: The device keeps the motor state over a power cycle, and it does not move the gate at start-up while the motor is disabled. Send `EnableMotor`, then `Calibrate`.

**Q: The gate drops for a moment when I write `Speed` or `Torque`.**

A: The device switches the servo torque off and on to apply a new limit. Set these registers before a movement, not while the gate holds a position or moves. See [Tune Speed and Torque](tune-speed-torque.md).

**Q: `Torque` reads back a different value from the one I wrote.**

A: The device keeps only the lower seven bits of the value. A write of 200 is applied and reported as 72. Use values from 0 to 127.

**Q: `Position` reads 0 while the gate is still moving near the bottom, or does not change after a calibration.**

A: `Position` is limited to 0 to 255, and the gate rests about 10 mm below the recorded home, so the last part of the travel reads 0. A calibration replaces the home without changing `Position`. Read [`RawPosition`](monitor-servo.md#visualize-position-events) to see the encoder count and the home value separately.

**Q: `Position`, `RawPosition` or `ServoTelemetry` read back the same value every time, and the gate does not move.**

A: The servo is not answering, and these registers return their last value instead of an error. Check [`GateState`]: it reads `Error` when the servo did not answer. Then check the servo power, cable, ID and baud rate as above, and send `Calibrate`. While the event streams are on, the device skips these registers' events instead of reporting the fault, so a stream that goes quiet during a movement is a sign of the same problem.

**Q: The gate stalls before it reaches the top, or pushes hard into the end stops.**

A: Raise or lower the [`Torque`](tune-speed-torque.md#set-the-torque-limit) limit. If the raised position is a little short or a little long, trim it with the [`CalibrationOffset`](calibrate-gate.md#trim-the-raised-position) register instead of moving the hardware.

[!INCLUDE [](version-footer.md)]

<!--Reference Style Links -->
[`Device`]: xref:Aeon.VertiGate.Device
[`GateState`]: xref:Aeon.VertiGate.GateState
[`Control`]: xref:Aeon.VertiGate.Control
