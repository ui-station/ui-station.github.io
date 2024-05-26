---
title: "작은 인공지능을 활용한 자동차 번호판 인식"
description: ""
coverImage: "/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_0.png"
date: 2024-05-23 16:37
ogImage: 
  url: /assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_0.png
tag: Tech
originalTitle: "Car license plate recognition with TinyML"
link: "https://medium.com/arduino-engineering/car-license-plate-recognition-with-tinyml-e594f08ecacb"
---


## 엣지 임펄스와 아두이노를 이용해 엣지에서 클라우드까지

본 기사는 EU 및 스위스 차량 번호판을 감지하는 용도로 설계된 자동차 번호판 인식 시스템에 대한 자세한 소개를 제공합니다. 컴퓨터 비전과 머신 러닝 기술을 활용하여, 이 시스템은 번호판을 식별하고 분류하여 결과를 화면에 표시합니다. 해당 시스템은 차량 출입지역에서 차량의 출발지에 따라 적용되는 요금이 다른 유료 주차장 시나리오에 사용하기 위한 것입니다.

사용자들은 원격 웹 대시보드를 통해 주차 지역으로의 출입 횟수 통계를 확인할 수도 있습니다.

![차량 번호판 인식 시스템](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_0.png)

<div class="content-ad"></div>

# 어떻게 하나요?

이 시스템의 구현은 Arduino와 Edge Impulse를 사용하여 하드웨어, 소프트웨어 및 클라우드 서비스를 결합하는 과정을 포함합니다.

- Arduino는 사용하기 쉬운 하드웨어와 소프트웨어에 기반한 오픈 소스 플랫폼입니다. Arduino는 Arduino IoT Cloud라는 클라우드 서비스도 제공하는데, 이를 통해 장치의 손쉬운 연결, 데이터 저장 및 원격 관리가 가능합니다.
- Edge Impulse는 임베디드 장치용 머신 러닝 모델을 생성하고 배포하는 플랫폼입니다. 개발자들이 Arduino와 같은 하드웨어에 직접 머신 러닝 모델을 생성, 훈련 및 배포할 수 있도록 지원합니다.

![이미지](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_1.png)

<div class="content-ad"></div>

하드웨어 시스템은 세 가지 주요 노드로 구성되어 있습니다:

- Nicla Vision: 이미지 획득과 엣지 임퍼스로 생성된 ML 모델을 사용한 번호판 인식에 특화된 노드입니다. 이미지를 캡처하고 훈련된 머신 러닝 모델을 통해 처리합니다. 인식된 번호판 유형 데이터는 이후 Giga R1 WiFi 보드로 전송됩니다.
- Giga R1 WiFi: Nicla Vision 노드에서 데이터 수신을 처리하고 Arduino IoT Cloud와 통신하여 원격 대시보드를 관리합니다. 또한 로컬 대시보드 시각화를 위한 디스플레이를 제어합니다.
- Giga Display Shield: Giga R1 WiFi 보드에 연결된 디스플레이 구성 요소입니다. 로컬 대시보드의 시각화를 위한 인터페이스로 작동합니다.

![이미지](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_2.png)

![이미지](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_3.png)

<div class="content-ad"></div>

# 엣지 컴퓨팅에 관해...

엣지 컴퓨팅은 데이터를 원산지에 더 가까운 위치에서 처리하는 것을 의미하며, 원격 서버로 데이터를 보내서 처리하는 방식보다 더 빠르고 효율적입니다.

주차 관리 시스템에서는 Nicla Vision이 엣지 노드로 작동하여 이미지를 처리하고 로컬에서 머신러닝 모델을 실행합니다.
엣지 기기에서 머신러닝 모델을 실행하는 것을 TinyML이라고 합니다.

TinyML은 마이크로컨트롤러와 같은 작고 저전력 장치에 머신러닝 모델을 배포하는 것을 의미합니다.

<div class="content-ad"></div>

이 프로젝트의 ML 모델은 이미지를 입력으로 받아들이고 이를 EU 및 Swiss 두 가지 카테고리로 분류합니다.
이 작업은 이미지 분류로 알려져 있으며 지도학습(machine learning)의 범주에 속합니다. 지도학습은 각 입력이 해당하는 출력 레이블과 관련된 레이블이 있는 데이터 집합을 기반으로 모델을 교육하는 것을 포함합니다.

![이미지](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_4.png)

## ML 모델을 어떻게 구축할까요?

Tiny Machine Learning 모델을 구축하는 과정은 4단계로 이루어집니다:

<div class="content-ad"></div>

- 데이터 수집
- 데이터 전처리
- 모델 학습
- 엣지에 배포

![image](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_5.png)

## OpenMV 및 Edge Impulse를 사용한 데이터 수집

이 단계에서는 기계 학습 모델을 훈련시키기 위한 관련 데이터를 수집합니다. 이 프로젝트의 경우 OpenMV를 사용하여 EU 및 스위스의 번호판이 포함된 이미지 세트를 수집하는 것을 포함합니다.

<div class="content-ad"></div>

