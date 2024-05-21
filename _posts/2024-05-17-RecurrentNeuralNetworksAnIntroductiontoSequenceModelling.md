---
title: "반복 신경망 시퀀스 모델링 소개"
description: ""
coverImage: "/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_0.png"
date: 2024-05-17 19:43
ogImage: 
  url: /assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_0.png
tag: Tech
originalTitle: "Recurrent Neural Networks: An Introduction to Sequence Modelling"
link: "https://medium.com/towards-data-science/recurrent-neural-networks-an-introduction-to-sequence-modelling-478e0e07c4ec"
---


![img](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_0.png)

많은 문제와 현상은 순차적으로 발생합니다. 대표적인 예로는 음성, 날씨 패턴, 시계열 등이 있습니다. 이러한 시스템들의 다음 위치는 이전 상태에 따라 달라집니다.

안타깝게도, 전통적인 신경망은 이러한 유형의 데이터를 처리하거나 예측할 수 없습니다. 왜냐하면 입력값을 독립적으로 분석하기 때문에 데이터가 실제로 순차적이라는 개념을 이해하지 못하기 때문입니다.

그렇다면, 이러한 유형의 데이터를 어떻게 예측할 수 있을까요?

<div class="content-ad"></div>

"우리는 순환 신경망이라고 불리는 것으로 넘어갑니다!

표준 신경망에 익숙하지 않다면, 확인할 블로그 시리즈가 있어요! RNN으로 계속 진행하기 전에 이 일반적인 신경망이 어떻게 작동하는지 알아보는 것을 권장합니다.

# 순환 신경망이란 무엇인가요?

다음은 순환 신경망(RNNs)을 설명하는 다이어그램입니다:"

<div class="content-ad"></div>


![RecurrentNeuralNetworksAnIntroductiontoSequenceModelling](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_1.png)

왼쪽에는 순환 뉴런이 있고, 오른쪽에는 시간에 따라 펼쳐진 순환 뉴런이 있습니다. RNN은 바닐라 피드포워드 신경망과 비슷해 보이지만, 이전 반복 실행에서 입력을 받는 중요한 차이점이 있습니다.

그래서 그들을 "순환"이라고 부르는 것입니다. 각 단계의 출력이 시간 안에 전파되어 다음 단계의 값을 계산하는 데 도움이 됩니다. 시스템에는 어떤 내재적 "기억"이 있어서 모델이 과거의 패턴을 추적할 수 있습니다.

예를 들어, Y_1을 예측할 때, X_1의 입력 및 이전 시간 단계 Y_0에서의 출력을 사용할 것입니다. Y_0가 Y_1에 영향을 미치기 때문에 Y_0가 Y_2에도간접적으로 영향을 줄 수 있다는 것을 알 수 있습니다. 이 알고리즘의 순환성을 명확하게 보여주는 사례입니다.


<div class="content-ad"></div>

# 숨겨진 상태

문학 작품에서는 일반적으로 숨겨진 상태라는 개념을 볼 수 있습니다. 주로 순환 뉴런을 통해 전달되는 h로 표시됩니다.

![이미지](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_2.png)

간단한 경우에는 숨겨진 상태가 셀의 출력인 경우도 있습니다. 즉, h=Y입니다. 그러나 우리가 나중에 살펴볼 것처럼, 장기 단기 메모리(LSTM) 및 게이트 순환 유닛(GRU)과 같은 보다 복잡한 셀의 경우에는 이것이 항상 참일 수 있는 것은 아닙니다.

<div class="content-ad"></div>

따라서 각 뉴런으로 전달하는 것과 각 뉴런으로부터의 전달을 명시적으로 하는 것이 가장 좋습니다. 이것이 대부분의 문헌에서 위와 같이 표시되는 이유입니다.

# 이론

순환 뉴런의 각 숨겨진 상태는 다음과 같이 계산할 수 있습니다:

