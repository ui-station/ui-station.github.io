---
title: "로라란 무엇인가 기본 이해"
description: ""
coverImage: "/assets/img/2024-05-20-WhatisLoRaThefundamentals_0.png"
date: 2024-05-20 19:08
ogImage:
  url: /assets/img/2024-05-20-WhatisLoRaThefundamentals_0.png
tag: Tech
originalTitle: "What is LoRa: The fundamentals"
link: "https://medium.com/@prajzler/what-is-lora-the-fundamentals-79a5bb3e6dec"
---

![LoRa Image](/assets/img/2024-05-20-WhatisLoRaThefundamentals_0.png)

Let me start by debunking what even AWS got wrong. As of Oct 27, 2023, the AWS page mistakenly claims that "LoRa is a physical layer protocol". To add insult to injury, it further claims that "LoRa is a wireless audio frequency technology".

Sorry AWS, but no. LoRa has nothing to do with audio frequencies. There are chirps, but not that kind of chirps.

More importantly, LoRa is not a protocol. At least not if you want to talk about it with a radio engineer.

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

하지만 나는 왜 혼동이 많은지 이해합니다. LoRa를 처음 살펴볼 때 LoRaWAN과 LoRa 사이의 구분과 네트워크 스택을 통해 어떻게 나누어지는지 혼란스러울 수 있습니다.

그리고 특히 AWS 동료들과 함께 긴 지루한 회의 중에 chirps에 대해 이야기하기 시작하면, 새들이 지저귀고 오디오 주파수를 생각하게 될 수도 있습니다. 이해해요.

그럼 제가 정확히 무엇인지 궁금하시다면 알려드리죠.

하지만 진지한 톤으로 돌아가서 더 깊게 파고들기 전에, 왜 날 듣는 게 중요할까요?

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

# LoRa와 함께 떠나는 여정

저의 LoRa와 LoRaWAN과의 여정은 10년 넘게 지난 지금(2023년 10월 기준) 시작되었습니다.

Semtech와 IBM Research가 LoRaWAN에 대해 협업하기 시작한 때, Cycleo를 인수한 Semtech가 LoRa의 원조 회사이자 기술을 개발한 회사였던 Cycleo를 구매한 직후였죠. 제 전 직장 동료들 둘이 LoRaWAN 표준을 직접 쓰곤 했습니다 — LoRaWAN 1.0의 두 번째 페이지를 확인해보세요.

그 당시에는 The Things Network이 등장하기도 전, Chirpstack이 만들어지기도 전이었습니다. 심지어 SX1301도 존재하지 않았는데요 — 우리는 FPGA를 사용해야 했습니다!

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

2015년에 네트워크 운영자를 위한 LoRaWAN 소프트웨어를 개발하기 위해 회사를 설립했어요.

그때부터 LoRa에 대해 많이 배웠어요.

추억은 그만하고, LoRa에 대해 알아볼까요?

# 지상 진실

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

로라는 디지털 스프레드 스펙트럼 변조 중에서도 더 구체적으로 치르프 스프레드 스펙트럼 변조입니다.

하지만 제 말 믿지 마세요. 로라가 무엇인지 정확히 알고 싶다면 특허를 읽어보세요.

해당 특허는 변조 자체에 대해 많이 주장하지 않지만, 특정 방식으로 변조기와 복조기를 구축하는 데 초점을 맞추고 있습니다.

21세기에는 모듈레이션을 특허 내기가 꽤 어려울 정도로 모듈레이션이 예로부터 존재해왔다는 사실에 놀라실 지도 모르겠네요.

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

만약 패턴트에서 설명한 대로 LoRa를 정확하게 정의하고 싶다면, LoRa는 칩 신호에 대한 변조기와 복조기를 포함하는 통신 시스템이라고 할 수 있어요.

그건 좀 긴 설명이죠, 그리고 당연히 제품을 홍보하는 데 마케팅 부서가 선택할 내용은 아니에요.

패턴트용어를 좀 더 간단하게 줄이면, LoRa가 칩 변조라고 말할 수 있어요.

어떤 방식을 선택하든, LoRa는 명확히 프로토콜이 아니에요. LoRa와 함께 사용할 프로토콜을 원한다면, LoRa의 물리 계층으로 LoRaWAN을 살펴보세요. LoRaWAN은 프로토콜이지만 LoRa는 아니에요.

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

