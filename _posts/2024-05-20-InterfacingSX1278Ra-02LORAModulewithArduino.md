---
title: "아두이노와 SX1278 Ra-02 LORA 모듈 연결하기"
description: ""
coverImage: "/assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_0.png"
date: 2024-05-20 19:36
ogImage: 
  url: /assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_0.png
tag: Tech
originalTitle: "Interfacing SX1278 (Ra-02) LORA Module with Arduino"
link: "https://medium.com/@shathiralakdilu/interfacing-sx1278-ra-02-lora-module-with-arduino-a02b7b520fc7"
---


# LoRa란 무엇인가요?

LoRa는 채프 확산 스펙트럼(CSS) 기술을 기반으로 한 무선 변조 방법입니다. 이 기술은 라디오 파동에 데이터를 인코딩하여 전파펄스를 통해 전송하며, 돌고래와 박쥐가 의사 소통하는 방식과 유사합니다. LoRa 기술은 소량의 전력을 소비하면서 장거리로 양방향 정보를 전송하는 데 사용할 수 있습니다.

## LoRaWAN

LoRaWAN은 Semtech의 LoRa 변조 체계를 사용하는 포인트 투 멀티포인트 네트워킹 프로토콜입니다. 이는 LoRa 변조를 기반으로 구축된 미디어 액세스 제어(MAC) 레이어 프로토콜입니다.

<div class="content-ad"></div>

로라 프로토콜은 기본적으로 두 개의 LoRa 모듈 간 통신을 지원하지는 않지만, "라디오헤드 패킷 메소드" 기술을 통해 LoRa 프로토콜을 준수하면서 두 개의 LoRa 모듈 간 통신이 가능합니다.

"라디오헤드"는 패킷 라디오 라이브러리로서 미세한 마이크로프로세서들을 위한 완전한 객체 지향 라이브러리로서 패킷화된 메시지를 송수신하기 위한 기능을 제공합니다. 

# 아두이노와 LoRa SX1278

![LoRa SX1278 with Arduino](/assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_0.png)

<div class="content-ad"></div>

## LoRa SX1278 모듈

여기서 사용 중인 LoRa 모듈은 433MHz에서 작동하는 SX1278 Ra-02입니다.

![이미지](/assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_1.png)

## Arduino LoRa SX1278 송신기

<div class="content-ad"></div>

아두이노 LoRa SX1278 송신기의 회로도가 아래에 제시되어 있습니다. LoRa SX1278은 5V와 호환되지 않으므로 5V를 공급하지 마세요. 그렇게 하면 보드가 손상될 수 있습니다. VCC 핀에 연결하려면 아두이노의 3.3V를 사용하세요.

![아두이노 LoRa SX1278 송신기 회로도](/assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_2.png)

![아두이노 LoRa SX1278 송신기 회로도](/assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_3.png)

## 아두이노 LoRa SX1278 수신기

<div class="content-ad"></div>

아두이노 LoRa SX1278 수신기의 회로도가 아래에 제공되었습니다. OLED 디스플레이가 추가로 연결되어 수신된 값을 확인할 수 있습니다.

![회로도](/assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_4.png)

![회로도](/assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_5.png)

## 아두이노 IDE 준비

<div class="content-ad"></div>

하드웨어 설정을 완료한 후 다음 단계는 아두이노 IDE를 활용하는 것입니다. LoRa 모듈과 상호 작용할 때 Sandeep Mistry에 의해 개발된 포괄적인 LoRa 라이브러리를 사용할 수 있습니다.

라이브러리를 추가하려면 아두이노 IDE를 열고 스케치 - 라이브러리 포함 - 라이브러리 관리를 따르세요.

<img src="/assets/img/2024-05-20-InterfacingSX1278Ra-02LORAModulewithArduino_6.png" />

## 송신기 코드

<div class="content-ad"></div>

```markdown
#include <SPI.h>
#include <LoRa.h>

int counter = 0;

void setup() {
  Serial.begin(9600);
  while (!Serial);

  Serial.println("LoRa Sender");

  if (!LoRa.begin(433E6)) {
    Serial.println("Starting LoRa failed!");
    while (1);
  }
  LoRa.setTxPower(20);
}


void loop() {

  Serial.print("Sending packet: ");
  Serial.println(counter);
  // send packet
  LoRa.beginPacket();
  LoRa.print("hello ");
  LoRa.print(counter);
  LoRa.endPacket();
  counter++;
  delay(5000);

}
```

## Receiver Code

```markdown
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <SPI.h>
#include <LoRa.h>

#define SCREEN_WIDTH 128 // OLED display width, in pixels
#define SCREEN_HEIGHT 64 // OLED display height, in pixels

// Declaration for an SSD1306 display connected to I2C (SDA, SCL pins)
#define OLED_RESET     -1 // Reset pin # (or -1 if sharing Arduino reset pin)
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

String inString = ""; //hold incoming characters
String myMessage = ""; // hold compleye message
int led = 3;

void setup() {
  Serial.begin(9600);

  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) { // Address 0x3D for 128x64
    Serial.println(F("SSD1306 allocation failed"));
    for(;;);
  }
  delay(2000);
  display.clearDisplay();
  display.display(); 

  while (!Serial);
  Serial.println("LoRa Receiver");

  display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.setCursor(20, 30);
    // Display static text
    display.println("LoRa Receiver");
    display.display();

  if (!LoRa.begin(433E6)) {
    Serial.println("Starting LoRa failed!");
    while (1);
  }
  pinMode(led, OUTPUT);
}

void loop() {
   String message = "";
  // try to parse packet
  int packetSize = LoRa.parsePacket();
  if (packetSize) {

    Serial.print("Received packet '");
    
    digitalWrite(led, HIGH);
    delay(1000);
    display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.setCursor(0, 10);
    // Display static text
    display.println("Received packet - ");
    display.display();

    Serial.println(packetSize);
 
    // read packet
    while (LoRa.available()) {
   
    // Serial.print((char)LoRa.read());
     
    message += (char)LoRa.read();
   
    }

    // display.clearDisplay();
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.setCursor(20, 30);
    // Display static text
    display.println(message);
    display.display();
    Serial.print(message);

    // print RSSI of packet
    Serial.print("' with RSSI ");
    Serial.println(LoRa.packetRssi());
    digitalWrite(led, LOW);
  }
}
```