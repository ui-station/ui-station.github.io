---
title: "하이브리드 A star에 대해 부드럽게 소개하기"
description: ""
coverImage: "/assets/img/2024-05-20-GentleintroductiontoHybridAstar_0.png"
date: 2024-05-20 20:01
ogImage:
  url: /assets/img/2024-05-20-GentleintroductiontoHybridAstar_0.png
tag: Tech
originalTitle: "Gentle introduction to Hybrid A star"
link: "https://medium.com/@junbs95/gentle-introduction-to-hybrid-a-star-9ce93c0d7869"
---

![Hybrid A*](/assets/img/2024-05-20-GentleintroductiontoHybridAstar_0.png)

하이브리드 A\*는 자동차 형태의 로봇들을 위한 가장 인기 있는 경로 계획 알고리즘 중 하나로 간주됩니다. 이는 현재 웨이모(Waymo), 구글의 자율 주행 자동차 프로젝트의 공동 최고 경영자로 있는 Dmitri Dolgov가 개발했습니다.

학문적으로, 하이브리드 A\*는 DARPA 챌린지에서 우수한 성능을 보여주면서 1000회 이상 인용되었습니다. 심지어 2023년에도 이 방법은 자동차의 운동 계획을 위한 주요한 프론트엔드 알고리즘 중 하나로 자주 사용됩니다(예: Bai Li나 Zhang의 논문들).

이 논문을 처음 읽었을 때 격자 형태의 도메인에서 확장해야 해서 이해하기 어려웠습니다. 이 글은 여러분이 이 원칙과 특성을 이해하는 데 도움이 되도록 작성되었습니다.

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

어떤 방식으로 구현된 코드들을 찾을 수 있습니다.

- KTH 논문 (ROS1) (제가 개인적으로 이 버전을 조사했습니다).
- nav2 (ROS2)
- Unity
- Python
- MATLAB

## 먼저, Djkstra와 A\*

하이브리드를 이해하기 위해 먼저 Djkstra 알고리즘과 A\* 알고리즘을 복습해보세요.

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

## 1. Djikstra

Djikstra 알고리즘을 올바르게 이해하는 방법은 가장 짧은 경로 트리를 찾아내기 위해 "역전파" 하는 것으로 생각하는 것입니다. 이 알고리즘의 목적은 단일 목표에 대한 최단 경로를 찾는 것이 아닙니다! 모든 가능한 노드에 대해 찾아냅니다.

