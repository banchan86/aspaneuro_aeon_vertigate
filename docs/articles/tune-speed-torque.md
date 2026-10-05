## Tune Speed and Torque

The VertiGate moves the gate at a speed limit and a torque limit that it applies to the servo and keeps in non-volatile memory, so a setting made once survives a power cycle. Refer to the [connections](./connections.md?tabs=servo#connections) article to set up the servo, which we will use for the rest of these examples.

This article covers how to set the speed, set the torque limit, and read a setting back from the device in Bonsai.

The complete workflow is shown below. Copy and paste it into Bonsai or build each section by following the step-by-step instructions below.

:::workflow
![Tune Speed and Torque](../workflows/tunespeedtorque-toplevel.bonsai)
:::

### Set the Speed

The [`Speed`] register sets the profile velocity of the servo. One count is 0.38 mm/s, and the default is 255, the fastest setting.

:::workflow
![Set the Speed](../workflows/tunespeedtorque-speed.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `A`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateSpeedPayload`.
    - `Speed` - Set the speed in counts (e.g. 128 for about half speed).
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>A</kbd>. The next movement of the gate runs at the new speed.

> [!WARNING]
> Writing [`Speed`] or [`Torque`] switches the servo torque off and on to apply the new limit. A gate that is holding a position drops for a moment, and a gate that is moving is interrupted. Set these registers before you move the gate, not during a movement.

> [!NOTE]
> A speed of 0 is the slowest setting, not a standstill. The firmware adds a fixed minimum to the value before it reaches the servo.

### Set the Torque Limit

The [`Torque`] register sets the current limit of the servo. One count is 0.36 kgf·mm, the range is 0 to 127, and the default is 35. Raise it if the gate stalls; lower it if the gate pushes too hard at the end stops.

:::workflow
![Set the Torque Limit](../workflows/tunespeedtorque-torque.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `S`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `Payload` - Select `CreateTorquePayload`.
    - `Torque` - Set the torque limit in counts (e.g. 50).
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

Run the workflow and press <kbd>S</kbd>. The limit applies to the next movement.

> [!NOTE]
> The device keeps only the lower seven bits of the value, so a write of 200 is applied and reported as 72. If the motor is [disabled](move-gate.md#enable-and-disable-the-motor), the device accepts the write but leaves the motor off.

### Read Back the Torque

The device replies to every write with the value it applied, and you can also ask for a register at any time with a read request. Reading back is the way to see the value the device holds, for instance after a write of more than 127 to [`Torque`].

:::workflow
![Read Back the Torque](../workflows/tunespeedtorque-readtorque.bonsai)
:::

- Insert a [`KeyDown`] operator and set the `Filter` property to `D`.
- Insert a [`CreateMessage`] operator and configure these properties:
    - `MessageType` - Select `Read`.
    - `Payload` - Select `CreateTorquePayload`.
    - `Torque` - Leave at the default, the payload value is ignored by the device for a read request.
- Insert a [`MulticastSubject`] operator named `VertiGate Commands`.

In a separate branch:

- Insert a [`SubscribeSubject`] operator named `VertiGate Events`.
- Insert a [`Parse`] operator and configure the `Register` property to `TimestampedTorque`.
- Insert a [`VisualizerWindow`] operator. This will automatically open a window displaying the parsed replies when the workflow starts.

Run the workflow and press <kbd>D</kbd>. The visualizer will display the torque limit the device holds and the device timestamp of the reply:

```text
50@1234.567
```

[`Parse`] matches messages by register, so the same window also shows the reply to each write you send with <kbd>S</kbd>.

[!INCLUDE [](version-footer.md)]

<!--Reference Style Links -->
[`Speed`]: xref:Aeon.VertiGate.Speed
[`Torque`]: xref:Aeon.VertiGate.Torque
[`CreateMessage`]: xref:Aeon.VertiGate.CreateMessage
[`Parse`]: xref:Aeon.VertiGate.Parse
[`KeyDown`]: xref:Bonsai.Windows.Input.KeyDown
[`MulticastSubject`]: xref:Bonsai.Expressions.MulticastSubject
[`SubscribeSubject`]: xref:Bonsai.Expressions.SubscribeSubject
[`VisualizerWindow`]: xref:Bonsai.Design.VisualizerWindow
