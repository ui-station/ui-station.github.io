---
title: "라벨을 놓치지 마세요 계층적 범주에 대한 대체 인코딩 방법"
description: ""
coverImage: "/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_0.png"
date: 2024-05-18 20:24
ogImage:
  url: /assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_0.png
tag: Tech
originalTitle: "No Label Left Behind: Alternative Encodings for Hierarchical Categoricals"
link: "https://medium.com/towards-data-science/no-label-left-behind-alternative-encodings-for-hierarchical-categoricals-d1bcf00afc37"
---

<img src="/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_0.png" />

데이터 과학자로 일하면서 다양한 레이블을 많이 볼 수 있어요. 우편 번호 레이블, 성별 레이블, 의료 진단 레이블, 직책 레이블, 주식 코드 레이블 등 다양한 종류의 레이블이 데이터에 포함됩니다. 레이블은 간단한 것(S, M, L 같은 셔츠 사이즈)일 수도 있고 복잡한 것(7만 가지가 넘는 의료 질병을 인코딩하는 국제질병분류체계처럼)일 수도 있어요.

데이터에 포함된 레이블을 범주형 특성이라고 해요. "많은" 가능한 값을 가지는 범주형 특성을 고차원 범주형 특성이라고 해요. 고차원 범주형 특성은 머신러닝 모델에서 사용할 때 어려움을 겪게 될 수 있어요. 높은 차원 수는 직접 사용하기 불가능하거나 비실용적이기 때문이에요("차원의 저주"라고 합니다). 그래서 이러한 특성을 간단하게 만드는 다양한 인코딩 방법이 사용되어요.

낮은 빈도 또는 보이지 않는 코드도 고차원 범주형 특성에 도전을 제공할 수 있어요. 예를 들어 어떤 우편번호는 인구가 드물게 분포되어있고, 다른 우편번호는 수백만 명의 사람을 포함하고 있어요; 우리는 어떤 것의 인코딩에 더 확신을 갖게 될 수 있어요. 또한 의료 진단 코드와 같은 일반적인 코드 집합은 정기적으로 업데이트되어, 교육에 사용할 수 없는 보이지 않는 값들을 발생시킬 수 있어요. 보이지 않는 값과 낮은 빈도의 코드는 성장 중인 비즈니스나 제품 또는 교육 데이터가 제한되어있을 때 중요할 수 있어요.

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

인코딩은 반응 정보를 유지하면서 오버피팅을 방지하는 방식으로 저빈도 및 미처 보지 못한 값들을 고려해야 합니다. 트리 기반 모델에서 사용되는 표준 타깃 인코딩에서는 이를 샘플 평균과 전체 평균을 카운트에 비례하여 혼합함으로써 수행합니다.

고차원 범주형 변수들은 그룹으로 구성할 수 있습니다. 예를 들어 우편번호는 군, 주, 또는 지역으로 (대략) 집계될 수 있습니다.

그룹 정보는 낮은 볼륨이거나 보지 못한 코드들을 예측하는 데 도움이 될 수 있습니다. 이 정보를 타깃 인코딩에 통합하여 상위 그룹 수준의 평균을 계층적으로 혼합하는 방식으로 사용할 수 있습니다. 저는 제 마지막 블로그 포스트에서 이를 시도해 봤고, 보지 못한 코드에 대해 성능이 향상된 것을 보았지만, 일부 그룹화에서는 오버피팅이 발생했습니다.

그 이후로, 계층적 범주형 변수에 대한 대안 처리 방법에 대해 고민하고 있습니다. 코드 시스템에 대한 가능한 많은 정보를 포함하는 기능을 설계하는 것이 더 나은지, 아니면 모델이 작업을 수행하게 하는 방법을 찾는 것이 더 나은지 궁금해지기 시작했습니다.

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

여기서는 XGBoost 모델에서 계층적 블렌딩 대안을 테스트합니다. 계층 수준을 분리해서 사용하는 것이 단일 특성으로 블렌딩하는 것보다 성능 개선을 보입니다. 낮은 볼륨 코드를 null로 설정하고 불확실성을 나타내는 다른 값으로 설정하는 간단한 임계치 설정은 오버피팅에 특히 강합니다. 표준 코드 계층에 대해서는 계층 블렌딩이 약간 더 우세하지만 일부 코드 그룹에 대해서는 심하게 오버피팅됩니다.

