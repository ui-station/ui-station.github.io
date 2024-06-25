---
title: "나담 옵티마이저 뒤의 수학"
description: ""
coverImage: "/assets/img/2024-05-20-TheMathBehindNadamOptimizer_0.png"
date: 2024-05-20 20:12
ogImage:
  url: /assets/img/2024-05-20-TheMathBehindNadamOptimizer_0.png
tag: Tech
originalTitle: "The Math Behind Nadam Optimizer"
link: "https://medium.com/towards-data-science/the-math-behind-nadam-optimizer-47dc1970d2cc"
---

![이미지](/assets/img/2024-05-20-TheMathBehindNadamOptimizer_0.png)

이전에 우리가 Adam 옵티마이저에 대해 이야기했을 때, Adam이 적응적 학습률을 효과적으로 다루면서 기계 학습에서 최적화 지형을 변화시켰다는 것을 탐구했습니다. 다양한 기계 학습 대회에서 특히 Kaggle과 같은 플랫폼에서의 성공으로 알려진 Adam은 확실히 최적화 기술에 높은 기준을 설정했습니다. 그러나 최적화 알고리즘의 진화는 거기서 멈추지 않았습니다. 여기 나담(Nadam)이 나옵니다 — Nesterov-accelerated Adaptive Moment Estimation의 약자인 Adam의 고급 후속 버전입니다.

이 문서를 이해하기 위해 이전에 Adam에 대한 나의 기사를 읽을 필요는 없지만, 관심이 있다면 다음 링크에서 확인할 수 있습니다:

Nadam은 나스테로프 모멘텀을 통합하여 Adam 옵티마이저를 개선하며, 기울기 업데이트에 선행(lookahead) 능력을 도입합니다. 이 조정은 수렴 과정을 가속화할 뿐만 아니라 손실 함수를 최소화하기 위한 단계의 정확도도 향상시킵니다.

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

Nadam을 Adam보다 선택해야 하는 이유는 무엇일까요? 이 기사는 Nadam의 메커니즘을 분석하고 Adam과 비교하며 실제 적용에서의 통찰을 제공하여 이 질문에 대답합니다. 우리는 이를 지원하는 수학적 내용을 알아볼 것이며, Python에서 최적화 도구를 처음부터 구축하여 그것이 어떻게 실행되는지 살펴볼 것입니다. 이 기사를 마치면 기계 학습 프로젝트에 Nadam이 좋은 선택일 수 있는 시기와 이유를 명확히 이해하게 되어, 당신이 필요로 하는 최적화 도구에 대해 정보를 잘 얻을 수 있게 해줄 것입니다.

## 목차

1: Nadam: 개념 및 기원
∘ 1.1: Nadam이란 무엇인가?
∘ 1.2: Nadam이 Adam을 기반으로하는 방식

2: Nadam 뒤에 숨은 메커니즘
∘ 2.1: 초기화
∘ 2.2: 각 타임 스텝(𝑡)에 대한 반복적인 업데이트
∘ 2.3: 네스테로프 모멘텀 보정
∘ 2.4: 편향 보정
∘ 2.5: 매개변수 업데이트
∘ 2.6: Adam과의 주요 차이점

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

3. 실무에서 Nadam 구현하기

- 3.1: Nadam Optimizer 클래스 정의
- 3.2: 선형 회귀 모델 클래스
- 3.3: 모델 트레이너 클래스
- 3.4: 데이터셋 처리 및 모델 훈련

4. 장점과 고려사항

- 4.1 Nadam의 우수성
- 4.2 한계와 도전 과제

결론

참고문헌

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

# 1: 나담: 개념과 기원

## 1.1: 나담이란?

나담은 뉴럴 네트워크를 최적화하기 위해 기계 학습에서 널리 사용되는 아담 옵티마이저를 개선한 것입니다. 이는 아담의 적응 학습률 기능과 네스테로프 모멘텀의 예측 능력을 통합하였습니다.

이 개선은 모델이 수렴하는 속도를 높일뿐만 아니라 복잡한 최적화 과제를 효과적으로 해결하는 방법을 제공합니다.

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

나담(Nadam) 뒤에 있는 아이디어는 1980년대 유리 네스테로프(Yuri Nesterov)의 선도적인 작업으로 거슬러 올라갈 수 있습니다. 그 당시 네스테로프는 네스테로프의 가속 그래디언트(Nesterov’s accelerated gradient, NAG)를 소개했습니다. NAG의 목표는 기울기 중심의 최적화 알고리즘의 수렴을 가속화하여 기울기의 경로를 가장 낮은 점으로 더 잘 지시하는 것이었습니다. 이 전략을 아담(Adam)의 적응형 학습률 조정과 결합하여, 나담은 이러한 기본 개념을 향상시켜 최적화 알고리즘의 능력을 크게 향상시킵니다.

