# Breathing LED — PWM Project

> *PWM 呼吸燈* | Copy & Run in Wokwi
> **Skill**: `analogWrite`, PWM, duty cycle, for loops
> **Parts**: None (uses ESP32 built-in LED on GPIO 2)

---

## Code（程式碼）

```cpp
const int ledPin = 2;  // ESP32 built-in LED

void setup() {
  pinMode(ledPin, OUTPUT);
}

void loop() {
  // Fade in（暗 → 亮）
  for (int brightness = 0; brightness <= 255; brightness++) {
    analogWrite(ledPin, brightness);
    delay(10);  // 10ms per step → ~2.5s fade-in
  }
  // Fade out（亮 → 暗）
  for (int brightness = 255; brightness >= 0; brightness--) {
    analogWrite(ledPin, brightness);
    delay(10);
  }
}
```

---

## PWM Explained（PWM 原理）

PWM (Pulse Width Modulation) = switch ON/OFF very fast, change the ratio.

```
Duty 25%:  █░░░ █░░░ █░░░ █░░░  → dim
Duty 50%:  ██░░ ██░░ ██░░ ██░░  → half
Duty 75%:  ███░ ███░ ███░ ███░  → bright
Duty 100%: ████ ████ ████ ████  → full
```

ESP32 PWM frequency is so high your eye sees "different brightness", not flicker.

---

## analogWrite Reference

```cpp
analogWrite(pin, value);
```

| value | Effect |
|---|---|
| 0 | OFF (0% duty) |
| 64 | ~25% duty — dim |
| 128 | ~50% duty — half |
| 192 | ~75% duty — bright |
| 255 | ON (100% duty) |

> **ESP32 Note**: `analogWrite` works on any GPIO. Uno only supports pins marked with `~` (3, 5, 6, 9, 10, 11).

---

## Variations（變化玩法）

### 1. Faster breathing（快速呼吸）
Change `delay(10)` → `delay(2)` — much faster.

### 2. Non-linear（非線性呼吸）
```cpp
// Feels more "natural" to human eyes
int brightness = (sin(millis() * 0.001) + 1) * 127.5;
analogWrite(ledPin, brightness);
delay(10);
```

### 3. Multiple LEDs（多顆 LED）
```cpp
const int leds[] = {2, 4, 5};  // 3 LEDs
for (int b = 0; b <= 255; b++) {
  for (int i = 0; i < 3; i++) analogWrite(leds[i], b);
  delay(10);
}
```

---

## Mechatronics Connection（機電應用）

Same PWM principle controls:
- **DC Motor speed** — via motor driver (L298N)
- **Servo angle** — PWM pulse width determines position
- **LED dimming** — for displays / indicators
- **Heater power** — via MOSFET
