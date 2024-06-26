---
title: "업리프트 모델링 상관관계에서 인과관계로"
description: ""
coverImage: "/assets/img/2024-05-23-UpliftModelingcorrelationtocausation_0.png"
date: 2024-05-23 18:01
ogImage:
  url: /assets/img/2024-05-23-UpliftModelingcorrelationtocausation_0.png
tag: Tech
originalTitle: "Uplift Modeling: correlation to causation"
link: "https://medium.com/@burakozen/uplift-modeling-b2dda3fd9f11"
---

대부분의 기업이 새로운 캠페인이나 프로모션을 도입했을 때 해당 캠페인이나 프로모션이 사용자 베이스에 효과가 있는지를 예측하는 ML 모델이 필요한 경우가 많습니다. 이러한 노력의 궁극적인 목표는 해당 캠페인들을 통해 더 많은 사용자를 전환하거나 CTR과 같은 특정 비즈니스 지표를 향상시키는 것일 수 있습니다. 물론 비즈니스 유형에 따라, 캠페인은 할인이나 충성도 프로그램과 같은 다양한 형태로 나타날 수 있습니다.

간편함을 위해 이렇게 설명해 보겠습니다. 우리는 전자 상거래 웹사이트의 투자 수익률(ROI)을 높이기 위해 할인 캠페인을 진행 중이라고 상상해 봅시다. 이 문제를 간단하고 직관적인 방식으로 정식화하는 방법은 머신 러닝 모델을 훈련시켜 제품이 곧 판매될지 여부를 예측하는 것입니다. 비즈니스는 이러한 예측을 스마트하게 활용하여 ROI 지표를 높일 수 있으며, 할인을 적용해야 할 제품은 그렇지 않을 확률이 높은 제품에만 적용할 수 있습니다. 이러한 모델의 경우, 훈련 방법론은 매우 직관적입니다. 우리는 모든 과거 거래(구매) 데이터를 사용해야 할 것입니다. 웹사이트에 제품이 게시된 후 7일 동안 아직 판매되지 않은 경우, 대상 변수는 0(판매되지 않음)이 됩니다. 그렇지 않으면 우리의 레이블은 1(판매됨)입니다. 이것은 기본적으로 알려진 ML 모델인 "제품 판매 가능성" 모델입니다. 이 확률을 계산합니다 → P( Y: 1 | x ), 여기서 Y는 대상 변수(1 — 판매됨)이며 x는 제품, 사용자, 거래 기능의 조합과 같은 기능의 집합입니다.

# 소개

본질적으로, uplift 모델링은 다른 방식으로 동일한 문제에 접근하는 방법론입니다. 여기서 우리가 정말 원하는 것은 제품을 판매하기 위해 할인이 필요한지 여부를 알 수 있는 능력입니다. 다시 말해, 우리는 적용한 할인이 제품의 성공적 거래에 "인과적" 요인이 될지를 알고 싶어합니다. 이는 ML 영역에서 "상관"에서 "인과"로의 전환 지점입니다. 이전 문제 정의에서 우리는 팔 확률이 낮은 제품에 할인을 적용하면 항상 전환을 긍정적으로 영향을 줄 것이라는 강한 가정을 했습니다. 이것이 "할인"과 "구매"의 수 사이의 가정되는 "상관관계"라고 부르는 것입니다.

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

그러나 Uplift 모델링은 판매될 가능성이 낮은 제품에 할인을 적용하지 않는 것을 원칙으로 합니다. 이는 "판매될 가능성이 낮은" 제품을 다양한 대상 하위 그룹으로 만듭니다. Uplift 모델링의 궁극적인 목표는 할인을 적용할 경우에만 판매될 수 있는 특정 제품 하위 그룹을 찾는 것입니다. 우리가 "할인 적용"을 Uplift 모델링 공식에 외부 요인으로 추가했기 때문에, 이제 2 (판매-판매되지 않음) x 2 (할인 적용-할인 적용하지 않음) = 4개의 레이블 데이터 그룹이 있습니다.

![Uplift Modeling](/assets/img/2024-05-23-UpliftModelingcorrelationtocausation_0.png)

## 1. 유기적으로 구매 — 할인 적용 후 구매:

이 집합은 때때로 "확실한 것"이라고 불립니다. 할인을 적용하든 말든, 해당 제품은 어찌되었든 팔릴 것입니다. 분명히 여기서 할인을 적용하지 않는 것이 논리적입니다. 이 제품 그룹에 예산을 낭비해서 ROI를 높이는 것은 우리가 원하지 않을 것입니다.

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

## 2. 할인 적용 전에 구매하지 않은 경우 - 할인 적용 후 구매:

이 집합은 때때로 "설득 가능한"이라고 불립니다. 이것은 업리프트 모델링이 찾고 있는 특정 제품 그룹입니다. 우리가 이러한 제품에만 할인을 적용한다면, 변환율과 ROI에 긍정적인 영향을 줄 수 있습니다.

