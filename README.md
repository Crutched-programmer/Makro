# MAKRO-The simple drag and drop interface for building macropads

> Metro UI-styled macropad firmware builder with physically accurate grid sizing and HID key generation for Arduino Leonardo and compatible boards.
<img width=1800 height=1200 src='https://github.com/user-attachments/assets/c4981a9d-f14f-47df-98d2-d8f959a25669'/>

## The Problem It Solves

Building custom macropad firmware requires juggling Arduino IDE, C++ code, and HID reports. MAKRO streamlines this by providing a visual grid designer with Metro UI aesthetics that generates production-ready Arduino sketches. Define your keymap in a clean JSON config, get perfectly sized HTML preview, and auto-generate the firmware code—no manual C++ plumbing required.

## Features

* Visual macropad grid designer with Metro UI aesthetics
* Physically accurate button sizing (25mm default, configurable)
* Real-time HTML preview rendering
* Automatic Arduino Leonardo HID firmware code generation
* JSON-based configuration for easy version control
* Support for standard HID keycodes and macro sequences
* Customizable grid dimensions (1x1 up to 12x12)
* Export-ready firmware with proper USB descriptor handling
* Metro accent color picker for UI customization
* No external JavaScript frameworks—vanilla HTML/CSS/JS

## Tech Stack

* HTML5 / CSS3 (Metro UI design system)
* Vanilla JavaScript (no frameworks)
* Python 3.7+ (code generation backend)
* Arduino C++ (generated firmware)
* USB HID protocol (Leonardo native)

## Requirements

* Python 3.7+ (for code generator)
* Arduino IDE 1.8+ (for flashing firmware to board)
* Arduino Leonardo or compatible board (ATmega32U4)
* USB cable (micro or appropriate for your board)

## Install & Run

```bash
# Clone the repository
git clone https://github.com/yourusername/makro
cd makro

# Install Python dependencies (optional, only if using CLI generator)
pip install jinja2

# Start the web interface (Python simple HTTP server)
python -m http.server 8000

# Open in browser
# Navigate to http://localhost:8000/designer.html
```

## Usage

**Via Web Designer:**

1. Open `designer.html` in a modern browser
2. Set grid dimensions (rows × columns)
3. Click grid cells to assign keycodes
4. Select keycodes from the dropdown (standard HID set)
5. Preview renders in real-time with accurate button sizing
6. Click "Generate Firmware" to download `makro_firmware.ino`
7. Flash to Arduino Leonardo via Arduino IDE

**Example Grid Config (JSON):**

```json
{
  "name": "MyMacropad",
  "rows": 2,
  "cols": 3,
  "button_size_mm": 25,
  "button_spacing_mm": 2,
  "layout": [
    ["KEY_A", "KEY_B", "KEY_C"],
    ["KEY_D", "KEY_E", "KEY_F"]
  ]
}
```

**Via CLI (Python):**

```bash
# Generate firmware from config
python generate_firmware.py config.json output/makro_firmware.ino

# Generate HTML preview
python generate_preview.py config.json output/preview.html
```

## How It Works

### Code Generation Pipeline

1. **User Input (JSON Config)**
   - Grid dimensions, keycode assignments, button sizing
   - Stored in human-readable JSON format

2. **HTML Preview Generation**
   - Python script reads config
   - Calculates CSS grid layout with accurate mm-to-px conversion (1mm ≈ 3.78px at 96 DPI)
   - Generates Metro-styled HTML with button grid
   - CSS Grid layout ensures pixel-perfect button alignment

3. **Arduino Firmware Generation**
   - Python script templates Arduino sketch
   - Inserts HID keycode array from config
   - Adds USB descriptor for Leonardo board recognition
   - Configures interrupt pins (2-11 by default)
   - Embeds debounce logic and matrix scanning

### Firmware Architecture

The generated Arduino sketch uses:

```
Pin Layout:
- Pins 2-11: Button input (debounced)
- LED feedback pins: 12, 13 (configurable)
- USB: Built-in Leonardo HID via ATmega32U4

Scanning Logic:
1. Read all button pins every 20ms
2. Debounce logic: require 2 consecutive stable reads
3. Detect press-to-release transition
4. Send HID report via USB
5. Wait for release before accepting next press
```

