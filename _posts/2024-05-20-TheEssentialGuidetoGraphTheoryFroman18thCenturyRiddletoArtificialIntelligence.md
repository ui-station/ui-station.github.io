---
title: "그래프 이론 필수 가이드 18세기 수수께끼부터 인공 지능까지"
description: ""
coverImage: "/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_0.png"
date: 2024-05-20 20:46
ogImage: 
  url: /assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_0.png
tag: Tech
originalTitle: "The Essential Guide to Graph Theory: From an 18th Century Riddle to Artificial Intelligence"
link: "https://medium.com/towards-data-science/the-essential-guide-to-graph-theory-from-an-18th-century-riddle-to-artificial-intelligence-c441cb9400de"
---


![The Essential Guide to Graph Theory: From an 18th Century Riddle to Artificial Intelligence](/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_0.png)

안녕하세요! 1736년 프러시아 케니흐스베르크의 번화한 거리를 떠나 떠돌아 보세요. 지금은 러시아 칼리닌그라드로 알려진 이 번창한 항구는 문화적으로도 건축적으로도 경이로운 곳입니다. 프레겔 강 둑을 따라 서성이며 이 도시의 중요한 동맥 중 하나인 이 강은 재주꾼 상인선박들의 먼 바다 속에서 멀리 분주한 시장의 떠들썩함과 만나게 됩니다. 이 강을 가로지르는 일곱 개의 멋진 다리들이 다양한 섬과 동네들을 연결하고 있습니다. 여러분이 걷는 길이 바로 유럽 대륙의 가장 예리한 두뇌들을 괴롭히고 있는 수학적 수수께끼의 기초가 되었다는 사실을 아셨나요.

이곳을 가로지르면서 각 다리를 정확히 한 번씩 건너는 것이 가능한지에 관한 문제를 묻고 있는 것입니다.

![The Essential Guide to Graph Theory: From an 18th Century Riddle to Artificial Intelligence](/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_1.png)

<div class="content-ad"></div>

처음에는 그 문제를 진부하다고 생각했지만, 결국 신생 수학 부문 관리자인 29살의 스위스 수학자 레온하르트 오일러가 러시아 성페테르스부르크 과학 아카데미에서의 끌림을 이기지 못했어. 그 오일러야, "오일러의 수"로 유명한 그 오일러야 (이거 떠올리기 즐거우신 상수 e, ≈2.71828로 기억하시겠지). 함수, 합, 그리고 허수 표기법 (f(x), Σ, 그리고 i 각각)을 선보인 그 같은 오일러야, 삼각함수 sin과 cos, 지수 함수 사이의 관계를 보여주는 항등식을 세운 그 대 그 오일러야. 오일러는 버노우이, 가우스, 뉴턴, 라이프니츠와 마찬가지로 여러 분야에 영향을 미친 불안한 수학자 중 하나였지. 현대 과학과 수학 원리를 튼튼하게 다지면서 영원히 그들을 바꿔 놓은 그 오일러야가 바로 그랬어.

만약 이미 좋아하는 수학자가 없다면, 적어도 상위 10명에는 오일러를 고려해 보길 권해드려. 그의 영향력을 과소평가하기 어려운 점이 많아. 그는 분명히 알려진 분야로 진출하고 수학과 과학 지식의 영역을 확장했지만, (논란의 여지는 있지만) 그의 가장 오래된 유산은 복잡한 개념을 단순화하는 데 있어. 오일러가 주요 직관적 개념과 표기법을 도입함으로써 어려운 주제를 보다 직관적으로 다가갈 수 있도록 도왔고, 앞으로 몇 세기 동안의 혁신을 허용하기에 더 나은 단순화를 이룰 수 있도록 했지. 어떤 면에선, 오일러는 머리카락을 잡아끌 수 있는 수학적 고난을 잊기 어렵게 만들었던 소리 없는 영웅인 셈이지 — 거기에 뭔가, 감히 말해보자면 즐거운(?) 게임이 되었던 고난을 —.

<div class="content-ad"></div>

알겠어요. 필수 오일러 감상 랜트를 끝냈으니... 이제 재미있는 내용으로 넘어가 봅시다.

## 절삭 “에지” — 그리고 “버텍스” — 솔루션

오일러의 채티인버그 다리 문제에 대한 혁신적인 접근은 그의 추상적 사고 능력을 드러냈습니다. 그는 대지내의 구체적인 경로보다 다리의 연속이 더 중요하다고 인식했습니다. 이 통찰력을 통해 그는 다리를 연결하는 "에지"로 표시된 노드인 지형을, 효과적으로 그래프를 만들어 주는 "버텍스"로 나타낸 문제로 변환할 수 있었습니다(그림 3을 참조하세요).

![그림](/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_2.png)

<div class="content-ad"></div>

오일러는 그 후에 지금은 "handshaking lemma"로 알려진 것을 적용했습니다: 각 다리는 양쪽 땅을 연결하기 때문에 두 번씩 세어집니다. 이는 각 땅에 연결된 다리의 총합이 짝수여야 한다는 것을 의미합니다. 다리의 총 숫자의 두 배입니다. 그러나, 홀수 개의 다리를 가진 홀수 개의 땅이 있는 경우, 통행 가능한 경로가 없게 됩니다.