## 3. 자연적으로 구매한 경우 - 할인 적용 후에 구매하지 않은 경우:

이 집합은 때때로 "무시해야 할 대상"이라고 불립니다. 조금 역설적으로 들릴 수 있지만, 때로는 할인에 부정적으로 반응하는 사용자 또는 제품 그룹이 있을 수 있습니다. 이 그룹에 대해 합리적으로 해야 할 일은 ROI 지표를 손상시키지 않기 위해 할인을 적용하지 않고 넘어가는 것입니다.

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

## 4. 구매되지 않은 유기적 상품 — 할인 적용 후 구매되지 않은 경우:

이 집합은 때때로 "잃어버린 원인"이라고도 불립니다. 이 그룹은 이탈한 것으로 생각할 수 있습니다. 이 제품에 할인을 적용해도 결과를 바꾸어 판매로 전환할 수는 없습니다. 그러나 최종 목표가 ROI 지표를 최적화하는 것이므로, 이 제품 그룹에 어떤 할인도 적용하고 싶지 않습니다.

# 할인 캠페인의 ROI

할인 캠페인의 성능을 측정하기 위해 증분성, 치킨넬라이즈이션이나 투자의 증분된 수익(iROI)과 같은 용어가 종종 사용됩니다.

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

위 용어들에 대한 정의를 하나씩 하기 전에, 이 예시를 위해 몇 가지 숫자에 이름을 붙여보겠습니다:

- N: 할인이 적용된 총 구매 건수 (예: 10000)
- T: 실험군의 총 구매 건수 (예: 25000)
- C: 대조군의 총 구매 건수 (예: 21000)

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

R_t: 치료 그룹의 총 수익 (예: $2.5백만)

R_c: 대조 그룹의 총 수익 (예: $2.1백만)

L: 적용된 할인으로 인한 총 매출 손실 (예: $300천)

간편을 위해, 치료 그룹과 대조 그룹의 규모가 동일하다고 가정하고 경우에 따라 계산이 진행됩니다 →

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

할인 캠페인의 저효요 구매 비율:

▶▶ 증가율 구매 비율 [(T - C) / N] → (25000 – 21000) / 10000 = 4000/10000 = 0.4 = 40%

할인 캠페인의 카니발리제이션 구매 비율:

할인 구매 중 증가치 없이 발생한 부분은 카니발리제이션으로 인한 것입니다. 이들은 할인이 없어도 발생했을 구매입니다. 이 경우, 카니발리제이션 구매의 수는 [N - (T - C)]입니다. 그 비율은 기본적으로 (1 - 증가율 비율)이며, 이는 (1 – 0.4) = 0.6 = 60% 입니다.

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

수익증대비율 (iROI):

이는 적용된 할인으로 인한 총 수익 손실에 대한 치료 및 통제 사이의 증가 수익액의 비율입니다.

[(R_t - R_c) / L] → ($2.5M - $2.1M) / $300K = 1.33

이 비율이 1보다 크면 이 캠페인을 수익성 있는 것으로 볼 수 있습니다.

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

# 모델 파트

주로 얼리프트 모델링은 다음 확률을 계산합니다:

P(Y=1 | x, T=1) - P(Y=1 | x, T=0)

여기서 Y는 우리의 대상 변수입니다 (0 — 판매되지 않음, 1 — 판매됨), x는 특징들의 집합이며, T는 할인이 적용되었는지 (T=1) 아닌지 (T=0)의 처리기본을 나타냅니다. 이 공식은 해당 제품을 할인을 적용하고 판매할 가능성의 차이 (얼프트)를 계산합니다. 다시 말해, 할인을 적용하면 어떻게 이 제품의 판매 기회가 높아질 수 있을까요? (인과관계)

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

이 상승 점수를 계산하기 위한 모델링 접근 방식은 주로 다음과 같이 나열됩니다. 이 블로그 글에서는 이미 많은 정보가 있기 때문에 이러한 접근 방식의 세부 사항에 대해 깊이 파고들지 않겠습니다. 중요한 점 하나는 아래에 나열된 것을 '모델'이 아니라 '접근 방식'로 일컬어주고 있다는 것입니다. 그것은 이러한 접근 방식에서 선호하는 ML 모델을 기본 모델로 사용할 수 있기 때문입니다.

## 접근 방식

이것은 단일 모델 접근 방식으로, 처리 (T)를 다른 특성으로 사용하여 제품의 상승 점수를 계산합니다. 처리 특성 (T)이 1 및 0일 때의 예측 값 차이를 가져와 상승 점수를 계산합니다. 이 방법론의 주요 단점은 처리 (T) 특성이 단일 모델 (m)에서 가장 중요한 특성 중 하나로 편입되지 않는다는 것입니다.

