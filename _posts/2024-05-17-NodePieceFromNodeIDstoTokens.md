---
title: "NodePiece 노드 ID에서 토큰으로"
description: ""
coverImage: "/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_0.png"
date: 2024-05-17 20:06
ogImage:
  url: /assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_0.png
tag: Tech
originalTitle: "NodePiece: From Node IDs to Tokens"
link: "https://medium.com/python-in-plain-english/nodepiece-from-node-ids-to-tokens-81130741e5d9"
---

그래프 임베딩에 대한 혁신적인 접근

![이미지](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_0.png)

# 소개

자연어 처리(NLP) 및 대형 언어 모델(LLM)에 대한 관심과 발전이 급증한 것은 잘 알려져 있습니다. “토큰화” 및 “트랜스포머”와 같은 용어들이 어디서든 볼 수 있을 정도입니다. 그러나 그래프 신경망(GNN) 및 특히 지식 그래프 임베딩이라는 또다른 강력한 분야는 전문가들을 제외하고는 훨씬 인기가 적습니다. 이러한 기술들은 추천 시스템에서 링크 예측, 노드 분류 등 다양한 애플리케이션 영역에서 강력한 솔루션을 제공합니다.

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

GNN 세계에서 주목할 만한 기술 중 하나는 NodePiece 토큰화인데, 이견에 따르면 많은 주의를 필요로 합니다. 이 접근 방식은 그래프 신경망의 기능성을 향상시키기 위해 자연어 처리(NLP)에서 여러 개념을 도입한 새로운 방법론입니다. 이 기술은 유니버셜 "토큰" 집합을 사용하여 그래프 내 노드를 표현합니다. 이 접근 방식은 미리 정의된 ID 어휘가 필요 없어져 노드의 좀 더 적응적인 표현을 가능케 하며 모델이 다양한 그래프에 대해 일반화할 수 있는 능력을 향상시킵니다.

잠재력이 커도 NodePiece 토큰화 방법론은 널리 다뤄지지 않는데, 예를 들어 LLMs와 같이요. 이 블로그 글은 NodePiece 토큰화를 명쾌하고 직관적으로 설명하며, 기존 구현이 매우 복잡하고 이미 구현된 라이브러리에 내장되어 있어 학습이 어려운 점을 감안하여 실용적인 Python 구현을 제공합니다.

이 글을 마치면 다음을 이해할 수 있을 것입니다:

1. NodePiece 토큰화의 기본 개념들을 파악할 것입니다.

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

2. 이 기술의 근간과 원칙을 이해하세요.

3. Python에서 NodePiece 토큰화의 기본 버전을 구현하는 기술을 익히세요.

저의 Github에서 Jupyter 노트북 및 토큰화 및 모델을 담은 모듈을 찾을 수 있습니다.

이 포스트는 꽤 길기 때문에 섹션 및 하위 섹션의 간단한 개요입니다:

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

제목 1: 건설 블록의 마법

1.1. NLP 세계 - 역사 요약: 이 섹션은 자연어 처리의 진화를 강조하며 Word2Vec 및 GloVe와 같은 기본 알고리즘에 초점을 맞춥니다.
1.2. 트랜스포머 토크나이저의 출현: NLP 모델의 효율성과 유연성에 미치는 트랜스포머 기반 토큰화의 중요한 영향을 논의합니다.
1.3. 그래프 세계: NLP 토큰화 개념을 그래프 신경망에 적용하는 잠재력을 탐구하여 NodePiece를 위한 무대를 마련합니다.

제목 2: NodePiece 알고리즘

2.1. 기여 및 출처: NodePiece 모델을 소개하며 새로움과 NLP로부터 영감을 얻은 주요 출처에 집중합니다.
2.2. 기본 개념: NodePiece 모델의 핵심 구성 요소를 설명하며 그래프 노드의 토큰화 방법을 소개합니다.
2.3. 위치적 특징: NodePiece가 노드의 공간적 위치를 선택된 기준점에 상대적으로 어떻게 활용하는지 설명합니다.
2.4. 관계적 특징: NodePiece가 노드가 참여하는 관계 유형을 어떻게 파악하는지 상세히 설명합니다.
2.5. 독특한 지문 - 직관: NodePiece가 위치적 및 관계적 특징을 결합하여 독특한 노드 임베딩을 만드는 방식에 대한 직관적 설명을 제공합니다.

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

### 파트 3: 형식적 정의

3.1. Set-Based Form: 노드를 앵커, 거리 및 관계의 집합을 통해 표현하는 프로세스를 형식화합니다.
3.2. 앵커, 거리 및 관계의 내장: NodePiece가 벡터 표현으로 변환하는 집합의 방법을 설명합니다.
3.3. 부호화: 노드 정보를 단일 벡터로 압축하는 부호화 프로세스를 설명합니다.
3.4. 이것으로 무엇을 할까요?: NodePiece 임베딩이 그래프 신경망에서의 잠재적인 응용 및 이점에 대해 논의합니다.

### 파트 4: 간소화된 구현

4.1. 전체 개요: 교육 목적을 위해 설계된 NodePiece의 간소화된 버전을 소개합니다.
4.2. 데이터: 데모에 사용된 데이터셋을 설명하며, 실용성을 위해 관리 가능한 하위 집합의 선택을 강조합니다.
4.3. 토큰화: 간소화된 모델 내에서 NodePiece의 토큰화 프로세스를 설명합니다.
4.4. 앵커 선택: 구현에서 앵커 노드를 선택하는 방법을 상세히 설명합니다.
4.5. K개 최근 앵커까지의 거리 구성: 모델이 노드에서 가장 가까운 앵커까지의 거리를 계산하는 방법을 설명합니다.
4.6. 관계적 컨텍스트 추출: 노드의 관계적 컨텍스트를 식별하고 내장하는 프로세스를 설명합니다.
4.7. 특성 행렬: 토큰화 및 컨텍스트 추출 결과를 특성 행렬 형태로 강조합니다.
4.8. 전체 모델 정의: 모델 아키텍처 및 구성 요소에 대해 포괄적으로 살펴봅니다.
4.9. TransE 모델 훈련: 지식 그래프 임베딩 작업에 NodePiece 임베딩을 사용하여 TransE 모델 훈련에 대해 논의합니다.

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

# 파트1: 빌딩 블록의 마법

## NLP 세계 — 역사 소개

