# Hardware Interrupts

> *硬體中斷* | Advanced topic for Level 3 → 4 transition

---

## What is an Interrupt?（什麼是中斷？）

Normal code: runs line by line in `loop()`.
Interrupt: hardware event PAUSES `loop()`, runs a special function, then resumes.

> Think: you're reading a book. The doorbell rings. You put down the book, answer the door, then return to reading. The doorbell = interrupt.

**Why use interrupts?**
- Don't miss fast events (encoder pulses, button presses)
- Don't waste CPU `delay()`-polling
- Real-time response

---

## Basic Interrupt — Button（按鈕中斷）

```cpp
const int buttonPin = 15;
const int ledPin = 2;

volatile bool ledState = false;  // volatile = can change anytime!

void IRAM_ATTR buttonISR() {  // ISR = Interrupt Service Routine
  ledState = !ledState;       // Toggle state
  digitalWrite(ledPin, ledState);
}

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(buttonPin), buttonISR, FALLING);
  Serial.begin(115200);
}

void loop() {
  Serial.print("LED: ");
  Serial.println(ledState ? "ON" : "OFF");
  delay(500);  // loop runs normally, ISR interrupts it
}
```

| Keyword | Meaning |
|---|---|
| `volatile` | Tells compiler: this variable can change in ISR |
| `IRAM_ATTR` | Puts ISR code in RAM (faster, required on ESP32) |
| `attachInterrupt(pin, ISR, mode)` | Attach interrupt to pin |
| `digitalPinToInterrupt(pin)` | Convert pin number to interrupt number |

---

## Interrupt Modes（中斷模式）

| Mode | Triggers When |
|---|---|
| `LOW` | Pin is LOW |
| `HIGH` | Pin is HIGH |
| `CHANGE` | Pin changes (HIGH→LOW or LOW→HIGH) |
| `FALLING` | HIGH → LOW（按下按鈕時觸發，搭配 INPUT_PULLUP） |
| `RISING` | LOW → HIGH |

---

## ISR Rules（中斷服務程式規則）— MUST FOLLOW

| Rule | Why |
|---|---|
| Keep ISR **very short** | Blocks everything else while running |
| **No `delay()`** in ISR | Interrupts are disabled during ISR |
| **No `Serial.print()`** in ISR | Too slow, will crash |
| Use `volatile` for shared variables | Compiler won't optimize them away |
| No `millis()` in ISR | Won't update during ISR |
| `IRAM_ATTR` on ESP32 | Code must be in RAM, not Flash |

---

## Correct Pattern: ISR sets flag, loop() handles it

```cpp
volatile bool buttonPressed = false;

void IRAM_ATTR buttonISR() {
  buttonPressed = true;  // Only set a flag! Fast!
}

void setup() {
  pinMode(15, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(15), buttonISR, FALLING);
  Serial.begin(115200);
}

void loop() {
  if (buttonPressed) {
    buttonPressed = false;  // Clear flag
    Serial.println("Button was pressed!");  // Heavy work here, not in ISR
    // Do your processing...
  }
}
```

---

## Encoder Reading with Interrupts（編碼器中斷讀取）

Rotary encoder — common in mechatronics for position/speed sensing.

```cpp
const int encA = 16, encB = 17;
volatile int encoderCount = 0;

void IRAM_ATTR encISR() {
  if (digitalRead(encB) == HIGH) encoderCount++;
  else                            encoderCount--;
}

void setup() {
  pinMode(encA, INPUT_PULLUP); pinMode(encB, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(encA), encISR, CHANGE);
  Serial.begin(115200);
}

void loop() {
  static int lastCount = 0;
  if (encoderCount != lastCount) {
    Serial.print("Encoder: "); Serial.println(encoderCount);
    lastCount = encoderCount;
  }
}
```

---

## Interrupt Pins on ESP32

**All GPIO pins on ESP32 support interrupts** — unlike Arduino Uno (only pins 2 and 3).

Recommended for interrupts: GPIO 15, 16, 17, 18, 19, 21, 22, 23, 25, 26, 27, 32, 33.

---

## When NOT to Use Interrupts

- Simple button reading → use `digitalRead()` polling (simpler, safer)
- Slow sensors (temperature every 1s) → polling is fine
- Any task that takes > 1µs → handle in `loop()`, not ISR
