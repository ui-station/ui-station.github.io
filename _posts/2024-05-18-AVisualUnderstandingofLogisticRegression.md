---
title: "로지스틱 회귀의 시각적 이해"
description: ""
coverImage: "/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_0.png"
date: 2024-05-18 20:20
ogImage: 
  url: /assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_0.png
tag: Tech
originalTitle: "A Visual Understanding of Logistic Regression"
link: "https://medium.com/towards-data-science/a-visual-understanding-of-logistic-regression-2e6733844397"
---


<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_0.png" />

로지스틱 회귀는 이진 분류에서 사용되는 통계 모델입니다. 이진 분류 문제에서 대상은 두 가지 범주만 가지고 있으므로 기계 학습 알고리즘은 데이터를 이 두 범주 중 하나로 분류해야 합니다. 로지스틱 회귀는 각 범주에 속할 확률을 예측하는 데 사용되는 로지스틱 함수에서 유래했습니다. 로지스틱 회귀는 지도 기계 학습, 금융, 의학 및 사회과학 등 여러 분야에 응용됩니다.

본 문서에서는 로지스틱 회귀의 시각적 이해를 제시하고, 이 모델의 각 요소의 역할을 설명할 것입니다. 이 글을 읽으면 독자는 로지스틱 회귀와 그 한계에 대한 직관적인 이해를 가질 수 있습니다.

본 문서의 모든 이미지는 저자에 의해 제작되었습니다.

<div class="content-ad"></div>

간단한 데이터셋

로지스틱 회귀가 분류 문제를 해결하는 방법을 보여주기 위해 간단한 데이터셋을 만들겠습니다. 먼저 필요한 모든 Python 라이브러리를 가져옵니다.

```js
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from sklearn.linear_model import LogisticRegression
from matplotlib.colors import ListedColormap

import warnings
warnings.filterwarnings("ignore")
```

우리의 데이터셋은 두 개의 특성 (x₁, x₂)과 100개의 예제가 있습니다. 이는 두 개의 클러스터로 구성되어 각각 정규 분포를 사용하여 만들어졌습니다.

<div class="content-ad"></div>

```python
np.random.seed(0)
x1 = np.random.randn(50, 2) * 0.4 + np.array([-1, -1])
x2 = np.random.randn(50, 2) * 0.4 + np.array([2.6, 2.6])

y = 50*[0]+50*[1]
X = np.vstack((x1, x2))
```

이 데이터셋에 대한 target 또는 label 열 (y)도 정의했습니다. 첫 번째 클러스터의 데이터 포인트들의 레이블은 0이고, 두 번째 클러스터의 데이터 포인트들의 레이블은 1입니다. 따라서 target 열에는 2개의 레이블만 있어서 binary classification 문제가 됩니다. 이제 이 데이터셋을 플롯합니다. 결과는 아래 그림에서 확인할 수 있습니다.

