---
title: "LangChain과 Neo4j를 활용한 GraphRAG 소개"
description: ""
coverImage: "/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_0.png"
date: 2024-05-20 20:33
ogImage:
  url: /assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_0.png
tag: Tech
originalTitle: "Introducing GraphRAG with LangChain and Neo4j"
link: "https://medium.com/microsoftazure/introducing-graphrag-with-langchain-and-neo4j-90446df17c1e"
---

![그림](/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_0.png)

LLM-파워드 애플리케이션 랜드스케이프에서 그래프 기반 기술에 대한 제 최근 기사에서는 이러한 데이터 구조가 다중 에이전트 프레임워크의 맥락에서 어떻게 활용될 수 있는지 탐구했습니다. 더 구체적으로, 2024년 1월에 소개된 새로운 LangChain 라이브러리인 LangGraph에 대해 다루었는데, 이는 에이전트 애플리케이션을 위한 대표적 프레임워크로서 그래프 수학적 객체를 기반으로 합니다.

LangGraph의 주요 목표는 기존 LangChain의 주요 제한사항인 실행 중 사이클 부재를 극복하는 것입니다. 이 제한사항은 개발 목적에 따라 방향성이 있는 비순환 그래프(DAGs)에 쉽게 사이클을 도입하여 우회할 수 있습니다.

하지만 그래프는 Retrieval Augmented Generation (RAG) 시나리오에서도 지식베이스를 조직하는 강력한 도구입니다. 구체적으로, 그래프는 "검색" 단계를 강화하여 더 의미 있는 컨텍스트 검색을 이끌어내어 보다 정확한 생성된 응답을 얻는 데 도움이 됩니다. 이를 위해, 아이디어는 지식베이스를 그래프 기반 데이터베이스(예: Neo4j)에 저장하고, LLM의 의미론적 파워를 활용하여 엔티티와 관계를 올바르게 추출하고 매핑하는 것입니다.

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

이제 질문은: 어떻게 하는 걸까요? 다행히도 LangChain은 LLMGraphTransformer라는 강력한 라이브러리를 개발했습니다. 이 라이브러리의 목적은 구조화되지 않은 텍스트 데이터를 그래프 기반 표현으로 변환하는 것입니다.

이 라이브러리가 어떻게 작동하는지 완벽히 이해하기 위해, 먼저 그래프의 작동 방식과 관련 용어를 다시 확인해 보겠습니다.

## 그래프와 그래프 데이터베이스

그래프는 객체간의 쌍별 관계를 모델링하는 데 사용되는 수학적 구조입니다. 노드와 관계 두 가지 주요 요소로 구성됩니다.

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

- 노드: 노드는 전통적인 데이터베이스에서 레코드로 볼 수 있습니다. 각 노드는 사람이나 장소와 같은 객체 또는 개체를 나타냅니다. 노드는 "고객" 또는 "제품"과 같은 역할에 따라 분류되는 레이블에 의해 분류되어 쿼리됩니다.
- 관계: 이것들은 노드 간의 연결을 나타내며 서로 다른 개체 간의 상호 작용 또는 관계를 정의합니다. 예를 들어, 사람은 "EMPLOYED_BY" 관계를 통해 회사에 연결될 수 있습니다. 또는 "LIVES_IN" 관계를 통해 장소에 연결될 수 있습니다.

![그래프](/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_1.png)

유사한 구조로 데이터를 저장하기 위해 2000년대 초에 새로운 데이터베이스 패밀리가 소개되었습니다: 그래프 데이터베이스. 그래프 데이터베이스는 데이터 사이의 관계를 데이터 자체와 동등하게 중요하게 취급하도록 설계된 데이터베이스 유형입니다. 그들은 서로 연결된 데이터와 복잡한 쿼리를 효율적으로 처리하기 위해 최적화되어 있습니다.

가장 잘 알려진 것 중 하나는 Neo4j이며, 이 데이터베이스는 노드와 관계뿐만 아니라 속성, 레이블 및 경로 기능을 활용하여 데이터를 표현하고 저장하는 유연한 그래프 구조를 사용합니다.

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