Shapley 테스트 결과, 다중 특성 인코딩을 사용하면 모델이 범주적 계층의 상위 수준 정보와 다른 예측 변수 정보를 활용할 수 있게 됩니다.

주요 주의점은 모델이 보지 못한 경우에 대해 일반화하기 위해 낮은 볼륨 사례에서 훈련되어야 한다는 것입니다. 제가 검토한 대부분의 타겟 인코딩 변형에는 이 상황이 적용되지만 계층적 인코딩을 제외하고는 예외가 있습니다. 또한, 이 게시물에서 논의된 모든 인코딩 방법은 낮은 볼륨 또는 누락된 코드가 보이지 않는 코드와 다를 경우 편향 리스크를 가지고 있을 겁니다.

그러므로, 훈련 데이터에 null(또는 다른) 값의 무작위 삽입이 모델이 보이지 않는 코드에 대해 일반화하는 법을 가르칠 수 있는지 궁금합니다. 뉴럴 네트워크의 엔티티 임베딩에 대해 비슷한 것을 테스트하고, 보이지 않는 코드에 대해 상당한 성능 향상이 있었습니다. 아마도 XGBoost에 도움이 될 수 있는 유사한 전략이 있을거라 생각됩니다. 훈련 반복/에폭에서 무작위성을 섞어넣을 수 있다면 가장 좋을 것입니다.

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

# 방법들

제가 사용하는 XGBoost 이진 분류 모델은 미국 소기업 행정청(SBA) 대출 데이터 세트에서 기관들의 대출 연체를 예측하는 데 사용됩니다. 이 데이터는 공개 데이터셋이며(CC BY 4.0) 방법들은 주로 이전에 설명되었습니다. 모든 코드는 GitHub에서 확인하실 수 있습니다.

저는 NAICS(산업 특성)를 인코딩합니다. NAICS 코드는 공식적으로 5단계 계층 구조를 가지며, 다양한 정밀도의 Bucket으로 기관의 유형을 그룹화합니다. 예시는 다음과 같습니다:

![Table 1](/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_1.png)

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

과거 방법과의 중요한 차이점 중 하나는 가끔 블렌딩 중점의 다른 값(100)을 사용한다는 것입니다. 이것은 중점에 민감한 새로운 방법과 비교하기 위해 성능을 비교하는 데 도움이 됩니다. 결과는 [3]의 것과 매우 유사하지만 완전히 동일하지는 않습니다.

# 대안 인코딩 탐구

다음은 내가 시도할 인코딩 변형들입니다:

1. 타겟 인코딩 (NAICS만): NAICS 특성의 표준 타겟 인코딩(특성 1개)

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

2. Hierarchical Blending: 관층 층위의 그룹 평균을 관측된 비율에 혼합하여 응답을 더 잘 추정하는 대상 인코딩 (1 특성)

3. 대상 인코딩 (모두): NAICS 계층구조의 모든 수준에 대한 표준 대상 인코딩 (5 특성)

4. 대상+카운트 인코딩: 4와 동일하지만 추가 카운트 인코딩 특성 포함 (10 특성)

5. 대상-임계 인코딩: 표준 대상 인코딩과 유사하지만, 낮은 체적/보이지 않는 값이 대상(또는 기타) 평균 방향으로 축소되는 대신, 절단점 아래의 값만 null로 설정합니다. (5 특성)

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

위의 모든 경우에 있어서, 가장 낮은 수준의 NAICS 특성의 인코딩은 매우 유사합니다. 실제로 1, 3 및 4에 대해 동일합니다. 2 및 4에 대해서는 변형이 낮은 볼륨이나 보이지 않는 코드에서만 발생합니다. 메소드 3-5는 가장 낮은 수준의 코드를 인코딩하는 것을 넘어 추가적인 기능을 포함합니다.

타겟 인코딩 (NAICS만): 표준 타겟 인코딩은 범주형 특성을 해당 범주에 대한 평균 응답(대출 채무 불이행률)으로 대체합니다. 평균은 누출을 피하기 위해 학습 데이터에서 계산됩니다.

