---
title: "16진수 디스플레이로 다양한 진법 탐험하기"
description: ""
coverImage: "/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_0.png"
date: 2024-05-23 16:55
ogImage:
  url: /assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_0.png
tag: Tech
originalTitle: "Exploring Different Number Bases With a Hexadecimal Display"
link: "https://medium.com/@russelleveleigh/exploring-different-number-bases-with-a-hexadecimal-display-d1d2c726263b"
---

내 Hexadecimal 표시는 Raspberry Pi Pico W 프로젝트로, Neopixel RGB LED 및 Micropython을 사용하여 실제로 카운트할 때 어떤 일이 벌어지는지 보여줍니다.

## 목적 및 디자인 선택

이 프로젝트에는 세 가지 주요 목표가 있었습니다:

- 서로 다른 숫자 베이스에서 카운트하는 경우에 무엇이 발생하는지 보여주기 위해 주소 지정 LED (NeoPixels)를 사용하여 디스플레이 구축.
- 6자리 16진수 색상 코드를 생생하게 만들어서 주어진 16진수 코드에 대한 색상이 벽에서 반사되도록 하는 것.
- Raspberry Pi Pico W를 사용하여 작은 웹 서버를 호스팅하여 디스플레이를 제어하고 사용자가 제공한 데이터가 표시되는 내용에 영향을 줄 수 있도록 함.

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

## 열 개에 대한 대안

우리는 0부터 9까지의 숫자로 계산하고 표현하는 소수 세계에 살고 있습니다. 숫자가 배치된 위치에 따라 숫자가 다른 의미를 갖는다는 사실을 이해합니다. 그래서 숫자 1849에서 우리는 숫자 1이 1000을 의미하고, 8은 800을, 4는 40을, 그리고 9는 9개를 의미한다는 것을 알고 있습니다. 어떤 위치에서든 9까지 세면 공간이 부족해지고 다음 열이 필요합니다. 이것을 10진수라고 부르는데 이는 10개의 숫자가 있기 때문입니다.

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*FYV5OPPBmrceJd0d4lIp9A.gif)

각 열은 이전 열보다 10배 더 가치가 있으며, 10의 거듭제곱에 증가하는 자리 값이 있습니다.

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

숫자를 이렇게 표현하는 것은 당연한 것처럼 느껴지며, 마치 자연의 법칙인 것처럼 보입니다. 찰스 펫졸드는 자신의 책 '코드: 컴퓨터 하드웨어와 소프트웨어의 숨겨진 언어'에서 이 느낌을 잘 설명합니다.

만약 8개의 손가락을 가진 만화 캐릭터라면, 우리는 7 이후의 각 자리에 대한 기호가 부족해지고 이렇게 세어 나갈 것입니다: 1, 2, 3, 4, 5, 6, 7, 10! 이것이 8진수 체계이며, 기수 8입니다. 여기서 각 열은 8의 거듭제곱을 나타냅니다.

찰스 펫졸드는 이 아이디어를 계속 확장하여 2개의 지느러미만을 가진 돌고래가 어떻게 세어 나갈지도 고려합니다.

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

이렇게 세어주는 돌고래가 있다면 0, 1, 10, 11로 세겠죠. 여기서 각 열은 2의 거듭제곱을 나타냅니다.

![image](https://miro.medium.com/v2/resize:fit:1400/1*1ClifUAqF-Tgv91PW9Mgrw.gif)

물론 두 자릿수만으로 세상을 통제하는 것에 이익을 보는 것은 돌고래뿐만이 아닙니다. 현대 컴퓨팅은 결국 2진법 덕분에 가능합니다. 전기 연결이 전류를 통과하는 경우(1) 또는 그렇지 않은 경우(0)이기 때문이죠. 스위치의 다양한 조합과 부울 대수의 마법을 통해 컴퓨터는 데이터를 의미 있는 방식으로 저장, 처리 및 표시할 수 있습니다.

10을 넘어서 세려면 새로운 기호가 필요합니다. 만약 저가 책임을 맡으면 멋진 새로운 물결표와 같은 것들을 만들어 애완동물처럼 명명할 것이고, moppity-squidge와 같은 새로운 숫자들이 생겼을 겁니다. 그러나 스웨덴계 미국인 엔지니어인 존 윌리엄스 나이스트롬은 전파체제인 16진법을 설명할 때 현명하게 A부터 F까지의 문자를 사용하였습니다.

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

각 열은 16의 거듭제곱을 나타냅니다.

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*fU7lvzbf1q7uzkeJqT7lFw.gif)

## 16진수의 힘

컴퓨터는 오직 1과 0만을 이해하지만, 16진수는 그들을 프로그래밍하는 실수를 저지르는 인간에게 매우 유용합니다. 데이터의 1바이트 또는 8개의 이진 비트는 두 개의 16진수 숫자로 감소시킬 수 있습니다. 예를 들어, 10110001은 177의 10진수 값을 갖고 있으며, 16진수로는 B1입니다.

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