```python
plt.scatter(x1[:, 0], x1[:,1], label="y=0")
plt.scatter(x2[:, 0], x2[:,1], label="y=1")
plt.legend(loc="best", fontsize=14)
plt.xlabel("$x_1$", fontsize=16)
plt.ylabel("$x_2$", fontsize=16)
plt.show()
```

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_1.png" />
```

<div class="content-ad"></div>

지금 이 데이터셋을 분류하기 위해 로지스틱 회귀 모델을 사용할 수 있습니다. 이 모델을 훈련시켜 이 데이터셋의 데이터 포인트의 이진 레이블을 예측할 것입니다. 또한 이 모델은 이 훈련 데이터셋에 없는 어떤 보이지 않는 데이터 포인트에 대한 예측을 총체화할 수 있어야 합니다.

로지스틱 회귀 방정식

로지스틱 회귀 모델을 이해하려면 먼저 그 방정식을 자세히 살펴봐야 합니다:

![로지스틱 회귀 방정식](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_2.png)

<div class="content-ad"></div>

여기서 P는 데이터 포인트 (x₁, x₂)가 레이블 1일 확률을 예측한 값입니다. 이 방정식은 로지스틱 회귀 모델의 핵심입니다. 그냥 데이터 포인트를 가져와서 해당 레이블이 1일 확률을 계산하는 것이죠. 이 함수는 표준 로지스틱 또는 시그모이드 함수라고 불립니다. 아래 소개된 그림 2는 이 함수의 플롯을 보여줍니다. x가 ∞로 다가갈수록 y는 1로 수렴하고, x가 -∞로 다가갈수록 y는 0으로 수렴함을 주목하세요. 게다가 x=0에서 y=0.5인 것을 알 수 있습니다. 따라서 y는 항상 0과 1 사이에 제한됩니다. 우리는 확률이 항상 [0,1] 범위 내에 있음을 알고 있기 때문에 결과의 확률을 표현하기 위해 시그모이드 함수를 사용할 수 있습니다. 이 함수는 임의의 실수 값을 가진 입력(x)을 0과 1 사이의 확률 값으로 매핑할 수 있습니다.

<div class="content-ad"></div>

이제 방정식 1이 특징 x₁과 x₂로 표현된 데이터 포인트를 입력하여 해당 레이블이 1일 확률로 변환하는 방법을 알아봅시다.

차원 축소

방정식 1을 두 부분으로 나눌 수 있습니다. 먼저 입력 데이터(x₁, x₂)를 선형 항으로 변환합니다.

![equation](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_5.png)

<div class="content-ad"></div>

여기서 w₀, w₁ 및 w₂는 모델의 매개변수이며 이 값은 모델을 학습한 후에 결정될 것입니다. 이것은 두 개의 특징 (x₁, x₂)으로 시작하여 방정식 2에 의해 제공된 단일 숫자로 변환하는 차원 축소의 예입니다. 실제로 우리는 입력 데이터 포인트의 차원을 2에서 1로 줄입니다. 이 차원 축소가 기하학적으로 어떻게 이루어지는지 살펴보겠습니다. 데이터 포인트 (x₁, x₂)로 시작합니다. 우리는 이를 2차원 공간에서 점 또는 벡터로 표시할 수 있습니다(Figure 3). 또한 벡터 w를 다음과 같이 정의할 수 있습니다:

![vector w equation](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_6.png)

벡터 u를 w의 단위 벡터로 정의할 수 있도록 하는 다음 방정식을 사용하여 정의합시다:

![unit vector equation](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_7.png)

<div class="content-ad"></div>

벡터 u가 w와 같은 방향을 가지지만 길이는 1입니다. 이제 벡터 x를 w에 평행한 벡터와 수직인 벡터 두 가지 구성 요소 벡터로 분해할 수 있습니다. 평행 벡터는 x를 w에 투영한 벡터로 불리며 x^로 표시됩니다(그림 3). 또한 x와 w의 내적을 사용하여 x^를 얻을 수 있습니다:

![Figure 3](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_8.png)

![Image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_9.png)

x^의 길이는 x를 w에 투영한 스칼라 투영이라고 하며 다음과 같이 주어집니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_10.png" />

이제 x^를 ||w||로 곱하면 d로 표시된 새로운 벡터를 얻습니다:

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_11.png" />

그리고 d의 길이는 u가 단위 벡터이기 때문에 w.x와 동일합니다. 내적의 정의에 따라 다음과 같습니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_12.png" />

이것은 방정식 2에서 정의된 용어 일부를 제공합니다. 그러나 w₀를 추가해야 합니다.

이를 위해 다음과 같은 벡터를 정의합니다.

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_13.png" />

<div class="content-ad"></div>

이제, d에서 o를 뺀다면 다음과 같습니다:

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_14.png)

즉, d-o의 길이는 방정식 2에서 정의된 용어와 같다는 것을 의미합니다 (그림 4). 이것은 벡터 d의 새로운 원점을 정의하는 것과 같습니다. 기하학적 관점에서 보면, 우리는 벡터 o의 끝 지점을 기준으로 d의 길이를 측정합니다. 반면 2차원 공간의 원점을 기준으로 하지 않습니다. (이 그림에서 w₀가 양수라고 가정하였기 때문에 벡터 o는 w의 반대 방향에 있습니다).

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_15.png)

<div class="content-ad"></div>

위의 텍스트를 친근한 톤으로 한국어로 번역하겠습니다.

요약하자면, 방정식 2의 용어 역할은 차원 축소입니다. 입력 데이터 점의 차원을 1로 줄입니다. 따라서 변환된 데이터 점은 모두 원점을 통과하고 벡터 w를 따라 있는 선 l로 가정할 수 있습니다. 이 선을 새로운 축으로 생각하면, 원점은 벡터 o의 끝에 있는 새 축이라는 것을 알 수 있습니다. 이제 이 축 위의 변환된 데이터 점의 좌표는 방정식 2에 의해 주어집니다.

벡터 x가 입력 데이터 점을 나타낸다고 가정했습니다. 새로운 축 l 상의 변환된 데이터 점을 얻기 위해 우리는 먼저 직교 투영을 수행하고 x를 w에 투영한 벡터를 찾았습니다. 그런 다음 결과 벡터인 (x^)에 w의 길이를 곱하여 벡터 d를 얻었습니다. 벡터 d는 새로운 축 l 상의 변환된 데이터 점을 나타내지만, 그 좌표는 o에서 d를 뺀 d-o로 주어집니다.

이제 우리는 장난감 데이터 세트에서 변환된 일차원 데이터 점을 계산할 수 있습니다. 여기서는 scikit-learn 라이브러리의 로지스틱 회귀 모델을 사용합니다. 데이터 세트를 fitting한 후, 방정식 2의 선형 항의 계수를 검색할 수 있습니다.

```python
lg=LogisticRegression()
lg.fit(X,y)
w = lg.coef_[0]
w1, w2 = w[0], w[1]
w0 = lg.intercept_[0]
print("w0={}, w1={}, w2={}".format(w0, w1, w2))
```

<div class="content-ad"></div>

```md
w0=1.2124, w1=0.9033, w2=0.9075
```

이제 w₁와 w₂의 값을 사용하여 벡터 w를 형성할 수 있습니다. w의 단위 벡터는 다음과 같이 정의됩니다:

\[ w = \begin{bmatrix} 1.2124 \\ 0.9033 \\ 0.9075 \end{bmatrix} \]

다음과 같이 계산됩니다:
```