## 1.2: 나담(Nadam)이 아담(Adam)을 바탕으로 어떻게 구축되는가

나담은 아담이 설정한 프레임워크를 바탕으로 업데이트가 어떻게 계산되는지를 조정함으로써 구축됩니다. 아담이 과거 제곱 기울기와 과거 기울기의 지수 이동 평균을 사용하는 반면, 나담은 기울기 업데이트 규칙을 수정합니다. 나담은 누적된 기울기 방향으로 적극적으로 전진하며, 미래 기울기를 예측하는 종류의 "모멘텀"(momentum)을 활용합니다.

이를 더 잘 이해하기 위해 최적화 경로를 보는 것을 상상해보세요. 위의 이미지는 나담과 아담이 최적화 랜드스케이프를 통해 어떻게 이동하는지 개념적으로 보여줍니다.

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

![TheMathBehindNadamOptimizer](/assets/img/2024-05-20-TheMathBehindNadamOptimizer_1.png)

이 그림에서 두 옵티마이저는 동일한 지점에서 시작합니다. "Adam"은 효율적인 수렴과 적응력을 나타내는 평탄하고 잘 포장된 고속도로 위에 있습니다. "Nadam"은 가끔 충격이 있는 약간 더 구불구불한 도로 위에 있으며 종종 최소값 지점을 향해 더 날카로운 회전을 하곤 합니다. 이러한 행동은 예상 업데이트에 의해 주도되며 Adam보다 덜 최적의 경로를 더 효과적으로 피할 모멘텀을 제공합니다. 이 능력은 Nadam이 Adam과 Nesterov 모멘텀의 강점을 결합하여 복잡한 손실 함수 랜드스케이프에 특히 적합한 강력한 옵티마이저를 형성한다는 것을 보여줍니다.

## 2: Nadam의 작동 메커니즘

Nadam은 Adam 옵티마이저의 메커니즘을 Nesterov 모멘텀과 똑똑하게 결합하여 학습을 최적화합니다. 주요 방정식을 살펴보고 Adam과 비교하여 업데이트 규칙의 차이점을 강조해보겠습니다.

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

## 2.1: 초기화

시작할 때, Nadam은 첫 번째 및 두 번째 모멘트 벡터를 초기화합니다. 이 벡터들은 기울기와 제곱 기울기의 이동 평균을 저장하는 데 중요합니다. 시간이 지남에 따라 매개 변수 업데이트를 부드럽게 합니다.

![그림](/assets/img/2024-05-20-TheMathBehindNadamOptimizer_2.png)

동시에 우리는 반복의 시작을 표시하는 초기 타임스텝을 설정합니다.

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

<img src="/assets/img/2024-05-20-TheMathBehindNadamOptimizer_3.png" />

## 2.2: 각 타임 스텝(𝑡)별 반복적 업데이트

각 반복(𝑡)에 대해, 해당 시점의 매개변수에 대한 손실 함수의 기울기(𝑔_𝑡)를 계산하는 것으로 시작합니다. 이 기울기는 함수의 손실이 가장 빠르게 증가하는 방향을 가리킵니다.

편향된 첫 번째 모멘트 추정값 업데이트

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

![매쓰사인네처날날담옵티마이저 그림 4](/assets/img/2024-05-20-TheMathBehindNadamOptimizer_4.png)

첫 번째 모멘트(𝑚_𝑡)는 과거 그래디언트의 지수적 평균이며, 이는 감쇠율(β1, 일반적으로 0.9 주변)을 사용하여 업데이트됩니다. 이 비율은 과거 그래디언트 정보 대비 새 데이터 유지 비율에 영향을 미치며, 더 높은 β1은 더 부드럽지만 더 반응이 느린 추정치로 이어집니다.

편향된 두 번째 모멘트 추정 업데이트

![매쓰사인네처날날담옵티마이저 그림 5](/assets/img/2024-05-20-TheMathBehindNadamOptimizer_5.png)

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

비슷하게, 두 번째 모멘트 (𝑣_𝑡)은 이전 그래디언트의 제곱의 지수 가중 평균을 추적합니다. 여기서 감쇠율 (β2, 일반적으로 약 0.999)은 매개변수 업데이트의 변동성에 따라 학습률을 조정하여 업데이트를 안정화하는 데 도움을 줍니다.

## 2.3: 네스테로프 모멘텀 보정

![이미지](/assets/img/2024-05-20-TheMathBehindNadamOptimizer_6.png)

여기서 Nadam은 일반적인 모멘트 계산을 수정하여 네스테로프 모멘텀을 통합합니다. 이 접근 방식은 그래디언트의 미래 위치를 예상하여 업데이트 방향을 보정합니다. 이러한 선행은 초기에 제로 초기화된 모멘트가 편향된 업데이트를 초래할 수 있는 훈련 초기에 특히 유용합니다.

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

## 2.4: 편향 보정

