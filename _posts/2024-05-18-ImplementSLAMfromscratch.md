---
title: "SLAM을 처음부터 구현해 보기"
description: ""
coverImage: "/assets/img/2024-05-18-ImplementSLAMfromscratch_0.png"
date: 2024-05-18 19:19
ogImage:
  url: /assets/img/2024-05-18-ImplementSLAMfromscratch_0.png
tag: Tech
originalTitle: "Implement SLAM from scratch"
link: "https://medium.com/machinevision/implement-slam-from-scratch-b1fb599f40c8"
---

SLAM (Simultaneous Localization and Mapping)을 위한 솔루션을 구현하는 다양한 방법이 있지만, 구현하기 가장 간단한 알고리즘은 Graph SLAM입니다.

Graph SLAM은 로봇공학에서 사용되는 기술로, 로봇의 궤적을 시간에 따라 동시에 추정하고 환경 안의 랜드마크 위치를 노드와 제약조건으로 나타내는 그래프입니다. 그래프는 로봇의 자세 및 랜드마크 위치를 나타내는 노드와 간격을 제약 조건으로 나타내는 에지로 구성됩니다. 제약 조건은 초기 위치, 상대 움직임 및 상대 측정 제약 조건과 같은 것들을 나타냅니다. 그래프를 최적화함으로써, Graph SLAM은 센서 측정을 가장 잘 설명하는 가장 확률적인 궤적과 랜드마크 위치를 찾으려고 합니다.

## 예제

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

Graph SLAM으로 파고들기 전에, Graph SLAM을 어떻게 구현할지 탐구하는 과정에서 도움이 될 예제를 소개하겠습니다. 이 예제에서는 하나의 차원적인 세계에서 로봇이 이동하는 상황을 살펴보겠습니다. 로봇의 첫 번째 자세는 시간 단계 t0에서이며, 로봇의 자세는 x=2입니다. 이 위치에서 로봇은 랜드마크 L0(예: 나무)를 보고 거리가 9단위 떨어져 있습니다. 그런 다음 로봇은 5단위만큼 앞으로 이동합니다. 이 시점에서 로봇은 x=7에 있어야 하며 랜드마크는 4단위 떨어져 있어야 합니다. 그러나 시간 단계 t1에서 로봇은 랜드마크까지의 거리를 보거나 측정하지 않습니다. 시간 단계 t1에서의 랜드마크 측정 부재는 센서 오류, 가리개, 또는 다른 이유로 인할 수 있습니다. 마지막으로, 로봇은 3단위 앞으로 이동하고 랜드마크를 1단위로 떨어져서 볼 수 있습니다. 이 시점에서 로봇은 x=10에 있어야 하며 랜드마크는 x=11에 있어야 합니다.

![이미지](/assets/img/2024-05-18-ImplementSLAMfromscratch_1.png)

## 제약 조건

Graph SLAM에서는 세 가지 중요한 유형의 제약 조건이 있습니다. 각각의 제약 조건을 자세히 살펴보겠습니다:

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

- 초기 위치 제약: 초기 위치 제약은 로봇이 환경 속 초기 위치에 대한 지식을 나타냅니다. 이는 로봇의 궤적 추정에 고정된 참조점을 제공합니다. 이 제약은 초기 위치 및 방향 추정을 캡처하기 위해 `x, y, θ`로 표현될 수 있습니다. 이 제약을 그래프에 통합함으로써 로봇의 궤적 추정을 기준으로 잡고 추가적인 제약 최적화를 위한 시작점을 제공할 수 있습니다.
- 상대 운동 제약: 상대 운동 제약은 연이은 시간 단계 간 로봇의 자세 변화에 대한 정보를 캡처합니다. 이러한 제약은 통상 휠 엔코더 또는 IMU와 같은 오도메트리 센서에서 얻어집니다. 오도메트리 센서는 로봇의 움직임에 대한 추정을 제공하며, 위치 및 방향의 변화와 같은 로봇의 움직임을 제공합니다. 연이은 시간 단계 간 오도메트리 측정을 비교함으로써 로봇의 이동을 기술하는 상대 운동 제약을 유도할 수 있습니다. 이러한 제약은 움직임 추정 값의 불확실성을 포착하는 가우시안 분포로 표현됩니다.
- 상대 측정 제약: 상대 측정 제약은 환경 속 서로 다른 랜드마크나 특징들 간의 상대 위치 또는 거리에 대한 정보를 캡처합니다. 이 제약은 레이저 거리계 또는 카메라와 같은 센서 측정을 통해 얻어집니다. 예를 들어, 로봇이 랜드마크를 관찰하고 해당 랜드마크까지의 거리를 측정한 경우, 이 정보는 상대 측정 제약으로 사용될 수 있습니다. 이러한 제약은 로봇의 궤적에 상대적으로 랜드마크 위치를 추정하는 데 도움을 줍니다.

