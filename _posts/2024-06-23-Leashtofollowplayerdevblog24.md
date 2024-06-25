---
title: "플레이어를 따라오는 리쉬 구현 방법  개발 블로그 24"
description: ""
coverImage: "/assets/img/2024-06-23-Leashtofollowplayerdevblog24_0.png"
date: 2024-06-23 22:10
ogImage:
  url: /assets/img/2024-06-23-Leashtofollowplayerdevblog24_0.png
tag: Tech
originalTitle: "Leash to follow player — devblog 24"
link: "https://medium.com/@forest.whiskers/leash-to-follow-player-devblog-24-5cb2a5547456"
---

게임에 새로운 도구가 추가되었습니다 - 리드입니다. 이 도구는 동물을 "플레이어를 따르도록" 또는 "따르지 않도록" 전환하는 데 사용됩니다.

저는 이 전환을 어떻게 구현할지 오랜 시간 고민했고 처음에는 리드를 사용하고 싶지 않았습니다. 그러나 마침내 모든 옵션 중에서 리드가 가장 논리적이고 직관적인 것으로 결론이 났습니다. 실제로 동물은 리드에 묶여 있는 것이 아니며, 플레이어에 물리적으로 묶여 있지 않고 먹이를 먹으러 다닐 수 있습니다. 오히려 "나를 따라와" 또는 "나를 따르지 말아라"라는 표시와 비슷합니다. 현실적으로 들리지 않을 수 있지만, 게임 속에서는 (적어도 저에게는) 일반적인 게임 논리처럼 느껴집니다.

동물 위로 마우스를 가져가면 이제 "따르는 중" 또는 "따르지 않는 중"이라고 표시됩니다. 나중에는 이를 텍스트 없이 표시하고 싶지만, 아직 최적의 방법을 고려 중입니다 (동물의 외관을 변경할까요? 영구적인 라벨을 추가할까요? 캐릭터에 연결하는 선을 그릴까요? 동물 주변에 어떤 효과? 따르는 동물들의 초상화를 어딘가에 그릴까요?).

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

![2024-06-23-Leashtofollowplayerdevblog24_1](/assets/img/2024-06-23-Leashtofollowplayerdevblog24_1.png)

I also drew animations for the kittens to eat and sleep, so now they eat and sleep nicely.

![2024-06-23-Leashtofollowplayerdevblog24_2](/assets/img/2024-06-23-Leashtofollowplayerdevblog24_2.png)

I slightly adjusted the logic for how an animal recognizes that it’s near food or a bed. If the cat’s rectangle and the bed’s rectangle overlap, then the cat is at the target. Previously, an overlap was counted if they intersected by even one pixel. Now there are three types:

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

- 적어도 한 픽셀 이상 겹침
- 중간으로 겹침
- 완전히 겹침

고양이는 이제 침대 위에 완전히 올라가서 눕고 자야 해요.

![고양이](https://miro.medium.com/v2/resize:fit:1400/1*GcFnFoDejJB1cV4l-uP2cg.gif)

그리고 고양이는 음식 위에 반이라도 올라가서 먹어야 해요.

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

![Image](https://miro.medium.com/v2/resize:fit:1400/1*NGkoOBqXnR9MaUpWq6e40g.gif)

현재, 새끼 고양이는 플레이어가 먹는 것과 같은 음식을 먹을 수 있고 배부름도 100%가 됩니다. 하지만 미래에는 그들만의 음식이 생기고, 그 음식은 다른 배고픔 영향을 가질 것입니다.
