---
title: "최종 - 서보 모터로 조종되는 미로"
description: ""
coverImage: "/assets/img/2024-05-17-FinalServoMotorcontrolledLabyrinth_0.png"
date: 2024-05-17 19:23
ogImage: 
  url: /assets/img/2024-05-17-FinalServoMotorcontrolledLabyrinth_0.png
tag: Tech
originalTitle: "Final — Servo Motor controlled Labyrinth"
link: "https://medium.com/@ymoussa1242/final-servo-motor-controlled-labyrinth-ec8509b0bb19"
---


제 마지막 프로젝트에서는 게임을 만들고 싶었어요. 멀리 찾아보던 중에 이 튜토리얼을 발견했어요. 이 링크를 통해 프로젝트를 가이드받았지만, 최종적으로는 제가 독자적으로 만들었어요.

가장 먼저 한 일은 부품을 잘라내는 것이었어요. 프로젝트에 페인트를 칠하고 더 일관된 모양을 주고 싶었기 때문에 먼저 페인트를 칠하기로 결정했어요. 원래는 이 부품들을 3D 프린팅하고 싶었지만, 시간이 부족했기 때문에 다른 대신 판지 캔버스 재료로 이러한 부품을 자르기로 했어요. 자르는 일이 정말 지루했어요.

다음 단계는 많은 사고와 부품 이동이 필요했어요. 먼저 기본 부품을 묶어 붙인 다음, 더 잘 맞도록 부품을 잘라내고 다시 붙이면서 모든 부품을 함께 조립했어요.

다음 단계는 하드웨어를 조립하는 것이었어요. 원래는 Instructables에서 배선을 따랐지만, 전혀 운이 없었고 발표 날이 다가오고 있었어요. 아주 흔들거리는 전후 운동만 하는 것이었죠.

<div class="content-ad"></div>

발표 날에 Danne Woo 교수님께서 도와주셔서 전선을 고쳐주셔서 정말 감사했어요. 그리고 코드도 수정해야 했어요.

집에 도착하니 프로젝트가 대부분 부서져 있었어요. 다시 조립해야 했고 뜨거운 접착제만으로는 부족했어요.

이 구조물에 강도를 높일 방법을 고안하려고 노력했어요. 수업 중 교수님께서 측면에 추가 지지대를 권장했지만 전 아이디어를 내지 못했어요. 아빠가 바닥에 바인더 클립을 추가하는 것을 권유했고, 그것이 천재적인 아이디어인 줄 알았어요. 뜨거운 접착제가 실제로 달라붙을 수 있는 텍스처를 제공하고 벽이 스스로 서 있을 수 있도록 해주었어요.

<img src="/assets/img/2024-05-17-FinalServoMotorcontrolledLabyrinth_0.png" />

<div class="content-ad"></div>

구조를 수정한 후 Arduino를 연결했을 때 똑같이 잘못된 위치에서 시작해서 조이스틱이 왼쪽으로 돌아가지 않는 문제들이 계속 발생했어요. 먼저 제가 한 일은 제한을 제거하는 것이었어요. 조이스틱이 잘못 연결된 것 같아 시리얼 모니터를 확인했지만 제대로 작동 중이었어요. 실제 문제를 해결한 것은 모터를 분리하고 45도로 돌리고 다시 연결한 후에 이전처럼 이동할 수 있었던 것이 해결책이었어요.

```js
#include <Servo.h>
Servo myServoX;
Servo myServoY;
int ServoXPin = 8;
int ServoYPin = 9;
int ServoXHomePos = 190; //작은
int ServoYHomePos = 50; //큰
int ServoXPos = 190; //작은
int ServoYPos = 50; //큰
int XAxlePin = A0; //A0
int YAxlePin = A1; //A1
int XAxleValue = 0;
int YAxleValue = 0;
int Direction = 0;
int range = 12; // X나 Y 이동 범위
int center = range/2; // 휴식 위치 값
int threshold = range/2; // 휴식 임계값
void setup()
{
myServoX.attach(ServoXPin);
myServoY.attach(ServoYPin);
myServoX.write(ServoXPos);
myServoY.write(ServoYPos);
Serial.begin(9600);
}
void loop() {
    XAxleValue = readAxis(XAxlePin);
    YAxleValue = readAxis(YAxlePin);

    Serial.print("X: ");
    Serial.print(XAxleValue);
    Serial.print(" - Y: ");
    Serial.println(YAxleValue);

    int XSpeed = map(abs(XAxleValue), 0, range, 5, 1);
    int YSpeed = map(abs(YAxleValue), 0, range, 5, 1);

    if (XAxleValue > 0) {
        ServoXPos++;
        myServoX.write(ServoXPos);
        delay(XSpeed * (7 - XAxleValue));
    } else if (XAxleValue < 0) {
        ServoXPos--;
        myServoX.write(ServoXPos);
        delay(XSpeed * (7 + XAxleValue));
    }

    if (YAxleValue > 0) {
        ServoYPos++;
        myServoY.write(ServoYPos);
        delay(YSpeed * (7 - YAxleValue));
    } else if (YAxleValue < 0) {
        ServoYPos--;
        myServoY.write(ServoYPos);
        delay(YSpeed * (7 + YAxleValue));
    }

    delay(10);
}
int readAxis(int thisAxis) {
// 아날로그 입력을 읽음:
int reading = analogRead(thisAxis);
// 아날로그 입력 범위에서 출력 범위로 매핑:
reading = map(reading, 0, 1023, 0, range);
// 출력 읽기가 휴식 위치 임계값을 벗어나면 사용:
int distance = reading - center;
if (abs(distance) < threshold) {
distance = 0;
}
// 이 축의 거리를 반환:
return distance;
}
```

<img src="/assets/img/2024-05-17-FinalServoMotorcontrolledLabyrinth_1.png" />

최종 제품에 대해 매우 만족합니다. 즐기고 새로 사용한 부품들과 그에 딸려 온 함수들을 실험할 수 있었어요. 미래에는 이 수업에서 배운 모든 기술을 계속 활용하고 발전시키는 것을 좋아할 거예요. 배선, 코딩, 아날로그 입력, 납땜 같은 기술을 많이 배웠고, 이런 것들에 항상 관심이 있었어요. 너무 겁먹고 시도해보지 못했을 거예요. 그런데 이 수업을 듣고 나니 이런 것들과 작업할 수 있는 능력이 있는 것을 깨달았어요.