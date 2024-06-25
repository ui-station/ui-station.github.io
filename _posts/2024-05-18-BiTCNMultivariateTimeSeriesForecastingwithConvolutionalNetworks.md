---
title: "BiTCN 컨볼루션 네트워크를 활용한 다변수 시계열 예측"
description: ""
coverImage: "/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_0.png"
date: 2024-05-18 19:41
ogImage:
  url: /assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_0.png
tag: Tech
originalTitle: "BiTCN: Multivariate Time Series Forecasting with Convolutional Networks"
link: "https://medium.com/towards-data-science/bitcn-multivariate-time-series-forecasting-with-convolutional-networks-1471347c1bcc"
---

![image](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_0.png)

시계열 예측 분야에서는 모델의 아키텍처가 주로 다층 퍼셉트론(MLP) 또는 트랜스포머 아키텍처에 의존합니다.

N-HiTS, TiDE 및 TSMixer와 같은 MLP 기반 모델은 훈련 속도가 빠르면서 매우 좋은 예측 성능을 달성할 수 있습니다.

한편, PatchTST 및 iTransformer와 같은 트랜스포머 기반 모델도 좋은 성능을 달성하지만 더 많은 메모리를 소비하고 더 많은 훈련 시간이 필요합니다.

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

아직도 예측에서 널리 활용되고 있지 않은 아키텍처 하나가 있습니다: 합성곱 신경망(CNN).

전통적으로 CNN은 컴퓨터 비전에 적용되었지만, 예측 분야에서는 TimesNet이 최근의 예만 있습니다.

그러나 CNN은 순차 데이터를 처리하는 데 효과적임이 입증되었으며, 그들의 아키텍처는 병렬 계산을 허용하여 훈련 속도를 크게 높일 수 있습니다.

따라서 본 기사에서는 2023년 3월 논문 'Parameter-efficient deep probabilistic forecasting'에서 제안된 BiTCN을 탐색합니다. 두 개의 시계열 합성곱 신경망(TCN)을 활용하여 이 모델은 과거와 미래의 변수를 인코딩하면서도 계산 효율적인 특징을 유지합니다.

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

더 자세한 내용은 원본 논문을 꼭 읽어보세요.

시작해봅시다!

## BiTCN 탐험

이전에 언급된대로, BiTCN은 두 개의 시계열 합성곱 신경망을 활용하므로 그 이름이 BiTCN입니다.

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

한 TCN은 미래 공변량을 인코딩하고, 다른 하나는 과거 공변량 및 시계열의 역사적 값들을 인코딩합니다. 이렇게 함으로써 모델은 데이터로부터 시간 정보를 배울 수 있고, 합성곱의 사용으로 계산 효율성을 유지할 수 있습니다.

여기에는 분석할 것이 많기 때문에 아키텍처를 좀 더 자세히 살펴보겠습니다.

## BiTCN 아키텍처

BiTCN의 아키텍처는 많은 시계열 블록으로 구성되어 있습니다. 각 블록은 다음과 같이 구성됩니다:

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

- 확장된 합성곱
- GELU 활성화 함수
- 드롭아웃 단계
- 완전 연결 레이어

시계열 블록의 일반적인 아키텍처는 아래에 표시됩니다.

![temporal block](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_1.png)

위 그림에서 각 시계열 블록이 출력 O를 생성함을 볼 수 있습니다. 최종 예측은 N개의 레이어에 쌓인 각 블록의 모든 출력을 더하여 얻습니다.

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

드롭아웃과 댄스 레이어는 신경망에서 흔한 구성 요소입니다. 그러나 이번에는 확장 컨볼루션(dilated convolution)과 GELU 활성화 함수에 대해 좀 더 자세히 살펴봅시다.

## 확장 컨볼루션

확장 컨볼루션의 목표를 더 잘 이해하기 위해, 기본적인 컨볼루션이 어떻게 작동하는지 상기해 봅시다.

![이미지](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_2.png)

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

위 그림에서 일차원 입력에 대한 전형적인 합성곱이 어떻게 보이는지 볼 수 있습니다. 출력의 길이를 동일하게 유지하기 위해 입력 시리즈는 왼쪽에 0으로 채워집니다.

세 개의 커널 크기와 한 개의 스트라이드를 가정할 때, 위 그림에 나와 있는대로 출력 텐서도 네 개의 길이를 가집니다.

출력의 각 요소가 세 개의 입력 값을 기반으로 한다는 것을 볼 수 있습니다. 다시 말해 출력은 색인의 값과 이전 두 값에 의존합니다.