더 깊이 분석해 보니, 오일러는 이러한 경로가 존재하려면 홀수 개의 다리를 가진 땅이 없거나 두 개여야 한다는 것을 확인했습니다. 케ーニ흐스베르크의 경우, 네 개의 땅 모두 홀수 개의 다리를 가졌기 때문에 각 다리를 정확히 한 번씩 건너는 경로가 불가능했습니다. 이 결론은 지금 그래프 이론에서의 오일러 경로로 이해하게 된 기초를 형성하게 되었습니다.

오일러는 그래프 내 노드와 엣지의 특정 배열은 중요하지 않음을 언급했습니다; 중요한 것은 그들 사이의 연결이라는 것입니다. 이 관찰은 그래프 이론(그리고 최종적으로 위상학 분야)에서 중요한데, 이는 기본 구조를 변경하지 않고 복잡한 네트워크를 대변할 수 있게 해줍니다.

마지막으로, 오일러는 지금은 오일러 경로라고 알려지는 각 엣지를 정확히 한 번씩 통과하는 경로와, 이와 같은 경로가 시작점과 끝점이 동일한 오일러 회로라고 불리는 것 사이에 구별했습니다. 오일러의 이 경로에 대한 기준은 그래프가 연결되어 있고 홀수 차수의 노드가 정확히 제로 또는 두 개일 때 오일러 경로가 존재하며, 그래프가 연결되어 있고 모든 노드가 짝수 차수일 때만 오일러 회로가 존재한다는 것입니다.

<div class="content-ad"></div>

# 그래프 이론 오늘 — 선택과 경로

오일러가 케ーニ히스베르크 다리 문제를 해결한 것으로 뿌리를 둔 지적 씨앗은 그래프 이론이라는 건강하고 방대한 나무로 자라났습니다. 그래프 이론은 복잡한 시스템에 대한 우리의 이해를 계속해서 형성하고 있는 프레임워크입니다.

그래프 이론은 객체 간의 쌍 관계를 모델링하는 데 사용되는 수학적 구조를 연구합니다. 이 분야는 정점 또는 노드로 알려진 포인트의 모음으로 실제 세계 문제를 추상화하고, 엣지로 불리는 선분에 의해 연결된 그래프로 나타냅니다.

이러한 단순화는 매우 유용하며, 그래프 이론적 렌즈를 통해 다양한 도메인에서 문제를 분석하고 해결하는 것이 가능합니다. 실제 세부 사항의 혼란을 줄이면서, 그래프 이론은 기술부터 사회과학에 이르기까지 다양한 분야에서 직접적인 경로, 효율적인 연결 및 최적의 해결책을 발견하는 것을 가능하게 합니다.

<div class="content-ad"></div>

이것의 매력은 네트워크를 모델링하는 능력에만 있는 것이 아닙니다. 그래프의 다양한 유형을 특징 짓는 데 기여하는 것도 있습니다. 각 유형은 가중 그래프, 방향 그래프, 이분 그래프 및 다중 그래프와 같이 파티에 고유한 맛을 불어넣습니다. 이제 옷소매를 걷어차고 상용어로 말하자면 '야생'에서 이러한 그래프를 탐험해봅시다. 우리의 사용 사례에 맞게 몇 가지 Python 데모를 활용하여 이를 직접 만들어보는 방법을 알아보겠습니다. 복잡한 네트워크의 생성, 조작, 및 연구를 위해 설계된 Python 라이브러리인 networkx를 사용할 것입니다.

## 가중 그래프: 적은 이용하는 길

번화한 도시의 도로 네트워크를 고려해보십시오. 모든 도로가 같은 중요도로 만들어지는 것은 아닙니다. 어떤 도로는 도시의 한쪽 끝에서 다른 쪽 끝까지 빠르게 이동할 수 있는 고속도로이며, 다른 도로는 여러분의 인내심을 시험하는 혼잡한 거리입니다. 가중 그래프는 각 에지에 "가중치"를 할당하여 이러한 세부 사항을 포착할 수 있습니다. 이 가중치는 건너편으로 이동하는 데 필요한 비용, 거리, 또는 시간을 반영합니다.

```python
import networkx as nx #이것은 그다지 흔하지 않은 라이브러리이므로 먼저 pip 설치해야 할 수도 있습니다
import matplotlib.pyplot as plt

# 가중 그래프 만들기
G_weighted = nx.Graph()

# '비용'이 다른 도로를 나타내는 가중 에지 추가
G_weighted.add_edge('A', 'B', weight=4)
G_weighted.add_edge('B', 'C', weight=1)
G_weighted.add_edge('C', 'D', weight=7)
G_weighted.add_edge('A', 'D', weight=5)

# 노드에 대한 레이아웃 생성
pos = nx.circular_layout(G_weighted)

# 노드 위치에 따라 그래프 그리기
nx.draw_networkx(G_weighted, pos, with_labels=True, node_color='skyblue', node_size=700)

# 에지 레이블 그리기
edge_labels = nx.get_edge_attributes(G_weighted, 'weight')
nx.draw_networkx_edge_labels(G_weighted, pos, edge_labels=edge_labels)

# 그래프 플롯 표시
plt.title("Fig 4a: 가중 그래프: 도시 도로")
plt.show()
```

<div class="content-ad"></div>

위의 코드를 실행하면 아래와 같은 모습의 그림이 나올 것입니다:

![image](/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_3.png)

그다지 화려하지는 않겠지만, 이게 바로 포인트입니다. 더 복잡한 시나리오를 나타내는 데 확장할 수 있으면서도, 우리가 관심 있는 것만 단순하게 유지하고 불필요한 데이터의 이슈를 해결할 수 있습니다.