첫 번째 및 두 번째 모멘트 추정치의 편향을 보정하세요:

![Image](/assets/img/2024-05-20-TheMathBehindNadamOptimizer_7.png)

첫 번째와 두 번째 모멘트는 처음에는 영에서 시작하기 때문에 편향되어 있습니다. 이를 극복하기 위해 편향 보정 용어가 도입되었으며, 더 많은 반복이 완료됨에 따라 모멘트의 추정치를 점진적으로 확대하여, 추정치가 시간이 지남에 따라 보다 정확하고 진짜 기울기 정보를 반영하도록 보장합니다.

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

## 2.5: 매개변수 업데이트

![Image](/assets/img/2024-05-20-TheMathBehindNadamOptimizer_8.png)

최종 매개변수 업데이트는 기울기의 방향에 대해 조정된 적응형 학습률(𝛼)과 작은 상수(𝜖, 10^(-8)과 같은)로 수치적 안정성을 위해 이루어집니다. 수정된 첫 번째 모멘트(𝑚*𝑡)와 수정된 두 번째 모멘트의 제곱근(𝑣*𝑡)은 이러한 업데이트를 적절하게 조정하는 데 사용되며, 기울기의 방향과 크기를 모두 고려합니다.

## 2.6: Adam과의 주요 차이점

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

Nadam과 Adam 사이의 주요 차이점은 Nadam이 적극적으로 조정을 합니다. Adam은 현재 기울기 추정만을 의존하는 반면, Nadam은 Nesterov 모멘텀을 통해 앞서 보는 단계를 도입합니다. 이로 인해 미래의 기울기 방향을 예측뿐만 아니라 업데이트를 더 정확하게 조정하여 더 효율적인 학습 역학을 이끌어냅니다.

Nadam은 Nesterov 모멘텀의 통합을 통해 최적화에 대해 보다 섬세하고 선심을 기울인 접근 방식을 제공하여 Adam보다 데이터 landscape를 효과적으로 탐색하고 적응하는 능력을 향상시킵니다. 이로써 Nadam은 다양한 머신 러닝 과제에 대한 견고한 선택지가 되며, 실제 결과를 선택할 때 이론적 통찰력과 실용적 결과를 모두 고려하도록 실무자에게 요청합니다.

## 3. 실무에서 Nadam 구현

이 섹션에서는 Nadam 옵티마이저를 처음부터 구축하고 머신 러닝 환경에서 적용할 것입니다. 우리는 선형 회귀 작업에 Nadam 옵티마이저를 구현하고 사용하는 맥락에서 코드를 여러 주요 섹션으로 분할할 것입니다.

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

아래의 코드를 살펴보고 싶다면, 오늘 다룰 코드와 추가적인 보너스 콘텐츠가 모두 포함된 이 Jupyter 노트북을 살펴보는 것을 고려해보세요:

## 3.1: Nadam Optimizer Class Definition

Nadam 알고리즘을 사용하여 매개변수를 최적화하는 데 중요한 역할을 하는 NadamOptimizer 클래스를 자세히 살펴봅시다.

```js
class NadamOptimizer:
    def __init__(self, learning_rate=0.001, beta1=0.9, beta2=0.999, epsilon=1e-8):
        self.learning_rate = learning_rate
        self.beta1 = beta1
        self.beta2 = beta2
        self.epsilon = epsilon
        self.m = None
        self.v = None
        self.t = 0

    def initialize_moments(self, params):
        self.m = {k: np.zeros_like(v) for k, v in params.items()}
        self.v = {k: np.zeros_like(v) for k, v in params.items()}

    def update_params(self, params, grads):
        if self.m is None or self.v is None:
            self.initialize_moments(params)

        self.t += 1
        updated_params = {}
        mu_t = self.beta1 * (1 - 0.5 * 0.96 ** (self.t * 0.004))
        mu_t1 = self.beta1 * (1 - 0.5 * 0.96 ** ((self.t + 1) * 0.004))
        for key in params.keys():
            g_tilde = grads[key] / (1 - np.prod([self.beta1] * self.t))
            self.m[key] = self.beta1 * self.m[key] + (1 - self.beta1) * grads[key]
            self.v[key] = self.beta2 * self.v[key] + (1 - self.beta2) * np.square(grads[key])

            m_corrected = self.m[key] / (1 - mu_t1 ** self.t)
            v_corrected = self.v[key] / (1 - self.beta2 ** self.t)

            m_bar = (1 - mu_t) * g_tilde + mu_t1 * m_corrected

            updated_params[key] = params[key] - self.learning_rate * m_bar / (np.sqrt(v_corrected) + self.epsilon)

        return updated_params
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

NadamOptimizer 클래스는 Nadam 최적화 방법을 관리하고 실행하기 위해 구조화되어 있습니다. 아래는 클래스와 해당 함수들을 설명한 것입니다:

**init** 메서드

```python
class NadamOptimizer:
    def __init__(self, learning_rate=0.001, beta1=0.9, beta2=0.999, epsilon=1e-8):
        self.learning_rate = learning_rate
        self.beta1 = beta1
        self.beta2 = beta2
        self.epsilon = epsilon
        self.m = None
        self.v = None
        self.t = 0