- 속성: 노드와 관계 모두 속성을 포함할 수 있습니다. 이는 key-value 쌍으로 저장된 속성으로, 엔티티에 관한 구체적인 세부 정보를 제공합니다. 예를 들어, 사람의 이름이나 나이 또는 관계의 길이와 같은 정보를 포함할 수 있습니다.
- 레이블: 레이블은 노드에 할당된 태그로, 노드를 다양한 유형으로 분류하는 데 사용됩니다. 단일 노드는 여러 레이블을 가질 수 있으며, 이는 그래프를 보다 동적이고 유연하게 조회하는 데 도움이 됩니다.
- 경로: 경로는 노드와 관계를 연결하는 순서가 정해진 시퀀스를 설명합니다. 그들과 그들 사이를 연결하는 경로를 나타내며, 다른 노드가 어떻게 서로 연결되는지 보여줍니다. 경로는 조회에서 유용하며, 소셜 네트워크에서 한 사람에서 다른 사람까지 모든 가능한 경로를 발견하는 것과 같은 노드 간의 관계를 찾는 데 사용됩니다.

이것은 Neo4j가 특히 소셜 네트워크, 추천 시스템 및 사기 탐지와 같은 응용 프로그램에 적합한 이유입니다. 여기서 관계와 동적 조회가 중요합니다.

## RAG 및 GraphRAG

검색 증강 생성(RAG)은 LLM(언어 모델)을 기반으로 하는 응용 프로그램 시나리오에서 강력한 기술로, 다음 문제에 대응합니다: "LLM이 훈련된 데이터 세트에 포함되지 않는 내용을 LLM에게 물어보고 싶다면 어떻게 해야 하나요?". RAG의 아이디어는 LLM과 우리가 탐색하고자 하는 지식 베이스를 분리하는 것이며, 이는 적절히 벡터화되거나 임베드되어 VectorDB에 저장된 지식 베이스에서 이루어집니다.

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

RAG는 세 단계로 구성되어 있습니다:

- 검색 → 사용자의 쿼리와 해당 벡터를 고려했을 때, 가장 유사한 문서 조각들(사용자 쿼리의 벡터에 더 가까운 벡터에 해당하는 것들)이 검색되어 LLM의 기본 맥락으로 사용됩니다.

![image](/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_2.png)

- 증강 → 검색된 맥락은 추가적인 지시사항, 규칙, 안전 가드레일 및 프롬프트 엔지니어링 기술에 특히 특징적인 유사한 방법을 통해 풍부화됩니다.

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

<img src="/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_3.png" />

- Generation → 사용자의 쿼리에 대한 응답을 LLM이 증강된 컨텍스트를 기반으로 생성합니다.

<img src="/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_4.png" />

언급했듯이 일반적인 RAG 애플리케이션은 모든 내장된 지식 베이스가 저장된 기저 VectorDB를 가정합니다. 그러나 GraphRAG의 경우 이 접근 방식이 약간 변합니다.

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

사실 그래프 RAG는 "검색" 단계에서 작동합니다. 그래프 구조의 유연성을 활용하여 지식 베이스를 저장하고, 더 많은 관련 문서 조각을 검색하고 이를 컨텍스트로 확장하는 것을 목표로 합니다 (마이크로소프트의 그래프 RAG에 대한 첫 실험에 대해 여기에서 읽을 수 있습니다).

지식을 검색하는 데 그래프 데이터베이스를 활용하는 두 가지 주요 방법이 있습니다:

- 그래프 검색(키워드 검색인)을 완전히 의지하여 관련 문서를 검색한 후, 생성 모델로 최종 응답을 생성하는 데 사용할 수 있습니다.
- 그래프 검색과 벡터 검색(임베딩을 통한)과 같은 더 발전된 LLM 관련 검색을 결합할 수 있습니다.

참고: Neo4j도 벡터 검색을 지원하며, 이는 하이브리드 그래프 RAG 시나리오에 매우 적합하게 만듭니다. 다음 섹션에서 확인할 수 있습니다.

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

