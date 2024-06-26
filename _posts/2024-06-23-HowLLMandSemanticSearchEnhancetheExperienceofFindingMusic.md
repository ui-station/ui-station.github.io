---
title: "LLM과 시맨틱 검색이 음악 찾기 경험을 향상시키는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_0.png"
date: 2024-06-23 21:53
ogImage:
  url: /assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_0.png
tag: Tech
originalTitle: "How LLM and Semantic Search Enhance the Experience of Finding Music"
link: "https://medium.com/@lamslide/music-discovery-using-semantic-retrieval-experimenting-with-different-chunk-sizes-for-song-data-a0f8fd4d7f44"
---

## 음악 데이터에 대한 의미 검색 및 주관적 분석의 시범

의미 검색을 통해 우리의 취향과 기분에 맞는 음악을 발견하는 것이 훨씬 쉬워졌어요. 오늘날 다채로운 노래 바다에서, 예술가, 제목 또는 장르로 검색하는 전통적인 방법은 종종 우리가 원하는 뉘앙스와 편리함을 잡아내는 데 제한이 있어요.

본 글에서는 의미 검색이 음악 발견을 향상시킬 수 있는 방법을 소개하고 이러한 시스템을 구축할 때 고려해야 할 사항에 대해 논의하고 있어요.

![image](https://miro.medium.com/v2/resize:fit:1200/1*PEaRXrrXL8kLPe9qXuQtOg.gif)

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

이것은 GenAI 시스템을 구축에 관심이 있는 사람들을 위한 실용적인 예제입니다. 특히 청크 크기가 의미적 결과에 어떻게 영향을 미치는지에 대해 다루고 있습니다.

# 전통적 검색 대 의미적 검색

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_0.png)

전통적 음악 검색은 아티스트 이름, 제목, 장르 또는 날짜와 같은 명시적 기준을 의존합니다. 해당 아이템에 대한 개념을 가지지 않으며, 기준("메타데이터")으로 정의됩니다.

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

이것은 노래를 검색할 때 키워드(노래 제목/아티스트)를 맞춰야 의미 있는 결과를 반환할 수 있다는 뜻입니다.

대부분은 우리가 알게 된 검색 경험이죠. 유용하지만 알지 못하는 노래를 발견하는 능력을 제공하지는 않습니다.

반면에 시맨틱 검색은 정의된 카테고리를 초월하여 의미에 기초해 노래를 찾을 수 있도록 합니다. 정확한 일치를 찾는 대신, 의미적 유사성을 기반으로 검색할 수 있습니다.

![image](https://miro.medium.com/v2/resize:fit:1200/1*m_Gn2kJRYcrN_dg0pnb-9Q.gif)

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

"서로 일치 또는 '로맨틱' 또는 '슬픈' 곡을 찾는 대신, '내 남자친구는 까칠하게 행동하고 나는 방금 그를 차 버렸어.' 라는 용어로 검색할 수 있어요. 그리고 '여름'과 '인기' 아래를 찾는 대신, '마이애미 보트에서 나와 친구들을 위한 여름 분위기'를 요청할 수도 있어요. 😆

사용 가능한 노래 가운데 가장 유사성 점수를 사용하여 가장 가까운 근사치를 반환할 수 있는 모든 종류의 쿼리를 고려해 볼 수 있어요.

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_1.png)

## 벡터 임베딩: 간략한 개요"

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

시멘틱 검색은 음악 가사 AI가 이해할 수 있도록 완성됩니다. 이전 기사에서 논의된 바와 같이, LLM 및 기타 AI 모델은 의미를 밀집 벡터 공간에서 수학적으로 표현합니다.

벡터 임베딩은 자연어 처리와 기계 학습에서 기본적인 개념입니다. 이것은 단어, 구, 또는 문서를 고차원 공간에서 밀집 벡터로 표현하는 것을 포함합니다. 벡터의 각 차원은 텍스트의 고유한 기능 또는 특성을 나타냅니다.

