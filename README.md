# Makro - The drag and drop macropad code generator

A high-level, visual firmware generator for custom HID peripherals. Design your hardware, define your intent, and get ready-to-flash code without worrying about pin-mapping or HID descriptors.

---

<h3>Change log:<br>
- Added drag-and-drop grid system for physical component placement<br>
- Integrated real-time GPIO validation to prevent pin conflicts<br>
- Added "Intent-based" configuration to describe logic in plain English<br>
- Support for multiple firmware backends (QMK, KMK, Arduino)<br>
- Built-in SVG preview for all components including OLEDs and Encoders<br>
</h3>

---

## The Problem Solved

Creating a custom macropad is often more difficult than the physical assembly suggests. **makro** addresses the following hurdles:

| Challenge | How makro Fixes It |
| :--- | :--- |
| **GPIO Management** | Automatically tracks used pins and ensures your MCU has enough physical bandwidth for the layout. |
| **Hardware Logic** | Handles complex I2C addresses for OLEDs and rotational logic for encoders out of the box. |
| **Power & Compliance** | Generates HID-compliant descriptors so your device is recognized by modern OSs instantly. |
| **Code Friction** | Removes the need to manually write matrix scan logic or complex C++/Python boilerplate. |

---

## Tech Stack

| Component | Details |
| :--- | :--- |
| **Core Engine** | Vanilla ES6+ JavaScript — No heavy framework overhead |
| **Design System** | Custom CSS Grid / Flexbox with Syne & DM Mono typography |
| **Icons & Assets** | Optimized Inline SVGs for zero-latency component rendering |
| **Runtime** | Web-standard Browser Environment (works offline once loaded) |
| **Integrations** | js.puter.com for cloud-native workspace features |

---

## Workflow Phases

| Phase | Description | Key Actions |
| :--- | :--- | :--- |
| **1. Structure** | Hardware Layout | Drag keys, encoders, and displays onto the grid. Select your MCU. |
| **2. Working** | Logic & Intent | Set labels and define what happens on press, turn, or trigger. |
| **3. Code** | Generation | Pick your platform (QMK, KMK, etc.) and copy the resulting source. |

---

## Supported Hardware

| Input Type | Output Type | MCU Compatibility |
| :--- | :--- | :--- |
| Mechanical Switches | OLED Displays (SSD1306) | Pro Micro (ATmega32U4) |
| Rotary Encoders | NeoPixel / RGB Strips | RP2040 (Pi Pico) |
| Potentiometers | Piezo Buzzers | Teensy 2.0 / 3.2 |
| Toggle Switches | Status LEDs | Blue Pill (STM32F103) |

---

## Installation & Setup

No installation is required. This tool runs entirely in the browser.

```bash
# Clone the repository
git clone [https://github.com/your-username/makro.git](https://github.com/your-username/makro.git)

# Open the tool
cd makro
open Makro.html