낮은 볼륨의 코드에 대해 평균 추정치는 신뢰할 만하지 않으며, 오버피팅 위험이 있습니다. 따라서 타겟 인코딩은 일반적으로 매우 낮은 볼륨(또는 보이지 않는) 코드에 대해 타겟 평균과 블렌딩하는 것을 포함하여 낮은 볼륨 코드에 대해 타겟 평균과 유사한 값을 얻는 동시에 높은 볼륨 코드는 거의 실제 응답으로 매핑됩니다. "낮은 볼륨"의 의미는 타겟 비율에 따라 다르며, 블렌딩은 매개변수화됩니다. 여기서는 블렌딩 중점과 폭을 변형하는 시그모이드함수를 사용합니다(일부 테스트에서 중점을 변형합니다).

계층적 블렌딩: 계층적 블렌딩은 타겟 인코딩의 변형으로, 높은 수준의 NAICS 그룹 평균을 사용하여 낮은 볼륨이나 보이지 않는 코드에 대한 평균 응답을 더 잘 추정합니다. 응답은 계층의 모든 가능한 수준의 평균과 함께 사용되며, 각 수준을 가중하는 데에 동일한 시그모이드 함수를 사용합니다.

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

Target Encoding (All): 이 방법은 NAICS만 사용하는 대상 인코딩(Target Encoding)과 동일하지만 더 높은 수준의 코드를 대상으로 인코딩합니다. 예를 들어 NAICS 부문이나 산업 그룹을 대상으로 인코딩하여 총 5개의 기능을 생성합니다.

비선형 모델이 필요할 때 더 높은 수준의 기능을 "자동으로" 포함시킬 수 있다는 것은 어느 정도 이해할 만한 일입니다. 반면에 대부분의 코드에 대해서는 높은 수준의 그룹 기능이 추가 정보를 제공하지 않을 수 있습니다.

Target+Count Encoding: XGBoost가 카운트 정보를 활용하여 대상 인코딩된 기능이 저수량 또는 보이지 않는 코드에 대해 신뢰할 수 없다는 것을 추론할 수 있을지 궁금했습니다. 따라서 제가 시도하는 "Target+Count" 인코딩은 Target Encoding (All)에 카운트 인코딩 기능을 추가하는 방식으로 총 10개의 기능을 생성합니다. 일정 수준 이상에 해당하는 카운트 인코딩 값을 임계값으로 설정하며, 이는 응답 비율의 95% 이상이 샘플 평균에 의해 혼합되었다고 볼 수 있는 지점에 해당합니다.

Target-Thresh Encoding: 비선형 모델이 카운트 정보를 사용하여 특징에 대한 응답을 수정할 수 있다면, 누락된 값이나 특정 값에서 유사한 정보를 얻을 수 있을지도 모릅니다. 저수량 코드의 평균을 알 수 없다고 모델에게 알려주고 스스로 결정하게 하는 것이 어떨까요? Target-Thresh 인코딩은 높은 수준의 부피 코드에 대해 대상 인코딩과 동일하지만 저수량 또는 보이지 않는 코드에 대한 값은 null로 설정합니다.

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

# 그렇다면, 그들의 성능은 어떤가요?

일반적인 무작위 학습/검증/테스트 분할을 수행하며, 또한 NAICS 코드의 10%를 샘플로 설정하여 보지 못한 값들에 대한 성능을 평가합니다 (자세한 내용은 [3] 참조). 저는 정밀도-재현율 곡선 아래 면적(PR-AUC) 메트릭을 보고하는데, 이는 부도 감지를 강조하기 때문에 높은 값일수록 더 좋습니다.

![image](/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_2.png)

테스트 데이터

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

Figure 1의 왼쪽 패널은 테스트 데이터셋에서의 성능을 보여줍니다. 표준 타겟 인코딩과 계층적 블렌딩은 대부분의 중간 지점에서 매우 유사합니다. 그러나 타겟 인코딩(모두), 타겟+카운트, 타겟-임계치와 같은 세 가지 다중 변수 인코딩 방법은 약간의 성능 향상을 보여줍니다. 이 세 곡선은 서로 매우 유사합니다.

