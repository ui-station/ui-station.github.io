---
title: "전자 상거래에서 테마 기반 분류와 GenAI를 활용한 패션 발견 혁신"
description: ""
coverImage: "/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_0.png"
date: 2024-05-23 17:42
ogImage:
  url: /assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_0.png
tag: Tech
originalTitle: "Transforming Fashion Discovery in E-Commerce through Theme-Based Categorization with GenAI"
link: "https://medium.com/@dschonfeld/transforming-fashion-discovery-in-e-commerce-through-theme-based-categorization-with-genai-8c32aa0224e9"
---

By
Xingming Qu
, Gaomin Wu
, Dan Schonfeld
그리고
Jonathan Galsurkar

우리는 LLM(대형 언어 모델)을 활용하여 이베이의 패션 추천 시스템을 혁신하여 제품 발견에 대한 맞춤화 및 맥락 감수성을 제공함으로써 이베이의 "View Item" 페이지에서 사용자 참여도와 전환율을 크게 향상시켰습니다.

## 소개

대형 언어 모델 (LLM)을 다양한 분야에 통합하는 것은 인공 지능의 발전에서 중요한 마일스톤을 표시했습니다. 이를 통해 사용자 상호작용과 만족도를 향상시키기 위한 혁신적인 솔루션을 선보이고 있습니다. 패션 분야에서 전통적인 벽돌과 모르타르 상점들은 스타일, 행사 적합성에 대한 상세한 토론을 하는 지식 있는 판매원들의 큰 도움을 받습니다. 고객들이 선호도를 이해하고 정리하는 데 도움을 주는 것이 바로 이 환경입니다. 이를 온라인 플랫폼이 재현하는 데 어려움을 겪고 있습니다. 우리는 이 도전에 대처하기 위해 LLM의 기능을 활용하여 이베이의 패션 추천 시스템을 혁신하고 있습니다. 특히, 우리의 접근 방식은 LLM을 활용하여 제품을 일관된 피벗 그룹으로 분류하고 이러한 그룹을 위한 딥 러닝 임베딩 센터를 생성합니다. 이는 이러한 명확히 정의된 주제 내에서 후보 아이템을 조직화된 방식으로 제공하는 독특한 전략을 제공합니다. 이 방법론은 제품 발견 프로세스를 보다 간단하게 만들 뿐만 아니라, 기존 추천 알고리즘의 능력을 능가하는 맞춤화 및 맥락 감수성을 편입하고 있습니다.

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

## 배경

온라인 패션 쇼핑의 빠른 속도에서 맞춤 및 관련 제품 추천을 제공하는 도전은 특히 두드러집니다. 현재 여성용 패션이나 일반 패션 아이템을 둘러보는 사용자들은 그림 1에 나와 있는 것처럼 특정 기회에 적합한 특정 스타일의 옷을 자주 찾습니다.

![이미지](/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_0.png)

그러나 이 중요한 정보는 판매자가 제공하는 항목 제목이나 설명에서 항상 명확하게 제시되지는 않습니다. 전통적인 매장 쇼핑 경험과는 대조적으로, 매력적인 옷의 스타일, 행사 적합성에 대해 고객과 자세한 토론을 진행하고 그들의 선호도를 더 잘 이해하기 위해 비슷한 제품을 안내해주는 지식 있는 판매원들이 있다면, 우리는 eBay 패션 아이템에 대한 이 맞춤형 지원을 복제하고 개선하려고 합니다.

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

물리 매장에서, 옷 판매원들은 패션의 복잡성을 안내하는 데 중요한 역할을 합니다. 그들은 각 제품의 스타일 미학을 논하며, 다양한 상황에 적합한 제품이며, 고객들에게 다른 유사한 제품을 보여주어 그들이 더 잘 이해하고 세련되게 취향을 확립할 수 있도록 도와줍니다. 우리의 목표는 LLM의 힘을 활용하여 이 맞춤형 쇼핑 경험을 온라인으로 재현하는 것입니다. 이를 통해 우리는 바이어가 완벽한 패션 아이템을 찾는 데 도움을 주는 것뿐만 아니라 물리 매장에서 일반적으로 느끼는 맞춤 가이드와 전문지식의 느낌을 재현하는 것을 목표로 합니다. 궁극적으로, eBay의 패션 발견 프로세스를 더 매력적이고 유익하며 만족스럽게 만드는 데 노력하고 있습니다.

