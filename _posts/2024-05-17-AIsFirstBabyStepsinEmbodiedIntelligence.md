---
title: "살아있는 인공지능의 첫 걸음, 바디 인텔리전스"
description: ""
coverImage: "/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_0.png"
date: 2024-05-17 19:35
ogImage:
  url: /assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_0.png
tag: Tech
originalTitle: "AI’s First Baby Steps in Embodied Intelligence"
link: "https://medium.com/@ignacio.de.gregorio.noblejas/ais-first-baby-steps-in-embodied-intelligence-2c4a90e61424"
---

두 개의 세계가 충돌하고 있어요.

우리는 방금 두 가지 분야에서 로봇 공학의 상당한 도약을 목격했어요. Figure AI의 말하는 로봇과 Google의 일반적인 에이전트 SIMA가 그겁니다.

하지만, 아니요, 이것은 일반적인 인공 지능(AGI)은 아니에요. 일부 사치스럽지만 입증되지 않은 주장이 돌아다니고 로봇들이 우리를 다 죽일 거라고 말하진 않을 거예요.

그러나 두 소식이 이미 자체로 흥미롭지만, 그들 간의 시너지는 내 관점에서 AI 구현 지능의 첫 번째 발걸음이라고 생각해요.

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

# GPT-4의 구현

그러면 Figure AI는 뭔가요?

Figure AI는 로봇 회사로, CEO의 말에 따르면 "위험하고 원하지 않는 직업이 필요하지 않도록 하여 미래 세대가 더 행복하고 의미 있는 삶을 살도록 허용하는 로봇을 구축하고 있습니다."

이미 유사한 웅장한 사명을 가진 회사들을 들어보셨을 것입니다. 그러나 OpenAI, NVIDIA, Jeff Bezos, 그리고 Intel이 시장에 제품이 없는 회사에 대해 26억 달러 평가액에서 6억 7500만 달러 시리즈 투자를 한다면, 그들이 무엇인가를 찾고 있다는 것을 알 수 있습니다.

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

그러나 왜 모두가 이 회사에 대해 이야기하는 걸까요? 이 비디오 때문입니다.

그 링크를 클릭하는 것을 강력히 권장합니다. 간단히 말하자면, 이 비디오를 통해 사람과 상호작용하는 로봇이 여러 작업을 민첩하게 수행한다는 것을 보여줍니다.

흥미로운 점은 Figure AI의 로봇의 핵심에는 GPT-4V, OpenAI의 주요 다중모달 대형 언어 모델인 MLLM이 있다는 것입니다.

다시 말해, Figure AI의 로봇은 '화신 ChatGPT'의 처음으로, 즉 LLMs가 실체화된 작업도 할 수 있는 능력이 생긴 것입니다.

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

그러나 대부분의 사람들이 놓칠 수도 있는데, 로봇이 이전 동작에 대한 이유를 설명하면서 사람에게 작업을 수행하도록 요청하는 순간에 주목하여 주길 바랍니다.

이 질문이나 요청이 사소한 것은 아니라는 점을 분명히 이해해야 합니다. 모델이 동시에 여러 작업을 수행할 수 있는 능력을 보여주기 위해 일부러 그랬던 것입니다.

구체적으로 말하자면, 그들은 GPT-4를 세밀하게 조정하여 텍스트와 작업 표현을 출력하도록 만들었는데, 전자는 보코더를 통해 음성으로 디코딩되고, 후자는 액추에이터 동작으로 디코딩되어 몸을 움직이도록 합니다.

Figure AI의 로봇의 기본 메커니즘에 대해서는 많이 알지 못하지만, Deepmind의 RT-2 모델과 같은 예시를 통해, 우리는 이미 LLM을 로봇 동작이나 텍스트를 출력하도록 훈련하는 방법을 증명한 연구자들이 있기에 꽤 좋은 아이디어를 얻을 수 있습니다.

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

![image](/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_0.png)

그러나 RT-2는 GPT-4의 능력에 가까운 두 LLM인 PALM-E와 PALI-X를 사용하여 훈련된 것을 고려할 때, Figure AI의 로봇 뒤에 있는 LLM이 이 모델이 지금까지 존재한 가장 고급 비전-언어-행동 모델일 수 있다는 주장이 가능합니다.

하지만 현실적으로 생각해 봅시다.

그것은 여러 차례 연습된 것일 수 있는 매우 제한된 데모였습니다. 사실, 로봇이 일반적인 용도의 능력 측면에서 아직 매우 초기 단계에 있다는 것은 거의 확실합니다.

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

따라서, 이 로봇들의 여정에서 다음 단계를 상상하기 위해 구글이 방금 공개한 것에서 영감을 받을 수 있습니다.

