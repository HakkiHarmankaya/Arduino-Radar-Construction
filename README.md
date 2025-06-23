# 🛰️ Arduino #16: Radar Yapımı (HC-SR04 + Servo + Processing)

Bu projede, **HC-SR04 ultrasonik sensör** ve **servo motor** kullanarak bir **radar benzeri sistem** tasarlanmıştır.  
Arduino üzerinden ölçülen mesafeler **Processing uygulaması** ile grafiksel olarak radar ekranında gösterilir.

🔗 [Web Siteme Bakmak İçin Tıkla](https://www.hakkiharmankaya.com/)  


---

## 🧰 Gerekli Malzemeler

- 1 adet **Arduino**
- 1 adet **HC-SR04 ultrasonik mesafe sensörü**
- 1 adet **Servo motor**
- **Jumper kabloları**

---

## ⚙️ Adım 1: Devre Tasarımı

### 🧩 Bağlantılar:

- **HC-SR04**:
  - **VCC** → **5V**
  - **GND** → **GND**
  - **TRIG** → **D10**
  - **ECHO** → **D11**

- **Servo Motor**:
  - **Sinyal** → **D12**
  - **VCC** → **5V**
  - **GND** → **GND**

---

## 💻 Adım 2: Arduino Kodunu Yükleyin

```cpp
#include <Servo.h>

const int trigPin = 10;
const int echoPin = 11;
long duration;
int distance;

Servo myServo;

void setup() {
  pinMode(trigPin, OUTPUT); 
  pinMode(echoPin, INPUT); 
  Serial.begin(9600);
  myServo.attach(12);
}

void loop() {
  for (int i = 15; i <= 165; i++) {
    myServo.write(i);
    delay(30);
    distance = calculateDistance();
    Serial.print(i);
    Serial.print(",");
    Serial.print(distance);
    Serial.print(".");
  }

  for (int i = 165; i > 15; i--) {
    myServo.write(i);
    delay(30);
    distance = calculateDistance();
    Serial.print(i);
    Serial.print(",");
    Serial.print(distance);
    Serial.print(".");
  }
}

int calculateDistance() {
  digitalWrite(trigPin, LOW); 
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH); 
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH); 
  distance = duration * 0.034 / 2;
  return distance;
}
```

## 💻 Adım 3: Processing Kodunu Yükleyin 

```cpp

import processing.serial.*;
import java.awt.event.KeyEvent;
import java.io.IOException;

Serial myPort;
String angle = "";
String distance = "";
String data = "";
String noObject;
float pixsDistance;
int iAngle, iDistance;
int index1 = 0;
int index2 = 0;

void setup() {
  size(1366, 700);
  smooth();
  myPort = new Serial(this, "COM3", 9600); // <-- Portunu değiştir
  myPort.bufferUntil('.');
}

void draw() {
  fill(98,245,31);
  noStroke();
  fill(0,4);
  rect(0, 0, width, 1010);
  fill(98,245,31);
  drawRadar();
  drawLine();
  drawObject();
  drawText();
}

void serialEvent(Serial myPort) {
  data = myPort.readStringUntil('.');
  data = data.substring(0, data.length() - 1);
  index1 = data.indexOf(",");
  angle = data.substring(0, index1);
  distance = data.substring(index1 + 1, data.length());
  iAngle = Integer.parseInt(angle);
  iDistance = Integer.parseInt(distance);
}
