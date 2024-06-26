---
title: "파이썬 패키지 Yellowbrick와 PiML으로 소중한 데이터와 모델 통찰력 발견하기 코드 포함"
description: ""
coverImage: "/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_0.png"
date: 2024-05-20 18:29
ogImage:
  url: /assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_0.png
tag: Tech
originalTitle: "Unlocking Valuable Data and Model Insights with Python Packages Yellowbrick and PiML (with Code)"
link: "https://medium.com/towards-data-science/unlocking-valuable-data-and-model-insights-with-python-packages-yellowbrick-and-piml-with-code-945d5a39da9c"
---

<img src="/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_0.png" />

안녕하세요! 이 기사에서는 Yellowbrick와 PiML의 Python 패키지가 데이터 전문가가 데이터와 모델을 더 잘 이해할 수 있도록 도와주는 방법을 탐구할 것입니다. 다양한 데이터 및 모델 품질 문제에 대처하고 있습니다. LLM들의 시대에 여전히 중요한 이유들은 다음과 같습니다: (a) 데이터와 모델에 대한 강력한 시각적 통찰을 제공한다, (b) 모델 성능 분석 영역에서 교육 도구로 사용될 수 있다, (c) 비용과 자원 측면에서 효율적이며, (d) 데이터 프라이버시를 제공한다. 모든 교육 및 시각화는 데이터를 클라우드 기반 서버로 이전하지 않고도 내부에서 이뤄질 수 있습니다.

다음은 토론할 주제들입니다:

- 분류 작업에 충분한 훈련 데이터 크기인지
- 특정 매개변수 튜닝이 분류 성능에 어떻게 영향을 주는지 시각화
- 군집 알고리즘 성능을 클러스터 중첩 및 클러스터 거리 측면에서 시각화. 클래스 간 특징 분포 시각화.
- 다섯 가지 다른 측정 방법을 사용한 모델 성능 비교
- 전역 및 지역 해석가능성
- 모델의 약한 조각 식별
- 모델 신뢰성 분석
- 모델 견고성 분석
- 모델 분할 진단 분석

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

# A. 옐로브릭

## A.1 분류

옐로브릭의 기능을 탐색하기 위해 잘 알려진 공개 데이터 세트 와인을 사용할 것입니다. 이 데이터 세트는 178개의 행과 13개의 열로 구성되어 있습니다. 특징은 '색상 강도', '알콜', '말산 산' 등의 다양한 와인 특성입니다. 목표 변수는 0, 1, 2로 표시된 3가지 와인 클래스로 구성되어 있습니다. 먼저 와인 데이터 세트를 사용하여 분류를 수행할 것입니다. RandomForestClassifier를 사용하여 다음과 같은 결과를 얻을 것입니다.

분류 정확도가 0.97인 것을 볼 수 있습니다. 이는 높은 수준의 정확도를 나타냅니다.

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

A.1.1 학습 데이터 양 충분성

Yellowbrick를 사용하여 모델의 학습 곡선을 플로팅하여 학습 데이터 양 충분성 문제를 해결할 것입니다. 이 곡선은 그림 1에 나타나 있습니다. 학습 곡선은 모델이 학습 데이터 양을 증가시킬 때 모델의 성능이 어떻게 변하는지 시각화합니다. 학습 곡선을 통해 우리는 다음을 이해할 수 있습니다:

- 더 많은 데이터를 추가하면 모델의 일반화 능력이 향상될 수 있는지 여부.
- 과적합/과소적합이 있는지 여부. 이는 학습 및 검증 점수가 수렴하는 방식에 따라 달라집니다.

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

학습曲선은 다음을 보여줍니다:

- 훈련 점수는 모든 훈련 인스턴스에 대해 1.0에 가까운 높은 값을 유지합니다. 이는 RandomForest 모델이 훈련 데이터로부터 효과적으로 학습하는 것을 시사합니다.
- Figure 1의 교차 검증 점수 라인을 살펴보면, 교차 검증 점수가 일정하지 않고 훈련 점수보다 낮게 시작하는 것을 볼 수 있습니다. 훈련 인스턴스 수가 증가함에 따라 일부 변동성을 보이다가 결국 훈련 점수보다 아래 수준에 안정화됩니다. 훈련과 교차 검증 사이에 뚜렷한 차이가 있으며, 이는 과적합의 지표입니다. 일반화를 향상시키기 위해 과적합을 줄이는 정규화와 같은 개선이 필요합니다.
- 더 많은 데이터가 필요한가요? 아마도 그렇지 않습니다. 교차 검증 점수는 안정화되어 특정 훈련 인스턴스 수 이상(약 70~80)으로 진전이 없어 보입니다. 이는 더 많은 데이터를 추가해도 새로운, 보이지 않는 데이터에 대한 모델 성능이 크게 향상되지 않을 수도 있다는 것을 시사합니다.