![Recurrent Neural Networks](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_3.png)

<div class="content-ad"></div>

여기서:

- h_t는 시간 t에서의 은닉 상태입니다.
- h_'t−1'는 이전 시간 단계의 은닉 상태입니다.
- x_t는 시간 t에서의 입력 데이터입니다.
- W_h는 은닉 상태에 대한 가중치 행렬입니다.
- W_x는 입력 데이터에 대한 가중치 행렬입니다.
- b_h는 은닉 상태에 대한 편향 벡터입니다.
- σ는 활성화 함수로, 일반적으로 tanh 또는 sigmoid 함수를 사용합니다.

그리고 각 순환 뉴런의 출력을 예측하는 방법은 다음과 같습니다:

![Recurrent Neurons](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_4.png)

<div class="content-ad"></div>

위와 같이:

- y_t는 시간 t에서의 출력입니다.
- W_y는 출력과 관련된 가중치 행렬입니다.
- b_y는 출력 편향 벡터입니다.

보시다시피 표기법과 변수 대부분은 일반 피드포워드 신경망과 유사합니다. 유일한 차이점은 숨겨진 상태의 전달로, 그것은 모델이 출력을 예측하는 데 사용할 다른 입력이나 특성으로 볼 수 있습니다.

각 숨겨진 층은 여러 반복 뉴런을 포함할 수 있으므로 각 후속 입력 뉴런에 숨겨진 상태의 벡터를 전달하게 됩니다. 이를 통해 네트워크는 데이터에서 더 복잡한 패턴을 포착하고 표현할 수 있습니다. 각 시간 단계에서 미니 신경망으로 생각할 수 있습니다.

<div class="content-ad"></div>

# 작업 예시

우리는 RNN 내부에서 실제로 무슨 일이 일어나고 있는지 설명하기 위해 간단한 예제를 살펴볼 수 있습니다. 이 예제는 매우 단순한 시나리오일 것이지만, 알아야 할 주요 직관력을 설명해줄 것입니다. 실제로 현실에서는 어떤 문제도 이렇게 간단하지 않을 겁니다!

## 설정

1, 2, 3의 숫자 시퀀스가 있다고 가정해 보겠습니다. 이 시퀀스에서 다음 숫자인 4를 예측하기 위해 RNN을 훈련하려고 합니다.

<div class="content-ad"></div>

당신의 RNN은 다음과 같은 구조를 가지고 있을 것입니다:

- 하나의 입력 뉴런
- 하나의 은닉 뉴런
- 하나의 출력 뉴런

가중치와 바이어스를 랜덤하게 초기화할 수 있습니다:

- W_x (입력에서 은닉으로의 가중치): 0.5
- W_h (은닉에서 은닉으로의 가중치): 1.0
- b_h (은닉 바이어스): 0
- 𝑏_𝑦 (출력 바이어스): 0

<div class="content-ad"></div>

위의 텍스트를 친근한 톤으로 한국어로 번역해 드리겠습니다.

다음 활성화 함수를 사용해주세요:

- 은닉층: tanh
- 출력층: 없음 (identity/linear)

초기 은닉 상태 값:

- h_0 = 0

<div class="content-ad"></div>

## Time Step 1 (Input: 1)

다음은 첫 번째 숨겨진 상태입니다:

![첫 번째 숨겨진 상태](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_5.png)

그리고 출력은 다음과 같이 계산됩니다:

<div class="content-ad"></div>

![Recurrent Neural Networks](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_6.png)

이 예시에서 출력 활성화 함수는 identity이므로 출력 값은 hidden state 값과 동일합니다. 그러나 많은 문제에서 항상 그런 것은 아니라는 것을 기억하세요.

## 시간 단계 2 (입력: 2)

이제 최근에 계산된 h_1 값 사용하여 시간 단계 2에서 다음 입력 값을 위한 위 과정을 반복할 수 있습니다.

<div class="content-ad"></div>

