---
title: "K-평균 클러스터링 마스터하기"
description: ""
coverImage: "/assets/img/2024-05-23-MasteringK-MeansClustering_0.png"
date: 2024-05-23 17:59
ogImage: 
  url: /assets/img/2024-05-23-MasteringK-MeansClustering_0.png
tag: Tech
originalTitle: "Mastering K-Means Clustering"
link: "https://medium.com/towards-data-science/mastering-k-means-clustering-065bc42637e4"
---


## Python으로부터 K-Means 알고리즘을 처음부터 구현하는 단계별 튜토리얼

![image](/assets/img/2024-05-23-MasteringK-MeansClustering_0.png)

이 글에서는 오늘 처음 시작한다면 어떻게 K-Means 알고리즘을 배우는지 보여드립니다. 우리는 기본 개념부터 시작하여 Numpy 패키지만 사용하여 클러스터링 작업을 수행하는 Python 클래스를 구현할 것입니다.

기계 학습을 처음 접하는 사람이라면 개념을 확실하게 이해하려고 노력하는 중이거나 알고리즘이 어떻게 작동하는지 이해해야 하는 맞춤형 기계 학습 응용 프로그램을 만들려는 실무자라면, 이 글은 여러분을 위한 것입니다.

<div class="content-ad"></div>

# 1. 소개

대부분의 기계 학습 알고리즘 중에는 선형 회귀, 로지스틱 회귀, 의사결정 트리 등이 있습니다. 이러한 알고리즘들은 레이블이 지정된 데이터로부터 예측을 만드는 데 유용합니다. 즉, 각 입력은 레이블 값과 함께 특성 값으로 구성됩니다. 이를 지도 학습이라고 합니다.

그러나 종종 레이블이 지정되지 않은 대규모 데이터 집합과 다뤄야 할 때가 있습니다. 구매 행동, 인구 통계, 주소 등을 기반으로 고객의 다양한 그룹을 이해하여 더 나은 서비스, 제품 및 프로모션을 제공할 필요가 있는 비즈니스를 상상해보십시오.

이러한 유형의 문제는 비지도 학습 기술을 사용하여 해결할 수 있습니다. K-평균 알고리즘은 기계 학습에서 널리 사용되는 비지도 학습 알고리즘입니다. 간단하고 우아한 방식으로 데이터 집합을 사용자가 원하는 K 개의 서로 다른 클러스터로 분리할 수 있으며, 이를 통해 레이블이 지정되지 않은 데이터에서 패턴을 학습할 수 있습니다.

<div class="content-ad"></div>

# 2. K-Means 알고리즘은 무엇을 하는가?

이전에 말했듯이, K-Means 알고리즘은 데이터 포인트를 주어진 클러스터 수로 분할합니다. 각 클러스터 내의 포인트들은 유사하며, 다른 클러스터의 포인트들은 상당한 차이를 보입니다.

그렇다면 한 가지 의문이 생깁니다: 유사성 또는 차이를 어떻게 정의할까요? K-Means 클러스터링에서 유사성을 측정하는 가장 일반적인 메트릭은 유클리드 거리입니다.

아래 그림에서는 세 가지 다른 그룹이 명확히 보입니다. 따라서 각 그룹의 중심을 결정하고, 각 포인트는 가장 가까운 중심과 연관됩니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-MasteringK-MeansClustering_1.png" />

이렇게 함으로써, 수학적으로 말하면, 아이디어는 클러스터 내 분산을 최소화하는 것이며, 각 포인트와 가장 가까운 중심점 사이의 유사성을 측정하는 것입니다.

위 예제를 수행하는 것은 데이터가 2차원이고 그룹이 명확하게 구분되어 있었기 때문에 간단했습니다. 그러나 차원의 수가 증가하고 다양한 K 값이 고려될 때, 복잡성을 다루기 위한 알고리즘이 필요합니다.

## 단계 1: 초기 중심을 선택하세요 (랜덤으로)

<div class="content-ad"></div>

