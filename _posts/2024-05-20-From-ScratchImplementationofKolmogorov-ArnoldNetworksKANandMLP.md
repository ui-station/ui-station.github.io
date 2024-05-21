---
title: "콜모고로프-아르노드 네트워크KAN와 MLP의 빈대떡처럼 구현하기"
description: ""
coverImage: "/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_0.png"
date: 2024-05-20 20:16
ogImage: 
  url: /assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_0.png
tag: Tech
originalTitle: "From-Scratch Implementation of Kolmogorov-Arnold Networks
(KAN) and MLP"
link: "https://medium.com/@sidhuser/from-scratch-implementation-of-kolmogorov-arnold-networks-kan-and-mlp-14a021376386"
---


콜모고로프-아놀드 네트워크 (KAN)가 최근에 소개되었고 AI 커뮤니티에서 큰 관심을 받고 있습니다. KAN은 정확성과 해석 가능성을 모두 제공할 수 있지만, 일부는 GPUs와 같은 대규모 병렬화된 계산 구조에 쉽게 적응하지 않을 수 있다고 추측하고 있습니다. KAN은 에지 함수가 입력에 대해 비선형이며 B-스플라인의 가중 합으로 표현될 때 나타납니다. 반면, 에지 함수가 선형인 경우 아키텍처는 전통적인 다층 퍼셉트론 (MLP)로 단순화됩니다. 

AI 커뮤니티에서 KAN이 널리 받아들여질지는 시간이 결정할 것입니다. 그러나 이 문서에서는 단순한 Python으로 KAN을 처음부터 구현하는 멋진 기회로 활용했습니다 (PyTorch/TensorFlow 없이 순수한 numpy만 사용했습니다!). 실제로 KAN 또는 클래식 MLP로 인스턴스화할 수 있는 일반 목적의 완전히 연결된 피드포워드 네트워크를 구성했습니다. 이 연습은 일반 피드포워드 네트워크에서 역전파와 관련된 여러 개념을 검토하는 가치 있는 방법입니다.

# 다재다능한 뉴런 아키텍처

먼저, KAN 및 MLP를 하위 케이스로 수용할 수 있는 일반 뉴런 구조를 설명하겠습니다.

![이미지](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_0.png)

<div class="content-ad"></div>

신경원에 대한 입력이 됩니다. i번째 요소는 매개변수화된 엣지 함수를 통해 변환됩니다. 가중치 세트 w에 의해 학습 가능한 가중치로 변환되며 중간 변수가 생성됩니다.

<div class="content-ad"></div>


![Image1](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_3.png)

이때 b는 뉴런의 편향으로, 학습 가능합니다. "손실" 함수 L은 네트워크의 예측 품질을 지면 진실에 대해 평가합니다. 
우리의 목표는 손실을 최소화하는 가중치와 편향 w, b의 값을 학습하는 것입니다.
 이를 위해 매개변수에 대한 손실 도함수를 평가하고 이를 경사 하강법을 통해 업데이트합니다. 
그런 다음 이런 도함수를 연쇄 법칙을 통해 더 작은 조각으로 나눌 수 있습니다:

![Image2](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_4.png)

![Image3](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_5.png)


<div class="content-ad"></div>

## 2.1 “클래식” 뉴런

이제 일반적인 뉴런 구조를 선형 엣지 함수 φ를 사용하는 "클래식" 뉴런으로 인스턴스화합니다:

![image](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_6.png)

## 2.2 KAN 뉴런

<div class="content-ad"></div>

제1차 논문에서 설명된 대로, Kolmogorov-Arnold Network (KAN) 뉴런의 독특한 점은 엣지 함수 φ가 비선형 함수 fk의 선형 조합으로 정의되어 있다는 것입니다.


![image](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_7.png)


## 3. 완전 연결 계층


![image](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_8.png)


<div class="content-ad"></div>


<img src="/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_9.png" />

# 4. 피드포워드 네트워크

마침내, 임의의 레이어를 쌓아 완전히 연결된 피드포워드 네트워크를 형성합니다.
훈련 중에는 네트워크가 (xtrain, ytrain) 형태의 쌍 목록을 받습니다.
포워드 패스에서는 xtrain이 첫 번째 레이어에 공급되며, 각 뉴런은 내부 변수 xmid, xout을 계산하고,
다음 레이어로 전달하며 내부 도함수를 계산합니다. 마지막으로 네트워크의 출력 y가 생성되고 손실 L이 계산됩니다.
역전파 중에는, 손실 그래디언트는 레이어의 입력에 대한 것으로 먼저 초기화됩니다.

<img src="/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_10.png" />