아래는 Markdown 형식으로 테이블 태그를 바꿔본 것입니다.


![이미지](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_7.png)

한번 더, 우리는 시간 단계 2에서 출력 값을 계산합니다:

![이미지](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_8.png)

## 시간 단계 3 (입력: 3)


<div class="content-ad"></div>

마지막 입력 값과 세 번째 타임 스텝에서는 다음 이미지가 예측 모델을 보여줍니다:

![image](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_9.png)

![image](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_10.png)

현재 모델은 다음 숫자를 0.984로 예측하고 있습니다. 실제 값인 4와는 분명히 멀리 떨어져 있습니다. 실제로는 더 많은 훈련 세트를 사용하여 시간을 거슬러 거슬러 역전파를 수행하여 매개 변수를 최적화할 것입니다. 이 내용은 다음 글에서 다룰 예정입니다!

<div class="content-ad"></div>

다행히도 이 모든 계산과 최적화는 PyTorch와 TensorFlow와 같은 패키지를 통해 Python에서 수행됩니다. 제가 이 기사에서 이를 하는 방법의 예시를 나중에 보여 드리겠습니다!

# RNN의 종류

위의 예는 많은 입력으로부터 하나의 RNN 프로세스의 논리적인 과정을 설명하고 있습니다. 우리는 여러 입력(1,2,3)으로 시작하여 시퀀스에서 다음 숫자를 예측하기 위해 노력하고 있는데, 이는 단일 값입니다.

그러나 다른 작업을 위한 여러 종류의 RNN이 있으며, 우리는 지금 그것들을 살펴볼 것입니다.

<div class="content-ad"></div>

## 일대일

이것은 단일 예측을 내놓는 입력 세트가 하나인 전통적인 신경망입니다. 이것은 일반적인 지도 학습 문제를 해결하는 데 도움이 됩니다.

![image](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_11.png)

## 일대다

<div class="content-ad"></div>

하나의 입력이 여러 출력으로 이어집니다. 이미지 캡션을 만들거나 음악을 생성하는 데 사용할 수 있습니다.

![image](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_12.png)

## Many-To-One

여러 입력이 하나의 최종 출력을 생성합니다; 이 아키텍처는 감성 분석에 사용됩니다. 영화 리뷰를 제공하면 영화가 좋은지 나쁜지에 따라 +1 또는 -1을 할당합니다.

<div class="content-ad"></div>


![Many-To-Many](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_13.png)

This one gets an input at every step and produces an output at each step. This architecture is used for machine translation and also for problems like speech tagging.

![Many-To-Many](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_14.png)


<div class="content-ad"></div>

## 인코더-디코더

마지막으로, 인코더-디코더 네트워크를 사용할 수 있습니다. 이는 많은 개별 데이터를 입력으로 받아 하나의 데이터를 출력하는 네트워크와, 그로부터 다시 많은 개별 데이터를 출력으로 하는 네트워크로 구성됩니다. 이는 주로 한 언어로 된 문장을 다른 언어로 번역하는 데 사용됩니다.

![image](/assets/img/2024-05-17-RecurrentNeuralNetworksAnIntroductiontoSequenceModelling_15.png)

# PyTorch 예시

<div class="content-ad"></div>

위는 PyTorch에서 간단한 RNN을 구현하는 간단한 예제입니다. 위에서 해결한 문제를 시연합니다. 입력이 1,2,3이고 순서에 따라 다음 숫자를 예측하려고 합니다.

