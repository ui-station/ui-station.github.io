---
title: "Raspberry Pi Pico를 MicroPython으로 사용하여 MPU-6050을 어떻게 사용하는지 알아보기"
description: ""
coverImage: "/assets/img/2024-05-20-HowtouseanMPU-6050withaRaspberryPiPicousingMicroPython_0.png"
date: 2024-05-20 19:47
ogImage: 
  url: /assets/img/2024-05-20-HowtouseanMPU-6050withaRaspberryPiPicousingMicroPython_0.png
tag: Tech
originalTitle: "How to use an MPU-6050 with a Raspberry Pi Pico using MicroPython"
link: "https://medium.com/@timhanewich/how-to-use-an-mpu-6050-with-a-raspberry-pi-pico-using-micropython-cd768ea9268d"
---


2023년 여름에 MicroPython을 사용하여 Raspberry Pi Pico에서 실행되는 고유의 쿼드콥터 비행 컨트롤러를 처음부터 개발했어요. 이 프로젝트의 일환으로, 내 비행 컨트롤러는 기체에 탑재된 IMU(관성 측정 장치)에서 지속적으로 텔레메트리 데이터를 수집해야 했어요.

저는 MicroPython에서 MPU-6050 드라이버를 작성해 MPU-6050이라는 저렴한 3축 가속도계 및 자이로스코프를 I2C 프로토콜을 사용해 연결할 수 있도록 했어요. 제가 언급한 쿼드콥터 비행 컨트롤러 프로젝트를 위해 이를 작성했지만 이는 어떤 응용프로그램에서든 사용할 수 있기 때문에 해당 코드를 오픈소스로 공유하고 있어요.

온라인에서 찾을 수 있는 다른 드라이버들과 달리, 제 드라이버는 .py 파일 하나(한 모듈)뿐이며 매우 직관적으로 읽고 수정하고 사용할 수 있도록 설계되었으며 MPU-6050 텔레메트리 데이터를 네이티브 파이썬 값 유형(tuples)으로만 제공해요.

라즈베리 파이 Pico에서 MicroPython을 사용해 MPU-6050에서 텔레메트리를 수집하기 시작하는 기본 단계를 설명할게요:

<div class="content-ad"></div>

# 필요한 하드웨어

기본 예제를 위해 준비해야 할 것:

- 라즈베리 파이 Pico (Pico W도 가능)
- MPU-6050
- 브레드보드
- 수초 메스 투 수초 점퍼 와이어 4개
- 컴퓨터에 연결할 USB

# 단계 1: 배선

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-HowtouseanMPU-6050withaRaspberryPiPicousingMicroPython_0.png" />

- Raspberry Pi Pico를 브레드보드에 꽂습니다.
- MPU-6050을 Raspberry Pi Pico 바로 아래에 브레드보드에 꽂습니다.
- MPU-6050의 VCC 핀을 Raspberry Pi Pico의 VBUS 핀(핀 40)에 연결합니다.
- MPU-6050의 GND 핀을 Raspberry Pi Pico의 GND 핀 중 하나에 연결합니다(여기서는 핀 38을 사용하였습니다).
- MPU-6050의 SCL 핀을 Raspberry Pi Pico의 GP15 핀(핀 20)에 연결합니다.
- MPU-6050의 SDA 핀을 Raspberry Pi Pico의 GP14 핀(핀 19)에 연결합니다.

# 단계 2: MPU6050.py 모듈을 Pico에 업로드합니다.

아래는 MPU-6050을 위한 MicroPython 드라이버입니다.

<div class="content-ad"></div>

위의 코드 스니펫을 사용할 수 있지만이 코드의 업데이트는 내 GitHub의 MicroPython-Collection 저장소에서 확인할 수 있습니다.

라즈베리 파이 Pico에 이 코드 스니펫을 업로드하려면 아래 단계를 따라하세요:

- 위의 스니펫에서 파일을 다운로드하고 MPU6050.py로 컴퓨터에 저장합니다.
- 이미 MicroPython이 설치된 라즈베리 파이 Pico를 USB 케이블로 컴퓨터에 연결하세요. Thonny를 엽니다.
- 단계 1에서 다운로드한 MPU6050.py 파일을 파일 창에서 찾아 위쪽 오른쪽에 있는 파일 창에서 해당 파일을 찾아 마우스 오른쪽 버튼을 클릭한 후 /로 업로드하세요:

![image](/assets/img/2024-05-20-HowtouseanMPU-6050withaRaspberryPiPicousingMicroPython_1.png)

<div class="content-ad"></div>

4. 라즈베리 파이 Pico에 MPU6050.py 파일을 업로드한 후, 이제 Pico 디렉토리에 파일이 나타나는 것을 확인할 수 있습니다:

![MPU6050.py](/assets/img/2024-05-20-HowtouseanMPU-6050withaRaspberryPiPicousingMicroPython_2.png)

# 단계 3: 텔레메트리 캡처!

MPU6050.py 모듈을 Pico로 로드했으므로 이제 MPU-6050에서 텔레메트리 수집을 시작할 준비가 되었습니다! Thonny에서 새 코드 파일을 열려면 창의 왼쪽 상단에있는 새로 만들기 버튼을 클릭하십시오. 다음 테스트 코드를 붙여넣으세요:

<div class="content-ad"></div>

다음으로, 왼쪽 상단의 녹색 '현재 스크립트 실행' 버튼을 클릭하고 라즈베리 파이 Pico가 I2C를 사용하여 MPU-6050에서 텔레메트리를 읽기 시작하는 것을 관찰해보세요!