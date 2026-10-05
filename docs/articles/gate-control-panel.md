## Gate Control Panel

The Gate Control Panel is a ready-made Bonsai workflow with buttons and sliders for every register of the VertiGate. Use it to exercise a new device, to trim the calibration, or to move the gate by hand during setup, without building a workflow of your own.

Before beginning, [install the Bonsai packages](installation.md#software-packages), including `Bonsai.Gui`, follow the hardware setup [guide](connections.md), and connect the USB cable to the computer. Then download `GateControl.bonsai` and the `Extensions` folder next to it from the [docs/workflows](https://github.com/SainsburyWellcomeCentre/aeon_vertigate/tree/main/docs/workflows) folder of the repository, keep them together, and open `GateControl.bonsai` in Bonsai.

[placeholder - gate-control-panel.png]{width=650}

### Usage

- Select the `VertiGate` device node and set its `PortName` property to the Harp port of the board, which is the second of the two serial ports the board shows. Do not use the first one, which is the MicroPython console.
- Press "Start". The panel opens and every slider moves to the value the device holds now. The label beside each slider shows that value.
- Use the controls:

| Control | What it does |
| ------- | ------------ |
| Lower down, Raise up | Move the gate to an end stop. |
| Calibrate, Stop, Enable motor, Disable motor | Send one `Control` command. |
| Position slider | Move the gate. The gate follows the slider as you drag it. |
| Speed, Torque, Calibration offset | Set a value, then press Apply. |

The three settings have an Apply button because the device stores every write to them in non-volatile memory, and it switches the motor off and on to change the speed or the torque. A slider that wrote on every step would do both many times a second.

> [!NOTE]
> The value a slider shows and the value the device holds can differ. The device masks `Torque` to seven bits, so a write of 200 is stored and reported as 72.

> [!WARNING]
> Only one program can access the device's COM port at a time. Stop the Gate Control Panel before starting another Bonsai workflow that uses the device.

[!INCLUDE [](version-footer.md)]
