---
title: "인과 검증 만병 통치제"
description: ""
coverImage: "/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_0.png"
date: 2024-05-17 19:57
ogImage:
  url: /assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_0.png
tag: Tech
originalTitle: "Causal Validation: A Unified Theory of Everything"
link: "https://medium.com/towards-data-science/causal-validation-a-unified-theory-of-everything-1d6cdb31c1ee"
---

![Causal Inference](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_0.png)

# 소개

인과 추론은 머신 러닝 내에서 부상 중인 분야로, 무엇이 발생할 수 있는지 예측하는 것을 넘어 그 이유를 설명하고, 그로 인해 문제의 근본적인 해결책을 제시함으로써 잠재적인 파급효과를 다루는 대신 지속적으로 해결책을 제공합니다.

인과 모형의 주요 구성 요소 중 하나는 변수와 사건 간의 인과 관계를 간단한 시각적 형식으로 포착하는 "유향 비순환 그래프" (DAG)입니다. 그러나 DAG의 주요 문제점은 일반적으로 도메인 전문가에 의해 주관적으로 구성된다는 것입니다.

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

따라서 DAG가 정확하다는 보장이 없습니다. 만약 DAG가 부정확하다면 인과 추론 모델의 연산과 결론이 잘못될 수 있습니다.

인과 유효성은 DAG를 기반 데이터와 비교하여 오류나 모순을 식별하고 수정하는 과정을 설명하는 용어입니다. 이 작업을 신뢰성 있게 수행할 경우 인과 추론의 결론 및 관련 조치와 변경 사항이 개선된 영향과 결과로 이어질 것입니다.

## 문제점

DAG가 "잘못" 되는 여러 가지 방법이 있습니다. 인과 관련 문헌에서 다양한 유형의 오류에 대한 참조를 찾을 수 있지만, 인과 유효성을 종합적으로 다루는 방법에 대해 많은 정보가 없습니다.

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

## 기회

만약 DAG(Directed Acyclic Graph)에서 모든 종류의 오류를 찾아 수정할 수 있는 통합적인 인과 유효성 알고리즘이 개발된다면, 영역 전문가의 주관적인 지식에 의존을 줄이고 인과 추론 모델의 신뢰성을 크게 향상시킬 수 있을 것입니다.

## 계획 수립

다른 종류의 DAG 오류에 대한 개별 알고리즘을 결합, 통합 및 테스트하여 인과 유효성을 위한 통합 이론을 만들 수 있는 방법에 대해 고민해 보겠습니다.

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

# 배경

다른 머신 러닝 문제와 마찬가지로 인과 추론 프로젝트는 기능 세트로 구성된 데이터로 시작됩니다…

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_1.png)

인과 추론 모델의 목적은 모든 다른 변수의 효과가 고려되고 조정된 상태에서 치료(일반적으로 "X"로 레이블링)의 실제 효과를 결과(일반적으로 "Y"로 레이블링)에 수립하는 것이며, 시작점은 전문가들이 인과 관계에 대한 주관적 이해를 포착하기 위해 유향 비순환 그래프를 만드는 것입니다…

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

![Causal Validation Example](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_2.png)

통합 인과 유효성 탐구를위한 가정의 예제로는 현실 세계에서의 의미가 없는 X, Z1, Z3 등의 노드 이름을 포함한 다양한 유형의 인과 관계가 포함 된 충분히 간단한 예제가 선택되었습니다.

그러나 이해를 돕기 위해 X가 새 약 복용 수준을 나타낼 수 있고, W는 환자의 혈압에 미치는 약의 영향을 나타낼 수 있으며, Y는 환자 회복에 대한 영향을 나타낼 수 있습니다.

이 예에서 화살표의 방향이 의미가 있습니다. 명백히 약을 복용하는 것은 혈압에 변화를 일으킬 수 있지만 그 반대는 아니며, 혈압의 변화가 환자 결과에 변화를 일으키는 경우도 마찬가지입니다. 그 반대는 매우 그럴듯하지 않습니다.

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

예시의 이 단계에서는 데이터가 캡처되었고 제안된 관계를 나타내는 DAG가 구성되었습니다.

이것은 인과 유효성을 설명하기 위해 디자인된 테스트 케이스이므로 DAG는 정확합니다. DAG와 데이터가 함께 가짜로 생성되었기 때문에 확실합니다. 그러나 만약 이것이 현실 세계의 문제였다면 어떨까요?

도메인 전문가들이 잘못된 다른 DAG를 만들었다면 어떤 종류의 실수를 저질렀을까요?

## 누락된 엣지 오류

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

첫 번째 유형의 오류는 빠진 링크나 엣지입니다 (링크와 엣지는 상호 교환 가능한 용어로 동일한 의미를 가지며, 한 노드/기능과 다른 노드/기능 간의 연결을 의미합니다).

도메인 전문가들이 실제로 데이터에 존재하는 몇 가지 엣지를 완전히 누락할 수 있습니다. 예를 들어, 데이터와 현실 세계 사이에 Z3와 Y 간의 인과 관계 링크가 존재할 수 있지만 DAG에는 반영되지 않은 경우 …

![image](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_3.png)

이 예시에서 DAG에는 누락된 엣지 오류가 포함되어 있으며, 데이터에 존재하는 엣지나 링크가 DAG에서 누락되면 인과 모델의 모든 계산과 결론이 잘못될 수 있습니다.

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

## 잘못된 엣지 오류

두 번째 유형의 오류는 가짜 엣지입니다. 즉, DAG에서 실제로 존재하지 않는 엣지가 식별되고 나타납니다.

예를 들어, 도메인 전문가들이 Z1과 W 사이에 인과 관계가 있다고 제안했지만, DAG에 이 연결이 만들어졌지만 실제 데이터에는 존재하지 않을 수 있습니다...

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_4.png)

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