무슨 방식을 선택하든, 프로세스의 핵심 단계는 지식 베이스를 그래프로 구성하는 것입니다. 우리에게 다행히도, LangChain은 이 목표에 정확히 부합하는 새 라이브러리를 소개했습니다: 비구조화된 지식을 그래프 데이터베이스에 매핑하기 쉽게 만들어주는 것을 목표로 한 새 라이브러리를 도입했습니다. 이 글 전체를 통해 우리는 Neo4j를 활용한 구현을 살펴볼 것입니다.

## LangChain 및 LLMGraphTransformer와 함께 구현하기

LangChain은 LLM을 애플리케이션에 통합하기 쉽게 만드는 다양하고 계속 성장하는 라이브러리, 사전 구축된 구성 요소 및 커넥터들을 제공하는 활기찬 생태계를 제공합니다. 최근 릴리스 중 하나가 GraphRAG 방향으로 나아간 것인 LLMGraphTransformer입니다.

LLMGraphTransformer의 좋고 강력한 점은 현재 OpenAI 모델(포함된 Azure OpenAI 및 Mistral)을 활용하여 텍스트 내의 개체와 관계를 파싱하고 분류한다는 것입니다. 실제로 LLM의 자연어 기능 덕분에 결과 그래프는 문서 내의 가장 정교한 상호 연결성조차도 정확하게 포착하여 이전 방법에 비해 극도로 정확합니다.

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

이제부터는 몇 줄의 코드로 구조화되지 않은 문서에서 시작하여 완전히 채워진 그래프를 얻을 수 있습니다 (그 뒤에 있는 로직을 확인하고 싶다면, 여기서 소스 코드를 볼 수 있습니다).

예제를 살펴보겠습니다. 먼저, 무료 인스턴스인 Neo4j Aura 데이터베이스를 사용하겠습니다 (이 자습서를 따라 직접 만들 수 있습니다) 그리고 Azure OpenAI GPT-4 모델을 사용할 것입니다.

AuraDB 인스턴스를 생성하고 나면, 다음에서 실행 중인 것을 확인할 수 있을 겁니다:

![이미지](/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_5.png)

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

아래는 인스턴스에 연결해야 하는 변수들입니다:

```js
os.environ["NEO4J_URI"] = os.getenv("NEO4J_URI");
os.environ["NEO4J_USERNAME"] = "neo4j";
os.environ["NEO4J_PASSWORD"] = os.getenv("NEO4J_PASSWORD");
api_key = os.getenv("AZURE_OPENAI_API_KEY");
azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT");
api_version = "2023-07-01-preview";
```

이제 LLM을 초기화해보겠습니다:

```js
llm = AzureChatOpenAI(
  (model = "gpt-4"),
  (azure_deployment = "gpt-4"),
  (api_key = api_key),
  (azure_endpoint = azure_endpoint),
  (openai_api_version = api_version)
);
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

이제 시작할 수 있는 샘플 문서가 있습니다! (해리 포터와 마법사의 돌의 처음 몇 줄을 선택했습니다):

```js
#LLMTransformer 모델 초기화
llm_transformer = LLMGraphTransformer(llm=llm)

#문서 변환
from langchain_core.documents import Document

