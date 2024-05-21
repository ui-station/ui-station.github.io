---
title: "시간을 통해 전파하는 역전파  RNN이 학습하는 방법"
description: ""
coverImage: "/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_0.png"
date: 2024-05-18 19:38
ogImage: 
  url: /assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_0.png
tag: Tech
originalTitle: "Backpropagation Through Time — How RNNs Learn"
link: "https://medium.com/towards-data-science/backpropagation-through-time-how-rnns-learn-e5bc03ad1f0a"
---



![RNN](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_0.png)

순환 신경망(RNN)은 시계열 및 자연어와 같은 순차 데이터를 처리하는 정규 피드포워드 신경망 변형입니다.

과거 입력 및 출력에서 다음 단계로 정보를 전달할 수 있도록 "순환" 뉴런을 추가하여 이를 달성합니다. 아래 다이어그램은 전통적인 RNN을 보여줍니다:

![RNN Diagram](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_1.png)


<div class="content-ad"></div>

왼쪽에는 반복 뉴런이 있고, 오른쪽에는 시간을 거듭할수록 펼쳐진 반복 뉴런이 있습니다. 이전 실행이 이어지는 계산에 전달되는 방식을 주목해주세요.

이것은 시스템에 어느정도의 "기억력"을 추가하여 모델이 이전 시간에 발생한 역사적 패턴을 잡는 데 도움이 됩니다.

Y_1을 예측할 때, 반복 뉴런은 X_1의 입력과 이전 시간 단계의 출력인 Y_0을 사용합니다. 이는 Y_0이 Y_1에 직접적인 영향을 미치고, 이는 Y_2에 간접적으로 영향을 미침을 의미합니다.

RNN에 대한 완벽한 소개와 몇 가지 실습 예제를 원하신다면, 이전 포스트를 확인해보세요.

<div class="content-ad"></div>

그러나 이 기사에서는 RNN이 어떻게 학습하는지를 뒷방향 시간으로 이해하자구! 

# 역전파란?

BPTT에 대해 들어가기 전에, 일반적인 역전파를 다시 확인하는 것이 중요하다. 역전파는 일반적인 피드포워드 신경망을 훈련하는 데 사용되는 알고리즘이야.

역전파의 본질은 손실 함수를 기반으로 신경망의 각 매개변수를 조정하여 오차를 최소화하려는 것이야. 이 조정은 편도함수와 연쇄법칙을 사용해 이루어져.

<div class="content-ad"></div>

compute 그래프를 통해 간단한 예제를 보여드릴게요. compute 그래프는 신경망과 매우 닮은데요.

다음 함수를 살펴보세요:

![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_2.png)

이것을 compute 그래프로 그릴 수 있습니다. 이는 함수를 시각화하는 방법일 뿐이에요:

<div class="content-ad"></div>

`<table>` 태그를 마크다운 형식으로 변경해 주세요.

<div class="content-ad"></div>

우리는 먼저 p=x-y 및 f=pz에 대한 편미분을 계산할 수 있습니다:

![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_5.png)

하지만, 어떻게 얻을까요?

![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_6.png)

<div class="content-ad"></div>

요기서는 chain rule을 사용해요! x에 대한 예시가 있어요:

![chain rule example](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_7.png)

서로 다른 편미분을 결합하면 원하는 표현을 얻을 수 있어요. 그래서 위 예시에서:

![partial derivatives example](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_8.png)

<div class="content-ad"></div>

f에 대한 x의 출력 그라디언트는 z입니다. 이게 말이 되지요. z는 x를 곱하는 유일한 값이기 때문이죠.

y와 z에 대해서도 반복합니다:

![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_9.png)

이러한 그라디언트들과 그에 해당하는 값들을 계산 그래프에 적어볼 수 있습니다:

<div class="content-ad"></div>


![Gradient Descent Image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_10.png)

Gradient descent works by updating the values (x, y, z) by a small amount in the opposite direction of the gradient. The goal of gradient descent is to try and minimize the output function. For example, for x:

![Equation for x](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_11.png)

Where h is called the learning rate, it decides how much the parameter will get updated. For this case, let’s define h=0.1, so x=3.7.


<div class="content-ad"></div>

지금 출력 결과는 무엇인가요?

![이미지](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_12.png)