만약 데이터에는 존재하지 않지만 DAG에서 실제로 존재하지 않는 추가적인 인과 관계 링크가 식별되면, 인과 관계 계산 결과를 햇살이다는 결론을 내리는데 오류를 유발할 수 있습니다.

## 역방향 엣지 오류

마지막 오류 유형은 역방향 엣지 오류입니다. 즉, 전문가들이 두 기능 간의 관련을 정확하게 식별했지만 방향성을 잘못 이해한 경우입니다.

예를 들어, 전문가들이 X와 Z3 사이에 인과 관계 링크를 제안했지만 실제 엣지 / 링크는 Z3에서 X로의 반대 방향에 있을 수 있습니다...

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

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_5.png)

DAG에서 실제 인과관계의 방향과 반대 방향에 있는 가장류가 원인을 계산하면 잘못된 답변도 나올 수 있습니다.

# 인과 관계 유효성 검사 — 가장류 오류 감지 및 수정

첫 번째 단계는 DAG에 어떤 유형의 오류가 존재하는지 이해하는 것입니다. 위에서 설명한 것처럼, 오류가 총 3가지 유형이 존재합니다 — 누락된 가장류, 부적절한 가장류 및 반전된 가장류입니다.

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

그 외 카테고리나 에러 유형이 없으므로 각각의 3가지 유형에 대한 인과 검증의 해결은 인과 검증을 위한 모든 것에 대한 통일된 이론으로 이어져야 합니다.

## 독립과 의존의 중요성

독립과 그 반대인 의존의 중요성이 DAG의 모든 3가지 유형의 오류를 감지하고 수정하는 데 중요하다는 것이 밝혀졌습니다.

독립에 대한 공식적인 정의 제안 중 하나는 다음과 같습니다...

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

그리고 이것은 초기 DAG에 적용될 수 있습니다(오류가 없는 것이며 데이터를 정확하게 대표한다고 가정합니다)...

![Image](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_6.png)

DAG로부터 명백하게 알 수 있듯이, 노드 Z1과 Z2(데이터에서의 특성 Z1과 Z2)는 연결되어 있지 않습니다.

Z1의 값이 변경되더라도 Z2의 값은 변경되지 않을 것이며, 그들 사이에는 직간접적인 연결이 없기 때문입니다.

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

이 경우 Z2는 Z1과 독립되었다고 합니다. 이는 다음 식으로 나타낼 수 있습니다 (⫫ 기호는 "더블 업택"으로 불립니다) -

![Expression](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_7.png)

Z2가 Z1과 독립적인 개념은 데이터의 행에서 Z1과 Z2의 사례를 산점도로 나타내고 산점의 관계 값을 나타내는 차트에 선을 추가하여 시각화하고 이해할 수 있습니다...

![Scatter Plot](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_8.png)

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

Z1과 Z2 사이에는 상관 관계가 없음을 쉽게 알 수 있습니다. 계수는 0.03이며(거의 0에 가깝습니다), Z1을 1, 2 또는 4 증가시키더라도 Z2가 증가하지 않음을 알 수 있습니다. 왜냐하면 선의 계수가 거의 평평하기 때문입니다.

뿐만 아니라 산점도의 점들의 패턴은 직선이 아닌 구름 형태이며, 이것들은 모두 Z2가 Z1과 독립적임을 보여줍니다.

Z2가 Z1과 독립적이지만, DAG에서 반대로 상관 관계에 있는 다른 경우도 있습니다...

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_9.png)

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

W가 X에 의존한다는 것은 직관적으로 명백합니다. 이는 X의 값이 변함에 따라 W가 변화한다는 것을 의미합니다. 이를 다음 표현으로 나타낼 수 있습니다(⫫̸ 기호는 "슬래시된 두 번의 업택"이라고 합니다)...

![표 1](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_10.png)

독립변수와 마찬가지로, 의존성 개념을 이해하는 데는 데이터의 특성 간 관계를 시각화하는 산점도가 도움이 될 수 있습니다...

![표 2](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_11.png)

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

이 경우 X와 Y 사이에 관계가 있는 것이 명백하게 보입니다. X 값을 20, 40 또는 60 증가시키면 Y 값도 명확하게 증가하고 산점점을 가장 잘 표현하는 직선의 계수는 6.77입니다. 따라서 X 값을 1 증가시키면 Y 값이 6.77 증가합니다.

독립성 예제와는 달리, 이번에는 산점점들이 구름이 아니라 선을 형성하고 있어 서로에 의한지의 징표입니다.

독립성과 의존성에 대한 자세한 탐구는 본 문서의 범위를 벗어나지만, 관심이 있고 pandas의 DataFrame 객체에 확장 기능을 추가하여 어떤 독립성 표현식을 쉽게 계산할 수 있는 방법을 배우고 싶다면 이 문서를 확인해보세요.

## 누락된 엣지 오류 탐지

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

링크 누락 오류는 원인 인과 문헌과 온라인에서 여러 곳에서 추적할 수 있는 알고리즘을 사용하여 탐지됩니다. 이 알고리즘은 독립성에 기반한 것인데요…

다음에 나오는 DAG를 고려하여 설명할 수 있습니다. 여기에는 오류가 있는데요 — Z3에서 Y로의 링크가 누락되어 있습니다. 데이터가 합성적으로 생성되었기 때문에 데이터에 해당 링크가 존재하는 것으로 알려져 있습니다…

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_12.png)

누락된 링크 유효성 검사 알고리즘은 독립성 테스트를 수행하는 모든 노드를 반복합니다.

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

예를 들어, 알고리즘이 노드 Y에 도달하면 "노드는 부모에 의존할 때 조건부로 이전 노드와 독립적임"이라는 문장은 Y가 부모인 노드 W와 Z2에 의존할 때 Y가 이전 노드 X, Z1 및 Z3과 (Y의 이전 노드) 독립적이라는 것을 의미합니다.