알고리즘을 초기 중심 벡터로 시드화해해야 합니다. 이는 데이터에서 무작위로 선택하거나 원본 데이터와 같은 차원의 무작위 벡터를 생성할 수 있습니다. 아래 이미지에서 하얀 다이아몬드를 확인해주세요.

![다이아몬드](/assets/img/2024-05-23-MasteringK-MeansClustering_2.png)

## 단계 2: 각 점에서 중심까지 거리 찾기

이제, 각 데이터 포인트에서 K 중심까지의 거리를 계산할 것입니다. 그리고 각 점을 해당 점에 가장 가까운 중심에 연관짓게 됩니다.

<div class="content-ad"></div>

데이터셋에는 N개의 항목과 M개의 피처가 있습니다. 센터 c까지의 거리는 다음 방정식에 의해 주어질 수 있습니다:

![equation](/assets/img/2024-05-23-MasteringK-MeansClustering_3.png)

여기서:

k는 1부터 K까지 변합니다.

<div class="content-ad"></div>

D는 점 n에서 k 센터까지의 거리입니다.

x는 점 벡터입니다.

c는 센터 벡터입니다.

따라서 각 데이터 포인트 n마다 K개의 거리가 있고, 그런 다음 가장 작은 거리로 센터에 대한 벡터를 레이블해야 합니다.

<div class="content-ad"></div>

![](/assets/img/2024-05-23-MasteringK-MeansClustering_4.png)

D는 K 거리를 가진 벡터입니다.

## 단계 3: K 중심점 찾기 및 반복

K 개의 클러스터 각각에 대해 중심점을 재계산합니다. 새로운 중심점은 해당 클러스터에 할당된 모든 데이터 포인트의 평균입니다. 그런 다음 중심점의 위치를 새로 계산된 위치로 업데이트하세요.

<div class="content-ad"></div>

이전 반복에서 중심접 중점이 크게 변했는지 확인하세요. 현재 반복에서 중심점의 위치를 이전 반복에서의 위치와 비교하여 확인할 수 있습니다.

중심점이 크게 변했다면, 단계 2로 돌아가세요. 변하지 않았다면, 알고리즘이 수렴했고 프로세스가 중지됩니다. 아래 이미지를 참조해주세요.

