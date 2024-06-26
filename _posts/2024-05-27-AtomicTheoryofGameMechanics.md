---
title: "게임 메카닉의 원자론"
description: ""
coverImage: "/assets/img/2024-05-27-AtomicTheoryofGameMechanics_0.png"
date: 2024-05-27 16:48
ogImage:
  url: /assets/img/2024-05-27-AtomicTheoryofGameMechanics_0.png
tag: Tech
originalTitle: "Atomic Theory of Game Mechanics"
link: "https://medium.com/perspectives-in-game-design/atomic-theory-of-game-mechanics-dbb752a77987"
---

![Atomic Theory of Game Mechanics](/assets/img/2024-05-27-AtomicTheoryofGameMechanics_0.png)

2차 패턴 언어 게임 디자인의 2판에서, 게임 메카닉에 초점을 맞춘 고급 패턴 연습을 제시할 것입니다. 두 가지 모두 중요하고 구분됩니다. 이러한 연습의 구조는 이전 섹션보다 더 고급입니다. 이 중 많은 연습이 그룹이 수행하는 것이 가장 좋고, 개인에게는 매우 어려우며 시간이 많이 소요됩니다. 여기서 메카닉에 초점을 둔 것도 중요한데, 과거 연습의 언어를 사용하여 메카닉 기반 패턴과 기능적 패턴을 유도하는 과정이 구별됩니다. 이러한 패턴의 씨앗을 선택할 때 개념적 설계 작업을 더 많이 수행해야 하며, 연습 자체는 더 고급 분석적 설계를 요구합니다.

디자인 패턴은 게임 디자인의 여러 다양한 측면에 적용할 수 있음을 보셨습니다. Adams와 Dormans의 작업은 이러한 아이디어를 게임 시스템에 성공적으로 적용했습니다. 그들의 패턴은 게임 메카닉: 고급 게임 디자인에서 발견되는데, 물론 메카닉에 초점을 맞추고 있습니다. 그들의 언어는 게임 시스템을 경제로 보고 해당 구현에 경제학 원리를 적용하는 주로 상위 수준의 일반 시스템 패턴을 서술합니다. 그 접근 방식은 가치 있는데, 저는 이후에 이러한 프레임워크 중 일부를 채택합니다. 그러나 Jesse Schell의 언어로 렌즈 또는 방법을 제시하여 다양한 추상화 수준과 구체성에서 게임 메카닉 패턴을 제시할 계획입니다.

저는 노스이스턴 대학에서 사용하도록 개발 중인 '게임 메카닉 및 시스템 디자인' 강의용으로 게임 메카닉을 조사하기 시작했을 때, 게임 메카닉의 폭넓지만 전혀 철저하지 않은 목록을 작성하는 것부터 시작했습니다. 이 목록에는 다음과 같은 것들이 포함되어 있었습니다:

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

- 뛰기
- 점프
- 공격

이런 것들은 표면적으로는 간단해 보이지만, 조금 더 신중히 살펴보면 더 복잡해 보입니다. 예를 들어, 걷기와 뛰기 사이에는 어떤 차이가 있는가요? 뛰기에는 대시가 포함됩니까? 점프에는 더블 점프나 공중 대시가 포함되나요? 뛰기는 점프와 어떻게 상호 작용하나요? 이러한 것들이 2D, 3D, 일인칭, 혹은 등각 게임에서는 어떻게 다른가요?

그래서 나는 이 목록을 반복하여 각각을 세부적으로 분해하여 구성 요소를 파악했습니다. 결국 훨씬 더 긴 목록이 되었습니다. 그런데 심지어 그 중 하나를 살펴보니 더 작은 기계적 구성 요소들로 이루어져 있다는 것을 알게 되었습니다. 어느 순간에는 '움직임 벡터', '지면 마찰', 중력과 같은 것들을 설명할 정도로 미세한 '기계적 구성 요소'들로 이루어진 목록이 생겼습니다.

나에게는 이것들이 단일한 기계적 요소를 설명하기에는 충분하지 않아 보였으며, 최상위 수준에서는 '움직임'이나 '전투'와 같은 것들이 기계적으로 만들어진 시스템으로 이루어진 것처럼 개별적인 기계적 요소가 아니라는 것이 보였습니다. 따라서, 이 모든 것은 기계적 설계의 계층 구조를 시사한다고 보았습니다.

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

가장 높은 수준에서 게임 시스템이 있습니다; 이들은 게임플레이의 완전한 부분을 설명합니다; Ori and the Blind Forest에서의 이동이나 Doom Eternal에서의 전투와 같은 것을 생각해보세요. 이러한 시스템은 더 단순하지만 더 집중된 Compound Mechanics로 분해됩니다​​. Doom Eternal의 총 쏘기나 Ori에서의 점프와 같은 것입니다. 이러한 Compound Mechanics는 Atomic Mechanics의 모음으로 구성되어 있습니다; 이들은 매우 구별되고 제한적인데요, Ori에서의 한 번의 점프나 Doom Eternal의 조준과 같은 것입니다. 이 Atomic Mechanics은 더 자세히 분해 될 수 있지만, 그것들의 서브-원자적 부품은 한 가지 메카닉을 설명하지 않으며, 매개변수에 더 가까워집니다. 이러한 매개변수의 개념은 더 많은 고민을 필요로 합니다. 매개변수는 것을 측정하며, 변수에 저장하고 적용합니다. 이것은 Adam과 Dormans가 경제적으로 초점을 맞춘 패턴 언어에 설명한 자원과 비슷해 보입니다. 여기서 제가 설명하는 패러다임에서, 이러한 자원은 Atomic Mechanics이 서로 상호 작용하여 Compound Mechanics을 형성하고, Compound Mechanics이 Game Systems을 형성하는 방식이 됩니다. 이 전체 과정을 거치는 이점은 이제 이러한 자원, Atomic 및 Compound Mechanics, 그리고 Game Systems를 팔로우하게 될 패턴 생성 연습에서 씨앗으로 사용할 수 있다는 것입니다. Atomic Mechanic을 지배하는 패턴을 생성하거나, Compound Mechanic 또는 Game System의 형성을 안내하거나, 자원의 효과를 설명하는 패턴을 만들 수 있습니다. 이러한 패턴은 그 자체로 유용할 뿐만 아니라, 특정 게임의 복잡한 디자인 문제를 설명하는 패턴 언어를 작성할 수 있는 구조를 제공하기 시작합니다. 다음 몇 개의 기사에서는 이러한 메카닉 패턴 연습 중 일부를 소개하고, 언어의 형성과 구조에 어떻게 그 특성을 활용할 수 있는지에 대해 논의할 것입니다.
