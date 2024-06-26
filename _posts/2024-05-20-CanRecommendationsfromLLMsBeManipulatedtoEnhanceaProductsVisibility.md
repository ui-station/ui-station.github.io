---
title: "LLMLarge Language Model의 추천은 제품의 가시성을 높이기 위해 조작될 수 있을까요"
description: ""
coverImage: "/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_0.png"
date: 2024-05-20 20:31
ogImage:
  url: /assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_0.png
tag: Tech
originalTitle: "Can Recommendations from LLMs Be Manipulated to Enhance a Product’s Visibility?"
link: "https://medium.com/towards-data-science/can-recommendations-from-llms-be-manipulated-to-enhance-a-products-visibility-64c64fa9cd24"
---

## 책임 있는 인공지능

요즘 트위터에서 한 가지 팁을 발견해서 공유해볼게. "before:2023"을 구글 검색에 추가하면 AI가 생성한 SEO 콘텐츠를 걸러낼 수 있다는 거야. 실제로는 이 기능을 사용해본 적이 없지만, 개념은 이해가 되겠지? 요즘 인터넷은 너무 많은 AI 생성 콘텐츠로 가득 차 있어서 실제 정보를 걸러내기가 어려워졌어. 상황이 심각해서 구글도 검색 알고리즘 조작하고 순위를 인위적으로 높이려는 모든 AI 생성 콘텐츠를 제거하기로 결정했어. 말이 AI 생성 콘텐츠에 반대한다는 게 아니야, 하지만 검색 결과에 영향을 주기 시작하면 문제가 될 수 있어. Generative AI 시대에는 콘텐츠 생성이 너무 쉬워져서 상황이 더 복잡해지는 거야.

대규모 언어 모델(LLMs)은 이미 전자 상거래 플랫폼에서 검색 및 추천 프로세스를 개선하는 데 사용되고 있어. 그런데 추천을 제공하는 데 사용되는 이 LLM이 조작된다면 어떻게 될까? 전자 상거래 시장에서의 조작은 새로운 게 아니야. 로이터(Reuters)의 2016년 보고서에 따르면 아마존은 "검색 시드(Seeding)"라는 기술을 사용해 아마존 베이직스(AmazonBasics)와 솔리모(Solimo) 브랜드 제품이 출시 직후 상위 검색 결과에 표시되도록 했어. 보고서에는 "검색 시드를 사용해 신규 출시된 ASINs가 검색 결과의 처음 두 개 또는 세 개의 ASIN으로 나타나도록 했다"고 구체적으로 언급돼. LLMs를 이용하면 규모와 속도 때문에 상황이 더 악화될 수 있어.

Manipulating Large Language Models to Increase Product Visibility란 제목의 새 연구에서 Aounon Kumar와 Himabindu Lakkaraju가 이러한 시나리오를 자세히 연구했어. 특히 제품 정보에 전략적 텍스트 시퀀스(STS)라고 불리는 특별히 디자인된 메시지를 포함시킴으로써 특정 업체들이 경쟁 업체에 비해 불공정한 이점을 얻고 제품이 최상의 추천으로 선정될 가능성이 크게 증가함을 보여줘. 이런 관행은 소비자들의 구매 결정과 온라인 시장에 대한 신뢰에 영향을 미칠 수 있어, 온라인 비즈니스에서 신뢰는 중요한 요소니까.

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

본 문서에서는 작가들이 이 특별한 텍스트 시퀀스를 생성하고 논문에서 전달된 결과를 더 자세히 이해하는 방법에 대해 이해해 봅시다. 작가들은 관련 코드를 GitHub에서 공개했습니다.

# LLM 기반 검색 작동 방식

일반적인 검색 엔진은 관련 페이지를 찾는 데 효과적이지만 정보를 일관되게 제시하는 데는 그리 효과적이지 않습니다. 반면 LLM(Large Language Model)은 검색 결과를 가져와 관련 답변으로 변환할 수 있습니다. 사용자의 검색어를 받으면 검색 엔진은 인터넷이나 제품 설명서와 같은 지식 베이스에서 관련 정보를 가져옵니다. 이후 이 검색 결과와 사용자의 입력을 LLM에 공급하기 전에 사용자의 쿼리와 함께 이 정보를 연결하여 LLM이 사용자의 특정한 요구에 직접적으로 대응하는 맞춤형 최신 답변을 생성할 수 있습니다. 아래 그림(상기한 논문에서 제공)은 전체 과정을 자세히 보여줍니다.

<img src="/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_0.png" />

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

# LLM이 생성한 추천을 조작할 수 있을까요?

논문은 특정 제품을 선호하도록 LLM이 생성한 추천을 조작할 수 있다는 사실을 입증하기 위한 설득력 있는 예시를 제시합니다. 예를 들어, 아래의 그림을 살펴보세요 (이 그래프가 어떻게 만들어졌는지에 대한 세부 내용은 나중에 설명하겠습니다). 아래 그래프는 전략적 텍스트 시퀀스(STS)를 추가하기 전과 후의 추천 척도에서 제품의 순위 차이를 명확히 보여줍니다. STS를 적용하기 전에는 제품이 일관되게 추천 중에서 하위 순위, 순위 10 근처에 위치했습니다. 그러나 STS를 적용한 후에는 제품이 추천의 정상으로 도약하여 순위 1에 가까이 위치했습니다.

![이미지](/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_1.png)