A.1.2. 특정 매개변수 조정이 분류 성능에 미치는 영향

아래 코드 스니펫에서 RandomForest 분류기의 최대 트리 깊이는 값 1부터 11까지 변화합니다. 그런 다음 'max_depth' (최대 트리 깊이)가 변하는 것을 추적할 수 있도록 모델 성능 변화를 보여주는 검증 곡선 시각화기가 생성됩니다.

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

![Unlocking Valuable Data and Model Insights with Python Packages Yellowbrick and PiML with Code](/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_2.png)

그림 2는 검증 곡선을 보여줍니다. 일반적으로 검증 곡선은 기계 학습에서 모델이 어떻게 수많은 하이퍼파라미터 값 범위에서 수행되는지를 평가하는 데 사용됩니다. 이 곡선은 하이퍼파라미터의 최적값을 찾는 데 도움을 주며, 그 곳은 검증 점수가 최대화되고, 훈련 및 검증 점수의 갭이 합리적인 곳입니다. 이는 우리 모델이 잘 일반화될 수 있다는 것을 의미합니다.

교차 검증 점수가 'max_depth'=7에서 최대치를 보입니다. 그림 2의 이 관찰은 'max_depth'가 7로 설정할 때, 모델이 데이터에서 충분한 복잡성을 캡처하고 새로운 데이터로 일반화하기 위한 최상의 균형을 제공한다는 것을 시사합니다. 다시 말해, 이는 모델이 의미 있는 패턴을 학습하면서 너무 많은 잡음에 과적합 되지 않는 깊이입니다. 이러한 매개변수 설정으로 인해 우리는 과적합을 피하면서도 정확한 예측을 수행할만큼 충분한 세부 정보를 캡처할 수 있습니다. 매개변수 값을 7 이상으로 증가시키면 과적합으로 이어질 수 있으며, 이는 검증 점수의 플래토 또는 약간의 감소로 나타납니다.

## A.2 클러스터링

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

다음으로 와인 데이터에 K-Means 클러스터링 방법을 적용하고 알고리즘 성능을 시각적으로 확인하기 위해 Yellowbrick을 활용할 것입니다. 그러나 그 전에, 볼 수 있는 게 드물지만 유용한 Yellowbrick 그래프를 사용할 것입니다. 이는 와인의 특성 분포에 관한 것입니다(도표 3). 세 가지 클래스에 대한 대부분의 특성이 서로 겹치는 것을 볼 수 있지만, '알콜'과 '프로린' 등 일부 주목할 만한 차이점이 있습니다. Shapley 분석에서도 이 두 가지 특성이 중요한지 확인하는 것이 흥미로울 것입니다.

<img src="/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_3.png" />

실제로, 아래의 도표 4에서 '알콜'과 '프로린'이 가장 중요한 세 가지 특성 중에 속합니다.

<img src="/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_4.png" />

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

아래는 클러스터링 알고리즘의 결과입니다. 아래의 내용을 보면, 클래스 1과 2에서 4개의 샘플이 잘못 분류되었지만, 전반적으로 결과는 좋습니다.

Yellowbrick의 도움을 받아 PCA 도메인에서 클러스터를 시각화할 것입니다. PCA 도메인에서 왜 하는지 궁금할 수 있습니다. PCA의 맥락에서 클러스터는 첫 번째 몇 개의 주요 구성 요소에 의해 정의된 공간에서 데이터 지점으로 표시됩니다. PCA는 데이터를 직교 방향으로 프로젝션하므로 원래 공간에서 겹치는 특징을 종종 분리할 수 있습니다. 와인 데이터에 대한 우리의 클러스터는 그림 5에 표시되어 있으며, 잘 분리되어 보입니다.

마지막으로, Yellowbrick의 다차원 스케일링(MDS)을 사용하여 클러스터 간 거리를 시각화할 것입니다. MDS는 데이터 세트의 개별 데이터 지점의 유사성을 시각화하는 데 중점을 둡니다. 유사성 또는 상이성을 보존하므로 클러스터 간 거리를 시각화하는 데 적합한 도구입니다. 그림 6에서 세 클러스터 간에 큰 거리가 있음을 볼 수 있으며, PCA 도메인에서 명확한 클래스 분리와 일치합니다. 왼쪽 하단의 파선 원은 각 클래스에 속한 샘플 수를 나타냅니다(34: 클래스 0, 44: 클래스 1, 41: 클래스 2).

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