SIMA, 3D 총론적 에이전트.

하지만 이번에는 Figure AI의 경우와 달리, 에이전트에 대해 훨씬 더 많은 정보를 갖고 있습니다.

# SIMA, 최초의 진정한 총론적 에이전트?

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

Google의 확장 가능한 인공 지능 멀티월드 에이전트(SIMA) 프로젝트는 어떤 3D 환경에서 임의의 언어 명령을 이해하고 실행할 수 있는 인공 지능(AI) 시스템을 만들고자 합니다.

이 계획은 언어를 지각 및 다양한 가상 세계에서 실제 행동과 결합하여 처리함으로써 일반적인 AI 개발에서 중요한 과제에 대처합니다. 이는 연구 환경 및 상업 비디오 게임을 포함한 다양한 가상 세계를 대상으로 합니다.

간단히 말하면, SIMA는 언어 요청을 받아들이고 이를 3D 환경에서 키보드 및 마우스 조작으로 변환합니다.

그러나 고려해야 할 중요한 요소는 다음과 같습니다:

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

이 에이전트들의 목표는 "자원을 수집하고 집을 짓는" 등 사용자가 제시하는 자연어 지침대로 작업을 수행하는 것입니다.

간단히 말해, 모델은 해당 환경에서 상호작용할 때 사람들과 똑같은 입력을 갖고 있으므로 사람들보다 약점이 없다는 것을 의미하며, 이는 기접근 API나 이와 유사한 것에 대한 접근 권한이 없다는 것을 의미합니다.

결과적으로, 요구된 작업을 수행하는 유일한 방법은 해당 작업을 수행하는 데 사람이 수행할 키보드 및 마우스 작업을 예측하는 것입니다.

SIMA 에이전트는 다음 구성 요소로 이루어져 있습니다:

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

아래는 마크다운 형식으로 변경한 텍스트입니다.

![이미지](/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_1.png)

- 인코더:

  - 텍스트 인코더: 언어 명령을 모델이 해석할 수 있는 임베딩으로 번역합니다.
  - SPARC 개발을 기반으로 한 이미지 인코더.
  - 비디오 인코더.

2. Multi-modal Transformer + Transformer XL: 두 개의 트랜스포머 아키텍처로, 전자는 모달 간 교차 어텐션을 수행하고, 후자는 이전 상태를 취해 새로운 상태를 식별합니다.

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

3. 정책: 선택된 행동이 600가지 가능한 기술 중 하나로 결정되는 분류 헤드

여기에는 해석할 내용이 많기 때문에 단계별로 진행해 봅시다.

## 세계 처리

대부분의 최신 모델들에서와 마찬가지로, 첫 번째 단계는 입력을 "인코딩"하는 것입니다.

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

일반적인 용어로 설명하자면, 입력 데이터(텍스트와 비디오)를 가져와 해당 인코더를 사용하여 이러한 데이터 포인트를 벡터 임베딩으로 변환하는 것이 아이디어입니다.

![image](/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_2.png)

하지만 왜 이 작업을 하는 것일까요?

이 변환을 수행함으로써 각 요소가 해당 개념의 의미론을 캡처하는 밀집 벡터로 표현됩니다.

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

다시 말해, 의미론적으로 유사한 개념은 유사한 벡터를 갖게 되어 벡터 공간에서 표현되었을 때 더 가까이 위치하게 됩니다:

![Concept Vectors](/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_3.png)

또 다른 중요한 이점은 '개념을 벡터로 변환함'으로, 인간이 아주 무의식적으로 수행하는 '우리 세계를 이해하는' 행위를 수학적인 연습(컴퓨터에 이상적)으로 변환하여, 개념이 이제 수치로 표현된다는 것입니다.

특히, 이 모델은 이러한 벡터들 사이의 유사성(그들 사이의 거리)을 계산하여, 어떤 개념이 다른 것들과 유사한지를 알아냅니다.

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

예를 들어, 모델은 '개'와 '고양이'가 유사한 속성 (포유류, 길들여진 동물, 네 다리 등)을 공유하는 유사한 개념을 나타낸다는 것을 알 수 있습니다.

분명히 말씀드리자면, AI가 각 숫자를 다루는 방식은 아니며, AI가 중요시하는 것은 벡터 간의 근접성입니다. 만약에 도움이 될 경우, 벡터의 의미를 단순히 벡터 자체로만 생각하지 마십시오 (우리는 AI가 정말 '개'가 무엇인지 아는지 확신할 수 없습니다) 대신, 가장 가까운 이웃들의 합으로 생각하고, 모델에게 다른 것들과 유사하다는 신호를 보내는 것으로 여기면 됩니다 (강아지는 고양이와 유사하며 문과는 다릅니다).