모든 인코딩 방법에서 성능은 매우 높은 중간 지점에서 악화되는데, 이는 평균 응답에 대한 정보가 손실되기 때문으로 예상됩니다 (전체 타겟 비율은 약 20%임에 유의하십시오). 악화가 가장 심한 것은 코드 계층 구조에서 정보를 포함하지 않는 표준 타겟 인코딩입니다.

만약 Figure 1B의 왼쪽 패널만 보면, 다중 필드 인코딩 중 하나가 가장 좋다고 결론을 내릴 수 있을 것입니다. 그렇다면 보이지 않는 코드들은 어떨까요?

홀드아웃 데이터

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

Figure 1의 오른쪽 패널은 보이지 않는 NAICS 코드에 대한 성능을 보여줍니다. 보편적으로, 왼쪽 패널보다 값이 낮게 나타날 수 있습니다. 이는 훈련 시에 사용되지 않은 코드의 평균 응답이 없기 때문입니다.

NAICS만 사용하는 타겟 인코딩은 NAICS 계층을 완전히 무시하는 유일한 방법이지만, 성능이 가장 나쁩니다. 현재 계층적 블렌딩이 가장 강력해 보이며, 블렌딩 중간점에 민감하지 않은 것으로 나타납니다. 특성 다중 방식은 미묘한 성능 향상이 있어 보입니다. 나중에 이에 대해 더 자세히 이야기하겠습니다.

Figure 1의 오른쪽 패널 결과를 살펴보면, 보이지 않는 코드가 중요할 때 계층적 블렌딩이 선호될 것으로 결론 낼 수 있을 것 같습니다. 그러나 이전 글\[3\]에서 일부 코드 그룹에 대해 계층적 블렌딩이 과적합 문제에 직면했다는 것을 알았습니다. 그러므로 더 많은 실험을 해봐야 할 것 같습니다.

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

# 다른 코드 계층에 대해 어떻게 생각하세요?

NAICS에는 널리 사용되는 표준 계층이 있습니다. 그러나 다른 체계를 갖는 다른 코드는 어떨까요? 레벨 수가 다르거나 세분화가 다를 수도 있습니다. 모든 계층 테스트에서는 중간점/임계값을 100으로 고정합니다.

NAICS 표준 계층 변형

모델이 계층 구조 변형에 어떻게 반응하는지 감을 잡기 위해 동일한 인코딩 방법을 사용하지만 NAICS 계층 구조의 여러 지점에서 시작합니다. 모든 5단계를 사용하는 대신 기본 NAICS를 사용한 다음, 예를 들어 3단계부터 그룹화합니다. 도표 2는 표준 NAICS 계층에서 시작점을 변경했을 때의 성능을 보여줍니다.

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

![Random Test Dataset](/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_4.png)

랜덤 테스트 데이터셋의 경우(왼쪽 패널), Figure 2 결과는 Figure 1과 매우 유사합니다. 계층적 블렌딩은 표준 타겟 인코딩과 매우 유사하지만, 멀티 필드 인코딩 방법은 성능을 더 향상시킵니다. 이 패턴은 인코딩이 이루어지는 레벨에 관계없이 발생합니다.

Figure 2의 오른쪽 패널은 보이지 않는 코드에 대한 성능이 계층이 변경됨에 따라 강하게 변하는 것을 보여줍니다. 계층적 블렌딩의 경우, 블렌딩에 가장 높은 수준의 그룹(섹터)만 사용하는 것은 실제로 간단한 타겟 인코딩보다 성능이 더 나빠집니다(이 결과는 이전에도 보였음 [3]).

DGI 피처 기반 그룹화

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

기존 NAICS 계층을 수정하는 것 외에도 완전히 다른 코드 그룹을 시도해보겠어요. 제 마지막 글 [3]에서 Deep Graph Infomax (DGI) 및 클러스터링을 시도하여 예측 필드를 기반으로 그룹을 생성했어요. DGI 그룹은 모델 응답과 상관관계가 있지만 예측 필드 이상의 추가 정보는 담고 있지 않아요.

이전에 DGI 그룹은 계층적 블렌딩에 대한 과적합을 초래했는데, 이 방법은 다른 예측자들과 중복되는 경우에 위험할 수 있다는 것을 시사합니다 [3]. 다른 방법들은 DGI 그룹에 어떻게 반응할까요?