만약 TL;DR을 찾으시면 여기까지 읽으시고 그만두셔도 돼요.
자세한 내용을 찾으신다면 계속 읽어주세요.

# LoRa에서 왜 체프를 사용할까요?

LoRa 이전에도 체프 변조는 있었습니다. 하지만 프랑수아, 니콜라, 올리비에는 체프 변조를 사물 인터넷에서 발생하는 문제의 해결책으로 바꾼 무언가를 만들었습니다.

그렇다면, 체프 변조란 무엇인가요?

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

다른 변조 기술에 대해 들어본 적이 있을 수 있습니다. 예를 들어 AM 또는 FM이 있습니다. AM은 진폭 변조, FM은 주파수 변조를 의미합니다. 이러한 것들은 오래된 변조 방식인 아날로그 변조에 속합니다.

칩 변조는 더 발전된 기술이며, Wi-Fi에서 사용되는 변조와 가까운 관련이 있습니다. 하지만 Wi-Fi와는 조금 다릅니다. 이러한 변조 방식은 디지털 변조라고 하며, 조금 더 구체적으로 확산 스펙트럼 변조입니다.

확산 스펙트럼 변조는 처음에는 군사 응용 분야에서 무선 통신 탐지를 회피하기 위해 사용되었으나, 이후 소비자 시장에서도 사용되고 있습니다.

그리고 소음 속에 숨어 있는 것이 LoRa와 같은 칩 변조가 뛰어난 점입니다.

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

# LoRa 치르핑 — 자연에서 영감을 받은

치르핑 변조를 우연히 치르핑 변조라고 부르지 않는 이유가 있습니다. 이 변조는 새들의 치르프와 매우 유사합니다.

여러 새들이 동시에 치르퍼도 특정 새종의 치르프를 듣는 것이 얼마나 쉬운지 생각해 보신 적이 있나요? 그리고 얼마나 먼 거리에서 그 소리를 듣고 배경 소음과 구별할 수 있는지요?

박쥐나 돌고래들은 치르프를 하지 않는다고 생각할 수도 있지만, 실제로 치르프를 합니다. 새들과 달리, 그들의 치르프는 인간에게는 감지할 수 없는 더 높은 주파수로 이루어져 있지만, 동일한 이유로 — 전파 범위와 다른 소음과의 구별을 위해.

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

모든 지저귀는 소리파도 특성이 라디오파로 잘 변환됩니다. 지저귀 변조의 장점은 신호를 완전히 소음에 잠겨 있을 때조차 해독할 수 있다는 것입니다. 이것이 LoRa가 가지는 초능력의 원천입니다.

# 지저귀 변조 신호의 세부사항

정확한 용어로, 지저귀란 주파수가 시간에 따라 증가하거나 감소하는 신호입니다. 이것이 무엇을 의미하는지, 복잡하지 않지만 더 잘 알려진 변조 기술인 AM과 FM과 비교하여 설명해보겠습니다.

AM은 신호의 진폭을 일정한 주파수에서 변화시킴으로써 변조를 수행합니다.

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

![2024-05-20-WhatisLoRaThefundamentals_1.png](/assets/img/2024-05-20-WhatisLoRaThefundamentals_1.png)

FM은 캐리어 주파수 위에 신호 주파수를 변조합니다. FM 라디오 방송에서는 캐리어가 MHz 범위에 있으며, 스테레오 신호의 변조된 신호는 54 kHz이며 RDS의 경우 59 kHz입니다.

![2024-05-20-WhatisLoRaThefundamentals_2.png](/assets/img/2024-05-20-WhatisLoRaThefundamentals_2.png)

AM과 FM은 모두 아날로그 변조로, 오디오와 같은 아날로그 신호를 전달하기 위해 설계되었습니다. 우리는 아날로그가 아닌 디지털 변조인 칩 변조로 큰 도약을 해야 합니다.

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

Chirp 변조는 전송하려는 데이터의 1과 0을 가져와 이를 여러 개의 chirp로 변환합니다. 이러한 chirp들은 고정 주파수 캐리어 신호 위에 하나씩 차례대로 전송됩니다.

![Chirp in a LoRa frame](/assets/img/2024-05-20-WhatisLoRaThefundamentals_3.png)

안타깝게도, chirp 변조 신호는 간단한 다이어그램으로 설명하기에는 너무 복잡합니다. 그래서 양해 부탁드리겠습니다. LoRa 특정 케이스를 설명하겠습니다.

