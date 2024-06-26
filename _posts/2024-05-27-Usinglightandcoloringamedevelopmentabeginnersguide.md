---
title: "게임 개발에서 빛과 색상 활용하기 초심자를 위한 가이드"
description: ""
coverImage: "/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_0.png"
date: 2024-05-27 16:54
ogImage:
  url: /assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_0.png
tag: Tech
originalTitle: "Using light and color in game development: a beginner’s guide"
link: "https://medium.com/my-games-company/using-light-and-color-in-game-development-a-beginners-guide-400edf4a7ae0"
---

빛과 색은 감정을 전달하고 분위기를 조성하는 데 가장 강력한 도구 중 하나입니다. 그러나 이 도구들을 올바르게 활용하기 위해서는 일정한 예술적 지식이 필요합니다. 이 글에서는 크래시 코스와 앞으로의 방향에 대해 알아볼 거예요!

![이미지](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_0.png)

거의 모든 게임에서, 빛은 매우 중요한 요소입니다. 여기서 말하는 것은 현실적이고 아름다운 그래픽을 만들어내는 빛 뿐만 아니라, 게임 플레이 자체를 지원하는 요소입니다. 즉, 좋은 조명 없이는 게임이 반 준비만 된 것과 다름없습니다. 그렇다고 해서 기술적으로 올바르게 빛을 다루는 것만 중요한 것은 아닙니다. 예술적 측면에서 빛을 이해하는 것이 중요합니다.

안녕하세요! 저는 알렉산더 샤로프라고 해요. 워 로봇: 프론티어 프로젝트에서 시니어 환경 아티스트로 일하고 있습니다. 7년 가까이 게임 산업에서 일하며, 모바일 개발, VR 프로젝트 및 AAA 게임 제작 경험이 있습니다. 이 글은 게임에서 빛을 다루는 기본에 대해 다룰 것이며, 그 중요성을 정확히 이해하고 싶어하는 분들을 위한 것입니다. 또한 색상과 빛이 근본적으로 관련된 개념이기 때문에 색상에 대해서도 이야기할 것입니다.

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

# 게임 및 영화 제작 시 빛의 중요성

우리는 기본부터 시작해봅시다: 빛은 분위기를 표현하거나 개발자가 의도한 정확한 감정을 전달하며, 그림자, 반사 등을 만들어내어 게임 메카닉에도 영향을 미칠 수 있습니다. (짧은 예로, 생존 게임에서 빛은 어둠 속에 휩쓸리고 주변이 보이지 않을 때 플레이어에게 공포와 긴장감을 느끼도록 하는데 사용될 수 있습니다.)

빛은 게임 내 중요한 강조를 만들어내는 가장 강력한 도구입니다. God of War의 2018년 리부트를 시작으로 이를 고려해 봅시다.

![이미지](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_1.png)

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

여기서 빛은 플레이어의 시선을 안내하고 대조를 강조하며, 핵심 초점을 보여줍니다. 즉, 플레이어가 가야 할 곳과 거기에서 기다리는 것이 무엇인지를 보여줍니다. 조명은 또한 특정 감정을 불러일으키는 분위기를 조성하고 "와우" 효과를 줍니다.

파란색과 빨간색 컬러 쉐이드의 대비는 그림에 더 많은 흥미를 더하며, 결과적으로 눈은 그들 사이를 왔다갔다하게 됩니다. 도로도 빛과 색상으로 강조되어 있습니다. 이는 이동 방향을 빠르게 이해할 수 있도록 합니다. 빛은 또한 전경, 중경 및 배경을 분리하여 위치의 부피를 더 잘 볼 수 있게 하고 물체가 합쳐지는 것을 방지합니다. 물론, 플레이어는 보는 것을 철저히 분석하지 않습니다. 모든 것을 무의식적으로 수행합니다.

두 번째 예는 언급한 시리즈의 최신 게임인 God of War: Ragnarök에서 나온 것입니다.

![image](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_2.png)

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

