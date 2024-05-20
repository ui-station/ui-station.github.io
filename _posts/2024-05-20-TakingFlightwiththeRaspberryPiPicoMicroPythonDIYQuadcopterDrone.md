---
title: "라즈베리 파이 Pico와 MicroPython으로 DIY 쿼드콥터 드론을 만들어봅시다"
description: ""
coverImage: "/assets/img/2024-05-20-TakingFlightwiththeRaspberryPiPicoMicroPythonDIYQuadcopterDrone_0.png"
date: 2024-05-20 19:54
ogImage: 
  url: /assets/img/2024-05-20-TakingFlightwiththeRaspberryPiPicoMicroPythonDIYQuadcopterDrone_0.png
tag: Tech
originalTitle: "Taking Flight with the Raspberry Pi Pico , MicroPython: DIY Quadcopter Drone"
link: "https://medium.com/@timhanewich/taking-flight-with-the-raspberry-pi-pico-micropython-diy-quadcopter-drone-61ed4f7ee746"
---


```markdown
![Quadcopter](/assets/img/2024-05-20-TakingFlightwiththeRaspberryPiPicoMicroPythonDIYQuadcopterDrone_0.png)

라즈베리 파이 피코는 라즈베리 파이 재단에서 처음으로 직접 설계한 마이크로컨트롤러인 RP2040을 장착한 매우 다재다능한 플랫폼으로 매우 저렴한 비용으로 구매할 수 있습니다!

스마트 홈 시스템, LED 조명 제어, 공기 질 센서 등과 같은 것들을 제어하는 데 라즈베리 파이 피코로 할 수 있는 일의 한계는 우리의 상상력뿐인 것 같습니다!

2023년 여름에 라즈베리 파이 피코를 "두뇌"로 사용하여 파이썬 기반의 사용자 정의된 비행 컨트롤러를 실행시키는 DIY 쿼드콥터 드론을 개발했습니다. 이 작업은 계산적으로 큰 도전이었지만, 많은 시행착오를 거치면서 RP2040에서 충분한 성능을 뽑아내어 안정적이고 기민한 비행을 가능하게 했습니다.
```

<div class="content-ad"></div>

처리 능력 뿐만 아니라, 소형이면서도 강력한 Raspberry Pi Pico는 쿼드콥터에 필요한 다양한 센서 및 구성 요소를 쉽게 통합할 수 있게 해주었습니다. GPIO 핀을 통해 다음과 같이 쉽게 통합할 수 있었어요:

- I2C 프로토콜을 통해 MPU-6050 가속도계 및 자이로스코프로부터 텔레메트리 읽기
- UART를 통해 기판 수신기로부터 무선통신으로 레이디오 명령 수신
- PWM을 통해 ESC를 통해 네 개의 독립된 모터 제어

# 12부작 시리즈

전 이 DIY 쿼드콥터 드론을 완전히 오픈소스로 공개했어요. '스카우트'라는 이 쿼드콥터 개발 과정을 문서화한 12부작 시리즈를 작성했어요. 아래 시리즈에서 스카우트의 각 조각에 대한 자세한 자습서를 찾을 수 있답니다. 이 시리즈는 개념 및 코드 설명, 샘플 코드, 배선 다이어그램, 안전 지침 등으로 구성된 완전한 내용을 제공해요.

<div class="content-ad"></div>

- 스카웃 비행 컨트롤러 소개
- 쿼드콥터 비행 역학
- 자이로스코프를 사용한 텔레메트리 캡처
- RC 수신기로 조종사 입력 수신
- PID 컨트롤러로 비행 안정화
- ESC 및 PWM을 사용한 브러시리스 모터 제어
- 쿼드콥터 하드웨어 설정
- 전체 비행 컨트롤러 코드 및 설명
- 비행 시작
- 끈기에 관한 한 가지 교훈
- 잠재적인 향후 개선점
- 보너스 코드

질문이 있으시면 Twitter/X에서 @TimHanewich로 연락해주세요!