![이미지](https://miro.medium.com/v2/resize:fit:1280/1*trbuwKohsyn_SZrWrk7fKw.gif)

# 3. Python에서의 구현

<div class="content-ad"></div>

이제 K-Means 알고리즘의 기본 개념을 알게 되었으니, Python 클래스를 구현할 때입니다. 사용된 패키지는 수학적 계산을 위해 Numpy, 시각화를 위해 Matplotlib, 그리고 모의 데이터를 생성하기 위해 Sklearn의 Make_blobs 패키지였습니다.

```python
# 필요한 패키지 가져오기
import numpy as np
import matplotlib.pyplot as plt
from sklearn.datasets import make_blobs
```

이 클래스에는 다음 메소드가 포함될 것입니다:

- Init 메소드

<div class="content-ad"></div>

알고리즘의 기본 매개변수인 군집 값 k, 최대 반복 횟수 max_iter 및 최적화를 중지하는 허용 오차 tol 값을 초기화하는 생성자 메서드입니다.

- 보조 함수

이러한 방법은 훈련 중 최적화 프로세스를 지원하는 데 목적이 있으며, 유클리드 거리 계산, 초기 중심 임의 선택, 각 점에 가장 가까운 중심 할당, 중심값 업데이트 및 최적화가 수렴했는지 확인이 포함됩니다.

- Fit 및 predict 메서드

<div class="content-ad"></div>

앞서 말한 대로, K-Means 알고리즘은 지도 학습 기술이 아닌 비지도 학습 기술입니다. 이는 훈련 과정 중에 레이블이 지정된 데이터가 필요하지 않음을 의미합니다. 따라서 데이터를 적합시키고 각 데이터 포인트가 어떤 클러스터에 속하는지 예측하는 단일 방법이 필요합니다.

- 총 오류 방법

최적화의 품질을 평가하기 위해 최적화의 총 제곱 오류를 계산하는 방법입니다. 다음 섹션에서 자세히 알아볼 것입니다. 

다음은 전체 코드입니다:

<div class="content-ad"></div>

```js
class Kmeans:
    
    # 하이퍼파라미터 초기화를 위한 생성자 메소드
    def __init__(self, k=3, max_iter=100, tol=1e-06):
        self.k = k
        self.max_iter = max_iter
        self.tol = tol
  
    # 초기 중심점을 입력 데이터에서 무작위로 선택
    def pick_centers(self, X):
        centers_idxs = np.random.choice(self.n_samples, self.k)
        return X[centers_idxs]
    
    # 각 데이터 포인트에 대해 가장 가까운 중심점을 찾음
    def get_closest_centroid(self, x, centroids):
        distances = [euclidean_distance(x, centroid) for centroid in centroids]
        return np.argmin(distances)
    
    # 각 클러스터의 인덱스를 포함하는 리스트를 생성
    def create_clusters(self, centroids, X):
        clusters = [[] for _ in range(self.k)]
        labels = np.empty(self.n_samples)
        for i, x in enumerate(X):
            centroid_idx = self.get_closest_centroid(x, centroids)
            clusters[centroid_idx].append(i)
            labels[i] = centroid_idx

        return clusters, labels
    
    # 각 클러스터에 대해 평균 값을 사용하여 중심점을 계산
    def compute_centroids(self, clusters, X):
        centroids = np.empty((self.k, self.n_features))
        for i, cluster in enumerate(clusters):
            centroids[i] = np.mean(X[cluster], axis=0)

        return centroids
    
    # 중심점이 크게 변경되었는지 확인하는 헬퍼 함수
    def is_converged(self, old_centroids, new_centroids):
        distances = [euclidean_distance(old_centroids[i], new_centroids[i]) for i in range(self.k)]
        return (sum(distances) < self.tol)


    # 데이터를 학습하고 최적화된 중심점을 찾아 각 데이터 포인트를 클러스터에 따라 레이블링하는 메소드
    def fit_predict(self, X):
        self.n_samples, self.n_features = X.shape
        self.centroids = self.pick_centers(X)

        for i in range(self.max_iter):
            self.clusters, self.labels = self.create_clusters(self.centroids, X)
            new_centroids = self.compute_centroids(self.clusters, X)
            if self.is_converged(self.centroids, new_centroids):
                break
            self.centroids = new_centroids

    
    # 최적화의 클러스터 내 분산을 평가하는 메소드
    def clustering_errors(self, X):
        cluster_values = [X[cluster] for cluster in self.clusters]
        squared_distances = []
        # 총 제곱 유클리드 거리 계산
        for i, cluster_array in enumerate(cluster_values):
            squared_distances.append(np.sum((cluster_array - self.centroids[i])**2))

        total_error = np.sum(squared_distances)
        return total_error
```

# 4. 평가와 해석

이제 시뮬레이션 데이터의 클러스터링을 수행하기 위해 K-Means 클래스를 사용합니다. Sklearn 라이브러리의 make_blobs 패키지를 사용할 것입니다. 데이터는 4개의 고정된 중심점을 가진 500개의 이차원 점으로 구성됩니다.

```js
# 예시용 시뮬레이션 데이터 생성
X, _ = make_blobs(n_samples=500, n_features=2, centers=4, 
                  shuffle=False, random_state=0)
```

<div class="content-ad"></div>

이미지:
![마스터링K-Means 클러스터링](/assets/img/2024-05-23-MasteringK-MeansClustering_5.png)

네가 네가 이미지:
![클러스터링 결과](https://miro.medium.com/v2/resize:fit:1280/1*JBTi_DXtCz_NzOhDZzMEag.gif)

네가 네가 네가 네가 별 다섯 이미지:

훈련을 통해 네 개의 클러스터를 사용하여 다음과 같은 결과를 얻을 수 있습니다.

```js
model = Kmeans(k=4)
model.fit_predict(X)
labels = model.labels
centroids = model.centroids
plot_clusters(X, labels, centroids)
```

<div class="content-ad"></div>

그 경우 알고리즘은 18번의 반복을 통해 성공적으로 클러스터를 계산할 수 있었습니다. 그러나 시뮬레이션된 데이터에서 최적 클러스터 수를 이미 알고 있다는 점을 명심해야 합니다. 실제 응용 프로그램에서는 종종 그 값을 모르게 됩니다.

이전에 말했듯이, K-Means 알고리즘은 클러스터 내 분산을 가능한 한 작게 만드는 것을 목표로 합니다. 그 분산을 계산하는 데 사용되는 메트릭은 다음과 같은 총 제곱 유클리드 거리입니다:

![식](/assets/img/2024-05-23-MasteringK-MeansClustering_6.png)

여기서:

<div class="content-ad"></div>

클러스터 내 데이터 포인트의 수를 p로 표시하고;

클러스터의 중심 벡터를 c_i로 나타내며;

K는 클러스터의 수입니다.

위의 공식은 데이터 포인트와 가장 가까운 중심까지의 거리들을 합한 것입니다. K의 수가 증가할수록 오차가 줄어듭니다.

<div class="content-ad"></div>

에러를 클러스터 수에 대한 그래프로 그려서 그래프가 "접히는" 지점을 살펴보면, 최적의 클러스터 수를 찾을 수 있어요.

![Plot](/assets/img/2024-05-23-MasteringK-MeansClustering_7.png)

그래프가 "팔꿈치 모양"을 하고 있으며 K = 4에서 접히고 있으니, K의 값이 클수록 총 에러 감소가 덜 중요해질 것이라는 것을 볼 수 있어요.

# 5. 결론과 다음 단계

<div class="content-ad"></div>

이 글에서는 K-Means 알고리즘의 기본 개념, 사용 사례 및 응용 프로그램에 대해 다루었습니다. 또한 이러한 개념을 사용하여 시뮬레이션 데이터의 클러스터링을 수행하는 Python 클래스를 처음부터 구현했으며, scree 플롯을 사용하여 K의 최적 값을 찾는 방법을 알아보았습니다.

그러나 이는 비지도 학습 기술을 다루고 있기 때문에 한 가지 추가 단계가 있습니다. 알고리즘은 성공적으로 클러스터에 레이블을 할당할 수 있지만, 각 레이블의 의미는 데이터 과학자나 기계 학습 엔지니어가 각 클러스터의 데이터를 분석하여 수행해야 할 작업입니다.

또한, 추가로 탐구할만한 몇 가지 포인트를 남겨 드리겠습니다:
- 우리의 시뮬레이션 데이터는 이차원 점을 사용했습니다. 알고리즘을 다른 데이터 세트에도 적용하고 K의 최적 값을 찾아보세요.
- 계층적 클러스터링과 같은 다른 널리 사용되는 비지도 학습 알고리즘도 있습니다.
- 문제의 도메인에 따라 맨하탄 거리 및 코사인 유사성과 같은 다른 오류 지표를 사용해야 할 수도 있습니다. 이를 조사해 보세요.

<div class="content-ad"></div>

다음은 전체 코드가 있는 링크입니다:

코드를 자유롭게 사용하고 개선하고, 의견을 나누고, LinkedIn, X, 그리고 Github에서 저와 연락하십시오.

# 참고문헌

[1] Sebastian Raschka (2015), Python Machine Learning.

<div class="content-ad"></div>

[2] Willmott, Paul. (2019). Machine Learning: An Applied Mathematics Introduction. Panda Ohana Publishing.

[3] Géron, A. (2017). Hands-On Machine Learning. O’Reilly Media Inc.

[4] Grus, Joel. (2015). Data Science from Scratch. O’Reilly Media Inc.