모델에서 엣지에 있는 가중치는 현실적인 요소를 더해줍니다. 예를 들어 높은 가중치는 긴 도로나 혼잡한 교통이 있는 도로를 나타낼 수 있으며, 이를 통해 계획자들이 병목 현상을 식별하거나 인프라 향상이 필요한 지역을 찾는 데 도움이 됩니다. 결국, 때로는 무엇을 해야할지 모를 만큼 많은 데이터를 보유하고 있는 세상에서 가장 중요한 질문은 종종 다음과 같습니다: 특정 결정을 내리기 위해 정말 필요한 정보는 무엇인가요? 나머지는 그저 소음뿐입니다.

<div class="content-ad"></div>

그림 4a의 그래프 유형을 사용하면 알고리즘을 적용하여 가장 짧은 경로를 찾거나 트래픽 흐름을 최적화하거나 심지어 새 도로 건설을 계획할 수 있습니다. 도시의 연결성과 효율성을 향상시키기 위해 데이터 기반 의사결정에 강력한 도구가 됩니다. 이러한 그래프는 다음과 같은 분야에 중요할 수 있습니다:

- 교통 관리: 가중 그래프를 분석함으로써 관할 당국은 교통이 집중되는 경로를 식별하고 대응함으로써 통근 시간을 단축하고 혼잡을 줄일 수 있습니다.
- 인프라 계획: 계획자들은 서비스가 부족한 지역이나 새 도로나 대중교통 연결 공간의 잠재적 사이트를 발견할 수 있어 도시의 성장과 발전을 위해 더 나은 환경을 조성할 수 있습니다.
- 비상 대응 최적화: 비상 서비스는 이러한 그래프를 사용하여 가장 빠른 경로를 결정하여 환경적인 상황에서 시기적극적인 대응을 보장할 수 있습니다.

## 방향성 있는 그래프: 일방향 디지털 흐름

이제 방향성 있는 그래프로 이동해 보겠습니다. 여기서 관계는 방향이 있는데, 마치 일방통행거리나 인터넷에서의 정보 흐름과 유사합니다. 가중 그래프는 각 엣지에 값을 할당하여 거리나 비용과 같은 특성을 양적으로 표현하지만, 방향성 있는 그래프는 정보의 흐름에 집중합니다. 가중 에지는 거리나 날짜에 따라 변경되는 통행료가 있는 도로와 같으며, 방향성 있는 에지는 한 방향 도로로, 엄격하게 A 지점에서 B 지점으로의 움직임을 허용합니다.

<div class="content-ad"></div>

우리는 웹사이트 내 개별 웹페이지를 나타내는 노드로 디지털 수퍼하이웨이의 스냅샷을 만들 것입니다. 방향성을 가진 엣지들은 사용자가 정보를 찾을 때 이동할 수 있는 단방향 경로를 강조합니다.

```js
# 마지막 예제와 다른 커널에서 작업 중인 경우, 필요한 라이브러리를 가져와야 합니다
# 방향 그래프 생성
G_directed = nx.DiGraph()

# 방향을 갖는 엣지 추가. 각 노드는 하나의 웹페이지를 나타냅니다. 각 방향성을 가진 엣지는 한 페이지에서 다른 페이지로 이어지는 하이퍼링크입니다
G_directed.add_edge('Page 1', 'Page 2')
G_directed.add_edge('Page 1', 'Page 3')
G_directed.add_edge('Page 2', 'Page 4')

# 그래프 시각화
pos = nx.spring_layout(G_directed)
nx.draw_networkx(G_directed, pos, with_labels=True, arrows=True)
plt.title("Fig 4b: Directed Graph: Web Pages")
plt.show()
```

<img src="/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_4.png" />

웹 내비게이션에서 이는 사용자가 한 페이지에서 다른 페이지로 이동할 수 있지만 반대로는 그렇지 않음을 의미합니다. 이 흐름을 이해하는 것은 다양한 응용 프로그램에 매우 중요합니다.

<div class="content-ad"></div>

- 콘텐츠 접근성: 상호 액세스 없이 단방향 링크 뒤에 가치 있는 콘텐츠가 갇히지 않도록 보장합니다.
- 사이트 구조: 사용자가 막다른 곳에 도달하지 않고 일반 콘텐츠에서 보다 구체적인 페이지로 자연스럽게 이동할 수 있도록 웹사이트의 구조를 설계합니다.
- 정보 계층 구조: 사용자가 소개 정보에서 상세 콘텐츠로 안내되는 명확한 경로를 수립하여 사이트의 정보 구조를 반영합니다.

이 경우 Fig 4b를 사용하여 디지털 트래픽의 경로를 설명하는 방향 그래프를 사용해 웹사이트의 구조를 통해 사용자가 어떻게 이동할 수 있는지, 각 페이지가 어떻게 상호 연결되어 사이트의 구조를 형성하는지 이해할 수 있었습니다. 이해는 웹 개발자부터 디지털 마케터에 이르기까지 디지털 콘텐츠의 디자인 및 관리에 관여하는 모든 사람들에게 기본적입니다.

## 이분 그래프: 선호도와 예측 사이의 다리