위 스크린샷에서 분위기는 더 어둡고 차가워졌어요. 등잔불에서 나오는 빛의 대비는 배경과 주변의 다른 것들에 명확히 보이게 하여 개발자들이 강조를 만들 수 있도록 도와줍니다. 이것은 플레이어들에게 어떤 포인트가 중요하다는 것을 알리기 가장 간편한 방법입니다. 예를 들어, 등잔불이 없었다면 이 문을 들어갈 수 있다거나 이 방향으로 가야 된다는 것이 명확하지 않았을 것입니다. 여기서 빛은 시각적인 역할뿐만 아니라 게임플레이를 강조하는 데도 도움이 됩니다. 다시 한 번 강조할 점은 눈이 차가운 파란색과 따뜻한 색조를 왔다갔다한다는 것도 있어요.

세 번째와 네 번째 스크린샷은 'Horizon Forbidden West'와 'Burning Shores' 애드온에서 나왔어요.

위 세 번째와 네 번째 스크린샷은 같은 기본 규칙을 따라서: 빛은 분위기를 만들고, 관심 지점을 보여주고, 규모와 방향을 보여줍니다. 이 경우, 이 효과들은 차가운 빛과 따뜻한 빛의 대비, 형상, 물체의 기울기, 그리고 다른 디자인 요소들을 통해 달성됩니다. 빛은 모든 이것들을 강조하고 강화하여, 개발자들이 상상한 아이디어를 전달합니다.

더 구체적으로, 세 번째 스크린샷에서, 따뜻한 빛으로 조명이 비춰지는 거대한 입구를 볼 수 있어요; 차가운 곳 배경 앞에서 높이 서있는 이 빛은 플레이어의 시선을 끕니다. 더불어, 그 자체의 위치 디자인도 매우 효과적입니다: 입구는 다른 모든 것과 두드러지게 대조되는 독특한 형상을 가지고 있습니다.

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

제 4번째 스크린샷에서 삐딱하게 자라난 나무와 인물이 함께 있는 것으로 보아 플레이어의 시선을 그림의 오른쪽으로 이끄는 것 같습니다. 그곳에는 흥미로운 물건이 위치해 있는데, 이 경우엔 모든 것과 잘 어우러지는 외로운 건물이 눈에 띕니다. 여기서의 빛은 계획들을 잘 분리하고, 장소의 규모를 강조하며, 모든 것이 하나의 연속적인 질량으로 융합되지 않도록합니다. 주 건물 뒤의 버려진 도시는 강조되기 위해 특히 짙은 안개에 덮여 있고, 주인공을 다른 물건과 대비시키기 위해 사용되는 다른 여러 물건들과 함께 이 장소에 존재합니다.

# 칼라 심리학

그래서, 이제 일부 게임에서 빛이 어떻게 작용하는지 살펴보았으니, 어떻게 실제로 작업해야 할까요? 올바른 흥미로운 결과를 얻기 위해 따라야 할 규칙은 무엇일까요? 심리학적 측면에서 사람들에게 어떻게 영향을 미치는지에 관심을 가진 색상과 관련된 과학이 있습니다. 이러한 규칙은 예술가들이 특정 플레이어 감정, 분노, 기쁨, 슬픔, 희망 등을 불러일으키기 위해 사용되지만, 예술적 구성 요소에 더 많은 중점을 두며 어떻게 원하는 효과를 이끌어낼 수 있는지 탐구합니다. 색채 이론은 색상이 어떻게 생성되는지, 서로 상호 작용하는 방식, 그리고 어떻게 사람들의 감정과 기분에 영향을 미치는지를 탐구하고 설명합니다.

![이미지](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_3.png)

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

게임 개발 과정에서는 색이 핵심적인 역할을 하는 많은 영역이 있습니다: UI, VFX, 그리고 조명, 환경, 시네마틱 디자인 등이 있죠. 예를 들어, UI/UX에서는 종종 동맹과 적을 구별하기 위한 표식이 필요한데, 게임에서는 보통 동맹을 파란색 또는 초록색으로, 적을 빨간색으로 표시합니다. 이렇게 해야 플레이어가 즉시 친구와 적을 구별할 수 있기 때문이에요.