<div class="content-ad"></div>

# 5. 손실 함수 및 해당 미분

백프로파게이션 방정식 (3)-(4)을 완전히 지정하려면 손실 함수를 선택하고 해당 미분을 계산해야 합니다.
마지막 레이어의 뉴런 출력 변수에 대한 손실 함수를 선택하고 해당 미분을 계산해야 합니다. 여기에서는 평균
제곱 오차 (MSE) 손실을 선택합니다:

![image](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_11.png)

여기서 N은 훈련 예제의 수이고, \hat{y}_n과 y_n은 각각 모델 예측과 실제 정답 레이블입니다.
이의 미분은 다음과 같습니다:

<div class="content-ad"></div>


![image](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_12.png)

이 문서에서는 Kolmogorov-Arnold Networks (KAN)와 고전적인 Multi-Layer Perceptrons (MLP)의 아키텍처와 수학적 기본을 개요로 제시했습니다. 이러한 네트워크에서 역전파(backpropagation) 및 경사 하강(gradient descent)을 구현하는 데 필요한 도함수 계산을 제공했습니다. 이 유연한 뉴런 구조를 사용하여 이제 numpy를 사용하여 Python에서 KAN 및 MLP를 구현할 수 있습니다.

## 6. 참고 문헌

[1] Ziming Liu, Yixuan, Sachin Vaidya, Fabian Ruehle, James Halverson, Marin Soljacic, Thomas Y. Hou, Max Tegmark. KAN: Kolmogorov-Arnold networks. arXiv preprint arXiv:2404.19756, 2024 [링크]


<div class="content-ad"></div>

# 7. 그리드 정의

KANs에서 그리드는 스플라인 기저 함수가 정의된 입력 공간의 이산점 집합입니다.
일차원 입력 공간을 고려해 보겠습니다. 구간 [a, b]에 대해 N개의 점으로 구성된 그리드는 다음과 같이 표현될 수 있습니다:
'x0, x1, x2, . . . , xN '
여기서 a = x_0 ` x_1 ` x_2 ` · · · ` x_N = b

<img src="/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_13.png" />

# 9. 그리드 확장 기술

<div class="content-ad"></div>

## 9.1 초기 거친 격자

먼저 거친 격자로 시작합니다. 예를 들어, x ∈ [0, 1]인 경우 5개의 지점을 가진 거친 격자는 다음과 같을 수 있습니다: '0, 0.25, 0.5, 0.75, 1'

## 9.2 격자 세분화하기

격자를 세분화하려면 기존 지점 사이에 더 많은 지점을 추가하면 됩니다. 예를 들어, 지점의 수를 두 배로 늘린다면 다음과 같이 됩니다: '0, 0.125, 0.25, 0.375, 0.5, 0.625, 0.75, 0.875, 1'

<div class="content-ad"></div>

## 9.3 스플라인 함수 개선

그리드를 개선할 때, 스플라인 함수들은 새로운 그리드에 맞게 다시 정의됩니다. 새로운 기저 함수들이 다시 계산되고, 계수들은 그에 맞게 조정됩니다.

![이미지1](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_14.png)

![이미지2](/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_15.png)

<div class="content-ad"></div>

아래는 Markdown 형식의 테이블 태그입니다.


| Tag        | Description         |
| -----------|---------------------|
| <img>      | 이미지 태그          |
| <a>        | 링크 태그            |
| <table>    | 테이블 태그          |
| <h1> - <h6> | 제목 태그           |


<div class="content-ad"></div>

## 12.2 재안 되도록 새로운 방식 배우기

그리드 개선과 업데이트의 지역화는 새로운 정보를 학습할 때 이전에 배운 정보가 크게 방해받지 않도록 합니다.
예를 들어, 새로운 데이터가 간격 [0.25, 0.75]에 속하는 경우, 계수 c0, c1, c2만 업데이트되고, 다른 간격의 계수는 영향을 받지 않습니다.

- 그리드 정의: 입력 공간 상의 이산적인 지점 집합.
- 스플라인 함수: 그리드 상에서 정의된 조각 다항식 함수.
- 그리드 세밀화: 해상도를 높이기 위해 그리드에 더 많은 지점 추가.
- KAN 순방향 패스: 그리드 상에서 스플라인 함수를 사용한 계산.
- KAN 역방향 패스: 스플라인 계수에 대한 기울기 계산.
- 계속적인 학습: 지역 업데이트가 재안을 방지합니다.

이 그리드 기반 접근 방식을 사용하면, KAN은 새로운 정보에 적응하여 이전에 학습한 지식을 잃지 않고 복잡한 함수를 효과적으로 학습할 수 있습니다.