이를 수용 영역(Receptive Field)이라고 합니다. 시계열 데이터를 다루고 있으므로 출력 계산이 더 긴 이력을 볼 수 있도록 수용 영역을 증가시키는 것이 유익할 것입니다.

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

그렇게 하려면, 커널 크기를 크게 하거나 더 많은 합성곱 계층을 쌓을 수 있습니다. 커널 크기를 크게 하는 것은 최선의 선택이 아닙니다. 정보를 손실하고 모델이 데이터의 유용한 관계를 학습하지 못할 수 있습니다. 그래서 더 많은 합성곱을 쌓아보겠습니다.

위 그림에서 볼 수 있듯이, 커널 크기가 3인 두 개의 합성곱 작업을 쌓으면 출력의 마지막 요소는 이제 입력의 다섯 요소에 의존합니다. 따라서 수용 영역이 3에서 5로 증가했습니다.

안타깝게도 이것도 문제가 됩니다. 이러한 방식으로 수용 영역을 증가시키면 아주 깊은 신경망이 생성될 수 있습니다.

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

따라서, 우리는 모델에 너무 많은 레이어를 추가하지 않으면서 수용 영역을 증가시키기 위해 확장된 합성곱을 사용합니다.

![이미지](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_4.png)

위의 그림에서 우리는 2-확장된(convolution)을 실행한 결과를 볼 수 있습니다. 기본적으로 매 두 요소가 하나의 출력을 생성하는 것으로 간주됩니다. 따라서 우리는 이제 컨벌루션을 쌓지 않고도 수용 영역이 5임을 볼 수 있습니다.

실제로 수용 영역을 더 증가시키기 위해, 주로 2로 설정된 확장 베이스를 사용하여 많은 희석커널(diluted kernel)을 쌓습니다. 이는 첫 번째 레이어가 2¹-확장 커널이되고, 그다음에 2²-확장 커널이 따르며, 그런 다음 2³로 이어지는 방식입니다.

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

수용 영역이 늘어나면 모델은 더 긴 입력 시퀀스를 고려하여 출력을 생성할 수 있습니다. Dilated convolutions을 사용하면 합리적인 수의 레이어를 유지할 수도 있습니다.

이제 Dilated convolutions의 내부 작업을 이해했으니 GELU 활성화 함수를 알아보겠습니다.

## GELU 활성화 함수

많은 딥러닝 아키텍처에서는 ReLU(Recitified Linear Unit) 활성화 함수를 사용합니다. ReLU의 방정식은 아래와 같이 표시됩니다.

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

위의 식을 보면 ReLU는 간단히 0과 입력 중 최대 값을 취하는 것을 알 수 있습니다. 다시 말해, 입력이 양수이면 입력이 반환되고, 입력이 음수이면 0이 반환됩니다.

ReLU는 사라지는 그래디언트 문제를 완화하는 데 도움이 되지만 죽은 ReLU 문제를 만들 수도 있습니다.

이는 네트워크에서 일부 뉴런이 오직 0만 출력하여 모델의 학습에 더 이상 기여하지 않는 경우에 발생합니다.

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

해당 상황에 대처하기 위해 가우시안 에러 선형 유닛 또는 GELU를 사용할 수 있습니다. GELU 방정식은 아래와 같이 나타낼 수 있습니다.

![GELU Equation](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_6.png)

이 함수를 사용하면 입력 값이 0보다 작을 때 작은 음수 값을 활성화 함수로 사용할 수 있습니다.

![Activation Function with GELU](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_7.png)

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

이렇게 하면 신경세포가 소멸하지 않게 되어 음수 입력 값을 사용하여 0이 아닌 값이 반환될 수 있습니다. 이는 역전파에 대해 더 풍부한 그래디언트를 제공하며 모델의 기능을 유지할 수 있습니다.

## BiTCN에서 모두 모아보기

이제 BiTCN의 시간 블록의 내부 작업을 이해했으니, 우리는 모델에서 모든 것이 어떻게 함께 동작하는지 살펴봅시다.

![이미지](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_8.png)

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

위 그림에서는 늦게 발생한 값들이 밀도 레이어를 통과하고 시간 블록 스택을 거친 후 모든 이전 공변량과 결합된 것을 볼 수 있습니다.

상단에는 범주형 공변량이 다른 공변량과 결합되기 전에 먼저 임베딩된 것을 볼 수 있습니다. 여기서는 미래와 과거 공변량이 아래에 표시된 대로 모두 결합됨에 유의해주세요.

그럼 그 값들은 밀도 레이어와 시간 블록 스택을 거쳐 이끌어집니다.

최종 출력은 아래에 표시된 것과 같이 늦게 발생한 값과 공변량에서 나온 정보가 결합된 것입니다.

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

