# IoT-Based Mushroom Farm Monitoring System

Act as my technical project guide, IoT engineer, cloud architect, and hands-on mentor. Help me design and implement a reliable, low-cost IoT-based environmental monitoring system for my mushroom farming unit in Tirupati, Andhra Pradesh, India.

## 1. My Technical Background

- I am a software/DevOps engineer with approximately 5 years of experience.
- I have experience with AWS, Azure, Docker, Kubernetes, Terraform, CI/CD, and cloud infrastructure.
- I can write Python and work with FastAPI.
- I have ECE Background with little knowledge on electronics, microcontrollers, sensors but not on hardware wiring.
- Explain hardware concepts from the basics, but do not over-explain software concepts I already understand.
- Give practical, step-by-step instructions with wiring diagrams, component lists, commands, code, and testing procedures.
- Prefer Windows-compatible instructions and PowerShell commands wherever applicable.

## 2. Project Objective

Build an IoT-based mushroom cultivation room monitoring system that measures:

- Temperature (°C)
- Relative humidity (% RH)

The system should eventually send sensor readings to the cloud, store historical data, display live and historical measurements, and send WhatsApp notifications when readings cross configured thresholds. It must be reliable for long-term use in a high-humidity mushroom environment, and support adding more sensors and monitoring multiple mushroom-growing rooms in the future.

## 3. Planned Technology Architecture

1. SHT31 temperature and humidity sensor
2. ESP32 microcontroller
3. Wi-Fi
4. MQTT
5. Cloud IoT endpoint (AWS IoT Core or Azure IoT Hub)
6. Serverless ingestion (AWS Lambda or Azure Functions)
7. Managed database (MongoDB, MySQL, or PostgreSQL, depending on fit)
8. FastAPI
9. Dashboard (Streamlit or React)
10. WhatsApp notifications

### Planned Capabilities

- Live temperature and humidity monitoring.
- Periodic sensor readings, initially every 5 minutes.
- Historical data storage and visualization.
- Temperature and humidity threshold alerts.
- WhatsApp notifications.
- Data export to CSV and Excel.
- Multiple sensor and device support.
- Offline handling and recovery if Wi-Fi or internet connectivity fails.
- Secure device authentication and cloud communication.

## 4. Implementation Roadmap

Implement the project in the following order. Work phase by phase and do not skip ahead.

### Phase 1 — Hardware Setup and Testing

1. Purchase and assemble an ESP32 DevKit V1 and a genuine SHT31 sensor.
2. Connect the sensor to the ESP32 using the wiring table in Section 5.
3. Install Arduino IDE, ESP32 board support, and the SHT31 library.
4. Read temperature and humidity, and print both to the Serial Monitor every 5 minutes with timestamps.
5. Handle sensor initialization and reading failures gracefully.
6. Run a continuous 48–72-hour stability test.
7. Validate accuracy against a trusted thermometer and hygrometer.

**Phase 1 is limited to local hardware testing.** Wi-Fi and cloud integration begin in Phase 2.

### Phase 2 — Hardware and Cloud Integration

1. Connect the ESP32 to Wi-Fi.
2. Configure the cloud IoT endpoint (AWS IoT Core or Azure IoT Hub) with certificates, policies, and MQTT.
3. Publish sensor readings securely to the cloud.
4. Implement local buffering and offline recovery for Wi-Fi or internet outages.
5. Verify end-to-end message delivery with cloud-side logging.

### Phase 3 — Optimized Cloud Resource Setup and Dashboard

1. Choose AWS (serverless preferred) or Azure and a suitable managed database (MongoDB, MySQL, or PostgreSQL).
2. Build ingestion with AWS Lambda or Azure Functions to validate and store temperature, humidity, device ID, room, and timestamp.
3. Design the schema to support multiple rooms and devices.
4. Build FastAPI REST endpoints for current and historical readings, with filters for device, room, and time range.
5. Build a dashboard with live readings, historical graphs and trends, and CSV and Excel exports. Prefer Streamlit for speed and consider React later if needed.
6. Apply security, logging, monitoring, error handling, and cost optimization.

### Phase 4 — WhatsApp Integration and Notifications

1. Configure WhatsApp Cloud API or another suitable provider.
2. Send alerts when temperature or humidity crosses configured limits, with optional periodic updates.
3. Prevent duplicate alerts and implement sensible alert cooldowns.
4. Add delivery logging and failure handling.

## 5. Phase 1 Wiring

| SHT31 pin | ESP32 DevKit V1 pin |
| --- | --- |
| VIN / VCC | 3V3 |
| GND | GND |
| SDA | GPIO 21 |
| SCL | GPIO 22 |

The typical SHT31 I²C address is `0x44`. Check your breakout module’s pin labels and documentation before wiring.
