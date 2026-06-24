# Button Input — Wokwi Project

> *按鈕輸入* | Copy & Run in Wokwi
> **Skill**: `digitalRead`, `INPUT_PULLUP`, debounce, state machine
> **Parts**: Push Button + LED (or built-in LED)

---

## Wiring（接線）

Add **Push Button** and **LED** from the `+` menu:

| Button Pin | ESP32 |
|---|---|
| Terminal 1 | GPIO **15** |
| Terminal 2 | **GND** |

| LED | ESP32 |
|---|---|
| Anode (+) | GPIO **2** (built-in LED, or add external) |
| Cathode (-) | **GND** (via 220Ω resistor if external) |

---

## Code — Basic Toggle（基本切換）

```cpp
const int buttonPin = 15;
const int ledPin = 2;

bool ledState = false;

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);  // Internal pull-up!
  Serial.begin(115200);
}

void loop() {
  // Button is LOW when pressed (INPUT_PULLUP reverses logic!)
  if (digitalRead(buttonPin) == LOW) {
    ledState = !ledState;               // Toggle
    digitalWrite(ledPin, ledState);
    Serial.print("LED: ");
    Serial.println(ledState ? "ON" : "OFF");
    delay(300);                         // Simple debounce
  }
}
```

---

## INPUT_PULLUP Explained（內建上拉電阻）

| Mode | When NOT pressed | When pressed |
|---|---|---|
| `INPUT_PULLUP` | Reads `HIGH` (1) | Reads `LOW` (0) |
| `INPUT` (with external resistor) | Depends on wiring | Depends on wiring |

> **Why INPUT_PULLUP?** No external resistor needed. Connect button between pin and GND.
> 為什麼用 INPUT_PULLUP？不需要外接電阻，按鈕接引腳和 GND 就好。

---

## Code — Debounced Button（去彈跳）

Mechanical buttons "bounce" — one press triggers multiple reads. Fix:

```cpp
const int buttonPin = 15;
const int ledPin = 2;

int lastButtonState = HIGH;
unsigned long lastDebounceTime = 0;
const unsigned long debounceDelay = 50; // 50ms

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(buttonPin, INPUT_PULLUP);
}

void loop() {
  int reading = digitalRead(buttonPin);

  if (reading != lastButtonState) {
    lastDebounceTime = millis();  // Reset timer
  }

  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (reading == LOW) {  // Button pressed
      digitalWrite(ledPin, !digitalRead(ledPin)); // Toggle LED
    }
  }

  lastButtonState = reading;
}
```

| Function | Meaning |
|---|---|
| `millis()` | Returns ms since board started（開機以來的毫秒數） |
| `digitalRead(pin)` | Read pin: HIGH (1) or LOW (0) |

---

## Code — Press & Hold（按住才亮）

```cpp
void loop() {
  if (digitalRead(buttonPin) == LOW) {
    digitalWrite(ledPin, HIGH);   // ON while pressed
  } else {
    digitalWrite(ledPin, LOW);    // OFF when released
  }
}
```

---

## Common Mistakes（常見錯誤）

| Mistake | Fix |
|---|---|
| LED always ON, button does nothing | Check `INPUT_PULLUP` — pressed = LOW, not HIGH |
| LED flickers on single press | Add debounce: `delay(50)` or use `millis()` method |
| Forgot `pinMode(buttonPin, INPUT_PULLUP)` | Pin floats → reads random values |
