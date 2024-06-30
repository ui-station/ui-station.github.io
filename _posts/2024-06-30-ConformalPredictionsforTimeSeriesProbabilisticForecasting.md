---
title: "시간 시리즈 확률 예측을 위한 Conformal Predictions 방법 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_0.png"
date: 2024-06-30 22:21
ogImage: 
  url: /assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_0.png
tag: Tech
originalTitle: "Conformal Predictions for Time Series Probabilistic Forecasting"
link: "https://medium.com/dataman-in-ai/conformal-predictions-for-time-series-probabilistic-forecasting-2a892377bd5c"
---


<img src="/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_0.png" />

샘플 eBook 챕터(무료): [여기](https://github.com/dataman-git/modern-time-series/blob/main/20240522beauty_TOC.pdf)

Teachable.com에서의 eBook: $22.50 [여기](https://drdataman.teachable.com/p/home)

Amazon.com에서의 인쇄판: $65 [여기](https://a.co/d/25FVsMx)

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

실제 세계 응용 프로그램 및 계획에는 포인트 추정이 아닌 확률적 예측이 필요합니다. 확률적 예측 또는 예측 불확실성이라고도 하는 확률적 예측은 계획자에게 불확실성의 감을 줄 수 있습니다. 그러나 선형 회귀, 랜덤 예측 또는 그래디언트 부스팅 머신과 같은 전형적인 기계 학습 모델은 가능한 값의 범위가 아닌 평균 추정을 생성하도록 설계되었습니다. 이 책이 관심을 갖는 것은 점 추정에서 예측 구간으로 발전하는 과정이며, 현대의 시계열 모델링 기술이 이에 관심을 가지고 있습니다. 확률적 예측 시리즈에서는 'Monte Carlo Simulation for Time Series Probabilistic Forecasting'에서 Monte Carlo 시뮬레이션 기법 및 'Quantile Regression for Time Series Probabilistic Forecasting'에서 분위 회귀 기법을 소개했습니다. 이 장에서는 다른 인기있는 방법인 Conformal Prediction (CP)을 소개하겠습니다.

CP를 기본적인 예측 맥락에서 설명해 보겠습니다. 우리는 예측 모델의 목표가 조건부 평균에 대해 편향되지 않은 추정을 제공하는 것이라는 것을 알고 있습니다. 예측 값과 실제 값 간의 차이를 오차라고 합니다. 이 오류들은 무엇일까요? 모델이 확실하지 않은 불확실성입니다. 그렇다면 어떻게 불확실성을 양적으로 표현할까요? 이 질문의 답은 질문 자체에 있습니다. 추정 값과 실제 값 사이의 오차는 불확실성을 나타내며, 우리는 이 오류를 분석하여 불확실성을 양적으로 나타낼 수 있습니다. 그런 다음 예측값에 양적으로 측정된 불확실성을 더하거나 뺌으로써 예측 구간을 계산합니다. CP는 새로운 예측에 대한 자신감 수준을 결정하기 위해 이전 데이터를 사용합니다. CP는 새로운 예측이 예측 범위 내에 있을 확률 (예: 95%)을 보장합니다. 특정 모델을 언급하지 않았음에 주목하십시오. 따라서 CP는 모델에 관계없이 작동합니다.

CP의 구성은 다음과 같습니다:

- 오류는 실제 값과 예측 값 사이의 절대값입니다. 예측 값과 실제 값 사이의 오차를 작은 것부터 큰 것까지 나열할 것입니다. 히스토그램을 사용하여 오차 값의 비율을 표시할 것입니다.
- 대부분의 경우, 예를 들어 95%의 경우, 오류는 임계값 이하일 것입니다. 이 임계값은 예측 오류에 대한 용인 값으로 간주할 수 있습니다. 이 임계값은 예측에서 해당 실제 값까지의 오류가 95%의 경우 임계값 아래에 있을 것이라는 뜻입니다.
- 따라서 예측값에 허용 오차를 더하거나 빼면 해당 예측에 대한 예측 구간을 얻을 수 있습니다. 이는 우리가 예측한 실제 값이 예측 구간 내에 존재할 확률이 95%라는 것을 의미합니다. 그림 (A)는 일치 예측의 구성을 보여줍니다.

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

![Conformal Prediction Procedure](/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_1.png)

이 예측 간격을 찾는 프로시저는 Conformal Prediction (CP) 전략입니다. 모델 사양과 기본 데이터 분포에 대한 가정을 하지 않는다는 점에 유의하십시오. CP는 모델에 구애받지 않으며 — 모든 모델링 기법과 함께 작동합니다. Conformal Prediction 기술은 Volodya Vovk, Alexander Gammerman, 그리고 Craig Saunders (1999) [1], 그리고 Harris Papadopoulos, Kostas Proedrou, Volodya Vovk, 그리고 Alex Gammerman (2002) [2]에 의해 제안되었습니다. Conformal Prediction 알고리즘은 다음과 같이 작동합니다:

- 과거 시계열 데이터를 교육, 보정, 테스트 기간으로 분할합니다.
- 교육 데이터에서 모델을 훈련합니다.
- 훈련된 모델을 사용하여 보정 데이터에 대한 예측을 생성합니다. 그런 다음 예측 오차의 히스토그램을 개발하고 Figure (A)와 같이 허용 수준을 정의합니다.
- 미래 점 추정치와 함께 예측 데이터 내의 예측에 허용 간격을 더하고 빼어 예측 간격을 제공합니다.

소프트웨어 요구사항

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

예측 구간에 대해 NeuralProphet은 세 가지 옵션을 가질 수 있어요: (i) 분위 회귀(Quantile regression, QR), (ii) 일치 예측(Conformal predictions, CP) 및 (iii) 일치화된 분위 회귀(Conformalized quantile regression, CQR). "시계열 확률적 예측을 위한 분위수 회귀(Quantile Regression for Time Series Probabilistic Forecasting)"에서 분위 회귀를 연구했습니다. 이 장에서는 일치 예측을 수행하고, 다음 장 "시계열 확률적 예측을 위한 일치화된 분위 회귀(Conformalized Quantile Regression for Time Series Probabilistic Forecasting)"에서는 일치화된 분위 회귀를 할 거에요. NeuralProphet를 설치하려면 표준 설치 pip install NeuralProphet를 따르시면 돼요.

```shell
!pip install neuralprophet
```

Google Colab을 사용하는 경우, NeuralProphet은 numpy1.23.5를 사용하지 않으면 작동하지 않는다는 점을 염두에 두세요. numpy를 제거하고 numpy1.23.5를 설치해야 해요.

```shell
# neuralprophet은 numpy1.23.5를 사용하지 않으면 colab에서 작동하지 않습니다: https://github.com/googlecolab/colabtools/issues/3752
!pip uninstall numpy
!pip install git+https://github.com/ourownstory/neural_prophet.git numpy==1.23.5
```

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

데이터

이전 NeuralProphet 챕터를 계속 진행하면서, 이 챕터에서는 Kaggle에서 Bike Share Daily 데이터를 사용할 것입니다. 이전 챕터에서 데이터 단계를 상세히 살펴봤기 때문에, 여기서는 설명 없이 데이터를 바로 로드할 것입니다. 데이터를 이해하려면 이전 챕터 중 하나를 참조할 수 있습니다:

- 챕터 3: 튜토리얼 I: 추세 + 계절성 + 휴일 및 이벤트
- 챕터 4: 튜토리얼 II: 추세 + 계절성 + 휴일 및 이벤트 + 자기 회귀 (AR) + 지연 회귀 + 미래 회귀
- 챕터 5: "시계열 확률 예측을 위한 분위수 회귀".

```js
%matplotlib inline
from matplotlib import pyplot as plt
import pandas as pd
import numpy as np
import logging
import warnings
logging.getLogger('prophet').setLevel(logging.ERROR)
warnings.filterwarnings("ignore")

# If you use Google Colab
from google.colab import drive
drive.mount('/content/gdrive')
path = '/content/gdrive/My Drive/data/time_series'
data = pd.read_csv(path + '/bike_sharing_daily.csv')
data.tail()
```

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


![dataset](/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_2.png)

이 데이터셋은 매일의 대여 수요와 온도 또는 풍속과 같은 기타 날씨 정보를 포함한 다변량 데이터셋입니다. 모델링을 위해 매우 간단한 데이터 준비를 진행할 것입니다. NeuralProphet은 열 이름을 "ds"와 "y"로 요구합니다.

```python
# 문자열을 datetime64로 변환
data["ds"] = pd.to_datetime(data["dteday"])
df = data[['ds','cnt']]
df.columns = ['ds','y']
```

이제 모델을 설정하겠습니다.


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

모델링

다음과 같이 매우 간단한 NeuralProphet 모델을 사용할 것입니다. 이 모델은 추세 및 계절성 패턴만을 갖고 있습니다. 이전 장에서 언급한 대로 AR, 휴일 및 기타 공변량과 같은 다른 구성 요소를 추가할 수 있습니다.

```js
from neuralprophet import NeuralProphet, set_log_level
cp_model = NeuralProphet(
    yearly_seasonality=True,
    weekly_seasonality=True,
    daily_seasonality=False,
)
cp_model.set_plotting_backend("plotly-static")
```

이제 데이터를 준비해 봅시다.

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

학습, 보정 및 테스트 데이터

적합성 예측 또는 적합성 양자 회귀 기술에서 중요한 단계는 훈련 데이터를 훈련 및 보정 데이터로 분할하는 것입니다. 보정 데이터는 허용 통계를 구성하는 데 사용됩니다.

```js
df_train, df_test = cp_model.split_df(df, valid_p=0.2)
df_train, df_cal = cp_model.split_df(df_train, freq="D", valid_p=1.0 / 11)
[df_train.shape, df_test.shape, df_cal.shape]
# [(532, 2), (146, 2), (53, 2)]
```

세 가지 색으로 데이터 하위 집합을 그립니다.

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

<img src="/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_3.png" />

모델 구축은 이전 챕터와 동일합니다. 보정 데이터를 모델 검증 세트로 사용합니다.

```js
metrics = cp_model.fit(df_train, validation_df=df_cal, progress="bar")
metrics.tail()
```

<img src="/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_4.png" />

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

그러면 예측을 하고 예측 간격을 첨부할 준비가 된 것입니다. NeuralProphet은 자동으로 CP를 수행할 수 있지만, 여러분에게 단계를 보여주기 위해 수동으로 진행합니다.

일치 예측

우리는 "df" 데이터의 마지막 날짜로부터 50개의 기간을 포함하는 "미래" 데이터셋을 만들 것입니다. 이 데이터는 모든 과거 데이터에 대한 모델 예측값을 포함할 것입니다. 또는 n_historic_predictions=40을 지정하면 40개의 과거 데이터 포인트와 그에 대한 예측값만 포함할 것입니다.

NeuralProphet의 CP 옵션은 method=naive입니다. 우리는 `.conformal_prediction()`을 사용하여 일치 예측을 활성화할 것입니다.

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

```js
미래 = cp_model.make_future_dataframe(df, periods=50, 
     n_historic_predictions=True)

# 단순 일치 예측을 위한 매개변수
방법 = "naive"
알파 = 0.05

# 사전 훈련된 모델에서 일치 예측 활성화
cp_forecast = cp_model.conformal_predict(
    # df_test, # df_test를 사용할 수도 있습니다. 
    미래,
    calibration_df=df_cal,
    alpha=알파,
    method=방법,
    show_all_PI=True,
)
cp_forecast
```

결과에는 예측 'yhat1'과 상부 경계 'yhat1 + qhat1'가 포함되어 있습니다. 'qhat1'은 캘리브레이션 데이터에서 유도된 허용 간격입니다.

<img src="/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_5.png" />

'yhat1'에서 'yhat1+qhat1'을 빼서 'qhat1'을 얻을 수 있습니다. 이 값은 1951.214의 단일 값입니다. 그런 다음 yhat1에서 qhat1을 빼서 하한을 구성할 수 있습니다.

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

```js
cp_forecast['qhat1'] = cp_forecast['yhat1 + qhat1'] - cp_forecast['yhat1'] 
cp_forecast['yhat1 - qhat1'] = cp_forecast['yhat1'] - cp_forecast['qhat1']
cp_forecast
```

<img src="/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_6.png" />

예측값과 예측 구간을 그려봅시다. 모든 기간에 대해 CP가 고정 값임을 알 수 있습니다. 예측값에 더하거나 빼서 상한값과 하한값을 얻습니다.

```js
import matplotlib.pyplot as plt
plt.figure(figsize=(10,6))
# 각 시리즈를 플롯합니다
plt.plot(df_train['ds'],df_train['y'], label='학습 데이터')
plt.plot(df_cal['ds'],df_cal['y'], label='보정 데이터')
plt.plot(df_test['ds'],df_test['y'], label='테스트 데이터')

plt.plot(cp_forecast['ds'],cp_forecast['yhat1'], label='예측값')
plt.plot(cp_forecast['ds'],cp_forecast['yhat1 - qhat1'], label='하한값')
plt.plot(cp_forecast['ds'],cp_forecast['yhat1 + qhat1'], label='상한값')
plt.legend()
plt.title('일치 예측')
plt.xticks(rotation=45, ha='right')
# 수직 점선 그리기
plt.axvline(x=df_test['ds'].tail(1), color='r', linestyle='--', linewidth=2) 
plt.show()
```

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


![Conformal Prediction Example](/assets/img/2024-06-30-ConformalPredictionsforTimeSeriesProbabilisticForecasting_7.png)

중요한 점은 허용 구간이 보정 데이터의 실제 값에서 유도된다는 것입니다.

결론

본 장에서는 예측 구간을 제공하기 위한 Conformal Prediction 기술을 소개했습니다. 우리는 CP의 구성을 기본 통계 개념에서 배웠습니다. CP는 모델 가정에 의존하지 않고 어떤 모델에도 적용할 수 있는 모델에 중립적입니다. 또한 NeuralProphet에서 CP를 구성하는 코드 예제를 시연했습니다.


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

일부분에서 예측 구간의 길이가 모든 기간에 걸쳐 동일하다는 것을 알아차린 분들도 있을 것입니다. 특정 시나리오에 대해 다양한 예측 구간이 더 나은 의미를 갖을 수 있다고 제안할 수 있습니다. 다음 장에서는 다양한 예측 구간을 허용하는 Conformalized Quantile Regression (CQR)을 연구할 것입니다.

참고문헌

- Volodya Vovk, Alexander Gammerman, and Craig Saunders. Machine-learning applications of algorithmic randomness. In International Conference on Machine Learning, pages 444–453, 1999.
- Harris Papadopoulos, Kostas Proedrou, Volodya Vovk, and Alex Gammerman. Inductive confidence machines for regression. In European Conference on Machine Learning, pages 345–356. Springer, 2002.

샘플 eBook 장(chapter) (무료): [링크](https://github.com/dataman-git/modern-time-series/blob/main/20240522beauty_TOC.pdf)

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

- 이니빠레이션 프레스(The Innovation Press, LLC) 스탭 분들께 감사드립니다. 아름다운 형식으로 책을 재구성하여 즐거운 독서 경험을 제공해 주셔서 감사합니다. 전 세계 독자들에게 엄두 나지 않는 경영비용 없이 Teachable 플랫폼을 선택했습니다. 신용카드 거래는 Teachable.com이 비밀리에 안전하게 처리합니다.

Teachable.com에서 eBook: $22.50
https://drdataman.teachable.com/p/home

Amazon.com에서 인쇄본: $65 https://a.co/d/25FVsMx

- 인쇄본은 광택 처리된 표지, 컬러 인쇄, 아름다운 Springer 글꼴 및 레이아웃을 채택하여 즐거운 독서를 제공합니다. 7.5 x 9.25인치의 사이즈는 여러분의 책장에 있는 대부분의 책들과 잘 어울립니다.
- "이 책은 Kuo의 시계열 분석과 예측 분석, 그리고 이상 탐지에 대한 깊은 이해를 증명합니다. 이 책은 독자들에게 현실 세계의 과제에 대처하는 데 필요한 기술을 제공합니다. 데이터 사이언스로의 직업 전환을 고민하는 사람들에게 특히 가치 있는 책입니다. Kuo는 전통적이고 최신 기술을 자세히 탐구합니다. Kuo는 신경망과 다른 고급 알고리즘에 대한 논의를 통합하여 최신 동향과 발전을 반영합니다. 이를 통해 독자들이 확립된 방법뿐만 아니라 데이터 사이언스 분야의 가장 현재와 혁신적인 기술과 함께 소통할 수 있는 준비가 되도록 합니다. Kuo의 흥미로운 글쓰기 스타일로 인해 책의 명료함과 접근성이 향상되었습니다. 그는 복잡한 수학 및 통계 개념을 신비롭지 않게 다루어 엄격함을 희생하지 않으면서도 쉽게 접근 가능하게 만듭니다."

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

# 모던 시계열 예측: 예측 분석과 이상 탐지

제로 장편: 서문

제1 장: 소개

제2 장: 비즈니스 예측을 위한 선지자

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

### Chapter 3: 튜토리얼 1 - 추세 + 계절성 + 휴일 및 이벤트

### Chapter 4: 튜토리얼 2 - 추세 + 계절성 + 휴일 및 이벤트 + 자기 회귀(AR) + 시차 회귀변수 + 미래 회귀변수

### Chapter 5: 시계열 데이터의 변곡점 탐지

### Chapter 6: 시계열 확률 예측을 위한 몬테카를로 시뮬레이션

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

7장: 시계열 확률 예측을 위한 분위 회귀 분석

8장: 시계열 확률 예측을 위한 적응적 예측

9장: 시계열 확률 예측을 위한 적응적 분위 회귀 분석

10장: 자동 ARIMA!

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

# 11장: 시계열 데이터 형식 손쉽게 다루기

# 12장: 다기간 확률 예측을 위한 선형 회귀

# 13장: 트리 기반 시계열 모델을 위한 피처 엔지니어링

# 14장: 다기간 시계열 예측을 위한 두 가지 주요 전략

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

# 15장: 다기간 시계열 확률 예측을 위한 트리 기반 XGB, LightGBM 및 CatBoost 모델

# 16장: 시계열 모델링 기법의 진화

# 17장: 시계열 확률 예측을 위한 딥러닝 기반 DeepAR

# 18장: 응용 - 주식 가격에 대한 확률 예측

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

# 19장: RNN에서 Transformer 기반 시계열 모델로

# 20장: 해석 가능한 시계열 예측을 위한 Temporal Fusion Transformer

# 21장: 시계열 예측을 위한 오픈 소스 Lag-Llama 튜토리얼