text = """
더즈리 부부는 프리벳 드라이브 4번에 살았는데, 그들은 매우 평범하다고 자랑스러워했습니다. 상당한 정도로 정상적인 것이라고 말이죠. 그들은 이상하거나 신비한 어떤 일에도 연루될 것으로는 전혀 예상하지 못한 사람들이었습니다. 왜냐하면 그들은 그러한 헛소리를 믿지 않았거든요.
두즐리 씨는 대두를 만드는 그런닝스라는 회사의 사장이었습니다. 그는 거의 목이 없는 크고 굵은 남자였는데, 아주 큰 수염은 있었습니다. 두즐리 부인은 날씬하고 금발이었으며, 보통의 두 배 정도의 목을 가졌는데, 이것은 이웃을 엿보기 위해 정원 울타리 위를 많이 빙빙 돌아다닐 때 매우 유용했습니다. 두즐리 부부는 더드리라 불리는 작은 아들을 가지고 있었고, 그들은 자신들의 의견으로는 그보다 더 훌륭한 아이는 어디에도 없다고 생각했습니다.
더즈리 부부는 원하는 모든 것을 가지고 있었지만, 비밀도 하나 있었고, 가장 큰 두려움은 누군가가 그것을 발견할까봐라는 것이었습니다. 그들은 포터 가족에 대해 누군가가 알아낼까 봐 가만히 있을 수 없다고 생각했습니다. 더즈리 부인은 포터 부인이었는데, 하지만 여러 해동안 만나지 않았습니다. 사실 더즈리 부인은 언니가 없다고 속이곤 했습니다. 왜냐하면 그녀의 언니와 그녀 생각엔 아무것도 안 하는 남편이 흔치 않은 더즈리식인 것과 같이 달랐기 때문이었습니다. 더즈리 부부는 포터 가족이 거리에 도착하면 이웃들이 무슨 말을 할 지 상상하며 소름 끼치곤 했습니다. 더즈리 부부는 포터 가족이 작은 아들까지 가졌다는 것을 알고 있었지만, 심지어 그를 본 적이 한 번도 없었습니다. 이 아이를 만나지 않는 것은 포터 가족을 멀리하고 싶은 다른 이유였습니다. 둘리는 그런 아이와 어울리길 원치 않았기 때문이죠.
"""
documents = [Document(page_content=text)]
graph_documents = llm_transformer.convert_to_graph_documents(documents)
print(f"노드:{graph_documents[0].nodes}")
print(f"관계:{graph_documents[0].relationships}")
```

```js
노드: [
  Node((id = "Mr. Dursley"), (type = "Person")),
  Node((id = "Mrs. Dursley"), (type = "Person")),
  Node((id = "Dudley"), (type = "Person")),
  Node((id = "Privet Drive"), (type = "Location")),
  Node((id = "Grunnings"), (type = "Organization")),
  Node((id = "Mrs. Potter"), (type = "Person")),
  Node((id = "The Potters"), (type = "Family")),
];
관계: [
  Relationship(
    (source = Node((id = "Mr. Dursley"), (type = "Person"))),
    (target = Node((id = "Mrs. Dursley"), (type = "Person"))),
    (type = "MARRIED_TO")
  ),
  Relationship(
    (source = Node((id = "Mr. Dursley"), (type = "Person"))),
    (target = Node((id = "Dudley"), (type = "Person"))),
    (type = "PARENT_OF")
  ),
  Relationship(
    (source = Node((id = "Mrs. Dursley"), (type = "Person"))),
    (target = Node((id = "Dudley"), (type = "Person"))),
    (type = "PARENT_OF")
  ),
  Relationship(
    (source = Node((id = "Mr. Dursley"), (type = "Person"))),
    (target = Node((id = "Grunnings"), (type = "Organization"))),
    (type = "WORKS_AT")
  ),
  Relationship(
    (source = Node((id = "Mr. Dursley"), (type = "Person"))),
    (target = Node((id = "Privet Drive"), (type = "Location"))),
    (type = "LIVES_AT")
  ),
  Relationship(
    (source = Node((id = "Mrs. Dursley"), (type = "Person"))),
    (target = Node((id = "Privet Drive"), (type = "Location"))),
    (type = "LIVES_AT")
  ),
  Relationship(
    (source = Node((id = "Mrs. Dursley"), (type = "Person"))),
    (target = Node((id = "Mrs. Potter"), (type = "Person"))),
    (type = "SISTER_OF")
  ),
  Relationship(
    (source = Node((id = "The Dursleys"), (type = "Family"))),
    (target = Node((id = "The Potters"), (type = "Family"))),
    (type = "WANTS_TO_AVOID")
  ),
];
```

보시다시피, llm_transformer는 우리가 지정할 필요 없이 데이터에서 관련 엔티티와 관계를 캡처했습니다. 이제 이러한 노드와 관계를 AuraDB에 저장해야 합니다.

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
graph.add_graph_documents(
  graph_documents,
  (baseEntityLabel = True),
  (include_source = True)
);
```

