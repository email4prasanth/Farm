# Phase 1 Hardware Overview

The SHT31 measures air temperature and relative humidity. The ESP32 reads the measurements over I²C every five minutes and sends them to the computer over USB for testing.

## SHT31 temperature and humidity sensor

The SHT31 breakout is a small board that exposes the sensor’s power and data connections. The ESP32 supplies 3.3 V and uses two signal lines for I²C.

| SHT31 Pin | Function | Phase 1 Usage (ESP32 Connection) | Notes |
| --- | --- | --- | --- |
| VIN / VCC | Power Supply | Connect to 3V3 | The chip supports 2.4V–5.5V, but always power it from the ESP32's 3.3V pin to match logic levels . |
| GND | Ground | Connect to GND | Common ground is essential for I²C communication. |
| SDA | I²C Data | Connect to GPIO 21 | This is the ESP32's default I²C data pin . |
| SCL | I²C Clock | Connect to GPIO 22 | This is the ESP32's default I²C clock pin . |

If the breakout exposes **AD / ADR**, check its documentation for the default address. The Phase 1 sketch expects **0x44**, which the SHT31 selects when its address input is held low. Leave the breakout’s address pin unwired only if the board already sets it low. Leave **AL / ALR** (hardware alert output) unwired for Phase 1. [Sensirion SHT3x-DIS datasheet](https://admin.sensirion.com/media/documents/213E6A3B/63A5A569/Datasheet_SHT3x_DIS.pdf)

## ESP32 microcontroller and development board

| Part | What it does |
| --- | --- |
| **ESP32 chip** | Runs the program and provides GPIO, Wi-Fi, and Bluetooth. |
| **ESP32-WROOM-32 module** | The shielded assembly on the board. It combines the chip with flash memory, a crystal, and an onboard antenna. |
| **ESP32 DevKit V1** | The board you plug in and wire. It adds a USB port, a USB-to-serial bridge, a 3.3 V regulator, header pins, and EN and BOOT buttons. |

**EN** restarts the board. **BOOT** helps enter firmware upload mode if automatic upload fails. When choosing a board,

- check whether it uses Micro-USB or USB-C and confirm that the header layout fits your breadboard.
- Either a 30-pin or 38-pin version can work if it exposes the four connections in the table above.

## GPIO Categories

- safe pins for the current project - GPIO 21 (SDA) & GPIO 22 (SCL) -  default I2C pins they do not have any special boot functions that could interfere with your sensor

For future wiring:

- safe pins are GPIOs - 4 ,5, 16, 17, 18 , 19, 21, 22, 23, 25, 26, 27, 32 AND 33 dont have boot restrictions
- Aviod - **GPIO 6–11 are reserved for flash**, Never connect anything to these pins.
- special consideration  - GPIO 0, 2, 5, 12, and 15 affect startup, and
- Input only GPIOS 34, 35, 36, and 39.

[Espressif ESP32 GPIO reference](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/peripherals/gpio.html)

## USB power and data

- **During testing**, connect the computer to the DevKit’s USB port with a data cable. The same cable powers the board, uploads the sketch, and carries readings to the Serial Monitor.
- **After testing**, connect the DevKit’s USB port to a regulated **5 V DC, 2 A USB wall adapter** rated for **230 V AC, 50 Hz** input. Keep the adapter in a dry location outside the grow room. The sensor still sends readings to the ESP32 over I²C. Saving readings in Phase 1 requires a serial logger.

See the [Phase 1 guide](01_phase-1.md) for assembly, firmware upload, and stability testing.

## Details of ESP32 Development board

- **Chip** - Contains CPU, Wi-ri, bluetooth and pheriperahals
- **Module** - Mount chip with flash memory, Crystal oscillator and PCB Antenna covers with meatl sheet, Example:ESP32-WROOM-32,ESP32-WROOM-32E, ESP32-WROVER
- **Development Board** - Module with USB, Stable 3.3V, bread board friendly, Reset & Boot control

- how many pins need 30 or 38
- Micro USB or USB Type-c ( for data and power transfer)

- USB to UART Bridge is a transulatot between Laptop to ESP32 - Example: CP2102 , CH340, CH9102- need to check to install drivers so the OS can detect.
- when a power is on need to check the power led if fails there may be problem with cable, power source or board mostly it is available at GPIO2 Check doc
- EN (hard ware reset) and boot button (firm ware download board)
- PCB Antenna with 2.4 Ghz wifi and bluetooth
