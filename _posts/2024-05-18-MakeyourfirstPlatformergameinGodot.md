---
title: "당신의 첫 번째 플랫포머 게임을 Godot에서 만들어보세요"
description: ""
coverImage: "/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_0.png"
date: 2024-05-18 15:58
ogImage:
  url: /assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_0.png
tag: Tech
originalTitle: "Make your first Platformer game in Godot"
link: "https://medium.com/@sindhoorarao3/make-your-first-platformer-game-in-godot-1b6da26f3148"
---

# 소개

안녕하세요! 이 블로그는 Godot을 사용하여 간단한 플랫포머 게임을 만드는 방법을 소개하고 있어요! Godot은 사용자 친화적 인터페이스와 포괄적인 게임 개발 기능으로 유명한 견고한 게임 엔진이에요. 그러나 게임 개발에 뛰어들기는 쉽지 않아요. 특히 초보자에게는 물 속에서 숨 쉬는 것 같은 느낌일지도 모르죠. 하지만 걱정 마세요! 이 여행을 시작하며 Godot의 기본원리를 탐구하고, 작동 방식을 이해하며, 플레이어 캐릭터, 게임 세계를 만들고 플레이어를 도전할 제약 조건을 구현해볼 거에요. 함께 게임을 만들며 배움을 즐겁게 만들어봐요!

# Godot 재정리

Godot은 직관적인 인터페이스를 제공해 환경을 설계하고, 플레이어 캐릭터를 만들고, 애니메이션을 구현할 수 있는 다재다능한 게임 엔진이에요. Godot은 파이썬과 유사한 스크립팅 언어인 Gdscript를 사용해 새로운 이용자들에게 접근하기 쉽게 해줘요. Godot에서는 모든 요소가 노드로 표현돼요. 플레이어 캐릭터든, 오디오 소스든, 게임 개체든, 모두 씬 내에 구성된 노드들이에요. 게다가 씬들은 서로 중첩하여 복잡한 레벨 디자인과 상호 작용을 할 수 있어요.

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

저희 게임은 세 가지 필수 구성 요소로 구성되어 있습니다:

- 플레이어 → 우리의 주요 캐릭터를 대표합니다.
- 세계 → 플레이어가 이동하고 상호 작용하는 곳
- 킬존 → 플레이어 이동을 제한하는 도전적인 영역

누군가가 도약하는 것을 막는 것처럼, 우리 게임의 제한은 흥미와 도전을 더합니다. 예를 들어, 우리는 플레이어가 공허로 떨어졌을 때 플레이어의 죽음과 리스폰을 어떻게 처리할지 살펴볼 것입니다. 아래 인포그래픽은 장면, 노드 및 중첩 기능에 대한 시각적 개요를 제공합니다.

<img src="/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_0.png" />

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

# 미리보기

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*CwIotC0mkJa6RKpamFXY2w.gif)

# 설정

우선적으로, 여기서 Godot을 다운로드합니다. 이 프로젝트에 사용된 에셋은 Kenney의 것입니다.

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

Godot을 시작하고 +새로 만들기 버튼을 클릭하고 프로젝트 이름을 입력합니다. 그런 다음 폴더 만들기를 클릭하고 프로젝트 경로를 지정합니다. 마지막으로 만들기 및 편집 버튼을 클릭합니다.

![이미지](/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_1.png)

FileSystem에 assets, scripts 및 scenes라는 3개의 폴더를 생성합니다. 이제 Kenney에서 다운로드한 에셋을 assets 폴더로 드래그 앤 드롭할 수 있습니다.

![이미지](/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_2.png)

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

# 플레이어 씬 만들기

플레이어를 배치할 게임 환경이 필요합니다. 게임 씬을 구축하기 위해 새 노드 만들기 아래에 있는 2D 씬 옵션을 선택하고 game으로 이름을 바꿉니다. 파일을 scenes 폴더에 저장하세요.

이제 플레이어 씬을 만들어 봅시다. 이를 위해 + 아이콘을 클릭하여 새 씬을 생성합니다. 플레이어의 루트 노드는 CharacterBody2D가 될 것입니다. 새 노드를 추가하려면 Cmd(Ctrl)+A를 누르고 CharacterBody2D를 검색하세요. 이는 스크립트에 의해 이동되는 캐릭터용으로 특별화된 2D 물리 바디입니다.

![이미지](/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_3.png)

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

<img src="/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_4.png" />

이제 플레이어에 그래픽을 추가해야 합니다. 이를 위해 Cmd(Ctrl)+A를 눌러 AnimatedSprite2D를 검색한 후 생성을 클릭하세요. 비디오의 단계에 따라 애니메이션된 플레이어를 씬에 추가하세요. 가로 및 세로 타일은 타일맵의 크기에 따라 조정되어야 함을 유의하세요.