벡터 임베딩을 만드는 과정은 비슷한 의미나 맥락을 가진 단어들이 유사한 벡터 표현을 가져야 한다는 아이디어에 기반합니다.

이는 기계가 단어간의 의미적 관계를 이해하고 시맨틱 검색, 텍스트 분류, 그리고 감정 분석과 같은 작업을 수행할 수 있도록 합니다.

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

![image](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_2.png)

노래 가사에 대한 의미검색 컨텍스트에서 vector embeddings을 통해 시스템이 가사의 의미와 맥락을 이해할 수 있게 되었습니다. 단순한 키워드 일치가 아닌 글자들과의 맥락을 이해할 수 있습니다.

사용자의 쿼리와 노래 가사의 vector embeddings을 비교하여, 시스템은 정확히 같은 단어가 포함되지 않더라도 쿼리와 의미론적으로 유사한 노래를 검색할 수 있습니다.

# 노래 데모 데이터셋

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

우리의 음악 발견 데모를 만들기 위해, 2024년 1월 1일부터 3월 1일까지의 빌보드 탑 100곡에 초점을 맞추었습니다.

이 데이터셋은 비교적 작지만, 전국에서 가장 많이 재생된 노래로 구성되어 탐구의 좋은 기반이 됩니다. 마지막으로, 중복된 노래를 제거하고 데이터셋을 정리하여 301곡의 컬렉션으로 결과를 얻었습니다.

알아두실 점이 있다면, 대부분의 정리는 오래된 크리스마스 고전 노래를 제거하는 데 관한 것이었습니다. 크리스마스 때문에 지겨워서 자주 재생되는 것 때문에요 😓. 새로운 음악을 발견하겠다는 목표에 부합하지 않았거든.

# 서로 다른 청크 크기는 의미 결과를 변경합니다

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

의미 검색에 관한 것을 말할 때, 우리가 무언가를 분석하고 분석하는 방식이 결과에 큰 차이를 만들 수 있어요.

청크 크기는 데이터를 분석하고 벡터 표현을 위해 나누는 정교도를 의미해요. 그들은 서로 다른 의미를 갖고 있으며, 이는 직접적으로 벡터 임베딩의 성격과, 결과적으로 의미 검색 결과에 영향을 미쳐요.

## 청크 크기는 음악 검색에서 중요한 역할을 해요

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_3.png)

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

청크 크기의 선택은 검색 결과의 성격에 직접적인 영향을 미칩니다. 일반적으로 우리는(이전 데모에서 알아낸 바에 따르면) 더 큰 청크에 대해 매칭하면 주제/개념에 대한 포괄적인 보기를 제공하고, 더 작은 청크에 대해 매칭하면 더 구체적인 키워드 결과를 얻을 수 있습니다.

이를 더 실험해보기 위해, 우리는 4가지 다른 유형의 청크 세그먼트를 만들기로 결정했습니다:

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_4.png)

따라서 전체 곡 청크 이외에 다른 세그먼트 유형은 기본적으로 곡 가사를 여러 청크로 나누어 각각의 벡터 임베딩을 갖습니다.

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

## 명료한 피드백을 위한 일치 청크 시각화

우리는 노래 가사에 강조를 추가하여 쿼리, 일치하는 청크, 그리고 최종 결과 사이의 관계를 시각화하는 데 도움이 되도록 했어요.

![Matched Chunks](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_5.png)

- 일치하는 청크 (주황색)
- 중첩 (노란색, 스탠자와 대저만 스탠자에 대한 중첩)

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

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_6.png)

## 결과의 일반 토론

시맨틱 검색이 제대로 작동하는지 확인하는 것은 매우 간단했습니다. 결과는 입력한 쿼리와 매우 상관이 있었습니다. 우리의 초점은 최상위 결과에 있었으며, 이는 사용자가 원하는 경험을 시뮬레이션하고 300개가 아닌, 예를 들어 30,000개의 노래와 같은 큰 데이터 세트를 다룰 때 어떻게 느껴질지를 보여줍니다.

