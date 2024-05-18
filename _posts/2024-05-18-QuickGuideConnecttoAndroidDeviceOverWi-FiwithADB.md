---
title: "빠른 안내 ADB를 사용하여 Wi-Fi를 통해 Android 기기에 연결하기"
description: ""
coverImage: "/assets/img/2024-05-18-QuickGuideConnecttoAndroidDeviceOverWi-FiwithADB_0.png"
date: 2024-05-18 15:27
ogImage: 
  url: /assets/img/2024-05-18-QuickGuideConnecttoAndroidDeviceOverWi-FiwithADB_0.png
tag: Tech
originalTitle: "Quick Guide: Connect to Android Device Over Wi-Fi with ADB"
link: "https://medium.com/@dds861/quick-guide-connect-to-android-device-over-wi-fi-with-adb-8355f483cb6a"
---


![이미지](/assets/img/2024-05-18-QuickGuideConnecttoAndroidDeviceOverWi-FiwithADB_0.png)

# 소개

이 안내서는 Android 기기와의 무선 연결을 위해 ADB(Android Debug Bridge)를 설정하는 방법을 보여줍니다. 개발 및 디버깅 프로세스를 간소화합니다. Pixel 기기를 Android Studio에 Wi-Fi를 통해 연결하는 데 어려움을 겪고 있는 경우, 이 안내서는 성공적인 연결을 설정하기 위한 명확한 단계와 팁을 제공하는 데 목적이 있습니다.

# 전제 조건

<div class="content-ad"></div>

- USB 디버깅이 활성화된 Android 기기.
- ADB가 설치된 컴퓨터.
- 두 기기가 동일한 Wi-Fi 네트워크에 연결되어 있어야 합니다.

# 연결 방법

- 초기 USB 연결: Android 기기를 컴퓨터에 USB로 연결하세요.
- 연결 확인: 터미널이나 명령 프롬프트를 열고 다음을 실행하세요:

```js
adb devices
```

<div class="content-ad"></div>

요청하신 내용은 아래와 같습니다. 기기의 시리얼 번호가 출력되므로 확인하실 수 있습니다:

```js
List of devices attached
fakeSerialNumber5g67kyd device
```

3. TCP/IP 모드로 전환하기: ADB를 5555 포트에서 TCP/IP로 수신대기 상태로 설정합니다:

```js
adb -s fakeSerialNumber5g67kyd tcpip 5555
```

<div class="content-ad"></div>

4. USB 케이블 연결 해제: 기기에서 USB 케이블을 안전하게 제거하세요.

5. Wi-Fi로 연결: Wi-Fi 설정에서 기기의 IP 주소를 찾아 연결하세요:

```js
adb connect 192.168.0.20:5555
```

# 결론

<div class="content-ad"></div>

안녕하세요! 이제 안드로이드 기기가 Wi-Fi로 연결되어 있으니 물리적 연결 없이도 손쉽게 ADB 명령을 실행할 수 있습니다. 이는 더 유연한 개발 환경을 제공하여 안드로이드 스튜디오에서 Pixel과 같은 기기를 사용할 때 연결 문제에 대한 추가적인 문제 해결이 필요한 경우에 이상적입니다.