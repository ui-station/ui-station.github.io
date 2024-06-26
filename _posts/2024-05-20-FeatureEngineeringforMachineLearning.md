---
title: "머신 러닝을 위한 피처 엔지니어링"
description: ""
coverImage: "/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_0.png"
date: 2024-05-20 20:42
ogImage:
  url: /assets/img/2024-05-20-FeatureEngineeringforMachineLearning_0.png
tag: Tech
originalTitle: "Feature Engineering for Machine Learning"
link: "https://medium.com/towards-data-science/feature-engineering-for-machine-learning-eb2e0cff7a30"
---

## 알고리즘이 마법을 발휘할 수 있도록 허용하기

![image](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_0.png)

“\`쓰레기를 넣으면 쓰레기가 나온다\` 라는 속담을 들어보셨나요? 이 속담은 기계 학습 모델을 훈련할 때 실제로 적용되는 내용입니다. 무관한 데이터를 사용하여 기계 학습 모델을 훈련하면 최고의 기계 학습 알고리즘도 큰 도움이 되지 않습니다. 반면, 잘 설계된 의미 있는 특성을 사용하면 단순한 기계 학습 알고리즘조차 우수한 성능을 달성할 수 있습니다. 그렇다면, 어떻게 우리 모델의 성능을 극대화할 의미 있는 특성을 만들 수 있을까요? 그 해답은 기능 엔지니어링에 있습니다. 전통적인 기계 학습 알고리즘(회귀, 결정 트리, 서포트 벡터 머신 등)을 사용할 때 특히 중요한 작업입니다. 그러나 이러한 숫자 입력을 생성하는 것은 데이터 기술뿐만 아니라 창의력과 도메인 지식도 요구하는 프로세스이며 과학의 영역만큼 예술도 요구합니다.

일반적으로, 기능 엔지니어링을 두 가지 구성 요소로 나눌 수 있습니다: 1) 새로운 기능 생성 및 2) 이러한 기능을 처리하여 해당 기계 학습 알고리즘과 최적으로 작동하도록 만드는 과정입니다. 이 글에서는 횡단면, 구조화된, NLP가 아닌 데이터 집합에 대한 기능 엔지니어링의 이 두 가지 구성 요소에 대해 논의하겠습니다.

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

# 새로운 기능 생성

원시 데이터 수집은 지치는 작업일 수 있습니다. 이 작업이 끝날 때쯤에는 추가 기능을 만들기 위해 더 많은 시간과 에너지를 투자하기 힘들 수도 있습니다. 하지만 여기서 모델 훈련에 곧바로 뛰어들지 말아야 합니다. 제 약속드립니다, 그 작업이 노력할 가치가 충분할 것입니다! 이 지점에서 우리는 멈추고 스스로에게 물어봐야 합니다. "내 도메인 지식을 기반으로 수동으로 예측을 한다면, 어떤 기능이 좋은 결과를 도와줬을까요?" 이 질문을 던짐으로써, 우리의 모델이 그렇지 않았을지도 모르는 새로운 의미 있는 기능을 만들어내는 가능성을 열 수 있습니다. 추가로 어떤 기능이 도움을 줄 수 있는지 고려했다면, 아래 기술들을 활용하여 원시 데이터로부터 새로운 기능을 만들어낼 수 있습니다.

## 1. 집계

이 기법은 이름 그대로 여러 데이터 포인트를 결합하여 더 통합된 관점을 만들 수 있게 도와줍니다. 우리는 일반적으로 count, sum, average, minimum, maximum, percentile, standard deviation, variation 계수와 같은 표준 함수를 사용하여 연속적인 수치 데이터에 집계를 적용합니다. 각 함수는 다른 정보 요소들을 포착할 수 있으며, 사용할 최상의 함수는 특정 사용 사례에 따라 다릅니다. 종종, 우리는 그 문제의 맥락상 의미 있는 특정 시간이나 사건 창을 통해 집계를 적용할 수 있습니다.

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

가져온 신용 카드 거래가 사기인지 예측하려고 하는 예시를 살펴봅시다. 이 경우에는 거래별 특징을 사용할 수 있겠지만, 이러한 특징들과 함께 고객 단위의 집계된 특징을 만드는 것이 유익할 수 있습니다:

- 고객이 지난 다섯 년 동안 사기 피해자가 된 횟수: 이전에 여러 차례 사기 피해자가 된 고객은 다시 사기 피해자가 될 가능성이 높을 수 있습니다. 따라서 이러한 집계된 고객 단위의 관점을 사용하면 적절한 예측 신호를 제공할 수 있습니다.
- 마지막 다섯 거래 금액의 중앙값: 신용 카드가 침해당했을 때, 사기꾼들은 카드를 테스트하기 위해 여러 차례 저가 거래를 시도할 수 있습니다. 지금은 단일 저가 거래가 매우 일반적이고 사기의 징후가 될 수 없지만, 짧은 시간 동안 많은 이러한 거래를 보게 된다면, 침해된 신용 카드를 나타낼 수 있습니다. 이러한 경우를 위해, 마지막 몇 거래 금액을 고려하는 집계된 특징을 만들 수 있습니다.

![Feature Engineering for Machine Learning](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_1.png)

## 2. Differences and Ratios

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

많은 유형의 문제에서 집합 패턴의 변경은 예측이나 이상 탐지에 대한 유용한 신호가 될 수 있습니다. 차이와 비율은 숫자 특성의 변화를 나타내는 효과적인 기술입니다. 집계와 마찬가지로 이러한 기술들을 그 문제의 맥락에서 의미 있는 시간 창 위에도 적용할 수 있습니다.

예시:

- 지난 1시간 동안의 새 상인 거래의 백분율과 지난 30일 동안의 새 상인 거래의 백분율 간의 차이: 빠른 연속으로 발생하는 많은 새 상인 거래의 높은 비율은 사기 위험을 나타낼 수 있지만, 이 행동이 고객의 과거 행동과 비교하여 변경된 것을 보면 더 명백한 신호가 됩니다.
- 현재 날짜의 거래 건수를 지난 30일간의 중앙값 일일 거래 건수로 나눈 비율: 신용카드가 침해를 당하면 짧은 시간 동안 많은 거래가 발생할 가능성이 높으며, 이는 과거 신용카드 사용 패턴과 일치하지 않을 수 있습니다. 현재 날짜의 거래 건수를 지난 30일간의 중앙값 일일 거래 건수로 나눈 비율이 상당히 높으면 사기 사용 패턴을 나타낼 수 있습니다.

<img src="/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_2.png" />

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

## 3. 연령 인코딩

날짜 또는 타임스탬프 기능을 숫자로 변환하는데 연령 계산 기술을 사용할 수 있습니다. 두 타임스탬프 또는 날짜 사이의 차이를 이용하여 수치적인 특성을 만들 수 있습니다. 또한, 특정 비숫자적인 특성을 의미있는 수치 특성으로 변환할 수도 있습니다. 특성 값에 연관된 기간이 예측에 유용한 신호가 될 수 있는 경우 이 기술을 사용할 수 있습니다.

예시:

- 최근에 신용카드를 사용한 날로부터 경과한 일수: 오랜 기간 사용되지 않았던 신용카드에서 갑작스러운 거래는 사기 가능성이 높을 수 있습니다. 우리는 신용카드를 마지막으로 사용한 날짜와 현재 거래 날짜 사이의 시간 차이를 이용하여 이 특성을 계산할 수 있습니다.
- 고객이 사용한 기기가 처음 사용된 날로부터 경과한 일수: 새로운 기기로부터 발생한 거래를 볼 때, 해당 거래는 고객이 오랫동안 사용한 기기에서 만든 거래보다 더 위험할 수 있습니다. 우리는 고객이 이 기기를 처음 사용한 날로부터 현재 거래 날짜 사이의 차이를 나타내는 기기 연령의 특성을 만들 수 있습니다.

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

![Feature Engineering for Machine Learning](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_3.png)

## 4. Indicator Encoding

Indicator 또는 Boolean feature는 이진 값 '1, 0' 또는 'True, False'를 가지고 있습니다. Indicator feature는 매우 일반적이며 다양한 유형의 이진 정보를 나타내기 위해 사용됩니다. 경우에 따라 이미 이러한 이진 feature가 숫자 형태로 제공되어 있을 수 있고, 다른 경우에는 숫자가 아닌 값을 갖는 경우도 있습니다. 모델 훈련에 비숫자적인 이진 feature를 사용하려면 이를 숫자 값으로 매핑하면 됩니다.

Indicator feature의 일반적인 발생 및 사용을 넘어서, 우리는 비숫자 데이터 포인트 간의 비교를 나타내는 도구로서 indicator encoding을 활용할 수 있습니다. 이 특성은 비숫자 특성의 변화를 측정하는 방법을 만드는 데 특히 강력합니다.

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

예시:

- 최근 로그인 이벤트 중 검증 실패: 최근 실패한 로그인 이벤트는 사기 거래 위험을 나타낼 수 있습니다. 이 경우, 이 기능에 대한 raw data는 'Yes' 또는 'No' 값을 가질 수 있습니다. 여기서 해야 할 일은 이러한 값을 1 또는 0으로 매핑하는 것뿐입니다.
- 최근 거래 이전의 국가 위치 변경: 국가 위치 변경은 신용카드가 compromise되었을 수 있음을 나타낼 수 있습니다. 이 경우, '국가 위치' 라는 숫자가 아닌 기능에서 국가 변경 정보를 캡처하는 지표 기능을 생성합니다.

![이미지](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_4.png)

## 5. One-Hot Encoding

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

이 기술은 우리의 특성 데이터가 범주형 형식인 경우에 적용할 수 있습니다. 숫자 또는 숫자가 아닌 형식으로서 자료가 되는 자료를 말합니다. 숫자-범주형 형식은 비연속적이거나 측정되지 않는 데이터, 예를 들어 지리적 지역 코드, 상점 ID 등과 같은 데이터를 포함하는 숫자 데이터를 참조합니다. 원핫 인코딩 기술은 이러한 특성을 기곽 학습 모델에 사용할 수 있는 지표 특성 세트로 변환할 수 있습니다. 범주형 특성에 원핫 인코딩을 적용하면 해당 범주형 변수의 모든 범주마다 하나의 새로운 이진 특성을 생성합니다. 새로운 특성의 수가 범주의 수가 증가함에 따라 증가하기 때문에 이 기술은 특히 범주의 수가 적은 특성에 적합합니다. 특히 데이터셋이 작은 경우 이 기술을 적용하라는 표준 기준 중 하나는 범주당 최소 열 개의 레코드가 있을 때 이 기술을 적용하는 것을 제안합니다.

예시:

- 거래 구매 범주: 특정 유형의 구매 범주는 사기 위험이 높을 수 있습니다. 구매 범주 이름은 텍스트 데이터이기 때문에 이 기능을 숫자형 지표 특성 세트로 변환할 수 있습니다. 만약 열 가지 다른 구매 범주 이름이 있다면, 원핫 인코딩을 사용하면 각 구매 범주 이름마다 하나의 새로운 지표 특성이 생상됩니다.
- 기기 유형: 온라인 거래는 iPhone, Android 폰, Windows PC, Mac과 같은 여러 가지 기기를 통해 이루어질 수 있습니다. 이러한 기기 중 일부는 악성 소프트웨어에 민감하거나 사기꾼에게 쉽게 접근 가능하며, 따라서 사기 위험이 높을 수 있습니다. 기기 유형 정보를 숫자 형태로 포함시키기 위해, 기기 유형에 원핫 인코딩을 적용하여 각 기기 유형에 대한 새로운 지표 특성을 생성할 수 있습니다.

![이미지](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_5.png)

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

## 6. Target Encoding

이 기술은 일종의 피처에 적용되며 이를 원-핫 인코딩할 수도 있지만 원-핫 인코딩보다 장단점이 있습니다. 카테고리의 수가 많을 때(고 카디널리티), 원-핫 인코딩을 사용하면 원하지 않게 피처의 수가 많아져 모델 과적합으로 이어질 수 있습니다. 타겟 인코딩은 이런 경우에 효과적인 기술일 수 있으며, 지도 학습 문제에 사용할 수 있습니다. 이 기술은 각 카테고리 값을 해당 카테고리의 타겟값의 예상 값으로 매핑하는 기법입니다. 연속형 타겟이 있는 회귀 문제를 다룰 때, 이 계산은 카테고리를 해당 카테고리의 평균 타겟값으로 매핑합니다. 이진 타겟을 가진 분류 문제의 경우, 타겟 인코딩은 해당 카테고리의 긍정사건 확률에 매핑합니다. 원-핫 인코딩과 달리, 이 기술은 피처의 수를 증가시키지 않는 장점이 있습니다. 이 기술의 단점은 지도 학습 문제에만 적용할 수 있다는 것입니다. 또한 이 기술을 적용하면 특정 카테고리의 관측치 수가 적은 경우에 모델이 과적합되기 쉬울 수 있습니다.

예시:

- 가맹점 이름: 특정 가맹점에 대한 거래가 사기 활동을 나타낼 수 있습니다. 수천 개의 이러한 가맹점이 있을 수 있으며, 각각이 다른 사기 거래 위험을 가질 수 있습니다. 가맹점 이름을 포함한 피처에 원-핫 인코딩을 적용하면 원하지 않게 수천 개의 새로운 피처가 도입될 수 있는데, 이는 바람직하지 않습니다. 이런 경우에 타겟 인코딩을 통해 가맹점의 사기 위험 정보를 캡쳐할 수 있습니다.
- 거래 우편번호: 가맹점과 마찬가지로, 서로 다른 우편번호로 이루어진 거래는 서로 다른 사기 위험 수준을 나타낼 수 있습니다. 우편번호가 숫자 값이지만 연속형 측정 변수는 아니며 그대로 모델에 사용해서는 안됩니다. 대신, 우편번호와 관련된 사기 위험 정보를 포함할 수 있도록 타겟 인코딩과 같은 기술을 적용할 수 있습니다.

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

<img src="/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_6.png" />

새로운 피처들을 raw 데이터로부터 만들었다면, 다음 단계는 최적의 모델 성능을 위해 이를 처리하는 것입니다. 이는 다음 섹션에서 논의될 피처 처리를 통해 수행됩니다.

# 피처 처리

피처 처리는 머신러닝 모델이 의도한 대로 데이터에 적합하게 만들기 위한 일련의 데이터 처리 단계를 의미합니다. 일부 피처 처리 단계는 특정 머신러닝 알고리즘을 사용할 때 필수적이지만, 다른 단계들은 피처와 고려 중인 머신러닝 알고리즘 간에 좋은 작동 화학 반응을 일으키도록 하는 역할을 합니다. 이 섹션에서는 몇 가지 일반적인 피처 처리 단계와 그 필요성에 대해 논의하겠습니다.

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

## 1. 이상값 처리

몇몇 머신러닝 알고리즘, 특히 회귀 모델과 같은 모수적 알고리즘들은 이상값에 심각한 영향을 받을 수 있습니다. 이러한 머신러닝 알고리즘들은 이상값을 수용하려고 하다보니 모델 파라미터에 심각한 영향을 미치고 전반적인 성능을 저하시킵니다. 이상값을 처리하기 위해서 먼저 이들을 식별해야 합니다. 특정 기능의 이상값을 식별하기 위해 평균에 세 배의 표준 편차를 더한 절댓값이나 가장 가까운 박스(사분위 값과 상위에서 1.5배의 사분위 범위 값) 값을 초과하는 규칙을 적용함으로써 이상값을 감지할 수 있습니다. 특정 기능에서 이상값을 식별하면 아래 기술 중 하나를 사용하여 처리할 수 있습니다:

- 삭제: 하나 이상의 이상값이 있는 관측치를 삭제할 수 있습니다. 그러나 데이터에 다양한 기능에서 너무 많은 이상값이 포함되어 있다면 많은 관측치를 잃을 수 있습니다.
- 대체: 주어진 기능의 평균, 중앙값 및 최빈값과 같은 평균값으로 이상값을 대체할 수 있습니다.
- 기능 변환 또는 표준화: 로그 변환 또는 기능 표준화(척도 설명에 설명되어 있음)를 사용하여 이상값의 크기를 줄일 수 있습니다.
- 상한과 하한 설정: 일정 값 이상의 이상값을 해당 값으로 대체할 수 있으며, 예를 들어 99번째 백분위의 모든 값보다 큰 값을 99번째 백분위 값으로 대체하고 1번째 백분위의 모든 값보다 작은 값을 1번째 백분위 값으로 대체할 수 있습니다.

![image](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_7.png)

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

![2024-05-20-FeatureEngineeringforMachineLearning_8](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_8.png)

다변량 이상치 (여러 특성에 대한 이상치)를 감지하는 기술이 있지만, 이는 보통 복잡하고 머신 러닝 모델 훈련 측면에서 크게 가치를 더하지 않습니다. 또한 서포트 벡터 머신 및 의사 결정 트리, 랜덤 포레스트, XGBoost와 같은 트리 기반 알고리즘과 같은 대부분의 비모수형 머신 러닝 모델과 함께 작업할 때 이상치는 걱정거리가 되지 않습니다.

## 2. 결측값 처리

실제 데이터셋에서 결측 데이터는 매우 흔합니다. XGBoost와 같은 몇 가지 제외하고 대부분의 전통적인 머신 러닝 알고리즘은 훈련 데이터셋에서 결측값을 허용하지 않습니다. 그러므로 결측값을 해결하는 것은 머신 러닝 모델링에서의 루틴 작업 중 하나입니다. 결측값을 처리하는 여러 기술이 있지만, 어떤 기술을 실행하기 전에 결측 데이터의 원인을 이해하거나 적어도 데이터가 무작위로 누락되었는지를 알아야 합니다. 데이터가 무작위로 누락되지 않은 경우, 특정 부분집단이 결측 데이터를 더 자주 가지고 있는 경우가 많아 그러한 값에 대한 대치가 어려울 수 있습니다. 데이터가 무작위로 누락된 경우, 아래에서 설명한 몇 가지 일반적인 처리 기술을 사용할 수 있습니다. 이들은 각각 장단점을 가지고 있으며, 어떤 방법이 사용 사례에 가장 적합한지 결정은 우리에게 달려 있습니다.

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

- 삭제: 최소한 하나의 결측값이 있는 관측치를 삭제할 수 있습니다. 그러나 다양한 특성의 결측값이 많을 경우, 많은 관측치를 잃을 수도 있습니다.
- 삭제: 특정 특성에 결측값이 많은 경우 해당 특성을 삭제할 수 있습니다.
- 평균값으로 대체: 평균, 중앙값, 최빈값과 같은 평균값을 사용하여 결측값을 대체할 수 있습니다. 이 방법은 간단하지만 모든 유형의 관측치에 대해 좋은 추정을 제공하지 않을 수 있습니다. 예를 들어, 고 위험 사기 거래의 경우 낮은 위험 사기 거래의 평균 거래 금액과 다를 수 있으며, 높은 위험 사기 거래 금액의 평균값을 결측값으로 사용하는 것이 적절하지 않을 수 있습니다.
- 최대우도, 다중 대체, K 최근접 이웃: 다른 특성과의 관계를 고려하는 복잡한 방법으로 전체 평균보다 더 정확한 추정을 제공할 수 있습니다. 그러나 이러한 방법을 구현하기 위해서는 추가적인 모델링이나 알고리즘 구현이 필요합니다.

![이미지](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_9.png)

## 3. 스케일링

머신러닝 모델에서 사용하는 특성들은 종종 서로 다른 범위를 가집니다. 스케일링 없이 사용하면 절대값이 큰 특성이 예측 결과를 지배할 수 있습니다. 그 대신, 각 특성이 예측 결과에 공평하게 기여할 수 있도록 하기 위해 모든 특성을 동일한 척도로 조정해야 합니다. 가장 일반적인 스케일링 기술은 다음과 같습니다:

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

- 정규화: 이 스케일링 기술은 특징 값들을 0과 1 사이로 제한합니다. 정규화를 적용하기 위해 우리는 특징의 최소값을 뺀 다음 그 특징의 범위(최솟값과 최댓값의 차이)로 나눕니다. 정규화는 몇 가지 특징이 급격하게 치우친 경우나 몇 개의 극단값을 가지고 있는 경우에는 좋은 기술이 아닐 수 있습니다.
- 표준화: 이 기술은 특징 데이터 분포를 표준 정규 분포로 변환합니다. 이 기술을 적용하는 방법은 평균을 빼고 표준 편차로 나누는 것입니다. 이 기술은 특징이 급격한 치우침이나 몇 개의 극단값을 가진 경우에 일반적으로 선호됩니다.

결정 트리, 랜덤 포레스트, XGBoost 등과 같은 트리 기반 알고리즘은 조정되지 않은 데이터로 작업할 수 있으며 이러한 알고리즘을 사용할 때 스케일링이 필요하지 않습니다.

![이미지](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_10.png)

![이미지](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_11.png)

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

## 4. 차원 축소

오늘날, 우리는 거대한 데이터를 보유하고 있으며 모델을 학습시키기 위해 방대한 특성들의 모음을 구축할 수 있습니다. 대부분의 알고리즘에 대해, 더 많은 특성을 가지는 것이 모델 성능을 향상시킬 수 있는 더 많은 옵션을 제공하기 때문에 좋습니다. 그러나 이것이 모든 알고리즘에 대해 참이라는 것은 아닙니다. 거리 측정에 기반한 알고리즘들은 차원의 저주에 영향을 받습니다 - 특성의 수가 상당히 증가하면 두 개의 관측치 사이의 거리 값이 무의미해집니다. 따라서 거리 측정에 의존하는 알고리즘을 사용할 때는 많은 수의 특성을 사용하지 않도록 주의해야 합니다. 만약 데이터셋이 많은 특성을 가지고 있고 어떤 특성을 유지해야 할지 알 수 없다면 주성분 분석(PCA)과 같은 기법을 사용할 수 있습니다. PCA는 기존 특성 집합을 새로운 특성 집합으로 변환합니다. 고유값이 가장 높은 새로운 특성이 기존 특성에서 대부분의 정보를 캡처하도록 새로운 특성을 생성합니다. 그러면 상위 몇 개의 새로운 특성만 유지하고 나머지를 버릴 수 있습니다.

지도 학습 문제에서 특성의 수를 줄이기 위해 연관 분석과 특성 선택 알고리즘과 같은 다른 통계 기법을 사용할 수 있습니다. 그러나 이러한 방법들은 일반적으로 PCA와 동일한 수의 특성으로 동일한 수준의 정보를 포착하지는 못합니다.

![Image](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_12.png)

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

## 5. 정규 분포로 변환하기

이번 단계는 특이한 경우입니다. 이는 대상에만 적용되며 피처에는 적용되지 않습니다. 또한, 대부분의 머신러닝 알고리즘은 대상의 분포에 제약이 없지만, 선형 회귀와 같은 특정 알고리즘은 대상이 정규 분포를 가져야 합니다. 선형 회귀는 오류 값이 대칭이고 모든 데이터 포인트 주변에 집중되어 있다고 가정하며(마치 정규 분포의 형태처럼), 정규 분포로 분포된 대상 변수는 이 가정이 충족됨을 보장합니다. 대상의 분포를 이해하기 위해 히스토그램을 그려볼 수 있습니다. 샤피로-윌크 검정과 같은 통계 검정은 이 가설을 테스트하여 정규성을 알려줍니다. 대상이 정규 분포가 아닌 경우, 대상 분포를 정규화시키는데 어떤 변환을 시도할 수 있습니다. 로그 변환, 제곱 변환, 제곱근 변환 등 여러 변환을 해보고 대상 분포를 정규화하는데 가장 적합한 변환을 선택할 수 있습니다. 또한 박스-콕스 변환을 사용하여 여러 매개변수 값을 시도해볼 수 있으며, 대상 분포를 정규화시키는데 가장 적합한 값을 선택할 수 있습니다.

![이미지](/assets/img/2024-05-20-FeatureEngineeringforMachineLearning_13.png)

참고: 피처 전처리 단계를 모든 순서로 구현할 수 있지만, 그 적용 순서를 신중히 고려해야 합니다. 예를 들어, 평균값 대체를 사용한 누락 값 처리는 아웃라이어 탐지 전이나 후에 구현할 수 있습니다. 그러나 대체에 사용하는 평균값은 누락된 값을 아웃라이어 처리 전이나 후에 다르게 처리할 수 있습니다. 이 기사에서 제시된 피처 처리 순서는 성공적인 후속 처리 단계에 미치는 영향의 순서대로 문제를 해결합니다. 따라서 이 순서를 따르면 대부분의 문제를 해결하는데 효과적일 것입니다.

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

# 결론

소개에서 언급한 대로, 특성 엔지니어링은 모델의 성능을 탁월하게 제어할 수 있게 해주는 기계 학습의 한 측면입니다. 특성 엔지니어링을 최대한 활용하기 위해, 이 기사에서 우리는 새로운 특성을 만들고 기계 학습 모델과 최적으로 작동하도록 이를 처리하는 다양한 기술을 배웠습니다. 이 기사에서 선택한 특성 엔지니어링 원칙과 기술이 무엇이든 사용하더라도 중요한 메시지는 기계 학습이 패턴을 파악하도록 알고리즘에 요청하는 것이 아니다. 그것은 우리가 알고리즘이 필요로 하는 데이터 유형을 제공하여 효과적으로 작동하도록 하는 것입니다.

이미지는 별도로 언급되지 않는 한 모두 저자에 의해 제작되었습니다.