![그림](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_9.png)

위의 그림은 하나의 시간 블록이 공변량의 미래 값을 활용하여 모델 출력을 결정하는 아이디어를 강조합니다 (빨간 점으로 표시됨).

마지막으로, BiTCN은 예측 주변에 신뢰 구간을 구성하기 위해 Student’s t-분포를 사용합니다.

이제 BiTCN의 내부 작업을 이해했으니, Python을 사용하여 소규모 예측 프로젝트에 적용해 봅시다.

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

# BiTCN을 사용한 예측

이 실험에서는 BiTCN을 N-HiTS 및 PatchTST와 함께 사용하여 장기 예측 작업을 수행합니다.

구체적으로, 블로그 웹사이트의 일일 조회수를 예측하는 데 사용합니다. 데이터셋에는 일일 조회수와 새로운 글이 게시된 날짜를 나타내는 지표, 미국의 공휴일을 나타내는 지표와 같은 외생 특성이 포함되어 있습니다.

이 데이터셋은 제가 직접 제 웹사이트의 트래픽을 사용하여 컴파일했습니다. 데이터셋은 여기서 공개적으로 이용 가능합니다.

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

본 부분에서는 외부 기능을 지원하는 BiTCN의 사용 준비 구현을 제공하는 것으로 내가 알기로는 유일한 라이브러리인 neuralforcast를 사용합니다.

언제나 GitHub에 이 실험의 전체 소스 코드가 있습니다.

시작해 봅시다!

## 초기 설정

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

이 프로젝트에 필요한 라이브러리를 가져오는 것이 첫 번째 단계입니다.

```js
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from neuralforecast.core import NeuralForecast
from neuralforecast.models import NHITS, PatchTST, BiTCN
```

그런 다음, 데이터를 DataFrame으로 읽어옵니다.

```js
df = pd.read_csv('https://raw.githubusercontent.com/marcopeix/time-series-analysis/master/data/medium_views_published_holidays.csv')
df['ds'] = pd.to_datetime(df['ds]')
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

데이터를 그래프로 나타낼 수도 있습니다.

```js
published_dates = df[df["published"] == 1];
holidays = df[df["is_holiday"] == 1];

fig, (ax = plt.subplots((figsize = (12, 8))));

ax.plot(df["ds"], df["y"]);
ax.scatter(
  published_dates["ds"],
  published_dates["y"],
  (marker = "o"),
  (color = "red"),
  (label = "새 기사")
);
ax.scatter(
  holidays["ds"],
  holidays["y"],
  (marker = "x"),
  (color = "green"),
  (label = "미국 공휴일")
);
ax.set_xlabel("날짜");
ax.set_ylabel("총 조회수");
ax.legend((loc = "best"));

fig.autofmt_xdate();

plt.tight_layout();
```

![이미지](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_10.png)

위 그림에서, 주중에 주말보다 더 많은 방문이 발생하는 주별 계절성이 명확히 나타납니다.

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

또한, 방문 횟수의 급증은 일반적으로 새로운 기사가 게시된 후 발생합니다(빨간 점으로 표시됨). 새로운 콘텐츠가 더 많은 트래픽을 유도하기 때문에 새로운 기사가 게시될 때 일반적으로 트래픽이 증가합니다. 마지막으로, 미국 공휴일(녹색 십자로 표시됨)은 종종 낮은 트래픽을 시사합니다.

따라서, 외부 요인의 영향을 명확히 볼 수 있는 시리즈이며, BiTCN을 위한 훌륭한 사용 사례입니다.

## 데이터 처리

이제 데이터를 학습 세트와 테스트 세트로 분할해 봅시다. 테스트를 위해 마지막 28개 항목을 예약합니다.

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

```python
train = df[:-28]
test = df[-28:]
```

그런 다음, 예보 기간에 대한 날짜 및 외생 변수의 미래 값이 포함된 DataFrame을 생성합니다.

미래의 외생 변수 값을 제공하는 것이 의미가 있다는 점에 유의해야 합니다. 미래의 미국의 공휴일 날짜는 미리 알려져 있으며, 기사의 발행 또한 계획할 수 있기 때문입니다.

```python
future_df = test.drop(['y'], axis=1)
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

좋아요! 이제 시리즈를 모델링할 준비가 되었습니다.

## 모델링

언급했듯이, 이 프로젝트에서는 N-HiTS(MLP 기반), BiTCN(CNN 기반) 및 PatchTST(Transformer 기반)를 사용합니다.

N-HiTS와 BiTCN은 둘 다 외부 특성을 사용한 모델링을 지원하지만, PatchTST는 지원하지 않습니다.

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