OpenMV는 머신 비전 작업에 디자인된 마이크로컨트롤러 기반 플랫폼으로, 다양한 카메라 센서와 MicroPython을 실행할 수 있습니다. 엣지 디바이스에서 이미지를 캡처하는 간단하고 효율적인 솔루션을 제공합니다.

![이미지](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_6.png)

OpenMV를 사용하면 빠르게 MicroPython 스크립트를 만들어 다양한 이미지 데이터셋을 효율적으로 생성할 수 있습니다.

![이미지](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_7.png)

<div class="content-ad"></div>

## Edge Impulse

이전에 설명했듯이, Edge Impulse는 개발자가 모든 단계에서 도움을 받으며 TinyML 모델을 생성하는 과정을 완전히 지원합니다.

![image](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_8.png)

## 데이터 수집 및 전처리

<div class="content-ad"></div>

OpenMV는 Edge Impulse와 통합하여 데이터 수집 프로세스를 더욱 간단하게 만들어줍니다. 이를 통해 외부 도구가 필요하지 않고 이미지 데이터셋을 직접 업로드할 수 있습니다.

![이미지](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_9.png)

데이터 수집 후에는 특성 추출을 수행하는 것이 중요합니다. 이는 원시 데이터가 특성 집합으로 변환되는 과정입니다.

머신 러닝에서 특성은 특정 작업을 해결하는 데 관련이 있는 원시 데이터에서 추출된 특정한 특징을 가리킵니다. 이미지 처리 문제에서는 이미지에 존재하는 가장자리, 질감, 모양 또는 패턴과 같은 특성이 포함될 수 있습니다. Edge Impulse는 이미지에서 자동으로 특성을 추출할 수 있어 이를 통해 데이터 내에서 가장 관련성이 높은 정보를 식별하고 집중함으로써 모델 학습 프로세스를 간소화할 수 있습니다.

<div class="content-ad"></div>

## 모델 훈련

모델 훈련은 사전 처리된 데이터를 기계 학습 알고리즘에 공급하여 입력 특성(플레이트의 이미지 특성)과 출력 레이블(EU 또는 Swiss) 간의 관계를 학습하는 과정을 말합니다.

Edge Impulse는 미리 제작된 모델 아키텍처 또는 사용자 정의 모델 아키텍처를 선택하고, 훈련 주기와 학습률과 같은 모델 설정을 조정할 수 있는 기능을 제공합니다.

![Image](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_10.png)

<div class="content-ad"></div>

모델의 효과성은 정확도 지표(F1 점수)를 사용하여 평가되며, 이는 모델이 예측에서 얼마나 정확한지를 나타냅니다.

첫 번째 시도에서 우리는 72%의 정확도를 달성했지만 아직 허용할만한 수준이 아닙니다. F1 점수를 향상시키려면, 훈련 주기를 늘리거나 모델 아키텍처를 변경하거나 데이터셋의 품질을 개선할 수 있습니다(잡음을 줄이고, 서로 다른 배경을 처리하고, 정확하게 라벨을 지정하고, 양을 늘리는 등).

![이미지](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_11.png)

## 배포

<div class="content-ad"></div>

Edge Impulse는 하드웨어에 독립적이므로 모델을 어떤 엣지 장치에도 배포할 수 있어요.

![image1](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_12.png)

대부분의 아두이노 보드와 호환되는 아두이노 라이브러리를 빌드하고 다운로드할 수 있어요. 모델은 .c 및 .h 파일로 변환되어 /src 디렉토리에 저장되며, /examples 디렉토리에 다양한 예제도 포함돼요.

![image2](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_13.png)

<div class="content-ad"></div>

## The Edge sketch

Nicla Vision에서 실행되는 스케치의 주요 기능은 다음과 같습니다:

- 내장 카메라에서 이미지 획득.
- 라이선스 플레이트 클래스를 결정하기 위해 머신 러닝 모델 실행.
- 처리 결과를 시리얼 통신을 통해 로컬 및 원격 대시보드를 관리하는 노드에 전송합니다.

```js
// Edge Impulse 모델 라이브러리 포함
#include <eu-swiss-plate-recognition_inferencing.h>

void setup()
{
  // ...
  // 내장 카메라 초기화
  if (ei_camera_init() == false) {
      ei_printf("Failed to initialize Camera!\r\n");
      while(1) ;
  }
  // ...
  // 대시보드 노드와의 시리얼 통신 초기화
  Serial1.begin(115200);
}

void loop()
{
  // ...
  // 내장 카메라에서 이미지 획득
  if (ei_camera_capture(...) == false) {
      ei_printf("Failed to capture image\r\n");
      return;
  }

  // ...
  // ML 모델 실행
  ei_impulse_result_t result = { 0 };
  EI_IMPULSE_ERROR err = run_classifier(&signal, &result, debug_nn);

  // ...
  // 예측 결과 파싱
  if (strcmp(..., "swiss") == 0) {
      pred_result = 'S';
      break;
  } else if(strcmp(..., "eu") == 0) {
      pred_result = 'E';
      break;
  }
  
  // ...
  // 처리 결과를 시리얼을 통해 대시보드 노드로 전송
  Serial1.print(pred_result);
}
```