```

초기화 메서드는 최적화기를 학습률, 베타 값, 그리고 엡실론에 대한 미리 정의된 설정으로 설정합니다. 이러한 매개변수 각각은 중요한 역할을 합니다:

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

- 학습률: 이는 손실 함수의 최소값으로 이동하는 각 반복에서의 단계 크기를 결정합니다.
- beta1 및 beta2: 이러한 매개 변수는 기울기와 해당 제곱값의 이동 평균의 감쇠율을 제어하며 업데이트를 부드럽게 만들고 학습률을 동적으로 관리하는 데 도움이 됩니다.
- epsilon: 계산 중에 0으로 나누는 것을 방지하기 위한 매우 작은 수입니다.
- self.m 및 self.v: 초기에 None으로 설정되며 나중에는 각각 기울기와 제곱 기울기의 이동 평균을 저장합니다.
- self.t: 업데이트 또는 반복 횟수를 추적하는 카운터입니다.

이 설정은 Nadam의 기본 측면과 일치하며, 적응형 학습률이 지속적으로 조정되어 수렴을 개선합니다.

initialize_moments 메서드

```js
    def initialize_moments(self, params):
        self.m = {k: np.zeros_like(v) for k, v in params.items()}
        self.v = {k: np.zeros_like(v) for k, v in params.items()}
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

이 메소드는 이동 평균을 저장하는 딕셔너리 m과 v를 초기화합니다. 각 매개변수의 기울기와 제곱 기울기가 0으로 초기화되는데, 이는 최적화 프로세스의 후속 단계에서 계산을 시작하는 데 중요합니다.

update_params 메소드

```js
    def update_params(self, params, grads):
        if self.m is None or self.v is None:
            self.initialize_moments(params)

        self.t += 1
        updated_params = {}
        mu_t = self.beta1 * (1 - 0.5 * 0.96 ** (self.t * 0.004))
        mu_t1 = self.beta1 * (1 - 0.5 * 0.96 ** ((self.t + 1) * 0.004))
        for key in params.keys():
            g_tilde = grads[key] / (1 - np.prod([self.beta1] * self.t))
            self.m[key] = self.beta1 * self.m[key] + (1 - self.beta1) * grads[key]
            self.v[key] = self.beta2 * self.v[key] + (1 - self.beta2) * np.square(grads[key])

            m_corrected = self.m[key] / (1 - mu_t1 ** self.t)
            v_corrected = self.v[key] / (1 - self.beta2 ** self.t)

            m_bar = (1 - mu_t) * g_tilde + mu_t1 * m_corrected

            updated_params[key] = params[key] - self.learning_rate * m_bar / (np.sqrt(v_corrected) + self.epsilon)

        return updated_params
```

이 함수는 Nadam 옵티마이저의 핵심입니다. 전달된 기울기를 기반으로 매개변수를 업데이트합니다. 포함된 단계는 다음과 같습니다:

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

순간 초기화 확인

```js
if self.m이 None이거나 self.v가 None이면:
            self.initialize_moments(params)
```

만약 m 또는 v가 초기화되지 않았다면 0으로 설정됩니다.

시간 단계 업데이트

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
셀프.티 += 1;
```

decay factors를 동적으로 조절하는 시간 단계 t를 증가시킵니다.

네스테로프 모멘텀 조정

```js
뮤_티 = self.beta1 * (1 - 0.5 * 0.96 ** (self.티 * 0.004));
뮤_t1 = self.beta1 * (1 - 0.5 * 0.96 ** ((self.티 + 1) * 0.004));
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

mu_t 및 mu_t1은 시간 단계의 진행을 고려하여 Nesterov 모멘텀의 효과를 반영하기 위해 beta1을 조정합니다. 이러한 조정은 모멘텀이 미래의 기울기를 더 효과적으로 반영하도록 합니다.

각 매개변수를 순회하면서:

```js
for key in params.keys():
      g_tilde = grads[key] / (1 - np.prod([self.beta1] * self.t))
      self.m[key] = self.beta1 * self.m[key] + (1 - self.beta1) * grads[key]
      self.v[key] = self.beta2 * self.v[key] + (1 - self.beta2) * np.square(grads[key])

      m_corrected = self.m[key] / (1 - mu_t1 ** self.t)
      v_corrected = self.v[key] / (1 - self.beta2 ** self.t)

      m_bar = (1 - mu_t) * g_tilde + mu_t1 * m_corrected
```