그 후에 CharacterBody2D에 노드에 모양이 없어 다른 노드와 충돌하거나 상호작용할 수 없으며 노드에 노란색 경고 표시가 나타납니다. 이를 해결하기 위해 물리 노드를 추가하여 물리 엔진이 작동할 수 있는 노드를 추가해야 합니다. 새 노드를 추가하려면 Cmd(Ctrl)+A를 눌러 CollisionShape2D를 검색하세요. 인스펙터에서 모양 버튼을 클릭하여 모양을 정의하고 캐릭터에 적합한 모양을 선택하세요. 저는 캡슐 모양을 사용합니다. 필요에 따라 크기를 조정하고 씬을 저장하세요.

<img src="/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_5.png" />

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

이 플레이어를 게임에 넣으려면 플레이어 씬을 게임 씬으로 끌어다 놓기만 하면 됩니다. 제어하고 표시되는 내용을 보려면 카메라를 추가해야 합니다. 이를 위해 Cmd(Ctrl)+A를 누르고 카메라를 검색하세요. 생성을 클릭하고 게임에서 보이고 싶은 영역을 조정하세요.

![이미지](/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_6.png)

변경 사항을 저장하고 실행 버튼을 클릭하여 게임을 시작하고 캐릭터를 볼 수 있습니다.

![이미지](/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_7.png)

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

플레이어가 이동하지 않네요 :(
이 문제를 해결하려면 플레이어 노드를 열고 필터 막대 옆에있는 Attach new script 버튼을 클릭하세요. 기본 템플릿을 선택한 상태로 유지한 채, 경로에서 스크립트 폴더를 선택하고 생성을 클릭하세요.

기본 템플릿을 사용하면 플레이어가 화살표 키와 스페이스바를 사용하여 좌우로 이동하고 점프할 수 있게됩니다. 캐릭터 속도와 점프 속도는 스크립트 내에서 이 값을 변경하여 조절할 수 있습니다.

이제 게임을 재시작하세요!

캐릭터가 바로 떨어진 것이 보이나요? 이는 플레이어가 서 있을 땅이 없기 때문입니다! 플레이어는 이제 준비가 되었으니, 세계를 생성해봅시다!

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

# 월드 씬 만들기

이제 플레이어가 서 있을 실제 지면을 만들고 있기 때문에 타일맵을 사용합니다. 배경은 간단히 배경 이미지를 씬으로 드래그 앤 드롭하여 추가합니다. 비디오를 따라가며 세계를 만들어 보세요.

카메라가 플레이어를 따라가도록 만드려면, 카메라2D 노드를 플레이어 노드로 드래그하기만 하면 됩니다. 게임의 가장 아래 부분에서 카메라 중앙까지의 거리를 측정하고, 해당 수치를 카메라 인스펙터의 하한 제한에 추가하여 카메라가 아래쪽으로 빈 공간을 표시하지 않도록 합니다.

# 킬존 만들기

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

킬존을 생성하려면, 인스펙터에서 캐릭터의 충돌 레이어를 1에서 2로 변경합니다. 이렇게 하면 캐릭터가 새 레이어에 배치되어 킬존 영역을 캐릭터에게만 할당할 수 있습니다.

![킬존 만들기](/assets/img/2024-05-18-MakeyourfirstPlatformergameinGodot_8.png)

킬존을 만드는 방법은 비디오를 따라하면 됩니다. 주요 단계는 킬존이라는 Area2D 씬을 만들고, 이를 게임에 추가한 다음, worldBoundary와 충돌 모양을 만들어 캐릭터가 경계를 넘어서면 캐릭터를 죽이는 부분입니다. 경계는 작아 보이지만 무한히 이어집니다. 죽이고 다시 생성하는 부분은 스크립트에서 처리됩니다.

킬존에 사용된 스크립트:

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

# 마무리

축하해요! 우리는 Godot을 사용하여 게임 개발의 흥미로운 세계로 첫 걸음을 내딛었어요. 간단한 플랫포머를 만들면서 노드, 씬 그리고 GDScript로 스크립팅하는 기본 개념을 다뤘어요.

기억해요, 게임 개발은 코딩만큼 반복과 배움의 과정이에요. 게임 개발의 세계는 광활하고 다채롭고, 각 프로젝트를 통해 뛰어남에 한 걸음 가까워져요. 이 블로그가 유익하게 느껴졌으면 좋겠어요. 언제든지 피드백과 제안을 기다리고 있어요.

즐거운 코딩하시고, 여러분의 게임이 만드는 과정이 플레이하는 것만큼 즐거워지길 바래요!

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

## 참고 자료

Godot 문서
Brackeys Godot 초보자 튜토리얼