가끔 당장 찾고 싶은 정확한 노래를 1위 결과로 돌려받기 위해 올바른 단어를 몇 번 시도해야 하는 경우도 있었지만, 그것은 일관되고 예측 가능했습니다. 즉, 원하는 노래가 최상위 결과가 아닌 경우에는 그 이유가 분명했습니다.

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

아래는 청크 크기를 기반으로 한 결과들에 대한 토론입니다:

## 시 구석에 대한 결과 토론

시는 우리가 찾고 있는 정확한 일치를 반환할 때 가장 쉽고 예측 가능한 것으로 보입니다. 이는 전통적인 Google 검색과 매우 유사하며 우리는 상대적으로 우리의 검색어를 잘 알고 있어야 합니다.

![이미지](https://miro.medium.com/v2/resize:fit:1200/1*b4xFAhFSkoR8u1tlvcF18g.gif)

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

<img src="/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_7.png" />

단락 또는 한 문장과 같은 작은 청크는 키워드나 개념에 맞춰 일치시킬 수 있는 강력한 "퍼지" 검색으로 작용할 수 있습니다. 예를 들어 꽃의 경우 분홍색 장미와 같은 경우입니다.

하지만 전반적인 주제나 개념에 기반한 검색에는 큰 청크에 비해 작은 청크가 유용하지 않을 수 있습니다.

## 전체 노래 가사 결과 논의

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

이 점을 설명하기 위해 우리는 전체 노래 가사로 "꽃"이라는 용어를 검색할 수 있습니다. 결과는 마일리의 노래가 23% 일치하는 경우 4 위에 위치한 것을 알 수 있습니다.

![그림 1](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_8.png)

가사를 분석해보면 "꽃"이라는 용어 자체가 전체 노래의 작은 부분을 차지하기 때문에 이 결과가 매우 타당하다는 것을 알 수 있습니다.

![그림 2](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_9.png)

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

플라워라는 단어에 대한 여러 언급이 있지만 제목에 있더라도 이 단어가 곡의 의미에는 거의 기여하지 않습니다. 이 노래는 주로 이별 후 자신 안에서 행복을 찾는 아이디어를 중심으로 합니다. 더 큰 데이터 세트에서, Miley의 노래는 결과의 첫 페이지에 있을 가능성이 거의 없을 겁니다 — 말 그대로요.

## 다양한 검색어와 곡 전체에 대한 결과 예시:

세말리택 서치의 사용자 경험을 보여주기 위해 Benson Boone의 "Beautiful Things"를 사용한 더 예시를 살펴봅시다.

![2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_10.png](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_10.png)

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

한번 더 강조하자면, 우리의 주요 관심은 노래를 가능한 한 확실하게 찾아내어 더 큰 데이터셋을 사용하는 느낌을 시뮬레이션하는 것입니다.

초안에서 가사는 무엇인가에 대해 감사해하는 주제를 중심으로 살펴보지만, 특히 가족, 애인 등을 통한 사랑 관계의 맥락에서 더욱 명확해 보입니다. 그래서 제가 사용한 첫 번째 검색어는 "가진 것에 대해 감사해하는 것" 이었습니다.

그러나 이 방법은 가사를 3 번째 결과로 반환했고 유사도 점수는 23%에 불과했습니다. 가사를 더 자세히 살펴보니, 이것이 매우 타당한 것으로 보입니다. 노래 전체를 통틀어 누군가를 사랑하는 것을 잃을까 두려워하는 심오한 두려움이 더욱 두드러지는 주제처럼 보입니다.

![image](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_11.png)

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

결과를 개선하기 위해 검색 쿼리를 "예쁜 여자친구를 잃을까봐 무서운"로 수정했고, 이로 인해 노래가 상위 결과로 반환되었으며 유사도 점수도 35%로 높아졌어요.

이렇게 해서 "가진 것에 감사하는 것"이라는 쿼리는 데이터 세트가 1000배 더 큰 경우 이를 최상위 결과로 반환하지 않았을 것 같다는 점이 좋았어요.

그리고 흥미로운 사실인데, "예쁜"이라는 단어를 "섹시한"으로 바꾸었을 때 유사도 점수가 31%로 떨어지고, 여성에 대한 비슷한 언급을 한 다른 노래들이 37% 일치하여 결과의 최상단에 올라가는 것을 볼 수 있었어요.

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_12.png)

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