![image](https://miro.medium.com/v2/resize:fit:1114/1*43bXSP_BVTTfpm0QgxO2Qg.gif)

위의 애니메이션에서 회색 원은 시작(파란 점)에서 셀(즉, 노드)까지의 최단 경로를 계산했다는 것을 나타냅니다. 경로 찾기처럼 보이는 이유는, 구현이 목표에 도달했을 때 전파를 종료했기 때문입니다.

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

따라서, 검색은 단일 목표와는 아무 관련이 없습니다.

## 2. A\*

로봇 연구자들이 대부분의 시간을 보내는 작업은 경로를 "단일" 목표로 찾는 것입니다 (목표는 변경될 수 있음에도). 이를 위해, 로봇 연구자들은 Djikstra 알고리즘을 수정하여 단일 목표로 향하도록 만들려고 노력했습니다.

![이미지](https://miro.medium.com/v2/resize:fit:1114/1*ugjVvXCDjAoykSSEUiRUXA.gif)

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

검색을 안내하기 위해 두 가지 용어를 기반으로 "예상대로" 최적의 방향으로 진행합니다:

- 지금까지의 비용(이미 결정됨): 이 셀이 시작점부터 이 지점에 도달할 때까지 더 낮은 비용이 누적되었나요?
- 목표 지점까지의 비용(정확히 알지 못 함): 이 셀의 우수성을 목표 지점에 도달하기 위한 선호도로 얼마나 낙관적으로 추정하나요?

첫 번째 용어는 종종 g(x)로 표시되고 후자는 h(x)로 표시됩니다.후자를 휴리스틱이라고 부릅니다. 탐욕스럽게 다음 x*next를 검색합니다: 탐색 큐 내에서 g(x*'next') + h(x\_'next')가 가장 낮은 값.

## 휴리스틱 함수의 합리성

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

h(x)에 의존하는 안내형 검색을 수행하기 때문에 이 함수는 신중하게 설계되어야 합니다. 전역 최적해를 찾기 위해서는 휴리스틱에 대한 필수 조건이 있습니다. 그것은 목표까지의 예상 비용을 최상으로 낙관적으로 출력해야 한다는 것입니다. 그리드에서 A\*를 사용하는 경우, 이 함수는 일반적으로 x에서 목표까지의 유클리드 거리로 선택됩니다.

이 선택의 이유는 우리가 최선의 해인 전역 최적해를 발견하는 기회를 놓치지 않기 위함입니다. 이 함수가 이 조건을 충족하지 않으면 A가 (전역 최적해를 나타내는 표기법)임이 보장되지 않습니다.

## 3. 운동학을 반영한 합성 A\*

그리드 영역에서 A* 알고리즘이 컴퓨터 게임을 포함한 다양한 응용에 잘 작동합니다. 그러나 이러한 그리드에서의 안내형 검색은 자동차와 같은 비할모논 시스템에 적용하려고 할 때 제한이 있습니다. A*의 출력이 그리드에서 전역적으로 최적일 수 있지만, 실제 추적 시 경로는 역학에 의해 추적될 때 비효율적일 수 있습니다.

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

![Hybrid A* explained](/assets/img/2024-05-20-GentleintroductiontoHybridAstar_1.png)

위의 예에서 파란색 직선이 x\_'g'에 도달하는 전역 최적이지만, 자동차의 초기 헤딩 때문에 경로를 정확히 추적할 수 없습니다. 즉, A*의 결과가 유용하지 않을 수 있습니다. 원래 논문에서는 이 문제를 어떻게 해결할 수 있을까 고민했습니다. 자동차의 키네마틱스를 고려하면서 A*와 같은 경로 탐색을 안내할 수 있을까요?

# Hybrid A\* 설명

본 섹션에서는 하이브리드 A\*에 대해 설명합니다. 저는 Dimitri의 원래 논문을 기반으로 설명하겠습니다.

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

# 1. 목적

일반적인 A*은 R²의 위치 x₀에서 R²의 목표 위치 x_f까지 가장 짧은 경로를 찾으려고 합니다 (물론 세 가지 차원으로도 사용할 수 있습니다. 그러나 간단하게 하기 위해 2차원으로 제한합니다). 그러나 이번에는 하이브리드 A*가 SE(2)에서 안전한 자세의 연속체를 찾으려고 노력할 것입니다. 여행 길이를 최소화하면서 SE(2)에서 s₀ = (x₀, θ₀)에서 s₀ = (x_f, θ_f)으로의 시퀀스를 찾으려고 노력할 것입니다. 따라서, 검색하는 요소의 도메인은 SE(2)입니다.

## 운동학적 모델

논문에서 모델은 아래와 같이 자동차처럼 이동한다고 가정됩니다:

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

<img src="/assets/img/2024-05-20-GentleintroductiontoHybridAstar_2.png" />

논문에서 다음 단계 n+1로의 확장은 세 가지 중 하나를 따릅니다: 최대 곡률 두 개 (왼쪽 / 오른쪽)과 직선. 따라서 어떤 두 상태 s_a와 s_b 사이의 가장 짧은 이동은 Dubins 경로를 통해 해석적으로 계산될 수 있습니다.

이 모델은 역 이동을 갖도록 확장할 수 있으며, 이로써 여섯 가지 가능한 확장이 생깁니다. 이 경우, Reed Shepp을 따라 가장 짧은 경로로 어떤 두 상태도 연결할 수 있습니다. 물론, 이 논문과 같이 시나리오에 맞게 확장 모델을 확장시킬 수 있습니다.

# 2. 확장 (opening)

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

상태 x_n \in R²가 A 그리드 필드에서 전파될 때, 우리는 이웃한 셀들을 열었습니다. 이는 그리드를 따라 이동하는 것과 같습니다. 또한, 상태들은 각 셀의 중앙에 해당됩니다. 이것들은 하이브리드 A에서 다릅니다.

## 셀 열기

![이미지](/assets/img/2024-05-20-GentleintroductiontoHybridAstar_3.png)

하이브리드 A\*에서 상태 s*n = (x_n, \theta_n)의 전파는 위의 그림과 같이 운동학을 시뮬레이션하여 새로운 셀들을 엽니다. 따라서, 새로운 상태 s'n+1' = (x*'n+1', \theta\_'n+1')은 거의 중앙에 도달하지 못할 수 있습니다. (이를 흰색 원으로 보여주려고 노력했습니다).

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

## Cost-so-far g(s)

각 상태 s*'n+1'의 비용을 계산하는 것은 비교적 유사합니다. 우리는 s_n과 s*'n+1' 사이의 아크의 길이를 고려하여 비용을 결정합니다. 차이점은 우리가 덜 원하는 움직임에 패널티를 부여할 수 있는 능력에 있습니다.

예를 들어, 위의 그림에서 턴 움직임 (s*'n+1'² 및 s*'n+1'³)에 대해 직진 움직임 (s\_'n+1'¹)보다 더 높은 패널티를 부과할 수 있습니다. 또한, 확장을 위해 역방향 움직임을 사용할 때, 역방향 움직임도 더 높은 패널티를 부담하게 됩니다.

## 가지치기

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

이 시점에서 혼란스러울 것입니다: 만약 두 개 이상의 상태가 동일한 각도 구간에 있는 동일한 셀을 공유한다면 어떻게 해야 할까요? 여기서 가지치기가 등장합니다.

![이미지](/assets/img/2024-05-20-GentleintroductiontoHybridAstar_4.png)

성능상의 이유로, 동일한 이산화(동일한 2D 셀 및 각도 구간) 내 다른 상태가 지금까지 더 낮은 비용을 얻었다면 상태를 제거합니다. 위 그림에서, 회전 이동이 더 벌점을 받는다고 가정하면, 빨간 원은 두 회전 이동을 거치기 때문에 가지치기될 것입니다. 물론, 이 가지치기는 각도 이산화에 의해 각도가 다르게 처리된다면 일어나지 않습니다.

# 3. 휴리스틱 h(s)

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

위 섹션에서는 상태 s\_'n'에서 셀을 확장하고 열어보는 방법을 설명했습니다. 원래의 A\*와 마찬가지로 현재 반복에서 휴리스틱 플러스 현재까지의 비용에 기반하여 전파할 최상의 상태 s_n을 선택해야 합니다.

## 비비학적 휴리스틱

우리가 논의한 대로, 휴리스틱은 타당할 정도로 낙관적이어야 합니다. 휴리스틱의 개념은 A\* 그리드와 유사합니다. 목표까지의 기대 최단 경로를 의미합니다. 그러나 물론 여기서 목표는 위치 도메인이 아닌 SE(2)에 있습니다.

제가 여기서 말했듯이, 다른 포즈에서 포즈로 도달하는 것은 Dubins 경로나 Reeds Shepp 경로로 달성할 수 있습니다. 그렇다면 이것을 사용할 수 있을까요? 타당하지만 장애물을 고려하여 남은 거리를 보다 정확하게 추정할 수 있습니다. Karl의 멋진 사진을 살펴보세요.

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

![약간 Hybrid A*에 대한 친절한 소개](/assets/img/2024-05-20-GentleintroductiontoHybridAstar_5.png)

이 그림에서는 미래 비용을 과대 평가할 수 있습니다. 이는 우리가 잘못된 방향(이 경우에는 벽으로 향함..)으로 전파를 낭비할 수 있습니다. 이를 방지하기 위해 위치적 영역에서 가장 짧은 경로를 계산하여 도움을 줄 수 있습니다.

## 장애물 고려한 미치적 경향

이 경우에 그리드 방식으로 가장 짧은 경로를 어떻게 알 수 있을까요? 원래 저자들은 동적 프로그래밍과 같은 내용을 제외하고 자세히 언급하지 않습니다. 구현에 따라 이 부분이 다르다는 것을 발견했습니다.

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

예를 들어, nav2의 Smac 플래너는 Dijkstra와 유사한 다이나믹 프로그래밍을 사용했습니다. 그러나 목표지점부터 출발지점이 아닌 가장 짧은 거리를 전파하는 방식입니다. 자세한 내용은 여기에서 확인해주세요. Karl의 구현은 목표 위치에 도달할 때까지 A\*를 실행합니다. 어떤 방식이든지 진행하려면 두 가지 휴리스틱을 사용하여 2D 그리드에서 작업해야 하며, 계산을 줄이기 위해 다운샘플링 해상도를 적용해야 할 수 있습니다.

## Hybrid A\*의 휴리스틱

두 휴리스틱 중 최대값을 우리 최종 휴리스틱 h(s)로 선택합니다. 다음 확장을 위해 g(s) + h(s)의 최소값을 갖는 최적의 상태 s\_'next'를 선택합니다. 최적의 선택은 우선순위 큐에서 팝하면 자동으로 처리됩니다. 이 데이터 구조에 익숙하지 않다면, 꼭 공부해보세요. A\* 알고리즘을 구현하는 데 꼭 필요한 개념입니다.

# 4. 분석적 확장 (즉, 목표로의 슛)

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

이 과정은 최종 결과에 큰 영향을 미치는 중요하고 독특한 측면입니다.

## 최적의 경로를 탑재하는 최고의 노드는 무엇일까요? 그것이 해결책입니다!

우리의 휴리스틱 가이드 방법론에서는 욕심쟁이 서치 방식을 채택하여 대기열에서 최고의 후보를 우선시합니다. 실제로 최적의 해결책을 얻을 수 있습니다. 만약 최고의 후보를 충돌 없이 목표지점에 직접 연결할 수 있다면요. 최고의 후보는 가장 낙관적인 행동을 보여주며, 이는 이상적인 선택입니다.

예를 들어, A\* 검색 알고리즘에서, 만약 마지막으로 팝된 노드와 목표 지점을 충돌 없는 직선으로 연결할 수 있다면, 우리는 해결책을 찾았습니다. 안타깝게도 충돌 가능성이 있기 때문에, 검색 과정 중에는 이 작업을 시도하지 않습니다.

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

## 가장 낙관적인 접근법을 시도해 보세요!

하이브리드 A\*에서는 유망해 보일 때에 이 직선적인 목표로의 접근법을 탐색하는 기회를 살펴봅니다. 탐색 중에는 최적의 후보 자세와 목표 자세를 Dubins 경로(또는 역모션을 고려할 때 Reeds-Shepp 경로)로 연결하려고 노력합니다.

이러한 접근법이 충돌 없이 성공하면 탐색을 종료하고 최적의 해를 찾은 것으로 간주됩니다. 이 기술은 원본 논문에서 "해석적 확장"이라고 합니다. 이러한 접근법의 성공은 하이브리드 A\* 탐색의 실시간 효율성에 크게 기여합니다. 장애물이 없는 영역에서는 하이브리드 탐색이 몇 번의 반복으로 완료되는 경우가 많습니다. 본질적으로 이는 시작 위치에서 목표 자세로 직접 Dubins 경로를 푸는 것과 동등해집니다.

## 희소 영역에서는 작동하지만 밀집된 지역에서는 그렇지 않습니다.

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

장애물이 많은 지역에서는 촬영이 실패할 수 있습니다. 이러한 경우, 탐색은 장애물 휴리스틱을 활용하여 탐사를 안내합니다. 빈 공간을 만나면 성공적인 샷을 활용하여 탐색을 신속히 종료할 수 있습니다.

![이미지](/assets/img/2024-05-20-GentleintroductiontoHybridAstar_6.png)

제공된 그림에서는 트리를 확장하는 도중 갑자기 탐색 프로세스가 종료되었는데, 이는 성공적인 샷이 최적 후보와 목표 위치를 충돌 없이 연결했기 때문입니다.

# 5. 분석

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

## 당신의 실제 로봇을 위한 최적성

하이브리드 A\*: 널리 인기를 얻은 전역 최적 경로 탐색 알고리즘이지만, 다음과 같은 이유로 실제로 전역 최적적인 것은 아닌 한계가 있습니다:

## 제어 이산화

우리 차의 제어 메커니즘은 Dubins 경로와 같이 세 개(또는 여섯 개)의 조향 모드에 기반한 것이 아닙니다. Dubins 경로에서 가정된 것과는 달리, 우리 로봇은 특정 사전 정의된 회전 아크를 따르지 않습니다. 대신, 연속적으로 회전 아크를 변경할 수 있는 유연성이 있습니다. 이는 우리 차의 움직임에 대한 입력이 Dubins 경로 가정에 엄격하게 따라가는 대신 이산적으로 고려된다는 것을 의미합니다.

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

## 노드 가지치기

하이브리드 A\* 알고리즘에서는 특정 셀의 노드를 "지금까지의 비용"만을 고려하여 가지치기할 수 있습니다. 그러나 이 과정에서 가지치기된 노드들이 더 나은 경로로 이어질 수도 있습니다. 지금까지의 비용만을 기반으로 한 이 기믹적 가지치기 방식은 최적 결과를 얻는데 제약을 줄 수 있습니다. 이런 한계를 극복하기 위해 가지치기 과정에서 휴리스틱 정보를 고려하는 것이 전체 경로의 최적성을 향상시킬 수 있습니다.

요약하면, 하이브리드 A\*는 강력한 경로 탐색 알고리즘이지만 차량의 제어 입력이 이산적이고 지금까지의 비용만을 고려한 기믹적 가지치기로 인해 실제 전역 최적성이 제약되어 있습니다. 이러한 제약을 극복하기 위해 더욱 진보된 제어 메커니즘과 휴리스틱을 고려한 가지치기 전략 등을 통해 알고리즘의 최적성을 높일 수 있습니다.

## 성능

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

실시간 응용 프로그램에 이 알고리즘을 적용해도 괜찮습니다. 합리적으로 복잡한 환경에서 msecs 계산 시간 순서를 보여줍니다. 더 자세한 정보는 nav2 구현에서 이 결과를 읽는 것을 권장합니다.