### JSON to C++ Conversion

The generator:
- Reads JSON keymap array
- Converts HID keycode strings to byte constants (e.g., "KEY_A" → 0x04)
- Generates a 2D array in PROGMEM (flash memory)
- Creates `scanMatrix()` function to read grid and lookup codes
- Builds USB HID report with proper modifier key handling

Example generated array:
```cpp
// From config layout: [["KEY_A", "KEY_B"], ["KEY_C", "KEY_D"]]
const uint8_t keymap[2][2] PROGMEM = {
  {0x04, 0x05},  // A, B
  {0x06, 0x07}   // C, D
};
```

## Customization

**Change Button Size:**
- Edit `button_size_mm` in config JSON (default: 25mm)
- Preview updates automatically
- Firmware adapts spacing for visual feedback only

**Add Custom Keycodes:**
- Extend the keycode map in `keycodes.json`
- Include HID byte value and description
- Generator automatically includes in dropdown

**Modify Grid Dimensions:**
- Change `rows` and `cols` in config
- Generator creates appropriate number of pins/mappings
- Supports up to 12×12 grids (144 keys)

**Customize Metro Theme:**
- Edit CSS variables in `designer.html`:
  - `--accent-color`: Primary theme color
  - `--button-bg`, `--button-hover`: Button styling
  - `--text-color`, `--border-color`: Palette adjustments

**Add Macro Support:**
- Modify firmware template to support key sequences
- Extend config format: `"macro": ["KEY_L", "KEY_A", "KEY_B"]`
- Generator creates macro execution function with timing

## Limitations

* Single-layer layout only (no layer switching in base version)
* No mouse or analog input support
* Requires Arduino IDE for flashing (no drag-and-drop)
* HTML preview is visual only (doesn't test actual key registration)
* Limited to boards with ATmega32U4 or similar HID-capable chips
* No wireless support (USB-only in base version)

## Future Improvements

* Layer support with layer-switch keys
* Macro recording and playback (hold, sequence, repeat)
* Mouse movement and scroll wheel support
* OLED display integration for status/labels
* Wireless BLE variant using nRF52 boards
* Live USB testing in designer (requires browser USB API)
* Dark mode toggle in designer UI
* Import/export standard QMK configs
* 3D printable case generator with button cavity calculations

## Notes

* Generated firmware is ~2KB (fits easily in Leonardo's 32KB flash)
* Button debounce delay: 20ms (adjustable in firmware template)
* USB polling rate: 1000Hz (standard for HID keyboards)
* First flash requires manual Arduino IDE upload; subsequent updates via custom bootloader possible
* Keep config JSON version-controlled separately from firmware
* Test all keycodes before final flash (typos can cause unintended key sends)
* Metro UI chosen for modern, flat aesthetic and pixel-perfect grid alignment

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────┐
│                   MAKRO Designer                        │
│  (HTML/CSS/JS - Metro UI + Visual Grid Editor)          │
└────────────────┬────────────────────────────────────────┘
                 │
         ┌───────┴───────┐
         │               │
    Real-time HTML   Generate Config (JSON)
    Preview in Grid       │
         │                │
    CSS Grid Layout   Python Generator
    + Button Spacing      │
         │                │
         │          ┌─────┴──────┐
         │          │            │
         │       Firmware Code    HTML Export
         │       (Arduino C++)    (Standalone)
         │          │            │
         │      HID Reports    Visual Preview
         │      Matrix Scan    For Documentation
         │      Pin Config
         │
    ┌────┴─────────────┐
    │ Arduino Leonardo │
    │ (ATmega32U4)     │
    │ - USB HID        │
    │ - Button Matrix  │
    │ - LED Feedback   │
    └──────────────────┘
```
<p>Please note that this project is done with inspiration from app.musiconerd.com . This is is a drag and drop interface for building a MIDI controller. I took up this project as a simple challenge. I have no intentions to replicate or reproduce the interface.</p>
