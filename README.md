# 🌿 Smart Plant Mood Monitor

> An Arduino-based IoT project that gives your plant a voice — monitoring soil moisture and ambient light to display one of four expressive mood states on an OLED screen.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Components Required](#components-required)
- [Circuit Connections](#circuit-connections)
- [Mood Decision Logic](#mood-decision-logic)
- [Arduino Source Code](#arduino-source-code)
- [Simulation in Cirkit Designer](#simulation-in-cirkit-designer)
- [Learning Outcomes](#learning-outcomes)

---

## Overview

The **Smart Plant Mood Monitor** reads soil moisture and ambient light levels in real time using two analog sensors connected to an Arduino UNO. Based on the combined sensor values, the system maps to one of four personality-driven mood states and displays it on an OLED screen — updating every 2 seconds.

| Property | Details |
|---|---|
| **Software** | Cirkit Designer |
| **Microcontroller** | Arduino UNO (ATmega328P) |
| **Language** | C++ (Arduino Framework) |
| **Sensors** | SparkFun Soil Moisture Sensor + Photoresistor (LDR) Module |
| **Display** | OLED 128×64 I2C Monochrome (SSD1306, address `0x3C`) |
| **Libraries** | `Wire.h`, `Adafruit_GFX.h`, `Adafruit_SSD1306.h` |
| **Mood States** | Cold & Thirsty / Water Me Please / Sleepy / I Feel Great |
| **Update Interval** | Every 2 seconds |

---

## Components Required

| # | Component | Specification | Pin / Address | Qty |
|---|---|---|---|---|
| 1 | Arduino UNO | ATmega328P, 5V, 14 digital + 6 analog I/O | Central MCU | 1 |
| 2 | SparkFun Soil Moisture Sensor | Analog output, measures soil conductivity | A0 | 1 |
| 3 | Photoresistor (LDR) Module | Analog output, measures ambient light | A1 | 1 |
| 4 | OLED 128×64 I2C Display | SSD1306 controller, monochrome, 3.3–5V | SDA/SCL (I2C) | 1 |
| 5 | Resistors (200 Ω) | Current limiting for sensor signal lines | — | 2 |
| 6 | Breadboard | Full-size, for component interconnection | — | 1 |
| 7 | Jumper Wires | Male-to-Male for all connections | — | Many |
| 8 | USB Cable | Type-A to Type-B, powers Arduino via PC | — | 1 |

---

## Circuit Connections

```
Soil Moisture Sensor
  VCC  →  5V rail (breadboard)
  GND  →  GND rail (breadboard)
  SIG  →  Arduino A0  (via 200Ω resistor)

LDR Module
  VCC  →  5V rail
  GND  →  GND rail
  AO   →  Arduino A1  (via 200Ω resistor)

OLED 128×64 I2C Display
  VDD  →  3.3V or 5V rail
  GND  →  GND rail
  SDA  →  Arduino A4  (Hardware I2C SDA)
  SCL  →  Arduino A5  (Hardware I2C SCL)
```

---

## Mood Decision Logic

The Arduino evaluates both sensor readings simultaneously and maps them to one of four moods:

| Soil Moisture | Light Level | OLED Message |
|---|---|---|
| `< 300` (Dry) | `< 300` (Dark) | 😰 **Cold & Thirsty!** |
| `< 300` (Dry) | `≥ 300` (Bright) | 💧 **Water Me Please!** |
| `≥ 300` (Moist) | `< 300` (Dark) | 😴 **Sleepy zzz...** |
| `≥ 300` (Moist) | `≥ 300` (Bright) | 😄 **I Feel Great!** |

> Sensor values are raw 10-bit ADC readings (range: 0–1023). The threshold of **300** distinguishes dry/dark from moist/bright conditions.

---

### Code Breakdown

**Libraries & Display Object**
- `Wire.h` — enables I2C communication between Arduino and OLED.
- `Adafruit_GFX.h` — provides graphics primitives (text, lines, shapes).
- `Adafruit_SSD1306.h` — driver for the SSD1306 OLED controller.
- Display object initialised with 128×64 resolution and I2C address `0x3C`.

**`setup()`**
- Opens the serial port at 9600 baud for optional debug monitoring.
- Initialises and clears the OLED before the main loop begins.

**`setMood(line1, line2)`**
- Clears the display, draws the "Plant Mood" title and a horizontal separator.
- Renders `line1` at text size 2 (large) and `line2` at text size 1 (small).
- Calls `display.display()` to push the buffer to the screen.

**`loop()`**
- `analogRead(A0)` — reads raw soil moisture (0–1023); values below 300 = dry.
- `analogRead(A1)` — reads ambient light via LDR; values below 300 = dark.
- A two-variable `if/else` chain selects the correct mood and calls `setMood()`.
- `delay(2000)` pauses 2 seconds before the next reading cycle.

---

## Simulation in Cirkit Designer

You can simulate this project entirely in [Cirkit Designer](https://www.cirkitdesigner.com) before building physical hardware.

1. Open Cirkit Designer and create a new project.
2. Add an **Arduino UNO** from the component library.
3. Add a **SparkFun Soil Moisture Sensor** → connect VCC→5V, GND→GND, SIG→A0 via a 200Ω resistor.
4. Add a **Photoresistor (LDR) Module** → connect VCC→5V, GND→GND, AO→A1 via a 200Ω resistor.
5. Add an **OLED 128×64 I2C Display** → connect VDD→5V, GND→GND, SDA→A4, SCL→A5.
6. Open the Code Editor, select Arduino, and paste the sketch above.
7. Add `Adafruit_GFX` and `Adafruit_SSD1306` in the Libraries panel.
8. Click **Simulate** — adjust the soil and light sensor sliders to cycle through all four mood states.
9. Observe the OLED message update every 2 seconds.

---

## Learning Outcomes

- Reading multiple analog sensors on separate Arduino analog pins.
- I2C communication protocol for interfacing the SSD1306 OLED display.
- Multi-variable conditional logic (`if/else`) for sensor-driven decision making.
- Modular function design (`setMood`) to keep `loop()` clean and readable.
- Hands-on simulation with Cirkit Designer including library management.

---

## Libraries Installation

Install the required libraries via the Arduino IDE Library Manager:

```
Sketch → Include Library → Manage Libraries...
```

Search for and install:
- **Adafruit GFX Library** by Adafruit
- **Adafruit SSD1306** by Adafruit

---

*Simulated in Cirkit Designer | Arduino UNO | June 2026*