결과가 작아졌죠. 다시 말해, 최소화 중이에요!

이것이 역전파가 어떻게 작동하는지에 대한 직관을 제공해줬으면 좋겠어요. 기본적으로 그것은 그라디언트 강하와 같지만, 연쇄 법칙을 사용하여 상류 그라디언트를 전달한다는 거죠.

<div class="content-ad"></div>

백프로패게이션에 대한 전문 기사가 있어요. 더 읽고 싶으시면 참고하세요.

# 시간을 통한 역전파란?

## 개요

우리는 방금 백프로패게이션이 그래디언트 강하법이라는 것을 보았습니다. 그러나 우리는 네트워크 층마다 오류(도함수)를 역방향으로 전파하고 있습니다.

<div class="content-ad"></div>

BPTT은 각 시점에서 역전파를 수행하여 이러한 정의를 확장합니다. 예제를 함께 살펴보겠습니다.

![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_13.png)

다음 다이어그램에서:

- Y는 출력 벡터입니다.
- X는 피처의 입력 벡터입니다.
- h는 숨겨진 상태입니다.
- V는 출력을 위한 가중치 행렬입니다.
- U는 입력을 위한 가중치 행렬입니다.
- W는 숨겨진 상태를 위한 가중치 행렬입니다.

<div class="content-ad"></div>

어떤 시간 t에서 다음은 계산된 출력입니다:

![2024-05-18-BackpropagationThroughTimeHowRNNsLearn_14.png](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_14.png)

여기서 σ는 일반적으로 tanh 또는 sigmoid인 활성화 함수입니다.

우리의 손실 함수가 평균 제곱 오차인 경우를 가정해 봅시다:

<div class="content-ad"></div>


![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_15.png)

A_t is the actual value that we want our prediction to equal.

## Backpropagation Through Time

Now, we are in a position to start doing BPTT after this problem has been set up.


<div class="content-ad"></div>

백프로파게이션의 목표는 모델의 가중치와 매개변수를 조정하여 오차를 최소화하는 것입니다. 이것은 가중치와 매개변수에 대한 오차의 편미분을 통해 수행됩니다.

시간 단계 3의 업데이트를 계산해 봅시다.

V 가중치 행렬에 대해:

![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_16.png)

<div class="content-ad"></div>

요번은 꽤 간단해요. E_3은 Y_3의 함수에요. 그래서 Y_3에 대해 E_3을 미분하고, Y_3을 V에 대해 미분해요. 여기서는 너무 복잡한 일은 없어요.

W 가중 행렬에 대해:

![이미지](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_17.png)

이제 조금 멋진 것들이 나타나네요!

<div class="content-ad"></div>

위 식에서 첫 번째 용어는 비교적 간단합니다. E_3는 Y_3의 함수이며, Y_3는 h_3의 함수이며, h_3는 W의 요소입니다. V 행렬에서 보았던 것과 동일한 프로세스입니다.

그러나 h_2와 h_1에 대한 이전 단계에서도 행렬 W가 사용되었으므로 그 이전 단계에 대한 미분을 고려해야 합니다.

RNN에서 상태 h_3가 이전 상태에 종속되므로 W의 영향을 모든 시간 단계에 걸쳐 고려해야 합니다.

U 가중치 행렬에 대해:

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_18.png)

U 행렬에 대한 오류는 W에 대한 것과 매우 유사하며, 다른 점은 숨겨진 상태 h를 U로 미분한다는 점입니다.

숨겨진 상태는 이전 숨겨진 상태와 새 입력의 복합 함수입니다.

## 일반화된 공식


<div class="content-ad"></div>

BPTT는 다음과 같이 일반화될 수 있습니다:

![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_19.png)

여기서 J는 RNN 내의 임의의 가중치 행렬이며, U, W 또는 V가 될 수 있습니다.

RNN의 총 오차(손실)는 각 시간 스텝에서의 오차인 E_t의 합입니다:

<div class="content-ad"></div>


![2024-05-18-BackpropagationThroughTimeHowRNNsLearn_20.png](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_20.png)

And, that’s pretty much all there is to training an RNN! However, there is one problem ...

# Exploding & Vanishing Gradient Problem

## Overview


<div class="content-ad"></div>