NLP의 발전을 되짚어보면 초기 단계는 단어 임베딩, 어근 추출 및 토크나이제이션과 같은 방법론들에 의해 주도되었습니다. Church(2017)와 Brochier 등(2019)과 같은 선구적인 알고리즘들이 Word2Vec 및 GloVe와 같은 표준을 설정하며, 비교적 간단한 절차하에서 작동했습니다:

1. 큰 텍스트 말뭉치를 편집하십시오.

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

2. 토큰화, 표제어 추출 및 유사한 기술을 통해 전처리합니다.

3. 처리된 단어를 고유한 ID로 매핑하는 임베딩 조회를 구축합니다.

그러나 이 접근 방식은 공간을 많이 차지하고 계산 비용이 많이 드는 방식이었습니다.

예를 들어, 임베딩 행렬이 다음과 같이 정의된 경우를 고려해보겠습니다:

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

![NodePieceFromNodeIDstoTokens](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_1.png)

어휘 크기(V) 및 임베딩 크기(E)였습니다. 어휘 크기가 100,000이고 임베딩 크기가 300인 경우, 리소스에 상당한 수요를 일으키는 30 백만개의 부동 소수점을 할당해야 합니다.

## 트랜스포머 토크나이저의 등장

트랜스포머 및 그와 관련된 토큰화 방법의 도입이 이 분야를 혁신적으로 변화시켰습니다. Byte-Pair Encoding (BPE) (Sennrich et al., 2016)과 같은 기술은 부분 단어 또는 알파벳과 같은 건설 블록에 유사한 개념을 도입하여 토큰화 프로세스를 혁신적으로 개선했습니다. 이러한 부분 단어 또는 토큰은 전체 단어보다 더 간결하면서도 보다 보편적이며, 다양한 언어에 적용될 수 있고 새로운 어휘를 순차적으로 도입할 수 있도록 합니다.

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

아래와 같이 표시됩니다. 해당 표현은 전통적인 방법에서는 단어를 분리하여 각각 임베딩을 할당할 것이지만, 현대적인 토크나이저는 서브워드로 분할할 수 있습니다.