g_tilde은 과거 그래디언트의 감쇠를 현재 시간 단계까지 반영한 조정된 기울기를 계산합니다. 이는 lookahead를 포함하여 그래디언트 계산을 수정하여 Nesterov 모멘텀의 효과를 나타냅니다.

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

나중에는 새로운 기울기와 조정된 베타 값에 기초하여 각 매개변수에 대해 m과 v를 조정합니다. 이 단계는 과거 기울기의 모멘텀 이점과 새로운 기울기에 대한 적응성을 결합하는 중요한 단계입니다.

m_corrected와 v_corrected는 편향이 수정된 제1 및 제2 모먼트의 추정치를 나타냅니다. 바이어스 보정은 계산된 기울기가 더 적을 때 교육 초기에 중요합니다.

m_bar는 미래의 그래디언트와 모멘텀이 보정된 그래디언트를 결합하여 매개변수 공간에서 취해야 할 단계의 방향과 크기를 효과적으로 결정합니다.

매개변수 업데이트

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

updated_params[key] = params[key] - self.learning_rate \* m_bar / (np.sqrt(v_corrected) + self.epsilon)

모든 매개변수는 m_bar와 조정된 학습률에 의해 업데이트되며, v_corrected에 제곱근을 더한 것으로 스케일링됩니다. 이 단계는 실제 매개변수 업데이트가 발생하는 곳으로, 모델의 학습에 직접적인 영향을 미칩니다.

## 3.2: 선형 회귀 모델 클래스

이제 간단한 회귀 모델을 구축하고, Nadam 옵티마이저를 적용해 보겠습니다.

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
# 선형 회귀 모델
class LinearRegression:
    def __init__(self, n_features):
        self.weights = np.random.randn(n_features)
        self.bias = np.random.randn()

    def predict(self, X):
        return np.dot(X, self.weights) + self.bias
```

여기서 **init** 메서드는 features 수에 기반하여 랜덤한 weights와 biases로 모델을 초기화합니다.

그런 다음 predict 메서드는 입력과 weights 및 bias의 내적을 사용하여 예측을 계산합니다.

여기서 선형 회귀는 Nadam이 어떻게 작동하는지 이해하는 간단한 방법을 제공하지만, 실제로는 더 복잡한 딥러닝 모델에서 Nadam을 사용하려고 할 것입니다. 그렇다면, 가장 인기 있는 딥러닝 모델 중 일부를 포괄적으로 이해할 수 있는 다음의 글을 살펴보고 그 코드를 수정하여 Nadam을 구현해보기를 강력히 추천합니다:

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

## 3.3: Model Trainer Class

앞으로 나아가봅시다. 이제는 지정된 최적화 도구를 사용하여 머신 러닝 모델을 훈련시키는 전체 과정을 캡슐화하는 클래스가 필요합니다. 이것이 바로 ModelTrainer 클래스가 할 일입니다.

```js
class ModelTrainer:
    def __init__(self, model, optimizer, n_epochs):
        self.model = model
        self.optimizer = optimizer
        self.n_epochs = n_epochs

    def compute_gradients(self, X, y):
        predictions = self.model.predict(X)
        errors = predictions - y
        dW = 2 * np.dot(X.T, errors) / len(y)
        db = 2 * np.mean(errors)
        return {'weights': dW, 'bias': db}

    def train(self, X, y, verbose=False):
        for epoch in range(self.n_epochs):
            grads = self.compute_gradients(X, y)
            params = {'weights': self.model.weights, 'bias': self.model.bias}
            updated_params = self.optimizer.update_params(params, grads)

            self.model.weights = updated_params['weights']
            self.model.bias = updated_params['bias']

            # Optionally, print loss here to observe training
            loss = np.mean((self.model.predict(X) - y) ** 2)
            if epoch % 1000 == 0 and verbose:
                print(f"Epoch {epoch}, Loss: {loss}")
```

**init** 메서드

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
    def __init__(self, model, optimizer, n_epochs):
        self.model = model
        self.optimizer = optimizer
        self.n_epochs = n_epochs
```

이 생성자 메서드는 세 가지 주요 구성 요소로 트레이너를 초기화합니다:

- model: 훈련될 머신러닝 모델이며, 예측을 수행하는 메서드와 매개변수(가중치 및 편향)에 대한 속성이 있어야 합니다.
- optimizer: Nadam과 같은 옵티마이저 클래스의 인스턴스로, 계산된 그래디언트에 기초하여 모델의 매개변수를 업데이트하는 역할을 담당합니다.
- n_epochs: 훈련 프로세스가 실행할 훈련 데이터 세트를 전체적으로 순회하는 횟수입니다.

이러한 구성 요소는 손실 함수를 최소화하기 위해 모델의 매개변수가 반복적으로 업데이트되는 훈련 프로세스를 설정하는 데 필수적입니다.

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

compute_gradients 메서드