## 접근 방식

우리는 문맥과 제품을 고려한 pivot 그룹을 제작했습니다. 그림 2에서 텍스트 pill로 표시된 pivot 그룹은 eBay의 추천 목록을 향상시키기 위해 동적 버튼 역할을 하는 필터로 작용하여 사용자가 관심 있는 주제에 맞게 검색을 세부화할 수 있게 합니다. LLM이 생성한 이 pivot 그룹은 사용자에게 선택한 문맥과 공감하는 제품의 선별된 제품 목록으로 안내를 제공합니다. eBay 재고의 더 포괄적인 탐색을 가능하게 함으로써, pivot 그룹은 변환 확률을 높여줌으로써 쇼핑객들이 자신의 패션 감성과 완벽하게 일치하는 아이템을 찾아 구입하는 것을 더욱 용이하게 만들어 온라인 브라우징을 보다 보람있는 경험으로 만드는 데 도움을 줍니다.

<img src="/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_1.png" />

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

![이미지](/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_2.png)

저희는 새로운 추천 시스템의 효과와 적응성을 입증하기에 이상적인 도메인인 다이내믹하고 스타일과 트렌드의 변화가 큰 여성 드레스 카테고리를 연구 대상으로 선택했습니다. 우리는 사전 오프라인 단계에서 중심 그룹을 생성하며, 저희 방법론은 여성 패션 부문에서 가장 인기 있는 제품과 해당 "유사 스폰서 제품"을 선택하여 최신 스타일 흐름과 소비자 성향을 보장하는 것입니다. LLM의 분석적이고 추론적인 능력을 활용하여 이러한 패션 제품을 그들의 고유한 특성에 기반하여 독특한 주제 그룹으로 세심하게 분류했습니다. 이 전략적인 분류를 통해 여성 패션의 주제 스펙트럼 내에서 다양한 항목이 어떻게 배치되는지 세밀하게 이해할 수 있었습니다. 이 주제 그룹의 세분화 이후, 각 그룹의 항목에서 중요한 특징을 추출하여 풍부한 벡터 임베딩을 생성했습니다. 임베딩은 텍스트 제목이나 텍스트와 시각 정보를 모두 통합한 다중 모달 수단을 통해 유래했습니다.

이 과정은 각 중심 그룹의 평균 임베딩을 계산하여 계속되었습니다. 이는 해당 그룹의 중심적인 임베딩 대표로 작용합니다. 이 근본적인 임베딩은 후속적인 온라인 추천 단계에서 중요한 역할을 합니다. 여기서 후보 항목은 아이템 임베딩을 중심 그룹 임베딩과의 유사성에 따라 동적으로 할당받습니다. 할당은 아이템 임베딩과 중심 그룹 임베딩 사이의 유사성을 활용하여 진행되며, 이를 위해 코사인 거리가 측정 항목으로 사용됩니다. 이 방법론을 통해 추천은 맥락에 부합하고 현재 트렌드와 일치하며, 전반적인 추천 시스템의 효율성을 향상시키는 것이 보장됩니다.

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

저희 방법론의 초기 단계에서는 LLMs를 활용하여 이베이의 여성 드레스 카테고리를 조직하는 중요한 과정인 피벗 그룹을 위한 이름과 설명을 체계적으로 생성합니다. 이 프로세스는 아래의 Figure 3에서 시각적으로 설명되어 있습니다:

![Figure 3](/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_3.png)