이것은 두 개의 모델 접근 방식으로, 할인 적용 그룹 데이터를 사용하여 하나의 모델을 훈련시키고, 통제 그룹 (할인을 적용하지 않음 - T=0) 데이터를 사용하여 다른 모델을 훈련시킵니다. 제품의 상승을 계산할 때, 이 두 모델 (m1 및 m0)의 결과를 차이를 가져와 상승을 계산합니다. 이 방법론의 주요 단점은 두 모델이 서로 다른 데이터 특성과 분포를 가지고 있기 때문에 비교되지 않은, 동기화되지 않은 점수를 받을 위험이 있다는 것입니다.

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

M1은 처리 그룹 데이터로 훈련된 모델이며, M0는 대조 그룹 데이터만으로 훈련된 다른 모델입니다.

이것은 각 모델이 개별적인 처리 효과(ITE)를 학습하는 두 모델 접근법입니다. 첫 번째 단계는 T-러너와 동일하며, 두 번째 단계는 각 모델을 상대 그룹에 적용하고 다음과 같이 ITE를 계산하는 것입니다:

그런 다음 마지막 단계는 앞서 계산된 ITE로 새로운 대상 변수로 사용하여 그 ITE로 다른 2개 모델을 훈련시키는 것입니다. 이 2개의 새로운 모델을 m1_ITE와 m0_ITE로 지정해봅시다. 최종 업리프트 점수를 계산하는 방법은 다음과 같습니다:

여기서 g(x)는 경향 점수 가중 함수입니다. 예를 들어, 처리 그룹과 대조 그룹이 동일한 크기를 가지고 있다면, g(x)에 0.5 값을 사용할 수 있습니다.

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

기본적으로 uplift 알고리즘은 분할 기준으로 uplift의 차이를 사용하는 트리 기반 알고리즘입니다. 각 분할은 부모 노드와 비교하여 자식 노드에서 처리 및 대조 그룹 간 결과 분포의 차이를 증가시키는 것을 목적으로 합니다.

## Uplift 근사치

실제 uplift 값을 계산하려면 아래의 uplift 공식에 표시된 대로 가능하지만, 동일한 제품에 동시에 할인 적용하거나 적용하지 않는 것 또는 동일한 사용자에게 동시에 할인 가격을 적용하거나 적용하지 않는 것은 실제로 현실 시나리오에서 항상 가능한 것은 아닙니다. 게다가 "구매하지 않음"이라는 정의가 명확하지 않은 경우, 손에 "구매하지 않음" 데이터가 없을 수 있습니다. 이 경우 실제 uplift 값을 대신할 근사치를 도입할 수 있습니다. 이 근사치를 실제 uplift 값 대신으로 생각할 수 있습니다. 손에 있는 것은 성공적인 구매 거래(Y=1) 데이터 뿐이며 이전 거래에 할인이 적용되었는지 여부를 알고 있는 경우입니다. (T=1 또는 T=0).

다음은 성공적인 구매 거래만 사용하여 실제 uplift 값을 평가하기 위한 간단한 설명입니다 →

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

# Uplift Formula: P(Y=1 | x, T=1) - P(Y=1 | x, T=0)

## Step I:

- P(Y=1 | x, T=1) = [P(T=1 | Y=1, x) * P(Y=1 | x)] / P(T=1 | x)

- P(Y=1 | x, T=0) = [P(T=0 | Y=1, x) * P(Y=1 | x)] / P(T=0 | x)

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

Step II:

P(Y=1 | x, T=1) / P(Y=1 | x, T=0) = P(T=1 | Y=1, x) / P(T=0 | Y=1, x)

where P(T=1 | x) and P(T=0 | x) are 0.5 since we assume that the chance of a product which is treated (apply discount) or not treated (no apply discount) is equal.

Step III:

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

P(Y=1 | x, T=1) - P(Y=1 | x, T=0) = P(T=1 | Y=1, x) - [1-P(T=1 | Y=1, x)] = 2\*P(T=1 | Y=1, x) - 1

위 식은 II 단계에서 오른쪽 식을 사용하여 uplift 공식의 두 부분을 대체하는 데 사용됩니다.

실제 uplift 공식의 최종 추정은 다음과 같습니다:

P(Y=1 | x, T=1) - P(Y=1 | x, T=0) = 2\*P(T=1 | Y=1, x) - 1

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

과거 성공한 거래 데이터만 사용하여 Y=1인 모델을 교육하면 해당 성공적 거래에 할인이 적용된 확률을 예측할 수 있습니다. 이제 이러한 데이터가 이미 존재하므로 이러한 모델을 교육하는 것은 더 현실적이고 실현 가능한 목표입니다.

요약하면, 이 작은 응축 근사화 기법을 사용하여 할인이 판매에 미치는 인과 효과를 추정할 수 있습니다. 이러한 방식으로 거래의 인과 요인이 우리가 적용한 할인인지 여부에 대한 답을 얻게 됩니다. 내 의견으로는, 할인과 매출 사이에 강한 상관 관계 가정을 하는 주류 접근 방식보다 더 효과적인 모델링 접근 방식입니다.

![Uplift Modeling](/assets/img/2024-05-23-UpliftModelingcorrelationtocausation_1.png)