이분 그래프는 연결이 각 클래스 내에서 아닌 두 가지 다른 객체 클래스 사이에만 존재하는 독특한 방식으로 두 가지 서로 다른 객체 클래스를 나타내는 방법을 제공합니다. 두 가지 다른 그룹 사이의 춤으로 생각해보세요. 한 그룹의 각 구성원은 다른 그룹의 구성원과 춤을 추거나 할 수 있지만 자신의 그룹과는 춤을 추지 않습니다.

<div class="content-ad"></div>

이 Python 데모에서는 추천 시스템의 작은 부분을 모델링하기 위해 이분 그래프를 생성합니다. 사용자를 나타내는 노드 세트와 책, 영화 또는 기타 추천 가능한 엔티티가 될 수 있는 아이템을 나타내는 노드 세트 두 개를 정의합니다. 에지를 추가하여 사용자와 항목을 연결하며 선호도나 상호 작용을 나타냅니다. nx.bipartite_layout 함수를 사용하여 시각적으로 두 세트를 분리하여 구분과 연결을 명확히합니다.

```python
# 마지막 예제와 다른 커널에서 작업하는 경우 필요한 라이브러리를 가져오는 것을 기억하세요
# 그래프 인스턴스 생성
B = nx.Graph()

# "bipartite" 노드 속성을 가진 노드 추가
B.add_nodes_from(['User1', 'User2', 'User3'], bipartite=0)  # 사용자
B.add_nodes_from(['Book1', 'Book2', 'Movie1', 'Movie2'], bipartite=1)  # 항목

# 서로 다른 세트의 노드 간에 엣지 추가
B.add_edges_from([('User1', 'Book1'), ('User1', 'Movie2'),
                  ('User2', 'Book2'), ('User2', 'Movie1'),
                  ('User3', 'Book1'), ('User3', 'Movie1')])

# B가 이분 그래프인지 확인
print(nx.is_bipartite(B))  # 출력: True

# 이분 그래프를 그림
pos = nx.bipartite_layout(B, ['User1', 'User2', 'User3'])
nx.draw_networkx(B, pos, with_labels=True)
plt.title("Figure 4c: Bipartite Graph: Recommendation System")
plt.show()
```

<img src="/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_5.png" />

이분 그래프는 스트리밍 서비스, 전자상거래 플랫폼 및 소셜 네트워크에서 사용되는 추천 시스템에서 특히 효과적입니다. 이들은 아래와 같은 면에서 도움이 됩니다:

<div class="content-ad"></div>

- 개인화(Personalization): 사용자-항목 상호작용을 분석하여, 사용자가 이전에 상호작용했던 항목과 유사한 항목을 제안해 컨텐츠를 개인화할 수 있습니다.
- 협업 필터링(Collaborative Filtering): 이러한 그래프는 협업 필터링을 가능하게 하며, 비슷한 사용자들의 선호도에 기반하여 추천을 제공합니다.
- 네트워크 분석(Network Analysis): 연결성과 클러스터 패턴을 이해함으로써, 명확한 마케팅 및 사용자 행동 이해에 도움이 됩니다.

이분 그래프는 추천 엔진 내에서의 관계의 본질을 추상화하여, 사용자가 다음에 좋아할 것으로 예측하고 제안함으로써 사용자 경험을 강화하는 간소화된 유용한 방법을 제공합니다.

# 인공지능을 위한 그래프 이론의 유용한 개념 - 지도학습(Supervised Learning)

## 그래프로 시각화하는 신경망(Visualizing Neural Networks as Graphs)

<div class="content-ad"></div>

인공 지능과 기계 학습에서 그래프 이론에 의해 제공되는 추상 모델은 중요합니다. 예를 들어 신경망의 구조를 자세히 들여다보면 그래프가 당신을 노려보고 있는 것을 볼 수 있습니다. 각 뉴런은 노드이고 각 시냅스 가중치는 이야기가 담긴 간선입니다. 입력에서 출력 레이어로 데이터 경로는 방향 그래프의 탐색을 흉내냅니다. 이 그래프 중심적 관점은 메타포에 그치지 않습니다. TensorFlow와 같은 프레임워크에서 연산의 구조적 현실입니다. 이 그래프 기반 표현은 데이터로부터 학습하고 패턴을 인식하며 결정을 내릴 수 있는 알고리즘을 개발하는 데 중요합니다. 이 추상화를 통해 인공 지능 시스템은 인간의 사고 과정의 복잡성을 근사할 수 있습니다.

우리는 방향 그래프를 사용하여 밀도가 높은 연결된 신경망의 표현을 구축할 것입니다. 이 신경망은 두 개의 입력 노드, 두 개의 은닉층(첫 번째 층에는 두 개의 노드, 두 번째 층에는 세 개의 노드) 및 하나의 출력 노드로 구성됩니다. 레이어의 각 노드는 다음 레이어의 노드와 완전히("조밀하게") 연결됩니다.

이러한 시각화 방법은 우리가 한눈에 신경망의 구조를 이해하는 데 도움을 줍니다. 각각의 처리 레이어를 통해 입력 데이터로부터의 정보 흐름을 명확히하며 각각이 입력 데이터로부터 고유한 방식으로 학습하는 것에 기여합니다. 이러한 모델을 설계할 때 신경망의 깊이와 복잡성에 대한 통찰을 제공하기 때문에 중요합니다.