<div class="content-ad"></div>

```js
import numpy as np
import matplotlib.pyplot as plt
from scipy.interpolate import BSpline

class KANLayer:
    def __init__(self, grid_points, degree=3):
        """
        주어진 그리드 포인트와 스플라인 차수로 KAN 레이어를 초기화합니다.

        매개변수:
        grid_points (array-like): B-스플라인이 정의된 그리드 포인트입니다.
        degree (int): B-스플라인의 차수입니다. 기본값은 3입니다 (3차 B-스플라인).
        """
        self.grid_points = grid_points
        self.degree = degree
        # 길이가 len(grid_points) + degree - 1인 랜덤으로 계수 초기화
        self.coefficients = np.random.randn(len(grid_points) + degree - 1)
        # B-스플라인 기저 함수 생성
        self.b_splines = self._create_b_splines()

    def _create_b_splines(self):
        """
        그리드 포인트와 스플라인 차수를 사용하여 B-스플라인 기저 함수를 생성합니다.

        반환:
        b_splines (list): B-스플라인 기저 함수 목록입니다.
        """
        # 클램핑을 위한 'degree' 개의 선행 및 후행 점을 포함하는 knot 벡터 생성
        knots = np.concatenate(([self.grid_points[0]] * self.degree, self.grid_points, [self.grid_points[-1]] * self.degree))
        # B-스플라인 기저 함수 생성
        b_splines = [BSpline.basis_element(knots[i:i + self.degree + 1]) for i in range(len(self.grid_points) + self.degree - 1)]
        return b_splines

    def forward(self, x):
        """
        KAN 레이어를 통한 순전파입니다.

        매개변수:
        x (array-like): B-스플라인이 평가되는 입력 값입니다.

        반환:
        phi_x (array-like): KAN 레이어의 출력입니다.
        """
        # x에서 각 B-스플라인 기저 함수를 평가하고 계수로 가중치를 적용하여 결과를 합산합니다
        phi_x = np.sum([self.coefficients[i] * self.b_splines[i](x) for i in range(len(self.b_splines))], axis=0)
        return phi_x

    def backward(self, x, dL_dphi):
        """
        그래디언트를 계산하기 위해 KAN 레이어를 통한 역전파입니다.

        매개변수:
        x (array-like): B-스플라인이 평가되는 입력 값입니다.
        dL_dphi (array-like): KAN 레이어의 출력에 대한 손실의 그래디언트입니다.

        반환:
        gradients (array-like): 계수에 대한 손실의 그래디언트입니다.
        """
        # 각 계수에 대한 손실의 그래디언트를 계산합니다
        gradients = np.array([np.sum(dL_dphi * self.b_splines[i](x)) for i in range(len(self.b_splines))])
        return gradients

def mean_squared_error(y_true, y_pred):
    """
    실제 값들과 예측 값 사이의 평균 제곱 오차를 계산합니다.

    매개변수:
    y_true (array-like): 실제 값들입니다.
    y_pred (array-like): 예측 값들입니다.

    반환:
    mse (float): 평균 제곱 오차입니다.
    """
    return np.mean((y_true - y_pred) ** 2)

# 예제 사용:
grid_points = np.linspace(0, 1, 5)  # 그리드 포인트 정의
layer = KANLayer(grid_points)  # KAN 레이어 초기화

x = np.linspace(0, 1, 100)  # 입력 값
ground_truth = np.sin(2 * np.pi * x)  # 실제 값 함수 정의

learning_rate = 0.02  # 학습률
epochs = 1000  # 에폭 수

# 학습 루프
for epoch in range(epochs):
    phi_x = layer.forward(x)  # 순전파
    loss = mean_squared_error(ground_truth, phi_x)  # 손실 계산
    dL_dphi = 2 * (phi_x - ground_truth) / len(x)  # 출력에 대한 손실의 그래디언트 계산

    gradients = layer.backward(x, dL_dphi)  # 역전파
    layer.coefficients -= learning_rate * gradients  # 계수 업데이트

    if epoch % 100 == 0:
        print(f'에폭 {epoch}/{epochs}, 손실: {loss}')

# KAN 레이어의 출력과 실제 값 플로팅
plt.plot(x, layer.forward(x), label="예측된 출력")
plt.plot(x, ground_truth, label="실제 값", linestyle='dashed')
plt.title("KAN 레이어 출력 vs 실제 값")
plt.xlabel("x")
plt.ylabel("phi(x)")
plt.legend()
plt.show()
```

<img src="/assets/img/2024-05-20-From-ScratchImplementationofKolmogorov-ArnoldNetworksKANandMLP_17.png" />