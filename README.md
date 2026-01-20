<div align="center">

# Smart Access and Parking System

[![Arduino](https://img.shields.io/badge/Platform-Arduino-blue.svg)](https://www.arduino.cc/)
[![RFID](https://img.shields.io/badge/Auth-RFID%20MFRC522-green.svg)](/)
[![OLED](https://img.shields.io/badge/Display-SSD1306-orange.svg)](/)
[![CDAC](https://img.shields.io/badge/CDAC-Workshop-purple.svg)](/)

**An IoT-based dual-gate access control system with RFID authentication and ultrasonic proximity detection**

[Overview](#overview) | [Features](#features) | [Hardware](#hardware) | [Demos](#demonstrations) | [Author](#author)

</div>

---

## About

This project was developed during the **CDAC (Centre for Development of Advanced Computing) Workshop** at VIT Vellore from November 2024 to December 2024. It demonstrates an IoT-based dual-gate access control system integrating RFID authentication and ultrasonic proximity detection.

| | |
|---|---|
| **Institution** | CDAC Workshop, VIT Vellore |
| **Duration** | November 2024 - December 2024 |
| **Team Size** | 2 |
| **Role** | Team Lead |
| **Tools** | Arduino IDE, VSCode |

### Key Achievements

- Developed IoT-based **dual-gate access control system** on Aries V3.0 board
- Integrated **MFRC522 RFID module** for secure employee verification at Gate 2 with UID validation
- Implemented **HC-SR04 ultrasonic sensor** for automated Gate 1 control based on distance (<50cm threshold)
- Integrated **servo motors** for door actuation
- Added **RGB LEDs** for status indication and **buzzer** for unauthorized access alerts
- Implemented **SSD1306 OLED display** for real-time feedback using Arduino framework

<div align="center">
<img src="images/workshop_setup.jpeg" alt="Workshop Setup" width="600"/>

*Development setup at CDAC Workshop, VIT Vellore*
</div>

---

## Overview

A microcontroller-based access and parking gate system featuring:
- **Ultrasonic ranging** to auto-open Gate 1 when a vehicle/person approaches
- **RFID (MFRC522)** authentication to control Gate 2
- **Dual servos** for two barrier doors
- **OLED (SSD1306)** for on-device status messages
- **Tri-color LED + buzzer** for clear user feedback

---

## Features

| Feature | Description |
|---------|-------------|
| **Proximity Gate (Gate 1)** | Opens when distance ≤ 50 cm; closes automatically when clear |
| **RFID Gate (Gate 2)** | Valid UID opens gate; invalid UID triggers buzzer and OLED error |
| **Visual Feedback** | RYG LEDs indicate system status |
| **Audio Feedback** | Buzzer alerts on unauthorized attempts |
| **Display** | 128×32 OLED shows init/status/denied messages |
| **Safe Motion** | Timed servo moves with state tracking to avoid jitter |

---

## System Architecture

```
                    +------------------+
                    |   Aries V3.0     |
                    |   Microcontroller|
                    +--------+---------+
                             |
        +--------------------+--------------------+
        |                    |                    |
        v                    v                    v
+---------------+    +---------------+    +---------------+
|  HC-SR04      |    |  MFRC522      |    |  SSD1306      |
|  Ultrasonic   |    |  RFID Reader  |    |  OLED Display |
|  (Gate 1)     |    |  (Gate 2)     |    |  (128x32)     |
+---------------+    +---------------+    +---------------+
        |                    |
        v                    v
+---------------+    +---------------+
|  Servo Motor  |    |  Servo Motor  |
|  (Gate 1)     |    |  (Gate 2)     |
+---------------+    +---------------+
                             |
              +--------------+--------------+
              |              |              |
              v              v              v
        +--------+    +---------+    +--------+
        |  RGB   |    |  Buzzer |    |  Power |
        |  LEDs  |    |         |    |  Supply|
        +--------+    +---------+    +--------+
```

---

## Hardware

### Bill of Materials

| Component | Model | Function |
|-----------|-------|----------|
| **Microcontroller** | Aries V3.0 | Main processing unit |
| **Ultrasonic Sensor** | HC-SR04 | Proximity detection |
| **RFID Reader** | MFRC522 | Card authentication |
| **OLED Display** | SSD1306 (128×32) | Status display |
| **Servo Motors** | SG90/MG90S (x2) | Gate actuation |
| **LEDs** | RGB/RYG | Status indication |
| **Buzzer** | Active | Audio alerts |
| **Power Supply** | 5V | System power |

### Pin Mapping

| Signal | Pin | Protocol |
|--------|-----|----------|
| Ultrasonic TRIG | 3 | GPIO |
| Ultrasonic ECHO | 4 | GPIO |
| Servo – Gate 1 | 0 | PWM |
| Servo – Gate 2 | 1 | PWM |
| LED Red | 6 | GPIO |
| LED Yellow | 7 | GPIO |
| LED Green | 8 | GPIO |
| Buzzer | 9 | GPIO |
| MFRC522 SS (SDA) | 10 | SPI |
| MFRC522 RST | 2 | GPIO |
| SSD1306 | Wire(0) | I²C (0x3C) |

---

## How It Works

### Gate 1 – Proximity Control

1. Ultrasonic sensor measures distance continuously
2. If `distance ≤ 50 cm` and gate is closed → Servo opens to 90°, Green LED ON
3. If `distance > 50 cm` and gate is open → Servo closes to 0°, Red LED ON

### Gate 2 – RFID Authentication

1. RFID reader scans for card presence
2. If UID matches authorized card → Gate opens, "Access Granted" on OLED
3. If UID doesn't match → "Entry Restricted" on OLED, buzzer sounds for 1s

---

## Demonstrations

Video demonstrations of the system:

| Demo | Description |
|------|-------------|
| [Gate Demo 1](demos/videos/gate_demo_1.mp4) | Ultrasonic proximity gate operation |
| [Gate Demo 2](demos/videos/gate_demo_2.mp4) | Complete system demonstration |
| [RFID Demo](demos/videos/rfid_demo.mp4) | RFID authentication process |

---

## Software Setup

### Libraries Required

```cpp
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Servo.h>
#include <MFRC522.h>
```

Install via Arduino Library Manager before compiling.

### Configuration

```cpp
// Change this to your card's 4-byte UID
byte validUID[4] = {0x83, 0x64, 0x4B, 0x97};

// Distance threshold for Gate 1 (in cm)
#define THRESHOLD_DISTANCE 50
```

---

## Project Structure

```
Smart-Parking-Assistance-System/
├── README.md
├── src/
│   └── main.ino              # Arduino firmware
├── images/
│   └── workshop_setup.jpeg   # Development setup photo
└── demos/
    └── videos/
        ├── gate_demo_1.mp4   # Proximity gate demo
        ├── gate_demo_2.mp4   # Full system demo
        └── rfid_demo.mp4     # RFID authentication demo
```

---

## Author

**Debtonu Bose**
B.Tech Electronics and Communication Engineering
Vellore Institute of Technology (2021-2025)

[![GitHub](https://img.shields.io/badge/GitHub-DarkDragoXE-black?logo=github)](https://github.com/DarkDragoXE)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-debtonu--bose-blue?logo=linkedin)](https://linkedin.com/in/debtonu-bose)

---

## Acknowledgments

- **CDAC (Centre for Development of Advanced Computing)** for the workshop and training
- VIT Vellore for hosting the workshop

---

## Future Enhancements

- [ ] Add multiple authorized RFID cards
- [ ] Implement cloud-based access logging
- [ ] Add mobile app for remote monitoring
- [ ] Integrate camera for visual verification

---

<div align="center">

**IoT Access Control | CDAC Workshop | Arduino**

</div>