```js
#직전 예제와 다른 커널에서 작업 중이라면 필요한 라이브러리를 가져와야 합니다.
#신경망을 모델링하기 위해 NetworkX DiGraph를 초기화합니다.
neural_net = nx.DiGraph()

#입력 레이어를 위한 노드 추가
neural_net.add_nodes_from(['Input1', 'Input2'], layer='input')

#첫 번째 은닉층을 위한 노드 추가(2개의 노드)
neural_net.add_nodes_from(['Hidden1_1', 'Hidden1_2'], layer='hidden1')

#두 번째 은닉층을 위한 노드 추가(3개의 노드)
neural_net.add_nodes_from(['Hidden2_1', 'Hidden2_2', 'Hidden2_3'], layer='hidden2')

#출력 레이어를 위한 노드 추가
neural_net.add_node('Output', layer='output')

#시냅스를 나타내는 간선을 사용하여 노드 연결 및 가중치 추가
#가중치 라벨링 규칙은 'w(현재 노드 인덱스)_(연결 노드 인덱스) 입니다.
weights = {
    ('Input1', 'Hidden1_1'): 'w1_3', ('Input2', 'Hidden1_1'): 'w2_3',
    ('Input1', 'Hidden1_2'): 'w1_4', ('Input2', 'Hidden1_2'): 'w2_4',
    ('Hidden1_1', 'Hidden2_1'): 'w3_5', ('Hidden1_1', 'Hidden2_2'): 'w3_6', ('Hidden1_1', 'Hidden2_3'): 'w3_7',
    ('Hidden1_2', 'Hidden2_1'): 'w4_5', ('Hidden1_2', 'Hidden2_2'): 'w4_6', ('Hidden1_2', 'Hidden2_3'): 'w4_7',
    ('Hidden2_1', 'Output'): 'w5_8', ('Hidden2_2', 'Output'): 'w6_8', ('Hidden2_3', 'Output'): 'w7_8'
}

for (u, v), weight in weights.items():
    neural_net.add_edge(u, v, weight=weight)

#각 노드의 위치를 명확히 하기 위해 수동으로 설정
pos = {
    'Input1': (0, 1), 'Input2': (0, -1),
    'Hidden1_1': (1, 1), 'Hidden1_2': (1, -1),
    'Hidden2_1': (2, 2), 'Hidden2_2': (2, 0), 'Hidden2_3': (2, -2),
    'Output': (3, 0)
}

#플로팅을 위한 figure 생성
fig, ax = plt.subplots(figsize=(12, 8))

#가중치에 대한 에지 라벨 작성
edge_labels = nx.get_edge_attributes(neural_net, 'weight')

#에지 라벨 포지션 조절(중첩 방지)
edge_label_pos = {}
for edge in neural_net.edges():
    u, v = edge
    #중간점 계산 및 라벨 위치 조정
    mid_x = (pos[u][0] + pos[v][0]) / 2
    mid_y = (pos[u][1] + pos[v][1]) / 2
    delta_x = pos[v][0] - pos[u][0]
    delta_y = pos[v][1] - pos[u][1]
    if abs(delta_x) > abs(delta_y):
        edge_label_pos[edge] = (mid_x, mid_y + 0.1)  #수평 에지를 위한 y 오프셋 조정
    else:
        edge_label_pos[edge] = (mid_x + 0.1, mid_y)  #수직 에지를 위한 x 오프셋 조정

#신경망 그래프 그리기
nx.draw_networkx(neural_net, pos, with_labels=True, node_size=1000, node_color='skyblue', arrowsize=20, font_size=10)

#가중치에 대한 에지 라벨 작성
edge_labels = nx.get_edge_attributes(neural_net, 'weight')

#조정된 위치에 에지 라벨 그리기
for (u, v, d) in neural_net.edges(data=True):
    edge_weight = d['weight']
    x, y = (pos[u][0] * 0.6 + pos[v][0] * 0.4), (pos[u][1] * 0.6 + pos[v][1] * 0.4)
    txt = plt.text(x, y, edge_weight, ha='center', va='center', rotation=0, fontsize=8)

#제목 설정
ax.set_title("Fig 5a: Neural Network Graph (Directed and Weighted) with Two Hidden Layers")

#플롯 표시
plt.show()
```

<div class="content-ad"></div>


![image](/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_6.png)

신경망을 그래프로 모델링하면 그들의 구조를 이해하고 전달하는 강력한 도구를 얻습니다. 그래프 기반 접근법은 다음과 같은 것이 가능합니다:

- 복잡성 시각화: 신경 계산의 상호 연결된 특성을 시각화할 수 있습니다. 뉴런과 그들의 연결을 배치함으로써, 데이터 내의 복잡한 패턴 및 관계가 어떻게 감지되고 처리되는지 볼 수 있습니다.
- 아키텍처 디버깅 및 최적화: 병목 현상이나 중복 연결을 식별하는 것이 더 쉬워집니다. 이는 특히 딥 러닝에서 유용할 수 있습니다. 여기서 레이어와 노드의 수를 조정하는 것이 성능에 미치는 영향이 상당할 수 있습니다.
- 연구 및 개발 지원: 연구자들은 그래프를 사용하여 새로운 네트워크 아키텍처를 제안하고 테스트할 수 있습니다. 그래프의 구조를 조정함으로써, 정보 처리의 새로운 방법을 실험해볼 수 있습니다.

신경망을 그래프로 추상화함으로써, 우리는 모델을 분석하기 위한 다양한 그래프 이론 도구와 지표를 활용할 수도 있습니다. 이는 최단 경로를 조사하는 것(입력부터 출력까지의 가장 적은 변환 횟수를 이해하기 위해), 네트워크 중심성(가장 중요한 뉴런을 찾기 위해) 또는 심지어 커뮤니티 탐지(긴밀하게 협력하는 뉴런 군집 식별)를 포함할 수 있습니다.


