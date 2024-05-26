---
title: "ESP32 내장 LED 깜빡이기"
description: ""
coverImage: "/assets/img/2024-05-23-ESP32Built-inLEDBlinking_0.png"
date: 2024-05-23 16:41
ogImage:
  url: /assets/img/2024-05-23-ESP32Built-inLEDBlinking_0.png
tag: Tech
originalTitle: "ESP32 Built-in LED Blinking"
link: "https://medium.com/@adihendro/esp32-built-in-led-blinking-4dd0b50264a"
---

이것은 ESP32를 사용하는 간단한 프로젝트입니다.

ESP32는 Espressif Systems에서 만들고 개발한 마이크로컨트롤러입니다.

우리는 Arduino IDE에서 예제 코드를 사용하여 ESP32 기본 LED를 깜빡이게 할 것입니다.

# 요구 사항.

<div class="content-ad"></div>

ESP32 DEVKIT V1 보드

![이미지](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_0.png)

Arduino IDE

![이미지](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_1.png)

<div class="content-ad"></div>

PC/Laptop

![PC/Laptop image](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_2.png)

Micro USB to USB cable

![Micro USB to USB cable image](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_3.png)

<div class="content-ad"></div>

# 단계

단계 1: 파일`예` 01.기초`Blink`에서 Arduino IDE에서 블링크 기본 프로젝트를 엽니다.

![이미지](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_4.png)

단계 2: Micro USB에서 USB 케이블을 사용하여 ESP32 보드를 컴퓨터에 연결합니다.

<div class="content-ad"></div>

![이미지](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_5.png)

단계 3: Tools`Board에서 DOIT ESP32 DEVKIT V1으로 보드 구성 변경.

![이미지](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_6.png)

단계 4: Tools`Port에서 /dev/cu.SLAB_USBtoUART로 포트 구성 변경.

<div class="content-ad"></div>


![Step 5: Click Verify to compile the project.](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_7.png)

![Image 1:](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_8.png)

![Image 2:](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_9.png)


<div class="content-ad"></div>

### 단계 6: ESP32에서 코드를 실행하려면 업로드를 클릭하세요.

![이미지](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_10.png)

![이미지](/assets/img/2024-05-23-ESP32Built-inLEDBlinking_11.png)

![이미지](https://miro.medium.com/v2/resize:fit:736/1*vZwPRq3B-OKM5kZGDdDSXg.gif)

<div class="content-ad"></div>

7단계: 내장 LED는 1초 간격으로 깜박입니다.

![LED](https://miro.medium.com/v2/resize:fit:674/1*I-96rdV6d-PjL3PBMiFGlA.gif)

감사합니다!