이 프로세스는 여성 드레스 카테고리 내에서 인기 있는 최상위 시드 아이템을 선택하는 것으로 시작하여, 이를 통해 각 시드마다 수십 개의 추천 후보 아이템을 무작위로 샘플링하여 수만 개의 아이템으로 이루어진 데이터셋을 형성합니다. 아이템 제목 및 최상위 아이템 측면과 같은 정보는 추출되어 LLM에 제공될 데이터셋을 형성합니다. 이러한 아이템은 일괄로 그룹화되고 각 배치에 대해 LLM은 배치 내 아이템을 특성에 따라 그룹화하여 고유한 피벗 그룹 이름과 설명을 생성하는 작업을 수행합니다. 이러한 이름과 요약은 유용한 정보를 제공하도록 특별히 설계되어 구매자가 판단 과정을 더할 수 있도록 돕는 통찰력을 제공합니다. 이 과정은 그림 2에 나와 있는 것처럼 유용한 정보를 제공하여 구매자의 결정 과정을 풍부하게 해줍니다. 이 프로세스는 고유한 이름과 설명을 갖는 다수의 피벗 그룹을 생성합니다.

이 그룹 이름과 설명이 고객 기반에 적합하고 관련성이 있는지를 확인하기 위해 품질 보증 전문가 팀이 철저한 검토를 진행합니다. 이 방법론 접근은 아이템 분류 프로세스를 최적화할 뿐만 아니라 피벗 그룹이 여성 드레스 부문 내에서 트렌드와 관련 테마를 대표하는지를 보장합니다.

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

## 회전 표현 생성

회전 그룹의 이름과 설명을 최종 확정한 후, 초기 단계와 동일한 샘플링 방법을 사용하여 다른 날짜에 데이터 세트를 추가로 생성합니다. 이는 첫째로, 계절 변화, 신흥 스타일 및 소비자 행동의 변화를 반영한 가장 최신 데이터를 기반으로 회전 표현 생성이 이루어져야 하기 때문입니다. 둘째로, 이러한 실천은 훈련 및 테스트 데이터 세트를 효과적으로 분리하여 LLM의 다양한 시간 스냅샷을 통해 정확하고 잘 적응하는 능력을 더 체계적으로 시험할 수 있습니다.

![image](/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_4.png)

데이터 세트는 일괄 처리로 분할되고 LLM을 통해 처리됩니다. 이때, LLM에게 각 항목을 해당 특성을 기반으로 적절한 회전 그룹으로 분류하도록 안내하는 특별히 설계된 프롬프트를 제공합니다. 이 단계는 항목을 각각의 그룹으로 매핑하는 데 중요하며, 우리에게는 각 회전 그룹이 의도된 주제와 특성을 반영한 올바른 항목을 포함하고 있는지 확인할 수 있습니다.

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

분류 과정을 따라, 우리는 각 그룹 내 모든 항목에 대한 항목 임베딩을 추출하기 위해 임베딩 룩업 캐시인 "NuKV"에서 뽑아낸다. 이를 통해 각 피벗 그룹의 평균 임베딩 벡터를 계산할 수 있다. 기억해야 할 것은 기존 임베딩의 두 가지 유형을 활용할 수 있다는 것이다: eBERT 제목 임베딩 또는 항목 멀티모달 임베딩. 이 벡터는 그룹 내 항목들의 집합적 특성을 대표하는 중심 표현 또는 피벗 센터로 작용한다.

도식화된 접근 방식은 Figure 4에 개요되어 있으며, 각 피벗 그룹이 일관된 테마를 대표하고 있음을 보장하는데 그치지 않고, 그룹 간 항목 분포의 보다 상세한 이해를 용이하게 한다. 각 그룹에 대한 평균 임베딩을 계산함으로써, 우리는 후속 추천 프로세스를 위한 견고한 프레임워크를 정립하고, 제품 제안의 맞춤화와 사용자에 대한 관련성을 향상시키는데 기여한다.

## 온라인 과제

저희 추천 서비스 내에서는 항목의 실시간 할당 및 추천에 대한 정교한 메커니즘을 개발했습니다, Figure 5에 나와있습니다. 이 과정은 상류 단계에서 기억된 항목을 검색하는 것으로 시작되며, 비슷한 후원된 항목의 관련성과 최신 선택을 수집하는 데 중요한 단계입니다.

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

<img src="/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_5.png" />

다음으로, 우리는 항목이 특성과 가장 일치하는 중심 그룹에 동적으로 할당되는 중심 할당 단계를 소개합니다. 항목 임베딩과 중심 그룹 임베딩 사이의 코사인 거리를 활용하여 가장 가까운 일치를 결정합니다. 이 방법은 각 항목이 가장 적합한 중심 그룹에 할당되어 사용자에게 제공되는 추천의 관련성과 일관성을 보장합니다.