```js
[CLS]
modern
token
##izer
##s
revolution
##ized
the
way
,
how
we
process
text
these
days
[SEP]
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

이는 "tokenizer"와 같은 각 새로운 단어에 대한 고유한 ID가 필요하지 않게되어 "token"과 "##izers"가 이미 어휘에 있는 상태로 다른 단어의 표현을 구성할 수 있게 해줍니다. 이는 임베딩 메모리 공간을 절약하면서 가능하게 합니다.

## 그래프의 세계

그래프 처리 및 지식 그래프 추론의 분야에서는 현대 NLP 토큰화에 사용되는 개념과 유사한 개념을 받아들이는 과정이 느리게 진행되어 왔습니다. TransE (Bordes et al., 2013) 및 RotatE (Sun et al., 2019)와 같은 지식 그래프 추론을 위한 전통적인 알고리즘은 대부분 엔티티와 관계를 고유한 임베딩에 매핑하는 데 의존해 왔습니다. 이 접근 방식은 간단하지만 메모리 집약적이며, 각 엔티티 및 관계는 임베딩 공간 내에서 고유한 식별자를 필요로 합니다 — 마치 word2vec이나 기존 NLP 솔루션에서의 단어들처럼!

TransE, RotatE 및 유사한 모델에 대해 더 알고 싶은 분은 Medium.com의 게시물을 강력히 추천합니다.

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

이 기사는 이러한 모델 뒤에 있는 원칙들에 대해 포괄적인 개요를 제공합니다.

그래프 도메인 내에서 확장 가능하고 효과적인 솔루션을 찾는 것은 오랜 시간이 걸리지만 결과가 있었습니다. 노드피스(NodePiece)는 이 맥락에서의 선도적인 알고리즘 중 하나로 등장했으며, NLP 토큰화 기술의 발전에서 많은 영감을 받았습니다. 최신 토큰화 기술의 원칙을 그래프 구조에 적용함으로써, 노드피스는 그래프 엔티티와 관계를 표현하는 새로운 방법을 제공하며, 지식 그래프 도메인에서 더 많은 메모리 효율적이고 일반화 가능한 모델을 향한 중요한 발전을 이룩했습니다.

# 파트 2: 노드피스 알고리즘

## 기여와 출처

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

노드피스 알고리즘은 대규모 지식 그래프에 대한 합성 및 매개변수 효율적인 표현에 중점을 둔 지식 그래프 임베딩 분야에서의 중요한 발전을 대표합니다. "NodePiece: Compositional and parameter-efficient representations of large knowledge graphs" (Galkin et al., 2021) 논문에서 소개된 이 방법은 널리 사용되는 Python 라이브러리인 PyKeen에 통합되어 있습니다.

PyKeen 구현은 아래에서 찾을 수 있습니다:

게다가, 논문 기여자들에 의해 작성된 구현은 GitHub에서 접근 가능하며, 원본 코드베이스를 더 탐구하려는 분들을 위해 제공됩니다:

이론적 관점에서 노드피스에 대한 포괄적이고 접근성 있는 소개를 위해, 원문의 공헌자 중 한 명인 Michael Galkin이 작성한 Medium 블로그 게시물 "NodePiece: Tokenizing Knowledge Graphs"를 읽어보시기를 권장합니다. 이 기사는 알고리즘에 대한 귀중한 심층적인 통찰을 제공하여 개발자들로부터 직접 얻은 통찰을 제공합니다.

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

마이클 갈킨의 블로그 게시물 'NodePiece: 지식그래프의 토큰화'는 NodePiece 알고리즘에 대한 깊은 설명으로 두루 짚고 있으며, 그 중 하나인 창조자에 의해 저술되었습니다. 이는 원래의 학술 논문을 넘어서 모델을 이해하려는 사람들에게 좋은 자료입니다.

## 기본 개념

NodePiece 알고리즘은 지식 그래프 내 개체마다 고유 식별자를 할당하는 전통적 요구사항에서 벗어나, 대신 기본적인 구성 요소의 조합을 통해 개체를 나타냅니다. NLP의 토큰화 개념과 유사점을 그리며, 이러한 구성 요소는 다음과 같습니다:

1. 위치 특성: 지정된 앵커 노드에 대한 노드의 근접성.

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

2. 관계 특성: 노드가 포함된 관계 유형

## 위치 특성

노드는 미리 정한 일련의 기준 앵커 노드들까지의 거리로 특성화됩니다. 이러한 앵커 노드를 선택하는 방법은 가장 연결된 노드를 선택하거나 클러스터링 알고리즘을 사용하거나 심지어 무작위 선택과 같은 선택 옵션을 포함합니다.

선택 기술에 관계없이 근본적인 원칙은 간단합니다. 각 노드는 K개의 가장 가까운 앵커 노드까지의 거리에 의해 정의됩니다. 전략적인 앵커 노드 선택을 통해 가장 가까운 앵커에 대한 근접성이 각 노드에 대한 고유한 "지문"을 제공하는 높은 가능성이 있습니다.

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

다음 예시를 통해 이 개념을 설명해 보겠습니다:

![NodePieceFromNodeIDstoTokens_2](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_2.png)

아래 표는 각 노드에서 앵커까지의 거리를 요약한 것입니다:

![NodePieceFromNodeIDstoTokens_3](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_3.png)

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

## 관계형 기능

이질적인 지식 그래프에서 엣지는 노드 간의 다양한 관계 유형을 캡슐화합니다. NodePiece 알고리즘은 이러한 관계를 노드 표현의 중요한 구성 요소로 활용하여 각 노드의 컨텍스트와 그래프 내에서의 연결성을 풍부하게 합니다. 관계형 기능이 어떻게 통합되는지 설명하기 위해, 우리의 이전 예제를 다시 살펴보고 확장하겠습니다. 이번에는 관계 유형을 포함하여:

![노드의 관계형 기능](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_4.png)

각 노드의 관계를 다음의 표로 요약할 수 있습니다:

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

![NodePieceFromNodeIDstoTokens_5](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_5.png)

Or with counting each relation type:

![NodePieceFromNodeIDstoTokens_6](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_6.png)

Probably you see now, where it is going…

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

## 독특한 지문 — 직감

NodePiece 알고리즘의 본질은 각 노드에 대한 일관된 표현인 위치 및 관계형 두 가지 다른 기능을 종합하는 능력에 있습니다. 이 과정은 가장 가까운 앵커 노드와 노드가 참여하는 관계 유형을 연결하여 노드의 고유한 "지문"이라는 단일 벡터를 얻게 됩니다.

이 과정을 개념화하기 위해 다음 단순화된 스키마를 통해 노드 2를 나타내는 것을 고려해보세요:

![NodePieceFromNodeIDstoTokens_7](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_7.png)

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

# 파트 3: 형식적인 정의

NodePiece 알고리즘 코딩을 시작하기 전에 언급할 중요한 세부 정보가 몇 가지 있습니다. 예를 들어:

1. 선택적 앵커 사용: 모든 노드의 표현에 모든 앵커가 관련이 있는 것은 아닙니다. 가장 가까운 k개의 앵커만이 각 노드에 임베딩하기 위해 고려됩니다.

2. 관계 추출: 마찬가지로, 노드의 관계적 문맥은 즉시 외부 관계 중에서 샘플링을 통해 파생되며, 각 노드당 최대 m개의 관계로 제한됩니다.

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

3. 연결 끊김 처리: 노드가 연결이 끊겼거나 특정 앵커나 관계에 대한 링크가 없는 경우 — 특별한 [DISCONNECTED] 토큰이 사용됩니다. 이는 NLP 시나리오에서의 `OOV` (out-of-vocabulary) 토큰과 유사합니다.

이제 수학 시간입니다.

NodePiece에 대한 입력이 무엇인지 시작해 봅시다:

![NodePieceFromNodeIDstoTokens_8.png](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_8.png)

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

NodePiece는 다음과 같이 설계되었음을 알 수 있습니다:

1. 지식 그래프 - 특정 관계(R)에 속하는 에지(E)로 연결된 노드(N)의 모음으로 표현됩니다.

2. 선택된 앵커 노드 (A) - 이들은 각 노드의 "지문" 표현의 일부가 될 것입니다.

3. 관계 (R) 및 앵커 (A)는 모델의 어휘 (V)를 형성합니다.

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

4. 논문에서 언급했듯이, 모든 노드에 대해 모든 앵커를 사용할 필요는 없으므로 k개의 앵커가 샘플링됩니다.

5. 관계에도 같은 원칙이 적용됩니다. 각 노드에 대해 m개의 관계만이 샘플링됩니다.

## 집합 기반 형태

우리가 가지고 있는 핵심 요소들로부터, 그래프 내 각 노드의 고유한 "지문"에 대한 형식적인 정의로 이어질 수 있습니다. 이 지문은 세 가지 구성 요소로 나뉩니다:

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

1. 앵커 세트: 모든 앵커(A) 집합에서 임의로 선택된 k개의 앵커입니다.

2. 앵커 거리: 노드에 대해 결정된 k개 가장 가까운 앵커까지의 최단 경로 거리입니다. 앵커에 도달할 수 없는 경우, 해당 거리는 미리 정의된 "마법 값"으로 표시됩니다. (-1 또는 다른 토큰과 같이)

3. 관계적 맥락: 노드를 위해 샘플링된 m개의 직접 외부 관계 중 일부로, 즉시 관계적 환경을 포함합니다.

형식적으로 표현하면, 정점 집합 V의 각 노드 u에 대한 표현은 다음과 같이 설명할 수 있습니다:

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

![2024-05-17-NodePieceFromNodeIDstoTokens_9.png](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_9.png)

원본 논문의 저자들은 거리에 positional encoding을 적용하여 각 거리를 차원 d의 벡터로 매핑하는 것을 옹호합니다. 이 접근 방식은 임베딩의 의도된 차원을 유지하는 것을 보장합니다.

![2024-05-17-NodePieceFromNodeIDstoTokens_10.png](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_10.png)

앵커 및 해당 거리에 대해 임베딩 조회 전략이 제안됩니다. 이는 각 앵커 id(예: 앵커=0)가 임베딩 행렬의 특정 행에 연결되며, 거리에 대해서도 동일한 방식으로 적용됨을 의미합니다(예: 거리=1은 embedding 1에 해당). 이 방법은 각 노드의 그래프 내 고유한 서명의 위치 및 관계적 측면을 효율적이고 의미 있게 인코딩하는 데 도움이 됩니다.

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

고려해야 할 점은 기존 방법에서 사용되는 고유 노드 ID의 인구보다 앵커 수와 앵커 거리가 훨씬 작다는 것이다!

## 앵커, 거리, 관계의 임베딩

방정식 (1)으로 구분된 세트의 변환을 통해 임베딩을 통한 벡터 표현으로 포함된 기본 단계가 여러 단계 포함됩니다 (텍스트와 방정식을 섞는 Medium.com의 제한으로 인해 아래 부분은 이미지로 게시되었습니다).

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

![Node representation matrix](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_12.png)

After that, authors sum vectors related to anchors, so that the matrix node representation is as follows:

![Node representation matrix](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_13.png)

## Encoding

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

방정식 (3)에 나온 행렬은 각 노드 u에 대해 해당 인코더(MLP 또는 Transformer)를 적용하여 벡터로 변환을 용이하게 합니다. 이것은 원래 연구에서 발표된 것처럼 진행됩니다.

이 인코더는 행렬을 각 노드에 대한 "펼쳐진" 벡터 표현으로 변환하여 복잡한 관계 및 위치 정보를 간결한 형태로 요약합니다.

아래는 이 인코딩 프로세스를 실제 예제로 설명하는 목적으로 노드 2에 대한 표현을 고려해 봅시다:

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

- Anchor를 0부터 시작하는 식별자로 할당하므로 id(Anchor 1) = 0 및 id(Anchor 2) = 1입니다.

- 관계는 유사하게 식별자가 부여되어 id(r1) = 0, id(r2) = 1, id(r3) = 2 등입니다.

- 거리는 id(dist=1) = 0, id(dist=2) = 1, id(dist=3) = 2 등과 같은 방식으로 인덱싱됩니다.

- k=m=2인 경우 각 노드에 대해 두 개의 Anchor와 두 개의 관계가 샘플링됩니다.

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

주어진 매핑을 가정하고 차원 공간 d = 3인 경우, 방정식 (2)에 따른 표현은 다음과 같이 나타납니다:

![image1](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_15.png)

![image2](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_16.png)

\*0부터 세는 것을 기억하세요 :) id(r1) = 0, id(Anchor1) = 0, 등등 :).

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

![이미지](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_17.png)

그 후, 각 앵커, 거리, 그리고 관계에 d-차원 임베딩이 적용됩니다. 이들은 크기 d의 벡터로 매핑됩니다. 예를 들어, d=3인 경우, 앵커, 거리, 그리고 관계에 대한 결과 행렬은 방정식 (2)와 일치하는 다음과 같을 수 있습니다:

![이미지](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_18.png)

물론 각 행렬의 값은 예시입니다 — 실제 모델에서는 아마 무작위 숫자들이 될 것입니다.

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

앵커와 관련된 벡터를 합한 후에 방정식(3)에 정해진대로 연결하면 다음과 같이 유도됩니다:

![image](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_19.png)

이러한 과정은 인코딩이 각 노드에 대해 앵커, 거리 및 관계 데이터의 복잡한 배열을 통합하고 단순화하여 다운스트림 그래프 처리 작업에 즉시 사용할 수 있도록 만드는 능력을 보여줍니다.

## 어떻게 활용할까요?

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

지금부터 사용자는 분류, 클러스터링 등과 같은 하위 작업을 진행할 수 있습니다.

각 노드의 표현은 이제 일반적인 특성처럼 사용할 수 있는 평면 벡터입니다.

노드의 고유한 특성을 캡처해야 합니다. 즉, 그래프 내에서의 위치, 관계 등을 포함해야 합니다.

논문 자체에서는 NodePiece 알고리즘의 기본적인 최적화, 트릭 및 개선 사항들이 여러 가지 언급되어 있지만, 이 간소화된 설명에서는 나머지 부분을 건너 뜁했습니다. 자세한 내용에 관심이 있다면 원본 논문을 꼭 읽어보시기를 강력히 권장합니다.

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

# 파트 4: 단순화된 구현

## 개요

노드피스 알고리즘의 기존 구현인 원본 논문의 저자들이 제공하고 있는 PyKeen 라이브러리 내의 버전들은 포괄적이지만 복잡합니다. 이러한 버전들은 성능 및 기존 프레임워크 내 통합을 최적화한 것이지만, 응용 프로그램 개발에 유용할 수 있지만, 알고리즘의 이론적 기초와 실제 코드 표현 사이의 개념적 공통점을 명확히하는데 어려움을 줄 수 있습니다. 이러한 복잡성은 알고리즘을 이해하려는 사람들에게 어려움을 줄 수 있습니다.

교육용으로 맞춤화된 구현의 제한된 가용성과 알고리즘의 기본 메커니즘을 이해하고자 하는 사람들을 위한 것이 아닌 알고리즘에 대해 더 깊이있게 이해하려는 사람들을 위한 간소화된 NodePiece의 버전을 개발하기로 선택했습니다. 이 버전은 명확성과 확장성을 염두에 두고 설계되었으며, 최적화와 프레임워크별 고려사항을 초과하는 부담 없이 알고리즘의 기본 메커니즘을 이해하려는 개인들에게 더 접근성 있는 진입점을 제공합니다.

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

위 간소화된 구현은 두 가지 주요 구성 요소로 구성되어 있습니다:

1. Tokenization Module: 이 코드 세그먼트는 앵커 및 관계를 선택하고 노드 표현을 구성하는 데 책임이 있으며 앵커, 관계 및 거리에 대한 식별자로 노드 표현을 정렬합니다. 이는 방정식(1)에서 설명된 프로세스와 일치하며, 노드의 앵커와의 관계 및 다양한 관계에 참여함으로써 노드의 고유한 "지문"을 생성하는 초기 단계를 총망라합니다.

2. Models Module: 이 부분은 식별자를 벡터 공간에 포함시키고 예측 모델을 구축하는 작업을 수행합니다. 이는 NodePiece 알고리즘의 후속 단계를 구현하는 것으로, 노드의 추상적인 표현을 밀집 벡터 표현으로 변환하고 이를 하류 기계 학습 작업에서 활용할 수 있게 합니다.

## 데이터

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

실습 목적으로 이 튜토리얼은 FB15k-237 데이터셋의 작은 하위 집합을 활용할 것입니다. 데이터셋의 축소판을 사용하는 이유는 실용성에 있습니다: 로컬에서 전체 데이터셋에 모델을 학습하는 데에는 시간이 많이 소요될 수 있습니다. 이 하위 집합은 결과 그래프가 일관되고 연결되어 있으며 전체 데이터셋의 구조적 무결성과 관계 복잡성을 유지하도록 꼼꼼히 만들어졌습니다. 이 방식을 통해 NodePiece 알고리즘을 빠르고 통찰력 있게 탐구할 수 있으며, 단순화된 구현을 더 관리하기 쉬운 규모로 실험하고 확장할 수 있습니다.

저희 축소된 데이터셋은 다음과 같은 구성 요소로 이루어져 있습니다:

1. 훈련 데이터셋 — 123,816 개의 삼중 세트.

2. 검증 데이터셋 — 402 개의 삼중 세트.

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

3. 테스트 데이터셋 — 224개의 삼중 세트가 있습니다.

4. 237가지 고유한 관계 유형이 있습니다.

## 토큰화

이제 그래프를 토큰화할 시간입니다. 우리는 단순화된 NodePiece 논리를 사용하는 사용자 정의 함수를 사용할 것입니다.

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

위의 내용을 다음과 같이 번역하겠습니다.

전체 데이터셋에서:

- 30개의 앵커를 선택합니다.
- 각 노드에 대해 20개의 가장 가까운 앵커를 선택합니다.
- 각 노드에 대해 10가지 관계를 선택합니다.

## 앵커 선정

앵커 선정 함수를 시작해보겠습니다.

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
def degree_anchor_select(g: nx.Graph, n_anchors: int|float = 0.1) -> Tuple[List[int], Dict[int, int]]:
    """앵커 선택 방법으로, 용이한 경중에 따라 기초합니다. 가장 간단한 휴리스틱에 기초하여
    이 그래프 내에서 가장 많은 노드와 연결되는 것으로 가정하고 노드의 최고 차수를 가진 노드를
    앵커로 선택합니다.

    매개변수
    ----------
    g : nx.Graph
        Networkx 그래프입니다.
    n_anchors : int | float, 선택사항
        선택할 앵커의 수로, 기본값은 0.1입니다.
        int일 경우 - 선택할 앵커 수입니다.
        float일 경우 - 앵커로 선택할 노드의 비율입니다.

    반환값
    -------
    Tuple[List[int], Dict[int, int]]
        1. 앵커 노드 목록입니다.
        2. 앵커 노드를 해당 ID로 매핑한 딕셔너리입니다. 앵커 ID는 [0, n_anchors) 범위 내에 있습니다.
    """
    if type(n_anchors) == float:
        n_anchors = int(g.number_of_nodes() * n_anchors)

    degrees = sorted(g.degree, key=lambda x: x[1], reverse=True)
    anchor_2_id = {}
    anchors = []
    for i, (node, _) in enumerate(degrees[:n_anchors]):
        anchors.append(node)
        anchor_2_id[node] = i

    return anchors, anchor_2_id
```