<img src="/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_6.png" />

# B. PiML

## B.1 데이터 및 준비 단계

Yellowbrick가 아름다운 시각화로 사용자를 기쁘게 하듯이, PiML은 EDA부터 모델 강건성 및 신뢰성 분석, 그리고 약한 조각 식별까지 다양한 기능으로 인상을 주는 것입니다. 그러나 PiML의 기능을 논의하기 전에, 분석에 사용할 데이터 세트에 대해 알아보겠습니다. UCI Machine Learning Laboratory의 Adult 데이터 세트 또는 Census Income 데이터 세트로 알려진 이 데이터는 수입을 분류하고 50K를 초과하는지 예측하는 데 사용될 수 있습니다. 이 데이터 세트에는 48842개의 인스턴스와 14가지 피처 및 대상 수입 변수가 포함되어 있습니다. 그 피처들의 일부를 아래에서 확인할 수 있습니다.

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

![image](/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_7.png)

많은 변수가 다양한 값을 갖는 범주형이기 때문에 일부 의미론적으로 유사한 값들을 병합하기 위해 feature engineering이 수행되었습니다. 우리는 [1]에서 설명된 feature engineering 예제를 따랐습니다. 'education' feature의 예시는 아래에 표시되어 있습니다.

PiML은 사용자로부터 매우 적은 프로그래밍을 필요로 합니다. 모든 기능은 'Experiment' 객체를 통해 사용할 수 있습니다.

한 줄로 된 코드 아래에서 PiML에서 EDA (탐색적 데이터 분석)를 하는 방법을 보여줍니다. 그리고 이것이 모든 PiML 기능에 접근하는 방법입니다: 한 줄로 된 명령어로.

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

아래 그림 7에서 나타나는 것처럼, PiML은 일변량 및 이변량 특성 분석을 수행하고 히트맵을 표시할 수 있습니다.

![Figure 7](/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_8.png)

- 어떤 분석을 수행하기 전에 대상 변수와 수행할 분석 유형(회귀 또는 분류)을 지정해야 합니다. 이 작업은 data_prepare() 모듈을 통해 수행됩니다.

그림 8은 데이터 준비 모듈 화면을 보여줍니다.

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

<img src="/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_9.png" />

- 중요한 모델 훈련 단계입니다. 다양한 모델 중에서 선택할 수 있습니다. 아래 그림 9에서 볼 수 있듯이, 우리는 XGB1, XGB2, EBM(설명 가능한 부스팅 머신), 그리고 ReLU-DNN을 선택했습니다.

<img src="/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_10.png" />

## B.2 모델 분석

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

B.2.1 다섯 가지 숫자 측정치를 사용한 예측 성능 평가

Figure 10은 선정된 모델에 대한 리더보드를 보여주며, 테스트 및 훈련 데이터셋에 대한 다섯 가지 모델 성능 지표를 보여줍니다. 이러한 지표는 (a) 정확도, (b) AUC-ROC: ROC 곡선 아래 영역, (c ) F1 점수: 정밀도와 재현율의 결합, (d) 로그 손실: 잘못된 예측에 대해 벌점을 부과하고 예측의 불확실성을 고려합니다, (e) Brier 점수: 확률 예보의 정확성을 측정하는 데 사용됩니다. 예측 신뢰도를 평가해야 할 때 특히 유용합니다. 값 범위에 대한 내용: 정확도, AUC-ROC 및 F1 점수의 경우 높은 값이 더 나은 모델 성능을 나타내고, 로그 손실 및 Brier 점수의 경우 낮은 값이 더 나은 모델 성능을 나타냅니다.

따라서 리더보드는 EBM 알고리즘이 모든 다른 모델보다 모든 지표에서 테스트 및 훈련 세트에서 뛰어남을 보여줍니다.

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

B.2.2 전역 및 지역 설명

전역 설명에 관해, PiML은 다양한 측정치를 제공합니다: (a) 순열 피쳐 중요도, (b) 단변량 및 이변량 부분 의존도 플롯, 그리고 (c ) 누적 지역 효과. 피규어 11은 순열 피쳐 중요도 결과를 보여줍니다. '자본이익'이 가장 중요한 측정치라는 것을 알 수 있습니다. 직관적으로 이해하기 쉬운데, 과제가 50K 이상을 벌어들이는지 예측하고 싶기 때문입니다.