이 문장은 위에서 설명한 독립성 표기를 사용하여 다음과 같은 표현으로 단순화될 수 있습니다...

![image](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_13.png)

한 단계 더 나아가 표현은 완전한 형태로 확장될 수 있는데, 이는 그래프에서 Y가 X, Z1, Z3에 대해 W, Z2가 주어졌을 때 독립이라는 것을 요약하는 문장 Y가 데이터에서 W, Z2가 주어졌을 때 X, Z1, Z3과 독립이라는 것을 나타냅니다...

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

![Table 14](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_14.png)

This condition can then be tested using the `.dependence()` extension method of the DataFrame class that was introduced in the section above ...

![Table 15](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_15.png)

The results show that node Y is independent of nodes X and Z1 but Y is not independent of Z3 because the co-efficient for Z3 in the regression is 3.5012 and the p-value is 0.000.

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

이것은 노드 Y에 대한 독립성 테스트가 DAG에 오류가 있는 것을 보여준 것뿐만 아니라 그 오류가 정확히 어디에 있는지, 즉 데이터에서 Z3와 Y 사이에 의존 관계가 있어 DAG에 추가해야 한다는 것을 보여준 것을 의미합니다.

이제 DAG를 다음과 같이 "고치" 수 있습니다...

![그림](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_16.png)

전문가들이 제안한 DAG에 누락된 링크 오류가 있는 것으로 확인된 DAG는 원치 않는 유효성이 인식되어 Causal Validation을 사용하여 고쳐졌습니다!

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

이렇게 독립성을 사용하여 누락된 링크를 감지하는 방법을 간략히 살펴보았습니다. 자세하고 상세한 설명은 다음 기사를 참조해 주세요...

## 잘못된 가장자리 오류 감지

인과 유효성을 연구해온 여러 달 동안 문헌에서 이상한 링크 오류를 탐구하는 사례나 이 유형의 오류를 해결하기 위한 알고리즘 제안 중 단 하나도 찾지 못했습니다.

사실, 나는 정확히 3종류의 오류가 있다고 주장하는 자료를 찾은 적이 없었기 때문에 가능한 오류 종류 및 잘못된 링크에 대한 제안된 알고리즘에 관한 결론은 제가 직접 조사하고 Python 코드를 사용하여 수많은 시행착오를 거쳐 얻은 결과입니다.

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

스풀러스 링크 알고리즘에 대한 아이디어가 떠올랐습니다. 빠진 링크 알고리즘을 파이썬으로 구현하는 과정에서 모든 오류를 "고치는" 데 충분한지 고민하며 도출되었습니다. 인과 유효성에 대한 통합 이론이 아직 멀리 떨어져 있지만, 스풀러스 링크 알고리즘은 상대적으로 직관적입니다.

빠진 링크의 알고리즘을 수정하여 다음과 같은 명제에 도달했습니다...

"노드는 부모에 의존합니다."

다음은 스풀러스 링크 오류(즉, DAG에 나타나지만 데이터에는 존재하지 않는 링크)가 의도적으로 도입된 예시 DAG입니다...

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

![Causal Validation](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_17.png)

이전 테스트와 마찬가지로, 의심 알고리즘 테스트는 모든 노드를 반복하면서, 이번에는 빠진 링크에 대한 독립성 테스트 대신 의존성 테스트를 진행합니다.

알고리즘이 노드 W에 도달할 때 W가 그래프에서 X와 Z3(부모 노드)에 의존한다는 문장은 W가 데이터에서 X와 Z3에 의존한다는 것을 의미합니다...

![Causal Validation](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_18.png)

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

다시 이 조건은 DataFrame 클래스의 .dependence() 확장 메서드를 사용하여 테스트할 수 있습니다...

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_19.png)

결과에서 노드 W는 노드 X에 의존하지만 Z3에 대해 의존하지 않음을 보여줍니다. 이는 회귀에서 Z3에 대한 계수가 -0.1580(작음)이며 p-값이 0.566(0이 아님)임을 나타냅니다.

이는 노드 W에 대한 의존성 테스트가 DAG에 오류가 있음을 보여주고 Z3로부터 W로의 잘못된 링크가 제거되어야한다는 것을 보여주었음을 의미합니다...

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

![Image 1](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_20.png)

## Detecting Reversed Edge Errors

A solution to detecting reversed link errors eluded me for a long time and initially I thought it was impossible to solve because if the co-efficient between Z3 and X (for example) where 2.5 then the co-efficient between X and Z3 is the inverse …

![Image 2](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_21.png)

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

... 그리고 이 중 어떤 것이 방향성과 인과 관계를 함축하는지 알아내려면 어떻게 해야 할까요?

이용 가능한 인과 관계 문헌과 온라인 기사에는 일부 단서와 징후가 있지만 포괄적이고 완전한 것은 없습니다. 제 연구를 통해 다음과 같은 결론과 해결책이 도출되었습니다...

모든 DAG에는 처리 항목(X)과 결과 항목(Y)이 있으며 적어도 하나의 전방 경로(처리 항목에서 결과 항목까지의 DAG 경로)가 있습니다. 또한 추가적인 노드와 추가적인 링크가 있을 수 있지만, DAG는 비순환적이어야 합니다. 어떤 노드도 고아가 되어서는 안 됩니다.

다양한 경로는 시작, 중간 및 끝 노드로 구성된 낮은 수준 단위인 점들로 분해할 수 있습니다. 이들은 정확히 2개의 링크를 가진 Chain(체인), Fork(포크) 및 Collider(충돌체)와 같이 3가지 유형의 점이 있습니다.

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

