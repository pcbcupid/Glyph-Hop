# Glyph-Hop 

> An ultra-fast, flicker-free arcade obstacle-dodging game crafted for the upcoming **S3 Lucid** (ESP32-S3) development board by [PCB Cupid](https://github.com/pcbcupid).

![Platform](https://img.shields.io/badge/Platform-ESP32--S3-blue?style=flat-square&logo=espressif)
![Display](https://img.shields.io/badge/Display-ST7735%20160x80-green?style=flat-square)
![Frame Rate](https://img.shields.io/badge/FPS-50%20Locked-orange?style=flat-square)
![Framework](https://img.shields.io/badge/Framework-Arduino%20%2F%20C%2B%2B-red?style=flat-square&logo=arduino)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)
![Maintained](https://img.shields.io/badge/Maintained%20by-PCB%20Cupid-purple?style=flat-square)

---

##  Overview

**Glyph-Hop** is a high-performance arcade side-scroller developed specifically for the next-generation **S3 Lucid** development board. Leveraging the dual-core ESP32-S3 microcontroller and an integrated 160×80 Mini-TFT display, Glyph-Hop brings responsive retro gameplay with custom physics, procedural pipe generation, and zero-flicker delta slice rendering.

### 🎮 Live Gameplay Preview

<p align="center">
  <img src="assets/Game-preview.gif" alt="Glyph-Hop Live Gameplay Preview" width="600"/>
  <br>
  <sub>🎥 Video source: <a href="https://files.pcbcupid.com/Glyph-Hop/Game-preview.mp4">Game-preview.mp4</a></sub>
</p>

---

##  Media & Hardware Showcase

### S3 Lucid Hardware
The upcoming **S3 Lucid** board combines the processing power of the ESP32-S3 with a built-in 160×80 Mini-TFT display and a three-button tactile control layout.

<p align="center">
  <img src="https://files.pcbcupid.com/Glyph-Hop/Glyph%20S3%20front%20view.avif" alt="Glyph S3 Front View" width="640"/>
  <br>
  <em>Figure 1: S3 Lucid Front View</em>
</p>

### Game Interface & Screens

| Game Start Interface | Game Over & High Score Card |
| :---: | :---: |
| <img src="https://files.pcbcupid.com/Glyph-Hop/Glyph-game%20interface.avif" alt="Glyph-Hop Game Interface" width="400"/> | <img src="https://files.pcbcupid.com/Glyph-Hop/Game%20over%20!.avif" alt="Game Over Screen" width="400"/> |
| *Title Screen with dual-tone logo, player preview, and prompt* | *Game Over summary with current score, high score, and "NEW!" badge* |

---

##  Key Highlights

- **Silky 50 FPS Physics Engine:** Hard-locked 20ms frame cycle with smooth gravity, flap impulsing, and sub-pixel bounding-box collision detection.
- **Zero-Flicker Delta Slice Rendering:** Erases only trailing pixel columns and paints new leading edge slices across SPI instead of clearing entire frames or drawing solid rectangles.
- **Isolated HUD Architecture:** The top HUD bar (Score & High Score) remains static while score digits are selectively refreshed in-place only when an obstacle is cleared.
- **Dynamic Arcade Graphics:**
  - 8×8 custom player sprite with animated wings and beak.
  - Multi-toned shaded pipes with gold end-caps and lip overhangs.
  - Alternating two-tone scrolling ground terrain.
  - Static background starfield backdrop.
- **Built-in Power-On Self-Test (POST):** Executes automated firmware unit tests for physics and collision detection over the serial monitor at boot.
- **Full State Machine:** `START` ➔ `PLAYING` ⇄ `PAUSED` ➔ `GAMEOVER`.

---

##  Controls & Navigation

The **S3 Lucid** board features three dedicated tactile pushbuttons wired with internal pull-ups (`INPUT_PULLUP`, active `LOW`):

| Button | Label / Color | GPIO Pin | Function in Game |
| :--- | :--- | :---: | :--- |
| **SW5** | **Center (Blue)** | `GPIO 38` | **Start Game** / **Flap (Jump)** |
| **SW6** | **Top (Yellow)** | `GPIO 40` | **Pause** / **Resume Game** |
| **SW4** | **Bottom (Red)** | `GPIO 39` | **Restart** (after Game Over) |

---

##  Hardware Pinout & Architecture

### ST7735 Mini 160×80 SPI Display

| Signal Name | ESP32-S3 GPIO | Description |
| :--- | :---: | :--- |
| **TFT_CS** | `21` | SPI Chip Select |
| **TFT_DC** | `12` | Data / Command Control |
| **TFT_RST** | `42` | Hardware Reset |
| **TFT_MOSI** | `36` | Master Out Slave In (Data) |
| **TFT_SCLK** | `35` | SPI Serial Clock |
| **TFT_BLK** | `41` | Display Backlight (PWM / AnalogWrite) |

### Tactile Switches

| Switch | GPIO Pin | Circuit Configuration | Active Level |
| :--- | :---: | :--- | :---: |
| **SW4 (Red - Bottom)** | `39` | Internal Pull-Up (`INPUT_PULLUP`) | `LOW` |
| **SW5 (Blue - Center)** | `38` | Internal Pull-Up (`INPUT_PULLUP`) | `LOW` |
| **SW6 (Yellow - Top)** | `40` | Internal Pull-Up (`INPUT_PULLUP`) | `LOW` |

---

##  Game Physics & Tuning Constants

All physical constants and gameplay parameters are decoupled in `Glyph-Hop.ino`:

| Constant | Value | Description |
| :--- | :---: | :--- |
| `FRAME_DELAY_MS` | `20` | Fixed update interval (50 FPS) |
| `GRAVITY` | `0.38` | Downward velocity added per frame |
| `FLAP_STRENGTH` | `-3.8` | Instantaneous upward velocity impulse |
| `PIPE_SPEED` | `2` | Pixels moved horizontally per frame |
| `PIPE_WIDTH` | `16` | Width of the vertical pipe obstacles |
| `PIPE_GAP_HEIGHT` | `28` | Vertical passage clearance between pipe caps |
| `BIRD_SIZE` | `8` | Bounding box dimensions (8×8 px + 2px beak) |
| `HUD_HEIGHT` | `14` | Height reserved for the top score header |
| `GROUND_Y` | `75` | Vertical position of the animated ground strip |

---

##  Rich Arcade Color Palette (RGB565)

| Color Token | RGB565 Hex | Visual Role |
| :--- | :---: | :--- |
| `COLOR_BG` | `0x0821` | Deep space navy background |
| `COLOR_HUD_BG` | `0x10A3` | Sleek dark slate HUD bar |
| `COLOR_HUD_BORDER` | `0x04D3` | Crisp teal boundary divider |
| `COLOR_LIGHT_CYAN` | `0x87FF` | Pipe highlights & label text |
| `COLOR_DARK_CYAN` | `0x03EF` | Pipe shadows & badge outlines |
| `COLOR_GOLD` | `0xFEA0` | Pipe caps & "NEW!" high score badge |
| `COLOR_ORANGE` | `0xFD20` | Player body & title text |
| `COLOR_STAR` | `0x52AA` | Subtle background starfield pixels |

---

##  Getting Started

### 1. Prerequisites & Dependencies

Make sure you have the **Arduino IDE** (2.x recommended) or **PlatformIO** installed with the ESP32 board support package.

Install the following libraries via the Arduino Library Manager (`Ctrl + Shift + I`):
1. **Adafruit GFX Library** (`>= 1.11.x`)
2. **Adafruit ST7735 and ST7789 Library** (`>= 1.10.x`)
3. **SPI** (Included with ESP32 Arduino Core)

### 2. Board Configuration (Arduino IDE)

Configure your target settings under **Tools**:
- **Board:** `ESP32S3 Dev Module` *(or the dedicated Lucid S3 board profile once officially published)*
- **USB CDC On Boot:** `Enabled` (for Serial debug output)
- **CPU Frequency:** `240MHz (WiFi)`
- **Flash Mode:** `QIO 80MHz`
- **Flash Size:** `4MB` (or larger)
- **Partition Scheme:** `Default 4MB with SPIFFS` (or `Huge APP`)
- **Upload Speed:** `921600`

### 3. Flashing the Firmware

1. Clone this repository:
   ```bash
   git clone https://github.com/pcbcupid/Glyph-Hop.git
   ```
2. Open `Glyph-Hop/Glyph-Hop.ino` in Arduino IDE.
3. Connect your **S3 Lucid** board to your computer via USB-C.
4. Select the detected COM port under **Tools > Port**.
5. Click **Upload** (`Ctrl + U`).
6. Open the Serial Monitor at `115200 baud` to view the boot-up self-test diagnostics!

---

## 🔬 Technical Deep-Dive: Delta Rendering

SPI TFT displays on microcontrollers often suffer from visible screen tearing or flicker when clearing frames (`tft.fillScreen(...)`) or updating large sprite bounding boxes.

Glyph-Hop solves this using single-pixel column slice rendering:
```cpp
// Erase only trailing edge as pipe moves left by PIPE_SPEED
for (int sx = newX + PIPE_WIDTH + 1; sx <= oldX + PIPE_WIDTH; sx++) {
  clearPlayfieldSlice(sx);
}
// Paint only newly revealed leading edge slices
for (int sx = newX - 1; sx <= oldX - 1; sx++) {
  drawPipeSlice(sx, sx - newX, gapY);
}
```
This reduces SPI transaction traffic per frame by over **85%**, maintaining a rock-solid 50 FPS without requiring an external double-buffer framebuffer in PSRAM.

---

##  Repository Structure

```text
Glyph-Hop/
├── assets/
│   ├── Game-preview.gif   # Autoplaying looping gameplay preview
│   └── Game-preview.mp4   # 2-second in-game preview video
├── Glyph-Hop.ino          # Main game sketch, physics loop, display driver, and POST
└── README.md              # Project documentation, pinout guide, and visual showcase
```

---

---

##  License

This project is open-source and released under the [MIT License](LICENSE). Feel free to modify, extend, and share!
