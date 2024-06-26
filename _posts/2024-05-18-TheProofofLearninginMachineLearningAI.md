---
title: "기계 학습 AI에서의 학습 증명"
description: ""
coverImage: "/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_0.png"
date: 2024-05-18 20:13
ogImage:
  url: /assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_0.png
tag: Tech
originalTitle: "The Proof of Learning in Machine Learning AI"
link: "https://medium.com/towards-data-science/the-proof-of-learning-in-machine-learning-ai-4faae3c85fe6"
---

## 수학적인 개발을 하기 전에, 먼저 학습의 기초를 이해하고 이것이 오류 개념과 얼마나 밀접하게 관련되어 있는지 알아야 합니다.

# 가상의 요리사

어느 날, 유명한 레스토랑에서 먹은 특별 요리를 복제하기로 결정했다고 상상해보세요. 그 특별 요리의 맛을 완벽하게 기억합니다. 이를 기반으로 온라인에서 레시피를 찾아 집에서 재현하려고 노력합니다.

레스토랑에서 먹은 특별 요리의 맛을 T로 표시하겠습니다. 이는 기대되는 맛, 즉 당신의 목표를 나타냅니다. 온라인에서 찾은 레시피를 토대로 이 목표, 즉 맛 T를 달성하기를 희망합니다.

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

이 레시피를 재현하려면 지시된 모든 단계를 따라야 하고 모든 재료와 필요한 온도, 조리 시간 등을 사용해야 합니다. 이 모든 방법과 재료를 X로 표기해봅시다.

전체 과정을 완료한 후 요리를 맛보게 됩니다. 이 때, 예상하는 맛 T와 유사한지 판단하게 됩니다. 예상보다 더 짠지 달콤한지에 대한 판단을 하게 되죠. 집에서 재현한 요리의 맛은 Y로 표현됩니다.

따라서, 목표인 T와 다른 맛을 느꼈을 때, 맛 Y를 기반으로 목표 맛과 얼마나 다른지를 양적으로 평가합니다. 다시 말해 더 많은 소금을 넣었을 수도 있고, 더 적게 향신료를 넣었을 수도 있습니다.

T와 Y 간의 차이를 오차 E로 정의할 수 있습니다. T와 Y의 차이는 여러분의 입맛 기억을 통해 이루어지죠. 따라서 이 순간 여러분의 입맛은 특정 기능을 수행하며, 이를 P(Y) = E로 정의할 수 있습니다. 다시 말해 맛 Y를 경험할 때, 입맛은 목표 맛 T를 기준으로 오차를 할당합니다.

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

Quantitative measure of error E가 있으면 매일 동일한 레시피를 복제하여 매일매일 오차 E가 줄어듭니다. 다른 말로, 목표하는 맛 T와 실제 맛 Y 사이의 거리가 T = Y가 될 때까지 줄어듭니다.

이 가상 시나리오를 기반으로하면, 오류를 관찰된 현실과 다르게 판단하는 것으로 정의할 수 있으며, 항상 판단 작업을 수행하는 기능이 있습니다. 따라서 위의 경우에는 맛과 기억이 이 판단 기능을 만들었습니다.

특정한 경우에서의 학습 행위는 주로 오류를 줄이는 능력으로 특징 지어집니다. 다시 말해, 이는 판단 함수의 출력을 줄이기 위해 복제 된 객체와 다양한 방식으로 상호 작용하는 능력입니다.

# 요리사의 전문지식

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

가정의 경우로 돌아와서, 우리는 레시피에 나와 있는 재료와 방법 X를 가지고 있습니다. 모든 재료와 장비는 레스토랑에서 사용된 것과 동일하며, 결과는 목표하는 맛 T를 달성하기 위해 그것들을 올바르게 다룰 수 있는 당신의 능력에만 달려 있습니다.

다시 말해서, 당신은 X를 조작하여 Y를 얻는 것입니다. 따라서 당신이 본질적으로 X를 Y로 변환하는 함수라고 정의할 수 있습니다. 이를 f(X) = Y로 표기합니다.

재료를 다루는 행위를 나타내는 함수 f(X)는 또한 당신의 뇌가 어떻게 작용하는지에 따라 달라집니다. 다시 말해, 만약 당신이 요리 경험이 있다면, X를 Y로 변환하는 것이 더 쉬울 것입니다.

이제 우리는 당신의 뉴런의 가중치 W 또는 X를 다루는 신경 능력을 정의해 봅시다. W가 요리 경험에 기반하여 이미 사전에 조정되어 있다면, X를 Y로 변환하는 것이 더 쉬울 것입니다. 그렇지 않으면, X를 Y로 변환할 수 있을 때까지 W를 조정해야 할 것입니다.

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