<img src="/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_12.png" />

지역 설명에 대해, PiML은 LIME과 SHAP을 제공합니다. 피규어 12는 LIME 결과를 보여줍니다. 여기서 '자본이익'이 상당히 긍정적인 가중치를 가지고 해당 긍정적인 효과를 갖는 것을 관찰합니다. 이는 자본이익 증가가 모델의 출력값을 긍정적으로 이동시키는 것과 강하게 관련이 있다는 것을 의미합니다. '나이' 또한 긍정적인 가중치와 효과를 가지고 있어, 나이가 높아질수록 모델 결과에 긍정적으로 기여한다는 것을 의미합니다.

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

![image](/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_13.png)

### B.2.3 Underperforming Slices Identification

In PiML, this analysis is referred to as 'weakspot' analysis. Underperformance can result from various factors, such as data issues (bias or inadequacy) or model problems (lack of complexity, overfitting, etc.). To conduct this analysis, we utilize the model_diagnose function, which will also be used for other types of analyses in the following sections, like robustness or reliability. The code snippet below demonstrates its application for 'weakspot' analysis.

Let's delve into the different parameters. When calling the model_diagnose function, the following parameters are passed:

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

- 'model': 우리가 평가하고 싶은 모델입니다.
- 'show': 우리가 하고 싶은 작업을 나타냅니다.
- 'metric': 성능 메트릭입니다. 이는 위에서 논의한 다섯 가지 숫자 메트릭 중 하나일 수 있습니다.
- 'slice_method': 데이터를 슬라이스하는 방법입니다. 히스토그램 또는 트리 중 하나일 수 있습니다.
- 'slice_features': 슬라이스하는 데 사용할 1개 또는 2개의 특징입니다. 'marital_status' 특징을 사용할 것입니다.
- 'threshold': 우리가 약한 지역과 좋은 지역을 구분하기 위해 사용할 성능 메트릭 '임계 비율'입니다. 우리는 기본 값 1.1을 사용할 것이며, 이는 모델 정확도 메트릭에서 10% 성능 하락에 해당합니다.
- 'min_samples': 약한 지역으로 간주되기 위한 최소 샘플 크기를 지정합니다. 우리는 기본 값 20을 사용할 것입니다.
- 'use_test': 이 매개변수는 훈련 데이터 또는 테스트 데이터를 사용할지를 지정합니다. 우리는 기본 값 False를 사용할 것이며, 이는 훈련 데이터를 사용할 것을 나타냅니다.

약점 분석은 아래 그림 13에 나와 있으며, 두 개의 플롯으로 구성되어 있습니다.

![그림 13](/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_14.png)

상단 플롯은 'marital-status'의 함수로서 모델의 정확도를 나타내며, 버킷으로 분할됩니다. 단계별 함수는 'marital-status' 기능의 서로 다른 범위에서 모델의 정확도가 크게 변한다는 것을 나타냅니다. 빨간 점선은 원하는 임계 정확도를 나타냅니다. 빨간 '0' 마커는 모델의 정확도가 원하는 임계 미만인 특정 간격을 강조합니다.

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

하단 플롯은 데이터셋의 특징 값 히스토그램을 보여줍니다. 색상(“임계값 이상”은 파란색, “임계값 미만”은 연한 파란색)은 각 ‘marital-status’ 범주에서의 샘플이 성적 임계값을 초과하거나 미만한 횟수를 나타냅니다.

이러한 진단용 플롯은 데이터의 특정 하위 그룹에서 모델 동작을 이해하는 데 중요합니다. 마찬가지로, 오버피팅 영역을 분석하기 위해 show 매개변수를 overfit으로 설정할 수 있습니다.

히스토그램 X축에 표시된 정수 값과 데이터 레이블 간의 매핑 방법에 대해 여기에 노트를 추가하고 싶습니다. 여기에 코드가 있습니다:

그리고 결과는 다음과 같습니다: '0: ‘Married’, 1: ‘NotMarried’, 2: ‘Separated’, 3: ‘Widowed’'

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

B.2.4 모델 신뢰성 분석

