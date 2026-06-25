# Analog Sensors — Code Examples

> *類比感測器* | Ready-to-use snippets
> Command: `analogRead(pin)` → 0–4095 (ESP32) or 0–1023 (Uno)

---

## 1. Potentiometer（電位器）

```cpp
const int potPin = 34;

void setup() { Serial.begin(115200); }
// No pinMode needed for analogRead!

void loop() {
  int raw = analogRead(potPin);
  int percent = map(raw, 0, 4095, 0, 100);
  Serial.print("Raw: "); Serial.print(raw);
  Serial.print("  %: "); Serial.println(percent);
  delay(200);
}
```

---

## 2. LDR — Light Sensor（光敏電阻）

```cpp
const int ldrPin = 34;  // LDR + 10kΩ voltage divider → GPIO 34

void setup() { Serial.begin(115200); }

void loop() {
  int light = analogRead(ldrPin);  // HIGH = bright, LOW = dark
  Serial.print("Light level: "); Serial.println(light);
  delay(200);
}
```

**Wiring**: LDR一端接3.3V，另一端接GPIO 34 + 10kΩ電阻到GND（電壓分壓器）。

---

## 3. LM35 — Temperature Sensor（溫度感測器）

```cpp
const int tempPin = 34;  // LM35 Vout → GPIO 34

void setup() { Serial.begin(115200); }

void loop() {
  int raw = analogRead(tempPin);
  // LM35: 10mV per °C. ESP32 ADC: 3.3V / 4095 = 0.806mV per step
  float tempC = raw * (3.3 / 4095.0) * 100.0; // mV → °C
  Serial.print("Temperature: "); Serial.print(tempC); Serial.println(" °C");
  delay(1000);
}
```

> **Important**: LM35 needs 4–30V supply. Use ESP32 **5V pin** for VCC, not 3.3V!

---

## 4. Reading Multiple Sensors（多感測器讀取）

```cpp
const int sensors[] = {34, 35, 32, 33};  // 4 sensors
const int numSensors = 4;
const char* names[] = {"S1", "S2", "S3", "S4"};

void setup() { Serial.begin(115200); }

void loop() {
  for (int i = 0; i < numSensors; i++) {
    int val = analogRead(sensors[i]);
    Serial.print(names[i]); Serial.print(": "); Serial.print(val); Serial.print("\t");
  }
  Serial.println();
  delay(500);
}
```

---

## 5. Averaging for Stable Readings（平均值濾波）

```cpp
int readAverage(int pin, int samples = 10) {
  long sum = 0;
  for (int i = 0; i < samples; i++) {
    sum += analogRead(pin);
    delay(1);
  }
  return sum / samples;
}
// Usage: int stableValue = readAverage(34);
```

---

## 6. Threshold Detection（閾值偵測）

```cpp
const int sensorPin = 34;
const int threshold = 2000;  // Adjust based on your sensor

void setup() {
  Serial.begin(115200);
  pinMode(2, OUTPUT); // Alert LED
}

void loop() {
  int val = analogRead(sensorPin);
  if (val > threshold) {
    digitalWrite(2, HIGH);
    Serial.println("ALERT: Threshold exceeded!");
  } else {
    digitalWrite(2, LOW);
  }
  delay(100);
}
```

---

## ADC Pin Reference for ESP32

| ADC1 Channels (Works with WiFi) | GPIO |
|---|---|
| ADC1_CH0 | 36 |
| ADC1_CH1 | 37 |
| ADC1_CH2 | 38 |
| ADC1_CH3 | 39 |
| ADC1_CH4 | 32 |
| ADC1_CH5 | 33 |
| ADC1_CH6 | 34 |
| ADC1_CH7 | 35 |

> GPIO 34, 35, 36, 39 are **input-only** — no pull-up/pull-down, no output!

---

## Voltage Divider Formula（分壓公式）

```
Vout = Vin × R2 / (R1 + R2)
```

For LDR: R1 = LDR (variable), R2 = fixed 10kΩ.
For thermistor: same arrangement.
