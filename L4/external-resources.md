# L4 — External Resources

> *整理自 davidmonterocrespo24 repos + arduino-cli + Velxio*

---

## 1. Velxio — Browser Arduino/ESP32/RPi Emulator

| Field | Detail |
|---|---|
| Repo | [davidmonterocrespo24/velxio](https://github.com/davidmonterocrespo24/velxio) |
| Stars | 2,250+ |
| Language | TypeScript |
| Description | Emulate Arduino, ESP32 & Raspberry Pi in your browser. Write code, compile, and run on 19 real boards — Arduino Uno, ESP32, ESP32-C3, Raspberry Pi Pico, Raspberry Pi 3, and more. No hardware, no cloud, no limits. |

**Why it matters / 為什麼重要:**

Velxio is a direct alternative/complement to Wokwi. It supports 19 board types — more than Wokwi in some areas (e.g. full Raspberry Pi 3 emulation). You can use it when Wokwi doesn't have the board you need.

Velxio 是 Wokwi 的直接替代/補充方案。支援 19 種開發板，包含 Wokwi 沒有的（如 Raspberry Pi 3 完整模擬）。Wokwi 沒有的板子就用 Velxio。

| Velxio vs Wokwi | Velxio | Wokwi |
|---|---|---|
| Boards | 19 (incl. RPi 3) | ~15 |
| ESP32 | Yes | Yes |
| Arduino Uno | Yes | Yes |
| Raspberry Pi Pico | Yes | Yes |
| Raspberry Pi 3 | Yes | No |
| Discord Community | Yes | Yes |

---

## 2. Arduino CLI — Official Command-Line Tool

| Field | Detail |
|---|---|
| Repo | [arduino/arduino-cli](https://github.com/arduino/arduino-cli) |
| Stars | 4,945+ |
| Language | Go |
| License | GPL-3.0 |
| Description | Arduino command line tool — compile, upload, manage libraries & boards without the IDE. |

**Why it matters / 為什麼重要:**

When you eventually get real Arduino hardware (L4 practical), `arduino-cli` lets you compile and upload code from the terminal — faster than the Arduino IDE. Also integrates with VS Code and CI/CD pipelines.

當你之後拿到真實 Arduino 硬體（L4 實作課），`arduino-cli` 讓你從終端機直接編譯和上傳程式碼，比 Arduino IDE 快。也能整合 VS Code。

**Quick Install / 快速安裝:**
```bash
# Linux/macOS
curl -fsSL https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh | sh

# Windows (PowerShell)
# Download from: https://arduino.github.io/arduino-cli/installation/
```

**Basic Commands / 基本指令:**
```bash
arduino-cli board list          # List connected boards / 列出連接的板子
arduino-cli compile --fqbn arduino:avr:uno sketch/   # Compile / 編譯
arduino-cli upload -p /dev/ttyUSB0 --fqbn arduino:avr:uno sketch/  # Upload / 上傳
arduino-cli lib install "Servo"  # Install library / 安裝函式庫
```

---

## 3. David Monterocrespo24 — Curated Arduino/Embedded Repos

From 50+ repos, these are the ones potentially useful for L4 Arduino learning:

從 50+ 個倉庫中，以下是對 L4 Arduino 學習可能有用：

| Repo | Stars | Relevance | What It Is |
|---|---|---|---|
| [velxio](https://github.com/davidmonterocrespo24/velxio) | 2,250 | ⭐⭐⭐⭐⭐ | Arduino/ESP32/RPi browser emulator (covered above) |
| [Animated-EYE](https://github.com/davidmonterocrespo24/Animated-EYE) | 0 | ⭐⭐⭐ | ST7789 TFT LCD display with eye animation — good display project reference |
| [andro-copter](https://github.com/davidmonterocrespo24/andro-copter) | 0 | ⭐⭐ | Drone project (old, from code.google.com) — reference for motor + sensor integration |
| [ArduKidAI](https://github.com/davidmonterocrespo24/ArduKidAI) | 1 | ⭐⭐ | Name suggests Arduino + AI — may be TypeScript, needs checking |
| [build-your-own-x](https://github.com/davidmonterocrespo24/build-your-own-x) | 0 | ⭐ | General programming tutorials (not Arduino-specific, but good fundamentals) |

> **Note**: The remaining 45+ repos are mostly AI/LLM/agent development tools and Odoo/blockchain projects — not relevant to Mechatronic L4 Arduino studies.
>
> **註**：其餘 45+ 個倉庫多數是 AI/LLM/Agent 開發工具、Odoo/區塊鏈專案 — 與機電 L4 Arduino 學習無關。

---

## Recommended Priority / 建議優先級

1. **Start with Wokwi** (already in `02-wokwi-projects/`) — easiest entry point
2. **Try Velxio** when you want to explore boards Wokwi doesn't have (RPi 3 etc.)
3. **Install Arduino CLI** only when you have real hardware — not needed for simulation
4. **Browse Animated-EYE** if you want a fun display side project
