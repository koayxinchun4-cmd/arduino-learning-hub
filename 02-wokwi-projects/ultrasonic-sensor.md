# Ultrasonic Distance Sensor (HC-SR04)

> *超音波距離感測器* | Copy & Run in Wokwi
> **Skill**: `pulseIn()`, timing, distance calculation
> **Parts**: HC-SR04 Ultrasonic Sensor

---

## How HC-SR04 Works（原理）

1. **Trigger** pin: send a 10µs HIGH pulse → sensor emits 40kHz ultrasound
2. **Echo** pin: goes HIGH until the sound bounces back
3. Measure Echo HIGH duration → calculate distance

> Sound speed: ~343 m/s = 0.0343 cm/µs
> Distance = (Echo duration in µs) × 0.0343 / 2

---

## Wiring（接線）

| HC-SR04 | ESP32 |
|---|---|
| VCC | **5V** |
| GND | **GND** |
| TRIG | GPIO **5** |
| ECHO | GPIO **18** |

---

## Code（程式碼）

```cpp
const int trigPin = 5;
const int echoPin = 18;

void setup() {
  Serial.begin(115200);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}

void loop() {
  // Send 10µs HIGH pulse to TRIG
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // Measure how long ECHO stays HIGH
  long duration = pulseIn(echoPin, HIGH);

  // Calculate distance in cm
  float distance = duration * 0.0343 / 2.0;

  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  delay(500);
}
```

| Command | Meaning |
|---|---|
| `delayMicroseconds(µs)` | Pause for microseconds (1µs = 0.001ms) |
| `pulseIn(pin, HIGH)` | Measure how long pin stays HIGH (returns µs) |
| `duration * 0.0343 / 2` | µs → cm conversion |

---

## Code — With LED Warning（加 LED 警告）

```cpp
const int trigPin = 5;
const int echoPin = 18;
const int ledPin = 2;     // Built-in LED
const int buzzerPin = 4;  // Optional buzzer

void setup() {
  Serial.begin(115200);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(ledPin, OUTPUT);
}

void loop() {
  digitalWrite(trigPin, LOW); delayMicroseconds(2);
  digitalWrite(trigPin, HIGH); delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH);
  float distance = duration * 0.0343 / 2.0;

  Serial.print("Distance: "); Serial.print(distance); Serial.println(" cm");

  if (distance < 10) {
    digitalWrite(ledPin, HIGH);   // Object too close!
    Serial.println("  WARNING: Too close!");
  } else {
    digitalWrite(ledPin, LOW);
  }

  delay(200);
}
```

---

## Troubleshooting（常見問題）

| Problem | Likely Cause |
|---|---|
| Distance always 0 | Check wiring — TRIG/ECHO swapped? |
| Distance wrong | Object too close (<2cm) or too far (>400cm) |
| Unstable readings | Add averaging: take 5 readings, divide by 5 |
| pulseIn timeout | Default timeout 1 second. Set: `pulseIn(echoPin, HIGH, 30000)` |

---

## Mechatronics Application（機電應用）

- **Obstacle avoidance robot**（避障機器人）
- **Parking sensor**（倒車雷達）
- **Liquid level detection**（液位檢測）
- **Object counting on conveyor**（輸送帶計數）
