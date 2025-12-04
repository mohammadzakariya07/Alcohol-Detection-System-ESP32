# Alcohol Detection System Using ESP32, MQ-3 Sensor, OLED & Relay

A real-time alcohol monitoring and motor control system built using **ESP32**, **MQ-3 gas sensor**, **SSD1306 OLED display**, and a **relay module**.  
The project turns the motor ON/OFF based on alcohol threshold levels and displays data on an OLED screen.

---

## 🚀 Features  
- Real-time alcohol detection using MQ-3  
- OLED display shows live sensor reading  
- Motor ON/OFF control using relay  
- Correct Active-LOW relay logic  
- Threshold-based safety mechanism  
- Serial monitor logs values + threshold  

---

## 📌 Components Used  
- ESP32 Development Board  
- MQ-3 Alcohol/Gas Sensor  
- SSD1306 128×64 OLED Display (I2C)  
- Relay Module (Active LOW)  
- Jumper wires  
- DC Motor or Load  

---

## 🛠️ Circuit Connections  
| Component       | ESP32 Pin |
|----------------|-----------|
| MQ-3 OUT       | GPIO 34   |
| Relay IN       | GPIO 27   |
| OLED SDA       | GPIO 21   |
| OLED SCL       | GPIO 22   |
| VCC            | 3.3V      |
| GND            | GND       |

---

## 📄 Code

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define MQ3_PIN 34
#define RELAY_PIN 27
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);

// NEW THRESHOLD VALUE
int threshold = 600;  // change as needed

void setup() {
  Serial.begin(115200);

  pinMode(RELAY_PIN, OUTPUT);

  // Motor OFF initially (ACTIVE LOW relay)
  digitalWrite(RELAY_PIN, HIGH);

  if (!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    Serial.println("OLED Not Found");
    while (1);
  }

  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);
  display.setCursor(0,0);
  display.println("Alcohol Detection");
  display.println("Using ESP32");
  display.display();
  delay(2000);
}

void loop() {
  int mqValue = analogRead(MQ3_PIN);

  Serial.print("MQ3 Value: ");
  Serial.print(mqValue);
  Serial.print(" | Threshold: ");
  Serial.println(threshold);

  display.clearDisplay();
  display.setCursor(0,0);
  display.setTextSize(1);
  display.println("Alcohol Level:");

  display.setTextSize(2);
  display.setCursor(0,20);
  display.println(mqValue);

  // --- FIXED RELAY CONTROL (ACTIVE LOW) ---
  if (mqValue > threshold) {
    // Alcohol high → MOTOR OFF
    digitalWrite(RELAY_PIN, HIGH);  
    display.setTextSize(1);
    display.setCursor(0,50);
    display.println("Status: HIGH! Motor OFF");
  } 
  else {
    // Alcohol safe → MOTOR ON
    digitalWrite(RELAY_PIN, LOW);   
    display.setTextSize(1);
    display.setCursor(0,50);
    display.println("Status: SAFE Motor ON");
  }

  display.display();
  delay(300);
}
