# ESP32 WiFi & IoT

> *WiFi 與物聯網* | Level 4 prep — comes after Microcontrollers

---

## 1. Connect to WiFi（連接到 WiFi）

```cpp
#include <WiFi.h>

const char* ssid = "YOUR_WIFI_NAME";
const char* password = "YOUR_WIFI_PASSWORD";

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  Serial.print("Connecting");
  while (WiFi.status() != WL_CONNECTED) {
    delay(500); Serial.print(".");
  }
  Serial.println("\nConnected!");
  Serial.print("IP: "); Serial.println(WiFi.localIP());
}

void loop() {
  // Your code here
}
```

---

## 2. Web Server — Control LED（網頁控制 LED）

```cpp
#include <WiFi.h>
#include <WebServer.h>

const char* ssid = "YOUR_WIFI";
const char* password = "YOUR_PASS";
WebServer server(80);  // Port 80 = standard HTTP

const int ledPin = 2;
bool ledState = false;

void handleRoot() {
  String html = "<!DOCTYPE html><html><head><meta charset='UTF-8'>";
  html += "<meta name='viewport' content='width=device-width,initial-scale=1'>";
  html += "<title>ESP32 Control</title></head><body style='text-align:center;font-family:Arial;'>";
  html += "<h1>ESP32 LED Control</h1>";
  html += "<p>LED: " + String(ledState ? "ON" : "OFF") + "</p>";
  html += "<a href='/on'><button style='font-size:24px;padding:20px;'>ON</button></a> ";
  html += "<a href='/off'><button style='font-size:24px;padding:20px;'>OFF</button></a>";
  html += "</body></html>";
  server.send(200, "text/html", html);
}

void setup() {
  Serial.begin(115200);
  pinMode(ledPin, OUTPUT);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) delay(500);
  Serial.print("IP: "); Serial.println(WiFi.localIP());

  server.on("/", handleRoot);
  server.on("/on", []() { ledState = true; digitalWrite(ledPin, HIGH); server.sendHeader("Location", "/"); server.send(303); });
  server.on("/off", []() { ledState = false; digitalWrite(ledPin, LOW); server.sendHeader("Location", "/"); server.send(303); });
  server.begin();
  Serial.println("Server started!");
}

void loop() {
  server.handleClient();
}
```

> Open the IP address printed in Serial Monitor on your phone/PC browser. Tap buttons to control LED!

---

## 3. HTTP GET — Fetch Data from Internet

```cpp
#include <WiFi.h>
#include <HTTPClient.h>

void setup() {
  Serial.begin(115200);
  WiFi.begin("YOUR_WIFI", "YOUR_PASS");
  while (WiFi.status() != WL_CONNECTED) delay(500);
}

void loop() {
  HTTPClient http;
  http.begin("http://api.open-notify.org/iss-now.json");  // Free API
  int code = http.GET();
  if (code == 200) {
    String payload = http.getString();
    Serial.println(payload);
  }
  http.end();
  delay(10000);  // Every 10 seconds
}
```

---

## 4. MQTT — IoT Protocol（MQTT 物聯網協議）

MQTT = lightweight publish/subscribe protocol for IoT.

```cpp
#include <WiFi.h>
#include <PubSubClient.h>

WiFiClient espClient;
PubSubClient mqtt(espClient);
const char* mqttServer = "test.mosquitto.org"; // Free public broker

void callback(char* topic, byte* payload, unsigned int len) {
  Serial.print("Message on "); Serial.print(topic); Serial.print(": ");
  for (int i = 0; i < len; i++) Serial.print((char)payload[i]);
  Serial.println();
}

void reconnect() {
  while (!mqtt.connected()) {
    if (mqtt.connect("ESP32_Student")) {
      mqtt.subscribe("mechatronic/test");  // Subscribe to topic
    } else delay(5000);
  }
}

void setup() {
  Serial.begin(115200);
  WiFi.begin("YOUR_WIFI", "YOUR_PASS");
  while (WiFi.status() != WL_CONNECTED) delay(500);

  mqtt.setServer(mqttServer, 1883);
  mqtt.setCallback(callback);
}

void loop() {
  if (!mqtt.connected()) reconnect();
  mqtt.loop();
  mqtt.publish("mechatronic/test", "Hello from ESP32!");
  delay(5000);
}
```

---

## 5. WiFi + Sensor Dashboard（WiFi + 感測器儀表板）

Send sensor data as JSON to a web page:

```cpp
void handleData() {
  int potVal = analogRead(34);
  float temp = potVal * 3.3 / 4095.0 * 100.0;
  String json = "{\"temperature\":" + String(temp) + ",\"raw\":" + String(potVal) + "}";
  server.send(200, "application/json", json);
}
// Add: server.on("/data", handleData);
```

---

## WiFi Quick Reference（速查）

| Topic | Library | Key Function |
|---|---|---|
| Connect WiFi | `WiFi.h` | `WiFi.begin(ssid, pass)` |
| Web Server | `WebServer.h` | `server.on("/path", handler)` |
| HTTP Client | `HTTPClient.h` | `http.GET()` / `http.POST()` |
| MQTT | `PubSubClient.h` | `mqtt.publish(topic, msg)` |
| WebSocket | `WebSocketsServer.h` | Real-time bi-directional |

---

## Important: ESP32 WiFi is 2.4GHz only!
ESP32 cannot connect to 5GHz WiFi networks. Make sure your router has 2.4GHz band enabled.