그래프 SLAM에서는 이러한 모든 제약을 함께 사용하여 환경과 로봇의 궤적에 대한 그래프 표현을 구축합니다. 그래프는 서로 다른 시간 단계의 로봇 자세와 랜드마크 위치를 나타내는 노드 및 그들 사이의 제약을 나타내는 엣지로 구성됩니다.

이것은 테이블 태그를 마크다운 형식으로 변경한 것입니다.

이미지는 [여기](/assets/img/2024-05-18-ImplementSLAMfromscratch_2.png)에서 확인할 수 있습니다.

우리의 예제에서는 5개의 총 제약이 있습니다: 초기 위치 제약 1개, 상대 운동 제약 2개 및 상대 측정 제약 2개입니다. 4개의 상대 제약은 그래프 내의 엣지로 표시됩니다.

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

## 그래프 표현

그래프 SLAM에서 환경과 로봇의 궤적은 그래프 구조를 사용하여 표현됩니다. 그래프는 노드와 엣지로 구성되어 있으며, 노드는 로봇의 포즈(위치 및 방향)를 시간에 따라 다른 지점에서 나타냅니다. 엣지는 이러한 포즈 간의 제약 조건이나 측정값을 나타냅니다.

로봇의 포즈뿐만 아니라 환경에 있는 랜드마크나 특징을 나타내는 노드도 그래프에 포함됩니다. 이러한 랜드마크는 로봇이 인식하고 로컬리제이션 및 맵핑에 사용할 수 있는 객체, 관심 지점 또는 기타 독특한 특징일 수 있습니다.

그래프 표현은 엣지를 통해 로봇의 포즈와 랜드마크를 연결하여 센서로부터 얻은 측정값이나 제약 조건을 나타냅니다. 이러한 측정값에는 거리 측정, 방위 측정 또는 로봇과 랜드마크의 상대적인 위치와 방향에 대한 정보를 제공하는 기타 유형의 센서 데이터가 포함될 수 있습니다.

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

예를 들어, 로봇이 현재 자세에서 landmark를 관측한다고 가정해 봅시다. 이 관측은 그래프에서 로봇의 자세 노드와 landmark 노드 사이에 엣지를 생성합니다. 이 엣지는 센서로부터 얻은 측정값을 나타내며, 이를 통해 로봇과 landmark 간의 상대적인 위치와 방향에 대한 정보를 제공합니다.

이러한 측정값을 그래프에 통합함으로써 SLAM 알고리즘은 로봇의 가장 가능성 있는 궤적과 측정값에 의해 적용된 제한 조건을 가장 잘 만족하는 환경 지도를 추정할 수 있습니다. 그래프 최적화 과정은 예측된 측정값과 센서로부터 실제로 얻은 측정값 간의 오차를 최소화하기 위해 그래프 내의 자세와 landmark 위치를 조정하는 것을 포함합니다.

## 행렬과 벡터 표현

그래프 SLAM에서는 로봇의 자세와 landmark 간의 관계를 모델링하기 위해 행렬과 벡터 표현을 사용합니다. 이러한 표현은 SLAM 문제를 해결하는 데 도움이 됩니다.

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

행렬 표현부터 시작해봅시다. Graph SLAM에서 우리는 정보 행렬이라고 하는 행렬을 만듭니다. 이 행렬은 Ω (오메가)로 표시되며 서로 다른 변수들 간의 제약 조건이나 관계를 나타냅니다. 각 변수는 지도상의 로봇 pose나 landmarke에 해당합니다.

정보 행렬은 정사각 행렬이며, 그 크기는 SLAM 문제에서 변수의 수에 따라 달라집니다. 그래프에 n개의 노드가 있는 Graph SLAM 문제의 경우, n x n 크기의 정보 행렬을 갖게 됩니다. 이 행렬의 요소들은 변수들 간의 관계에 대한 정보를 인코딩합니다. 예를 들어, 두 변수가 높은 상관 관계를 가진 경우, 정보 행렬의 해당 요소는 더 높은 값을 갖게 됩니다.

이제 벡터 표현으로 넘어가 봅시다. Graph SLAM에서 우리는 정보 벡터라고 하는 벡터를 생성합니다. 이것은 ξ (크싸이)로 표시되며, SLAM 문제에서 우리가 한 측정치나 관측치를 나타냅니다. 벡터의 각 요소는 특정 측정치나 관측치에 해당합니다. 그래프에 n개의 노드가 있는 Graph SLAM 문제의 경우, n x 1 크기의 정보 벡터를 갖게 됩니다.