<div class="content-ad"></div>

```js
length_w = np.linalg.norm(w)
u = w / length_w
```

변환된 일차원 데이터 포인트들은 이 벡터를 따라 놓이게 될 것이고, u와 w가 동일한 방향을 가지고 있기 때문에 w도 따라 늘어날 것입니다. 이 선의 원점은 벡터 o=-w₀u의 끝에 위치합니다.

```js
o = -w0 * u
```

다음 코드 스니펫을 통해 원본 데이터 세트, 벡터 w와 o, 그리고 변환된 데이터 포인트를 플롯합니다. 결과는 Figure 5에 표시됩니다.

<div class="content-ad"></div>

```markdown
```python
lt.figure(figsize=(6,6))

# 원본 데이터 세트 플롯
plt.scatter(X[y==0, 0], X[y==0,1], label="y=0", alpha=0.4, color="red")
plt.scatter(X[y==1, 0], X[y==1,1], label="y=1", alpha=0.4, color="blue")

# 변환된 포인트 플롯
transformed_points = np.dot(X, w).reshape(-1,1) * np.tile(w, (len(X), 1))
plt.scatter(transformed_points[:, 0], transformed_points[:, 1],
            alpha=0.5, color='green', label="변환된\n포인트")

# 포인트 o 플롯
plt.scatter(o[0], o[1], color='black', s=35, zorder=10)
# 벡터 w와 o 플롯
plt.quiver([0], [0], w[0], w[1], color=['b'],
           width=0.01, angles='xy', scale_units='xy', scale=1, zorder=5)
plt.quiver([0], [0], o[0], o[1], color=['black'],
           width=0.01, angles='xy', scale_units='xy', scale=1, zorder=5)