비슷한 이유로 유사성은 데이터 유형이 다른 상황에 집중하는 데 중요한 역할을 합니다.

다양한 데이터 유형에서 나온 벡터로 오는 여러 모달 상황에 집중하는 데 유용합니다.

기본 아이디어는 '개'라는 단어와 '개의 이미지'가 유사한 벡터를 공유하도록 원한다는 것으로, 이것은 모델에게 두 가지가 동일한 개념을 나타낸다는 것을 알려줍니다.

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

하지만 텍스트와 이미지는 구조적으로 매우 다른 데이터 유형이기 때문에 서로 다른 인코더가 필요합니다.

이것은 문제입니다. 비슷한 개념에 대해 유사한 임베딩을 생성하도록 보장하며 별도의 인코더를 훈련해야 합니다.

이 문제를 해결하기 위해 SIMA는 SPARC 이미지 인코더를 사용합니다.

매우 최근에 개발된 SPARC 인코더는 대부분의 다른 이미지 인코더와 매우 유사한 방식으로 훈련됩니다(대비 학습을 사용), 그러나 미세 구체적인 세부 사항을 더 잘 캡처할 수 있습니다.

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

가장 흔한 이미지 인코더들은 이미지의 지역 세부사항을 제대로 포착하지 못하는 문제가 있습니다.

네, 그들은 이미지가 무엇에 대해인지를 알려줄 수 있지만, 이미지의 전역 의미를 설명하는 데 도움이 되지 않는 중요한 작은 세부사항을 놓치기도 합니다. 그럼에도 불구하고 많은 경우에 중요합니다.

SPARC는 유사한 방법을 제안하지만 매우 흥미로운 요소를 추가합니다.

예를 들어, "바구니 속 고양이와 개"를 나타내는 이미지-텍스트 쌍이 있다고 가정해 봅시다.

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

<img src="/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_4.png" />

- 먼저, SPARC는 이미지를 패치로 나눕니다.
- 그런 다음 각 패치에 대해 텍스트 설명 중 하나의 토큰을 할당합니다. 예를 들어, 패치가 개의 일부를 나타내면 "개"라는 단어가 할당됩니다.
- 이 작업은 모든 패치(왼쪽 색상 그리드의 수직 열)에 대해 수행되며, 텍스트 설명의 여러 측면을 다루는 패치의 경우 각각에 가중치가 할당됩니다.

- 특정 개념에 할당된 모든 패치를 가지고 있으면 각 패치가 '개'와 같은 특정 단어에 부여하는 가중치를 그룹화하여 이 그룹화된 임베딩을 실제 단어와 비교합니다. 그러나 여기에는 중요한 점이 있습니다. 이미지 전역에 대해 적용하는 대신, 모든 개념에 대해 이를 다섯 번 적용합니다.

하지만 모든 이 작업을 하는 이유는 무엇일까요?

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

지역성. 이미지의 특정 부분이 이제 특정 개념에 할당되었으므로 모델은 이제 두 가지를 알게 됩니다:

- 이미지에서 어떤 개념이 더 많이 표현되는지
- 이러한 개념이 이미지에서 어디에 위치하는지.

일반인이 이해하기 쉽게 설명하면 SPARC와 다른 이미지 인코더 간의 주요 차이점은 텍스트 설명의 개별 단어를 이미지의 특정 부분에 할당한다는 사실에 있습니다.

이러한 방식으로 이미지의 특정 부분의 그룹화된 임베딩이 '개' 단어 토큰에 크게 편향되어 있다면, 이미지의 해당 영역에는 아마도 개가 포함되어 있을 것입니다.

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

SIMA의 중요한 요소입니다. 3D 에이전트는 요청된 작업의 일환으로 상호 작용할 특정 객체를 식별할 수 있어야합니다.

마지막으로, 비디오 인코더에 대해 이야기하면, 모델이 과거 상태를 고려할 수 있도록 포함되어 있습니다. 중요한 것은 비디오 인코더가 시간적 인식을 포함한다는 것인데, 이는 텍스트나 이미지 인코더가 제공할 수 없는 기능입니다.

환경의 현재 상태뿐만 아니라 과거의 환경 상태와 취해진 조치에 따라 다음 조치를 취할지 결정하기 때문에 이는 중요합니다.

## 최적의 정책 선택

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

