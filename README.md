# Alfa Giulia / Stelvio Dashboard Interface

A real-time telemetry interface for **Alfa Romeo Giulia / Stelvio** dashboards, using two **MCP2515 CAN modules**.  
It reads live data from SimRacing games and controls a real Alfa Romeo Giulia/Stelvio OEM cluster via **CAN bus**, closely emulating factory behavior.

---

## 🚗 Features

- ✅ Full support for **Alfa Romeo Giulia / Stelvio** instrument clusters  
- ✅ Compatible with both **Arduino** and **STM32** platforms  
- ✅ Dual **CAN Bus** communication:
  - 500 kbps (Powertrain CAN)
  - 125 kbps (Infotainment / IHS CAN)

☕️ Like the project? [Buy me a coffee](https://paypal.me/xb6783746)!  
Your support helps keep dashboards alive and wheels spinning.

---

## 🎮 Supported Simulators

| Simulator        | Protocol      |
|------------------|---------------|
| BeamNG.drive     | OutGauge      |
| Assetto Corsa    | Shared Memory |

---

## 🧱 Architecture Overview

| Component       | Description                           |
|----------------|---------------------------------------|
| Python Script   | Reads game telemetry, sends via serial |
| Arduino/STM32   | Parses serial data, transmits over CAN |
| MCP2515 #1      | CAN @ 500 kbps (required)              |
| MCP2515 #2      | CAN @ 125 kbps (optional)              |

---

## 🔌 Serial Protocol (PC → MCU)

ASCII lines in format KEY:VALUE, one per line.

### Example:
```
RPM:3100
VEL:88
TMPOLIO:90
CARB:64
MARCIA:3
MEDIA:Best Lap: 1:45.872
```

---

## 📁 File Structure

```
📁 /AlfaRomeoGiuliaSimRacing/         --> Arduino / STM32 code
📁 /Windows [Python]/                 --> Games Support / Windows
📄 README.md                          --> This file
```
> 🗂️ From v1.1 files are distributed individually and not as ZIPs for better readability, diffing, and version tracking on GitHub.
---

## 🔧 Software Requirements (Board Side)

- **Arduino IDE**
- Supported Boards: **Arduino Uno, Nano, Micro**
- MCP2515 Library: [`MCP_CAN` by coryjfowler](https://github.com/coryjfowler/MCP_CAN_lib)

### Installation Steps

1. Open Arduino IDE  
2. Go to **Sketch → Include Library → Manage Libraries...**  
3. Search for `"MCP_CAN by coryjfowler"`  
4. Click **Install**

---

## 📦 STM32 Arduino Support (Only for STM32 Boards)

1. Open Arduino IDE  
2. Go to **File > Preferences**  
3. Add this URL to **Additional Board Manager URLs**:

```
https://github.com/stm32duino/BoardManagerFiles/raw/main/package_stmicroelectronics_index.json
```

4. Go to **Tools > Board > Boards Manager**  
5. Search for `STM32` and install:  
   - **STM32 MCU based boards by STMicroelectronics**

6. Set the following:

| Setting                 | Value                                     |
|-------------------------|-------------------------------------------|
| Board                   | Generic STM32F4 series*                   |
| Board part number       | Generic F401CCUx*                         |
| Upload method           | STM32CubeProgrammer (DFU)                 |
| Optimize                | Smallest (-Os default)                    |
| C Runtime Library       | Newlib Nano + Float Printf                |
| USB Support             | CDC (generic "Serial" supersede U(S)ART)  |
| U(S)ART Support         | Disabled (no Serial support)              |
| USB Speed               | Low/Full Speed                            |

`*STM32F401CCU6 Black Pill, adjust according to your board`

---

## 🔧 `Config.h` — Firmware Configuration

This is the **only file you need to edit** to adapt the firmware to your setup.

### Platform Selection
```c
#define STM32
```

Uncomment if using STM32. Leave commented for Arduino.

### Optional CAN Module
```c
#define CAN2_OPTIONAL
```

Enable second CAN bus (IHS).  
> ⚠️ STM32 users must **physically disconnect** the unused CAN2 module or it may cause SPI instability.

### Optional Features
```c
// #define MY23   // For 2023 clusters (shift light handling)
// #define SHIFT  // Enable shift light for 7" TFT only
```
- `MY23`: activates special handling for Giulia/Stelvio MY2023 cluster.
- `SHIFT`: enables shift light display on 7" dashboards, don't use on 3.5".

Leave them commented unless you need these features.
### CAN Bus Settings
```c
#define MCP1_CLOCK MCP_8MHZ
#define CAN1_SPEED CAN_500KBPS

#define MCP2_CLOCK MCP_8MHZ
#define CAN2_SPEED CAN_125KBPS
```
- Clock frequency of each MCP2515 (typically **8MHz** or **16MHz**).
- Bitrate of each CAN bus (values for Giulia/Stelvio: **500kbps for CAN1**, **125kbps for CAN2**).

### Send Interval
```c
#define SEND_INTERVAL 0
```

Set to `0` for maximum throughput (recommended). Increase only for debugging or bandwidth issues

---

## 🔌 Pin Definitions

### STM32 (F401CCU6 Black Pill)
```c
// SPI
#define SCK_PIN    PA5
#define MISO_PIN   PA6
#define MOSI_PIN   PA7

// CAN1
#define CAN1_CS_PIN   PA4
#define CAN1_INT_PIN  PA10

// CAN2 (optional)
#define CAN2_CS_PIN   PB12
#define CAN2_INT_PIN  PB13
```

### Arduino (e.g., Uno, Nano)
```c
// CAN1
#define CAN1_CS_PIN 10
#define CAN1_INT_PIN 2

// CAN2 (optional)
#define CAN2_CS_PIN 9
#define CAN2_INT_PIN 3
```
---

## 🧪 Python Serial Communication


- Python 3.10+
- `pyserial`

Install with (in Powershell):

```bash
pip install pyserial
```

---
#### 🐍 Python Serial Communication Setup

To interact with the STM32 from your PC, the firmware exposes a USB CDC serial port (COM port).  
Use the following parameters in Python script:


| Item         | Value  | Description                       |
|--------------|--------|-----------------------------------|
| SERIAL_PORT  | "COMx" | Adjust according to your system   |
| BAUD_RATE    | 250000 | Must match the STM32 USB CDC speed|

---

## ⚙️ Hardware Setup

### Required Components

- ✅ STM32 or Arduino [i used STM32F401CCU6]
- ✅ 2x MCP2515 with TJA1050 [both with 120Ω jumper, i soldered pins]
- ✅ Real Giulia/Stelvio instrument cluster
- ✅ 12v Power Supply
- ✅ Some wires and connectors

## 🔌 Connector Pinout

![Connettore](https://github.com/user-attachments/assets/3c094945-f1c9-4e3a-9795-c4596a38559c)

| Pin | Description        |
|-----|--------------------|
| 1   | Ground             |
| 2   | Battery            |
| 3   | ACC                |
| 5   | BH - CAN A (Low)   |
| 6   | BH - CAN B (High)  |
| 11  | CAN-C1 A (Low)     |
| 12  | CAN-C1 A (Low)     |
| 13  | CAN-C1 B (High)    |
| 14  | CAN-C1 B (High)    |

> ⚠️ Notes:
> - Pins **2 and 3** must be bridged together to +12v.
> - Pins **11 and 12** are electrically connected together (same for **13 and 14**).  

> - You can connect either pin in each pair CAN A/B pairing order does not matter.
> - I personally supply **14.4V** using a variable power supply.

> - If you don’t have the original OEM connector for the cluster, don’t worry — it’s not a problem.     
>       Standard **Dupont jumper wires** or **2.54mm pitch connectors** (e.g., 2 pieces 1x6) can be used reliably without major issues.

### 🔌 SPI & CAN Wiring (STM32)

#### 🧩 Shared SPI Bus

| STM32 Pin | MCP2515 #1  | MCP2515 #2  | Description         |
|-----------|-------------|-------------|---------------------|
| PA5       | SCK         | SCK         | SPI Clock           |
| PA6       | MISO        | MISO        | SPI Master In       |
| PA7       | MOSI        | MOSI        | SPI Master Out      |
| 3.3V      | VCC         | VCC         | Logic power         |
| GND       | GND         | GND         | Common ground       |

#### 🔧 Chip Select & Interrupts

| STM32 Pin | CAN Module  | Description            |
|-----------|-------------|------------------------|
| PB12      | MCP2515 #1  | CS1 (CAN1 - 500k)      |
| PA4       | MCP2515 #2  | CS2 (CAN2 - 125k)      |
| PB13      | MCP2515 #1  | INT1 (IRQ input)       |
| PA13      | MCP2515 #2  | INT2 (IRQ input)       |

> ⚠️ Both MCP2515 share the same SPI bus. Only CS and INT are separate.

---

### 🔌 SPI & CAN Wiring (Arduino Nano)

#### 🧩 Shared SPI Bus

| Arduino Pin | MCP2515 #1 | MCP2515 #2 | Description        |
|-------------|-------------|-------------|---------------------|
| D13         | SCK         | SCK         | SPI Clock           |
| D12         | MISO        | MISO        | SPI Master In       |
| D11         | MOSI        | MOSI        | SPI Master Out      |
| 5V          | VCC         | VCC         | Logic power (5V)    |
| GND         | GND         | GND         | Common ground       |

#### 🔧 Chip Select & Interrupts

| Arduino Pin | CAN Module  | Description           |
|-------------|-------------|------------------------|
| D10         | MCP2515 #1  | CS1 (CAN1 - 500k)      |
| D9          | MCP2515 #2  | CS2 (CAN2 - 125k)      |
| D2          | MCP2515 #1  | INT1 (IRQ input)       |
| D3          | MCP2515 #2  | INT2 (IRQ input)       |

> ⚠️ Both MCP2515 share the same SPI bus. Only CS and INT are separate.

---

## ✅ Tested With

- BeamNG.drive
- Assetto Corsa
- OEM Giulia cluster (2017)
- STM32F401CCU6 (Black Pill)
- Arduino Nano
- Dual MCP2515 (8MHz, TJA1050, 120Ω)

---

## 🧪 Feature Support

| Feature             | Can Bus                                                  |
|---------------------|----------------------------------------------------------|
| **Rpm**             | CAN1                                                     |
| **Speed**           | CAN1                                                     |
| **Oil Temperature** | CAN1                                                     |
| **Fuel Level**      | CAN1                                                     |
| **ABS Light**       | CAN1 – Only `BeamNg`                                     |
| **ESC Light**       | CAN1 – Only `BeamNg`                                     |
| **Handbrake Light** | CAN1 – Only `BeamNg`                                     |
| **DNA**             | CAN1 – Idle in `Normal`*                                 |
| **Shift Light**     | CAN1 - It works only in `RACE`, Read `DNA` problem*      |
| **Current Gear**    | CAN1                                                     |
| **External Temp**   | CAN2 - Realistic in `Assetto Corsa`, dummy in `BeamNg`   |
| **MEDIA**           | CAN2 - Best Lap in `Assetto Corsa`, dummy in `BeamNg`    |

`*This feature is fully implemented in the code, but lacks proper game telemetry support for now. A workaround is being developed.`
> ⚠️ When the cluster is in **RACE** profile, **MEDIA** and **External Temp** lines on 7" dashboards are not available and cannot be displayed.    

> ℹ️ The optional second CAN module (IHS) helps **prevent odometer blinking** by providing additional messages expected by the cluster.

My glorious Setup:

![photo_2025-06-18_21-43-05](https://github.com/user-attachments/assets/05503a44-37b6-490e-b8a1-c3d60526ebee)

![photo_2025-06-16_22-49-17](https://github.com/user-attachments/assets/797eec8e-579b-4dec-9faa-3c7e21724716)

---

## ⚠️ Known Notes

- **General**:
  - On first cluster power-up, a **handbrake system fault warning** may appear.  
    This is expected and will clear automatically after a few seconds.  

  - Development is in progress to remove or map them to game events where possible:
    - **TPMS (Tire Pressure Monitoring System)**
    - **Power Steering Fault**
    - **Lane Assist / Lane Departure Warning**
    - **Headlights**

  - The following warnings are **suppressed**, but not yet synchronized with game logic:
    - **Airbag**
    - **MIL (Check Engine)**
    - **Clock/Time**
    - **Turn Signals / Indicators**

- **BeamNG.drive**:
  - Enable **OutGauge** in the settings menu.
  - A **low fuel alert** may briefly appear after respawning.

- **Assetto Corsa**:
  - No plugin required — uses native **shared memory** access.
  - On the **first launch** of Assetto Corsa make sure to **enter the track at least once before running the Python script**.

---

## 📜 License

MIT License — Use at your own risk.  
Not affiliated with Alfa Romeo, FCA, or Stellantis.
