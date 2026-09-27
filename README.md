# Gluttonous-Snake on MSPM0G3519

English | [中文](./README.zh-CN.md)


A classic Snake game implemented on the Texas Instruments **MSPM0G3519** microcontroller, featuring an OLED display, a 4×4 matrix keypad for local control, and **UART remote control** via VOFA+ for simultaneous operation, developed as a training project for university electronics competition.

---

## 🎮 Features

- **Full Snake Game Logic**  
  Snake movement, food consumption, growing body, boundary collision, and scoring.
- **OLED Graphics**  
  Real-time display of the snake, food, game field, and UI screens using OLED.
- **Dual Control Interface**  
  - **Matrix keypad** for local control: direction keys, pause/resume, quit.  
  - **UART command interface** (VOFA+) for remote control: start, pause, direction commands.
- **Food Mechanics**  
  Food appears at random positions. If not eaten within a configurable timeout, it respawns to prevent stalemate.
- **Scoring & Penalty**  
  Score increases with each food eaten. Hitting the field boundary deducts points (configurable).
- **Pause / Resume**  
  Game can be paused and resumed via keypad or serial command.
- **Startup & Menu Screens**  
  Boot screen and a simple choice screen to start the game.
- **Hardware Debouncing**  
  Keypad inputs are debounced in software to ensure reliable detection.

---

## 🛠 Hardware

| Component | Details |
|-----------|---------|
| **MCU** | Texas Instruments MSPM0G3519 |
| **Display** | 128×64 OLED |
| **Input** | 4×4 matrix keypad (8 I/O pins) |
| **Remote Control** | UART for communication with VOFA+ or any serial tool |

---

## 💻 Software Architecture

The firmware is written in C using **TI DriverLib** and SysConfig.

### Main Components

| Module | Description |
|--------|-------------|
| `main.c` | Initialisation, main loop, keyboard + UART command handling, screen state machine. |
| `Keyboard.c/h` | Matrix keypad scanning with software debounce. Returns ASCII characters for pressed keys. |
| `oled_spi_V0.2` | Low-level OLED driver: SPI communication, drawing pixels, fonts, and screen refresh. |
| `function.c/h` | Core game logic: snake movement, collision detection, scoring, food generation, timing. |
| `interface.c/h` | UI screens (boot, pause, game over) and menu drawing. |

### UART Remote Control

- **Baud rate**: 115200 (configurable)
- **Data format**: 8N1
- **Protocol**: Simple single-byte commands (ASCII)
- **VOFA+ setup**: Create buttons and set **Send Content** to the corresponding ASCII command.

| Command | Key Press Equivalent | Action |
|---------|----------------------|--------|
| `A` | `A` (keypad) | Start game |
| `#` | `#` (keypad) | Pause/Resume |
| `*` | `*` (keypad) | Quit game |
| `2` | `2` (keypad) | Move up |
| `8` | `8` (keypad) | Move down |
| `4` | `4` (keypad) | Move left |
| `6` | `6` (keypad) | Move right |

The UART receive interrupt stores the byte in a `volatile` variable. The main loop checks this variable alongside keypad input, ensuring both control sources work simultaneously.

---

## 🕹 How to Use

### Local Control (Matrix Keypad)

- **Start game**: Press `A`
- **Direction**: `2` = up, `8` = down, `4` = left, `6` = right
- **Pause / Resume**: `#`
- **Quit game**: `*`

### Remote Control (VOFA+)

1. Connect the USB-TTL adapter to your PC and board.
2. Open **VOFA+**, select the correct COM port, and set baud rate to 115200.
3. Create buttons with **Send Content** as ASCII command characters.
4. Click buttons to send commands.

---

## 📁 File Structure

```
📁 Project Root
│
├── Application/                     # Application layer: game logic and UI
│   ├── function.c                   # Core snake game logic
│   ├── function.h                   # Core game function declarations
│   ├── interface.c                  # UI drawing (boot/pause/game-over)
│   └── interface.h                  # UI function declarations
│
├── BSP/                             # Board Support Package
│   ├── bsp.h                        # Global declarations
│   ├── Keyboard.c                   # Matrix keypad driver and scanning
│   ├── Keyboard.h                   # Keypad driver declarations
│   ├── oled_spi_V0.2.c              # OLED low-level SPI driver
│   ├── oled_spi_V0.2.h              # OLED driver declarations
│   ├── oledfont.h                   # OLED font data
│   └── oledpicture_V0.2.h           # OLED picture data
│
├── Project/                         # IDE project files
├── Source/
│   ├── ti/                          # TI SDK
│   └── third_party/                 # Third-party libraries
│
├── User/
│   ├── config.syscfg               # SysConfig file
│   ├── Event.dot
│   ├── main.c                      # Main entry and command handling
│   ├── ti_msp_dl_config.c          # Generated driver config source
│   └── ti_msp_dl_config.h          # Generated driver config header
│
└── README.md
```

## Acknowledgments

- Texas Instruments for MSPM0 SDK and SysConfig.
- Keil for MDK-ARM development environment.
- This project was developed as a training exercise for university electronics competition.
