---
title: "아두이노로 만든 실내 식물 자동 관수 시스템"
description: ""
coverImage: "/assets/img/2024-05-23-SelfwateringsystemforindoorplantswithArduino_0.png"
date: 2024-05-23 16:41
ogImage: 
  url: /assets/img/2024-05-23-SelfwateringsystemforindoorplantswithArduino_0.png
tag: Tech
originalTitle: "Self watering system for indoor plants with Arduino"
link: "https://medium.com/@alexandrumarius/self-watering-system-for-indoor-plants-with-arduino-9d53ac8ed5e9"
---


여름이 빠르게 다가오고 휴가 시간이 가까워지면, 몇 주에 한 번 이웃에게 물 주라고 부탁하지 않고도 식물을 살려 놓는 해결책이 필요했죠.

그래서 나는 아마존에서 DIY 아두이노 기반 자동 관개 시스템과 아두이노 보드를 구입해 작업을 시작했어요.

부품 목록:
- DIY 관개 시스템
- 아두이노 UNO
- 전선 및 솔더

설치 위치에 따라 아두이노 보드와 펌프를 전원 공급하기 위해 USB 충전 블록도 필요할 수 있어요.

<div class="content-ad"></div>

# 배선 다이어그램

![다이어그램](/assets/img/2024-05-23-SelfwateringsystemforindoorplantswithArduino_0.png)

먼저, 센서를 물에 담그고 말리면 센서가 반환하는 값들을 확인하기 위해 캘리브레이션을 해야 합니다.

센서의 노란색 케이블을 보드의 A0에 연결하고 아래의 코드 스니펫을 사용하여 각 센서를 캘리브레이션하세요:

<div class="content-ad"></div>

```js
 void setup()
 { 
   Serial.begin(9600);
 }
 
 void loop()
 {
   Serial.println(analogRead(A0));
   delay(100);
 }
```

수중에 있을 때 550을 받았고, 건조할 때 190을 받았어요. 센서 자체에 영구 마커를 사용하여 이러한 값을 적는 것이 편리하다고 생각합니다. 각 센서에 대해 반복하세요.

![이미지](/assets/img/2024-05-23-SelfwateringsystemforindoorplantswithArduino_1.png)

각 센서는 토양 수분 퍼센트가 `20%` 미만인 식물에 물을 보내기 위해 펌프를 작동시키는 중계를 트리거합니다.

<div class="content-ad"></div>

아래는 코드이며 제 Github에서도 확인할 수 있습니다. 

```js
const int relayPins[] = {2, 3, 4, 5};
const int sensorPins[] = {A0, A1, A2, A3};
const int numSensors = sizeof(sensorPins) / sizeof(sensorPins[0]);
const int readingDelay = 2000;

void setup()
{
  Serial.begin(9600);
  for (int i = 0; i < numSensors; i++)
  {
    pinMode(relayPins[i], OUTPUT);
    pinMode(sensorPins[i], INPUT);
    Serial.print("Reading From Sensor ");
    Serial.print(i);
    Serial.println(" ...");
    delay(readingDelay);
  }
}

void loop()
{
  for (int i = 0; i < numSensors; i++)
  {
    int outputValue = analogRead(sensorPins[i]);
    Serial.print("Sensor ");
    Serial.print(i);
    Serial.print(" - Analog Moisture: ");
    Serial.println(outputValue);
    outputValue = map(outputValue, 550, 190, 0, 100);
    Serial.print("Sensor ");
    Serial.print(i);
    Serial.print(" - Moisture: ");
    Serial.print(outputValue);
    Serial.println("%");
    
    if (outputValue < 20)
    {
      digitalWrite(relayPins[i], LOW);
    }
    else
    {
      digitalWrite(relayPins[i], HIGH);
    }
    
    delay(1000);
  }
}
```

코드에서 주요한 부분은 수분이 많을 때(수중 - 550)와 건조할 때(공기 - 190)에 할당하는 값들입니다. 그런 다음 이러한 값을 0에서 100까지의 백분율로 매핑합니다. 그 후 보드는 센서를 반복하여 각 센서가 20% 미만인 경우 릴레이와 펌프를 트리거하기 시작합니다.

<div class="content-ad"></div>

케이블 관리는 나중에 하지만 첫 번째 수정으로는 매우 만족합니다.

시스템은 모니터링되고 성능이 평가될 것이며, 이 센서에 문제가 있는 다른 사용자들에 대해 온라인에서 읽었으므로 기다려보겠습니다.

이상적으로는 WIFI를 지원하는 버전의 보드로 업그레이드하고, Grafana에 연결하여 수분뿐만 아니라 온도, 습도 등과 같은 다른 측정 항목을 시간별로 모니터링하고 싶습니다.