# 벡터 w를 따라 나아가는 선
plt.plot([-12*u[0], 19*u[0]],
         [-12*u[1], 19*u[1]], color='gray')

# 축 생성
plt.axhline(0, color='black', linewidth=0.8)
plt.axvline(0, color='black', linewidth=0.8)
plt.text(0.3, 1.2, "$\mathregular{w}$", color='b', fontsize=14,
         weight="bold", style="italic")
plt.text(-1.7, -1, "$\mathregular{o}$", color='black', fontsize=14,
         weight="bold", style="italic")

plt.xlim([-8, 6])
plt.ylim([-8, 6])
ax = plt.gca()  
ax.set_aspect('equal')
plt.legend(loc="best", fontsize=13)
plt.xlabel('$x_1$', fontsize=16)
plt.ylabel('$x_2$', fontsize=16)

plt.show()
```

![그림](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_17.png)

모든 변환된 데이터 포인트는 w 벡터를 따라 있는 선상에 있음을 유의해주세요. 이 선의 원점은 점 o에 위치합니다. 원본 데이터 세트의 각 데이터 포인트 (x₁, x₂)는 이 선상의 데이터 포인트로 변환되며, 변환된 데이터 포인트 (각 녹색 점)의 점 o로부터의 거리는 w₀+w₁x₁+w₂x₂와 같습니다.

시그모이드 함수 추가
```

<div class="content-ad"></div>

먼저 우리는 방정식 1을 두 부분으로 나눴다는 것을 기억해야 해요. 먼저 입력 데이터 (x₁, x₂)를 선형 항인 w₀+w₁x₁+w₂x₂로 변환합니다. 이는 차원 축소 과정으로, 변환된 일차원 데이터 포인트를 만들어냅니다. 다음 부분은 이러한 변환된 데이터 포인트에 시그모이드 함수를 정의합니다. 이 함수는 변환된 데이터 포인트가 레이블 1을 가지는 확률을 계산합니다. 이 확률을 계산하기 위해 각 변환된 데이터 포인트의 좌표 (l=w₀+w₁x₁+w₂x₂)를 시그모이드 함수에 넣는 것입니다:

![이미지](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_18.png)

다음 코드 스니펫은 이 확률을 계산하고 도표 6에 시그모이드 함수를 그리는 것입니다:

```js
plt.figure(figsize=(15,5))

transformed_points = np.dot(X, w) + w0
plt.scatter(transformed_points, [0]*len(transformed_points),
            s=280, color='green', alpha=0.4,
            label="변환된 데이터 포인트")
l_array = np.linspace(-12, 8, 100)
P = 1 / (1+np.exp(-l_array))
plt.plot(l_array, P, color='black', label="시그모이드 함수")

plt.xlim([-8, 8])
plt.ylim([0, 1.05])
plt.legend(loc="best", fontsize=18)
plt.xlabel('$l$', fontsize=22)
plt.ylabel('$P$', fontsize=22)
plt.xticks(fontsize=18)
plt.yticks(fontsize=18)
plt.show()
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_19.png" />

그래서 각 변환된 데이터 포인트마다 y=1의 확률이 있습니다. 그러나 실제 레이블을 얻기 위해서는 확률 임계값을 정의해야 합니다 (y의 실제 값). 이 임계값은 이진 분류 결정을 내릴 확률을 정의합니다. 기본적으로 로지스틱 회귀는 P=0.5의 임계값을 선택합니다. 시그모이드 곡선은 원점에서 값이 0.5임을 기억해 주세요. 따라서 w₀+w₁x₁+w₂x₂≥0 (y^=1)인 모든 포인트에 대해 예측된 레이블은 1이며, w₀+w₁x₁+w₂x₂<0 (y^=0)인 모든 포인트에 대해 예측된 레이블은 0입니다. 따라서 확률 임계값은 각 변환된 데이터 포인트의 예측된 확률(P)을 y^로 나타내는 예측된 이진 레이블로 변환합니다. 이것은 Figure 7에 표시되어 있습니다.

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_20.png" />

우리는 또한 이 시그모이드 곡선을 원래 2차원 공간에 그릴 수 있습니다. 이 결과는 Figure 8에 표시되어 있습니다.

<div class="content-ad"></div>

```js
plt.figure(figsize=(6,6))

