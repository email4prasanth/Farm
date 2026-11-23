# Phase 1 — Hardware Setup and Testing

Build and validate a local temperature and relative humidity monitor for the mushroom-growing room in Tirupati, Andhra Pradesh. The ESP32 DevKit V1 reads a Sensirion SHT31 and prints measurements over USB every five minutes.

Complete the modules in order and use each module's exit criteria to decide when to move on. Wi-Fi, MQTT, cloud services, dashboards, and alerts begin in later phases.

## Module 1 — Purchase and Verify the Hardware

| Component | What to verify before ordering | Budget estimate |
| --- | --- | ---: |
| ESP32 DevKit V1 | ESP32-WROOM-32, 3.3 V GPIO, onboard regulator, and clearly labeled pins. Check the board width, pin count, and USB connector. | ₹350–₹550 |
| Sensirion SHT31 breakout | Traceable SHT31-DIS part, 3.3 V operation, documented I²C pull-ups, and accessible VIN/VCC, GND, SDA, and SCL pins. Choose pre-soldered headers for solderless assembly. | ₹450–₹900 |
| Solderless breadboard | 400 or 830 tie points. Check that the ESP32 fits across the center gap with access to both pin rows. | ₹80–₹150 |
| Jumper wires | Male-to-male and male-to-female as needed. Use short leads for the bench setup. | ₹100–₹200 |
| USB power adapter | Regulated 5 V / 2 A for stand-alone operation. | ₹150–₹250 |
| USB data cable | Matches the ESP32 connector and supports data transfer. | ₹100–₹150 |
| Sensor enclosure | Ventilated, with protection from direct droplets and mist. | ₹100–₹250 |
| Electronics enclosure | Separate enclosure for the ESP32 and breadboard, with protected cable entry. | ₹150–₹300 |

The target budget is **₹1,500–₹2,500**. The estimates total **₹1,480–₹2,750** including both enclosures, before delivery. Confirm live prices, stock, and shipping before ordering. Use a traceable seller for the SHT31 and keep the breakout documentation and invoices. Record the model, seller, actual price, and product link for each item, plus the ESP32 board pin count and USB connector.

The SHT31-DIS datasheet lists typical accuracy of ±0.2 °C from 0 to 90 °C and ±2 % RH. Use the datasheet's tolerance curves when evaluating readings near the high-humidity end of the range. [Sensirion SHT3x-DIS datasheet](https://admin.sensirion.com/media/documents/213E6A3B/63A5A569/Datasheet_SHT3x_DIS.pdf)

**Exit criteria**

