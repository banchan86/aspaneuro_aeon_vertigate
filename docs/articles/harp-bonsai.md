## Bonsai

Bonsai is a visual reactive programming language for building interactive experiments and processing data streams in real time. It supports a growing ecosystem of hardware and software packages that are commonly used in neuroscience. This article will cover how to set up the VertiGate in Bonsai.

>[!TIP]
> More information on Bonsai can be found in the official [documentation](https://bonsai-rx.org/docs/).

### First Steps

We will use a simple example to connect and test the device in Bonsai. This example displays the state of the gate as the device reports it. We revisit this example in more detail in the "Bonsai Workflows" section.

Before beginning:
- Connect the [USB](connections.md) cable to the computer.
- Launch "Bonsai" from the Windows Start menu.
- Hover over the workflow cell below, and click on the "Copy" icon on the top right.
- Paste the workflow into Bonsai.

:::workflow
![VertiGate First Steps](../workflows/vertigate-firststeps.bonsai)
:::

> [!TIP]
> The [Harp device pattern](https://harp-tech.org/articles/operators.html#device-pattern) will initialize the device, log data, and provide hooks to send commands as well as receive messages from the VertiGate using the [Harp communication protocol](https://harp-tech.org/protocol/BinaryProtocol-8bit.html). If your workflow does not look like the one above, make sure that the [Aeon.VertiGate](./installation.md#software-packages) package is installed.

- Click on the [`VertiGate (Device)`] operator and set the `PortName` property to the communications port for the device (e.g. COM4). The board shows two ports; use the second one, which is the Harp interface.
- Click on the [`VertiGateDataWriter (DeviceDataWriter)`] operator and set the `Path` property for the name and location of the save folder (e.g. `VertiGate.harp`).
- Press the "Start" button in Bonsai to run the workflow.

A [visualizer](xref:Bonsai.Design.VisualizerWindow) will automatically open when the workflow starts, displaying the state of the gate. The device reads every register when it connects, so the current state appears straight away. Once the gate has homed, it reports:

```text
Down
```

The device is ready to use! If, instead, an error appears in Bonsai, check out the [troubleshooting](troubleshooting.md) section.

Next, we suggest going through the "Bonsai Workflows" section if you are not familiar with using Harp devices in Bonsai.

Alternatively, if you have experience with Harp devices, you can check the [register table](xref:Aeon.VertiGate) in the reference to access the device functionality directly.

[!INCLUDE [](version-footer.md)]

<!--Reference Style Links -->
[`VertiGate (Device)`]: xref:Aeon.VertiGate.Device
[`VertiGateDataWriter (DeviceDataWriter)`]: xref:Aeon.VertiGate.DeviceDataWriter