# LoRa 프레임 속의 Chirp

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

유효한 LoRa 프레임을 생성하려면 단순히 하나의 치르프를 생성하는 것 이상의 작업이 필요합니다. 여러 치르프가 LoRa 라디오가 복조(modulate)할 수 있는 프레임으로 조립되는 특정 방법이 있습니다. 아래 프레임 그림을 참조해주세요.

![LoRa Frame](/assets/img/2024-05-20-WhatisLoRaThefundamentals_4.png)

이 그림에서 파란색 선은 각각 하나의 치르프입니다. 시간이 흐름에 따라 치르프가 주파수 범위를 '훑어' 가는 것을 확인할 수 있습니다.

LoRa 프레임에는 세 그룹의 치르프가 있습니다. Preamble, sync word, 그리고 data가 있습니다. 각 그룹은 LoRa 프레임에서 다른 역할을 합니다. 또한 프레임의 각 부분은 서로 다른 유형의 치르프를 사용합니다.

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

전문 부분과 동기 워드는 수신기의 "주의"를 끌기 위해 간단한 위아래 칠러프(chirping)를 사용합니다. 목표는 전문 부분과 동기 워드를 쉽게 감지할 수 있게 하는 것입니다. 많은 정보를 나르기 위해 필요한 것은 아닙니다. 노이즈 바닥에 대비하기 쉽고 고정밀도로 복조할 수 있게 만들어야 합니다.

데이터 부분은 실제로 변조된 부분으로, 여기서 주파수 시간 프로필이 심볼 값을 정의합니다.

전체 프레임 및 개별 심볼의 주파수 시간 프로필은 다양한 방식으로 모양을 갖출 수 있습니다. 아직 지쳐있지 않다면, LoRa 라디오에서 칠프 변조가 어떻게 사용되는지 살펴봅시다.

# 실제 세계에서 LoRa 사용하기

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

LoRa 라디오를 사용하기 시작하면 주파수 대역폭, 확산 요인 및 부호화 속도와 같은 구성 옵션에 주목하게 됩니다. 이러한 것들은 변조의 매개변수입니다. 이들이 신호가 생성되는 방식을 정의합니다.

# LoRa 대역폭

대역폭은 신호가 변하는 속도를 나타내며 단위 시간당 전송될 수 있는 정보의 이론적 최대치를 정의합니다. 대역폭의 단위는 헤르츠(Hz)입니다.

이론적으로는 LoRa와 함께 어떠한 대역폭도 사용할 수 있습니다. 그러나 실제로는 사용 가능한 주파수 스펙트럼, 스펙트럼 규제 및 송수신기의 물리적 한계로 항상 제한을 받게 됩니다. 이로 인해, 서브-GHz LoRa에서 일반적으로 사용되는 대역폭(채널 폭)은 125 kHz이며, 대부분의 칩은 250 kHz 및 500 kHz에서도 변조할 수 있으며 더 낮은 주파수에서도 사용할 수 있습니다.

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

2.4 GHz에서 LoRa 칩은 최대 1.625 MHz의 대역폭을 처리할 수 있습니다.

대역폭에 대한 경험 규칙은 다음과 같습니다.

- 대역폭이 높을수록 데이터 전송률(칩 속도)도 높아집니다.
- 대역폭이 낮을수록 데이터 전송률(칩 속도)도 낮아집니다.

최종 데이터 전송률을 계산할 때는 스프레딩 팩터(spreading factor)와 코딩 비율(coding rate)이라는 두 가지 추가 요소를 고려하는 것이 중요합니다.

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

# LoRa 확산 요소

확산 요소는 대역폭(칩 속도)과 데이터 속도(심볼 속도) 간의 비율을 정의합니다. 이는 심볼이 시간 및 주파수 상에서 얼마나 확산되어야 하는지를 정의합니다. 기본적으로, 선택한 대역폭 상에서 침 이동이 얼마나 빨리 또는 느리게 이루어져야 하는지를 말합니다.

![LoRa 확산 요소](/assets/img/2024-05-20-WhatisLoRaThefundamentals_5.png)

확산 요소가 높을수록 사용하는 스펙트럼이 많아지지만, 다른 쪽에서 신호를 해독하는 것이 더 쉬워집니다. 이는 LoRa 신호를 성공적으로 복조할 수 있는 범위를 늘리기 위해 확산 요소를 사용할 수 있다는 것을 의미합니다. 더 큰 안테나나 강력한 증폭기가 필요하지 않습니다. 이 모든 것은 구성으로 가능합니다.

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

