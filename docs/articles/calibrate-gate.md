## Calibrate the Gate

The VertiGate finds its lowered position by driving the gate into the lower end stop and recording that point as home. It does this a few seconds after power-up, and again whenever you ask it to. Refer to the [connections](./connections.md?tabs=servo#connections) article to set up the servo, which we will use for the rest of these examples.

This article covers how to home the gate on command, trim the raised position, and visualize gate state events in Bonsai.

The complete workflow is shown below. Copy and paste it into Bonsai or build each section by following the step-by-step instructions below.

:::workflow
![Calibrate the Gate](../workflows/calibrategate-toplevel.bonsai)
:::

> [!WARNING]
> You can find and add these operators to the workflow from the Bonsai [Toolbox](https://bonsai-rx.org/docs/articles/editor.html?tabs=mouse-controls#toolbox). Make sure to use the device-specific versions, e.g. `Device (Aeon.VertiGate)` instead of `Device (Harp)`. If correctly selected, the names of these operators in the workflow panel will change to reflect either the name of the device or the selected register/payload.

### Home the Gate

Homing is a command bit of the [`Control`] register. The gate moves down at its default speed until the servo stops moving, records that point as home, and releases the motor.

:::workflow
![Home the Gate](../workflows/calibrategate-calibrate.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `A`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `Calibrate`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>A</kbd>. The gate moves to the lower end stop and stops there. Homing takes a few seconds; if the servo does not reach a stop within 10 seconds the gate reports the `Error` state.

> [!TIP]
> The device refuses a `Calibrate` command while the motor is disabled, and the workflow stops with an error. To enable the motor and home the gate in one command, select both `EnableMotor` and `Calibrate` in the `Control` property.

> [!WARNING]
> Homing runs at the default speed and torque, then puts your own [Speed and Torque](tune-speed-torque.md) values back. A `Stop` command during homing leaves the gate with a provisional home: send `Calibrate` again before you rely on the position.

### Trim the Raised Position

The [`CalibrationOffset`] register moves the fully-raised position without touching the hardware. One count is one encoder count, 25 µm, so the full range of −128 to 127 trims the top of the travel by about 3 mm either way. The device stores the value and keeps it over a power cycle.

:::workflow
![Trim the Raised Position](../workflows/calibrategate-offset.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `S`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateCalibrationOffsetPayload`.
    - `CalibrationOffset` - Set the number of encoder counts to add to the raised position (e.g. 20 raises it by 0.5 mm).
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>S</kbd>. The offset applies the next time the gate is [raised fully up](move-gate.md#raise-and-lower-the-gate).

### Visualize Gate State Events

The VertiGate broadcasts events occurring on the device using the [Harp communication protocol](https://harp-tech.org/protocol/BinaryProtocol-8bit.html). To visualize what the gate is doing and when it changes state, we can decode the [`HarpMessages`] coming from the device using the workflow below:

:::workflow
![Visualize Gate State Events](../workflows/calibrategate-visualizeevents.bonsai)
:::

- Insert a [`SubscribeSubject`] operator named `VertiGate Events`. This will listen to [`HarpMessages`] broadcast from the [`PublishSubject`] named `VertiGate Events` in the Harp device pattern.
- Insert a [`Parse`] operator and configure the `Register` property to `TimestampedGateState`.
- Insert a [`VisualizerWindow`] operator. This will automatically open a window displaying the parsed events when the workflow starts.

> [!NOTE]
> Every register event can be parsed in two forms, selected in the `Register` property of [`Parse`]. The bare payload (e.g. `GateState`, used in [First Steps](./harp-bonsai.md#first-steps)) returns only the register values while the timestamped variant (e.g. `TimestampedGateState`) returns the same payload wrapped in a `Value` field and adds a `Seconds` field carrying the device timestamp. Use the bare variant if it is enough for live monitoring or the timestamped variant if you need to visualize the timestamp as well. Regardless of which option is chosen, all data is [logged](./logging-analysis.md) with device timestamps.

Run the workflow and press <kbd>A</kbd> to home the gate. The visualizer will display:

```text
Calibrating@1234.567
Down@1241.832
```

The first value is the [`GateState`], one of `Idle`, `Up`, `Down`, `Moving`, `Calibrating` or `Error`, and the second number is the timestamp on the device clock. The device also reports the state once when the workflow starts, because the Harp device pattern reads every register on connection.

### Alternative: Home the Gate with Timer

You can replace [`KeyDown`] with other operators to home the gate with other triggers in Bonsai, for instance a [`Timer`].

:::workflow
![Home the Gate with Timer](../workflows/calibrategate-timer.bonsai)
:::

- Insert a [`Timer`] operator and set the `DueTime` property to the number of seconds to wait before homing (e.g. 2 seconds).
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `Calibrate`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and observe the gate home itself after 2 seconds.

[!INCLUDE [](version-footer.md)]

<!--Reference Style Links -->
[`Control`]: xref:Aeon.VertiGate.Control
[`CalibrationOffset`]: xref:Aeon.VertiGate.CalibrationOffset
[`GateState`]: xref:Aeon.VertiGate.GateState
[`CreateMessage`]: xref:Aeon.VertiGate.CreateMessage
[`Parse`]: xref:Aeon.VertiGate.Parse
[`KeyDown`]: xref:Bonsai.Windows.Input.KeyDown
[`Timer`]: xref:Bonsai.Reactive.Timer
[`MulticastSubject`]: xref:Bonsai.Expressions.MulticastSubject
[`SubscribeSubject`]: xref:Bonsai.Expressions.SubscribeSubject
[`PublishSubject`]: xref:Bonsai.Reactive.PublishSubject
[`HarpMessages`]: xref:Bonsai.Harp.HarpMessage
[`VisualizerWindow`]: xref:Bonsai.Design.VisualizerWindow