```python
def compute_gradients(self, X, y):
    predictions = self.model.predict(X)
    errors = predictions - y
    dW = 2 * np.dot(X.T, errors) / len(y)
    db = 2 * np.mean(errors)
    return {'weights': dW, 'bias': db}
```

이 메서드는 모델 매개변수에 대한 손실 함수의 그래디언트를 계산합니다:

```python
predictions = self.model.predict(X)
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

다음은 모델이 현재 매개변수를 사용하여 입력 X를 기반으로 출력을 예측하는 부분입니다.

```js
오류 = 예측 - y;
```

이 코드 라인은 예측된 출력과 실제 출력인 y와의 차이, 즉 오차를 나타냅니다.

```js
dW = (2 * np.dot(X.T, 오류)) / len(y);
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

이것은 가중치의 기울기를 계산합니다. 가중치에 대한 평균 제곱 오차 손실 함수의 도함수는 len(y)의 데이터 포인트에 의해 조정된 표현으로 주어집니다. 이는 모든 특성과 데이터 포인트에 대한 기울기를 효율적으로 계산하는 벡터화된 구현입니다.

```js
db = 2 * np.mean(errors);
```

마찬가지로, 이는 편향의 기울기를 계산하며, 이는 단순히 오차의 평균에 2를 곱한 것입니다.

```js
return { weights: dW, bias: db };
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

그러면 메서드는 이러한 그래디언트를 딕셔너리로 반환하여 모델의 매개변수를 업데이트하는 데 사용할 수 있도록 합니다. 이 메서드는 옵티마이저가 손실을 최소화하기 위해 매개변수를 조정해야 하는 필수적인 기울기 계산 단계를 캡슐화합니다.

train 메서드

```python
    def train(self, X, y, verbose=False):
        for epoch in range(self.n_epochs):
            grads = self.compute_gradients(X, y)
            params = {'weights': self.model.weights, 'bias': self.model.bias}
            updated_params = self.optimizer.update_params(params, grads)

            self.model.weights = updated_params['weights']
            self.model.bias = updated_params['bias']

            # 선택사항: 여기서 손실을 출력하여 학습을 관찰합니다
            loss = np.mean((self.model.predict(X) - y) ** 2)
            if epoch % 1000 == 0 and verbose:
                print(f"Epoch {epoch}, Loss: {loss}")
```

이 메서드는 각 epoch(데이터를 완전히 통과하는 단계)마다 n_epochs에 이르기까지 반복됩니다. 루프 내에서 현재 매개변수와 데이터 집합에 대해 compute_gradients를 사용하여 그래디언트를 먼저 계산합니다.

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

그런 다음 이러한 그래디언트를 현재 매개변수와 함께 옵티마이저(self.optimizer.update_params(params, grads))에 전달하고, 옵티마이저는 최적화 알고리즘(예: Nadam)을 기반으로 업데이트된 매개변수를 반환합니다.

모델의 매개변수는 이러한 새 값으로 업데이트되어 손실을 최소화하는 상태로 움직이게 됩니다.

verbose가 True로 설정되어 있는 경우, 메서드는 1000번의 epoch마다 손실을 인쇄하여 교육 진행 상황을 모니터링합니다. 손실은 예측 값과 실제 출력 사이의 평균 제곱 오차로 계산되며, 모델의 성능을 얼마나 잘 평가하고 있는지에 대한 간단한 지표를 제공합니다.

train 메서드는 따라서 전체 교육 과정을 조정하며, 손실 함수의 피드백에 기초하여 옵티마이저를 통해 모델의 매개변수를 반복적으로 조정합니다. 이 메서드는 머신러닝에서 사용되는 반복적 최적화 기법의 실용적 구현이며, 그래디언트 하강과 매개변수 업데이트의 이론적 원리를 직접 적용하여 여러 번의 반복을 통해 미리 정의된 손실 함수를 최소화합니다.

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

## 3.4: 데이터셋 처리 및 모델 훈련

마지막으로, Nadam 옵티마이저를 사용하여 머신러닝 모델을 설정, 훈련 및 평가해 봅시다. 이 접근 방식은 데이터 조작, Optuna를 사용한 하이퍼파라미터 튜닝, 그리고 모델의 효과를 개선하고 평가하기 위한 반복적인 훈련 및 테스트를 포함합니다.

### 3.4.1: 데이터 준비

```python
# 입력 피처(X) 및 타겟 값(y) 가져오기
X = diabetes.data
y = diabetes.target
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

우선, 당뇨 데이터셋에서 피처 데이터(X)와 타겟값(y)을 불러옵니다. 이 데이터셋은 sci-kit learn에서 가져오며 상업적 이용이 가능합니다. (Scikit-learn: Python의 머신 러닝, Pedregosa et al., JMLR 12, pp. 2825–2830, 2011.)

