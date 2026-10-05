## Move the Gate

The VertiGate moves the gate to any of 256 positions along its travel, where 0 is fully lowered and 255 is fully raised, in steps of 1.2 mm. Refer to the [connections](./connections.md?tabs=servo#connections) article to set up the servo, which we will use for the rest of these examples.

This article covers how to raise and lower the gate, move it to a position, stop it, enable and disable the motor, and visualize motor state events in Bonsai.

The complete workflow is shown below. Copy and paste it into Bonsai or build each section by following the step-by-step instructions below.

:::workflow
![Move the Gate](../workflows/movegate-toplevel.bonsai)
:::

### Raise and Lower the Gate

Writing the [`TargetPosition`] register starts a movement. The two end values move the gate to its end stops.

:::workflow
![Raise and Lower the Gate](../workflows/movegate-raiselower.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `A`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateTargetPositionPayload`.
    - `TargetPosition` - Set to 255 to raise the gate fully.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

In a separate branch:

- Insert a [`KeyDown`] operator and set the `Filter` property to `S`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateTargetPositionPayload`.
    - `TargetPosition` - Set to 0 to lower the gate fully.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>A</kbd> to raise the gate, then <kbd>S</kbd> to lower it. The [`GateState`] register reports `Moving` on the way, then `Up` or `Down`.

> [!NOTE]
> When the gate reaches the lowered position, the device releases the servo torque to protect the motor. The gate rests on the end stop until the next command. A command to an end stop the gate is already at does nothing.

### Move to a Position

Any value between the end stops moves the gate to the matching height. One count is 1.2 mm, so 125 is 150 mm above the lowered position.

:::workflow
![Move to a Position](../workflows/movegate-position.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `D`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateTargetPositionPayload`.
    - `TargetPosition` - Set the position in counts (e.g. 125 for halfway).
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>D</kbd>. The gate moves to the middle of its travel, holds there, and reports `Idle`.

> [!NOTE]
> The gate reaches its top at 250. A value from 251 to 255 moves the gate to the same place as 250.

### Stop the Gate

The `Stop` bit of the [`Control`] register ends the current movement and holds the gate where it is.

:::workflow
![Stop the Gate](../workflows/movegate-stop.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `F`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `Stop`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow, press <kbd>A</kbd> to start raising the gate, then press <kbd>F</kbd>. The gate stops and [`GateState`] reports `Idle`.

### Enable and Disable the Motor

The `DisableMotor` bit of the [`Control`] register turns the servo torque off, so the gate can be moved by hand. It is a state, not a one-off command: the device refuses every movement until you send `EnableMotor`, and it keeps the state over a power cycle.

:::workflow
![Enable and Disable the Motor](../workflows/movegate-motor.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `G`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `DisableMotor`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

In a separate branch:

- Insert a [`KeyDown`] operator and set the `Filter` property to `H`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `EnableMotor`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>G</kbd>. The motor releases and the [`MotorState`] register reports `Disabled`. Press <kbd>H</kbd> to turn it back on; the gate holds its current position.

> [!WARNING]
> While the motor is disabled, the device answers a write to [`TargetPosition`] or a `Calibrate` command with an error reply, and the Bonsai workflow stops with an exception. Send `EnableMotor` first. A device that was powered off with the motor disabled does not home at power-up either; enable the motor, then [home the gate](calibrate-gate.md#home-the-gate).

### Visualize Motor State Events

The VertiGate broadcasts events occurring on the device using the [Harp communication protocol](https://harp-tech.org/protocol/BinaryProtocol-8bit.html). To visualize when the motor is turned on and off, we can decode the [`HarpMessages`] coming from the device using the workflow below:

:::workflow
![Visualize Motor State Events](../workflows/movegate-visualizeevents.bonsai)
:::

- Insert a [`SubscribeSubject`] operator named `VertiGate Events`. This will listen to [`HarpMessages`] broadcast from the [`PublishSubject`] named `VertiGate Events` in the Harp device pattern.
- Insert a [`Parse`] operator and configure the `Register` property to `TimestampedMotorState`.
- Insert a [`VisualizerWindow`] operator. This will automatically open a window displaying the parsed events when the workflow starts.

Run the workflow and press <kbd>G</kbd>, then <kbd>H</kbd>. The visualizer will display:

```text
Disabled@1234.567
Enabled@1240.102
```

The first value is the [`MotorState`], `Enabled` or `Disabled`, and the second number is the timestamp on the device clock. To follow the movement itself, see the gate state events in [Calibrate the Gate](calibrate-gate.md#visualize-gate-state-events) and the position events in [Monitor the Servo](monitor-servo.md).

[!INCLUDE [](version-footer.md)]

<!--Reference Style Links -->
[`TargetPosition`]: xref:Aeon.VertiGate.TargetPosition
[`Control`]: xref:Aeon.VertiGate.Control
[`GateState`]: xref:Aeon.VertiGate.GateState
[`MotorState`]: xref:Aeon.VertiGate.MotorState
[`CreateMessage`]: xref:Aeon.VertiGate.CreateMessage
[`Parse`]: xref:Aeon.VertiGate.Parse
[`KeyDown`]: xref:Bonsai.Windows.Input.KeyDown
[`MulticastSubject`]: xref:Bonsai.Expressions.MulticastSubject
[`SubscribeSubject`]: xref:Bonsai.Expressions.SubscribeSubject
[`PublishSubject`]: xref:Bonsai.Reactive.PublishSubject
[`HarpMessages`]: xref:Bonsai.Harp.HarpMessage
[`VisualizerWindow`]: xref:Bonsai.Design.VisualizerWindow
