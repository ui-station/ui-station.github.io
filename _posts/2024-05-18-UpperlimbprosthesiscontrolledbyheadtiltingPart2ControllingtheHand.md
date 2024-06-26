---
title: "상체 보조장비를 머리 기울임으로 제어하기 제2부 손 제어"
description: ""
coverImage: "/assets/img/2024-05-18-UpperlimbprosthesiscontrolledbyheadtiltingPart2ControllingtheHand_0.png"
date: 2024-05-18 18:53
ogImage:
  url: /assets/img/2024-05-18-UpperlimbprosthesiscontrolledbyheadtiltingPart2ControllingtheHand_0.png
tag: Tech
originalTitle: "Upper limb prosthesis controlled by head tilting. Part 2: Controlling the Hand"
link: "https://medium.com/@Wosk1947/upper-limb-prosthesis-controlled-by-head-tilting-part-2-controlling-the-hand-b0c7d9c68b96"
---

<img src="/assets/img/2024-05-18-UpperlimbprosthesiscontrolledbyheadtiltingPart2ControllingtheHand_0.png" />

이전 글인 '머리 기울임으로 제어되는 상지 보조기 | Pavel Kochetkov | 2024년 3월 | Medium'에서는 팔목과 손의 제어를 머리 기울임을 통해 구현하는 방법을 논의했습니다. 우리는 손목을 공간상 필요한 지점에 위치시키고 나서 손 제어 모드로 전환하는 방법을 시연했습니다. 우리는 제어하는 방법 중 가장 간단한 방법과 회전을 통해 수행할 수 있는 일부 작업을 보여주었습니다. 본문에서는 손에 완전한 형태의 물체 그리핑 메커니즘을 추가하고 이 설정에 대한 여러 제어 방식을 편의성과 속도 측면에서 테스트하는 것을 더해 나가겠습니다.

지난 번에 비해 손에 두 개의 추가 서보 모터가 추가되었습니다. 하나는 손을 종횡축을 기준으로 회전시키고 다른 하나는 조작기를 열고 닫는 데 사용됩니다. 따라서 손은 어떤 각도로든 회전하고 물체를 집을 수 있는 3DOF 조작기로 변모되었습니다. 또한 이번에는 팔목 회전을 담당하는 모터가 일시적으로 제거되었습니다. 지금은 팔목이 정지되어 어깨에 대한 팔목의 평균 위치를 시뮬레이션한 25도 회전 각도로 고정될 것입니다.

<img src="/assets/img/2024-05-18-UpperlimbprosthesiscontrolledbyheadtiltingPart2ControllingtheHand_1.png" />

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

또한, 모드 전환 및 손가락 이동을 제어하기 위해 여러 버튼이 추가되었습니다. 이러한 버튼은 다른 근육(예: 상완 이두근 및 대흉근, 팔 부근에 가까운 근육)에 부착된 근전도 센서로 대체할 수 있습니다. 이는 전향감각에 긍정적인 영향을 미치며 신뢰할 수 있게 분리하여 수축시킬 수 있습니다. 그러나 이것은 필수적인 것은 아닙니다. 상용 바이오닉 프로시시가 있으며 현재 작동 중인 다른 신체 부위에서 눌리는 일반적인 버튼으로 제어 요소가 구성된 것을 확인할 수 있습니다.

연구 중에는 세 가지 제어 방식이 테스트되었습니다. 각각에 대해 상세히 논의하고 각 방식의 장단점 및 개선 가능성을 살펴보겠습니다.

Raw Input

이 방식은 컴퓨터 마우스의 해당 모드와 유사하게 이름이 지어졌는데, 커서 이동이 중간 신호 처리 없이 최소한 또는 전혀 전달되지 않고 조작자의 움직임을 직접 모방합니다. 이 방식에서는 팔목의 기울기가 이전 글에서 설명된 대로 머리의 기울기를 모방하며, 추가로 팔목의 종축 주변 회전은 머리의 종축 주변 회전을 복제합니다.

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

이를 확인하기 위해 간단한 테스트가 고안되었습니다. 여기서는 세 개의 물체를 가져와 탁자 위를 이동시켜야 했는데, 각각의 물체는 XYZ 축 중 하나와 정렬되고 연장된 모습을 가지고 있었습니다. 이 실험은 이러한 작업들이 상당한 시간이 소요된다는 것을 보여주었습니다.

이 체계의 주요 불편함은 머리의 측면 회전과 종 축 주위 회전이 자연스럽게 섞인다는 점에 있습니다. 우리가 머리를 오른쪽이나 왼쪽으로 돌릴 때, 약간 기울이기도 합니다. 이 두 움직임을 분리하는 노력이 필요합니다. 손목의 종 축이 시선과 수직이 되었을 때(즉, 손목이 시선과 수직이 되는 경우), 제어가 직관성을 잃고, 손목이 어디로 끝나게 될지 예측하기 어려워지며, 조작기가 어떤 방향으로 회전할지 혼란이 생깁니다.

이 모드의 장점 중 하나는 손목 축이 시선으로부터 조금 벗어났을 때 작동이 잘되고 직관적이라는 것입니다. 이는 조작기 앞에 있는 시선 바로 앞에 있는 작은 물체들을 정교하게 다루는 데 적합할 수 있습니다. 다양한 머리 움직임들이 섞이는 것은 문제입니다. 아마도 사용자의 의도를 더 정확하게 인식할 수 있는 신경망을 활용하여 이를 해결할 수 있을 것입니다. 그러나 저는 이 문제를 좀 덜 세련된 방식으로 해결해 보았습니다 — 두 움직임을 물리적으로 분리함으로써 동시에 수행되지 않도록 만들었습니다.