그리고 다 끝났어요! 이제 우리는 채워진 그래프 데이터베이스를 가지게 되었습니다. 이제 우리 온라인 AuraDB 인스턴스에서 올바르게 업로드된 문서를 확인할 수 있습니다.

<img src="/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_6.png" />

또한 우리 DB의 그래픽 표현을 다음의 Python 함수로 그릴 수도 있습니다:

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
# 지정된 Cypher 쿼리에서 그래프를 보여주는 함수
default_cypher = "MATCH (s)-[r:!MENTIONS]->(t) RETURN s,r,t LIMIT 50"

def showGraph(cypher: str = default_cypher):
    # 쿼리를 실행할 neo4j 세션 생성
    driver = GraphDatabase.driver(
        uri=os.environ["NEO4J_URI"],
        auth=(os.environ["NEO4J_USERNAME"],
              os.environ["NEO4J_PASSWORD"]))
    session = driver.session()
    widget = GraphWidget(graph=session.run(cypher).graph())
    widget.node_label_mapping = 'id'
    #display(widget)
    return widget

showGraph()
```

<img src="/assets/img/2024-05-20-IntroducingGraphRAGwithLangChainandNeo4j_7.png" />

이제 그래프 데이터베이스가 준비되었으니, 검색 기능을 향상시키는 벡터 검색 기능을 추가할 수 있습니다. 이를 위해 임베딩 모델이 필요하며, Azure OpenAI text-embedding-ada-002를 다음과 같이 사용하겠습니다:

```js
from langchain_openai import AzureOpenAIEmbeddings

embeddings = AzureOpenAIEmbeddings(
    model="text-embedding-ada-002",
    api_key=api_key,
    azure_endpoint=azure_endpoint,
    openai_api_version=api_version,
)
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
vector_index = Neo4jVector.from_existing_graph(
  embeddings,
  (search_type = "hybrid"),
  (node_label = "Document"),
  (text_node_properties = ["text"]),
  (embedding_node_property = "embedding")
);
```

이제 vector_index를 벡터 유사도 방법을 사용하여 쿼리할 수 있습니다:

```js
query = "떄리 누구야?";

results = vector_index.similarity_search(query, (k = 1));
print(results[0].page_content);
```

```js
버지니아 주 프리벳 드라이브 4번지에 사는 더즐리 부부는 매우 정상적인 사람들이라고
자랑스러워했다. 그들은 이상하거나 신비한 일에 관여할 것으로 생각되는 마지막
사람들 중 하나였다. 그들은 이러한 말장난을 믿지 않았다. 더즐리 씨는
드릴을 만드는 그러닝스 회사의 사장이었다. 그는 목이 거의 없는 건장한 사나이였지만,
매우 커다란 수염을 키웠다. 더즐리 부인은 날씬하고 금발이었으며, 보통의 목 두배의
길이를 가졌으며 이 긴 목은 너네 집 이웃들을 엿보는 데 매우 유용했다. 더즐리
가족은 말 그대로 어디서도 찾아볼 수 없는 더 좋은 아이가 없다고 생각했다. 그들은
원하는 모든 것을 가지고 있었지만, 그들은 비밀을 하나 갖고 있었으며, 그들의
가장 큰 두려움은 누군가 그 비밀을 발견할까 봐였다. 그들은 포터 가족에 대해
누군가에게 알려지는 것을 견딜 수 없을 거라고 생각했다. 포터 부인은 더즐리 부인의
자매였지만 그들은 여러 해간 만나지 않았다. 사실, 더즐리 부인은 자신에게
자매가 없는 것처럼 꾸역꾸역 거짓말쳤다. 왜냐하면 그녀의 자매와 그녀의
아무 소용 없는 남편은 가능한 한 더즐리 씨와 반대되는 사람이었다. 더즐리
씨 부부가 거주하는 골목에 포터 가족이 도착하면 이웃들이 무슨 말을 할지
생각만 해도 더즐리 부부는 오싹했다. 포터 가족이 또 다른 작고 맹수를 가졌다는
것을 더즐리 부부는 알고 있었지만, 그들은 심지어 그 아이를 본 적이 없었다. 이
아이가 포터 가족을 피해야 하는 또 다른 좋은 이유였다. 그들은 더 말해야 하는
이유는 없었다. 더즐리 부부는 더즐리 씨 부부 내의 아이와 섞이는 것을 원치 않았다.
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

