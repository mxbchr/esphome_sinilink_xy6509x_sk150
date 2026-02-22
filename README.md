# ESPHome Sinilink Power Supply (XY6509X / SK150) Modbus Integration

This project brings Sinilink / bench power supplies to ESPHome (and therefore Home Assistant) via Modbus over UART. It’s intended for supplies like the **Sinlink XY6509X** and **SK150**, and it effectively mimics the functionality of the optional OEM Wi-Fi add-on module (often sold as **XY-WFPOW**) by speaking Modbus directly from an ESP device.

## Supported devices

* XY6509X (tested)
* SK150 (tested)
* Other Sinlink PSUs may work if they use the same Modbus protocol/registers, but you’ll likely need to add a model-specific substitutions table for scaling/limits.

## Hardware wiring

Connect the PSU UART directly to your ESP (I used an ESP32-C3):

* **5V** from the PSU → ESP 5V (or VIN, depending on board)
* **GND** → ESP GND
* **TX (PSU)** → **RX (ESP)**
* **RX (PSU)** → **TX (ESP)**
Pinout is: **5V, TX, RX, GND**.

5V is usualy marked on the PSU Board. I could not find a matching connector so I soldered it directly onto the board.

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
   Those are the settings you’d normally reach via the PSU’s “I-SET"-Button long press menu.
   
   In Home Assistant:

* Select which memory group you want to edit using **“Memory group to edit”** (M0…M9)
* Change memory-group-specific setpoints and limits (V/I set, protection values, timers, etc.)

Update interval is set to **1s** for all PSU entities. This works fine. Everything below delivered mixed results.

## Credits / origin

* Originally based on `framenic/sinilink-modbus`
* Extended and adapted for XY6509X / SK150
* Added broader register coverage and memory-group editing support
* I mostly used the register addresses documented here for the XY-SK60: https://www.mikrocontroller.net/attachment/641206/Powermodule_XY-SK60_Mosbus_protocol_english.pdf

__________

<img width="262" height="795" alt="Screenshot 2026-02-22 at 18 05 49" src="https://github.com/user-attachments/assets/4046e4b7-2310-411e-883e-b6a9ff60edb4" />
<img width="260" height="795" alt="Screenshot 2026-02-22 at 18 05 59" src="https://github.com/user-attachments/assets/16ef1cf3-517b-4470-b2c6-faa28eed9dda" />
<img width="261" height="876" alt="Screenshot 2026-02-22 at 18 06 17" src="https://github.com/user-attachments/assets/2c014f7a-d0fc-4644-8fd3-1a57d341045e" />
<img width="265" height="572" alt="Screenshot 2026-02-22 at 18 06 36" src="https://github.com/user-attachments/assets/ec40bd4c-439d-4bd0-ba3b-cb2b7f2ae6b0" />
<img width="524" height="546" alt="1" src="https://github.com/user-attachments/assets/a69d0435-908c-464f-a3e7-50f42b6d7317" />
<img width="720" height="532" alt="2" src="https://github.com/user-attachments/assets/f475bc25-df30-41d1-a04b-7202ea8704aa" />