<div class="content-ad"></div>

# …클라우드로

아두이노 IoT 클라우드는 아두이노의 클라우드에서 IoT 프로젝트를 개발하기 위한 솔루션입니다. 이를 통해 사용자는 장치에서 보낸 데이터를 저장하고 검색하여 실시간 모니터링과 제어를 할 수 있습니다.

알림 기능을 제공하며 사전 구축된 위젯을 사용하여 제어 대시보드를 만들기 위한 사용자 친화적 인터페이스를 제공하여 데이터 시각화 및 관리를 간소화합니다.

이 플랫폼은 아두이노 보드 및 ESP32과 같은 제3자 장치를 포함한 다양한 장치와 호환됩니다.

<div class="content-ad"></div>

## 클라우드 스케치

아두이노 IoT 클라우드 플랫폼에서는 프로젝트 설계 중 사용자가 지정한 구성 및 설정에 기초하여 장치를 위한 스케치가 자동으로 생성됩니다.

```js
// 클라우드 변수 및 네트워크 인증 정보를 포함한 파일
#include "thingProperties.h" 

void setup() {
  // ...
  // thingProperties.h에서 정의된 속성 초기화
  initProperties();

  // 아두이노 IoT 클라우드에 연결
  ArduinoCloud.begin(ArduinoIoTPreferredConnection);
  // ...
}

void loop() {
  ArduinoCloud.update();  // 클라우드 변수와 통신 업데이트
  // ...
  if (Serial1.available()) {
    char inByte = Serial1.read(); // 에지 노드에서 오는 결과 읽기
    
    if (inByte == 'E') {
      region = REGION_EU; // 'region'은 클라우드 변수입니다
      // ...
    } else if (inByte == 'S') {
      region = REGION_SWISS;
      // ...
    }
    // ...
  }
  // ...
}
```

## 대시보드

<div class="content-ad"></div>

아두이노 IoT 클라우드는 사용자가 쉽게 사용할 수 있는 위젯을 활용하여 직관적인 대시보드를 생성할 수 있는 기능을 제공합니다. 사용자들은 이러한 위젯을 장치에서 사용되는 클라우드 변수에 쉽게 연결할 수 있어 데이터 시각화와 관리 과정을 간편화할 수 있습니다.

![image](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_14.png)

![image](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_15.png)

# GUI 소개

<div class="content-ad"></div>

Giga Display Shield로 관리되는 GUI는 로컬 대시보드인 인식된 자동차 번호판 유형을 보여줍니다. 디스플레이 관리는 Arduino_GigaDisplay_GFX 라이브러리에 의해 제어됩니다.

Arduino_GigaDisplay_GFX는 Adafruit_GFX 라이브러리 위에 구축된 라이브러리입니다. 개별 픽셀, 선, 사각형 및 기타 기하학적 모양을 그리는 기능을 제공합니다. 또한 숫자 값 및 문자열을 출력하는 기능을 지원합니다.

```cpp
#include "Arduino_GigaDisplay_GFX.h"

GigaDisplay_GFX display;

void setup() {
  // ...
  display.begin();
  display.setRotation(1);
  // ...
}

void loop() {
  if (Serial1.available()) {
    // ...
    if (inByte == 'E') {
      // ...
      drawEUFlag();
    } else if (inByte == 'S') {
      // ...
      drawSwissFlag();
    }
    // ...
  }
}

void drawEUFlag() {
  display.fillScreen(BLUE);
  display.setCursor(350, 400);
  display.setTextSize(10);
  display.print("EU");

  for (int i = 0; i < 12; i++) {
    drawStar(CENTER_X + (UE_FLAG_RADIUS * cos(i * PI / 6)), CENTER_Y + (UE_FLAG_RADIUS * sin(i * PI / 6)));
  }
}

void drawStar(uint x, uint y) {
  display.setCursor(x, y);
  display.setTextColor(YELLOW);
  display.setTextSize(3);
  display.print("*");
  display.setTextColor(WHITE);
}

void drawSwissFlag() {
  display.fillScreen(RED);
  display.setCursor(350, 400);
  display.setTextSize(10);
  display.print("CH");

  display.fillRect(CENTER_X - SWISS_FLAG_THICKNESS / 2, CENTER_Y - SWISS_FLAG_LENGTH / 2, SWISS_FLAG_THICKNESS, SWISS_FLAG_LENGTH, WHITE);
  display.fillRect(CENTER_X - SWISS_FLAG_LENGTH / 2, CENTER_Y - SWISS_FLAG_THICKNESS / 2, SWISS_FLAG_LENGTH, SWISS_FLAG_THICKNESS, WHITE);
}
```

![Car License Plate Recognition with TinyML](/assets/img/2024-05-23-CarlicenseplaterecognitionwithTinyML_16.png)


<div class="content-ad"></div>

# 참고 자료

- 코드:
https://github.com/csarnataro/arduino-tinyml-plate-recognition
- 이탈리안 임베디드 이벤트: https://www.italianembedded.com/events/arduino-ml/