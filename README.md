# Alfa Giulia/Stelvio Virtual Dashboard Interface

A real-time telemetry interface designed specifically for **Alfa Romeo Giulia / Stelvio dashboards**, using two **MCP2515 CAN modules**.  
It receives live telemetry from **BeamNG.drive** or **Assetto Corsa** and drives a real cluster via **CAN bus**, emulating OEM behavior.

---

## 🚗 Features

- ✅ Designed for **Alfa Romeo Giulia/Stelvio** dashboards
- ✅ Compatible with **Arduino** (e.g., Nano) and **STM32** (e.g., F401CCU6 / Black Pill)
- ✅ Dual **MCP2515** support (500 kbps CAN + 125 kbps IHS)
- ✅ Real-time **serial communication** from PC
- ✅ Supports **BeamNG.drive** and **Assetto Corsa**

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

## ⚙️ Firmware Configuration

File: `Config.h`

```cpp
#define CAN2_PRESENTE  // Uncomment to enable IHS bus (CAN2)
```

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

## ⚠️ Notes

- In BeamNG, enable OutGauge
- Assetto Corsa doesn't need plugins: uses shared memory
- !On first startup with Assetto Corsa, you must enter the track before launching the script!
---

## 📜 License

MIT License — Use at your own risk.
Not affiliated with Alfa Romeo, FCA, or Stellantis.