`degree_anchor_select` 함수는 이 작업에 대해 직관적이면서도 효과적인 방법을 보여줍니다. 노드의 차수를 앵커 선택 기준으로 활용합니다. 이 방법은 높은 차수를 갖는 노드가 더 많은 연결을 의미하므로 해당 그래프의 다양한 부분에 연결될 가능성이 높기 때문에 최적의 앵커로 간주합니다. 함수가 작동하는 방식을 단계별로 살펴보겠습니다:

1. 입력 매개변수: 함수는 NetworkX 그래프 `g`와 `n_anchors` 매개변수를 받습니다. `n_anchors` 매개변수는 선택할 앵커의 수를 지정하며, 이 값은 그래프의 노드 중 일정 비율(기본값은 0.1 또는 10%)을 앵커로 지정할 경우에 float로 지정하거나 원하는 앵커 수를 정수로 지정할 수 있습니다.

2. 차수 계산 및 정렬: 함수는 그래프 내 각 노드의 차수를 계산합니다. 그런 다음, 노드들을 차수에 따라 내림차순으로 정렬하여 앵커로 선택할 때 높은 차수의 노드를 우선하여 고려합니다.

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

3. ID 매핑 앵커: 선택된 각 앵커 노드를 0부터 시작하는 고유 식별자에 매핑하는 anchor_2_id 사전이 초기화됩니다. 이 매핑은 NodePiece 토큰화 프로세스의 이후 단계에서 앵커 노드를 효율적으로 식별하고 활용할 수 있게 합니다.