# 원본 데이터셋 플롯
plt.scatter(X[y==0, 0], X[y==0,1], label="y=0", alpha=0.7, color="red")
plt.scatter(X[y==1, 0], X[y==1,1], label="y=1", alpha=0.7, color="blue")

# 투영된 점 플롯
transformed_points = np.dot(X, w).reshape(-1,1) * np.tile(w, (len(X), 1))
plt.scatter(transformed_points[:, 0], transformed_points[:, 1],
            alpha=0.5, color='green', label="Transformed\n data points")

# 점 o 플롯
plt.scatter(o[0], o[1], color='black', s=35)
# 벡터 w 플롯
plt.quiver([0], [0], w[0], w[1], color=['b'],
           width=0.01, angles='xy', scale_units='xy', scale=1, zorder=5)


# 시그모이드 곡선 플롯
k = 200
l_array = np.linspace(-12, 8, k).reshape(-1, 1)
w_array = l_array * np.tile(u, (k, 1)) 
# 벡터 w를 따라 선 그리기
plt.plot([-12*u[0], 19*u[0]],
         [-12*u[1], 19*u[1]], color='gray')

sigm_x_array = ((w_array - o) /u)[:,0]
sigm_prob = 1 / (1+np.exp(-sigm_x_array))
norm_vector = np.array([-w2, w1]) if w1>=0 else np.array([w2, -w1])
sigm_y_array = sigm_prob.reshape(k, 1) * np.tile(norm_vector, (k, 1)) 
sigm_curve_array = sigm_y_array + w_array

plt.plot(sigm_curve_array[:, 0], sigm_curve_array[:, 1], color="blue")
# 시그모이드 곡선의 y축 플롯
plt.plot([o[0], o[0]+2*norm_vector[0]],
         [o[1], o[1]+2*norm_vector[1]], color='gray')

# 축 그리기
plt.axhline(0, color='black', linewidth=0.8)
plt.axvline(0, color='black', linewidth=0.8)
plt.text(0.3, 1.2, "$\mathregular{w}$", color='b', fontsize=14,
         weight="bold", style="italic")
plt.text(-0.8, -1.4, "$\mathregular{o}$", color='black', fontsize=14,
         weight="bold", style="italic")
plt.text(-2.9, 1.3, "$P$", color='black', fontsize=14,
         weight="bold", style="italic", rotation = 50)

plt.xlim([-8, 6.2])
plt.ylim([-8, 6.2])
ax = plt.gca()  
ax.set_aspect('equal')
plt.xlabel('$x_1$', fontsize=16)
plt.ylabel('$x_2$', fontsize=16)

plt.show()
```

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_21.png" />

하지만 2차원 공간에서 결정 경계를 어떻게 찾을까요? 이를 위해 2차원 공간의 모든 점을 찾아야 합니다. 이러한 점들은 1차원 공간의 원점으로 매핑됩니다 (Figure 8의 점 o). Figure 9에서 이러한 점들을 찾을 수 있는 방법을 보여줍니다.

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_22.png" />
```

<div class="content-ad"></div>

여기서는 x라는 지점을 찾고 있습니다.

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_23.png)

이러한 점들은 x^에서 l에 수직인 선상에 있습니다. 우리는 이 선을 s로 표시할 것입니다 (도표 9). 모든 점들이 2차원 평면의 원점으로부터의 거리는 |w₀| / ||w||입니다(점과 선 사이의 거리는 그 선에 수직이고 해당 점을 통과하는 선분의 길이입니다). 이제 s의 모든 점들에 대한 벡터 d는 다음과 같습니다:

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_24.png)

<div class="content-ad"></div>