물론, 텍스트를 조각내지 않았기 때문에 쿼리는 전체 문서를 반환할 것입니다. 다음 파트에서는 더 큰 문서를 다룰 때 이것이 관련성을 가지게 되는 방법을 알아볼 것입니다.

마지막 단계는 모델에서 생성된 실제 답변을 가져오는 것입니다. 이를 위해 두 가지 다른 접근 방법을 활용할 수 있습니다:

- Neo4j의 Cypher 쿼리 언어를 활용하여 그래프 데이터베이스와 상호 작용하는 사전 구축된 구성 요소인 CypherChain을 활용합니다. Neo4j와 네이티브로 통합되어 있으므로 AuraDB 그래프 기능과 상호 작용하여 쿼리 결과를 이해함으로써 문맥을 고려한 응답을 활성화합니다. 높은 정밀도, 문맥 인식, 그리고 Neo4j의 그래프 기능과의 직접적 상호 작용이 필요할 때 권장됩니다.

```js
from langchain.chains import GraphCypherQAChain

chain = GraphCypherQAChain.from_llm(graph=graph, llm=llm, verbose=True)
response = chain.invoke({"query": "Mr. Dursley의 직업은 무엇인가요?"})
response
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
> 새로운 GraphCypherQAChain 체인에 입장 중...
생성된 Cypher:
MATCH (p:Person {id: "Mr. Dursley"})-[:WORKS_AT]->(o:Organization) RETURN o.id
전체 컨텍스트:
[{'o.id': 'Grunnings'}]

> 체인 완료.
{'query': "Mr. Dursley의 직업은 무엇인가요?",
 'result': 'Mr. Dursley는 Grunnings에서 일합니다.'}
```

- 고전적인 QA 체인을 활용하여 LangChain의 데이터 저장소(vectordb 및 graphdb 모두)에 적용 가능한 vector_index.as_retriever() 메서드를 사용합니다.

```js
from langchain.chains import RetrievalQA

qa_chain = RetrievalQA.from_chain_type(
    llm, retriever=vector_index.as_retriever()
)

result = qa_chain({"query": "Mr. Dursley의 직업은 무엇인가요?"})
result["result"]
```

```js
"Mr. Dursley는 드릴을 만드는 회사인 Grunnings의 이사입니다.";
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

응답의 정확성을 잠시 보류하는 것이 좋습니다. 문서는 아직 청크로 나누어지지 않았으므로 현재 벤치마킹하는 것은 의미가 없습니다. 다음 파트에서는 이러한 구성 요소 간의 차이를 인식하고 이를 통해 훌륭한 RAG 성능을 낼 수 있는 방법에 대해 알아볼 것입니다.

## 결론

이 시리즈의 제1부에서는 그래프 데이터베이스의 기초와 RAG 기반 응용 프로그램의 맥락에서 그 이유를 다뤘습니다. 제2부에서는 이 첫 번째 부분에서 소개 된 모든 구성 요소를 활용한 그래프 기반 접근 방식의 실제 구현을 살펴볼 것입니다. 전체 GitHub 코드는 2부와 함께 제공될 예정입니다.

제2부를 기대해주세요!

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

## 참고 자료

- [Directed Acyclic Graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph?ref=blog.langchain.dev)
- [LangGraph 블로그](https://blog.langchain.dev/langgraph/)
- [Python API 문서](https://api.python.langchain.com/en/latest/_modules/langchain_experimental/graph_transformers/llm.html#LLMGraphTransformer)
- [Neo4j Cypher 소개](https://neo4j.com/docs/getting-started/cypher-intro/#:~:text=Cypher%20is%20Neo4j`s%20graph%20query,how%20to%20go%20get%20it).
- [Microsoft Research 블로그](https://www.microsoft.com/en-us/research/blog/graphrag-unlocking-llm-discovery-on-narrative-private-data/)
- [LangChain Quickstart](https://langchain.com/quickstart)
