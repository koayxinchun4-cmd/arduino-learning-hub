# Potentiometer + ADC — Wokwi Project

> *電位器 ADC 讀取* | Copy & Run in Wokwi
> **Skill**: `analogRead`, voltage conversion, `map()`

---

## Wiring（接線）

Add **Potentiometer** from the `+` menu:

| Potentiometer | ESP32 |
|---|---|
| VCC (right) | **3.3V** |
| SIG (middle) | GPIO **34** |
| GND (left) | **GND** |

---

## Code — Basic Read（基本讀取）

```cpp
const int potPin = 34;

void setup() {
  Serial.begin(115200);
  // analogRead does NOT need pinMode()!
}

void loop() {
  int raw = analogRead(potPin);           // 0–4095
  float voltage = raw * 3.3 / 4095.0;     // Convert to volts
  int percent = map(raw, 0, 4095, 0, 100); // Map to 0–100%

  Serial.print("Raw: "); Serial.print(raw);
  Serial.print("  Voltage: "); Serial.print(voltage);
  Serial.print("V  Percent: "); Serial.print(percent);
  Serial.println("%");

  delay(200);
}
```

---

## Code — LED Brightness Control（電位器控制 LED 亮度）

Add an **LED** (or use built-in LED on GPIO 2):

```cpp
const int potPin = 34;
const int ledPin = 2;   // Built-in LED

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(115200);
}

void loop() {
  int raw = analogRead(potPin);              // 0–4095
  int brightness = map(raw, 0, 4095, 0, 255); // Map to PWM range
  analogWrite(ledPin, brightness);

  Serial.print("Raw: "); Serial.print(raw);
  Serial.print("  Brightness: "); Serial.println(brightness);

  delay(50);
}
```

---

## map() Function Explained（map() 函數詳解）

```cpp
map(value, fromLow, fromHigh, toLow, toHigh)
```

| Example | Input Range | Output Range | Result |
|---|---|---|---|
| `map(2048, 0, 4095, 0, 255)` | 0–4095 | 0–255 | 128 |
| `map(0, 0, 4095, 0, 100)` | 0–4095 | 0–100 | 0 |
| `map(4095, 0, 4095, -90, 90)` | 0–4095 | -90~90 | 90 |

> **Tip**: Always use `constrain()` after `map()` to prevent overflow:
> `value = constrain(map(x, 0, 4095, 0, 255), 0, 255);`

---

## ADC Resolution Reference（ADC 解析度參考）

| Board | ADC Bits | Range |
|---|---|---|
| ESP32 | 12-bit | 0–4095 |
| Arduino Uno | 10-bit | 0–1023 |
| ESP8266 | 10-bit | 0–1023 |

Always check your board's ADC resolution!

---

## Challenge（挑戰）

Modify the code so the LED turns ON only when potentiometer > 50%.
提示：用 `if (percent > 50)` 控制 LED。