최근 Diablo IV에서도 이를 확인할 수 있는데, 건강과 마나 바가 명확하게 빨간색과 파란색으로 나뉘어져 있습니다. 건강에 빨간색이 선택된 이유는 인간의 혈액과 활력과 연관이 있습니다. 한편, 마나는 파란-보라색조로 표현되어 있는데, 이는 과거에 무언갈 알 수 없거나 마법적인 것을 떠올리게 한다고 합니다.

환경 디자인 측면에서는 빨간색이 긴장감을 조성하여, 플레이어가 적극적인 조치를 취할 수 있도록 하거나 두려움을 불러일으킵니다. 노란색조는 상호 작용 가능한 물체를 강조하여 환경 내에서 쉽게 눈에 띄게 함으로써 중요한 역할을 합니다.

같은 장면의 여러 프레임을 완전히 다르게 조명할 수 있으며, 이는 게임, 영화, 애니메이션, 그리고 예술 전반에서도 해석할 수 있습니다. 한 때 모든 게 빨강과 보라로 물들여진 분위기는 긴장과 불신을 조성합니다. 그리고 다른 때에는 녹색이나 파란색일 수도 있어서 차분하고 편안한 분위기를 연출할 수 있죠.

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

![Using light and color in game development: a beginner's guide](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_4.png)

# 구성, 톤, 색상

그래서 빛은 게임이나 영화 내에서 프레임 및 구성에서 중요한 요소입니다. 그러나 이 요소는 예술적인 규칙을 고려한다면에만 작용합니다: 선, 모양, 관점, 톤, 색 이론. 빛을 구성할 때 이론을 공부하고 적용하면 게임이 더 표현력 있고 흥미로워집니다.

이론을 살펴보고 빛을 설정할 때 적용하는 것은 게임을 더 표현력 있고 흥미롭게 만듭니다. 기본적인 사항에 대해 알아보겠습니다. 구성부터 시작해봅시다.

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

<table>태그를 Markdown 형식으로 변경하십시오.

사진 속 가장 인기 있는 구성 기법 중 하나는 세분법입니다. 이는 클래식한 구성 원칙으로, 널리 사용되며, 표시하고자 하는 프레임 내에서 가장 흥미로운 요소와 중요한 부분이 이 프레임 내에서 어떤 상상의 선의 교차점에 위치해야 한다는 것을 명시한 것입니다. 이미지의 오른쪽 상단에 설명이 나와 있습니다.

이것이 절대적인 규칙이 아니라 중요한 사항을 중점적으로 포착할 수 있도록 안내하고 편리한 템플릿으로 사용할 수 있는 것을 기억하는 것이 중요합니다. 아래에는 초점 요소의 사용 및 프레임을 요소 및 선분으로 나누는 방법이 작용하여 특정 시점에 중요한 요소나 행동에 중점을 둔 사진 몇 장을 보여줍니다.

구성에 대한 공부를 소홀히 하거나 프레임을 구성하기 위한 특정 규칙을 준수하지 않는다면 전달하려는 정보가 소실될 수 있습니다. 따라서 예술적 기본 사항을 계속적으로 검토하는 것이 중요하며, 3D의 많은 측면은 고전 회화, 일러스트 및 영화 제작에서 나옵니다.

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

일부 작곡 기법을 사용한다고 해서 사진과 장면이 즉각적으로 흥미롭고 영감을 준다는 것은 아니지만, 이를 통해 더 가깝게 다가갈 수 있게 될 거예요.

영화에서 몇 가지 예시를 살펴 보겠습니다. 촬영의 구성과 무대 장치가 감독의 아이디어를 전달하는 데 어떻게 작용하는지 보여줍니다. 초점 및 프레임의 구획과 선분할은 특정 시점에 가장 중요한 요소 또는 행동에 강조를 두는 데 도움이 될 수 있어요.

이제 조합이 게임에서 어떻게 적용될 수 있는지 살펴보죠. 예를 들어, God of War Ragnarök의 장소를 고려해 보세요.

내 의견으로는 "황금 삼각형" 규칙이 여기에 잘 맞는 것 같아요. 화가들은 아마도 다른 규칙을 따랐을 것이지만, 저는 이 구체적인 방법만 분석할 거에요. 편의를 위해 관련된 선을 초록색으로 강조했습니다.

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

플레이어가 이전에 알지 못했던 장소에 도착해서 더 나아가야 할 때를 상상해 보십시오. 우리는 그들을 혼란스럽게 하고 싶지 않으며, 이동 경로를 이해하도록 도와주되 여행 중 지루함이 없도록 하고 싶습니다. 위의 이미지 속 구성은 작가들이 요소들을 배치함으로써 모든 것이 명확하고 가장 중요한 정보가 가장 눈에 띄는 위치에 있도록 하는 좋은 예시입니다.

그러므로, 분명하게 영웅은 대각선으로 바로 앞에 있는 큰 문을 통해 통과해야 할 것이고, 프레임의 왼쪽에 위치한 내려가는 계단이 있습니다. 길이 강조되어 문으로 이어지고 있습니다. 물체들은 주로 양쪽에 위치하고 있는데, 이는 직진하고 싶은 본능적인 욕구를 일으킵니다.

한편, 달빛은 문에 집중을 둔 상태로, 그것을 가장 밝게 보이는 장소로 만듭니다. 나머지 공간이 그림자 속에 있기 때문에 이 대조는 문을 보고 싶어하게 만듭니다. 두 개의 램프는 빛과 그림자의 대조를 보여주기 위해 전경에 놓여져 있으며, 이는 플레이어의 주의를 끌기 위한 역할을 합니다. 여기서 가장 흥미로운 점은 수목과 덩굴이 밝은 붉은 색조로 만들어졌다는 것인데, 이는 달빛과 녹색 식물과는 다르게 최대한의 주의를 끌도록 고안되었습니다. 문 뒤에 위치한 산은 문 너머에 탐험해야 할 큰 공간이 있다거나 일종의 시각적 풍경이 있다는 것을 시사합니다.

이 모든 사항들: 구성, 조명, 색상, 내비게이션 및 물체 배치는 플레이어를 돕고 이야기를 전달하기 위해 설계되었습니다. (물론 싱글 플레이어 및 멀티플레이어 게임에서 작업할 때는 고유한 고려 사항이 있지만, 기본적인 접근 방식은 어디서나 동일합니다.)

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

이 예제에서는 하나의 특정 프레임을 하나의 각도에서 살펴보았습니다. 그러나 캐릭터는 움직이고 주변을 보기도 하므로 어떻게 처리해야 할까요? 개발자들은 게임 플레이 관점에서 중요한 몇 가지 주요 각도를 선택해야 합니다. 이러한 각도는 나머지가 개발 과정 중에 확정될 때까지 기반이 될 것입니다. 개발자들은 플레이어가 위치에 들어오고 떠날 때 어떻게 할지, 게임 플레이에 따라 무엇을 보아야 할지를 숙고해야 하며, 여러 전문가들이 이 노력에 참여합니다: 컨셉 아티스트, 조명 아티스트, 환경 아티스트 등이 있습니다.

구성 기초를 마스터한 후에는 빛과 색상을 다루는 또 다른 중요한 측면에 주목할 가치가 있습니다: 토널리티.

토널리티는 삽화, 게임 및 영화 내에서 평면을 분리하는 기본 도구입니다. 톤이 없으면 모든 것이 하나의 평면으로 합쳐져서 프레임의 주요 아이디어를 식별하기 어렵게 될 것입니다. 프레임을 구분하기 쉽게 만들어주는 몇 가지 잘 알려진 기술이 있습니다: 지브라 조명, 대조 및 프레임을 평면으로 분할하는 것입니다.

지브라 조명은 밝은 공간과 어두운 공간의 번갈아 나타남으로 인해 주목을 끌어냅니다. 이 기술을 사용하면 캐릭터를 강조하고 그들에게 강조를 둘 수 있습니다. 아래에서 캐릭터의 실루엣이 밝은 빛의 배경에 명확히 보이며 그 반대도 마찬가지입니다.

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

![이미지](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_6.png)

대조와 색조를 다루는 또 다른 중요한 기법입니다. 지각적으로는 색이 아닌 톤이 항상 더 중요하다는 것을 기억하는 것이 중요합니다. 톤이 주로 부피를 만들고 시각적으로 그림을 나누는 역할을 합니다.

아래 예시를 살펴보세요: 첫 번째 이미지에 색을 추가하는 것만으로는 색조나 강도를 다루지 않으면 큰 차이가 없습니다. 이미지는 여전히 단조롭고 부분들이 하나로 섞여있는 느낌을 줍니다. 그러나 톤을 조절하면 흑백 그림조차 돋보이며 명확한 대조가 나타납니다. 이미지의 부분들이 서로 어떻게 상호작용하는지 즉시 알아볼 수 있습니다.

![이미지](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_7.png)

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

이미지의 부분을 분리하는 데 도움이 되는 또 다른 좋은 기술은 프레임을 평면으로 나누는 것입니다. 이 방식을 사용하면 플레이어(또는 다른 뷰어)가 서로 다른 객체들을 더 명확하게 구별할 수 있게 됩니다.

아래 늑대 사진을 살펴보죠: 관찰자에게 얼마나 가까운지에 따라 빛과 톤으로 동물들을 그룹화한 것을 보여줍니다. 이 분할이 없다면, "카메라" 바로 앞에 앉아 있는 늑대와 먼 곳에 앉아 있는 늑대를 구별하기가 매우 어려울 것입니다.

![늑대 사진](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_8.png)

톤 키도 고려할 가치가 있는 유용한 도구입니다. 이는 사진, 프레임, 일러스트레이션의 표현 범위에 대한 정보를 제공하는 종류의 안내서입니다. 이 도구는 특히 게임에서 조명을 다룰 때 유용하며, 이야기 속에서 원하는 분위기를 원하는 시점에 달성할 수 있도록 도와줍니다.

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

프로젝트의 전반적인 아트 디렉션과 위치의 작업 설명을 기반으로 톤 키가 선택됩니다:

- 위치는 무엇입니까?
- 어떻게 묘사해야합니까?
- 어떤 분위기를 전달해야합니까?
- 어떤 감정을 일으켜야합니까?

보다 높은 컬러 키는 평온하고 희망을 전달할 수 있으며, 낮은 키는 긴장, 호기심, 위험을 전달할 수 있습니다.

![이미지](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_9.png)

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

아래는 개념 아티스트 스케치에서 가져온 몇 가지 예시이며, 이 중에서 고조도 및 저조도를 모두 포함하고 있습니다. 고조도는 밝은 색이 우세한 그림에서 보이며, 저조도는 어두운 톤이 우세한 그림에서 나타납니다.

![image](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_10.png)

# 색 조합의 종류

이제 색에 대해 조금 이야기해보겠습니다. 색은 여러 가지 분리된 과학에 의해 연구되는 굉장히 거대한 주제입니다. 이에 따라, 초보자에게 유용한 색의 매우 기본적인 내용을 살펴보겠습니다.

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

색상은 다양한 구성 요소를 가지고 있으며, 다양한 조합으로 다양한 효과를 내놓습니다.

![색상과 광과 색채의 사용: 게임 개발을 위한 초보자 안내서](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_11.png)

색조는 본질적으로 기본 색상을 가리킵니다. 채도는 색상의 강도 또는 순도이며, 밝기(값)는 색상의 어둠 또는 밝기로 색의 모양과 깊이를 결정합니다.

프레임이나 일러스트레이션에서 (전문 작업에 대한 이야기를 하고 있다면) 어떤 이유로 색상 조합이 특정 원칙에 따라 색상 바퀴에 근거하여 선택됩니다. 이 개념은 19세기로 거슬러 올라가며, 처음으로 언급된 것은 스위스 예술가 요한네스 이텐이 쓴 'The Art of Color'라는 책입니다.

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

![image](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_12.png)

이론에 따르면 항상 잘 어울리는 서로 다른 색상 세트가 있으며, 특별한 색상 바퀴를 사용하여 이러한 색상을 식별할 수 있습니다. 사실 다양한 조합이 있습니다: 상보적인 색상은 원의 반대편에 위치하며, 단일 색상 팔레트의 경우, 한 가지 색상을 취하고 조톤을 변경합니다; 유사한 색상은 색상 바퀴 상에서 서로 이어진 색상입니다.

각각의 예시를 살펴보겠습니다.

![image](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_13.png)

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

아래에 보면 보충색을 볼 수 있습니다. 컬러 휠 내에서 빨강과 주홍은 초록과 연두의 반대 색상이며, 이 대비는 주목을 끕니다.

![image1](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_14.png)

위에서는 보충색을 볼 수 있으며, 등대로부터 나오는 따뜻한 빛과 전체 위치에 반짝이는 차가운 달빛의 대조를 관찰할 수 있습니다.

![image2](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_15.png)

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

위 Firewatch 이미지에서는 유사한 색상이 따뜻한 분위기를 만들도록 도와줍니다. 동시에 밝기와 톤의 선택이 원하는 강조를 제공합니다.

![Firewatch Image](/assets/img/2024-05-27-Usinglightandcoloringamedevelopmentabeginnersguide_16.png)

위 그림에서는 분할 보충 색상을 사용했습니다. 확실한 파란 머리 색상과 노란색, 버건디 같이 색상 바퀴 반대편에서 온 색상들을 볼 수 있습니다.

위에 보여진 방법들은 강조를 통해 프레임 안에서 관심을 끌기 위해 필요합니다. 모든 방법은 작가의 재량으로 선택되며 명확하고 명백한 경계는 없습니다. 중요한 것은 플레이어나 관람자가 의도한 아이디어를 얼마나 성공적으로 이해하는지입니다.

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

# 대상 관객

광을 다룰 때 또 다른 중요한 측면은 대상 관객을 이해하는 것이며, 이는 작업 방법을 결정할 것입니다. 대상 관객은 대략적으로 두 가지 유형으로 나눌 수 있습니다: 현실주의와 스타일화. (이는 프로젝트, 아트 디렉션, 그리고 의도된 대상 관객에 따라 조건부이며 크게 영향을 받습니다)

작업 양면에서, 현실주의는 현실 세계에 가능한 한 가깝게 사진을 전달해야 한다는 임무로 인해 항상 더 어렵습니다: 현실적인 조명, 재료, 효과 등이 필요합니다. 기술적으로 말하면, 게임이 영화와 달리 미리 녹화되지 않고 실시간으로 실행되므로 그림을 처리하는데 많은 리소스가 필요합니다. 그리고 타협할 여지가 없습니다: 뭔가를 망친다면 관객이 즉시 알아차릴 것입니다.

스타일화는 강조색과 단순화된 형태에 초점을 맞춘 간소화된 방식입니다.

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

물론 창의성에 제한은 없어요: 스타일을 섞고 어울리게 조합할 수 있어요. 모든 것은 프로젝트의 총 예술적 방향에 달려 있죠.

특히 빛과 색에 관한 이야기를 할 때, 사실주의는 중립적이고 차가운 색조를 특징으로 하며 뚜렷한 강조가 없어요. 예를 들어, 영화 "덩컥"과 게임 콜 오브 듀티: 제2차 세계대전의 이미지들이 여기 있어요.

스타일화를 할 때는 완전히 다른 방식으로 작업해요. 밝은 색상과 강조로 주목을 끌며 게임의 전통을 완화하죠.

여기에 포트나이트와 오버워치의 몇 장면이 있어요. 실제 세계에서는 우리가 이런 색상이나 조명을 보지 못하지만, 스타일화된 것은 관객으로 하여금 이러한 불일치를 비판하지 않고 모든 것이 조화롭게 보이게 합니다.

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

라이트와 컬러는 게임과 영화 모두에서 분위기를 조성하고 감정을 전달하는 가장 강력한 도구 중 하나입니다. 그러나 이를 올바르게 활용하기 위해서는 일정 수준의 예술 지식이 필요합니다. 또한 다양한 게임을 플레이하고 분석하며 가능하다면 전문 서적을 읽어보는 것이 좋습니다 - James Gurney의 "Color and Light"로 시작하면 좋을 것 같아요. 이 모든 것이 여러분의 기술을 크게 향상시키는 데 도움이 될 것이며, 나머지는 이론을 계속 실천하는 문제에 불과해요!