Pitch-Yaw / Roll 분할

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

이 계획은 이전과 다른 점이 있습니다. 손목의 각도를 동시에 수직 및 수평으로 설정하거나 손목의 종축 주위의 회전을 설정할 수 있습니다. 두 모드 간 전환은 버튼을 눌러 발생합니다. 여기에는 약간의 어려움이 있습니다. 로봇 팔이 한 유형의 동작을 수행할 때 머리는 다른 유형의 동작을 계속하지만 이것은 로봇에 통신되지 않습니다. 이로 인해 다른 유형의 동작으로 전환할 때 로봇 팔이 위치를 갑자기 바꾸어 머리에서 통신되지 않은 움직임을 따라잡습니다. 이를 피하기 위해 다른 동작으로 전환할 때 부드럽게 적용되는 부드럽게 로봇 팔을 실제 위치로 이동시켜 사용자가 원하는 대로 알아차리고 조정할 수 있도록 합니다.

그 외 모든 면에서, 이 계획은 이전 계획과 동등하며 모든 장단점을 갖추었으며 별도로 추가됩니다. 첫 번째 계획에 대한 동일한 테스트를 수행하려고 시도했지만 결과가 너무 나빠서 표시하지 않기로 결정했습니다. 정확성과 직관성은 Raw Input보다 훨씬 떨어집니다. 이는 고립 감각이 감소되었기 때문일 수 있습니다. 두 부분이 다른 시간에 끊어질 때 Raw Input과 같이 움직임의 완전하고 제한이 없는 거울 링이 예상되면 뇌가 혼동됩니다.

그러나 다른 테스트에서는 로봇 팔을 슬롯을 통과시키고 긴 물체를 끄는 작업이었을 때이 계획이 잘 작동했지만, 내 의견으로는 테스트 공간의 기하학이 엄격한 조치의 순서를 지시했기 때문일 뿐입니다. 마지막으로, 이 제어 계획은 실패한 것으로 간주합니다.

위에 설명된 두 계획에는 또 다른 심각한 결점이 있습니다. 손목을 특정 위치에 고정하기가 어렵습니다. 이는 머리를 완전히 고정시키는 것이 필요하기 때문입니다. 이 문제 해결을 위해 그리고 다른 몇 가지 문제를 해결하기 위해 다른 계획이 구현되었습니다.

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

포즈 모드

이 계획에서는 손목의 기울기 각을 머리로 직접 제어하는 것을 거의 완전히 포기하기로 결정했습니다. 대신, 머리 움직임을 통해 다양한 고정된 손목 위치로 전환하고, 머리가 종축 주위로 회전하는 것은 여전히 손목의 종축 주위 회전을 제어합니다. 모드를 전환하려면 버튼을 누르고 머리를 원하는 위치로 돌리면 됩니다. 이후에 손목은 해당 위치에 남아 있고, 전역 Z-축에 대한 각도가 유지됩니다 (즉, 손목이 수직으로 아래로 설정되었거나 지면과 수평할 경우, 팔의 위치에 관계없이 항상 수직으로 아래로 남아 있거나 지면과 평행할 것입니다).

이 계획을 사용하여 여러 번의 테스트가 진행되었고, 매우 잘 작동했습니다. 개인적인 경험으로 볼 때, 이 계획은 다른 스키마와 달리 흔들림 없이 의도한 대로 손으로 정확히 행동할 수 있는 유일한 계획입니다. 물론, 이 계획은 Raw Input의 자유가 부족하지만, Raw Input을 이용하여 특이한 동작을 수행할 수 있는 모드 중 하나로 전환할 수 있으므로 단순한 일상 작업에는 고정된 위치를 사용할 수 있습니다. 주변 세계와 그 안의 물체는 종방향 방향성을 갖고 있으며, 이러한 제어 방법은 많은 작업에 충분합니다.

게다가, 이 방법은 상용 바이오닉 보조기에서 사용되는 기존 제어 시스템과 일치하여 사용자가 다양한 물건과 상호 작용하기 위한 손목 위치(방향 및 손가락 구성)을 선택할 수 있도록 허용합니다.

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

결론

이 기사에서는 바이오닉 핸드를 위한 여러 제어 방식을 테스트했습니다. 여기서 제시된 것보다 더 편리하고 정확한 방식이 더 많이 발견될 수 있습니다. 제 입장에서는 자이로스코프와 고전적 결정론적 알고리즘과 같은 간단한 센서로 달성할 수 있는 한계에 도달했습니다. 그래서 나는 VR 헤드셋을 활용하여 내 장치의 디지털 트윈을 이용한 추가 연구를 계획하고 있습니다. 내가 알기로는 이것은 기계 시각을 위한 카메라와 같은 복잡하고 비싼 구성 요소의 비용을 줄이고, 프로토타입을 만드는 데 필요한 시간을 단축하며, 단일 보드 컴퓨터에 쉽게 맞지 않는 계산적으로 복잡한 모델을 테스트할 수 있게 해주는 보청기 산업에서 일반적인 실천 방법입니다.

언제나 모든 소스 코드는 제 GitHub https://github.com/Wosk1947에서 찾을 수 있습니다. 지금은 여기서 작별인사를 드리겠습니다. 모두 주목해 주셔서 감사합니다!

파벨 코체토프

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

---

메일: ukivui@gmail.com

텔레그램: pashko_92

깃허브: [Wosk1947](https://github.com/Wosk1947)

링크드인: Pavel Kochetkov | LinkedIn

---