```js
# 데이터셋을 훈련 세트와 테스트 세트로 나눕니다
def split_dataset(X, y, test_ratio=0.2):
    indices = np.random.permutation(len(X))
    test_size = int(len(X) * test_ratio)
    test_indices = indices[:test_size]
    train_indices = indices[test_size:]
    return X[train_indices], X[test_indices], y[train_indices], y[test_indices]

X_train, X_test, y_train, y_test = split_dataset(X, y)
X_train, X_val, y_train, y_val = split_dataset(X_train, y_train)
```

split_dataset 함수는 지정된 비율에 따라 데이터셋을 훈련 세트와 테스트 세트로 무작위로 분할합니다. 데이터셋 인덱스를 섞어 분할을 무작위로 다양하게 만들어 모델 평가를 견고하게 합니다. 훈련 세트는 모델 매개변수를 학습하는 데 사용되고, 테스트 세트는 모델이 보지 못한 데이터에서 얼마나 잘 수행되는지를 평가합니다.

3.4.2: Optuna을 이용한 하이퍼파라미터 튜닝
우리는 Optuna를 사용하여 모델의 하이퍼파라미터를 최적화할 것이며, 이는 훈련 동적과 Nadam 옵티마이저의 효과에 상당한 영향을 미칩니다.

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

다른 기사에서 우리는 옵투나(optuna)를 광범위하게 다루었습니다. 거기에서는 이를 신경망에 적용하여 성공적으로 세밀하게 조정하는 방법을 보여주었습니다. 만약 Nadam을 더 복잡한 모델에 적용하고 싶다면, 이 기사를 읽어보시면 좋을 것 같아요:

목적 함수 정의

```python
def objective(trial):
    n_features = X_train.shape[1]

    learning_rate = trial.suggest_loguniform('learning_rate', 1e-5, 1e-1)
    beta1 = trial.suggest_uniform('beta1', 0.9, 0.999)
    beta2 = trial.suggest_uniform('beta2', 0.99, 0.9999)
    epsilon = trial.suggest_loguniform('epsilon', 1e-10, 1e-5)

    n_epochs = trial.suggest_int('epochs', 1000, 100000)

    # Define the model
    model = LinearRegression(n_features)
    optimizer = NadamOptimizer(learning_rate=learning_rate, beta1=beta1, beta2=beta2, epsilon=epsilon)
    trainer = ModelTrainer(model, optimizer, n_epochs=n_epochs)

    # Train the model
    trainer.train(X_train, y_train, verbose=False)

    # Compute the validation loss
    val_loss = np.mean((model.predict(X_val) - y_val) ** 2)

    return val_loss
```

우리의 목적 함수는 옵투나(optuna)의 최적화 과정을 안내하는 중요한 역할을 합니다. 각 시도는 다음 값을 제안합니다:

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

- 학습률: 매개변수 업데이트의 단계 크기에 영향을 줍니다.
- beta1 및 beta2: 그래디언트 및 그들의 제곱값에 대한 평균의 감쇠율을 조절합니다.
- epsilon: 업데이트 중에 0으로 나누는 것을 방지하기 위해 작은 값이 추가됩니다.

각 시도는 이러한 매개변수를 사용하여 LinearRegression 모델과 NadamOptimizer를 설정하고, ModelTrainer를 사용하여 모델을 훈련합니다. 훈련 후, 검증 세트에서의 검증 손실을 계산하여 하이퍼파라미터의 효과를 Optuna에 제공합니다.

```js
# 연구 객체 생성
optuna.logging.set_verbosity(optuna.logging.WARNING)
study = optuna.create_study(direction='minimize', sampler=optuna.samplers.TPESampler(seed=42))

# 연구 개선, 더 많은 시행을 사용하여 더 나은 결과를 얻거나, 더 적은 시행을 사용하여 더 비용 효율적일 수 있습니다
study.optimize(objective, n_trials=10)
```

Optuna가 최적의 매개변수를 식별한 후, 최종 모델이 구성되고 훈련됩니다. 이 단계에서는 테스트 세트에서 모델의 일반화 능력을 평가합니다.

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

훈련 및 평가

```js
# 최적 모델 가져오기
n_features = X_train.shape[1]
best_model = LinearRegression(n_features)
optimizer = NadamOptimizer(learning_rate=study.best_params['learning_rate'],
                          beta1=study.best_params['beta1'],
                          beta2=study.best_params['beta2'],
                          epsilon=study.best_params['epsilon'])

# 모델 훈련하기
trainer = ModelTrainer(best_model, optimizer, n_epochs=study.best_params['epochs'])
trainer.train(X_train, y_train)
```

Optuna의 최적 매개변수를 사용하여 모델은 추가 훈련을 받습니다. 이 추가 훈련을 통해 모델이 데이터에 완전히 적응하도록 보장합니다.