제공된 정보를 바탕으로 SIMA는 다른 인코더들에 의해 생성된 표현을 사용하여 변환 모델 세트를 활용합니다. 이 모델은 LLM이 단어를 예측하는 대신 작업을 출력하여 에이전트가 실행할 키보드 및 마우스 조작을 지시합니다.

한편, 왜 Gemini, 구글의 MLLM을 사용하는 대신 모델의 주요 '두뇌'로 이상한 Transformers 세트를 사용했는지 궁금할 수도 있습니다.

이유는 아마도 예산 때문인데, 연구자들 자신들이 기술 보고서에서 SIMA의 분명한 다음 단계는 Gemini를 사용하는 것이라고 인정했기 때문입니다.

이것은 매우 흥미로운데, 최고의 '두뇌'를 사용하지 않았음에도 놀라운 결과를 얻었다고 하니, 이제 우리가 곧 살펴볼 것입니다.

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

# 진정한 일반화자

지금쯤이면 알 수 있겠지만, 목표는 여태까지 한 게임에서도 능숙한 에이전트를 훈련시키는 것이었습니다. 게임을 해본 적이 없는 게임조차도요.

훈련 후 SIMA 에이전트는 다양한 범주로 구분된 600가지 다양한 기본 작업을 수행할 수 있었습니다. 이러한 범주에는 네비게이션, 동물 상호작용, 음식 등이 포함되어 있었습니다:

![image](/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_5.png)

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

SIMA가이러한작업을수행하고있는모습을여기에서확인할수있습니다. 게다가, SIMA는매우유망한결과를얻어 언급할만한가치가있습니다.

우선, 다양한게임에서훈련을받았음에도불구하고, 평균적으로SIMA는 단일게임에서전문화된 에이전트들보다 더우수한성능을보였습니다... 그특정게임안에서:

![image](/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_6.png)

더욱인상적인점은, 여러가지다른게임에서, 에이전트가영이의업무(non-trivial tasks)를달성했으며, 이중대부분은얼마들이나예시없이(zero-shot tasks)성과를거두었으며, 다시한번전문화된 에이전트들을이겼습니다:

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

<img src="/assets/img/2024-05-17-AIsFirstBabyStepsinEmbodiedIntelligence_7.png" />

이는 과거에 본 적 없는 환경에 놓여도 모델이 일반적으로 잘 수행되었으며, 때로는 Goat Simulator 3와 같은 경우에는 전문화된 에이전트(해당 게임에서만 훈련된 에이전트)보다 뛰어난 성과를 보였습니다.

그렇다면 이 모든 것이 의미하는 바는 무엇인가요?

간단히 말하자면, 우리는 게임 간의 지식 전이의 구체적인 증거를 관찰하고 있다는 것입니다.

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

다시 말해, 모델은 일부 게임에서 유용하게 적용할 수 있는 의미 있는 기술을 학습합니다 (예: 키보드로 이동하는 방법을 배우는 것과 같이).

더욱이, 이러한 기술들은 상당히 높은 품질을 갖고 있어서 이 일반화된 에이전트가 많은 게임에서 특화된 에이전트들을 이기는 것을 의미하며, 이는 일반화된 접근 방식이 그들이 다양한 환경에 적용할 우수한 기술을 학습하는 데 도움이 된다는 것을 시사합니다.

Figure AI의 발전과 함께 이러한 결과들이 어떠한 맥락에서 중요성을 얻을 수 있는 경우, 전체적으로 매우 인상적인 결과입니다.

# 로보틱스에게 훌륭한 주였습니다

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

AI 로봇 기술은 요즘 정말 빠르게 발전하고 있어요.

- 한 쪽에서, Figure AI는 우리가 점점 더 많은 수동 작업 범위를 알리는 인간형 로봇을 만들어내는 데 탁월한 실력을 보여줍니다.
- 반면에, SIMA는 3D 환경에서 최초의 일반 적 에이전트를 보여주고 있음을 암시합니다.

하지만 우리가 깨닫는 것은 시너지의 잠재력입니다.

이러한 에이전트들을 실생활 상황으로 가져오는 데는 아직 이르지만, 이 두 분야 사이의 융합은 다음 단계로 자연스러운 발전을 이루고 있습니다; SIMA가 훈련의 장소일 뿐만 아니라, Figure AI 로봇들이 일반적인 에이전트의 구현체 역할을 하기 때문이죠.

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

그리고 다른 기업들도 구체적 지능에 대한 자신들의 시사를 제시하며 경쟁이 치열해지고 있습니다. 많은 기존 기업들이 인류의 기술이 충분히 준비되어 있다고 느끼고 있어서 다음 큰 도전, 인공지능을 실생활에 적용하는 것에 착수할 준비가 되어있다고 느끼는 것이 분명합니다.