![그림 3](/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_5.png)

그림 3은 DGI 그룹이 과적합으로 성능 저하를 일으키는 경우가 많다는 것을 보여줍니다. 이는 타겟 인코딩 (모든) 및 타겟+카운트 인코딩에 대해 가장 심하게 나타납니다. 계층 블렌딩은 일부 계층 수준에 대해 과적합되지만, 다른 것에 대해서는 과적합이 발생하지 않아요. DGI 그룹에 대해서는 Target-thresh가 과적합에 저항하는 것처럼 보여요.

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

완전히 무작위 그룹

DGI 그룹화로 과적합 현상이 발생할 때, 무작위 그룹을 사용해 볼까 하는 생각이 들었습니다.

무작위 그룹은 코드 구조가 문제와 관련이 없는 경우를 시뮬레이션합니다. 코드 시스템은 종종 정부나 산업 전문가들에 의해 그들 자신의 목적을 위해 유지보수되는데, 이는 귀하의 관심 대상과 관련이 없을 수 있습니다. 예를 들어, 대출 채무 모델에서 산업을 알파벳순으로 구성한 계층구조를 사용했다고 상상해 보세요.

![이미지](/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_6.png)

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

**표 4**는 완전히 무작위로 생성된 그룹의 결과를 보여줍니다. 대부분의 인코딩 방법들은 계층을 전혀 고려하지 않으며, 이는 안심스럽습니다. 그러나 계층적 블렌딩은 상당히 오버피팅됩니다!

**특성 중요도**

Shapley 테스트는 다른 인코딩 방법들이 예측에 어떤 영향을 미치는지에 대한 상세 정보를 제공할 수 있습니다. Figure 5에서는 개별 SHAP 값들이 집계되어, 특정 테스트 케이스에 대한 전체 응답을 보여줍니다.

![Figure 5](/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_7.png)

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

Figure 5에서 많은 내용이 있지만 먼저 파란 막대부터 시작해보세요. 파란 막대는 "기타" (NAICS 관련이 아닌) 예측 변수를 나타냅니다. 이러한 막대는 왼쪽 열의 것보다 오른쪽 열의 플롯에서 더 깁니다. 보이지 않는 코드에 대해서는 모델이 NAICS 인코딩 외의 특성에 더 의존하게 됩니다. 이 효과는 다중 특성 방법(target encoding(전체), target+count encoding, target-thresh encoding)에서 가장 강합니다.

주황색 막대는 저수준 NAICS 코드의 인코딩 중요성을 보여줍니다. 시험 데이터에서는 차이가 작지만, 홀드아웃 데이터에서는 계층적 혼합이 두드러집니다. 이러한 특성에 대한 높은 의존성은 위에서 관찰된 과적합과 일관성이 있습니다.

Target encoding은 NAICS만을 기반으로 대출 연체를 예측하는 간단한 모델일 뿐이며, 이러한 예측값은 XGBoost에 공급됩니다. 계층적 혼합은 더 복잡한 전단 모델입니다. 여기에는 바이어스-분산 균형이 존재하는 것으로 보이며, 계층적 혼합은 과적합됩니다.

마지막으로, 녹색 막대는 계층 구조의 상위 수준에서 파생된 중요한 특성을 보여줍니다(또는 카운트); 이러한 특성은 다중 특성 방법에만 존재합니다. 상위 수준 효과는 표준 NAICS 계층 구조(플롯의 두 번째 행)에서 가장 강합니다. 표준 계층 구조는 모델에 추가 정보를 제공하는 반면, DGI 및 무작위 인코딩은 거의 가치를 제공하지 않습니다.

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

더 간단한 인코딩 방법을 사용하면 기본 기능이 더 뒤로 밀릴 수 있어요. 정보가 불완전한 경우 특성에 너무 의존하는 대신 모델이 대안 소스를 찾도록 합시다.

# 결측값에 대해 어떻게 생각하세요?

나는 훈련 데이터의 구성, 즉 저량 및 결측 코드의 모두가 중요한 고려사항이라고 생각해요.

내 데이터셋에는 결측값이 없어요. 원본 데이터에는 주로 오래된(2000년 이전) 대출에 누락값이 있었는데, 그 경우들을 삭제했어요 [3]. 장기 대출은 주로 낮은 채무 불이행률을 보이는 편이에요.

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

