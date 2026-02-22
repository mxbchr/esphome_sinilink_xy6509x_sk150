# ESPHome Sinilink Power Supply (XY6509X / SK150) Modbus Integration

This project brings Sinilink / bench power supplies to ESPHome (and therefore Home Assistant) via Modbus over UART. It’s intended for supplies like the **Sinlink XY6509X** and **SK150**, and it effectively mimics the functionality of the optional OEM Wi-Fi add-on module (often sold as **XY-WFPOW**) by speaking Modbus directly from an ESP device.

## Supported devices

* XY6509X (tested)
* SK150 (tested)
* Other Sinlink PSUs may work if they use the same Modbus protocol/registers, but you’ll likely need to add a model-specific substitutions table for scaling/limits.

## Hardware wiring

Connect the PSU UART header directly to your ESP (I used an ESP32-C3):

* **5V** from the PSU → ESP 5V (or VIN, depending on board)
* **GND** → ESP GND
* **TX (PSU)** → **RX (ESP)**
* **RX (PSU)** → **TX (ESP)**
Pinout is: **5V, TX, RX, GND**.
5V is usualy marked on the PSU Board. I could not find a matching connector so I soldered it directly onto the board.
Important notes:

## ESPHome configuration

1. Copy the YAML into your ESPHome device config.
2. Set the model in `substitutions` at the top:

```yaml
substitutions:
  # Change this model to fit your PSU.
  # e.g. "xy6509x"; "sk150"
  model: "xy6509x"
```

The selected `model` is used to pull all scaling factors, ranges, and limits from the model-specific substitution table (`${${model}_...}`), so you don’t have to manually edit dozens of values.

If you want to add another PSU model, create a new block of substitutions mirroring the existing XY6509X / SK150 entries (max voltage/current, multipliers, accuracies, protection limits). Everything else should work unchanged.

## Home Assistant entities and how to use them

After pairing the device with Home Assistant via the ESPHome integration, you’ll typically see entities grouped conceptually as:

1. Sensors / Configuration
   Those are the - more general - settings that can be accessed by long-pressing the "SW"-Button on the PSU

* Output enable (On/Off)
* Setpoints like V_SET / I_SET
* Various feature toggles (buzzer, MPPT, constant power, etc.)
* Telemetry (VOUT/IOUT/POWER, temps, VIN, runtime, counters)


2. Controls / Memory groups (M0–M9) editing
   Adds convenient editing of the PSU’s memory groups.
   Those are the settings you’d normally reach via the PSU’s “I-SET"-Button long press  menu
   In Home Assistant:

* Select which memory group you want to edit using **“Memory group to edit”** (M0…M9)
* Change memory-group-specific setpoints and limits (V/I set, protection values, timers, etc.)

## Credits / origin

* Originally based on `framenic/sinilink-modbus`
* Extended and adapted for XY6509X / SK150
* Added broader register coverage and memory-group editing support

