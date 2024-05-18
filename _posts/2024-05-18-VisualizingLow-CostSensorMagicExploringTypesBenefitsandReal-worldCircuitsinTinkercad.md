---
title: "저렴한 가격의 센서 마법 시각화 틴커캐드에서의 다양한 종류, 혜택 및 실세계 회로 탐구"
description: ""
coverImage: "/assets/img/2024-05-18-VisualizingLow-CostSensorMagicExploringTypesBenefitsandReal-worldCircuitsinTinkercad_0.png"
date: 2024-05-18 18:55
ogImage: 
  url: /assets/img/2024-05-18-VisualizingLow-CostSensorMagicExploringTypesBenefitsandReal-worldCircuitsinTinkercad_0.png
tag: Tech
originalTitle: "Visualizing Low-Cost Sensor Magic: Exploring Types ,Benefits and Real-world Circuits in Tinkercad"
link: "https://medium.com/@rashmiabeysekera3/visualizing-low-cost-sensor-magic-exploring-types-benefits-and-real-world-circuits-in-tinkercad-a8026c16e315"
---


고가의 센서 비용에 고민 중이신가요? 비싼 센서는 잊고, 저렴한 센서가 하드웨어 분야를 혁신하고 있다는 사실을 알아보세요.

그래서 아두이노와 함께 사용할 수 있는 센서에 대해 논의해보고, 선택한 센서에 대한 예제 코드도 보여드릴 거에요. 이는 초보자들에게 도움이 될 거예요.

그럼, 센서가 될 수 있는 것은 무엇일까요? 셋팅 버튼조차도 뭔가를 전송할 수 있고, 버튼을 눌렀는지 여부를 감지할 수 있습니다. 그것은 매우 명백하죠. 하지만 우리는 보통 셋팅 버튼을 센서라고는 부르지 않아요.

센서는 주변 상황에 따라 정보나 데이터를 제공하는 장치 자체입니다.

<div class="content-ad"></div>

일부는 디지털 출력 또는 아날로그 출력이 있습니다.

다양한 저렴한 센서 기술의 데이터 품질을 결정할 때 고려해야 할 중요한 매개 변수는 정확도, 신뢰성 및 안정성입니다. (센서가 사용된 후 일정 기간 동안 성능을 유지하는 능력을 안정성이라고 합니다)

비용 절감 센서를 선택하면 여러 이점이 있습니다.

1. 예산을 고려한 친화적인 센서는 학생들이 기술을 탐험하는 데 도움이 됩니다.

<div class="content-ad"></div>

2. 가격 부담을 줄이고 누구나 실용적인 기술을 개발할 수 있습니다.

3. 새 제품의 시장 진입 시간을 단축합니다.

4. 그 중 많은 제품이 다재다능하고 적응력이 높습니다.

# 토양 수분 센서

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경해 주세요.

<div class="content-ad"></div>

환경 온도를 측정하려면 금속의 전기적 행동의 차이를 이용하여 온도를 전압으로 변환하세요.

**PIR 센서**

변화를 감지하여 움직임을 감지합니다.

**적외선 거리 센서**

<div class="content-ad"></div>

IR 빔을 사용하여 객체까지의 거리를 측정하세요.

## 초음파 센서

타겟과 센서 사이의 거리를 측정하세요.

아두이노 보드에 센서들을 연결하고 창의적인 하드웨어 프로젝트를 만들 수 있어요. 여기서 Tinkercad를 사용하여 디자인한 회로 스크린샷을 공유하려고 해요.

<div class="content-ad"></div>

```markdown
![Image 0](/assets/img/2024-05-18-VisualizingLow-CostSensorMagicExploringTypesBenefitsandReal-worldCircuitsinTinkercad_0.png)

![Image 1](/assets/img/2024-05-18-VisualizingLow-CostSensorMagicExploringTypesBenefitsandReal-worldCircuitsinTinkercad_1.png)

```js
int ultra = 0;

long readUltrasonicDistance(int triggerPin, int echoPin)
{
  pinMode(triggerPin, OUTPUT);  // Clear the trigger
  digitalWrite(triggerPin, LOW);
  delayMicroseconds(2);
  // Sets the trigger pin to HIGH state for 10 microseconds
  digitalWrite(triggerPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(triggerPin, LOW);
  pinMode(echoPin, INPUT);
  // Reads the echo pin, and returns the sound wave travel time in microseconds
  return pulseIn(echoPin, HIGH);
}

void setup()
{
  pinMode(10, OUTPUT);
  pinMode(11, OUTPUT);
  pinMode(12, OUTPUT);
  pinMode(6, OUTPUT);
}

void loop()
{
  ultra = 0.01723 * readUltrasonicDistance(3, 2);
  if (ultra < 150) {
    digitalWrite(10, HIGH);
  } else {
    digitalWrite(10, LOW);
  }
  if (ultra < 100) {
    digitalWrite(11, HIGH);
  } else {
    digitalWrite(11, LOW);
  }
  if (ultra < 50) {
    digitalWrite(12, HIGH);
    digitalWrite(6, HIGH);
  } else {
    digitalWrite(12, LOW);
    digitalWrite(6, LOW);
  }
  delay(10); // Delay a little bit to improve simulation performance
}

```

Run this code and see the amazing result!!!
```

<div class="content-ad"></div>

이 센서 뿐만 아니라 아래 링크를 사용하여 이와 같은 회로를 만드는 방법을 배울 수 있습니다. 그러니 기다리지 마세요....지금 시도해보세요....

이 새롭게 얻은 지식과 열정을 앞으로의 프로젝트에 이어나가 봅시다!!!

이 여정에 참여해 주셔서 감사합니다. 즐거운 하루 되세요!!!