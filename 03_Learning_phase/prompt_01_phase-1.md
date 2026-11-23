# Phase 1 — Hardware Setup and Testing

> **Phase 1 only.** I currently want to work exclusively on Phase 1. Wait for me to explicitly request the next phase before implementing Wi-Fi, cloud, databases, FastAPI, dashboards, or WhatsApp.

## Selected Hardware

- ESP32 DevKit V1 (ESP32-WROOM-32).
- Genuine Sensirion SHT31 temperature and humidity breakout module.
- Solderless breadboard.
- Male-to-male and male-to-female jumper wires as required.
- Good-quality 5 V / 2 A USB power adapter and compatible USB cable.
- Small protective plastic enclosure for electronics.

The approximate Phase 1 budget is **₹1,500–₹2,500**. Prioritize accuracy, reliability, and long-term usability over choosing the cheapest components. Keep the SHT31 unless there is a strong technical or availability reason to use a lower-accuracy sensor.

## Wiring Configuration

| SHT31 pin | ESP32 DevKit V1 pin |
| --- | --- |
| VIN / VCC | 3V3 |
| GND | GND |
| SDA | GPIO 21 |
| SCL | GPIO 22 |

- The typical SHT31 I²C address is `0x44`.
- Check the actual module pin labels and documentation before connecting.
- Power the sensor from 3.3 V unless the breakout documentation explicitly supports another voltage.
- Power the ESP32 through USB with a suitable 5 V adapter and cable.
- Do not connect 5 V directly to the sensor or the ESP32 GPIO pins.

## Hardware and Environmental Precautions

The mushroom room can have very high humidity.

- Keep the ESP32, breadboard, and power connections outside the humid growing room wherever possible.
- Place the sensor to measure representative room air.
- Protect the sensor from direct water droplets, mist, and condensation.
- Use a ventilated sensor enclosure that allows air to circulate.
- Treat the PCB as susceptible to water even when the sensor is rated to measure 0–100% RH.
- Design the electronics enclosure and sensor placement separately.

## Software for Phase 1

Use Arduino IDE and Arduino C++ for the ESP32.

I know Python but am new to Arduino/embedded; explain the C++ code clearly.

### First Program

1. Initialize the SHT31.
2. Read the temperature and humidity.
3. Print both readings to the Serial Monitor.
4. Repeat every 5 minutes.
5. Handle initialization and reading failures.
6. Include timestamps or elapsed-time information where practical.

Do not introduce Wi-Fi or cloud code during Phase 1.

## Phase 1 Acceptance Criteria

- The ESP32 detects the SHT31.
- Temperature readings display correctly.
- Humidity readings display correctly.
- The system runs continuously without frequent resets or sensor errors.
- Readings are recorded every 5 minutes.
- The 48–72-hour stability test is complete.
- Readings are compared with a trusted thermometer and hygrometer, if available.

## 6. How I Want You to Guide Me

- Work phase by phase, in order.
- Explain why each hardware component is needed.
- Provide exact component specifications when recommending purchases.
- Consider Indian availability and prices in INR.
- Prefer components available in Chennai, Tirupati, or reputable Indian online electronics stores.
- Do not invent shop availability, prices, or product specifications.
- When I ask for current products or prices, verify them using current sources.
- Provide clear wiring diagrams and pin-to-pin connection tables.
- Provide complete, runnable code.
- Explain how to install libraries and configure Arduino IDE.
- Include testing steps and expected Serial Monitor output.
- Troubleshoot errors systematically, one step at a time.
- Assume I am new to electronics and may have no soldering equipment.
- Keep future expansion to multiple mushroom rooms in mind.
- Keep the solution cost-effective without compromising important reliability or safety requirements.

## 7. How to Start This New Conversation

First, understand and acknowledge the complete project context. I am preparing to purchase and assemble the Phase 1 hardware using an ESP32 DevKit V1 and a genuine SHT31 sensor.

1. Help me finalize the Phase 1 component list and verify the specifications.
2. Guide me through hardware assembly, Arduino IDE setup, wiring, and sensor testing.
3. Guide me through the 48–72-hour stability test.

Continue from this point in the project and use the selected architecture.