위 내용은 쿼리에서 선택한 단어가 검색 결과에 상당한 영향을 미칠 수 있음을 보여줍니다. 이때 전반적인 주제가 동일하더라도요.

## 노래 및 스탠자 청크 결과의 논의

스탠자 및 더블 스탠자를 청크로 검색하는 것은 특정 노래를 반환하려는 시도에서 가장 어려웠음을 입증했습니다. 사용자 관점에서는 각 청크에 어떤 개념이 포함될 수 있는지 명확한 감을 가지지 않기 때문입니다.

개념적으로, 핵심 정보가 분할되어 완전히 다른 전제로 변경되는 경우 어려움이 발생합니다. 스탠자와 큰 스탠자 청크의 중첩은 이를 돕기 위해 존재하지만, 결코 완벽한 해결책은 아닙니다.

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

그러나 일반적으로 노래의 주제나 일부분을 알고 있다면 쿼리를 할 수 있습니다. 대부분의 경우에는 빌보드 탑 100 노래들이 많은 반복을 포함하기 때문입니다.

"아름다운 것들"의 경우 "잃고 싶지 않은 내 귀여운 여자친구" 쿼리는 노래, 스탠자 및 대 스탠자 세 가지 크기의 청크를 모두 찾는 데 효과적이었습니다. 이는 상대방을 보호하는 주제가 이 노래 전반에 여러 번 나타나기 때문입니다.

스탠자 결과

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_13.png)

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

대형 스탠자 결과 (더블 스탠자)

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_14.png)

"내 예쁜 여자친구를 잃을까 봐 두려운"이라는 쿼리는 노래 수준에서는 최악의 결과를 보여주었습니다. 스탠자와 대형 스탠자의 경우 각각 45%와 47%에 비해 성능이 가장 낮았습니다.

가사를 더 자세히 살펴보면, 우리가 검색한 로맨틱한 테마 이외에도 다른 테마가 존재함을 알 수 있습니다. 다른 검색어들 역시 "Beautiful Things"를 최상위 결과로 반환하는 경우가 많을 것이기 때문에 이는 이치에 맞는 것입니다.

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

![이미지](/assets/img/2024-06-23-HowLLMandSemanticSearchEnhancetheExperienceofFindingMusic_15.png)

실제로, 저희의 메트릭스(인공지능 언어모델 주관적 분석에서 생성됨)에 따르면, "귀여운 여자친구를 잃을까봐 두려워"라는 쿼리에 주로 포함된 낭만적 주제 외에도, 동기 부여, 종교적, 그리고 슬픈 요소가 포함되어 있음을 보여줍니다.

# 결론

벡터 임베딩과 의미 검색은 우리가 만들 수 있는 새로운 가능성들을 엽니다. 서로 다른 청크 크기를 사용하여 의미 검색이 작동하는 방식을 맞춤화할 수 있습니다. 노래의 본질을 포착하는 적절한 쿼리를 작성하는 것은 어렵습니다, 특히 서로 다른 청크 크기 간 전환 시.

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

AI 에이전트 시스템을 구축하는 사람들에게는 청킹 전략(예: 어떤 크기가 어떤 사용 사례에 적합한지)이 매우 중요한 기본 요소라는 것을 고려해야 합니다. 이는 에이전트에 공급되는 정보의 크기와 유형을 결정하기 때문입니다. 이 예에서는 노래 데이터를 고려했지만, 여기에서 사용된 데이터 기술은 다른 사용 사례와 산업에 널리 적용될 수 있습니다.

