# Mechatronics Integration Projects

> *機電整合專案思路* | Level 3 → 4 Bridge

---

## Mechatronics = MEchanics + elecTRONICS + informaTICS

A mechatronic system has 4 layers:

```
┌─────────────────────────┐
│    CONTROL (Software)    │  ← Arduino / ESP32 code
├─────────────────────────┤
│    COMMUNICATION         │  ← UART / I2C / SPI / WiFi
├─────────────────────────┤
│    ACTUATORS             │  ← Motors, Servos, Relays
├─────────────────────────┤
│    SENSORS               │  ← Temperature, Distance, Force
└─────────────────────────┘
```

---

## Project 1: Line-Following Robot（循線機器人）

**Components**: 2x IR sensors + 2x DC motors + L298N

**Concept**: IR sensors detect black/white. If left sensor sees black → turn left. If both see white → go straight.

```cpp
const int leftIR = 34, rightIR = 35;

void loop() {
  bool l = analogRead(leftIR) > 2000;   // BLACK = HIGH
  bool r = analogRead(rightIR) > 2000;

  if (!l && !r) { goForward(); }         // Both white: straight
  else if (l && !r) { turnLeft(); }      // Left black: turn left
  else if (!l && r) { turnRight(); }     // Right black: turn right
  else { stop(); }                       // Both black: arrived
}
```

---

## Project 2: Temperature-Controlled Fan（溫控風扇）

**Components**: LM35 + DC Motor (fan) + L298N

```cpp
void loop() {
  float temp = analogRead(34) * 3.3 / 4095.0 * 100.0;

  if (temp > 35) {
    analogWrite(enA, 255);  // Full speed — too hot!
  } else if (temp > 28) {
    analogWrite(enA, 150);  // Medium
  } else {
    analogWrite(enA, 0);    // Off — cool enough
  }

  delay(1000);
}
```

---

## Project 3: Obstacle-Avoiding Robot（避障機器人）

**Components**: HC-SR04 + Servo + 2x DC motors

**Logic**:
1. Move forward
2. If obstacle < 20cm: Stop → Look left (Servo 180°) → measure distance
3. Look right (Servo 0°) → measure distance
4. Turn toward the side with more space

```cpp
float lookAndMeasure(Servo &s, int angle) {
  s.write(angle); delay(500);
  return getDistance();  // From ultrasonic code
}

void loop() {
  float dist = getDistance();
  if (dist > 20) { goForward(); }
  else {
    stop();
    float leftDist = lookAndMeasure(servo, 180);
    float rightDist = lookAndMeasure(servo, 0);
    if (leftDist > rightDist) turnLeft();
    else turnRight();
  }
}
```

---

## Project 4: PID Speed Control（PID 速度控制概念）

PID = Proportional-Integral-Derivative. The foundation of industrial control.

```
Error = Target - Actual
Output = Kp×Error + Ki×∫Error + Kd×d(Error)/dt
```

| Term | Effect | Tuning |
|---|---|---|
| **P** (Proportional) | Reacts to current error | Too high = oscillation |
| **I** (Integral) | Eliminates steady-state error | Too high = overshoot |
| **D** (Derivative) | Predicts future, dampens | Too high = noisy |

```cpp
float Kp = 2.0, Ki = 0.1, Kd = 0.5;
float integral = 0, lastError = 0;

float computePID(float target, float actual) {
  float error = target - actual;
  integral += error;
  float derivative = error - lastError;
  lastError = error;
  return Kp * error + Ki * integral + Kd * derivative;
}

// Usage:
// int targetSpeed = 200;
// int actualSpeed = readEncoder();
// int pwmOutput = computePID(targetSpeed, actualSpeed);
// analogWrite(motorPin, constrain(pwmOutput, 0, 255));
```

---

## State Machine Design（狀態機設計）

Complex mechatronic systems use state machines.

```cpp
enum State { IDLE, MOVING, OBSTACLE, TURNING, STOPPED };
State currentState = IDLE;

void loop() {
  switch (currentState) {
    case IDLE:
      if (startButtonPressed()) currentState = MOVING;
      break;
    case MOVING:
      goForward();
      if (obstacleDetected()) currentState = OBSTACLE;
      break;
    case OBSTACLE:
      stop();
      currentState = TURNING;
      break;
    case TURNING:
      turnLeft();
      if (obstacleCleared()) currentState = MOVING;
      break;
    case STOPPED:
      stop();
      if (resetButtonPressed()) currentState = IDLE;
      break;
  }
}
```

---

## What's Coming in Level 4 (MC 091-04-2016)

Based on typical Malaysian Mechatronic Diploma curriculum:

| Topic | What You'll Learn | Prep Now |
|---|---|---|
| **PLC & Industrial Automation** | Ladder logic, PLC programming | Learn state machines first |
| **Robotics & Kinematics** | Forward/inverse kinematics | Brush up trigonometry |
| **Power Electronics & Drives** | MOSFET, IGBT, motor drives | Master PWM + motor control |
| **Mechatronic System Integration** | Putting it all together | Practice multi-sensor projects |
| **Industrial Networks & IoT** | Modbus, CAN bus, MQTT | Learn WiFi + MQTT basics now |
| **Control System Engineering** | PID, stability analysis | Understand PID concept |
| **Maintenance & Reliability** | Predictive maintenance | Learn to log & analyze sensor data |

> **Your advantage**: By mastering Arduino/Wokwi now (Level 3), you'll walk into Level 4 already comfortable with code, sensors, and actuators. The Level 4 topics are just scaling up — more sensors, more motors, industrial protocols, formal control theory.

---

## Recommended Learning Path（建議學習路線）

```
Now (L3):  Arduino Basics → Sensors → Actuators → Simple Integration
         ↓
Soon:     Interrupts → WiFi/IoT → PID Concept → State Machines
         ↓
L4 Prep:  Industrial Protocols → PLC Basics → System Integration → Control Theory
```