RNN의 중요한 문제 중 하나는 사라지는 그래디언트와 폭발하는 그래디언트 문제입니다. 이 문제는 BPTT를 수행할 때 네트워크를 T번 시간 단계만큼 펼치기 때문에 발생합니다. 이로 인해 네트워크는 사실상 T개의 레이어를 갖게 됩니다.

흔히 사용되는 타임스탬프의 수가 많기 때문에, 펼쳐진 네트워크는 보통 아주 깊어집니다. 그래디언트가 역방향으로 전파됨에 따라 지수적으로 증가하거나 감소할 수 있습니다.

이것은 활성화 함수가 일반적으로 tanh 또는 sigmoid인 경우에 발생합니다. 이러한 함수들은 입력을 작은 출력 범위로 압축시킵니다: sigmoid는 0에서 1, tanh는 -1에서 1까지입니다.

이러한 함수의 미분 값은 큰 절댓값 입력에 대해 작고 거의 0에 가깝습니다. RNN과 같이 깊은 네트워크에서 이러한 미분 값이 연쇄 법칙에 사용될 때, 위에서 보았듯이 많은 작은 숫자들이 곱해지게 됩니다. 이는 매우 작은 숫자가 되어 초기 레이어에서 거의 0에 가까운 그래디언트를 생성하게 됩니다.

<div class="content-ad"></div>

## 수학적 추론

이전 내용을 참고하면, 이전 시간 단계의 다른 숨은 상태에 대한 숨은 상태의 편미분을 계산하는 많은 경우가 있습니다.

![image](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_21.png)

그런 다음 우리의 숨은 상태(시간 단계) 수에 따라 여러 번 곱해집니다.

<div class="content-ad"></div>

아래는 Markdown 형식의 텍스트입니다.

![이미지1](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_22.png)

여기에 일어나는 일입니다:

![이미지2](/assets/img/2024-05-18-BackpropagationThroughTimeHowRNNsLearn_23.png)

이제 RNN이 경험하는 기울기 소실과 폭주에 대한 이유를 이해할 수 있습니다. 순차 길이에 따라 기울기가 지수 함수적으로 소실되는 것은 숨은 상태의 편도함수를 반복적으로 곱하기 때문인 체인 규칙 효과입니다.

<div class="content-ad"></div>

## 문제

만약 그래디언트가 소멸한다면, RNN은 매우 나쁜 장기 기억력을 가지고 있어 과거에서 많은 것을 배울 수 없게 됩니다. 이는 정말 좋지 않은 상황인데, RNN은 순차 데이터를 다룰 수 있도록 메모리를 갖추도록 설계되었기 때문입니다.

이로 인해 그래디언트가 매우 작아지게 되는데, 이는 가중치가 업데이트되는 값도 작아진다는 것을 의미합니다. 따라서 네트워크가 훈련하는 데 시간이 오래 걸리고 더 많은 컴퓨팅 자원을 사용하게 됩니다.

물론, 많은 현명한 사람들이 이 문제를 해결하기 위한 방법을 개발해 왔는데, 다음 글에서 그에 대해 논의할 예정입니다!

<div class="content-ad"></div>

이전 게시물에서는 폭망 경사 문제와 이를 극복하는 데 사용되는 도구에 대해 더 많이 읽을 수 있어요.

# 요약 및 추가적인 생각

RNN은 일반 피드포워드 신경망과 비슷한 알고리즘을 사용하여 학습합니다. 시간을 통한 역전파는 일반적인 역전파와 매우 유사하지만, 각 오류와 가중치 행렬에 대해 해당 가중치 행렬이 사용된 모든 과거의 시간을 고려해야 합니다. 이로 인해 불안정한 경사를 초래할 수 있습니다. RNN은 종종 매우 깊은 구조를 가지므로 도함수를 여러 번 곱하게 되어 초기 레이어에 도달했을 때 그 값을 증가시키거나 감소시킬 수 있습니다.

# 저와 소통해요!

<div class="content-ad"></div>

- LinkedIn, X(Twitter) 또는 Instagram
- 기술적인 데이터 과학과 머신 러닝 개념을 배울 수 있는 내 YouTube 채널!

## 참고 및 더 읽을거리

- Stanford RNN CheatSheet
- Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow, 2nd Edition. Aurélien Géron. 2019년 9월. 출판사: O’Reilly Media, Inc. ISBN: 9781492032649.