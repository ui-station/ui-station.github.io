---
title: "중간 과정 프롬프트를 Shorten으로 개선하세요"
description: ""
coverImage: "/assets/img/2024-05-17-ImproveYourMidjourneyPromptsWithshorten_0.png"
date: 2024-05-17 20:14
ogImage:
  url: /assets/img/2024-05-17-ImproveYourMidjourneyPromptsWithshorten_0.png
tag: Tech
originalTitle: "Improve Your Midjourney Prompts With  shorten"
link: "https://medium.com/ai-advances/improve-your-midjourney-prompts-with-shorten-9f34c15e1a1a"
---

<img src="/assets/img/2024-05-17-ImproveYourMidjourneyPromptsWithshorten_0.png" />

미드절니(Midjourney)는 상세하고 현실적인 AI 생성 이미지로 유명합니다. 그러나 미드절니의 모델이 당신이 제시한 프롬프트를 정확히 생성하는 것은 도전일 수 있습니다.

예를 들어, 이 기사의 제목 이미지의 경우 저는 리본을 가로지르는 낡은 가위를 요청했습니다. 그러나 받은 이미지는 모두 리본 근처를 떠도는 가위의 이미지입니다.

/shorten 명령어는 프롬프트를 분석하는 유용한 도구입니다. 미드절니의 알고리즘이 프롬프트를 해석하는 방식을 파악하고, 원하는 이미지를 만들기에 더 적합한 더 짧은 프롬프트를 제안해줍니다.

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

이 기사에서 Midjourney Discord 인터페이스에서 `/shorten` 명령어가 어떻게 작동하는지 알게 될 것입니다. 그런 다음 두 가지 예제를 통해 안내해 드리겠습니다:

- 복잡한 장면을 /shorten 명령어로 분석하여 prompt를 개선하기
- prompt의 길이를 80% 줄이기

## /shorten 명령어 동작

Midjourney 봇은 프롬프트를 단어 또는 짧은 구문으로 구성된 토큰으로 분할합니다. 그런 다음 이러한 토큰과 교육 데이터의 연관성을 기반으로 이미지를 작성합니다.

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

직감과는 반대로 간결하고 간결한 프롬프트가 긴 설명보다 더 나은 성능을 발휘할 수 있다고 합니다. Midjourney는 시적인 언어와 과도한 설명이 이미지에 예상치 못한 객체들이 나타나게 할 수 있다고 설명합니다.

사용자들은 Discord 채팅 인터페이스에서 /imagine 키워드로 이미지 프롬프트를 제공하고 알고리즘은 4가지 가능한 이미지 그리드를 생성합니다. 이들은 더 세부적으로 다듬을 수 있으며, 섬세하고 창의적인 업스케일링 및 인페인팅을 통해 더 발전시킬 수 있습니다.

/imagine 와 마찬가지로, /shorten 도 Discord의 Midjourney 봇 채널에서 호출할 수 있습니다. 이것은 내 제목 이미지 프롬프트를 분석한 결과입니다:

![2024-05-17-ImproveYourMidjourneyPromptsWithshorten_1.png](/assets/img/2024-05-17-ImproveYourMidjourneyPromptsWithshorten_1.png)

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

사용자에게 추가 옵션이 두 가지 더 있어요:

- 해당 버튼을 클릭하여 다섯 가지 중 한 가지 단축 이미지 프롬프트를 기반으로 이미지를 생성합니다.
- 각 토큰에 할당된 중요도를 표시하려면 “세부 정보 보기”를 클릭합니다.

후자의 경우 Midjourney 봇은 각 토큰에 할당한 가중치를 반환합니다. 더 높은 가중치를 가진 토큰은 더 중요하다고 간주됩니다.

알고리즘은 이미지 구성 요소와 각각의 색상, 스타일에 중점을 둡니다. 구성은 덜 중요한 것으로 보이며 리본에 쓰인 단어의 설명도 마찬가지입니다.

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

## 가족 모습

다음에는 /shorten 명령어를 사용하여 가족 모습을 만드는 데 도움을 받겠어요. 네 개의 이미지 그리드 중에서, 주어진 프롬프트와 가장 일치하는 이미지를 항상 선택했어요.

저는 Midjourney에게 아늑한 가족 모습을 만들어 달라고 요청했어요. 그 가족 구성원들과 애완동물의 자세한 설명, 각자의 직업, 이미지에서의 위치를 포함하여 구체적으로 설명했고, 이미지 스타일은 사실적인 것으로 지정했어요.

![가족 모습](/assets/img/2024-05-17-ImproveYourMidjourneyPromptsWithshorten_2.png)

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