이 유형의 분석은 불확실성을 계량화하기 위해 사용되는 일치 예측 프레임워크에 기반을 두고 있습니다 [2]. 여기서 모델 신뢰성이 모델 견고성과 어떻게 다른지에 주목하는 것이 중요합니다. 이것은 다음 섹션에서 논의될 것입니다. 신뢰성은 모델이 다른 일반 운영 모드에서 전반적으로 일관성 있는지에 중점을 두며, 견고성은 모델이 입력의 변화에 대처하는 능력에 중점을 둡니다. 특히 금융 부문과 같은 동적 환경에서 신뢰성 분석은 중요합니다.

아래 코드 스니펫에서 'show' 매개변수의 값이 이제 'reliability_distance'이고, 'distance_metric'이라는 새 매개변수를 PSI 값으로 정의합니다. 이 코드는 Figure 14를 생성합니다.

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

위 그래프는 모델의 특성이 'Unreliable vs. Remaining Regions'간에 어떻게 분포가 변화하는지 보여주기 위해 인구 안정성 지수(PSI)를 사용하고 있습니다. PSI는 변수의 분포가 시간적으로나 다른 데이터셋 간에 얼마나 많이 변했는지를 측정하는 데 사용되는 지표입니다. 더 높은 PSI 값은 더 큰 변화를 나타내며, 이는 모델의 안정성에 문제가 될 수 있습니다.

우리의 경우, 하나의 데이터셋을 다루고 있으며 높은 PSI 값은 해당 특성의 분포에 중요한 차이가 있다는 것을 나타냅니다. 이러한 세분화된 세그먼트는 지리적 지역, 연령 그룹 또는 다른 인구통계 요인에 의해 범주화될 수 있습니다. Figure 14에서 'relationship', 'marital-status', 'occupation' 특성은 상대적으로 높은 PSI 값이 나타납니다. 이는 데이터 하위 세그먼트에서 중요한 분포 변화가 있음을 나타냅니다. 반면에 'race', 'education', 'capital-loss'는 PSI 값이 낮아 해당 특성의 분포가 다른 데이터 세그먼트 간에 더 안정적임을 시사합니다.

### 모델 견고성 분석

모델의 견고성은 ML 모델이 다양한 시나리오에서 특히 새로운 데이터를 처리할 때 성능을 유지하는 능력을 말합니다. 모델 견고성의 측면에는 (a) 새로운 데이터에서 잘 동작하는 일반화 능력, (b) 입력값의 작은 변화에 크게 영향을 받지 않는 안정성, (c) 입력 데이터의 오류에 영향을 받지 않는 노이즈 허용성이 포함됩니다.

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

도형 15에 나타난 그래프는 EBM 모델의 모든 피처에 대한 교란(즉, 의도적인 변화)이 어떤 영향을 미치는지를 보여줍니다. 아래는 해당 그래프를 생성한 코드입니다. 'perturb_size'는 교란의 단계 크기를 나타내며, 'perturb_method'는 'quantile'로 설정되어 있습니다. 이 매개변수에는 'raw'와 'quantile' 두 가지 선택지가 있습니다. 'raw' 값은 피처에 가우시안 노이즈가 추가됨을 의미하며, 많은 피처가 이산형임을 감안할 때 'quantile' 값이 더 나은 선택입니다. 이 값은 사분위 범위 내의 교란을 의미합니다.

![그래프](/assets/img/2024-05-20-UnlockingValuableDataandModelInsightswithPythonPackagesYellowbrickandPiMLwithCode_16.png)

그래프의 X축에는 교란 크기가, Y축에는 정확도 메트릭이 표시됩니다. 플롯 요소는 모델 정확도의 분포를 나타내는 상자 그림입니다. 특이치는 모델의 정확도가 대부분의 데이터와 현저하게 다른 지점을 보여줍니다.

제로 교란은 데이터에 대한 수정 없이 모델의 기본 성능을 나타냅니다. 모델은 최고의 성능을 발휘하며, 높은 중앙값 정확도와 견고한 IQR로 나타납니다. 교란 크기가 커질수록 정확도가 저하하는 경향이 뚜렷하게 나타납니다. 이는 입력 데이터가 점점 교란을 받을수록 모델의 정확도가 낮아진다는 것을 나타냅니다. 예상대로, 성능 저하는 교란이 커질수록 더욱 뚜렷해집니다.

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

이 유형의 분석은 모델이 입력 데이터의 변화에 얼마나 민감한지 이해하는 데 중요하며, 입력 데이터가 훈련 중에 본 조건과 다를 수 있는 실제 시나리오에서 모델이 얼마나 잘 수행될 수 있는지를 평가하는 데 도움이 될 수 있습니다.

B.2.6 세분화된 진단 분석