따라서 데이터 지점이 선 s에 있으면, 해당 변환된 지점은 지점 o(1차원 공간의 원점)에 있고, 그 확률은 0.5가 됩니다. 이로써 선 s가 2차원 공간의 로지스틱 회귀의 결정 경계라는 것을 결론짓게 되었습니다. 왜냐하면 이 선 상의 모든 데이터 지점은 1차원 공간의 시그모이드 곡선의 결정 경계로 매핑되기 때문입니다. 따라서 이제 우리 모델의 결정 경계를 쉽게 그릴 수 있습니다.

```js
boundary_point = (-w0 / length_w**2) * w

plt.figure(figsize=(6,6))

plt.scatter(X[y==0, 0], X[y==0,1], label="y=0", alpha=0.7, color="red")
plt.scatter(X[y==1, 0], X[y==1,1], label="y=1", alpha=0.7, color="blue")

plt.scatter(o[0], o[1], color='black', s=35)
# 벡터 w 그리기
plt.quiver([0], [0], w[0], w[1], color=['b'],
           width=0.01, angles='xy', scale_units='xy', scale=1, zorder=5)

plt.quiver([0], [0], boundary_point[0], boundary_point[1], color=['black'],
           width=0.01, angles='xy', scale_units='xy', scale=1, zorder=5)

# 결정 경계 그리기
plt.plot([boundary_point[0], boundary_point[0]-6],
         [boundary_point[1], boundary_point[1]-6*(-w1/w2)],
         color='black', linestyle="--")
plt.plot([boundary_point[0], boundary_point[0]+6],
         [boundary_point[1], boundary_point[1]+6*(-w1/w2)],
         color='black', linestyle="--")

# 시그모이드 곡선 그리기
k = 200
l_array = np.linspace(-8.4, 8, k).reshape(-1, 1)
w_array = l_array * np.tile(u, (k, 1)) 

# 벡터 w를 따른 선 그리기
plt.plot([-9*u[0], 8*u[0]],
         [-9*u[1], 8*u[1]], color='gray')

sigm_x_array = ((w_array - o) /u)[:,0]
sigm_prob = 1 / (1+np.exp(-sigm_x_array))
norm_vector = np.array([-w2, w1]) if w1>=0 else np.array([w2, -w1])
sigm_y_array = sigm_prob.reshape(k, 1) * np.tile(norm_vector, (k, 1)) 
sigm_curve_array = sigm_y_array + w_array

plt.plot(sigm_curve_array[:, 0], sigm_curve_array[:, 1], color="blue")
# 시그모이드 곡선의 y축 그리기 
plt.plot([o[0], o[0]+2*norm_vector[0]],
         [o[1], o[1]+2*norm_vector[1]], color='gray')

# 축 그리기
plt.axhline(0, color='grey', linewidth=0.8)
plt.axvline(0, color='grey', linewidth=0.8)

plt.text(0.35, 1, "$\mathregular{w}$", color='b', fontsize=14,
         weight="bold", style="italic")
plt.text(-0.95, -1.35, "$\mathregular{o}$", color='black', fontsize=14,
         weight="bold", style="italic")
plt.text(-3.15, 0.5, "$P$", color='black', fontsize=14,
         weight="bold", style="italic", rotation = 50)
plt.text(-4, 3, "결정 경계", color='black', fontsize=14)
plt.text(-0.3, -0.7, r"$\frac{-w_0}{\mathregular{||w||^2}\mathregular{w}$",
         color='black', fontsize=15, weight="bold", style="italic")


plt.xlim([-5.6, 4.2])
plt.ylim([-5.6, 4.2])
ax = plt.gca()  
ax.set_aspect('equal')

plt.xlabel('$x_1$', fontsize=16)
plt.ylabel('$x_2$', fontsize=16)

plt.show()
```

<img src="/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_25.png" />

