# Arduino Zero to One — Complete Beginner's Guide

> *Arduino 零基礎入門* | Created by Marvis | 2025-06-25
> **For**: Inforgenius Mechatronic MC 091-03-2016 (Level 3)
> **Tool**: Wokwi Simulator — no hardware needed
> **Time**: ~3 hours for all 6 steps

---

## Step 0: What is Arduino?（什麼是 Arduino？）

Arduino is a **small computer board** for **controlling electronic components**.
Arduino 是一塊小型電腦板，用來控制電子零件。

| Concept | Plain English（白話） |
|---|---|
| Board（開發板） | A circuit board with a chip — the "brain" |
| MCU（微控制器） | The black chip executing your code |
| Pin（引腳） | Gold holes to connect wires/sensors/motors |
| GPIO | General Purpose Input/Output — read or write |
| Sketch | Arduino program — written in C++ |
| Upload（上傳） | Send code to board — Wokwi: click Run |

> Your school likely teaches Arduino Uno, but Wokwi supports ESP32 best. C++ syntax is identical.

---

## Step 1: LED Blink（第一個程式）

Open: **https://wokwi.com/projects/new/esp32**

**Every Arduino program has exactly TWO functions:**
```cpp
void setup() { /* runs ONCE — 只跑一次 */ }
void loop()  { /* runs FOREVER — 不斷重複 */ }
```

**Code — paste into editor:**
```cpp
void setup() {
  pinMode(2, OUTPUT);       // GPIO 2 = built-in LED
}

void loop() {
  digitalWrite(2, HIGH);    // 3.3V → LED ON（亮）
  delay(1000);              // wait 1 second
  digitalWrite(2, LOW);     // 0V → LED OFF（滅）
  delay(1000);
}
```

| Command | Meaning |
|---|---|
| `pinMode(pin, mode)` | Set pin as OUTPUT or INPUT |
| `digitalWrite(pin, level)` | HIGH = ON, LOW = OFF |
| `delay(ms)` | Pause N milliseconds |

Click ▶ Run. The blue LED blinks every second. This is **digital output**.

---

## Step 2: Serial Monitor（串口監視器 — 偵錯視窗）

```cpp
void setup() {
  Serial.begin(115200);              // Start serial（啟動串口）
  Serial.println("Hello, ESP32!");   // Print line（印一行）
}

void loop() {
  Serial.println("Loop running..."); // See this in black panel below
  delay(1000);
}
```

| Command | Meaning |
|---|---|
| `Serial.begin(baud)` | Start serial; 115200 is standard |
| `Serial.println("text")` | Print + newline（印一行換行） |
| `Serial.print("text")` | Print without newline（印出不換行） |

Look at the black panel at the bottom — your debug window.

---

## Step 3: C++ Basics（C++ 基礎）

### Variables（變數）
```cpp
int count = 0;          // integer（整數）
float voltage = 3.3;    // decimal（浮點數）
bool isOn = true;       // true/false
String name = "ESP32";  // text（字串）
```

### For Loop（for 迴圈）
```cpp
for (int i = 0; i < 10; i++) { // i: 0→1→2→...→9
  digitalWrite(2, HIGH); delay(100);
  digitalWrite(2, LOW);  delay(100);
}
```

### If / Else（條件判斷）
```cpp
if (count < 5) {
  Serial.println("count < 5");
} else {
  Serial.println("count >= 5");
}
```

### Operator Quick Reference（運算子速查）
| Syntax | Meaning | Syntax | Meaning |
|---|---|---|---|
| `a + b` | 加 | `a % b` | 取餘數 |
| `a - b` | 減 | `a++` | a 加 1 |
| `a * b` | 乘 | `a == b` | 等於？ |
| `a / b` | 除 | `a != b` | 不等於？ |
| `&&` | AND | `\|\|` | OR |

---

## Step 4: Analog Input — Potentiometer（類比輸入 — 電位器）

A sensor = a **varying voltage**. ADC reads it as a number.
感測器 = 變化的電壓，ADC 把電壓讀成數字。

**Wiring in Wokwi**: Add "Potentiometer" → SIG to GPIO **34**, VCC to **3.3V**, GND to **GND**

```cpp
const int potPin = 34;

void setup() {
  Serial.begin(115200);
  // analogRead does NOT need pinMode()!
}

void loop() {
  int value = analogRead(potPin);    // 0–4095 (12-bit ADC)
  float voltage = value * 3.3 / 4095.0;
  Serial.print("Raw: "); Serial.print(value);
  Serial.print("  Voltage: "); Serial.print(voltage);
  Serial.println(" V");
  delay(200);
}
```

| Command | Meaning |
|---|---|
| `analogRead(pin)` | Read 0–4095 (ESP32 12-bit) |
| `const int` | Constant — cannot change |

Rotate the knob → Serial Monitor values change. **ALL sensors use `analogRead()`**.

---

## Step 5: PWM — Breathing LED（PWM 呼吸燈）

PWM = switch ON/OFF at high speed. Change ON-time ratio → different brightness.
PWM = 高速開關，改變 ON 的時間比例 → 不同亮度。

| Duty Cycle | Effect |
|---|---|
| 0% | OFF |
| 50% | Half bright |
| 100% | Full ON |

```cpp
const int ledPin = 2;  // Built-in LED, PWM-capable

void setup() { pinMode(ledPin, OUTPUT); }

void loop() {
  for (int b = 0; b <= 255; b++) { analogWrite(ledPin, b); delay(10); }
  for (int b = 255; b >= 0; b--) { analogWrite(ledPin, b); delay(10); }
}
```

`analogWrite(pin, 0~255)` — same principle controls **motor speed**.

---

## Step 6: Servo Motor（Servo 馬達）

See full project: **[servo-control.md](../02-wokwi-projects/servo-control.md)**

Quick start:
```cpp
#include <ESP32Servo.h>
Servo myServo;
myServo.attach(13);      // GPIO 13 → Servo signal
myServo.write(90);       // 0°–180°
```

---

## Learning Route（學習路線）

| Step | Topic | Core Skill |
|---|---|---|
| 1 | Blink | `pinMode`, `digitalWrite`, `delay` |
| 2 | Serial Monitor | `Serial.begin`, `Serial.println` |
| 3 | C++ Basics | Variables, for, if/else |
| 4 | Potentiometer ADC | `analogRead`, voltage conversion |
| 5 | Breathing LED PWM | `analogWrite`, duty cycle |
| 6 | Servo Motor | PWM + Servo library |

> **Mantra（口訣）: `analogRead` reads the world, `analogWrite` controls the world.**