<div class="content-ad"></div>

## 의사 결정 트리: 가지치기 그래프

의사 결정 트리는 핵심적으로 루트 노드에서 시작하여 잎 노드로 가는 그래프로, 그 구조에 의사 결정의 본질을 담고 있습니다. 노드는 질문이나 결정을 나타내며, 간선은 데이터의 답변을 기반으로 취해진 경로를 표현합니다. 그래프 이론적으로 의사 결정 트리는 방향성이 있고 비순환적인 그래프(DAGs)로, 각 방향 간선은 질문에서 답변으로의 흐름을 나타내어 연속된 질문이나 최종 결정으로 이어집니다. 이 구조는 계층적이며 루트에서 잎까지의 명확한 방향을 나타내며, 잎은 결과를 표현합니다.

우리는 이 그래프 기반의 의사 결정 과정을 클래식한 붓꽃 데이터셋에 적용할 것입니다.  이 데이터셋은 붓꽃의 150개 샘플로 구성되어 있고, 각각의 특징으로서 꽃받침 길이, 꽃받침 너비, 꽃잎 길이, 그리고 꽃잎 너비를 설명합니다. 데이터셋은 세 종류의 붓꽃 종(Sestosa, Versicolor, Virginica) 샘플을 담고 있습니다. 우리의 의사 결정 트리는 이러한 특징들로부터 학습을 통해 붓꽃의 종을 정확하게 예측할 것입니다.

의사 결정 트리의 추상적인 개념을 구체적인 그래프로 번역하고자 하며, 의사 권하는 networkx를 계속 활용하여 의사 결정의 흐름을 시각화할 것입니다. 우리는 붓꽃 데이터셋을 사용하여 각 특징을 결정 노드로 사용하고, 해당 값에 따라 가지를 친 의사 결정 트리를 구성할 것입니다. 이는 최종적으로 붓꽃의 종을 예측하는 경로를 제공합니다.

<div class="content-ad"></div>

```js
import matplotlib.pyplot as plt
from sklearn.datasets import load_iris
from sklearn.tree import DecisionTreeClassifier, plot_tree

# 아이리스 데이터세트 불러오기
iris = load_iris()
X, y = iris.data, iris.target

# 아이리스 데이터를 기반으로 의사결정 트리 분류기 훈련
clf = DecisionTreeClassifier()
clf.fit(X, y)

# sklearn의 plot_tree를 사용하여 트리 그리기
plt.figure(figsize=(20, 10))  # 그림의 크기 설정
plot_tree(clf, filled=True, feature_names=iris.feature_names, class_names=iris.target_names)
plt.title("Fig 5b: 의사결정 트리 그래프 표현")
plt.show()
```

<img src="/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_7.png" />

의사결정 트리는 기계 학습에서 그래프 이론의 중요성을 전통적으로 보여주며, 알고리즘의 단계별 논리를 명확히 제시하는 구조를 제공합니다. 이 경우에는 방향 그래프의 간단한 구조가 필요에 따라 확장되어 시작부터 끝까지 알고리즘의 결정 경로를 보여줍니다. 이는 알고리즘이 데이터를 처리하여 특정 결론에 도달하는 방식에 대한 직관적인 이해를 제공하며, 지능적이고 데이터 중심의 솔루션을 개발하는 데 그래프 이론의 실용적인 응용을 보여줍니다.

# 그래프 이론의 인공지능에 유용한 추상화 — 비지도 학습


<div class="content-ad"></div>

비지도 학습, 특히 클러스터링은 데이터 내의 관계를 활용하여 그룹이나 클러스터를 형성함으로써 그래프 이론에서 상당한 이득을 얻습니다. 그래프 이론은 클러스터링에 그 자체에만 있는 것이 아닙니다. 데이터의 기본 구조와 연결을 강조함으로써 이러한 기술을 이해하고 구현하는 방법을 개선할 수 있습니다.

클러스터링은 동일한 클러스터에 속하는 객체들이 다른 클러스터에 속한 객체들보다 서로 더 유사하도록 하는 것을 목표로 합니다. 그래프 이론은 데이터 포인트를 노드로, 이러한 포인트 간의 유사성이나 거리를 엣지로 나타내어 그래프를 형성함으로써 클러스터링을 용이하게 합니다. 이 접근 방식은 연결성 및 네트워크 구조를 기반으로 한 자연적인 그룹화를 드러내는 데 특히 유용하며 전통적인 벡터 공간 표현에서 가려진 경우가 많습니다.

## 스펙트럼 클러스터링: 그래프 기반 접근 방식

스펙트럼 클러스터링은 그래프 이론을 데이터의 그래프 구조를 포착하는 라플라시안 행렬을 사용하여 클러스터링 기술에 통합시킬 수 있는 좋은 예입니다. 그 핵심은 다음과 같이 작동합니다:

<div class="content-ad"></div>