4. 반환 값: 이 함수는 선택된 앵커 노드의 목록과 anchor_2_id 사전을 포함하는 튜플을 반환합니다. 목록에는 앵커로 선택된 노드들이 포함되며, 사전은 이러한 앵커 노드와 할당된 ID 사이의 매핑을 제공하여 노드 표현 구성 시에 직접 참조할 수 있게 합니다.

## K개 가까운 앵커까지의 거리 구축

다음으로, 각 노드에서 K개 가까운 앵커 노드까지의 최단 경로 거리를 계산하는 함수를 구축할 예정입니다.

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
def build_distance_to_k_nearest_anchors(
        G: nx.Graph,
        anchors: List[int],
        anchor2id: dict,
        k_closest_anchors: int = 15,
        use_closest: bool = True) -> Tuple[np.ndarray, np.ndarray, int]:
    """그래프의 각 노드에 대해 k개 가장 가까운 앵커까지의 거리를 계산합니다.

    매개변수
    ----------
    G : nx.Graph
        네트워크x 그래프.
    anchors : List[int]
        앵커 노드 목록.
    anchor2id : dict
        앵커에서 id로의 매핑.
    k_closest_anchors : int, optional
        각 노드에서 선택할 k개 가장 가까운 앵커의 수, 기본값은 15
    use_closest : bool, optional
        가장 가까운 앵커를 사용해야 하는지 또는 모두 사용해야 하는지 여부, 기본값은 True

    반환값
    -------
    Tuple[np.ndarray, np.ndarray, int]
        다음으로 구성된 튜플:
        1. 노드와 앵커 사이의 거리 행렬. 형태: (노드 수, 앵커 수).
        2. 노드와 앵커 id 행렬. 형태: (노드 수, 앵커 수).
        3. 그래프 내의 최대 거리. 거리 인코딩/임베딩에 사용될 것입니다.
    """
    node_distances = {i: [] for i in range(G.number_of_nodes())}
    for a in tqdm(anchors):
        for node, dist in nx.shortest_path_length(G, source=a).items():
            node_distances[node].append((a, dist))

    node2anchor_dist = np.zeros((G.number_of_nodes(), len(anchors)))
    node2anchor_idx = np.zeros((G.number_of_nodes(), len(anchors)))
    unreachable_anchor_token = len(anchors)
    node2anchor_idx.fill(unreachable_anchor_token)

    max_dist = 0

    for node, distances in tqdm(node_distances.items()):
        indices_of_anchors = sorted(distances, key=lambda x: x[1])[:k_closest_anchors] if use_closest else node_distances[node]
        for i, (anchor, dist) in enumerate(indices_of_anchors):
            anchor_id = anchor2id[anchor]
            node2anchor_dist[node, anchor_id] = dist

            node2anchor_idx[node, i] = anchor_id
            if dist > max_dist:
                max_dist = dist
    unreachable_anchor_indices = node2anchor_idx == unreachable_anchor_token
    node2anchor_dist[unreachable_anchor_indices] = max_dist + 1
    return node2anchor_dist, node2anchor_idx, max_dist
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

