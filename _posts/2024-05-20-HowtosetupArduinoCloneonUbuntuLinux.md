---
title: "아두이노 클론을 우분투 리눅스에 설정하는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-HowtosetupArduinoCloneonUbuntuLinux_0.png"
date: 2024-05-20 19:37
ogImage:
  url: /assets/img/2024-05-20-HowtosetupArduinoCloneonUbuntuLinux_0.png
tag: Tech
originalTitle: "How to set up Arduino Clone on Ubuntu Linux"
link: "https://medium.com/@samueladesola/setting-up-arduino-clone-on-ubuntu-linux-bca3feb061b1"
---

# 배경 이야기

기술적 설명서에 이야기나 이론을 많이 주는 것을 싫어하는 만큼, 이 에피소드의 배경 이야기는 상당히 재미있고 공유할 가치가 있다고 생각해요. 실례지만, 여러분은 읽을 필요가 없어요. 다음 섹션으로 바로 가시면 됩니다.

로봇 자동차를 만들려고 했는데, ROS2를 실행하기 위해 모터 컨트롤러가 필요했어요. Arduino Uno SMD 클론을 선택했는데, 그냥 사용하면 되리라고 생각했지만 그렇지 않았어요. 제 컴퓨터에는 platformIO를 사용했는데, 그게 문제인 줄 알았어요. 윈도우 컴퓨터로 전환하니 Arduino 보드에 스케치를 업로드할 수 있었어요. 그래서 남는 질문은, 왜 리눅스 머신에서는 작동하지 않는 걸까요?

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

온라인에서 다양한 해결책을 시도해보신 후에도 원하는 결과를 얻지 못하고 단 하나의 USB 포트인 /dev/ttyUSB0만 얻게 되었다면 상당히 답답하실 텐데요. 그만 이야기하고, 결국 문제를 해결하신다니 정말 다행입니다. 해결책은 아래와 같습니다.

## 해결책

BRLTTY는 리프레시 가능한 점자 디스플레이를 사용하는 시각장애인을 위한 Linux/Unix 콘솔(텍스트 모드)에 접근을 제공하는 백그라운드 프로세스(데몬)입니다. 이는 점자 디스플레이를 제어하고 완전한 화면 검토 기능을 제공합니다. 여러분이 이를 필요로 하지 않을 수도 있고 이를 제거하면 문제가 해결될 겁니다. 터미널을 열어서 다음 명령어를 입력해보세요:

```bash
sudo apt remove -y brltty
```

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

이제 Ubuntu 기기에서 Arduino Clone을 볼 수 있어요. 일반적으로 Arduino IDE나 PlatformIO를 사용할 때와 같이 포트를 선택하고 코드를 업로드할 수 있어요.

디바이스가 목록에 표시되었는지 확인하려면 이 명령을 입력할 수도 있어요:

```js
ls /dev/ttyUSB*
```

이게 저의 스크린샷이에요. 제 디바이스는 USB1이에요.

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

![How to set up Arduino Clone on Ubuntu Linux](/assets/img/2024-05-20-HowtosetupArduinoCloneonUbuntuLinux_1.png)