임의의 결측값이 결과값과 상관없는 것은 모델이 알 수 없는 값을 처리하는 방법을 학습하는 데 도움이 될 것이라고 생각합니다. 그러나 이 데이터셋에서는 알 수 없는 값에 편향이 있을 가능성이 있습니다. 왜냐하면 그 값들이 대출 연령을 반영하기 때문입니다. 따라서, target+thresh가 보이지 않는 코드들을 위해 null을 사용한다면, 성능이 좋지 않을 것으로 예상되며, 특히 결측값이 적은 코드보다는 행이 더 많을 가능성이 있기 때문입니다.

훈련 데이터에 결측값이 없다면, target 인코딩은 보이지 않는 레이블에 대해 일반화하기 위해 낮은 빈도 코드에 의존해야 합니다. Figure 1을 기억해보세요. 거기서 풀드 메소드의 성능이 약 100 앞에서 보이지 않는 코드에 대해 상승했던 것을 기억하실 겁니다. 보통 target 인코딩의 이상적인 중간점은 대부분 타겟 비율에 의존한다고 생각합니다. 그러나 Figure 1의 경우, 중간점은 교육에 충분한 낮은 빈도 케이스들이 더 의존할 수 있도록 하는 데 더 많이 의존합니다. 창 비유로 돌아가보겠습니다. 그 창은 충분히 열려 있어야 합니다. 모델이 다른 기능에 의존하는 방법을 배우기 위해 불확실한 비율을 대표하는 예가 충분해야 하기 때문입니다.

Figure 1에서도 보이듯이 보이지 않는 코드에 일반화하기에 충분히 높은 중간점과 너무 높은 중간점으로 인한 정보 손실 사이에 긴장이 있음을 알 수 있습니다. 충분한 데이터가 있고 NAICS가 불균형하게 분포되어 있으므로 작동 가능한 범위가 있습니다. 항상 이렇게 될까요? 올바른 균형이 없는 데이터셋도 있을까요?

아래 그림에서 Figure 1과 똑같은 인코딩을 수행하지만, XGBoost 모델을 적합하기 전에 훈련 데이터에서 낮은 빈도 코드를 제거합니다:

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

![그림](/assets/img/2024-05-18-NoLabelLeftBehindAlternativeEncodingsforHierarchicalCategoricals_8.png)

피규어 6는 훈련 데이터에 널 값이 없는 경우 target+thresh가 성능이 좋지 않음을 보여줍니다. 그림 6의 시나리오에서 훈련 데이터 평균으로 채워 target+thresh를 사용하는 경우, target 인코딩(모두) 및 target+count(표시되지 않음)와 매우 유사한 성능을 보입니다.

그림 6은 피팅 전에 훈련 데이터의 변경에 영향을 받지 않는 계층적 블렌딩 결과를 보여줍니다. 계층 구조의 의미에 대한 모든 정보는 인코딩된 피처에 포함되어 있습니다. 그러나 다른 방법은 모델이 낮은 볼륨 또는 누락된 코드를 보상하는 것을 학습하는 데 의존합니다.

저는 결과가 훈련 데이터의 낮은 볼륨 및 보이지 않는 경우의 특성에 얼마나 의존하는지 조심스럽게 생각합니다. 트레이닝 값을 누락되거나 타겟 평균으로 설정하는 어떤 형태의 무작위화가 도움이 될 수 있다고 생각합니다. 무작위 누락된 케이스를 사용하면 편향이 감소되고 충분한 관련 훈련 예제가 있는지 확실할 수 있습니다. 이러한 무작위화의 단점은 정보 손실인데, 훈련하는 동안 무작위화 및 각 부스팅 라운드마다 다른 관측을 널 값으로 설정하는 것이 좋을 수도 있습니다.

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

# 다른 모델 유형은 어떻게 되나요?

