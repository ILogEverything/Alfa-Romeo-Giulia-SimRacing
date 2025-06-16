# Alfa Giulia/Stelvio Dashboard Interface

A real-time telemetry interface designed specifically for **Alfa Romeo Giulia / Stelvio dashboards**, using two **MCP2515 CAN modules**.  
It receives live telemetry from **BeamNG.drive** or **Assetto Corsa** and drives a real cluster via **CAN bus**, emulating OEM behavior.

---

## 🚗 Features

- ✅ Designed for **Alfa Romeo Giulia/Stelvio** dashboards
- ✅ Compatible with **Arduino** (e.g., Nano) and **STM32** (e.g., F401CCU6 / Black Pill)
- ✅ Dual **MCP2515** support (500 kbps CAN + 125 kbps IHS)
- ✅ Real-time **serial communication** from PC
- ✅ Supports **BeamNG.drive** and **Assetto Corsa**

☕️ Like this project? Support it with a coffee! Your help keeps dashboards alive and wheels spinning. -> https://paypal.me/xb6783746

---

## 🎮 Supported Simulators

| Simulator        | Protocol          |
|------------------|-------------------|
| BeamNG.drive     | OutGauge via UDP  |
| Assetto Corsa    | Shared Memory     |

---

## 🧱 Architecture Overview

| Component         | Role                                      |
|------------------|-------------------------------------------|
| `Python script`   | Reads telemetry and sends serial data     |
| `Arduino/STM32`   | Parses serial and sends CAN messages      |
| `MCP2515 #1`      | CAN bus @ 500 kbps (standard)             |
| `MCP2515 #2`      | CAN bus @ 125 kbps (IHS, optional)        |

---

## 🔌 Serial Protocol (PC → MCU)

ASCII lines in format `KEY:VALUE`, one per line.

**Example:**
```
RPM:3100
VEL:88
TEMP:90
CARB:64
MARCIA:3
MEDIA_STRING:Best Lap: 1:45.872
```

---

## 🔧 Arduino Requirements