- 그래프 표현: 스펙트럴 클러스터링은 각 데이터 포인트를 그래프의 노드로 취급하여 시작합니다. 데이터 포인트 간의 유사성이나 차이는 이러한 노드를 연결하는 엣지로 간주됩니다. 각 엣지의 강도나 가중치는 선택한 기준인 유클리드 거리나 다른 유사성 측정에 따라 두 점이 서로 얼마나 유사하거나 가까운지를 반영합니다.
- 라플라시안 행렬 구성: 라플라시안 행렬은 그래프 표현에서 유도되며 노드 간의 연결성을 이해하는 데 중요합니다. 이 행렬은 그래프의 구조를 이해하는 데 도움이 되며 클러스터링 프로세스에 중요합니다. 이는 그래프의 인접 행렬에서 차수 행렬을 뺌으로써 유도됩니다. 인접 행렬은 노드 사이의 엣지 가중치를 기록하고, 차수 행렬은 노드에 연결된 엣지 가중치의 합을 나타내는 대각선 행렬입니다.
- 라플라시안 행렬 분해: 다음 단계는 라플라시안 행렬을 분해하여 그 고유값과 해당하는 고유벡터를 추출하는 것입니다. 고유값은 그래프의 연결성에 대한 정보를 드러내고, 고유벡터는 데이터를 새로운 저차원 공간으로 투영하는 데 사용됩니다.
- 데이터 투영: 가장 작은 비제로 고유값에 해당하는 고유벡터를 사용하여 데이터를 새로운 공간으로 투영합니다. 이 단계는 고차원 데이터를 클러스터가 더 분리되어 식별하기 쉬운 형태로 변환합니다.

다시 말해, 스펙트럴 클러스터링은 복잡한 데이터셋 내에 내재된 구조를 효과적으로 드러내고 활용하기 위해 그래프 이론을 활용하며, 비정형 모양의 클러스터를 식별하는 데 특히 강력합니다.

이제 "두 달" 데이터셋을 사용하여 스펙트럴 클러스터링을 시연할 것이며, 이를 통해 비선형 경계를 갖는 클러스터를 탐지하는 방법의 효과를 보여줄 것입니다. (그림 6a 참조)

```js
import matplotlib.pyplot as plt
from sklearn.datasets import make_moons
from sklearn.cluster import SpectralClustering

# 두 달 데이터셋 생성
X, _ = make_moons(n_samples=200, noise=0.1, random_state=42)

# "가장 가까운 이웃" 클러스터링을 사용하여 스펙트럴 클러스터링 적용
sc = SpectralClustering(n_clusters=2, affinity='nearest_neighbors', n_neighbors=10)
labels = sc.fit_predict(X)

# 결과 플롯
plt.scatter(X[:, 0], X[:, 1], c=labels, cmap='viridis', edgecolor='k')
plt.title('Fig 6a: 두 달 데이터의 스펙트럴 클러스터링')
plt.xlabel('Feature 1')
plt.ylabel('Feature 2')
plt.show()
```

<div class="content-ad"></div>


<img src="/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_8.png" />

오일러의 스위스 뿌리를 기리며, 더 복잡한 배열과 고차원에서이 전략이 어떻게 더 유용해지는지 모델링하여 클러스터링 알고리즘의 능력을 테스트하는 데 자주 사용되는 클래식 데이터 세트 인 스위스 롤 모양을 살펴보겠습니다. 특히 복잡한 매니폴드 구조를 가진 데이터를 해독할 수있는 알고리즘 (Fig 6b 참조) 입니다. 이 경우에는 스위스 롤 모양을 구성하는 포인트 (노드)를 두 가지 서로 다른 클러스터로 구분 할 수있을까요?

```js
import matplotlib.pyplot as plt
from sklearn.datasets import make_swiss_roll
from sklearn.cluster import SpectralClustering
from mpl_toolkits.mplot3d import Axes3D

# 2000개의 샘플과 잡음 수준이 0.1인 스위스 롤 데이터 세트 생성
X, _ = make_swiss_roll(n_samples=2000, noise=0.1, random_state=42)

# 2개의 클러스터에 Spectral Clustering를 적용하고 "최근 이웃" 친화성을 사용합니다.
# 결과를 확인하기 위해 더 많은 클러스터를 시도해보세요.
sc = SpectralClustering(n_clusters=2, affinity='nearest_neighbors', n_neighbors=10)
labels = sc.fit_predict(X)

# 3D 산점도로 결과 플롯하기
fig = plt.figure()
ax = fig.add_subplot(111, projection='3d')
ax.scatter(X[:, 0], X[:, 2], X[:, 1], c=labels, cmap='viridis', edgecolor='k')
ax.set_title('Fig 6b: 스위스 롤 데이터의 스펙트럴 클러스터링')
ax.set_xlabel('특성 1')
ax.set_ylabel('특성 3')
ax.set_zlabel('특성 2')
plt.show()
```

<img src="/assets/img/2024-05-20-TheEssentialGuidetoGraphTheoryFroman18thCenturyRiddletoArtificialIntelligence_9.png" />


<div class="content-ad"></div>

## 클러스터링에서 그래프 이론의 유틸리티 및 통찰

- 데이터의 그래픽 표현: 데이터를 그래프로 시각화하면 클러스터링 알고리즘이 공간이 높은 경우와 같이 데이터의 실제 구조를 더 잘 파악할 수 있습니다. 이 그래픽 관점은 접근성이나 밀도에만 의존하는 대신 점들이 어떻게 상호 연결되어 있는지에 기초하여 클러스터를 식별할 수 있도록 합니다.
- 복잡한 구조 다루기: 그래프 이론은 이음매가 얽힌 나선이나 동심원 같은 복잡한 모양을 형성하는 데이터를 처리하는 데 뛰어나며, 기존의 중심 또는 밀도 기반 클러스터링 방법이 실패할 수 있는 상황에서 능력을 발휘합니다.
- 유연성과 다양성: 그래프 기반 클러스터링 방법은 다양한 종류의 데이터를 처리하는 데 다재다능하며 다양한 유사성 측정에 적응할 수 있어 데이터의 불규칙성과 잡음에 대해 견고합니다.
- 시각화와 해석: 그래프 기반 방법은 클러스터링 성능을 향상시키는 데 그치지 않고 결과의 해석 가능성을 향상시킵니다. 클러스터가 어떻게 형성되는지 명확한 시각적 통찰을 제공하여 데이터 분석과 의사 결정 프로세스에 매우 가치 있는 정보를 제공합니다.