각 항목을 해당 중심 그룹에 할당한 후, 각 그룹 내 항목은 선행 순위 결정 단계에서 유도된 개별 순위 점수에 따라 순위가 매겨집니다. 여기서는 항목을 XGBoost 랭커로 훈련하여 속성에 따라 항목에 점수를 매기고 eBay의 비즈니스 규칙을 사용합니다. 중심 그룹 자체의 표시 순서를 최적화하기 위해 각 그룹은 중심 내 모든 항목의 순위 점수를 집계한 평균으로 계산된 점수가 할당되어 구매자에게 총괄적인 관련성과 매력에 기반한 중심 그룹의 전략적 순서를 허용합니다.

## 온라인 실험 결과

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

우리의 신기술인 Pivot Group 방법이 사용자 경험 및 추천 관련성 향상에 미치는 영향을 엄밀히 평가하기 위해 여성 드레스 카테고리 내 미국 사이트에 중점을 둔 A/B 테스트를 실시했습니다. 이 실험은 데스크톱 웹 뷰 아이템 페이지의 두 번째 위치에 배치된 기존 추천 변형(Control)과 Pivot Group 변형(Treatment)의 성능을 비교하기 위해 설계되었습니다. 본 실험은 가시성과 사용자 참여를 보장하기 위해 두 그룹 간의 트래픽을 공평하고 정확하게 분배하는 데 초점을 맞추었습니다.

저희의 온라인 실험 결과, 주요 참여 지표인 구매(Purchases) 및 클릭(Clicks)에서 이전에 데스크톱 웹 플랫폼에서 사용한 모델과 비교하여 통계적으로 유의한 개선 사항이 발견되었습니다:

![Figure 6](/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_6.png)

![Figure 7](/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_7.png)

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

결과는 Pivot Generation 접근 방식이 사용자의 관심을 끌 뿐만 아니라 구매를 유도하는 데도 효과적임을 확인합니다.

## 결론 및 다음 단계

아이템 특성을 기반으로 Pivot 그룹을 생성하기 위해 LLMs를 혁신적으로 활용함으로써, 이 방법은 제품 추천의 맞춤화와 관련성을 크게 향상시켰습니다. 우리의 포괄적인 접근 방식은 사용자 선호도와 시장 트렌드에 대한 심층적인 이해를 시사합니다.

우리의 방법론은 다양한 스타일 범주로 특징 지어지는 카테고리를 포함한 광범위한 범주에 대한 확장 및 적응 가능성을 명확히 보여줍니다. 우리의 파이프라인의 기본 원리는 아이템을 주제별 그룹으로 분할하는 데 LLMs를 활용하는 것으로, 이는 본질적으로 변화 가능하고 확장 가능한 프로세스입니다. 이 유연성은 우리의 방식이 핵심 프롬프트 구조를 심각하게 수정하지 않고도 다른 범주에 적용될 수 있음을 시사합니다. Pivot 그룹 방법의 확장 가능성은 여성 드레스 이상의 효과를 보여주는 그림 7 및 8에 나타난 결과와 함께 핸드백이나 구두와 같은 다른 다양한 범주에 그 유용성을 확장하는 개념 증명을 통해 입증되었습니다.

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

<img src="/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_8.png" />

<img src="/assets/img/2024-05-23-TransformingFashionDiscoveryinE-CommercethroughTheme-BasedCategorizationwithGenAI_9.png" />

그러나 저희의 파이프라인의 일반적인 프레임워크가 넓은 적용 범위를 제공하는 한편, 특정 카테고리별 세부 사항이 프롬프트에 맞게 조정되어야 하는 경우가 있을 수 있습니다. 이러한 맞춤화는 서로 다른 카테고리의 독특한 특성과 스타일적 요소가 정확히 포착되고 피벗 그룹화에 반영되도록 보장합니다.

Chen Xue
그리고 Xuyan Zhou도 이 기사에 기고했습니다.

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

eBay TechBlog