3. 거리 및 인덱스 행렬의 인구: 이 함수는 각 노드의 앵커까지의 거리를 정렬하여 각 노드에 대해 가장 가까운 k개의 앵커를 선택합니다. 선택된 앵커까지의 거리와 인덱스는 각각의 배열에 저장됩니다. 이 과정에서 max_dist 변수는 관측된 최대 거리를 반영하도록 업데이트되어, 도달할 수 없는 앵커는 이 최대값을 초과하는 거리로 표시됩니다 (max_dist +1 - "마법 OOV 토큰" :)).

4. 도달할 수 없는 앵커 처리: 앵커에 대한 경로가 없어 도달할 수 없는 노드의 경우 거리가 max_dist + 1로 설정됩니다. 이 조정은 node2anchor_idx의 unreachable_anchor_token과 일치하는 인덱스를 식별하여 해당하는 node2anchor_dist 항목을 이 증가된 최대 거리로 설정함으로써 이뤄집니다. 이 메커니즘은 효과적으로 일부 앵커로부터 격리된 노드를 처리하며, 그래프 내에서 연결되지 않은 구성 요소의 가능성을 인정하여 NodePiece 표현의 무결성을 유지합니다.

5. 반환 값: 함수는 노드마다 가장 가까운 앵커 지점에 대한 포용도를 제공하는 node2anchor_dist 거리 행렬, 앵커 인덱스 행렬 node2anchor_idx 및 max_dist 값을 포함하는 튜플을 반환함으로써 마무리됩니다. 이러한 출력은 NodePiece 임베딩을 구성하는 기초를 제공하여 각 노드의 근접성에 대한 종합적인 매핑을 제공합니다.

## 관계적 컨텍스트 추출

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

다음 단계는 각 노드에 대한 관련 컨텍스트를 추출하는 것입니다.

```js
def sample_rels(pyg_g: pyg_data.Data, max_rels: int = 50) -> th.Tensor:
    """각 노드에 대해 m개의 외부 관계를 샘플링합니다. 노드의 관계가 m보다 적은 경우, 특수 토큰으로 출력을 채웁니다.

    매개변수
    ----------
    pyg_g : pyg_data.Data
        PyTorch Geometric 그래프.
    max_rels : int, optional
        사용할 관계의 최대 수, 기본값은 50입니다.

    반환
    -------
    th.Tensor
        각 노드에 대한 관계 행렬. 형태: (노드 수, max_rels).
        각 행은 특정 노드에 해당하며, 각 열은 관계(ID)에 해당합니다.
    """
    rels_matrix = []
    missing_rel_token = pyg_g.edge_type.max() + 1
    for node in tqdm(range(pyg_g.num_nodes)):
        node_edges = pyg_g.edge_index[0] == node
        node_edge_types = pyg_g.edge_type[node_edges].unique()
        num_edge_types = len(node_edge_types)

        if num_edge_types < max_rels:
            pad = th.ones(max_rels - num_edge_types, dtype=th.long) * missing_rel_token
            padded_edge_types = th.cat([node_edge_types, pad])
            padded_edge_types = padded_edge_types.sort()[0]
        else:
            sampled_edge_types = th.randperm(num_edge_types)[:max_rels]
            padded_edge_types = node_edge_types[sampled_edge_types].sort()[0]
        rels_matrix.append(padded_edge_types)
    return th.stack(rels_matrix)
```

1. 초기화: 함수는 PyTorch Geometric (PyG) 그래프 객체 pyg_g와 선택적으로 최대 관계 수를 결정하는 max_rels 매개변수를 필요로 합니다. 기본값은 50입니다. 모든 노드의 관계 데이터를 보유할 빈 리스트 rels_matrix가 준비되어 있습니다. 또한, 주어진 노드에 대한 관계가 없음을 나타내는 missing_rel_token이 정의되며, 사실상 다음 "어휘 외" 토큰으로 작동합니다. 이 토큰은 그래프에서 발견된 가장 큰 관계 ID보다 1 큰 값으로 설정됩니다.

2. 고유 관계 탐색: 각 노드에 대해 고유한 외부 엣지 유형(관계)을 찾습니다.

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

3. 관계 수 확인: 그래프의 각 노드에 대해, 이 함수는 노드에 연결된 모든 고유한 발신 관계 유형(엣지 유형)을 식별합니다.

4. 관계 수 조정: 노드의 고유한 관계 수가 max_rels 임계값보다 작을 경우, 관계 목록에 누락된 관계 토큰을 추가하여 지정된 최대값에 도달하도록 패딩 처리됩니다. 이는 모든 노드에서 관계적 문맥의 길이를 균일하게 유지합니다.

반대로, 노드가 max_rels로 허용하는 관계 유형보다 많이 연결된 경우, 제한에 맞게 일부가 무작위로 선택됩니다. 이 무작위 샘플링은 세부 정보와 계산 효율성 사이의 균형 유지에 필요한 것을 나타냅니다.

4. 관계 정렬 및 저장: 각 노드에 대한 샘플링된(또는 추가된) 관계는 일관된 순서를 유지하도록 정렬됩니다. 정렬된 목록은 그래프 전체에 대한 포괄적인 관계적 문맥 저장소를 점진적으로 구축하기 위해 rels_matrix에 추가됩니다.

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