- [ ] The essential components have arrived and match the specifications above.
- [ ] Both enclosures have been selected and the purchase details are recorded.
[ESP32 DevKit V1 Tutorial](https://www.youtube.com/watch?v=n19DJIPB1sE)

[Assembly Turtorial-1] (https://microcontrollerslab.com/esp32-sht31-temperature-humidity-sensor-tutorial/)

[Assembly Turtorial-2](https://esp32tutorials.com/sht31-esp32-esp-idf-tutorial/)

## Module 2 — Assemble and Power Up

Keep USB power disconnected while wiring. Place the ESP32 across the breadboard's center gap so its two pin rows are electrically separate. If the board covers the usable holes, use a wider layout or two breadboards so you can access both sides. Verify the actual silkscreen labels before connecting the four wires.

| SHT31 breakout | ESP32 DevKit V1 | Purpose |
| --- | --- | --- |
| VIN / VCC | 3V3 | 3.3 V sensor supply |
| GND | GND | Common electrical reference |
| SDA | GPIO 21 | I²C data |
| SCL | GPIO 22 | I²C clock |

1. Inspect the wiring against the table. Check for swapped or loose leads, bridges between adjacent pins, and a possible short between 3V3 and GND. Keep 5 V off the sensor and ESP32 GPIO pins.
2. Connect the ESP32 to the Windows computer with the USB data cable. Check for a COM port in **Device Manager → Ports (COM & LPT)**. Disconnect USB immediately if the board gets unusually hot or you notice smoke or a burning smell.
3. Photograph the assembly with the pin labels visible.

**Exit criteria**

- [ ] Wiring matches the table and the ESP32 powers up normally.
- [ ] The assembly photo is saved. If Windows shows no COM port, follow the matching troubleshooting row in Module 3 before uploading.

## Module 3 — Install, Upload, and Check the Firmware

### Set Up Arduino IDE on Windows

1. Install [Arduino IDE 2.x](https://www.arduino.cc/en/software).
2. In **File → Preferences → Additional Boards Manager URLs**, add the stable ESP32 board index below. In **Boards Manager**, install **esp32 by Espressif Systems** and record the installed version. [Espressif installation instructions](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html)

   ```text
   https://espressif.github.io/arduino-esp32/package_esp32_index.json
   ```

3. In **Library Manager**, install **Adafruit SHT31 Library** and accept its **Adafruit BusIO** dependency. [Adafruit library metadata](https://github.com/adafruit/Adafruit_SHT31/blob/master/library.properties)
4. Select **ESP32 Dev Module** and the COM port identified in Module 2. Use an upload speed of **115200** and leave the other board settings at their defaults.

### Create and Upload the Sketch

Save the sketch as `Phase1_SHT31_Monitor.ino`. It takes a reading at startup, then every five minutes. If initialization or a reading fails, the next scheduled attempt tries to initialize the sensor again. The elapsed time is measured from boot and resets to zero when the ESP32 restarts.

```cpp
#include <Wire.h>
#include <Adafruit_SHT31.h>

constexpr int SDA_PIN = 21;
constexpr int SCL_PIN = 22;
constexpr uint8_t SHT31_ADDRESS = 0x44;
constexpr unsigned long READ_INTERVAL_MS = 5UL * 60UL * 1000UL;

Adafruit_SHT31 sht31;
bool sensorReady = false;
unsigned long lastAttemptMs = 0;

void readSensor() {
  if (!sensorReady) {
    sensorReady = sht31.begin(SHT31_ADDRESS);
    if (!sensorReady) {
      Serial.printf("ERROR elapsed_s=%lu SHT31 initialization failed\n",
                    millis() / 1000UL);
      return;
    }
    Serial.printf("SHT31 detected at 0x%02X\n",
                  static_cast<unsigned int>(SHT31_ADDRESS));
  }

  float temperature;
  float humidity;
  if (!sht31.readBoth(&temperature, &humidity)) {
    Serial.printf("ERROR elapsed_s=%lu SHT31 reading failed\n",
                  millis() / 1000UL);
    sensorReady = false;
    return;
  }

  Serial.printf("elapsed_s=%lu temp_c=%.2f humidity_pct=%.2f\n",
                millis() / 1000UL, temperature, humidity);
}

void setup() {
  Serial.begin(115200);
  Wire.begin(SDA_PIN, SCL_PIN);
  Serial.println("BOOT Phase 1 - SHT31 monitor");
  lastAttemptMs = millis();
  readSensor();
}

void loop() {
  const unsigned long now = millis();
  if (now - lastAttemptMs >= READ_INTERVAL_MS) {
    lastAttemptMs = now;
    readSensor();
  }
  delay(100);
}
```

`Wire.begin()` configures the I²C pins. `readBoth()` gets temperature and humidity in one measurement. The subtraction in `loop()` uses elapsed milliseconds to schedule readings. [Adafruit SHT31 API](https://github.com/adafruit/Adafruit_SHT31/blob/master/Adafruit_SHT31.h)

1. Click **Verify**, then **Upload**. Open **Serial Monitor** at **115200 baud**. If you missed the boot output, press **EN/RESET** with the monitor open.
2. Check for a reading at startup and another five minutes later. Your measured values will vary.

   ```text
   BOOT Phase 1 - SHT31 monitor
   SHT31 detected at 0x44
   elapsed_s=0 temp_c=27.45 humidity_pct=64.20
   elapsed_s=300 temp_c=27.48 humidity_pct=64.10
   ```

3. Verify the error path without disturbing powered wiring. Temporarily set `SHT31_ADDRESS` to an unused address such as `0x46` on this single-sensor bus, upload, and confirm the initialization error at startup and at the next scheduled attempt. Restore the breakout's documented address and upload the final sketch.

### Troubleshooting

| Symptom | What to check |
| --- | --- |
| No COM port in Windows | Try a known USB data cable and another USB port. If a driver is needed, identify the USB-to-serial chip in Device Manager and get the matching driver from its manufacturer. |
| Upload stops at `Connecting...` | Confirm the board and COM port. If the board needs manual bootloader entry, hold **BOOT** during connection and release it once writing begins. |
| SHT31 initialization or reading error | Check the wiring table, breakout address, pull-ups, and lead length. Disconnect USB before touching the wiring. |
| Frequent restarts | Check USB power, the cable, and possible shorts. Use repeated `BOOT` lines or a drop in elapsed time to identify resets. |
| Implausible room readings | Check direct mist, condensation, heat sources, and enclosure airflow. Compare with a reference instrument if available. |

**Exit criteria**

- [ ] The final sketch compiles and uploads, and the SHT31 returns both readings at startup and at five-minute intervals.
- [ ] The controlled address test shows an error and a scheduled retry. The correct address is restored and the final sketch and tool versions are saved.

## Module 4 — Place and Test the Device

### Place the Sensor and Electronics

Position the SHT31 where air represents the growing area. Use a ventilated enclosure and keep the sensor away from direct mist, droplets, wet walls, fans, sunlight, and heat sources. A high humidity measurement range gives no protection against liquid water on the breakout.

Keep the ESP32, breadboard, and exposed connections in a separate, dry enclosure outside the room wherever practical. Keep I²C leads short and verify the actual cable run before mounting permanently. If the run produces intermittent readings, revise the placement or cable arrangement before starting the stability test. Photograph the final setup.

### Compare with a Reference Instrument

If you have a trusted thermometer and hygrometer, place them near the SHT31 without touching it. Let the instruments stabilize in the same air, then record paired values at the same time. Interpret differences using each instrument's specifications and calibration status. If the room stays above 80% RH, repeat the comparison near the end of the stability test. Sensirion reports that sustained exposure above this level can temporarily shift the humidity reading. [Sensirion operating conditions](https://admin.sensirion.com/media/documents/213E6A3B/63A5A569/Datasheet_SHT3x_DIS.pdf)

| Date and time | SHT31 °C / % RH | Reference °C / % RH | SHT31 minus reference (°C / percentage points) |
| --- | --- | --- | --- |
| | | | |

### Log a 48–72-Hour Stability Test

1. Use the intended sensor position and cable run. Power the ESP32 through its USB data cable from the Windows computer. Keep the computer on AC power and disable sleep for the test.
2. Close Arduino Serial Monitor to release the COM port. In PuTTY, choose **Serial**, select the ESP32's COM port, and set **115200 baud, 8 data bits, no parity, 1 stop bit, and no flow control**. Under **Session → Logging**, select **All session output** and set the log path, such as `phase1-stability.log`. Open the session. [PuTTY downloads](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) · [PuTTY documentation](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)
3. Press **EN/RESET** once with the logger open. Record the start time, treating this boot as the start of the test. Check that the file contains the boot line and first reading, then confirm the next reading is added about five minutes later.
4. Run for at least **48 hours**, preferably **72**. Keep the serial logger open and the sensor in place. Note any log gaps, unexpected boots, sensor errors, power interruptions, or signs of condensation.
5. Record the end time and review the file. With the startup sample included and the end boundary excluded, an uninterrupted run has 576 scheduled samples in 48 hours or 864 in 72 hours. Compare the count and elapsed times to the actual logging window and account for any gaps.

For stand-alone operation on the wall adapter, use a separate serial logger if you need a record. USB power alone leaves the measurements unrecorded.

| Test record | Result |
| --- | --- |
| Start, end, and run duration | |
| Expected and captured valid readings | |
| Unexpected resets, sensor errors, and log gaps | |
| Power interruptions and placement observations | |
| Log file, reference comparison, and photos | |
| Result and any corrective action | |

**Exit criteria**

- [ ] The sensor measures representative air, and wiring and electronics remain dry at the chosen placement.
- [ ] A reference comparison is documented if an instrument is available.
- [ ] The saved log covers at least 48 hours and shows five-minute sampling without recurring resets or sensor communication failures. Gaps and interruptions are accounted for. Repeat an interrupted run if it cannot demonstrate 48 continuous hours.

Phase 1 is complete when all four modules meet their exit criteria. Keep this guide, the purchase and assembly records, final sketch, serial log, comparison results if available, and placement photos together.
