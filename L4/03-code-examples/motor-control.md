# Motor Control — Code Examples

> *馬達控制* | DC Motor, Servo, Stepper — Mechatronics Essentials

---

## 1. DC Motor — One Direction（直流馬達 — 單向）

Uses L298N or L293D motor driver (Wokwi compatible).

**Wiring**: ESP32 GPIO → L298N IN1/IN2 → Motor

```cpp
const int in1 = 16, in2 = 17, enA = 4;  // enA = PWM speed

void setup() {
  pinMode(in1, OUTPUT); pinMode(in2, OUTPUT); pinMode(enA, OUTPUT);
}

void loop() {
  // Forward
  digitalWrite(in1, HIGH); digitalWrite(in2, LOW);
  analogWrite(enA, 200);  // Speed: 0–255
  delay(3000);

  // Stop
  digitalWrite(in1, LOW); digitalWrite(in2, LOW);
  delay(1000);

  // Reverse
  digitalWrite(in1, LOW); digitalWrite(in2, HIGH);
  analogWrite(enA, 150);
  delay(3000);

  // Stop
  digitalWrite(in1, LOW); digitalWrite(in2, LOW);
  delay(1000);
}
```

| L298N Logic | IN1 | IN2 | Result |
|---|---|---|---|
| Forward | HIGH | LOW | Motor spins forward |
| Reverse | LOW | HIGH | Motor spins backward |
| Stop | LOW | LOW | Motor stops |
| Brake | HIGH | HIGH | Motor brakes |

| enA (PWM) | Speed |
|---|---|
| 0 | Stopped |
| 128 | ~50% |
| 255 | 100% |

---

## 2. DC Motor — Speed Ramp（速度漸變）

```cpp
void rampUp(int enPin) {
  for (int speed = 0; speed <= 255; speed++) {
    analogWrite(enPin, speed);
    delay(20);
  }
}

void rampDown(int enPin) {
  for (int speed = 255; speed >= 0; speed--) {
    analogWrite(enPin, speed);
    delay(20);
  }
}
```

---

## 3. Servo — Sweep & Hold（Servo 掃描與定位）

```cpp
#include <ESP32Servo.h>
Servo s;

void setup() {
  s.attach(13);  // GPIO 13 → Servo signal
}

void loop() {
  s.write(0);    delay(1000);  // 0°
  s.write(90);   delay(1000);  // 90° (center)
  s.write(180);  delay(1000);  // 180°
}
```

---

## 4. Servo — Gradual Move（Servo 漸進移動）

```cpp
void moveServo(Servo &s, int from, int to, int stepDelay = 15) {
  if (from < to) {
    for (int a = from; a <= to; a++) { s.write(a); delay(stepDelay); }
  } else {
    for (int a = from; a >= to; a--) { s.write(a); delay(stepDelay); }
  }
}
// Usage: moveServo(myServo, 0, 180, 10);
```

---

## 5. Stepper Motor — 28BYJ-48（步進馬達）

```cpp
#include <Stepper.h>

const int stepsPerRev = 2048;  // 28BYJ-48: 2048 steps/rev
Stepper myStepper(stepsPerRev, 16, 17, 5, 18); // IN1,IN2,IN3,IN4

void setup() {
  myStepper.setSpeed(10);  // RPM
}

void loop() {
  myStepper.step(512);   // 1/4 revolution clockwise
  delay(1000);
  myStepper.step(-512);  // 1/4 revolution counter-clockwise
  delay(1000);
}
```

---

## 6. Motor + Sensor Combo（馬達 + 感測器聯動）

Potentiometer controls DC motor speed:

```cpp
void loop() {
  int potVal = analogRead(34);
  int speed = map(potVal, 0, 4095, 0, 255);
  digitalWrite(in1, HIGH); digitalWrite(in2, LOW);
  analogWrite(enA, speed);
}
```

Ultrasonic stops motor when object too close:

```cpp
float dist = getDistance();  // From ultrasonic sensor code
if (dist < 10) {
  digitalWrite(in1, LOW); digitalWrite(in2, LOW); // STOP!
} else {
  digitalWrite(in1, HIGH); digitalWrite(in2, LOW); // FORWARD
}
```

---

## Motor Quick Reference

| Motor Type | Control Method | Library |
|---|---|---|
| DC Motor | L298N + PWM | None (digitalWrite + analogWrite) |
| Servo | PWM pulse (0.5–2.5ms) | `ESP32Servo.h` |
| Stepper (28BYJ-48) | Sequence of coil pulses | `Stepper.h` |
| Stepper (NEMA 17) | A4988 driver + step pulses | `AccelStepper.h` |