생성된 장면에는 요청한 모든 객체들이 포함되어 있지만, 구성이 예상대로 되지는 않았어요. 예를 들어, 부엌 카운터 위에 있는 태비 고양이 한 마리 대신 바닥에 두 마리가 있었어요. 바닥에는 장난감이 있지만, 아이들이 그것들과 놀고 있지는 않았어요. 하지만 정말 당황한 건 창문의 아랫부분이 빠져있던 거에요.

/shorten을 사용하여 Midjourney 봇이 제 원래 프롬프트를 분석한 결과를 제공해 주었어요. 봇은 가장 중요한 단어들을 굵은 글꼴로 강조했어요.

분석 결과 사람들과 애완동물이 중요하게 여겨졌어요. 그러나 프롬프트의 가장 관련된 부분은 설정이에요: 사실적이고, 환한, 아침, 가족, ... 흥미로운 점은 모피 색상 "태비"가 고양이 자체보다 훨씬 중요하게 여겨졌다는 것인데요 - 아마도 "태비" 객체를 요청했을 때 "고양이"가 자동으로 추론된다는 힌트일지도 모르겠어요?

봇은 또한 프롬프트를 다섯 가지 요약된 버전으로 제안했어요. 그것들은 프롬프트의 인식에 따라 중요한 부분을 포착하고 적절하지 않은 세부사항은 제외한 것으로 보입니다.

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

다음 패널에서는 "사실적인, 화창한 아침 가족 장면, 부엌에서 스무디를 준비하는 여성, 두 아이, 장난감 자동차로 뒤덮인 바닥. 태비 고양이, 과일, 커튼"에서 "사실적인, 화창한, 가족, 스무디"로 줄어든 프롬프트에 대한 이미지가 보입니다.

제가 만들고 싶은 장면과 일치하는 줄어든 프롬프트는 찾지 못했습니다. 알고리즘을 구성에 더 집중하도록 하기 위해, 스타일보다는 구성에 더 집중하게끔 아래의 수정된 프롬프트를 만들기로 결정했습니다.

더 이상 사진을 사실적으로 요구하지 않고, 태비 고양이와 다른 물건들이 바닥이 아니라 카운터 위에 놓이도록 요청합니다. 이것이 스타일보다 더 중요하다고 생각했기 때문입니다.

![이미지](/assets/img/2024-05-17-ImproveYourMidjourneyPromptsWithshorten_3.png)

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

## 시적인 언어 줄이기

다음 예시에서, 저는 시적이고 화려한 이미지 프롬프트를 만들 수 있도록 ChatGPT의 도움을 받았습니다:

미드저니 봇이 이를 줄였습니다.

이것은 총 5개 중 3번째 프롬프트였지만, 그 이후로는 포도와 키위가 사라지기 시작했습니다.

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

다음 패널은 두 프롬프트를 나란히 비교한 것을 보여줍니다. 원본 38단어 중 8단어만 남은 단축된 프롬프트는 장면을 충실하게 재구성합니다.

![이미지](/assets/img/2024-05-17-ImproveYourMidjourneyPromptsWithshorten_4.png)

## 결론

첫 번째 복잡한 프롬프트 예제에서, /shorten 명령어와 Midjourney 봇이 제공한 단축된 프롬프트는 내 문제를 직접 해결하지 못했습니다.

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

`/shorten`와 상호 작용하면 알고리즘을 더 잘 이해할 수 있었어요. 각 토큰에 부여하는 가중치를 알면, 지나치게 강조된 단어를 제외하고 다른 곳에서 설명을 개선할 수 있었어요.

예를 들어, 알고리즘이 "사실적인"과 "스무디"라는 단어에 이렇게 중요성을 부여할 것이라고는 상상하지 못했을 겁니다. 그 이미지를 만드는 과정에서 그 단어들이 장면을 잡아내는 데 꼭 필요하지 않은 것을 깨달았어요.

보다 간단한 두 번째 장면에서는 /shorten을 사용하여 원래 프롬프트의 장황한 언어를 줄일 수 있었어요. 구성 요소는 훨씬 짧은 프롬프트로 보존되었어요.

저는 /shorten 명령을 Midjourney 봇과의 소통 수단으로 생각해요. 어쨌든, 이것은 어떻게 특정 이미지를 생성하는지 설명해 주지 않아요. 각 토큰의 가중치를 알고 있으면, 사용자는 생성된 장면이 그들의 원래 아이디어와 일치할 때까지 프롬프트를 조정해 나갈 수 있어요.