그래프 이론을 통합하면 Spectral Clustering은 비지도 학습에서 전통적인 클러스터링 접근 방식의 한계를 극복할 뿐만 아니라 데이터의 고유 구조와 보다 일치하는 방식으로 데이터를 분석하는 새로운 가능성을 열어줍니다.

# 현대 AI 기반 제품에서의 그래프 이론

<div class="content-ad"></div>

Microsoft Copilot은 그래프 이론 원리가 제품의 유용성을 궁극적으로 향상시키는 데 사용될 수 있는 한 예입니다. 이 경우에는 다양한 데이터 유형과 소스를 데이터 생태계 전반에 걸쳐 연결함으로써 사용자 상호작용을 개선합니다.

Copilot의 그래프 이론적 기초는 사용자 상호작용을 문맥화하고 개인화하는 능력에 나타납니다. Copilot은 연결된 지점으로의 방대한 데이터 네트워크를 분석함으로써 사용자의 현재 문맥에 관련된 정보를 종합하고 생성할 수 있습니다.

## Copilot의 그래프 이론 기반 메커니즘 이해

Microsoft Copilot은 사용자 프롬프트와 조직 데이터를 복잡한 상호 연결 정보 그래프로 변환하여 작동합니다. 이 그래프는 정적 개체가 아니며, 새로운 데이터의 흡수 및 사용자 상호작용을 통해 지속적으로 업데이트되고 풍부화됩니다. 여기서 노드는 데이터 포인트를 나타내고 엣지는 그들 사이의 관계를 반영하며, 데이터 포인트 사이의 다양한 직간접 관계를 고려합니다.

<div class="content-ad"></div>

- 데이터 수집 및 스키마 생성: Copilot 운영의 핵심은 다양한 소스에서 통합 및 모델링된 데이터가 있는 Microsoft Graph에 있습니다. 여기서 데이터는 단순히 저장되는 것이 아니라 교차 연결성을 반영하는 스키마에 따라 구조화됩니다. 새로운 데이터가 통합되고 사용자 상호 작용이 발전함에 따라 기본 그래프는 성장하고 적응하여 시간이 지남에 따라 Copilot의 학습 능력을 향상시키고 응답을 개선합니다.
- 데이터 색인 및 쿼리 처리: Copilot이 이 방대한 그래프에서 관련 데이터 포인트를 검색하는 것은 색인 및 쿼리 처리 능력에 달려 있습니다. 이 프로세스들은 AI 도구가 데이터 그래프를 탐색하고 그래프 이론적 모델에서 최단 경로를 찾을 수 있도록 합니다.
- 내용 이해 및 응답 생성: 그래프 이론 원칙을 활용하여, Copilot은 상호작용의 내용을 "이해"합니다. 사용자 쿼리와 데이터를 독립적으로 보는 것이 아니라 더 큰 구조의 일부로 간주합니다. 이 관점을 통해 Copilot은 데이터 노드 간의 연결의 근접성과 강도를 고려하는 그래프의 위상학에 기반한 응답을 수립할 수 있습니다. 데이터의 그래프 구조를 활용하여, Copilot은 사용자의 요구를 더 직관적이고 반영적인 맥락에 맞게 제공할 수 있습니다.

# 마무리하며

끝까지 와서 축하드립니다! 우리는 그래프 이론의 매혹적인 세계를 탐험했습니다. 오일러의 쾨닉스베르크 다리 문제로부터 현대 기술에서의 중요한 역할까지 그 발전을 추적해 왔습니다. 노드와 엣지의 추상화가 복잡한 시스템을 간단하게 만들어 혁신적인 해결책을 이끌어 내는 방법을 확인했습니다. 복잡한 관계를 이해 가능한 모델로 분해하는 것으로, 우리는 그래프 이론이 최적화된 정보 처리, 패턴 발견 및 데이터 기반 결정을 효율적으로 처리할 수 있는 능력을 향상시킨다는 것을 발견했습니다. 이러한 원칙들은 알고리즘의 복잡성을 향상시키는 것뿐만 아니라 정보를 조직하고 검색하는 방식을 최적화합니다.

만약 제가처럼 ML 문제 해결의 세부 사항에 빠져들기를 즐기신다면, LinkedIn 및 Medium에서 팔로우해 주세요. 함께하면 하나의 현명한 해결책으로 AI 미궁을 탐험해 나갈 수 있습니다!

<div class="content-ad"></div>

다음 알고리즘 모험까지 계속 탐험하고, 계속 배우고, 노드를 계속 연결해 보세요! 데이터 과학의 그래프를 탐험하는 여정이 그 자체의 추상화만큼 명확하고 통찰력 있기를 바랍니다.

참고: 저자가 아닌 경우를 제외하고 모든 이미지는 저자의 것입니다.