6. 텐서 변환 및 결과 반환: 모든 노드에 대한 관계적 맥락 추출이 완료되면 rels_matrix 목록이 PyTorch 텐서로 변환됩니다. 이 텐서는 (num_nodes x max_rels) 모양을 가지며, 각 노드의 관계적 맥락을 구조화되고 기계가 읽을 수 있는 형식으로 체계적으로 나타냅니다.

## 기능 행렬

모든 작업이 완료되면 세 개의 행렬이 남습니다:

1. anchor_distances — 각 노드에서 K 개 가장 가까운 앵커까지의 거리. 차원: N 노드 x K 앵커.

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

2. anchor_hashes— 각 노드의 K 개 가장 가까운 앵커의 인덱스. 차원: N 노드 x K 앵커.

3. rel_hashes — 각 노드의 관계적 맥락. 차원: N 노드 x M 관계.

## 전체 모델 정의

이 연습에서는 좀 더 간단하면서 효과적인 지식 그래프 임베딩 모델인 TransE로 피벗합니다. 원래 논문은 RotatE를 사용했지만 — 좀 더 복잡한 모델 — TransE는 그래프 내에서 관계를 임베딩하는 기본 측면에 초점을 맞춘 단순화된 접근 방식을 제공합니다.

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

TransE는 헤드 노드, 관계 및 테일 노드로 구성된 트리플릿을 평가하는 방식으로 작동합니다. 이는 헤드와 테일 노드 사이에 지정된 관계가 존재할 확률을 나타내는 likelihood 점수를 할당합니다. 모델의 목적은 실제 트리플릿을 인공적으로 생성된(변형된) 트리플릿과 구별하기 위해 설계된 손실 함수의 최적화에 담겨 있습니다.

마크다운 형식으로 표를 변경하겠습니다:

![이미지 1](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_20.png)

![이미지 2](/assets/img/2024-05-17-NodePieceFromNodeIDstoTokens_21.png)

의사 코드로 작성하면 다음과 같습니다:

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
for (head, relation, tail) in data:
    head_embed = EMBED(head)
    rel_embed = EMED(relation)
    tail_embed = EMBED(tail)
    score = -1 * [(head_embed + rel_embed) - tail_embed]
    return score
```

We return -1 x score as we want to minimize the score, and maximize the likelihood of the triplet.

When interacting with NodePiece embeddings, TransE gets interesting when it comes to embedding head and tail nodes.

TransE embedding in this case will perform several steps. For each head or tail node:

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

1. 가장 가까운 앵커 인덱스를 가져와서 포함하세요.

2. 가장 가까운 앵커까지의 거리를 가져와서 포함하세요.

3. 관계적 맥락을 가져와서 포함하세요.

4. 방정식 (3)에 따라서: 앵커 ID 임베딩과 거리 임베딩을 더해주고, 관계 임베딩과 연결하여 하나의 벡터로 연결하세요.

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

5. 이 벡터를 인코더(MLP 또는 Transformer)를 통해 최종 임베딩을 얻기 위해 전달합니다.

우리의 구현은 다음과 같이 보일 것입니다 - 이 절차는 각 헤드 및 테일 노드에 대해 호출됩니다:

```js
    def embed_node(self, node: th.Tensor, closest_anchors, anchor_distances, rel_hash):

        # Dim: (N x K) values are anchor ids --> (N x K x D)
        anchor_embed = self.anchor_embed(closest_anchors[node])

        # Dim: (N x K) values are anchor distances --> (N x K x D)
        anchor_distances_embed = self.anchor_distances_embed(anchor_distances[node])

        # Dim: (N x M) values are relation types --> (N x M x D)
        rel_embed = self.rel_emb(rel_hash[node])

        # Dim: (N x K x D)
        combined_anchor_embed = anchor_embed + anchor_distances_embed

        # N x (K + M) x D
        stacked_embed = th.cat([combined_anchor_embed, rel_embed], dim=1)
        N, anchors_plus_rel, hidden_channels = stacked_embed.shape

        # reshape: (N x (K + M) x D) --> (N x (K + M) * D)
        flattened_embed = stacked_embed.view(N, anchors_plus_rel * hidden_channels)

        # N x (K + M) * D --> N x O
        lin_out = self.lin_layer(flattened_embed)

        return lin_out
