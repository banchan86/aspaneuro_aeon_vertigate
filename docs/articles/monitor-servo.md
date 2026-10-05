## Monitor the Servo

The VertiGate can stream the position of the gate while it moves and the readings of the servo once a second. Both streams are off on a new device, and the device keeps their on or off state over a power cycle. Refer to the [connections](./connections.md?tabs=servo#connections) article to set up the servo, which we will use for the rest of these examples.

This article covers how to enable position events, visualize the position, enable telemetry events, visualize the servo readings, and read the servo on demand in Bonsai.

The complete workflow is shown below. Copy and paste it into Bonsai or build each section by following the step-by-step instructions below.

:::workflow
![Monitor the Servo](../workflows/monitorservo-toplevel.bonsai)
:::

### Enable Position Events

The `EnablePositionEvent` and `DisablePositionEvent` bits of the [`Control`] register turn the position stream on and off. While it is on, the device reports [`Position`] and [`RawPosition`] every 50 ms whenever the gate moves or homes.

:::workflow
![Enable Position Events](../workflows/monitorservo-enableposition.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `A`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `EnablePositionEvent`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

In a separate branch:

- Insert a [`KeyDown`] operator and set the `Filter` property to `S`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `DisablePositionEvent`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>A</kbd>. The device starts sending position events on the next movement. Press <kbd>S</kbd> to stop them.

### Visualize Position Events

The VertiGate broadcasts events occurring on the device using the [Harp communication protocol](https://harp-tech.org/protocol/BinaryProtocol-8bit.html). To visualize where the gate is while it moves, we can decode the [`HarpMessages`] coming from the device using the workflow below:

:::workflow
![Visualize Position Events](../workflows/monitorservo-visualizeposition.bonsai)
:::

- Insert a [`SubscribeSubject`] operator named `VertiGate Events`. This will listen to [`HarpMessages`] broadcast from the [`PublishSubject`] named `VertiGate Events` in the Harp device pattern.
- Insert a [`Parse`] operator and configure the `Register` property to `TimestampedPosition`.
- Insert a [`VisualizerWindow`] operator. This will automatically open a window displaying the parsed events when the workflow starts.

In a separate branch:

- Insert a [`SubscribeSubject`] operator named `VertiGate Events`.
- Insert a [`Parse`] operator and configure the `Register` property to `TimestampedRawPosition`.
- Insert a [`VisualizerWindow`] operator.

Run the workflow, press <kbd>A</kbd> to enable the stream, then [move the gate](move-gate.md). The first visualizer will display the position on the same scale as `TargetPosition`:

```text
120@1234.567
```

The second visualizer will display the two encoder counts the position is built from:

```text
RawPositionPayload { Encoder = 128400, Home = 122640 }@1234.567
```

`Position` is `Encoder` minus `Home`, divided by 48 and limited to 0 to 255. The limit hides two things that `RawPosition` shows: the gate rests about 400 counts below `Home`, so `Position` reads 0 for the last 10 mm of travel, and a calibration replaces `Home` in one step at the end of homing.

> [!TIP]
> To watch a calibration, enable position events and then [home the gate](calibrate-gate.md#home-the-gate). `Encoder` decreases while the gate goes down and `Home` changes once at the end. The size of that change is the error in the previous home.

### Enable Telemetry Events

The `EnableTelemetryEvent` and `DisableTelemetryEvent` bits of the [`Control`] register turn the telemetry stream on and off. While it is on, the device reports [`ServoTelemetry`] once a second.

:::workflow
![Enable Telemetry Events](../workflows/monitorservo-enabletelemetry.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `D`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `EnableTelemetryEvent`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

In a separate branch:

- Insert a [`KeyDown`] operator and set the `Filter` property to `F`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateControlPayload`.
    - `Control` - Select `DisableTelemetryEvent`.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>D</kbd>. The device starts sending telemetry events once a second. Press <kbd>F</kbd> to stop them.

### Visualize Servo Telemetry Events

To follow the supply voltage, temperature, current and fault status of the servo, decode the [`ServoTelemetry`] events with the workflow below:

:::workflow
![Visualize Servo Telemetry Events](../workflows/monitorservo-visualizetelemetry.bonsai)
:::

- Insert a [`SubscribeSubject`] operator named `VertiGate Events`.
- Insert a [`Parse`] operator and configure the `Register` property to `TimestampedServoTelemetry`.
- Insert a [`VisualizerWindow`] operator. This will automatically open a window displaying the parsed events when the workflow starts.

Run the workflow and press <kbd>D</kbd>. The visualizer will display one reading per second:

```text
ServoTelemetryPayload { Voltage = 120, Temperature = 34, Current = 15, HardwareError = 0 }@1234.567
```

`Voltage` is in units of 0.1 V, `Temperature` in degrees Celsius, `Current` in mA with the sign giving the direction, and `HardwareError` is the servo fault status, where 0 means no fault. A voltage dip during a movement points to a weak supply, and a rising temperature to too high a duty cycle.

### Read the Servo on Demand

Every register in this article can also be read at any time, whether or not its stream is on. A read request returns one reply through the same [`Parse`] operators as the events.

:::workflow
![Read the Servo on Demand](../workflows/monitorservo-read.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `G`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `MessageType` - Select `Read`.
    - `Payload` - Select `CreateServoTelemetryPayload`.
    - `Voltage`, `Temperature`, `Current`, `HardwareError` - Leave at the default, the payload values are ignored by the device for a read request.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>G</kbd>. One reading appears in the servo telemetry visualizer, even while the telemetry stream is off.

> [!NOTE]
> If the servo does not answer, a read of [`Position`], [`RawPosition`] or [`ServoTelemetry`] returns the last value the device holds rather than an error reply. Check [`GateState`](calibrate-gate.md#visualize-gate-state-events), which reports `Error` in that case.

[!INCLUDE [](version-footer.md)]

<!--Reference Style Links -->
[`Control`]: xref:Aeon.VertiGate.Control
[`Position`]: xref:Aeon.VertiGate.Position
[`RawPosition`]: xref:Aeon.VertiGate.RawPosition
[`ServoTelemetry`]: xref:Aeon.VertiGate.ServoTelemetry
[`CreateMessage`]: xref:Aeon.VertiGate.CreateMessage
[`Parse`]: xref:Aeon.VertiGate.Parse
[`KeyDown`]: xref:Bonsai.Windows.Input.KeyDown
[`MulticastSubject`]: xref:Bonsai.Expressions.MulticastSubject
[`SubscribeSubject`]: xref:Bonsai.Expressions.SubscribeSubject
[`PublishSubject`]: xref:Bonsai.Reactive.PublishSubject
[`HarpMessages`]: xref:Bonsai.Harp.HarpMessage
[`VisualizerWindow`]: xref:Bonsai.Design.VisualizerWindow