이전에 논의한 대로, 구절은 의미론적 맥락이 매우 작기 때문에 거의 키워드 검색으로 사용하기에 가장 적합합니다. 반면에, 더 큰 청크 크기로 작업할 때에는 의미를 담을 공간이 더 많아집니다.

청킹이 자연어 검색 방식에 미치는 영향을 고려해야 합니다. 고려해야 할 몇 가지 질문은 다음과 같습니다: 우리의 문맥 창은 얼마나 큰가? 청크가 문서에 포함된 아이디어를 어떻게 분할할 것인가? 작업을 완수하기 위해 필요한 문맥의 유형은 무엇인가? 사용자 쿼리는 어떻게 보이며(크기는 어떤가)?

적절한 청크 크기 선택: 특정 사용 사례와 원하는 사용자 경험을 기반으로 적절한 청크 크기를 선택하는 중요성을 기억해야 합니다. 예를 들어, 사용자가 특정 가사나 구절을 기반으로 노래를 찾는 것을 목표로 한다면, 구절과 같이 작은 청크 크기가 더 적합할 수 있습니다. 반면에, 전반적인 주제나 감정을 기반으로 노래를 추천하려는 경우, 전곡이나 스탠자와 같이 큰 청크 크기가 더 효과적일 수 있습니다.

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

정확성과 효율성 균형 맞추기: 청크 크기 결정 시 검색 결과의 정확성과 계산 효율성 사이의 Trade-off를 고려하세요. 작은 청크 크기는 더 정확한 일치를 제공할 수 있지만 더 많은 계산 자원과 저장 공간이 필요합니다. 큰 청크 크기는 더 효율적일 수 있지만 정확성이 떨어질 수 있습니다. 가능한 자원과 성능 요구 사항을 기반으로 적절한 균형을 찾아보세요.

대규모 구현에 들어가기 전에 청킹 및 임베딩 전략을 세밀하게 조정하기 위해 작은 데이터 세트로 실험해보기를 권장합니다.

# 전망: 음악 발견의 미래

우리가 보았듯이, 의미 검색은 음악 발견에 대한 강력한 새로운 접근법을 제공하여 사용자가 노래를 의미, 주제 및 감정에 기반하여 찾을 수 있게 합니다. 이 기술은 아직 초기 단계에 있지만 음악과 상호 작용하는 방식을 개선하는 데 큰 잠재력을 보여줍니다.

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

가까운 미래에는 의미론적 검색과 LLM 생성 메트릭을 활용하는 AI DJ 에이전트들이 어떤 장소든 완벽한 노래를 찾는 데 사용되는 핵심 정보원이 될 수 있습니다. 다음 기사에서는 Large Language Models (LLMs)이 각 노래에 대한 주관적인 메트릭을 생성하는 방법, 예를 들어 로맨틱한 매력이나 감정적인 심오함과 같은 요소를 조사하여 음악 추천의 맥락과 맞춤화를 더욱 강화하는 방법에 대해 알아보겠습니다.

# 계속 읽기

이 시리즈의 제2부에서는(LLMs 및 주관적 분석에 대한 내용이 곧 출시됩니다), LLM의 흥미로운 세계와 주관적 분석에 대해 자세히 알아볼 것입니다. 어떻게 LLM을 활용하여 데이터 검색을 강화하는 데 사용할 수 있는 메트릭을 생성하는지 탐색해보세요.

# 데모에 액세스하기

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

죄송하지만 콘텐츠의 성격상 해당 데모를 독자들에게 공개할 수 없습니다. 더 많은 데모에 관심이 있다면 아래 링크를 방문해주세요: [https://unacog.com/](https://unacog.com/)

# 함께 소통해요

읽어 주셔서 감사합니다! 만약 이 기사를 즐겁게 보거나 유용하게 느끼셨고 더 많은 이와 같은 콘텐츠를 보고 싶다면, 제 Medium에서 팔로우하여 새로운 글을 올릴 때마다 알림을 받아보세요. 또한, LinkedIn에서도 저와 연락하실 수 있습니다. 🙏