정보 벡터에는 SLAM 문제의 측정치와 변수들과의 관계에 대한 정보가 포함되어 있습니다. 이는 우리가 측정치를 SLAM 문제에 통합하고 로봇 포즈와 랜드마크의 추정치를 업데이트하는 데 도움이 됩니다.

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

저희 예제에서는 그래프에 4개의 노드가 있으므로 4 x 4 행렬을 초기화합니다. 다음은 정보 행렬입니다:

```js
// 0으로 채워진 4x4 행렬
     + -- + -- + -- + -- +
     | t0 | t1 | t2 | L0 |
+ -- + -- + -- + -- + - -+
| t0 | 0  | 0  | 0  | 0  |
| t1 | 0  | 0  | 0  | 0  |
| t2 | 0  | 0  | 0  | 0  |
| L0 | 0  | 0  | 0  | 0  |
```

저희 예제에서는 또한 그래프에 4개의 노드가 있으므로 4 x 1 벡터를 초기화합니다. 다음은 정보 벡터입니다:

```js
// 0으로 채워진 4x1 벡터
+---+
| 0 |
| 0 |
| 0 |
| 0 |
+---+
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

## 그래프 SLAM 알고리즘

우리가 정보 행렬과 벡터를 선언하면, 초기 위치 제약을 행렬과 벡터에 적용해야 합니다. 예를 들어, 초기 위치인 2를 사용하여 정보 행렬을 업데이트하려면, 간단한 선형 방정식을 만들 것입니다:

이제 우리의 간단한 선형 방정식과 그 계수 `1,0,0,0;2`를 가지고 t0에 해당하는 행에 추가해봅시다:

```js
// 오메가 행렬 (결과)
     + -- + -- + -- + -- +
     | t0 | t1 | t2 | L0 |
+ -- + -- + -- + -- + - -+
| t0 | 1  | 0  | 0  | 0  |
| t1 | 0  | 0  | 0  | 0  |
| t2 | 0  | 0  | 0  | 0  |
| L0 | 0  | 0  | 0  | 0  |
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

```js
// Xi vector (결과)
+---+
| 2 |
| 0 |
| 0 |
| 0 |
+---+
```

일반화하기 위해 여기에 초기 의사 코드가 있습니다:

```js
void GraphSLAM(G, startPose) {
    // Omega와 Xi 선언
    Omega = new Matrix(n,n)
    Xi = new Vector(n)

    // 초기 위치 제약
    Omega['t0','t0'] = 1
    Xi['t0'] = startPose

    // 그래프 최적화
    Mu = GraphOptimization(Omega, Xi, G)
    return Mu
}
```

여기서부터 그래프 최적화에 대해 논의해야 합니다.

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

## 그래프 최적화

그래프의 초기 매트릭스 및 벡터 표현이 준비되면, 그래프 최적화를 수행해야 합니다. 그래프 SLAM에서의 그래프 최적화는 센서 측정을 기반으로 그래프를 반복적으로 업데이트하여 로봇 자세와 랜드마크 위치의 추정치를 개선하는 과정입니다. 이 과정은 측정 업데이트와 상태 업데이트라는 두 가지 주요 단계로 구성됩니다.

- 측정 업데이트: 측정 업데이트 단계에서는 그래프의 엣지를 반복하며 정보 매트릭스에 제약 조건을 추가합니다. 이러한 제약 조건은 센서에서 얻은 측정치(예: 거리 측정 또는 방향 측정)를 나타냅니다.
- 상태 업데이트: 상태 업데이트 단계에서는 선형 방정식 체계를 해결하여 그래프의 오차를 최소화하는 최적 로봇 자세와 랜드마크 위치를 추정합니다. 이는 정보 매트릭스의 역행렬을 취하고 정보 벡터와 곱하여 수행됩니다.

다음은 그래프 최적화를 위한 고수준의 의사 코드 예시입니다:

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
void GraphOptimization(Omega, Xi, G) {
    Omega, Xi = MeasurementUpdate(Omega, Xi, G);
    Mu = StateUpdate(Omega, Xi);
    return Mu;
}
```

측정 및 상태 업데이트에 대해 더 자세히 알아보겠습니다.

## 측정 업데이트

측정 업데이트에서는 그래프 데이터를 사용하여 정보 행렬 및 벡터 데이터를 정의합니다. Omega는 선형 방정식의 계수를 나타내는 정보 행렬이고, Xi는 해당 방정식의 상수항을 나타내는 정보 벡터입니다. G는 측정치(예: 거리)를 나타내는 엣지 가중치를 포함하는 그래프입니다.

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

각 모서리는 일련의 선형 방정식을 정의하는 데 도움이 됩니다. 예를 들어, 모서리 t0-t1로 정보 행렬을 업데이트하려면 두 개의 선형 방정식을 만들겠죠:

이제, 첫 번째 선형 방정식과 그 계수 `1,-1,0,0;-5`를 가져와서 t0에 해당하는 행에 추가해봅시다:

```js
// 오메가 행렬 (결과)
     + -- + -- + -- + -- +
     | t0 | t1 | t2 | L0 |