```

1. anchor_embed는 앵커 해시에 대한 임베딩 조회입니다. 가중치 행렬의 차원은 ((K 앵커 + 1) x D 임베딩 크기)입니다. (N x K) 앵커 해시 행렬을 가져와 (N x K x D) 텐서를 반환합니다.

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

2. anchor_distances_embed은 앵커 거리를 위한 임베딩 조회입니다. 가중치 행렬은 ((최대 거리 +1) x D 임베딩 크기)의 차원을 가집니다. (N x K) 크기의 앵커 거리 행렬을 가져와 (N x K x D) 텐서를 반환합니다.

3. rel_embed은 관계 해시를 위한 임베딩 조회입니다. 가중치 행렬은 ((고유 관계 +1) x D 임베딩 크기)의 차원을 가집니다. (N x M) 크기의 관계 해시 행렬을 가져와 (N x M x D) 텐서를 반환합니다.

## TransE 모델 학습

이제 TransE 모델을 준비하고, PyTorch Lightning으로 래핑하여 FB15k-237 데이터 하위 집합에서 훈련할 것입니다.

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

use_swa = True
swa_lr = 0.05

pyg.seed_everything(111)

params = models.KGModelParams(
num_nodes=train_data_orig.num_nodes,
num_relations=train_features.n_rels+1,
embedding_dim=200,
max_distance=max_distance+1,
hidden_sizes=(400,),
num_anchors=train_features.n_anchors,
top_m_relations=train_features.m_relations,
device=device,
kg_model_type=models.ModelType.TransE,
drop_prob=0.2
)

model_pl = models.NodePiecePL(
params,
lr=5e-2,
train_features=train_features,
val_features=val_features)

저희가 인스턴스화한 모델은 다음과 같습니다:

NodePieceTransE(
(anchor_embed): Embedding(31, 200)
(anchor_distances_embed): Embedding(13, 200)
(rel_emb): Embedding(238, 200)

(lin_layer): Sequential(
(0): BatchNorm(8000)
(1): Linear(in_features=8000, out_features=400, bias=True)
(2): LeakyReLU(negative_slope=0.01)
(3): Dropout(p=0.2, inplace=False)
(4): Linear(in_features=400, out_features=200, bias=True)
)
)

이전에 설명한 이론 부분과 완벽하게 일치합니다.

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

첫 번째로는 기준 성능을 얻기 위해 모델을 어떤 학습도 하기 전에 유효성 검사 집합을 사용하여 모델을 평가할 것입니다. 결과는... 음... 예상대로 :)

```js
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃      검증 메트릭      ┃       데이터로더 0        ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│    val_hits_at_k_epoch    │            0.0            │
│    val_mean_rank_epoch    │   0.0005408872966654599   │
└───────────────────────────┴───────────────────────────┘
```

Hits@k(10) = 0.0이며 올바른 tail의 평균 순위는 매우 낮습니다. 이는 모델이 아직 학습되지 않았기 때문에 예상된 결과입니다.

모델 학습에 시간을 투자한 후에는 학습 이후의 성능을 확인하기 위해 모델을 평가할 수 있습니다. 체크포인트 콜백이 사용되었으므로 모든 반복 중에서 최상의 모델을 선택할 수 있습니다.

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

Hits@k 및 평균 순위 메트릭에서 상당한 향상이 있음을 볼 수 있을 것입니다.

```js
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃      Validate metric      ┃       DataLoader 0        ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│    val_hits_at_k_epoch    │    0.3240000009536743     │
│    val_mean_rank_epoch    │    0.2862264811992645     │
└───────────────────────────┴───────────────────────────┘
```

실제로, 예측 품질이 향상되었음을 확인할 수 있습니다. 물론, 이러한 작은 데이터 세트에 대해서도 실행하는 데 상당한 시간이 소요될 수 있습니다. 저는 1개의 GPU(T4), 16GB RAM 및 8개 가상 코어를 사용하는 Lightning.ai 플랫폼을 사용했고, 계산에 약 30분이 걸렸습니다.

규모를 감을 수 있도록 말씀드리면, 원래 논문에서는 훨씬 큰 데이터 세트를 사용했으며, 학습에 GPU로 몇 시간이 걸렸습니다. 논문의 표 10에서 한 실험은 400번의 epoch와 1,000개의 앵커로 7시간이 소요되었습니다.

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

이 블로그 포스트에서는 NodePiece에 대해 자세히 알아보았습니다. NodePiece는 자연어 처리를 위해 사용되는 Transformer의 토크나이제이션 기법에서 영감을 받은 그래프 신경망의 혁신적인 접근 방식입니다. Transformer의 토크나이저가 텍스트를 관리 가능한 조각으로 분해하여 텍스트 분석을 혁신했던 것처럼, NodePiece는 그래프에 유사한 개념을 적용합니다. 그래프의 다양한 부분을 나타내기 위해 기본 요소 또는 "토큰"의 집합을 사용하여 복잡한 네트워크를 쉽게 처리할 수 있게 합니다.

NodePiece가 거대하고 복잡한 그래프에서 노드를 나타내는 도전에 대응하기 위해 Transformer에서 사용되는 토크나이제이션 전략에서 아이디어를 빌렸다는 전반적인 내용을 시작으로 하였습니다. 이 접근 방식은 NodePiece가 노드의 본질과 관계를 효율적으로 포착할 수 있도록 하며, 각 노드를 명시적으로 식별할 필요가 없기 때문에(아이디를 통해서), 링크 예측, 노드 분류 등과 같은 작업에 대한 중요한 장점을 제공합니다.

또한 NodePiece의 이론적 배경에 대해 다루었는데, 그래프 내에서 노드의 관계와 위치에 집중함으로써 노드를 유연하고 일반화된 방식으로 표현하는 방법을 설명했습니다. 이것은 노드의 표현을 단순화하고 모델이 다양한 그래프에서 학습하고 적응하는 능력을 향상시킵니다.

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

마침내, 교육적 목적을 고려하여 설계된 NodePiece 모델의 간소화된 구현을 소개했습니다. 이 구현은 NodePiece가 어떻게 작동하며 현실 세계의 그래프 신경망 작업에 어떻게 적용될 수 있는지를 이해하기 쉽도록 개념을 세분화했습니다.

이 소개가 유용하게 느껴지길 바라며, 여러분의 그래프 프로젝트에서 NodePiece 토큰화를 활용할 수 있기를 기대합니다!

이 이야기를 읽어주셔서 감사드립니다.

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

- Bordes, A., Usunier, N., Garcia-Duran, A., Weston, J., & Yakhnenko, O. (2013). 다중 관계 데이터 모델링을 위한 임베딩 번역. 신경 정보 처리 시스템 발전, 26. https://proceedings.neurips.cc/paper/2013/hash/1cecc7a77928ca8133fa24680a88d2f9-Abstract.html
- Brochier, R., Guille, A., & Velcin, J. (2019). 노드 표현을 위한 글로벌 벡터. 월드 와이드 웹 컨퍼런스, 2587–2593. https://doi.org/10.1145/3308558.3313595
- Church, K. W. (2017). Word2Vec. 자연어 공학, 23(1), 155–162.
- Galkin, M., Denis, E., Wu, J., & Hamilton, W. L. (2021). Nodepiece: 대규모 지식 그래프의 구성적 및 매개 효율적 표현. arXiv Preprint arXiv:2106.12144.
- Sennrich, R., Haddow, B., & Birch, A. (2016). 드문 단어의 신경 기계 번역 : 서브워드 단위(arXiv:1508.07909; 버전 5). arXiv. https://doi.org/10.48550/arXiv.1508.07909
- Sun, Z., Deng, Z.-H., Nie, J.-Y., & Tang, J. (2019). RotatE: 복소 공간내 관계 회전에 의한 지식 그래프 임베딩(arXiv:1902.10197; 버전 1). arXiv. https://doi.org/10.48550/arXiv.1902.10197

# 친근한 마음으로 안내합니다 💬

In Plain English 커뮤니티의 일원이 되어 주셔서 감사합니다! 다음에도 놓치지 마세요:

- 작가를 박수와 팔로우해 주세요 ️👏️
- 팔로우하기: X | LinkedIn | YouTube | Discord | Newsletter
- 다른 플랫폼 방문: Stackademic | CoFeed | Venture | Cubed
- PlainEnglish.io에서 더 많은 콘텐츠를 만나보세요