```js
import torch
import torch.nn as nn
import torch.optim as optim

# RNN 모델 정의
class SimpleRNN(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super(SimpleRNN, self).__init__()
        self.hidden_size = hidden_size
        self.rnn = nn.RNN(input_size, hidden_size, batch_first=True)
        self.fc = nn.Linear(hidden_size, output_size)

    def forward(self, x):
        x = x.unsqueeze(-1)
        h_0 = torch.zeros(1, x.size(0), self.hidden_size)
        rnn_out, _ = self.rnn(x, h_0)
        out = self.fc(rnn_out[:, -1, :])
        return out

# 데이터셋
train = torch.tensor([1, 2, 3, 4], dtype=torch.float32)
target = torch.tensor([5], dtype=torch.float32)

# 모델 설정
input_size = 1
hidden_size = 1
output_size = 1
model = SimpleRNN(input_size, hidden_size, output_size)

# 손실 및 옵티마이저
criterion = nn.MSELoss()
optimizer = optim.Adam(model.parameters(), lr=0.01)

for epoch in range(1000):
    optimizer.zero_grad()
    output = model(train.unsqueeze(0)).squeeze()  # 배치 차원 추가 및 목표 형태와 일치하도록 압축
    loss = criterion(output, target)
    loss.backward()
    optimizer.step()

# 다음 숫자 예측하는 함수
def predict(model, input_seq):
    with torch.no_grad():
        input_seq = torch.tensor(input_seq, dtype=torch.float32).unsqueeze(0)
        output = model(input_seq).squeeze().item()
    return output

# 예제 테스트 세트
test = [2, 3, 4]
predicted = predict(model, test)
print(f'Input: {test}, Predicted Next Number: {predicted:.2f}')
```

1000번의 에폭 후 출력 결과는 5입니다! 이 경우에는 모델이 실제로 1000번의 역전파로 훈련되었기 때문에 성능이 우리가 위에서 손으로 계산한 예제보다 훨씬 좋습니다.

소스 코드는 저의 GitHub에서 확인하실 수 있습니다: (GitHub URL)



<div class="content-ad"></div>

# 장점 대비 단점

이 모든 새롭게 습득한 정보를 바탕으로 RNN의 주요 장단점을 살펴보겠습니다:

## 장점

- 이전 입력값의 형태를 기억할 수 있어서 순차적 데이터를 다루는 데 도움이 됩니다.
- 정확한 가중치와 편향이 모든 시간 단계에서 공유되어, 더 적은 매개변수와 더 나은 일반화를 이끌어냅니다.
- 재귀적 성격으로 인해 RNN은 가변 길이의 순차 데이터를 처리할 수 있습니다.

<div class="content-ad"></div>

## 단점

- RNN(순환 신경망)은 장기 기억 문제로 이어지는 사라지는 그래디언트 문제에서 상당히 고통받습니다.
- 각 시간 단계는 이전 단계의 출력에 의존하기 때문에 RNN은 병렬 처리할 수 없어 계산 효율이 떨어집니다.

# 요약

RNN은 시퀀스 모델링에 매우 유용하며, 이전 실행의 정보와 메모리를 유지한 채 다음 예측으로 전파됩니다. 그들의 장점은 임의 길이의 입력을 처리할 수 있으며, 모델 크기가 이 입력 크기로 증가하지 않는다는 것입니다. 그러나 재귀적인 성격을 가지고 있기 때문에 병렬화할 수 없어 계산 효율이 낮으며, 사라지는 그래디언트 문제로 심각하게 고통받을 수 있습니다.

<div class="content-ad"></div>

# 또 다른 것!

무료 뉴스레터 'Dishing the Data'를 갖고 있어요! 매주 더 나은 데이터 과학자가 되기 위한 조언과 분야에서의 경험을 나누고 있어요.

# 저와 연결해보세요!

- LinkedIn, X (트위터), 또는 인스타그램
- 기술적인 데이터 과학과 머신 러닝 개념을 배울 수 있는 제 유튜브 채널!

<div class="content-ad"></div>

# 참고 자료 및 더 읽을거리

- Stanford RNN Cheatsheet
- Hands-On Machine Learning with Scikit-Learn, Keras, and TensorFlow, 2nd Edition. Aurélien Géron. September 2019. Publisher(s): O’Reilly Media, Inc. ISBN: 9781492032649.