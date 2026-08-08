# CraftifAI Hackathon — Wi-Fi Placement Sentinel

> **Portable Wi-Fi Link Health & Placement Validation Node**

A portable ESP32-C3 based wireless monitoring device designed to identify locations where Wi-Fi connectivity is unexpectedly degraded.

The system monitors **RSSI, Wi-Fi channel, gateway latency and packet loss**, with ultrasonic distance sensing being integrated to provide physical-distance context.

The firmware is developed and iteratively refined using **FirmGen**, converting natural-language requirements into ESP-IDF firmware that is built, flashed and validated on real ESP32-C3 hardware.

---

## 🎥 Demo

### Live Hardware Demonstration

**Demo video:**
[▶️ Watch the 1–2 minute demonstration](https://drive.google.com/file/d/1C_IKEi5QNVUB66PYlk0kHX-5XV7ZcYZt/view?usp=drive_link)

The demonstration shows:
1. FirmGen project and firmware-generation workflow
2. ESP32-C3 hardware
3. Wi-Fi connection and monitoring
4. RSSI measurement
5. Gateway latency
6. Packet-loss monitoring
7. RF obstruction experiment
8. Wireless degradation detection

Find the firmware package here : https://drive.google.com/drive/folders/1f0HW022s0XVICStRTLlObq907P5-6NeR?usp=drive_link
---

## 📸 Hardware

<!-- Replace the filename with your uploaded image -->
Network AP analyzer for warehouses
screenshots : (https://drive.google.com/drive/folders/1KJURYm1enSdwUEfygDc2UrblKAR0jW07?usp=drive_link)

**Prototype hardware:**
- ESP32-C3 development board
- HC-SR04 ultrasonic sensor
- Breadboard
- Jumper wires
- Smartphone hotspot used as the reference AP

---

# 1. Problem Statement

Wi-Fi connectivity in warehouses, offices, laboratories and industrial environments can degrade because of:

- Poor access-point placement
- Physical obstructions
- Metal racks and machinery
- Walls and structures
- RF interference
- Coverage gaps
- Changes in the surrounding RF environment

Identifying these problems often requires specialized Wi-Fi survey tools or a laptop running network-analysis software.

A technician walking through a facility should be able to quickly identify locations where wireless performance is unexpectedly poor.

### Problem

> How can we build a low-cost, portable device that allows a technician to move through an environment and immediately identify areas experiencing degraded Wi-Fi connectivity?

---

# 2. Proposed Solution

**Wi-Fi Placement Sentinel** is a handheld ESP32-C3 monitoring node.

The device connects to a Wi-Fi access point and continuously measures:

- RSSI
- Wi-Fi channel
- Gateway IP
- Gateway latency
- Packet loss 
- Ultrasonic distance

The measurements are used to identify potential wireless degradation.

The system does **not** claim to determine the exact cause of degradation automatically.

A degraded measurement may indicate:

- Possible RF obstruction
- Poor AP placement
- Coverage limitation
- Antenna orientation issue
- RF interference
- Other environmental effects

The device therefore acts as a **portable preliminary diagnostic tool** that can flag locations for further inspection.

---

# 3. System Architecture

```text
                  Wi-Fi Access Point
                         │
                         │
                    Wi-Fi Link
                         │
                         ▼
                ┌─────────────────┐
                │     ESP32-C3    │
                │                 │
                │  Wi-Fi Monitor  │
                │       │         │
                │       ├─ RSSI   │
                │       ├─ Channel│
                │       ├─ Latency│
                │       └─ Loss   │
                │                 │
                │  HC-SR04        │
                │       │         │
                │    Distance     │
                └────────┬────────┘
                         │
                         ▼
                  Link Assessment
                         │
                 ┌───────┴───────┐
                 ▼               ▼
               GOOD           DEGRADED
                 │               │
                 ▼               ▼
            Monitoring       Inspection
```

The Wi-Fi Monitor task runs continuously on FreeRTOS, sampling the link every 2 seconds and driving an onboard buzzer for immediate audible feedback in the field.

---

# 4. Hardware

| Component | Role |
|---|---|
| ESP32-C3 dev board | Wi-Fi monitoring, link-health state machine |
| HC-SR04 ultrasonic sensor | Distance-to-AP context *(integration in progress)* |
| Active buzzer (GPIO6) | Immediate audible alert on degraded link |
| Breadboard + jumper wires | Prototype wiring |
| Smartphone hotspot | Reference access point for testing |

---

# 5. Software Stack

| Layer | Technology |
|---|---|
| Firmware | ESP-IDF (C), FreeRTOS |
| Firmware generation | FirmGen — natural-language requirements → ESP-IDF firmware |

---

# 6. Firmware Development Workflow

Firmware is developed iteratively with **FirmGen**:

1. Requirements are written in natural language (e.g. "add a link-health state machine with GOOD/WARNING/DEGRADED states, driven by RSSI and gateway latency")
2. FirmGen generates or updates the corresponding ESP-IDF C code
3. The firmware is built and flashed to real ESP32-C3 hardware
4. Behavior is validated against the physical device (RSSI readings, buzzer response) before the next iteration

---

# 7. Repository Structure

```text
.
├── main/
│   ├── main.c                 # app entry point, Wi-Fi init, task startup
│   ├── wifi_monitor.c/.h       # RSSI / channel / latency measurement, link-health state machine
│   └── CMakeLists.txt
├── docs/
│   └── images/                 # hardware screenshots
├── CMakeLists.txt              # top-level ESP-IDF project file
└── README.md
```

---

# 8. Getting Started

### Prerequisites
- [ESP-IDF](https://docs.espressif.com/projects/esp-idf/en/stable/esp32c3/get-started/index.html) installed and set up for the ESP32-C3 target
- ESP32-C3 dev board connected via USB

### Build & Flash

```bash
idf.py set-target esp32c3
idf.py menuconfig     # set Wi-Fi SSID/password if configured via Kconfig
idf.py build
idf.py -p /dev/ttyUSB0 flash monitor   # adjust the port for your OS
```

---

# 9. Current Status & Limitations

To keep this accurate rather than aspirational:

**Implemented and validated on hardware:**
- Wi-Fi connection with automatic reconnect
- RSSI and channel measurement
- Gateway IP discovery and round-trip latency measurement
- GOOD / WARNING / DEGRADED link-health state machine
- Buzzer alert on GPIO6

**In progress / not yet validated:**
- Packet-loss measurement
- HC-SR04 distance sensing and distance-aware placement assessment

The demo video and screenshots in this README should reflect only what's actually running on the device at recording time — update the steps list above if the final demo doesn't cover packet loss or distance sensing.

---

# License

<!-- Add license, e.g. MIT -->