따라서 우리는 f(X) = Y가 W에도 의존한다는 것을 알 수 있습니다, 즉 우리는 f(X) = WX로 선형적으로 표현할 수 있습니다.

그래서, 우리의 목표는 생성된 Y가 매우 가깝거나 T와 동일해질 때까지 W를 어떻게 수정할 수 있는지 알아내는 것입니다. 다시 말해, 오차 E가 크게 감소하거나 0이 될 때까지 W를 어떻게 조절할 수 있는지 입니다.

# 비용 함수

결과와 기대 결과 간의 차이를 평가하는 함수가 비용 함수입니다. 재료와 조리 방법을 솜씨 좋게 바꾸는 함수가 바로 우리의 모델인데요, 이는 인공 신경망 또는 다른 머신러닝 모델일 수 있습니다.

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

![The Proof of Learning in Machine Learning AI 0](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_0.png)

In equation (1), the definition of the cost function E, which depends on the n weights w. In other words, it is a function that indicates the error based on the values of w. In a specific case where all n weights w are not adjusted, the value of the error E will be large. Conversely, in a case where the weights are properly adjusted, the value of the error E will be small or zero.

![The Proof of Learning in Machine Learning AI 1](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_1.png)

Therefore, our objective is to find the values of the n weights w such that the condition above is true.

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

# 그래디언트

이 작업을 수행하는 방법을 이해하는 데 도움이 되도록 다음 함수를 정의합니다:

![function](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_2.png)

따라서 x = 0이고 y = 0일 때 f(x, y) = 0임은 직관적으로 알 수 있습니다. 그러나 우리는 무작위로 선택된 x와 y 값이 주어졌을 때, x와 y의 값을 조정하여 함수 f(x, y)가 0이 되도록 하는 알고리즘을 원합니다.

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

위 작업을 수행하기 위해 함수의 그래디언트를 사용할 수 있습니다. 벡터 미적분학에서 그래디언트는 특정 지점으로부터 변위함으로써 어떤 양의 값을 최대로 증가시킬 수 있는 방향과 크기를 나타내는 벡터입니다.

![이미지](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_3.png)

즉, 함수 f(x, y)에 그래디언트를 적용하면 식(3)에 나타난 대로 x와 y의 값을 어떻게 증가시켜야 f(x, y)의 값이 증가하는지 알려주는 벡터를 얻을 수 있습니다. 그러나 우리의 목표는 함수 f(x, y) = 0의 값을 찾는 것입니다. 따라서 우리는 음의 그래디언트를 사용할 수 있습니다.

아래는 함수 f(x, y)의 두 차원 표현이며 색상이 z의 값을 보여줍니다. 음의 그래디언트를 사용하여 함수의 최솟값을 가리키는 벡터들을 볼 수 있습니다.

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

![image](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_4.png)

이에 따라 f(x, y) 함수의 그래디언트 필드를 사용하여 x와 y를 업데이트하는 방법을 개발할 수 있습니다. 이를 통해 f(x, y) = 0을 찾는 데 필요한 값을 찾을 수 있습니다.

# 학습의 증명

알고리즘 테스트를 위한 간단한 함수 f(x)를 정의하겠습니다. 저희의 의도는 이 함수의 최솟값을 찾는 것입니다. 이를 위해 f(x)의 그래디언트를 적용할 수 있습니다.

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

아래는 함수 f(x)의 기울기입니다. 이 글에서는 미분의 개념을 깊게 다루지는 않겠지만, 이렇게 표현할 수 있는 이유에 대해 정의와 함께 읽는 것을 추천합니다.

h가 0에 수렴한다는 것을 알 때, f(x)의 기울기를 다음과 같이 표현할 수 있습니다:

![image](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_6.png)

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

아래와 같이 h를 다음 용어로 대체할 수 있습니다:

![image](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_7.png)

우리는 요소 알파를 정의하여 용어 h의 필요성을 유지합니다. 이때 알파는 엄격히 양수이어야 하며 항상 영에 수렴하여 h와 동일해야 합니다. 새로운 관계를 도함수의 정의식에 대입하면 다음과 같습니다:

![image](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_8.png)

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

우리는 증명을 위해 소중한 관계를 가지고 있습니다. 우리는 모든 요소를 제곱하면 양수가 될 것을 알고 있습니다. 이 개념에서 h를 f(x)의 gradient의 음수 알파로 대체해야 합니다.

그러므로:

![image](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_9.png)

따라서, 알파가 항상 양의 값을 갖는 한 (8)의 조건이 참임을 판단할 수 있습니다.

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

<img src="/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_10.png" />

즉, x의 f(x) 값이 엄격히 양수인 값으로 뺀 값은 항상 f(x)의 원래 값보다 작을 것입니다. 따라서, 우리는 다음과 같은 관계로 대체할 수 있습니다. eq. (7)과 (9)를 사용하여:

<img src="/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_11.png" />

따라서, 우리는 x의 값들을 업데이트하는 방법에 대한 증명된 관계가 있으며, 함수 f(x)가 이전 상태보다 적어도 작아지도록 할 수 있습니다.

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

<table>
  <tr>
    <td><img src="/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_12.png" /></td>
  </tr>
</table>

이제 현재 x를 감소시켜 부등식 (11)을 만족시키는 방법을 알게 되었습니다:

<table>
  <tr>
    <td><img src="/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_13.png" /></td>
  </tr>
</table>

이 관계가 유효한지 확인하려면 우리가 알고 있는 동작을 가진 img. (1) 함수 f(x, y)에이 방법론을 적용할 수 있습니다. 그래서:

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

![image](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_14.png)

이 알고리즘을 함수 f(x, y)에 여러 번 적용하면 함수의 값이 최소값에 도달할 때까지 감소할 것으로 예상됩니다. 이를 위해 우리는 업데이트된 x와 y의 할당에 노이즈를 적용하여 f(x, y)의 값이 감소하는 것을 시각화한 시뮬레이션을 진행했습니다.

![image](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_15.png)

알파 값이 0에 가까워질수록 x와 y의 값이 함수의 최솟값에 수렴하는 것을 관찰할 수 있습니다. 이러한 경우가 아닌 경우, 예를 들어 알파 = 0.6인 경우, 함수 f(x, y)의 최솟값을 찾는 데 어려움이 있는 것을 관찰할 수 있습니다.

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

# 그라디언트 하강

이 알고리즘은 "그라디언트 하강" 또는 "가파른 하강 방법"으로 알려져 있으며, 각 단계가 음의 그라디언트 방향으로 이루어지는 함수의 최솟값을 찾기 위한 최적화 방법입니다. 이 방법은 함수의 전역 최솟값을 찾을 수 있다는 것을 보장하지는 않지만, 지역 최솟값을 찾을 수 있습니다.

전역 최솟값을 찾는 데 대한 논의는 다른 문서에서 할 수 있지만, 여기서는 그래디언트가 이러한 목적으로 사용될 수 있는 수학적 방법을 설명했습니다.

이제 이를 가중치 w에 의존하는 비용 함수 E에 적용해 보겠습니다:

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

<img src="/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_16.png" />

W를 기울기 하강법에 따라 모든 요소를 업데이트하려면 다음과 같이 합니다:

<img src="/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_17.png" />

그리고 W 벡터의 모든 n번째 요소 𝑤에 대해서 다음과 같이 표시됩니다:

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

![이미지](/assets/img/2024-05-18-TheProofofLearninginMachineLearningAI_18.png)

따라서, 우리는 이론적 학습 알고리즘을 갖고 있습니다. 논리적으로, 이는 요리사의 가상 아이디어에는 적용되지 않고 오늘날 우리가 알고 있는 다양한 머신 러닝 알고리즘에 적용됩니다.

# 결론

우리가 본 것을 바탕으로, 우리는 이론적 학습 알고리즘의 시연과 수학적 증명을 도출할 수 있습니다. 이러한 구조는 AdaGrad, Adam 및 확률적 경사 하강법 (SGD)과 같은 다양한 학습 방법에 적용됩니다.

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

이 방법은 비용 함수가 0 또는 매우 가까운 결과를 반환하는 n-가중치 w를 찾는 것을 보장하지는 않습니다. 그러나 비용 함수의 지역 최소값을 찾을 것을 보증합니다.

지역 최소값 문제를 해결하기 위해 SGD와 Adam과 같은 더 견고한 방법이 사용되고 있습니다. 이러한 방법들은 딥러닝에서 흔히 사용됩니다.

그럼에도 불구하고, 경사 하강법에 기반한 이론적 학습 알고리즘의 구조와 수학적 증명을 이해하면 더 복잡한 알고리즘의 이해에 도움이 될 것입니다.

## 참고문헌

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

Carreira-Perpinan, M. A., & Hinton, G. E. (2005). Contrastive divergence 학습에 대해. R. G. Cowell & Z. Ghahramani 편 (공저). 2005 인공지능과 통계. 33–41쪽. Fort Lauderdale, FL: Society for Artificial Intelligence and Statistics.

García Cabello, J. 수학적 신경망. Axioms 2022, 11, 80.

Geoffrey E. Hinton, Simon Osindero, Yee-Whye Teh. Deep Belief Nets를 위한 빠른 학습 알고리즘. Neural Computation 18, 1527–1554. Massachusetts Institute of Technology.

LeCun, Y., Bottou, L., & Haffner, P. (1998). 문서 인식에 적용된 Gradient-based 학습. IEEE 학회 논문집, 86(11), 2278–2324.