+ -- + -- + -- + -- + - -+
| t0 | 2  |-1  | 0  | 0  |
| t1 | 0  | 0  | 0  | 0  |
| t2 | 0  | 0  | 0  | 0  |
| L0 | 0  | 0  | 0  | 0  |
```

```js
// 시 벡터 (결과)
+---+
|-3 |
| 0 |
| 0 |
| 0 |
+---+
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

이제 두 번째 선형 방정식의 계수인 `-1, 1, 0, 0; 5`를 가져와 t1에 대응하는 열에 추가해 봅시다:

```js
// 오메가 행렬 (결과)
     + -- + -- + -- + -- +
     | t0 | t1 | t2 | L0 |
+ -- + -- + -- + -- + - -+
| t0 | 2  |-1  | 0  | 0  |
| t1 |-1  | 1  | 0  | 5  |
| t2 | 0  | 0  | 0  | 0  |
| L0 | 0  | 0  | 0  | 0  |
```

```js
// 크시 벡터 (결과)
+---+
|-3 |
| 5 |
| 0 |
| 0 |
+---+
```

일반적인 상황을 이해하기 위해 의사 코드를 보여 드리겠습니다:

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
void MeasurementUpdate(Omega, Xi, G) {
    for each edge:
        // LinEq 1: src - dst = -weight
        Omega[edge.src, edge.src] += 1
        Omega[edge.src, edge.dst] += -1
        Xi[edge.src] += -edge.weight
        // LinEq 2: dst - src = weight
        Omega[edge.dst, edge.dst] += 1
        Omega[edge.dst, edge.src] += -1
        Xi[edge.dst] += edge.weight
    return Omega, Xi
}
```

보시다시피, 측정 업데이트 프로세스는 그래프 G의 각 엣지에 대해 반복하는 것을 포함합니다. 각 엣지마다 코드는 두 단계를 수행합니다. 먼저, 엣지의 원본 노드에 해당하는 행에 대한 Omega 및 Xi 값을 업데이트합니다. 그 다음, 엣지의 대상 노드에 해당하는 행에 대한 Omega 및 Xi 값을 업데이트합니다. 두 단계 모두 엣지의 가중치는 두 노드 사이(예: 거리)의 측정을 나타냅니다.

이러한 단계는 측정에 의해 부과된 제약 조건이 Omega 및 Xi 행렬에 올바르게 표현되도록 보장합니다. 모든 엣지를 반복한 후 함수는 업데이트된 Omega 및 Xi 행렬을 반환합니다.

## State Update

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

이 시점에서, Omega와 Xi를 완전히 정의했습니다. 그러니 Mu를 해결하기 위해 방정식 체계를 해결하기만 하면 됩니다:

여기서 Mu는 업데이트된 상태 추정을 나타냅니다. Mu를 구하기 위해서는 단순히 Omega를 역행렬로 변환해야 합니다:

```js
void StateUpdate(Omega, Xi) {
    Mu = Omega.invert() * Xi
    return Mu
}
```

제공된 의사 코드는, 공분산 행렬(Omega)의 역행렬을 측정 벡터(Xi)로 곱하여 상태를 업데이트합니다. 결과인 Mu는 로봇의 자세 및 랜드마크 위치의 상태 추정을 나타냅니다. 이는 시스템의 상태를 정의하는 모든 변수의 값이 포함된 벡터입니다. 우리의 예시에서, 이는 Mu의 예상 값입니다:

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
// Mu vector (result)
+----+
|  2 |
|  7 |
| 10 |
| 11 |
+----+
```

이 예에서 첫 번째 요소는 t0에서 로봇의 위치이며, 두 번째 요소는 t1에서 로봇의 위치이고, 세 번째 요소는 t2에서 로봇의 위치이며, 네 번째 요소는 landmark(L0)의 위치입니다.

## 토론

Graph SLAM 알고리즘은 정확한 답변을 제공하지 않을 수 있지만, 근접한 결과를 제공합니다. SLAM 알고리즘의 결과는 알고리즘에 공급되는 측정값의 품질에 따라 달라집니다.

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

위 내용은 간단한 1차원 예시에 불과했습니다. 이를 쉽게 3차원 공간으로 확장할 수 있고 로봇이 바라보는 방향을 설명하는 추가적인 방향 차원도 포함할 수 있습니다.

만약 SLAM 용어 중 이해되지 않는 것이 있다면, SLAM 개요를 다시 참고해 주세요.

이 글이 마음에 드셨다면, ❤를 눌러 다른 사람들이 이를 발견하는 데 도움을 주세요!