식 스펙트럼은 대역폭이 고정되어 있는 경우(LoRaWAN 네트워크 및 대부분의 LoRa 네트워크의 경우) 사용하는 확산 인자가 높을수록 데이터 전송률(심볼 전송률)이 낮아진다는 것을 의미합니다. 확산 인자가 높을수록 Time on Air(ToA)가 길어지며, 이는 전송 시간이 길어진다는 것을 의미합니다.

여기에서 LoRa의 실제 강점이 나타납니다. 소프트웨어에서 범위, 대역폭 또는 더 짧은 전송(배터리 사용량 감소)이 필요한지를 결정할 수 있습니다.

이론적으로 LoRa에서는 어떤 확산 인자라도 사용할 수 있지만, 실제로는 사용 가능한 대역폭과 디모듥레이터 설계에 제한을 받습니다. 물리적으로 마이크로칩 크기, 마이크로칩 복잡성 및 기타 상업적 제약 사항은 LoRa 모듈레이터와 디모듥레이터의 성능을 결정합니다.

원래 LoRa 칩은 SF7부터 SF12까지의 확산 인자 범위를 가졌지만, 현대 칩에 탑재된 현대 모듈레이터는 SF5부터 SF12까지 범위를 가지고 있습니다.

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

만약 정의된 대역폭과 확산 계수로부터 정확한 심볼 속도를 계산해야 할 때 다음 공식을 사용할 수 있어요:

칩당 심볼 속도 = Hz 단위의 대역폭 ÷ (확산 계수의 2의 제곱)

간단히 말하면, 확산 계수가 증가함에 따라 심볼 속도는 지수적으로 감소합니다.

경험적 법칙: 확산 계수를 1 증가시키면 심볼 속도가 절반으로 줄어들어요.

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

125 kHz 대역폭에서 심볼 속도를 확인하는 데 사용할 수 있는 표입니다. 심볼 속도는 데이터 속도와 동일하지 않음을 유의하세요. 심볼 속도를 데이터 속도로 변환하는 방법을 알아보려면 계속 읽어보세요.

```js
| 확산 계수 | 대역폭 | 심볼 속도             |
| (정수)   | (Hz)  | (칩/초, 내림 처리)    |
| --------- | ------ | ----------------------|
| 5         | 125000  | 3906                  |
| 6         | 125000  | 1953                  |
| 7         | 125000  | 976                   |
| 8         | 125000  | 488                   |
| 9         | 125000  | 244                   |
| 10        | 125000  | 122                   |
| 11        | 125000  | 61                    |
| 12        | 125000  | 30                    |
| 13        | 125000 | 15                     |
| 14        | 125000 | 7                      |
```

SF12를 넘어가지 않는 이유는 무엇일까요? SF13, SF14? 그 이유는 대역폭 제약 때문입니다. 언젠가는 심볼 속도가 너무 낮아져서 사용 가능한 대역폭 내에서는 실용적으로 사용할 수 없을 것입니다.

확산 계수 TL;DR:

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

- 스프레딩 팩터가 높을수록 데이터 전송 속도가 낮아집니다.
- 스프레딩 팩터가 높을수록 전파 범위가 더 멀어집니다.
- 스프레딩 팩터가 높을수록 전송 속도가 느려집니다.
- 스프렝 팩터가 낮을수록 데이터 전송 속도가 높아집니다.
- 스프레딩 팩터가 낮을수록 전파 범위가 짧아집니다.
- 스프레딩 팩터가 낮을수록 전송 속도가 빨라집니다.

최종적으로, 데이터 전송 속도를 정의하는 요소는 스프레딩 팩터, 대역폭, 그리고 코딩률의 조합입니다.

# LoRa 코딩률

LoRa 커뮤니티나 LoRaWAN 표준에서 코딩률에 대한 논의는 거의 없습니다. 이는 대부분 LoRaWAN 표준에서 코딩률을 4/5로 고정하기 때문일 것입니다.

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

코딩 속도는 무엇인가요? 두 숫자는 무엇을 나타내나요? 디지털 변조에서 코딩 속도는 오류 수정 코드의 효율성을 정의합니다. 칩 모듈레이션과 LoRa도 마찬가지입니다.