이 3가지 타입 중에서 콜라이더는 의존성 테스트를 사용하여 감지할 수 있기 때문에 중요합니다. 한편, 체인과 포크는 정확히 동일한 결과 집합을 생성하므로 둘을 구별할 수 없습니다.

아래 내용으로 요약할 수 있습니다 ...

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_22.png)

... 위 내용은 다음과 같습니다 ...

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

- 충돌체 Y(결과)는 X(치료)와는 독립적입니다.
- 연결 또는 포크의 경우 Y(결과)는 X(치료)에 따라 달라집니다.

대부분의 문헌에서는 여기서 멈추지만, 한 단계 더 나아갈 수 있습니다. 충돌체에서 Y는 X와 독립적이지만 X와 Y 사이에 직접적이거나 간접적인 연결이 없는 경우에만 그렇습니다. 연결이 있으면 X의 “메시지”나 변경 사항이 Y의 변화를 일으켜 다시 종속적으로 만듭니다.

이로 인해 "v-구조"라는 충돌체 하위 집합이 생기는데, 여기서 시작 노드와 끝 노드가 연결되어 있지 않은 단순한 충돌체입니다. 이 예시 DAG에 존재하는 모든 충돌체를 고려함으로써 쉽게 시각화할 수 있습니다.

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

5개의 충돌체가 있지만 두 개는 시작 노드와 끝 노드 사이에 직접적인 연결이 있습니다 (Z3 -`Y `- Z2 및 `- Z1 -` X `- Z2), 이를 "인접"이라고 합니다.

"인접" 충돌체를 제거하면 예시 DAG에서 3개의 v-구조를 시각화할 수 있습니다...

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_24.png)

3개의 v-구조는 다음과 같이 대체적으로 표현될 수 있습니다...

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

- W -`Y`- Z2
- W -`Y`- Z3
- Z1 -`Z3`- Z2

시간을 내어 V-구조를 이해하는 이유는 해당 데이터에서 감지할 수 있으며, 이것을 기반으로한 알고리즘을 구성하여 링크의 방향성을 나타내고 따라서 반전을 탐지할 수 있게 됩니다. 이 알고리즘의 의사 코드는 다음과 같습니다.

- DAG(Directed Acyclic Graph)의 모든 엣지 주위를 반복하며 각 엣지에 대해...
- 현재 엣지를 뒤집어 새로운 DAG를 만듭니다.
- 만약 V-구조가 파괴되고 해당 V-구조가 데이터에 존재하지 않는다면, 현재 DAG가 잘못되었고 엣지를 뒤집어야 합니다.

- 엣지 뒤집기로 파괴된 DAG 내의 각 V-구조마다 이 종속성 테스트를 실행합니다...

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

<img src="/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_25.png" />

- 만약 이 테스트가 통과된다면, 현재 테스트 중인 엣지가 DAG에서 잘못되었고 뒤집어져야 한다는 것을 의미합니다.

- 만약 v-구조가 생성되었거나 데이터에 해당 v-구조가 존재한다면 현재 DAG가 잘못되었고 엣지를 뒤집어야 합니다.

- 엣지를 뒤집음으로써 DAG에서 생성된 각 v-구조에 대해 이 종속성 테스트를 실행합니다...

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

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_26.png)

- 이 테스트가 실패하면 현재 테스트 중인 엣지가 DAG(Directed Acyclic Graph)에서 잘못되었고 반전되어야 함을 나타냅니다.

Python에서 DataFrame .dependency() 확장 메서드를 사용하여 이 알고리즘을 구현하면 반대로 링크된 부분을 감지하고 수정할 수 있습니다.

예를 들어, 엣지/연결을 반전하려고 테스트할 때, 현재 테스트를 선택한 엣지가 Z1 -` Z3인 경우 다음 단계가 수행됩니다...

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

- Z1을 Z3으로 뒤집으면 DAG에서 정확히 하나의 v-structure이 파괴됩니다 (Z1 -`Z3`- Z2)
- 그런 다음 의존성 테스트를 데이터에 대해 다음과 같이 수행할 수 있습니다...

![image](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_27.png)

결과는 Z2가 데이터에서 Z1에 의존하지 않는다는 것을 보여줍니다. 왜냐하면 계수가 매우 작기 때문이고 (-0.0253), p-값이 0이 아닙니다 (0.485).

의존성 테스트는 통과되지 않았습니다 (즉, True를 반환하지 않았습니다). 따라서 Z1 -` Z3가 DAG에서 올바른 방향성을 가지고 있으며 뒤집어서는 안 됨을 추론할 수 있습니다.

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

만약 단계 1-4가 DAG(유향 비순환 그래프)의 각 엣지/링크에 반복적으로 적용된다면 모든 엣지의 방향성 및 전체 DAG의 유효성을 확인할 수 있습니다.

더 자세한 내용을 알고 싶다면, 이 글에는 더 많은 코드 샘플이 포함된 v-구조에 대한 자세한 설명이 있습니다...

## 누락된, 가짜 및 역방향 링크 오류 결합

이전 섹션에서 DAG(유향 비순환 그래프)에는 누락된 엣지, 가짜 엣지 및 역방향 엣지라는 정확히 3가지 유형의 오류가 있음을 보여주었으며, 의존성 테스트가 어떻게 구현되는지를 보여주었으며, 파이썬에서 이 3가지 유형을 모두 탐지하는 방법을 보여주었습니다.

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

이론적으로는 원인 규명을 위한 "모든 것의 통합 이론"을 순차적으로 결합하여 달성할 수 있습니다. 그래서 다음과 같은 통합 원인 규명 알고리즘을 제안하여 시작해 봅시다...

- 누락된 엣지 오류를 검사합니다.
- 잘못된 엣지 오류를 검사합니다.
- 역으로 연결된 엣지 오류를 검사합니다.

직관적으로 이 알고리즘이 작동해야 하지만 실제로 구현되고 견고하게 테스트되었을 때 일관적으로 실패하며 그 이유는 다음과 같습니다.

시작하기 전에 DAG에 오류가 포함되어 있다고 가정해 봅시다 — Z2와 Z3 노드 사이에 누락된 엣지가 있는 경우...

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

![Image](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_28.png)

The first step, which is testing for missing edges, will identify the missing edge. However, in step 3, the directionality of the v-structure at Z1 -`Z3`- Z2 cannot be tested because it does not exist due to the missing edge.

This issue can be easily resolved by updating the unified algorithm as follows:

- Test for any Missing Edge Errors
- Note any errors found and correct them
- Test for Spurious Edge Errors
- Note any errors found and correct them
- Test for and Fix Reversed Edge Errors
- Note any errors found

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

첫 번째 문제가 해결되었습니다. 단계 1은 Z2 - Z3 사이에 누락된 엣지를 복원하고, 그 후 단계 3에서의 반전 사항이 올바르게 테스트될 것입니다. 하지만 더 심각한 문제가 있습니다.

이제 DAG에 다른 오류가 있다고 가정해 봅시다. 이 예제에서는 X와 W 사이의 엣지가 잘못된 방향을 가지고 있고 역전되어 있습니다...

Step 1에서 누락된 링크를 테스트하는 과정은 "노드가 부모에 조건을 붙여 조건이 부모에게 독립적일 때"라는 조건을 실행하며 모든 노드를 반복합니다.

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

만약 DAG가 정확하다면(X와 W 사이의 엣지가 X → W로 변경된다면) 이 조건은 다음과 같이 해결됩니다...

<img src="/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_30.png" />

그러나 이 DAG에 오류가 포함되어 있기 때문에 X가 더 이상 W의 상위 요소가 아닙니다(사실상 W가 이제 X의 상위 요소가 됩니다). 이것은 선행자인 Z1, Z2, Z3와 W 사이의 누락된 링크가 감지되지 않으며 심지어 새로운 부모 및 선행자 집합을 기반으로 반복에서 모든 노드에 대해 실행되는 누락된 링크 테스트 집합이 모두 완전히 잘못될 것입니다.

특정 경우에는 순차 테스트의 순서를 변경하여 역방향 엣지 오류를 식별하고 수정한 후에 누락된 및 잘못된 엣지 테스트를 수행함으로써 만족스러운 결과를 얻을 수 있습니다. 그러나 이는 테스트에서 오류의 사전 지식, 즉 단일 역방향 엣지가 테스트를 생성하는 방법이기 때문에 가능합니다.

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

실제 세계의 인과 추론 문제에서 데이터 팀은 완전히 알지 못할 것입니다...

- DAG에 오류가 있는지 여부 및...
- 오류의 종류(결여, 가짜 또는 반전)를 구별할 수 없을 것입니다.

예를 들어 한 DAG에는 하나의 가짜 엣지 오류가 있을 수 있고 다른 DAG에는 결여 엣지와 반전 엣지가 있는 등 다양한 오류가 있을 수 있습니다.

이는 순차적 테스트의 순서를 변경하여 인과 관계 유효성을 위한 통합 이론을 제시하는 시도를 무효화합니다.

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

다른 해결책을 찾아야 합니다.

# 인과 유효성을 위한 제안된 모든 것의 통합 이론

내 연구에 있어서 나는 인과 유효성을 위한 모든 것의 통합 이론에 대해 포기할 뻔했습니다.

저는 각각의 3가지 유형의 오류를 성공적으로 감지하기 위한 알고리즘을 개발했는데, 이는 해당 알고리즘이 일치하는 유형의 오류만이 있고 다른 오류는 없는 경우에만 사용할 수 있는 신뢰성이 있는 기능을 수행했습니다. 실제로 이것은 쓸모가 없습니다. 왜냐하면 사전에 오류가 무엇인지 알 수 없기 때문입니다.

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

순차 알고리즘의 실패로, 재귀적인 해결책이 동작할 수 있을 것이라 의심하기 시작했습니다. 나는 프로그래밍에서 재귀를 항상 좋아해왔어; 그것은 우아하게 간단하면서도 악랄하게 복잡한데 동시에 한번 복잡성을 이해하고 나면, 수십 줄의 비재귀 코드로 해결되기 어려운 문제들을 몇 줄의 우아한 코드로 해결할 수 있는 특정한 종류가 있습니다.

프로그래밍에서의 재귀는 간단히 말해 자기 자신을 호출하는 함수를 의미합니다. 다른 함수를 호출하는 함수를 작성하는 것은 아주 흔하지만, 함수가 다시 자기 자신을 호출하는 것은 그렇게 흔하지 않죠.

재귀적 해결책은 종종 나무 구조(가계도와 같은)를 탐색, 구문 분석 또는 수정하는 알고리즘에서 사용됩니다. 그것들을 이해하는 가장 좋은 방법은 자신에게 다시 호출하는 것을 새로운 함수를 호출하는 것으로 보는 것입니다.

만약에 그것이 여전히 잘 이해되지 않는다면, 재귀 함수를 디버깅하고 모든 호출을 하나씩 따라가면 이해하고 구현하는 데 도움이 될 것입니다.

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

이것이 인과 유효성을 위한 제안된 모든 것의 통합 이론에 대한 의사 코드입니다...

fix_dag(DAG):

- 만약 DAG가 유효하거나 재귀 호출의 최대 횟수에 도달했을 경우 재귀를 중단하고 DAG를 반환합니다.
- 누락된 엣지가 발견되면 그것을 수정하고 fix_dag(FIXED_DAG)를 호출합니다.
- 잘못된 엣지가 발견되면 그것을 수정하고 fix_dag(FIXED_DAG)를 호출합니다.
- 역으로 뒤집힌 엣지가 발견되면 그것을 수정하고 fix_dag(FIXED_DAG)를 호출합니다.

첫눈에 보기에는 이 순차 알고리즘과 거의 동일해 보이지만 매우 다릅니다.

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

테스트의 순서에 더 이상 의존하지 않습니다. 순환 내에 순서가 있더라도, 재귀 알고리즘은 계속해서 고침을 시도하고 "고친" DAG가 유효한지를 평가할 것입니다. 이렇게 변경이 축적되어 유효한 DAG를 얻게 됩니다(또는 재귀의 최대 횟수에 도달하게 됩니다).

원래 DAG의 오류는 최종으로 고쳐진 DAG와 비교하여 차이점을 식별하는 것으로 유추할 수 있습니다.

## 통합 이론을 위한 Python 코드

백그라운드에서 실행되는 코드의 전부를 보여주는 것은 현실적이지 않습니다. 그러나 아래에 알고리즘을 구현한 fix_dag와 difference 코드가 있습니다...

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

# 통합 이론 테스트

제안된 알고리즘을 파괴하기 전에 고려해야 할 문제가 하나 더 있습니다.

3가지 유형의 오류와 각각의 탐지 알고리즘을 탐구한 이전 기사들은 "힌트"를 제공하는 증거를 제공했습니다.

힌트는 DAG 내의 특정 엣지가 정확하다는 것을 알고리즘에 전달하는 어설션으로, 알고리즘에 해당 엣지를 확인하지 않도록 지시합니다.

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

3가지 유형의 테스트 운영에 있어서 힌트 제공은 중요하다는 것이 밝혀졌습니다. 실제로는 확인 및 테스트를 통해 구분할 수 없는 다양한 DAG(Directed Acyclic Graph)가 존재할 수 있기 때문입니다.

인과 문헌에서는 구분할 수 없는 DAG 집합을 "동등 클래스"로 참조하며 유일한 방어 수단은 동등한 클래스를 검색 공간에서 제거하기 위해 충분한 힌트를 제공하는 것입니다.

힌트의 또 다른 장점은 확인해야 하는 순열을 대폭 줄여준다는 것입니다.

역방향 엣지 알고리즘의 일부인 부분은 유효한 DAG를 식별하기 위해 모든 역방향 엣지 조합을 탐색합니다. 11개의 엣지가 있는 경우 탐색 공간은 2의 11제곱 = 2048이지만 엣지 수를 1개만 늘리면 2의 12제곱 = 4096이 되는 식입니다.

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

파이썬은 해석형 언어이며 모서리의 수가 늘어날수록 성능이 기하급수적으로 저하되므로, 힌팅 엣지를 사용하면 처리 시간을 크게 줄일 수 있습니다.

## 예제 테스트 DAG에서의 힌팅 엣지

DAG에 관련된 몇 가지 원칙들이 힌팅 엣지 집합을 개발하는 데 도움이 됩니다.

먼저, DAG를 순환적으로 만들 수 있는 모든 엣지는 정의상 제외되어야 하며, 순환 DAG를 유발할 가능성이 있는 변경 집합을 테스트하지 않는 것이 최상의 접근 방식입니다.

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

병리와 효과의 성질에서 단서가 있습니다. 일반적으로 경로는 치료에서 효과로 흐르며, 정의상 효과는 사건을 일으키지 않아야 합니다. 따라서 가장자리는 효과 노드로 흐르고 반대 방향으로 흐르면 안 됩니다. 이에 몇 가지 예외 사항이 있지만, 이것은 좋은 팁이며 마찬가지로 인과 관계와 따라서 가장자리는 치료에서 멀어져야 합니다.

더 나아가서 외생 노드를 고려함으로써 추가적인 단서를 얻을 수 있습니다. 우리 예시에서는 Z1과 Z2가 외생노드입니다. 외생 노드는 DAG와 인과 관계 모델의 "입력"이며 다른 모든 노드는 보이지 않는 일련의 구조 방정식으로부터 구축됩니다(예: Z3 = 3 xZ1 + 1.5 x Z2+ ε).

외생 노드와 변수의 식별이 잘못될 수 있지만, 데이터와 도메인 전문가는 DAG와 모델의 입력 변수가 무엇인지 모를 가능성이 매우 낮습니다.

따라서 인과 관계와 가장자리가 외생 변수/노드에서 멀어지는 것이 매우 가능합니다.

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

마지막으로 추적성 개념이 있습니다. 예를 들어 DAG에서 X가 W를 일으키고 W가 Y를 일으킨다고 가정해 봅시다. 만약 X가 약물이라면, W가 혈압이고 Y가 환자 결과라고 한다면, 약물이 직접적으로 환자 결과를 일으키하는 것은 불가능합니다.

약물이 보통 환자들의 어떤 부분을 변경하고, 그 결과로 환자 결과를 개선시킵니다. 도메인 전문가가 Z2 -` Z3 -` X와 같이 어떤 추이적인 인과 관계를 식별했다면, Z2가 X를 직접 일으키는 것은 불가능합니다. Z2, Z3, X가 무엇을 나타내든 간에 그렇습니다.

요약하면, 이러한 특성들은 힌트를 식별하는 데 도움이 되는 단서를 제공할 수 있습니다...

- 비순환성
- 외생성
- 추적성

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

다음은 시도하면서 더욱 발전시킬 수 있습니다...

- 알고리즘 실행하기
- 결과 확인하기
- 도메인 전문가들이 동의하지 않는 알고리즘 오류를 제안했다면, 힌트로 추가하고 1단계로 돌아가기

Acyclity, Exogeneity, Transitivity의 방법을 사용하여 ("예시 DAG는 가짜이므로 도메인 전문가의 도움이 없는 경우") 제안된 "인과적 유효성에 대한 모든 것의 통합 이론" 구현 알고리즘을 위하여 제공할 합리적인 힌트의 세트는 다음과 같이 테스트에 사용되었습니다.

하이라이트가 에지(예: W -` Y)를 가려주면 해당 에지가 올바르다는 것을 암시하며, 공간을 가린다면(예: Z1 -` Z2) 에지가 없는 것이 올바르다는 것을 암시합니다. 부재 중인 에지가 양방향으로 힌트를 줄 수 있음에 유의하여, 일부 에지가 두 번 암시된 것처럼 보일 수 있습니다(예: Z1 -` Z2 및 Z2 -` Z1).

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

![Test Image](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_31.png)

# Testing the Algorithm

The next stage is to robustly and exhaustively test the algorithm for different combinations of the 3 types of errors and to measure performance.

The test harness works as follows:

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

준비

- 오류의 유형과 수를 선택합니다 (예: 누락된 엣지 1개, 잘못된 엣지 0개, 역전된 엣지 1개)
- 이 조합을 포함하는 모든 유효한 DAG의 가능한 조합을 생성합니다 (즉, 정확히 1개의 누락된 엣지와 1개의 역전된 엣지를 포함하는 모든 DAG) 힌트를 제외한)
- 유효한 조합에서 n개의 테스트를 선택합니다 (예: 10개의 테스트)

실행

- 선택한 각 테스트에 대해...
- 테스트/예제 DAG를 시작점으로 사용하여 오류 DAG를 생성하고, 오류를 만들기 위해 변경 사항을 적용합니다 (즉, 1개의 누락된 엣지를 제거하고, 1개의 역전된 엣지를 반전합니다)
- 유효한 DAG에 대한 새로운 데이터 세트를 생성합니다 (오류가 있는 것이 아닌)
- 오류 DAG를 수정하고 생성된 데이터를 통해 오류를 확인하기 위해 재귀적인 fix_dag 함수를 호출합니다
- 오류 DAG를 생성하는 데 사용된 확인된 오류와 비교합니다

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

테이블 태그를 마크다운 형식으로 변경해주세요.

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

아래는 유효한 가짜 엣지 검사를 생성하기 위해 존재하지 않는 3 개의 엣지가 추가된 예시입니다...

![예시 이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_33.png)

마지막으로 역으로 변경된 유효한 엣지 검사를 생성하기 위해 3개의 엣지가 뒤바뀐 예시입니다...

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

<img src="/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_34.png" />

위 예에서 용어 "유효한" 테스트가 일부러 사용되었습니다. DAG를 순환적으로 만들거나 힌트에 포함된 변경 내용을 제외하고 제거, 추가 또는 반전할 수 있는 다른 엣지도 있습니다.

## 테스트 결과

다음은 3가지 유형의 오류 조합에 대한 테스트 결과입니다.

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

## 각 유형의 1개

테스트 첫 번째 배치는 부재, 잘못된 및 역전된 엣지 각각 1개를 시도하는 것입니다. 결과는 다음과 같습니다...

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_35.png)

결과는 단일 부족한 엣지가 있는 DAG(Directed Acyclic Graph)에서 단일 오류가 100%의 정확도로 감지됩니다. 단일 역전된 엣지가 있는 DAG 및 단일 잘못된 엣지가 있는 DAG도 각각 100%와 66.7%의 정확도로 감지됩니다.

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

잘못된 가장자리 결과는 약간 좋지 않아 보일 수 있지만, 단일 가짜 링크에 대한 유효한 테스트 수는 3개 뿐이며 알고리즘이 Z1-W로부터의 가짜 링크를 감지하는 데 어려움을 겪습니다. 다른 방법으로 말하면, 가장자리 탐지에서 단일 실패만 있으며 다른 유형에 대한 실패는 없습니다.

## 다른 두 가지 유형의 오류

2가지 다른 오류에 대한 테스트는 다음과 같이 수행됩니다...

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_36.png)

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

DAG에는 1개의 누락된 엣지 오류와 1개의 잘못된 엣지 오류가 있을 때, 통합 검증 알고리즘은 테스트 케이스의 100%에서 오류를 올바르게 식별합니다. 1개의 누락된 엣지 오류와 1개의 반대 방향 엣지 오류는 테스트의 78%에서 정확하게 식별되며, 1개의 잘못된 엣지 오류와 1개의 반대 방향 오류는 테스트의 78%에서 정확하게 식별됩니다.

이러한 결과들은 인상적입니다. 이는 비재귀적 해결책에 대한 주요 문제인 개별 테스트가 DAG에 포함된 오류에 대한 일부 선험적 지식으로 순서가 지정되어야 한다는 것이 재귀 알고리즘에 의해 완전히 해결되었다는 것을 보여줍니다.

또한 이러한 결과들은 해당 알고리즘이 실제 원인 추론 문제에서 유용할 것이라는 것을 시사합니다. 도메인 전문가들이 실제 세계에서 찾을 수 있는 실제 인과성과 유사한 DAG를 생성할 수 있다는 합리적인 가정을 한다면, 그들이 소수의 실수를 할 수 있다는 것을 의미합니다.

이러한 가정을 고려할 때, 이러한 결과들은 알고리즘이 잘못된 DAG를 수정할 수 있으며, 후속 원인 모델링 단계에서 지시적이고 유용하며 사용 가능한 결과를 얻기 위한 선행 조건임을 보여줍니다.

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

## 각 유형별로 2개씩

각 유형의 오류가 2개씩 포함된 경우 테스트 결과는 다음과 같습니다...

![그림](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_37.png)

테스트 DAG에 각 종류의 오류가 2개씩 있는 경우 정확도가 저하됩니다. 2개의 누락된 엣지는 83.3%의 높은 정확도를 유지하지만, 2개의 가짜 엣지는 33.3%의 정확도로만 탐지되며, 2개의 역방향 엣지의 정확도는 25%입니다.

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

## 각 유형의 1개씩 있는 경우 DAG

다음 단계는 테스트를 더 다양하게 만들고 각각 누락, 가짜 및 역방향 오류 중 하나를 포함하는 DAG 세트를 시도하는 것입니다. 즉, 1개의 누락, 1개의 가짜, 1개의 역방향 = 각 테스트 DAG에 3개의 오류가 있는 것입니다...

![Test Image](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_38.png)

이 테스트에서는, 각 유형의 1개씩 에러가 포함된 DAG가 80%의 케이스에서 정확하게 감지된다는 것을 보여줍니다. 이는 놀라운 테스트 결과입니다!

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

DAG에는 3가지 유형의 오류가 포함될 수 있으며, 이 오류 조합은 80%의 정확도로 올바르게 식별됩니다!

## 동일한 DAG 내의 각 유형 2가지

마지막으로 각 유형의 오류가 2개 포함된 DAG에 대한 테스트 결과는 다음과 같습니다...

![이미지](/assets/img/2024-05-17-CausalValidationAUnifiedTheoryofEverything_39.png)

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

예시 DAG에는 총 6개의 오류가 있습니다. 이 중 2개는 누락, 2개는 잘못된 정보, 그리고 2개는 역전된 정보입니다. 이러한 오류들이 신뢰성 있게 감지되지 않을 수도 있습니다. 그러나 예시 DAG는 단지 8개의 엣지를 가지고 있고, 이 테스트에서 8개 중 6개 또는 75%의 엣지가 잘못되어 있기 때문에 이는 놀라운 일이 아닙니다.

# 결론

"모든 것의 통합 이론" 인과적 유효성 검사 알고리즘은 완벽하지 않으며, 더 복잡하고 다양하며 오류가 많을수록 정확도가 떨어집니다.

그러나 정확도는 충분히 높아서 사용 가능하고 유용합니다. 실제 프로젝트에서는 알고리즘에 의해 식별된 오류가 도메인 전문가들에 의해 검토되고, DAG에 점진적인 변경이 가해지며, 최종 DAG가 합의될 때까지 검증이 반복적으로 실행됩니다.

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

리뷰, 비판 및 수정에 더 많은 노력을 기울이면, 유효성 검사 알고리즘이 실행되기 전에 도메인 전문 지식을 활용하여 DAG를 개선하는 데 더 좋은 결과를 얻을 수 있습니다.

재귀 및 힌트를 통해 제공된 모든 개선을 갖춘 통합 알고리즘조차도 한계가 있지만, 데이터와 현실에서 내재된 인과 관계를 정확하게 포착하는 DAG를 제안하는 핵심 단계에 도움이 되는 가치 있는 도구를 제공하는 정확도는 충분합니다. 이는 인과 추론 머신러닝 모델로부터 유용하고 실용적이며 신뢰할 수 있는 결과를 도출하는 데 중요합니다.

# 추가 연구

다양한 종류의 오류가 100% 신뢰성으로 감지되지 못하는 주요 이유 중 하나는 종속성 테스트의 성능 때문입니다.

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

의존성 테스트는 누락된 링크 테스트를 구현하고 각 변수에 대해 결과를 평가할 수 있어야 하는 Y ⫫ X, Z1, Z3 | W, Z2와 같은 식을 유연하게 평가할 수 있어야 합니다.

이전 글에서 설명한 바와 같이 (위 링크 참조) p 값만으로 회귀 결과를 보는 것은 신뢰할 수 없습니다. 그래서 나의 해결책은 계수와 p 값의 조합을 보는 것이었고, 선택한 임계값은 여러 테스트에서 시행착오를 거쳐 결정했습니다.

최종 결과는 내가 만든 의존성 테스트가 100% 신뢰할 수 없지만 가능한 한 가까울 수 있도록 구현했다는 것입니다. 개선할 수 있는 옵션이 있을 수도 있습니다.

- 성능을 향상시키기 위해 p 값과 계수를 평가하는 다른 방법이 있을까요?
- 성능이 더 좋은 회귀 솔루션에 대해 완전히 다른 접근 방식이 있을까요?
- 인과 관계 검증을 위한 제안된 통일 된 모든 이론을 완전히 다른 것으로 바꿔 성능을 더 향상시킬 수 있을까요?

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

수많은 달 동안 책과 온라인 기사를 찾아보고 여러 다른 소스에서 솔루션을 조합해왔는데, 이제 최상의 성능을 이룰 수 있었어요.

제가 상기한 3가지 옵션 중 하나를 기반으로 개선 아이디어가 있는 분이나, 고려하지 못한 옵션을 사용하여 개선 아이디어가 있는 분 있으면 연락 주세요. 여러분의 소식을 듣고 싶어요.

한편, 이 기사는 총 5편 중 마지막으로, 인과 관계 확인을 위한 모든 것의 통일된 이론을 달성한 결실을 의미하며, 이를 활용하여 실제 기업 환경에서 영향과 결과를 이끌어내기 위해 이론과 다른 기법을 어떻게 활용했는지 설명할 것입니다.

# 연락하고 소통하기...

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

이 기사가 마음에 들었다면 제 팔로우를 눌러주세요. 앞으로도 계속해서 새로운 기사들을 받아보실 수 있습니다.

원하는 주제나 의견이 있다면, 특히 인과 추론 및 새로운 데이터 과학 분야에 관심이 있다면 댓글을 남겨주세요. 제게 연락 주시면 감사하겠습니다.

저의 이전 기사를 확인하려면 제 블로그인 The Data Blog에서 제 연구와 인과 추론에 대한 모든 것을 찾아볼 수 있습니다.