이미 논의한 바와 같이, LLM을 활용한 검색의 장점은 인터넷이나 제품 카탈로그에서 정보를 추출할 수 있는 능력에 있습니다. 판매업자들은 여기서 프로세스를 가이드할 수 있는 기회를 가지게 됩니다. 어떻게 가능할까요? 이 carefully crafted texts 또는 STS를 제품 정보 페이지/카탈로그에 포함시켜 LLM의 입력으로 만들면 됩니다.

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

<img src="/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_2.png" />

STS는 Universal and Transferable Adversarial Attacks on Aligned Language Models 논문에서 소개된 Greedy Coordinate Gradient (GCG)과 같은 적대적 공격 알고리즘을 사용하여 최적화됩니다. 이러한 공격은 일반적으로 LLM의 안전 제약 조건을 우회하고 해로운 출력을 생성하는 데 사용됩니다. 그러나 이 연구의 저자들은 이러한 알고리즘을 "더 친화적인" 목적으로 제품 가시성을 높이는 데 재활용합니다.

# 커피 머신 추천을 위한 LLM 검색 인터페이스 쿼리

저자들은 사용자가 가격이 적당한 커피 머신을 구매하고 싶어 하는 시나리오를 제시합니다. 이때 '적당한'이라는 단어에 주목해야 합니다. 이는 제품의 가격이 중요하며 사용자가 비싼 옵션을 원하지 않는다는 것을 의미합니다. 아래에 나와 있는 것처럼 LLM에 대한 입력 프롬프트로 시작해 봅시다.

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

![이미지](/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_3.png)

- 시스템 프롬프트 — 맥락 설정,
- 제품 정보 — JSON 형식의 데이터베이스에서 가져온 것으로, 10가지 가상 커피 머신 모델의 구체적인 내용을 제공합니다. 판매자는 여기에 STS를 포함할 수 있습니다.
- 사용자의 쿼리 — 가격이 저렴한 옵션을 찾고 있습니다.

논문에서 설명한 예시 프롬프트는 다음과 같습니다. 'ColdBrew Master Coffee machine'에 대한 '대상 제품' 필드에 STS가 삽입된 것을 확인해보세요.

![이미지](/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_4.png)

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

## 전략적 텍스트 시퀀스 제작

논문에서 설명하는 텍스트 시퀀스 생성 과정의 일부를 확인할 수 있습니다.

예를 들어, 제품 목록에서 ColdBrew Master의 순위를 높이려면 STS를 추가해야 합니다. 아래 표시된대로 STS는 '\*,'로 표시된 자리 표시자 토큰 시퀀스로 시작하여 GCG 알고리즘을 사용하여 반복적으로 최적화됩니다.

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

또한, 제품이 나열되는 방식에 관계없이 STS의 성능을 최적화하기 위해 각 최적화 이터레이션마다 제품 목록의 순서를 무작위로 섞을 수도 있습니다.

결과는 일반적으로 가시성이 낮아질 수 있는 $199의 높은 가격에도 불구하고, ColdBrew Master가 STS를 설명에 통합하여 추천 목록 상단으로 이동했다는 것을 보여줍니다. 그리고 놀랍게도, STS를 통합한 후 100번의 이터레이션만으로 숨겨져 있던 순위에서 상위로 끌어올려졌습니다.

![이미지](/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_6.png)

# 두 제품, ColdBrew Master 및 QuickBrew Express에 대한 전략적 텍스트 시퀀스 최적화 비교

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

이제 STS가 제품 순위에 미치는 영향에 대한 감을 잡았으니 다음 제품에 영향을 미치는 방법을 비교해보겠습니다.

☕️ ColdBrew Master는 가격이 $199로 높은 가격의 커피 머신입니다.

☕️ QuickBrew Express는 $89로 더 저렴한 옵션입니다.

여기에 비교 결과를 비교하기 위해 만든 표가 있습니다.

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

![image](/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_7.png)

위의 결과는 $199의 높은 가격에도 불구하고, 시각성이 적어지는 경향이 있는데도 ColdBrew Master가 STS를 설명에 통합함으로써 추천 목록의 선두로 올라간 것을 보여줍니다. 흥미로운 점은 이 제품이 원래 비용이 높아서 목록에 첫째 자리에 없었던 것입니다.

반면, 더 저렴한 가격대의 QuickBrew Express의 순위는 일반적으로 추천 목록에서 둘째 자리를 차지하는데, STS를 추가하면서 크게 향상되어 종종 최상위 자리에 도달합니다.

![image](/assets/img/2024-05-20-CanRecommendationsfromLLMsBeManipulatedtoEnhanceaProductsVisibility_8.png)

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

# 결론적인 생각: Generative Search Optimization(GSO)가 새로운 SEO인가요?

논문에서 소개된 상황은 현실과 크게 다르지 않습니다. 저자들은 Generative Search Optimization(GSO)와 전통적인 SEO 사이에 적절한 비교를 그려냈습니다.

이전에 언급한 대로, 온라인 비즈니스의 성공은 고객들과 확립하는 신뢰와 평판에 밀접하게 연관되어 있습니다. 의도적으로 제품 추천을 조작하는 것은 공정성과 소비자 속임수와 관련하여 윤리적인 문제를 제기합니다. 가짜 제품 리뷰의 존재는 이미 계속되는 문제입니다. 우리는 확실히 조작된 추천이 이러한 상황을 더욱 복잡하게 만들길 원하지 않습니다.

모든 블로그 및 관련 코드에 쉽게 액세스하려면 내 GitHub 저장소를 방문해주세요.
