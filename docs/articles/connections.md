## Ports and Connections

This article covers the ports and status indicator on the VertiGate, and how to connect the device to the servo, the computer, and a Harp clock generator.

### Ports

[placeholder - vertigate-devicepinout.svg]{width=600}

> [!WARNING]
> **TODO**: The repository has no hardware design files or board photo, so the connector types and silkscreen labels below are unknown. Replace each label with the one printed on the Dynamixel Controller board and add the connector type in parentheses.

**USB** - This port connects the device to the computer running [Bonsai](harp-bonsai.md). The board shows two serial ports: the first is the MicroPython console and the second is the Harp interface. Use the second one in Bonsai.

**Servo** - The Dynamixel bus. It connects the Dynamixel XM430-W210 servo that moves the gate. The firmware talks to the servo at 1 Mbaud and expects it to answer at ID 1.

**Power** - Supply for the servo and the board.

> [!WARNING]
> **TODO**: Add the supply voltage, the current rating, and the connector type for the servo power input.

**Clock In** - Harp synchronization input. Connect it to the clock output of a Harp clock generator such as the [Harp Timestamp Generator](https://github.com/harp-tech/device.timestampgeneratorgen3). The board receives the synchronization stream at 100 kbaud on this input.

### Indicator Lights

**STATE** - The LED is driven by the microharp core. It is on and off for equal periods of:

- 2 seconds when the device is active, that is, when a Bonsai workflow is running
- 4 seconds when in standby

When the device receives a Harp synchronization clock, the blinks fall on the second boundaries of that clock, so the LEDs of all synchronized devices blink together. Without a clock the LED free-runs with the same periods. The LED is off while visual indicators are disabled in the `Device` operator, and the core has no separate error pattern.

> [!WARNING]
> **TODO**: These periods come from the microharp source and have not been checked on hardware. Confirm them, and replace this callout with the measured pattern if they differ.

### Connections

# [Servo](#tab/servo)

[placeholder - connection-servo.svg]{width=450}

1. Connect the Dynamixel XM430-W210 to the **Servo** port with a Dynamixel TTL cable, and connect the servo power supply to the **Power** port.
2. Set the servo to ID 1 and a baud rate of 1 Mbaud, for example with the Dynamixel Wizard. A servo straight from the factory uses 57600 baud and does not answer the VertiGate.
3. Connect the **USB** port to the computer. The gate homes itself a few seconds after power-up: it moves to the lower end stop and records it as the lowered position.
4. Refer to the [Calibrate the Gate](calibrate-gate.md) article to home the gate from Bonsai, and to the [Move the Gate](move-gate.md) article to move it.

> [!WARNING]
> If the servo is not connected or not powered when the board starts, the gate reports the `Error` state. Correct the connection, then send a `Calibrate` command as described in [Calibrate the Gate](calibrate-gate.md).

# [Harp Synchronization](#tab/harpsynchronization)

[placeholder - connection-harpsynchronization.svg]{width=450}

1. Connect a clock output of a Harp clock generator (e.g. the [Harp Timestamp Generator](https://github.com/harp-tech/device.timestampgeneratorgen3)) to the **Clock In** port.
2. The VertiGate adopts the generator's clock automatically. To verify the connection, check that the **STATE** LEDs of the connected boards blink simultaneously.
3. Refer to the [Harp synchronization clock](https://harp-tech.org/protocol/SynchronizationClock.html) documentation for how devices synchronize.

---

[!INCLUDE [](version-footer.md)]