우리가 논의할 마지막 유형의 분석은 특히 유용한데, 이는 개별 기능에 대한 심층적인 살피기를 통해 가장 취약한 영역을 찾는 데 도움이 됩니다. 아래 코드 스니펫을 사용하여 예제를 생성합니다. 모델 XGB1의 경우, 코드는 '교육'의 다른 세그먼트가 정확도와 같은 기본 성능 측정에 대해 어떻게 수행되고 있는지 보여줍니다. '교육'은 범주형 기능이며 균일하게 세분화될 것입니다.

결과는 아래와 같습니다. 세그먼트 ID는 정확도를 오름차순으로 정렬하여 할당됩니다. 따라서 세그먼트 ID=0은 가장 낮은 정확도를 가진 세그먼트 2에 할당됩니다. 크기 매개변수는 세그먼트에 포함된 샘플 수를 보여줍니다. 가장 낮은 정확도를 가진 세그먼트에는 가장 적은 수의 샘플이 포함되어 있는 것은 좋은 소식입니다.

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

위 결과를 더 자세히 조사할 수 있습니다. 특히 가장 낮은 정확도를 보여주는 세그먼트 0을 살펴볼 필요가 있습니다. 아래 코드 조각은 'show' 매개변수에 'accuracy_table' 값을 할당하여 이를 수행합니다.

결과는 아래 테이블 3에서 표시됩니다. 해당 테이블은 다섯 가지 숫자 성능 측정 항목과 세그먼트 0의 훈련 데이터와 테스트 데이터 간의 성능 차이에 대한 중요한 정보를 보여줍니다. 또한, 위의 정확도 테이블에 표시된 수치가 테스트 데이터의 가장 낮은 정확도였음을 알 수 있습니다.

결론

Yellowbrick와 PiML은 데이터 과학자와 분석가들에게 모델 동작에 대한 이해를 심화하고자 하는 사람들에게 가치 있는 자원을 제공합니다. 이 도구들이 제공하는 시각화와 통찰력을 통해 사용자들은 데이터 품질 및 모델 성능과 관련된 여러 문제를 해결하고 이를 개선할 수 있습니다. 이를 통해 다양한 시나리오와 데이터 세그먼트에 대해 정확하고 견고하며 신뢰할 수 있는 모델을 보유할 수 있습니다. 이 글에서는 이러한 패키지의 일부 기능만 다루었습니다. 특히 PiML은 데이터 품질 평가, 모델 내구성 등과 같은 다양한 기능을 제공합니다.

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

또한, 오늘날 증가하는 개인정보 보호에 대한 우려를 고려할 때, 로컬에서 작업하는 것이 사이버 보안 계획의 중요한 측면이 될 수 있습니다. 또 다른 문제는 오늘날 데이터의 증가하는 복잡성입니다. 이미지, 오디오 및 텍스트가로 결합되는 로봇학 및 다중 모달 LLM을 사용한 상황 객체 감지와 같은 다중 모달 응용 프로그램이 있습니다. 이러한 모든 모달리티의 관심 대상은 가능한 정확하게 모델링되어야 하므로, 다양한 모델의 다른 측면을 어떻게 평가하는지에 대해 알고하는 것이 중요합니다.

모든 논의된 예제의 코드는 제 Github 저장소에서 찾을 수 있습니다: [https://github.com/theomitsa/Yellowbrik-PIML](https://github.com/theomitsa/Yellowbrik-PIML)

독자 여러분, 읽어 주셔서 감사합니다!

# 참고문헌

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

- Kaggle 노트북, 소득 분류 모델, https://www.kaggle.com/code/jieyima/income-classification-model

2. Manokhin, V., Practical Guide to Applied Conformal Prediction in Python: Learn and Apply The Best Uncertainty Frameworks to Your Industry Applications, Packt Publishing, December 2023.

# 사용된 데이터셋

- 와인 데이터셋: UCI Machine Learning Repository, https://archive.ics.uci.edu/dataset/109/wine, 라이센스: 본 데이터셋은 크리에이티브 커먼즈 저작자표시 4.0 국제 라이센스에 따라 라이센스가 부여됩니다.
- 성인 (인구조사 소득) 데이터셋: UCI Machine Learning Repository, https://archive.ics.uci.edu/dataset/2/adult 라이센스: 본 데이터셋은 크리에이티브 커먼즈 저작자표시 4.0 국제 라이센스에 따라 라이센스가 부여됩니다.

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

**참고: "저자가 아닌 경우, 모든 이미지는 저자에게 속합니다."**