이 실험의 수평선은 테스트 세트의 전체 길이를 포함하기 위해 28로 설정됩니다.

```js
horizon = len(test);

models = [
  NHITS(
    (h = horizon),
    (input_size = 5 * horizon),
    (futr_exog_list = ["published", "is_holiday"]),
    (hist_exog_list = ["published", "is_holiday"]),
    (scaler_type = "robust")
  ),
  BiTCN(
    (h = horizon),
    (input_size = 5 * horizon),
    (futr_exog_list = ["published", "is_holiday"]),
    (hist_exog_list = ["published", "is_holiday"]),
    (scaler_type = "robust")
  ),
  PatchTST(
    (h = horizon),
    (input_size = 2 * horizon),
    (encoder_layers = 3),
    (hidden_size = 128),
    (linear_hidden_size = 128),
    (patch_len = 4),
    (stride = 1),
    (revin = True),
    (max_steps = 1000)
  ),
];
```

그런 다음, 훈련 세트에 모델을 적용합니다.

```js
nf = NeuralForecast((models = models), (freq = "D"));
nf.fit((df = train));
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

그럼, 우리는 외부 요인의 미래 값을 사용하여 예측을 생성할 수 있어요.

```js
preds_df = nf.predict((futr_df = future_df));
```

좋아요! 지금 이 시점에서, preds_df에 저장된 예측이 있어요. 각 모델의 성능을 평가할 수 있어요.

## 평가

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

예측값과 실제 값들을 하나의 DataFrame으로 합치는 것으로 시작합니다.

```python
test_df = pd.merge(test, preds_df, 'left', 'ds')
```

선택적으로, 예측값을 실제 값과 비교해서 아래 그림과 같이 시각화할 수도 있습니다.

![예제 그림](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_11.png)

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

위의 그림에서는 모든 모델이 실제 트래픽을 전반적으로 과대 예측한 것으로 보입니다.

그럼 최상의 성능을 발휘하는 모델을 찾기 위해 평균 절대 오차 (MAE)와 대칭 평균 절대 백분율 오차 (sMAPE)를 측정해보겠습니다.

```js
from utilsforecast.losses import mae, smape
from utilsforecast.evaluation import evaluate

evaluation = evaluate(
    test_df,
    metrics=[mae, smape],
    models=["NHITS", "BiTCN", "PatchTST"],
    target_col="y",
)

evaluation = evaluation.drop(['unique_id'], axis=1)
evaluation = evaluation.set_index('metric')

evaluation.style.highlight_min(color='blue', axis=1)
```

![이미지](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_12.png)

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

위의 표에서 BiTCN이 최상의 성능을 달성했음을 확인할 수 있습니다. 해당 모델의 MAE 및 sMAPE가 가장 낮기 때문입니다.

이 실험만으로는 BiTCN의 강력한 벤치마크는 아니지만, 외생 변수를 활용한 예측 문맥에서 가장 우수한 결과를 달성하는 것을 볼 수 있어 흥미로운 실험입니다.

# 결론

BiTCN 모델은 이전 값과 미래 값을 함께 인코딩하기 위해 두 개의 시간 합성곱 신경망을 활용하여 효율적인 다변량 시계열 예측을 수행합니다.

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

시계열 분야에서 컨볼루션 신경망의 성공적인 응용을 보는 것은 흥미로운 일이죠. 대부분의 모델은 MLP 또는 트랜스포머를 기반으로 하지만요.

저희의 소규모 실험에서 BiTCN이 가장 우수한 성능을 발휘했습니다. 하지만 저는 각 문제에는 독특한 해결책이 필요하다고 믿습니다. 이제 BiTCN을 도구 상자에 추가하고 여러분의 프로젝트에 적용해 보세요.

독자 여러분, 읽어 주셔서 감사합니다! 즐기셨기를 바라며 무엇인가 새로운 것을 배우셨기를 기대합니다.

건배 🍻

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

# 저를 지지해주세요

제 작품을 즐기고 계신가요? Buy me a coffee로 제게 지지를 보여주세요. 그러면 여러분은 제게 격려를 주고, 저는 커피 한 잔을 즐길 수 있어요! 만약 그러고 싶다면, 아래 버튼을 클릭해주세요 👇

![Buy me a coffee](/assets/img/2024-05-18-BiTCNMultivariateTimeSeriesForecastingwithConvolutionalNetworks_13.png)

# 참고자료

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

**Parameter-efficient deep probabilistic forecasting** by Olivier Sprangers, Sebastian Schelter, Maarten de Rijke

Explanation of **dilated convolution** and figures of **dilates convolutions** inspired: **Temporal convolutional networks and forecasting** by Unit8