우리는 또한 여기서 발견한 결정 경계의 위치를 유효성 검사할 수 있습니다. 이를 위해 모델의 경계를 다른 방법을 사용하여 그리는 함수를 정의합니다. 먼저 2차원 공간에 메시 그리드를 생성하고 이를 사용하여 훈련된 로지스틱 회귀 모델을 사용하여 해당 지점의 목표를 예측합니다. y^=0 및 y^=1인 지점은 서로 다른 색으로 표시되므로 그리드가 충분히 잘 그려진 경우 모델의 결정 경계를 쉽게 확인할 수 있습니다. 결과는 Figure 11에 나와 있으며 이전에 발견한 결정 경계와 일치합니다.

<div class="content-ad"></div>

```js
def plot_boundary(X, y, clf, lims):
    gx1, gx2 = np.meshgrid(np.arange(lims[0], lims[1], (lims[1]-lims[0])/300.0),
                           np.arange(lims[2], lims[3], (lims[3]-lims[2])/300.0))
    
    cmap_light = ListedColormap(['lightsalmon', 'aqua'])
            
    gx1l = gx1.flatten()
    gx2l = gx2.flatten()
    gx = np.vstack((gx1l,gx2l)).T
    gyhat = clf.predict(gx)
    gyhat = gyhat.reshape(gx1.shape)

    plt.pcolormesh(gx1, gx2, gyhat, cmap=cmap_light)
    plt.scatter(X[y==0, 0], X[y==0,1], label="y=0", alpha=0.7, color="red")
    plt.scatter(X[y==1, 0], X[y==1,1], label="y=1", alpha=0.7, color="blue")
    plt.legend(loc='upper left')


plt.figure(figsize=(6,6))
plot_boundary(X,y,lg, lims=[-5.6, 4.2, -5.6, 4.2])

# Plot the vector w
plt.quiver([0], [0], w[0], w[1], color=['b'],
           width=0.01, angles='xy', scale_units='xy', scale=1, zorder=5)

plt.quiver([0], [0], boundary_point[0], boundary_point[1], color=['black'],
           width=0.01, angles='xy', scale_units='xy', scale=1, zorder=5)

# plot the decision boundary
plt.plot([boundary_point[0], boundary_point[0]-6],
         [boundary_point[1], boundary_point[1]-6*(-w1/w2)],
         color='black', linestyle="--")
plt.plot([boundary_point[0], boundary_point[0]+6],
         [boundary_point[1], boundary_point[1]+6*(-w1/w2)],
         color='black', linestyle="--")

# Plot the line along the vector w 
plt.plot([-9*u[0], 8*u[0]],
         [-9*u[1], 8*u[1]], color='gray')

# Draw axes
plt.axhline(0, color='grey', linewidth=0.8)
plt.axvline(0, color='grey', linewidth=0.8)

plt.text(0.35, 1, "$\mathregular{w}$", color='b', fontsize=14,
         weight="bold", style="italic")

plt.text(-2, 3, "$\hat{y}=1$\nregion", color='blue', fontsize=14)
plt.text(1, -5, "$\hat{y}=0$\nregion", color='red', fontsize=14)
plt.text(-0.3, -0.7, r"$\frac{-w_0}{\mathregular{||w||^2}\mathregular{w}$",
         color='black', fontsize=15, weight="bold", style="italic")

plt.xlim([-5.6, 4.2])
plt.ylim([-5.6, 4.2])
ax = plt.gca()
ax.set_aspect('equal')

plt.xlabel('$x_1$', fontsize=16)
plt.ylabel('$x_2$', fontsize=16)
plt.show()
```

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_26.png)

여기에 결과를 요약해보겠습니다. 두 가지 특성을 갖는 데이터셋에서 로지스틱 회귀 모델의 의사결정 경계는 직선으로 형성됩니다. 이 직선은 모델의 매개변수 w₀, w₁, w₂에 의해 결정됩니다. 의사결정 경계는 벡터를 연장한 선에 수직입니다.

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_27.png)

<div class="content-ad"></div>

이 라인과 의사결정 경계의 교차점은 (-w₀ / ||w||²)w 벡터에 의해 결정됩니다.

고차원에서의 의사결정 경계