```js
# 테스트 손실 계산하기
test_loss = np.mean((best_model.predict(X_test) - y_test) ** 2)**0.5
print(f'테스트 손실: {test_loss:.2f}')
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

마침내, 새로운, 보지 못했던 데이터에 대한 모델의 예측 정확도를 평가하기 위해 시험 손실을 계산합니다. 이 측정값은 모델의 실용적 성능을 평가하는 데 중요합니다.

# 4. 장점과 고려 사항

## 4.1 Nadam이 뛰어난 점

Nadam은 Adam을 바탕으로 네스테로프 모멘텀을 통합하여, 그레이디언트가 믿을 수 없거나 에포크 간 크게 다를 수 있는 복잡한 최적화 작업을 처리할 수 있는 능력을 향상시킵니다. 여기에서 Nadam이 빛을 발합니다:

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

딥 뉴럴 네트워크: Nadam은 수렴 속도가 중요한 딥 뉴럴 네트워크의 학습에서 뛰어납니다. 미래를 예측하는 기능이 수렴 속도를 높이는 데 도움을 주어 모델이 최적이 아닌 해결책에 갇히지 않도록 합니다.

희소한 데이터: 텍스트나 대규모 범주형 데이터와 같은 많은 영 피처를 포함한 데이터셋에 대해서는 Nadam이 매개변수 업데이트를 더 효과적으로 조정하여 희소한 정보를 더 잘 관리합니다.

잡음이 많은 데이터: 실시간 스트림이나 온라인 학습과 같이 잡음이 많은 데이터 환경에서는 Nadam의 변동 데이터 처리 및 적응형 학습률 조정이 특히 유용합니다.

## 4.2 한계와 도전과제

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

그 강점에도 불구하고, Nadam은 항상 최적의 선택이 되지는 않습니다:

간단한 문제: 오차 함수가 잘 행동하고 국소 최솟값이 적은 간단한 작업의 경우, SGD와 같은 간단한 옵티마이저가 연산 요구가 적어 더 효율적일 수 있습니다.

메모리 집약적 모델: Nadam은 그래디언트와 제곱 그래디언트의 모멘트 추정 값을 저장해야 하므로, 더 간단한 방법과 비교하여 메모리 사용량이 증가합니다. 이는 메모리 제한 환경에서 문제가 될 수 있습니다.

하이퍼파라미터 민감도: Nadam의 성능은 𝛽1, 𝛽2 및 학습률과 같은 하이퍼파라미터의 설정에 민감합니다. 올바른 조정을 위해 포괄적인 테스트 또는 하이퍼파라미터 최적화 도구가 필요할 수 있으며, 최적의 성능을 위해 필수적입니다.

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

Nadam을 탐색하는 것은 Nesterov 운동량과 Adam의 적응형 모멘트 추정을 결합하여 복잡한 머신 러닝 모델을 최적화하는데 중요한 발전을 나타냅니다. Nadam은 Adam에 선행 지식을 추가하여 매개변수 업데이트를 개선하고 수렴 속도를 향상시키며 다양한 어려운 데이터 환경에서 학습 과정을 안정화시킵니다.

Nadam은 최적화 알고리즘 분야에서 상당한 향상을 제공하지만, Adam보다 선택하는 것은 머신 러닝 프로젝트의 특정 요구 사항과 제약 사항을 신중히 고려해야 합니다. 최적화 기술의 지속적인 진화는 능력을 향상시키고 새로운 가능성을 열어주며, 더 많은 연구와 실험을 위한 활기찬 분야로 만들어 냅니다.

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

- Dozat, T. (2016). “Incorporating Nesterov Momentum into Adam.” ICLR Workshop.
- Kingma, D. P., & Ba, J. (2014). “Adam: A Method for Stochastic Optimization.” arXiv preprint arXiv:1412.6980.
- Nesterov, Y. (1983). “A Method for Unconstrained Convex Minimization Problem with the Rate of Convergence 𝑂(1/𝑘2)O(1/k2).” Doklady AN USSR.
- Ruder, S. (2016). “An overview of gradient descent optimization algorithms.” arXiv preprint arXiv:1609.04747.
- Bottou, L., Curtis, F. E., & Nocedal, J. (2018). “Optimization Methods for Large-Scale Machine Learning.” SIAM Review, 60(2), 223–311.
- Zhang, M. R., Lucas, J., Ba, J., & Hinton, G. E. (2019). “Lookahead Optimizer: k steps forward, 1 step back.” arXiv preprint arXiv:1907.08610.

당신은 끝까지 왔습니다. 축하해요! 이 기사를 즐겼다면 좋아요를 누르고 저를 팔로우해주시면 감사하겠습니다. 저는 주기적으로 비슷한 기사를 게시할 것이기 때문에, 재미있게 보아 주실 것을 희망합니다. 제 목표는 가장 인기 있는 알고리즘을 모두 처음부터 다시 만들어 기계 학습을 모두에게 접근 가능하게 하는 것입니다.