XGBoost를 테스트해보았고, 결과가 다른 부스팅 트리 모델에도 적용될 것으로 예상합니다. 누락된 값이 허용되지 않는 모델의 경우, target-thresh는 상수 값을 채워야 합니다. 이 부분에 대해 여러 번 테스트를 진행한 결과, 상수 값으로 채우는 것이 괜찮다고 보입니다. 실제로, 그림 6에서 상수 값으로 채우는 것이 훈련 데이터에 낮은 양의 코드가 있는 경우에 선호될 수 있다는 것을 보여줍니다. 그러나 특정 경우에는 여전히 문제가 될 수도 있지 않을까 싶습니다. 예를 들어, 일부 코드의 기본 비율이 전체 평균과 유사한 경우 등.

다중 특성 인코딩의 경우, 랜덤 포레스트 모델은 고수준 그룹 특성을 더 많이 활용할 것으로 예상됩니다 (XGBoost의 열 샘플링이 유사한 효과를 낼 수 있음). 이는 모든 관측치에 영향을 미칠 것이지만, 낮은 양의 또는 보지 못한 코드에 대해 도움이 될 수도 있습니다.

트리 기반 모델에서는 타겟 인코딩이 자주 사용되지만, 신경망 모델에서는 entity embeddings에 대한 유사한 고려 사항이 적용됩니다. 이전에, 신경망 모델이 unseen 케이스에 대해 훈련 데이터 입력을 무작위로 설정하지 않으면 심하게 과대적합되는 것을 발견했습니다. 과대적합이 심한 경우에는 N=2 예시를 든 entity embeddings에 무작위화가 일반화에 도움이 될 것으로 제안되었습니다. 현재 몇 명의 학생들에게도 이것을 권고했는데, 이들 또한 과대적합이 크게 감소했습니다. 따라서, N=2 예시에서는 무작위화가 entity embeddings의 일반화에 도움되는 것으로 보입니다. 앞으로 유망한 전략일지도 모릅니다.

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

# 마지막으로

저는 고차원 범주형 데이터에 의존하는 모델을 구축하여 생계를 유지하는 사람입니다. 제가 원하는 것은 보고 있거나 보지 못한 코드에 대해 양호한 성능을 제공하며, 과적합 위험이 낮은 쉽게 사용할 수 있는 시스템입니다. 아직은 테스트한 방법 중 어느 것도 완전하다고 느끼지 않지만, 어떤 방법들은 근접하고 있는 것 같습니다.

편리한 시스템은 목표 임계치 부여 및 최적 임계치를 결정하기 위한 피팅이 포함된 것일 수 있습니다(scikit-learn의 TargetEncoder와 유사한 방식). 또한, 모델이 고수준 기능으로부터 학습할 수 있도록 일종의 무작위 무효화/채움이 필요할 것입니다. 훈련 데이터에 충분한 양의 저주파 코드가 있는지 여부를 학습할 수 있어야 하며, 이미 존재하는 결측치가 데이터 편향을 반영하는 경우에도 모델이 학습할 수 있어야 합니다. 사용 편의성을 위해, 무효화가 모델 훈련에 내장되어 있으면 좋고, 데이터 섞기도 정보 손실을 줄일 수 있습니다.

앞으로 이러한 아이디어를 더 탐구하고 싶습니다! 댓글에서 제안을 듣고 싶습니다. 여러분은 범주형 특성을 어떻게 처리하나요? 어떤 문제를 겪어보셨나요?

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

# 참고 자료

[1] D. Micci-Barreca, Extending Target Encoding (2020), Towards Data Science.

[2] D. Micci-Barreca, A preprocessing scheme for high-cardinality categorical attributes in classification and prediction problems (2001), ACM SIGKDD Explorations 3 (1).

[3] V. Carey, Exploring Hierarchical Blending in Target Encoding (2024), Towards Data Science.

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

[4] M. Li, A. Mickel and S. Taylor, 해당 대출을 승인해야 할까요 거부해야 할까요?: 클래스 할당 지침이 포함된 대규모 데이터셋 (2018), 통계 교육 저널 26 (1). (CC BY 4.0)

[5] M. Toktogaraev, 해당 대출을 승인해야 할까요 거부해야 할까요? (2020), 캐글. (CC BY-SA 4.0)

[6] V. Carey, GitHub 저장소, https://github.com/vla6/Blog_gnn_naics.

[7] 미국 센서스국, 북미 산업 분류체계.

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

[8] Scikit-Learn Documentation, Target Encoder의 내부 교차 적합 (2024).