내 16 진수 디스플레이는 000000부터 FFFFFF까지 천천히 카운트됩니다. 이는 16,777,215초에 해당하여 완료까지 6개월 이상이 걸릴 것입니다. 얼마나 멋진 라이브 스트림이 될까요!

16 진수는 주로 프로그래머들이 사용하는 요약 표기법으로, 특히 색상을 다룰 때 사용됩니다. 여섯 자리의 16 진수는 빨강, 초록 및 파랑이 각각 두 자리씩 할당될 수 있습니다. 00은 해당 색상이 없음을 나타내며 FF는 255의 최댓값을 나타냅니다. 여섯 자리 숫자는 모두 합쳐서 16 백만 가지 이상의 색상을 만들어 냅니다.

<img src="/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_0.png" />

## 시공

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

내 16진수 디스플레이는 내 이진 시계 프로젝트의 후속작입니다. 그 프로젝트에서는 8비트 바이너리를 사용하여 시간을 표시했어요.

바이너리 시계와 마찬가지로, 이번에도 Cricut 절단기를 사용하여 아트보드의 층을 잘라서 만들었어요. 이번에는 병원 NeoPixel LED에 96개의 구멍을 자르기 위해 준비했어요.

![이미지](/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_1.png)

평범한 사무용지에 글자를 잘라내서, 루트라더 패브릭으로 뒷받침하여 빛을 분산시키도록 했어요.

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

![이미지](/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_2.png)

![이미지](/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_3.png)

![이미지](/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_4.png)

저는 배경판에 16개의 LED 스트립과 벽에 빛을 반사하기 위해 추가로 40개의 LED를 설치했습니다. 기본적으로 Raspberry Pi Pico W를 사용하여 개별적으로 제어되는 136개의 LED로 이루어진 긴 체인입니다.

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

![Exploring Different Number Bases With a Hexadecimal Display 5](/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_5.png)

![Exploring Different Number Bases With a Hexadecimal Display 6](/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_6.png)

USB 앰미터를 사용하여 전체가 그린 전류량을 계산한 다음 소프트웨어 내에서 밝기를 조정하여 가능한 근접한 값을 2암페어로 설정했습니다. 이렇게 하면 조명을 구동하는 데 표준 USB 휴대폰 충전기를 넘어서는 것이 필요하지 않게 됩니다.

전원 공급에 연결하려면 마이크로 USB 브레이크아웃 보드를 사용했습니다. 이를 통해 조명과 Raspberry Pi Pico W가 각각 별도로 전원을 공급받습니다. Pi는 5V 및 접지 핀을 통해 전원을 공급받습니다.

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

## Raspberry Pi Pico W 프로그래밍

이 프로젝트의 코드는 GitHub에서 찾을 수 있습니다. 여기에는 커팅 기계용 SVG 파일도 포함되어 있습니다.

Pico에서 웹 서버를 만드는 일반적인 개요를 원하신다면 위에 링크된 Binary Clock 프로젝트를 참조해주세요. 여기서는 단계를 반복하지 않고 모델이 작동하는 방식에 대한 개요를 제공하겠습니다.

## 웹 서버

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

한 번 더로 Blaz-R의 GitHub 저장소 안내에 따라 Neopixel 라이브러리를 설정하여 가동하기 시작했어요.

설정을 마치면 Pico가 자체 WiFi 네트워크를 송출하여 어떤 장치라도 연결하고 따라서 조명을 제어할 수 있었어요.

![이미지](/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_7.png)

메뉴에서 옵션을 선택할 수 있어요. '카운트' 메뉴에서는 카운트할 진법을 선택할 수 있고, ‘색상 선택기’ 메뉴는 여러분이 화면에 표시된 16진 코드의 색상과 뒷면 벽에 물들인 색상을 선택할 수 있는 경로로 안내해줘요.

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

![Image](/assets/img/2024-05-23-ExploringDifferentNumberBasesWithaHexadecimalDisplay_8.png)

'Colour Spectrum' 옵션을 선택하면 스펙트럼 색상을 표시하는 루프가 생성되며, 빨간색에서 녹색으로, 그리고 다시 파란색으로 서서히 변화합니다. GIF로는 잘 표현되지 않아요. 이를 확인하려면 YouTube 동영상을 확인해보세요.

마지막 메뉴 아이템을 선택하면 무지개색이 전체 LED 문자열을 따라 파도치는 효과가 나타납니다. 또한 카운트가 완료되면 이 디스플레이가 표시됩니다.

![GIF](https://miro.medium.com/v2/resize:fit:1400/1*M6RlO4NuI97PLc7m8kz9Eg.gif)

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

이 글을 즐겁게 읽으셨다면 저의 다른 프로젝트를 더 보고 싶으시다면 저를 Medium에서 팔로우하거나 YouTube 채널을 구독해주세요.
