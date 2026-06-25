# Common Issues & Troubleshooting

> *常見問題排解* | 20+ issues beginners face

---

## Compilation Errors（編譯錯誤）

| Error | Cause | Fix |
|---|---|---|
| `expected ';' before '}'` | Missing semicolon | Add `;` at end of previous line |
| `'variable' was not declared` | Variable used before declaration | Declare it first: `int x;` |
| `expected '}' at end of input` | Unmatched `{}` | Count braces — every `{` needs `}` |
| `No such file or directory` | Missing library | Install via Library Manager or add `#include` correctly |
| `redefinition of 'loop'` | Two `loop()` functions | Only ONE `loop()`. Merge them |
| `error: 'HIGH' was not declared` | Missing `Arduino.h` or wrong board selected | Select correct board in Wokwi/IDE |
| `collect2: error: ld returned 1` | Linker error — usually missing function body | Implement all declared functions |

---

## LED Won't Light Up（LED 不亮）

| Check | Action |
|---|---|
| Correct pin number? | `pinMode(2, OUTPUT)` must match wiring |
| LED polarity? | Anode (long leg) → GPIO, Cathode (short leg) → GND via 220Ω resistor |
| Forgot `pinMode`? | Must call `pinMode(pin, OUTPUT)` in `setup()` |
| Resistor too high? | Use 220Ω (red-red-brown). 10kΩ makes LED invisible |
| Wrong pin in breadboard? | Breadboard rows are connected horizontally, not vertically |
| Code uploading correctly? | Add `Serial.println("setup done")` to confirm code runs |

---

## Servo Not Moving（Servo 不動）

| Problem | Solution |
|---|---|
| No power | Servo needs 5V — don't power from 3.3V |
| Wrong pin | Check `myServo.attach(pin)` matches wiring |
| Forgot `#include <ESP32Servo.h>` | Must include library |
| Angle out of range | Valid: 0°–180°. `myServo.write(200)` won't work |
| No `delay()` in loop | Servo needs continuous PWM. Don't let loop exit instantly |

---

## Sensor Readings Wrong（感測器讀值錯誤）

### ADC / analogRead

| Issue | Fix |
|---|---|
| Always 0 | Check wiring — is VCC connected? Is signal connected? |
| Always 4095 | Short circuit? Pin floating? Check GND connection |
| Unstable / jumping | Add averaging: read 10 times, divide by 10 |
| Wrong voltage conversion | ESP32: `raw * 3.3 / 4095.0`. Uno: `raw * 5.0 / 1023.0` |
| Pin not ADC-capable | ESP32 uses ADC1 (GPIO 32-39). GPIO 34-35, 36, 39 are input-only |

### Ultrasonic HC-SR04

| Issue | Fix |
|---|---|
| Always 0 cm | Check TRIG/ECHO not swapped. Add `delay(60)` between readings |
| Wrong distance | Sound speed = 0.0343 cm/µs. Formula: `duration * 0.0343 / 2` |
| Too close / too far | Range is 2–400 cm. Beyond that = inaccurate |

---

## I2C Device Not Found（找不到 I2C 設備）

| Check | Action |
|---|---|
| Wiring | SDA → 21, SCL → 22 (ESP32 default) |
| Address wrong | Run I2C Scanner to find correct address |
| Pull-up resistors | Some modules need 4.7kΩ pull-up on SDA/SCL |
| Power | Some I2C devices need 5V, not 3.3V |

---

## WiFi Won't Connect（WiFi 連不上）

| Issue | Fix |
|---|---|
| Wrong SSID/password | Double-check credentials |
| `WL_CONNECT_FAILED` | Router too far, wrong password, or 5GHz (ESP32 = 2.4GHz only) |
| `WIFI_AUTH_OPEN` | WiFi network has no password — use different connection method |
| Code stuck at `WiFi.begin()` | Add timeout: `while (WiFi.status() != WL_CONNECTED && attempts < 20)` |

---

## Program Won't Upload（程式無法上傳）

| Wokwi | Solution |
|---|---|
| Simulation stuck | Click Stop, then Run again. Or refresh page |
| Syntax error | Fix red-highlighted code before running |

---

## General Debugging Checklist（通用偵錯清單）

1. **Serial output?** — Add `Serial.println("1")`, `Serial.println("2")` to trace where code reaches
2. **LED blink test?** — Blink the built-in LED to confirm board works
3. **Breadboard connections?** — Re-seat all wires. Check row connections
4. **Pin numbers?** — Code pin vs. physical pin must match
5. **Caps matter** — `pinMode` not `pinmode` not `PinMode`
6. **Variable scope** — Variable declared in `setup()` won't exist in `loop()`

---

## 繁體中文常見錯誤速查

| 錯誤 | 解法 |
|---|---|
| 忘了 `;` 分號 | 每行指令結尾要有 `;` |
| `{}` 大括號不配對 | 數一數，每 `{` 要有一個 `}` |
| 大小寫錯誤 | `pinMode` 是對的，`pinmode` 和 `PinMode` 都是錯的 |
| 變數宣告在 `setup()` 裡 | 放到檔案最上面（全域變數），`loop()` 才能用 |
| `analogRead()` 卻寫了 `pinMode` | 類比輸入不需要 `pinMode()` |
| 按鈕顛倒 | `INPUT_PULLUP` 模式：按下 = LOW，放開 = HIGH |
