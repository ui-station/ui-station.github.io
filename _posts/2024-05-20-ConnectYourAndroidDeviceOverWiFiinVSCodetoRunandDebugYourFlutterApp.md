---
title: "안녕하세요 VSCode에서 플러터 앱을 실행하고 디버그하기 위해 안드로이드 기기를 WiFi로 연결하는 방법에 대해 이야기하겠습니다"
description: ""
coverImage: "/assets/img/2024-05-20-ConnectYourAndroidDeviceOverWiFiinVSCodetoRunandDebugYourFlutterApp_0.png"
date: 2024-05-20 16:19
ogImage:
  url: /assets/img/2024-05-20-ConnectYourAndroidDeviceOverWiFiinVSCodetoRunandDebugYourFlutterApp_0.png
tag: Tech
originalTitle: "Connect Your Android Device Over WiFi in VSCode to Run and Debug Your Flutter App"
link: "https://medium.com/@quedicesebas/connect-your-android-device-over-wifi-in-vscode-to-run-and-debug-your-flutter-app-480adbeb85fa"
---

<img src="/assets/img/2024-05-20-ConnectYourAndroidDeviceOverWiFiinVSCodetoRunandDebugYourFlutterApp_0.png" />

# 핸드폰 배터리 손상하지 말고 앱 테스트, 케이블 없이 디버깅하세요 (거의).

이 게시물은 이미 USB 연결로 Android 기기에서 실행 및 디버깅을 구성했다고 가정합니다. Android 13 기기 및 Windows 10 컴퓨터에서 테스트되었습니다. 컴퓨터와 휴대폰 모두 동일한 네트워크에 연결되어 있어야 합니다.

## 일회성 IDE 및 장치 구성

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- [Optional] VSCode 확장 프로그램인 ADB Commands를 설치하세요. 안 깔았다면 adb 명령어를 사용할 수 있어요. 확장 프로그램을 사용하는 편이 더 직관적이고 설정할 게 거의 없어요.
- 기기에서 무선 디버깅을 활성화하세요:
  - 설정으로 이동해서 “debug”를 검색하세요.
  - “무선 디버깅”을 탭한 후, 다시 “무선 디버깅” 옵션을 (토글이 아닌 전체 항목을 탭해서 상세 페이지로 이동하세요.
  - “무선 디버깅 사용” 토글을 확인하세요. “이 네트워크에서 무선 디버깅 허용” 팝업에서 “이 네트워크에서 항상 허용”을 선택하고 “허용”을 탭하세요.
  - “IP 주소 및 포트 정보”는 나중에 예약하세요 (대부분의 튜토리얼은 포트가 5555이라고 가정하지만, 제 브랜드 new Pixel 7에서는 다른 포트번호였어요).

![Android Device](/assets/img/2024-05-20-ConnectYourAndroidDeviceOverWiFiinVSCodetoRunandDebugYourFlutterApp_1.png)

## 기기 연결

- [처음 연결할 때만] USB로 기기를 연결하세요.
- VSCode에서 Ctrl+Shift+P를 누르고 “ADB:📱 Connect to device IP”를 검색하고 선택하세요:
- IP 주소를 입력하고 엔터를 누르세요.
- 포트 번호를 입력하세요.
- “Connected to `IP`:`port`” 메시지가 나타나면 USB 케이블을 분리할 수 있어요. \*참고: IP 주소는 사용하는 네트워크와 라우터 설정에 따라 달라질 수 있어요. 포트 번호는 매번 바뀔 수 있어요. 이렇게 되면 “ADB returned null value”나 다른 오류가 발생할 수 있어요.
- VSCode에서 F5를 누르거나 "Run > Start Debugging"으로 이동하세요. 즐겁게 개발하세요!