이 비율은 정보(오류 수정된) 비트의 수와 전송된 총 비트의 수를 나타냅니다. 정보 비트의 수는 항상 총 비트 수보다 적습니다.

4/5의 코딩 속도는 각 심볼당 정보 비트 4개와 총 5개의 비트가 있음을 의미합니다.

만약 두 숫자가 동일하다면 오류 수정이 이루어지지 않습니다. 이것은 옵션이지만 LoRa와 함께 일반적으로 사용되지는 않습니다.

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

코딩 속도와 오류 정정 코드는 수학적으로 약간의 마법 같아요. 메시지에 여분의 비트를 추가하는데, 이러한 비트는 수신자가 데모듈레이션 오류를 복구하는 데 사용할 수 있어요. 중요한 오류가 아닌 한요. 오류가 중요하거나 남은 비트가 너무 적다면, 오류가 발생했는지 여부만 알 수도 있어요.

심볼 속도에서 실제 데이터 속도를 계산하려면 심볼 속도에 스프레딩 팩터와 코딩 속도 비율을 곱하면 돼요.

비트 당 데이터 속도 = 심볼 속도 x 스프레딩 팩터 x 코딩 속도 비율

125kHz 대역폭으로, 이러한 것들은 4/5 코딩률로 달성할 수 있는 데이터 속도의 내림된 값들이에요.

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

| Spreading factor | Bandwidth | Symbol rate | Coding rate | Data rate |
| ---------------- | --------- | ----------- | ----------- | --------- |
| (integer)        | (Hz)      | (chips/s)   | (ratio)     | (bits/s)  |
| 5                | 125000    | 3906        | 4/5         | 15624     |
| 6                | 125000    | 1953        | 4/5         | 9374      |
| 7                | 125000    | 976         | 4/5         | 5465      |
| 8                | 125000    | 488         | 4/5         | 3123      |
| 9                | 125000    | 244         | 4/5         | 1756      |
| 10               | 125000    | 122         | 4/5         | 976       |
| 11               | 125000    | 61          | 4/5         | 536       |
| 12               | 125000    | 30          | 4/5         | 288       |
| 13               | 125000    | 15          | 4/5         | 156       |
| 14               | 125000    | 7           | 4/5         | 78        |

코딩 비율 TL;DR:

- 중복 비트 수가 많을수록 실질 데이터 전송 속도가 낮아짐
- 중복 비트 수가 적을수록 실질 데이터 전송 속도가 높아짐
- 중복 비트 수가 많을수록 오류에 대한 저항력이 높아짐
- 중복 비트 수가 적을수록 오류에 대한 저항력이 낮아짐

중복 비트 수가 얼마나 많은 오류를 탐지하거나 복구할 수 있는지에 대한 구체적인 수학적 세부 사항은 이 글의 범위를 벗어납니다. 아마도 나중에 다룰 수 있을 것 같습니다.

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

# 왜 LoRa가 유용한가요?

모든 이러한 기능들은 기술적으로 흥미로운 것이지만, 실제로 현실 세계에서 유용한가요? 어떤 문제를 해결하나요?

LoRa는 고대역폭을 위해 설계된 것이 아니며, 결코 최상의 선택이 되지는 않을 것입니다. LoRa 채널을 통해 비디오 스트림을 전송하기를 기대하지 마세요.

하지만, LoRa는 범위와 데이터 속도 사이에 타협이 필요한 경우에 서브-기가헤르츠 무료 주파수 대역에서 훌륭한 선택지입니다. Wi-Fi와 비슷하지만 훨씬 더 긴 범위와 훨씬 낮은 데이터 속도를 제공합니다. Wi-Fi와 달리, LoRa 상에서 실행할 MAC 계층을 선택해야 합니다. LoRaWAN일 수도 있고 아닐 수도 있습니다.

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

로라는 무선 링크의 물리 계층을 조정할 수 있는 다양한 매개변수를 제공하여 범위, 데이터 속도, 신뢰성 및 배터리 사용에 대한 특정 요구 사항에 맞게 설정할 수 있습니다.

로라의 유연성은 로라가 시작된 지 10년 이상이 지난 지금에도 여전히 사용되는 이유입니다. 유연성 덕분에 쉽게 상자에서 꺼내서 사용할 수 없지만요.

MAC 계층에 대해서도 선택권을 줍니다만, 그건 다른 시간에 다루겠습니다.