- Arduino IDE.
- Board: Arduino Uno, Nano, Micro.
- Library: [mcp_can by coryjfowler] (https://github.com/coryjfowler/MCP_CAN_lib)

Install with:

1. Open **Arduino IDE**
2. Go to **Sketch → Include Library → Manage Libraries...**
3. Search for **"MCP_CAN by coryjfowler"**
4. Click **Install**

Alternatively, clone manually:

```bash
git clone https://github.com/coryjfowler/MCP_CAN_lib.git
```

Then copy the folder into your `Arduino/libraries/` directory.

---

## 🐍 Python Requirements

- Python 3.10+
- `pyserial`
- `socket` (builtin)

Install with:

```bash
pip install pyserial
```

---

### 🔧 `Config.h` — User Configuration Guide

This is the **only file you need to edit** to adapt the firmware to your setup.  
It allows you to configure platform, CAN bus settings, optional features, and pin mapping.

---

#### 📦 Platform Selection
```c
#define STM32
```
Uncomment if you are using an STM32 (e.g., STM32F401CCU6 “Black Pill”).  
Leave it commented to compile for standard **Arduino** boards (e.g., Nano, Uno).

---

#### 🧪 Optional CAN Modules
```c
#define CAN2_OPTIONAL
```
Enable if you're using a **second MCP2515** module (e.g., for a dual-bus setup with CAN1 500kbps and CAN2 125kbps).  
Comment to use only **CAN1**.

---

#### 🛠️ Optional Display Flags
```c
// #define MY23   // Enable shift light handling for MY2023 dashboards
// #define SHIFT  // Enable shift light for TFT 7" dashboard only
```
- `MY23`: activates special handling for Giulia/Stelvio MY2023 cluster shift lights.
- `SHIFT`: enables shift light display for 7" TFT dashboards, don't use on 3.5".

Leave them commented unless you need these features.

---

#### 🚗 CAN Bus Configuration
```c
#define MCP1_CLOCK MCP_8MHZ
#define CAN1_SPEED CAN_500KBPS

#define MCP2_CLOCK MCP_8MHZ
#define CAN2_SPEED CAN_125KBPS
```
- Clock frequency of each MCP2515 (typically **8MHz** or **16MHz**).
- Bitrate of each CAN bus (default for Giulia/Stelvio: **500kbps for CAN1**, **125kbps for CAN2**).

---

#### ⏱️ Send Interval
```c
#define SEND_INTERVAL 0
```
Controls the delay between message transmissions (in milliseconds).  
Set to `0` for maximum throughput (recommended). Increase only for debugging or bandwidth issues.

---

#### 🔌 STM32 Pinout (Black Pill / STM32F401CCU6)
```c
// SPI
#define SCK_PIN   PA5
#define MISO_PIN  PA6
#define MOSI_PIN  PA7

// CAN1
#define CAN1_CS_PIN  PA4
#define CAN1_INT_PIN PA10

// CAN2 (optional)
#define CAN2_CS_PIN  PB12
#define CAN2_INT_PIN PB13
```
Pins used for SPI and CAN module interrupts. Compatible with standard MCP2515 modules.

---

#### 🔌 Arduino Pinout (e.g., Nano, Uno)
```c
// CAN1
#define CAN1_CS_PIN 10
#define CAN1_INT_PIN 2

// CAN2 (optional)
#define CAN2_CS_PIN 9
#define CAN2_INT_PIN 3
```
Standard Arduino pin mapping for SPI and interrupts. Ensure the pins match your wiring.

---

## 🖥️ File Structure

```
📁 /AlfaRomeoGiuliaSimRacing/         --> Arduino / STM32 code
📁 /Windows [Python]/                 --> Games Support / Windows
📄 README.md
```

---

## 🛠️ Hardware Requirements

- ✅ STM32F401CCU6 or Arduino
- ✅ 2x MCP2515 modules
- ✅ Real Giulia/Stelvio instrument cluster

---

## 🧪 Tested With

- BeamNG v0.31+ with OutGauge enabled on port 4444
- Assetto Corsa (Steam)
- OEM Giulia cluster (2017)
- STM32F401CCU6 Black Pill
- Arduino Nano
- Dual MCP2515

---

| Feature             | Can Bus                                                  |
|---------------------|----------------------------------------------------------|
| **Rpm**             | CAN1                                                     |
| **Speed**           | CAN1                                                     |
| **Oil Temperature** | CAN1                                                     |
| **Fuel Level**      | CAN1                                                     |
| **ABS Light**       | CAN1 – Only `BeamNg`                                     |
| **ESC Light**       | CAN1 – Only `BeamNg`                                     |
| **Handbrake Light** | CAN1 – Only `BeamNg`                                     |
| **DNA**             | CAN1 – Idle in `Normal` due to games support             |
| **SHIFT LIGHT**     | CAN1 - Read DNA problem, working on a workaround         |
| **Current Gear**    | CAN1                                                     |
| **External Temp**   | CAN2 - Realistic in `Assetto Corsa`, dummy in `BeamNg`   |
| **MEDIA_STRING**    | CAN2 - Best Lap in `Assetto Corsa`, dummy in `BeamNg`    |

![photo_2025-06-16_22-49-17](https://github.com/user-attachments/assets/797eec8e-579b-4dec-9faa-3c7e21724716)

---

## ⚠️ Notes

- In BeamNG, enable OutGauge
- Assetto Corsa doesn't need plugins: uses shared memory

- !On spawn/respawn in BeamNg you have a fake fuel alert!
- !On first startup with Assetto Corsa, you must enter the track before launching the script!

---

## 📜 License

MIT License — Use at your own risk.
Not affiliated with Alfa Romeo, FCA, or Stellantis.