저희가 데이터셋에서 더 많은 피쳐를 가지고 있는 경우에 어떻게 될지 살펴봅시다. 우리는 같은 컨셉을 고차원으로 쉽게 적용할 수 있습니다. x₁, x₂, x₃라는 세 개의 피쳐를 가지고 있다고 상상해보겠습니다. 이제 로지스틱 회귀 방정식은 다음과 같습니다:

<div class="content-ad"></div>

모델 매개변수는 w₀, w₁, w₂ 및 w₃입니다. 차원 축소 부분은 동일하며, 원본 데이터 포인트는 여전히 1차원 공간에 매핑됩니다. 변환된 데이터 포인트는 여전히 직선 l 상에 있으며 해당 벡터를 확장합니다:

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_29.png)

결정 경계는 w로의 벡터 투영이 (-w₀ / ||w||²)w인 모든 포인트의 위치입니다. 이러한 포인트는 차원 축소 후 P=0.5를 갖게 됩니다. 따라서 결정 경계는 3차원 공간에서 평면입니다(도 12 참조). 이 평면은 l에 수직이며, l과의 교차점은 (-w₀ / ||w||²)w 벡터로 주어집니다.

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_30.png)

<div class="content-ad"></div>

보다 일반적으로, n차원 공간에서 로지스틱 회귀 모델은 n개의 매개변수 w₀, w₁, …, w_n을 가지고 있습니다. 여기에서, 만약 벡터를 확장한다면,

![image](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_31.png)

선 l로, 결정 경계는 n차원 초평면입니다. 이 초평면은 l에 수직이며, (-w₀ / ||w||²)w 벡터는 초평면과 l의 교차점을 나타냅니다.

로지스틱 회귀는 항상 n차원 공간에서 1차원 공간으로 차원 축소를 시작합니다. 따라서 그 결정 경계는 곡률이 없는 초평면입니다. 결정 경계가 초평면인 분류기는 선형 분류기라고 하며, 로지스틱 회귀는 그러한 분류기의 한 예입니다. 다른 예시로는 퍼셉트론과 서포트 벡터 머신(SVM)이 있습니다.

<div class="content-ad"></div>

데이터 세트(특성이 n개 있는)는 이진 대상을 가지고 있고 n차원 초평면을 사용하여 서로 다른 라벨을 가진 데이터 포인트들을 완전히 분리할 수 있다면 선형 분리 가능하다고 합니다. 따라서 선형 분류기는 선형 분리 가능한 데이터 세트에 대한 완벽한 모델입니다. 지금까지 사용된 토이 데이터 세트는 선형 분리 가능했습니다(Figure 1), 그러나 많은 데이터 세트는 선형 분리가 불가능하며 로지스틱 회귀와 같은 모델은 그에 적합하지 않을 수 있습니다. Figure 13는 선형 분리가 불가능한 데이터 세트의 예시를 보여줍니다.

![Figure 13](/assets/img/2024-05-18-AVisualUnderstandingofLogisticRegression_32.png)

여기서 데이터 세트는 원 모양을 가지고 있으며, 직선을 사용하여 y=0 및 y=1을 가진 데이터 포인트들을 완벽하게 나눌 수 없습니다. 따라서 이러한 분류 문제에 로지스틱 회귀 모델을 사용할 수 없습니다.

이 기사에서는 선형 대수를 사용하여 로지스틱 회귀의 시각적 해석을 제공하려고 노력했습니다. 로지스틱 회귀는 1차원 공간으로의 차원 축소부터 시작하고, 그런 다음 변환된 데이터 포인트에 대한 확률을 할당합니다. 확률 임계값을 정의함으로써, 해당 데이터 포인트의 이진 대상에 대한 최종 예측을 얻을 수 있습니다. 1차원 공간으로의 차원 축소로 인해 로지스틱 회귀는 선형 분류기가 됩니다. 따라서 n개의 특성을 가진 데이터 세트에 적용되는 경우 의사 결정 경계는 n차원 초평면이 됩니다.

<div class="content-ad"></div>

이 기사를 즐겁게 읽었으면 좋겠어요. 제 기사가 도움이 된다면, 저를 Medium에서 팔로우해 주세요.