---
title: "고급 RAG 08 Self-RAG"
description: ""
coverImage: "/assets/img/2024-05-20-AdvancedRAG08Self-RAG_0.png"
date: 2024-05-20 21:10
ogImage: 
  url: /assets/img/2024-05-20-AdvancedRAG08Self-RAG_0.png
tag: Tech
originalTitle: "Advanced RAG 08: Self-RAG"
link: "https://medium.com/ai-advances/advanced-rag-08-self-rag-c0c5b5952e0e"
---


이 기사는 흔한 시나리오로 시작됩니다: 공개 시험을 보는 경우입니다. 일반적으로 두 가지 전략을 사용합니다:

- 방법 1: 익숙한 주제에 대해서는 빠르게 답변하고, 익숙하지 않은 주제에 대해서는 참고서를 열어서 확인하고, 관련 부분을 빠르게 찾아내어 정리하고 요약한 다음, 시험지에 답변합니다.
- 방법 2: 모든 주제에 대해 책을 참고합니다. 적절한 부분을 찾아내고, 정리하고 요약한 다음, 시험지에 답변합니다.

분명히 방법 1이 선호되는 방법입니다. 방법 2는 시간이 소비될 수 있고, 관련성 없는 정보나 잘못된 정보가 들어올 수 있어 혼란과 실수를 야기할 수 있습니다. 심지어 처음에 이해한 부분에서도 발생할 수 있습니다.

하지만, 방법 2는 고전적인 RAG 프로세스를 보여주며, 방법 1은 자체 RAG 프로세스를 대표합니다. 이에 대해 이 기사에서 더 자세히 다룰 것입니다.

<div class="content-ad"></div>

# 개요

그림 1은 RAG 및 Self-RAG의 주요 프로세스를 비교한 것을 보여줍니다:

![그림](/assets/img/2024-05-20-AdvancedRAG08Self-RAG_0.png)

Self-RAG는 세 단계로 구성되어 있습니다:

<div class="content-ad"></div>

- 필요한 경우 검색: 모델이 검색을 요구하는 경우, 예를 들어 "미국 주가 이름을 어떻게 얻었습니까?" (그림 1의 오른쪽 상단)와 같은 쿼리가 있을 때, 모델의 출력에는 [검색] 토큰이 포함됩니다. 이는 쿼리와 관련된 내용을 검색해야 함을 나타냅니다. 반면에 "최고의 여름 휴가에 대해 에세이를 쓰세요" (그림 1의 오른쪽 아래)와 같이 물어볼 때, 모델은 검색 없이 직접 답변을 생성하도록 선택합니다.
- 병렬 생성: 모델은 프롬프트와 검색된 콘텐츠를 모두 사용하여 출력을 생성합니다. 이 과정에서 세 가지 유형의 반영 토큰이 검색된 콘텐츠의 관련성을 나타냅니다.
- 평가 및 선택: 단계 2에서 생성된 콘텐츠가 평가되고, 최상의 세그먼트가 출력으로 선택됩니다.

상기 모델은 특별히 훈련된 모델이라는 것을 유의하십시오. 이 모델의 훈련 과정은 이 기사의 후반부에서 논의될 것입니다.

# 반영 토큰

Self-RAG 프레임워크의 RAG와 비교했을 때, Self-RAG 프레임워크의 차이는 생성 중 더 정확한 제어를 위해 반영 토큰을 사용한다는 것입니다. 그림 2에서 보여집니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-AdvancedRAG08Self-RAG_1.png" />

본질적으로, self-RAG는 네 가지 명확한 판단을 내립니다:

- [Retrieve]: 리소스 R로부터 정보를 검색할지를 결정하는 의사결정 과정.
- [IsREL]: 주어진 데이터 d가 문제 x를 해결하는 데 필요한 정보를 포함하고 있는지를 결정하는 관련성 확인.
- [IsSUP]: 제공된 응답 y의 내용이 데이터 d로부터 지원되는지를 확인하는 검증 과정.
- [IsUSE]: 문제 x에 대한 응답 y의 유용성을 평가하는 평가 과정. 결과는 1에서 5까지의 점수로, 5는 가장 높은 유용성을 나타냅니다.

RAG에서 검색은 상태에 관계없이 항상 처음에 수행되는 고정된 과정입니다. 반면 self-RAG는 반사 토큰을 도입하여 LLM을 더 적응적이고 지능적으로 만듭니다. LLM이 텍스트를 생성하다가 불확실성이 발생하는 부분에 도달하면 반사 토큰에서 일시 정지하여 신속하고 정확한 검색을 수행한 후 새로 습득한 정보를 사용하여 생성을 재개합니다.

<div class="content-ad"></div>

# 코드 설명

self-RAG 프로세스를 직관적으로 이해하기 위해 먼저 코드를 살펴보고 모델의 훈련 과정을 설명하겠습니다.

self-RAG는 오픈 소스이며, Langchain과 LlamaIndex에는 각각의 구현이 있습니다. 우리는 설명을 위해 LlamaIndex의 구현을 참조할 것입니다.

## 환경 설정

<div class="content-ad"></div>

먼저, 환경을 설정하세요.

```js
(base) Florian@instance-1:~$ conda create -n llamaindex python=3.11

(base) Florian@instance-1:~$ conda activate llamaindex


(llamaindex) Florian@instance-1:~$ pip install llama-index

(llamaindex) Florian@instance-1:~$ pip install huggingface-hub

(llamaindex) Florian@instance-1:~$ huggingface-cli login
```

설치 후, LlamaIndex의 대응 버전은 다음과 같습니다:

```js
llama-index                             0.10.20

llama-index-core                        0.10.20.post2
```

<div class="content-ad"></div>

위의 표를 Markdown 형식으로 변경해주세요.

<div class="content-ad"></div>

```js
import os
os.environ["OPENAI_API_KEY"] = "여러분의 오픈AI API 키"

from llama_index.core import Document, VectorStoreIndex
from llama_index.core.retrievers import VectorIndexRetriever
from llama_index.core.readers import SimpleDirectoryReader
from pathlib import Path


# 옵션: SelfRAGPack 다운로드
# 첫 실행 시 SelfRAGPack을 다운로드해야 합니다. 
# 다음 실행부터는 이 부분을 주석 처리할 수 있습니다.
from llama_index.core.llama_pack import download_llama_pack
download_llama_pack(
    "SelfRAGPack",
    "./self_rag_pack")

from llama_index.packs.self_rag import SelfRAGQueryEngine

# 이전에 다운로드하고 저장한 Llama2 모델이 있는 디렉토리.
download_dir = "여러분의 다운로드 모델 디렉토리"

# 테스트 문서 생성
documents = [
    Document(
        text="남극 얼음 위를 '웨들'이라고 불리는 물개 떼가 지나다녔다. 그들의 턱시도 같은 깃털은 눈 위에서 돋보였다."
    ),
    Document(
        text="펭귄 중 가장 키가 큰 황제펭귄은 다른 어떤 새보다도 더 깊이 다이빙을 할 수 있어서 500m 이상의 심해까지 다이빙을 합니다."
    ),
    Document(
        text="펭귄들의 흑백색깔은 위험 방어라는 화장법의 한 종류인 카운터셰이딩입니다. 위에서 보면 펭귄의 검은 등은 바다 심지와 어우러지고, 아래에서는 펭귄의 흰 배는 밝은 표면과 어우러집니다."
    ),
    Document(
        text="수직 자세이지만, 펭귄은 날지 못하는 조류입니다. 그들의 날개는 지느러미로 진화했기 때문에 수중에서 전문 수영가입니다."
    ),
    Document(
        text="가장 빠른 펭귄 종류인 젠투 펭귄은 시속 36킬로미터까지 수영할 수 있으며, 수중을 순찰하는 동안 지느러미와 윤곽을 이용해 물을 가르는 식으로 전진합니다."
    ),
    Document(
        text="펭귄은 집단생활을 하는 조류입니다. 많은 종들이 번식을 위해 수만 마리까지 이를 결성합니다."
    ),
    Document(
        text="펭귄은 놀랍게도 귀가 우수하며 지저분한 떼 속에서 배우량과 새끼를 식별하는 데 명확한 호출을 의존합니다."
    ),
    Document(
        text="가장 작은 펭귄 종인 리틀 블루 펭귄은 약 40cm 높이로, 남부 호주와 뉴질랜드 해안가에서 발견됩니다."
    ),
    Document(
        text="번식 기간 중, 수컷 황제펭귄은 한없이 지속되는 남극 겨울을 버텨내며 몇 달간 급식없이 알을 부화시키는 반면, 암컷은 바다에서 사냥을 합니다."
    ),
    Document(
        text="펭귄은 다양한 해산물을 섭취합니다. 그들의 식단은 주로 생선, 오징어, 그리고 크릴로 이루어져 있으며 이를 수중 다이빙을 통해 잡습니다."
    ),
]

index = VectorStoreIndex.from_documents(documents)

# 간단한 리트리버 설정
retriever = VectorIndexRetriever(
    index=index,
    similarity_top_k=10,
)


model_path = Path(download_dir) / "selfrag_llama2_7b.q4_k_m.gguf"
query_engine = SelfRAGQueryEngine(str(model_path), retriever, verbose=True)

# 리트리벌 예시
response = query_engine.query("어떤 장르인가요?")

# 리트리벌 예시
response = query_engine.query("가장 작은 펭귄의 키는 얼마인가요?")
```

위의 테스트 코드는 다음 결과를 생성했습니다(대부분의 llama_cpp 디버깅 정보가 제거되었습니다):

```js
...
...
Model metadata: {'tokenizer.ggml.add_eos_token': 'false', 'tokenizer.ggml.eos_token_id': '2', 'general.architecture': 'llama', 'llama.rope.freq_base': '10000.000000', 'llama.context_length': '4096', 'general.name': 'LLaMA v2', 'tokenizer.ggml.add_bos_token': 'true', 'llama.embedding_length': '4096', 'llama.feed_forward_length': '11008', 'llama.attention.layer_norm_rms_epsilon': '0.000010', 'llama.rope.dimension_count': '128', 'tokenizer.ggml.bos_token_id': '1', 'llama.attention.head_count': '32', 'llama.block_count': '32', 'llama.attention.head_count_kv': '32', 'general.quantization_version': '2', 'tokenizer.ggml.model': 'llama', 'general.file_type': '15'}
Using fallback chat format: None

llama_print_timings:        load time =    4887.53 ms
llama_print_timings:      sample time =      11.29 ms /    22 runs   (    0.51 ms per token,  1947.76 tokens per second)
llama_print_timings: prompt eval time =    4887.46 ms /    24 tokens (  203.64 ms per token,     4.91 tokens per second)
llama_print_timings:        eval time =    5883.27 ms /    21 runs   (  280.16 ms per token,     3.57 tokens per second)
llama_print_timings:       total time =   10901.84 ms /    45 tokens
최종 답변: '오만과 편견'은 제인 오스틴의 로맨스 소설입니다.
...
...
llama_print_timings:        load time =    4887.53 ms
llama_print_timings:      sample time =      11.74 ms /    20 runs   (    0.59 ms per token,  1703.29 tokens per second)
llama_print_timings: prompt eval time =    7473.66 ms /    37 tokens (  201.99 ms per token,     4.95 tokens per second)
llama_print_timings:        eval time =    5414.34 ms /    19 runs   (  284.96 ms per token,     3.51 tokens per second)
llama_print_timings:       total time =   13076.88 ms /    56 tokens
입력: ### 지시사항:
가장 작은 펭귄은 얼마나 키가 큰가요?

### 응답:
[검색]<문단>펭귄들은 다양한 해산물을 섭취합니다. 그들의 식단은 주로 생선, 오징어, 크릴로 구성되어 있으며 이를 다이빙으로 잡습니다."</문단>
예측: [관련]가장 작은 펭귄 종류의 키는 종에 따라 달라질 수 있습니다.[지원되지 않음 / 모순][유틸리티:5]
점수: 1.4213598342974367
10/10 단락 완료

평가 종료
최상의 답변 선정: [관련]가

<div class="content-ad"></div>

테스트 코드를 이해하는 핵심은 SelfRAGQueryEngine 클래스의 구현에 있습니다. 이제 이 클래스를 자세히 살펴보겠습니다.

## 클래스 SelfRAGQueryEngine

먼저 생성자입니다. 주로 llama_cpp를 사용하여 Llama2-7B 모델을 로드하기 위해 사용됩니다.

```python
class SelfRAGQueryEngine(CustomQueryEngine):
    """간단한 Self RAG 쿼리 엔진."""

    llm: Any = Field(default=None, description="llm")
    retriever: BaseRetriever = Field(default=None, description="retriever")
    generate_kwargs: Dict = Field(default=None, description="llm generation arguments")
    verbose: bool = Field(default=True, description="Verbose.")

    def __init__(
        self,
        model_path: str,
        retriever: BaseRetriever,
        verbose: bool = False,
        model_kwargs: Dict = None,
        generate_kwargs: Dict = None,
        **kwargs: Any,
    ) -> None:
        """매개변수 초기화."""
        super().__init__(verbose=verbose, **kwargs)
        model_kwargs = model_kwargs or _MODEL_KWARGS
        self.generate_kwargs = generate_kwargs or _GENERATE_KWARGS
        try:
            from llama_cpp import Llama
        except ImportError:
            raise ImportError(_IMPORT_ERROR_MSG)
        self.llm = Llama(model_path=model_path, verbose=verbose, **model_kwargs)
        self.retriever = retriever
```

<div class="content-ad"></div>

그 다음으로 쿼리 기능에 대해 설명하겠습니다. 주요 프로세스는 아래 그림 3에 표시되어 있습니다:

![Image](/assets/img/2024-05-20-AdvancedRAG08Self-RAG_2.png)

이해를 돕기 위해 주요 부분에는 주석이 달려 있습니다.

```python
    def custom_query(self, query_str: str) -> Response:
        """커스텀 쿼리 실행."""
        # Llama2 모델을 사용하여 응답을 가져옵니다.
        response = self.llm(prompt=_format_prompt(query_str), **_GENERATE_KWARGS)
        answer = response["choices"][0]["text"]
        source_nodes = []

        # 검색이 필요한지 여부를 결정합니다.
        if "[Retrieval]" in answer:
            if self.verbose:
                print_text("검색이 필요합니다\n", color="blue")
            # 그림 1의 단계 1, 필요한대로 검색합니다.
            documents = self.retriever.retrieve(query_str)
            if self.verbose:
                print_text(f"받은 문서: {len(documents)}\n", color="blue")
            paragraphs = [
                _format_prompt(query_str, document.node.text) for document in documents
            ]

            if self.verbose:
                print_text("평가 시작\n", color="blue")

            # 그림 1의 단계 2 및 3, 병렬로 생성하고 평가합니다 
            # (코드에서 병렬화를 구현하지는 않음)
            critic_output = self._run_critic(paragraphs)

            paragraphs_final_score = critic_output.paragraphs_final_score
            llm_response_per_paragraph = critic_output.llm_response_per_paragraph
            source_nodes = critic_output.source_nodes

            if self.verbose:
                print_text("평가 종료\n", color="blue")

            # 가장 높은 점수를 받은 답변을 선택하고 반환합니다.
            best_paragraph_id = max(
                paragraphs_final_score, key=paragraphs_final_score.get
            )
            answer = llm_response_per_paragraph[best_paragraph_id]
            if self.verbose:
                print_text(f"최적 답변 선택: {answer}\n", color="blue")

        answer = _postprocess_answer(answer)
        if self.verbose:
            print_text(f"최종 답변: {answer}\n", color="green")
        return Response(response=str(answer), source_nodes=source_nodes)
```

<div class="content-ad"></div>

위의 코드에서 우리는 그림 1의 모든 세 단계가 표현된 것을 확인할 수 있습니다. 그러나 LlamaIndex의 코드는 병렬 처리를 구현하지 않았습니다. 더 자세한 정보는 관심 있는 독자들이 self._run_critic 함수를 살펴볼 수 있습니다. 해당 함수는 다양한 반사 토큰에 해당하는 점수를 처리합니다.

# Llama2-7B 모델 훈련 방법

이전에 여러 번 Llama2-7B 모델을 사용해왔으니, 이제 어떻게 얻을 지 알아봅시다.

## 훈련 목표

<div class="content-ad"></div>

훈련 과정에서는 평가 모델 C와 생성 모델 M 두 가지 모델이 필요합니다. 평가 모델 C는 모델 M이 필요로 하는 감독 데이터를 생성합니다.

그러나 추론 과정에서는 모델 M만 사용되며 모델 C는 필요하지 않습니다.

<div class="content-ad"></div>

## 비평가 모델 C

비평가 모델은 반사 토큰을 생성하는 데 훈련됩니다. 이 모델을 사용하는 목적은 작업 출력 오프라인에 반사 토큰을 삽입하여 훈련 말뭉치를 업데이트하는 것입니다.

각 세그먼트의 반사 토큰을 수동으로 주석 달기는 비용이 많이 듭니다. Self-RAG는 GPT-4를 활용하여 각 반사 토큰에 대해 고유한 지침을 할당하여 서로 다른 정의, 입력 및 출력을 가지고 있기 때문에 효율적으로 데이터 주석 작업을 완료합니다. 예를 들어, [검색] 토큰의 지시는 GPT-4가 외부 문서를 통합하는 것이 결과를 향상시킬지를 평가하도록 요청합니다.

훈련 데이터 D_critic를 얻으면 표준 조건부 언어 모델을 기반으로 훈련 목표를 구성할 수 있습니다.

<div class="content-ad"></div>


![image](/assets/img/2024-05-20-AdvancedRAG08Self-RAG_3.png) 

비평가 모델 C는 어떤 언어 모델로도 초기화할 수 있습니다. 예를 들어 생성자와 동일한 모델로 초기화할 수 있습니다. 예를 들면 Llama2-7B와 같은 모델을 사용할 수 있습니다.

## 생성자 모델 M

Figure 4는 훈련 데이터를 수집하는 구체적인 과정을 보여줍니다. 입력-출력 쌍 (x, y)가 주어지면 self-RAG는 검색 및 비평가 모델을 사용하여 원래의 출력 y를 확장하고 지도 데이터를 생성합니다. y의 각 세그먼트 yt에 대해:


<div class="content-ad"></div>

<img src="/assets/img/2024-05-20-AdvancedRAG08Self-RAG_4.png" />

Figure 4의 모든 조건 판단은 비평가 모델 C를 통해 실행됩니다. 획득한 훈련 데이터는 Figure 5에 나타나 있습니다:

<img src="/assets/img/2024-05-20-AdvancedRAG08Self-RAG_5.png" />

훈련 데이터 D_gen을 획득한 후, 다음 토큰 예측 표준 목적 함수를 다음과 같이 구성할 수 있습니다:

<div class="content-ad"></div>

![image](/assets/img/2024-05-20-AdvancedRAG08Self-RAG_6.png)

M 생성기는 결과뿐만 아니라 반영 토큰도 예측해야 합니다.

# self-RAG에 대한 나의 인사이트와 생각

일반적으로 self-RAG는 RAG 프로세스를 강화하는 새로운 관점을 제공합니다. 그러나 더 복잡한 훈련 과정이 필요하며 생성 단계 중에 여러 레이블 생성과 판단이 필요하기 때문에 추론 비용이 증가하기 때문에 실시간 성능이 필요한 프로젝트에는 중요한 영향을 줄 수 있습니다.

<div class="content-ad"></div>

또한, 이 프레임워크 내에서 최적화할 여지가 많이 있습니다. 더 많은 토론과 혁신을 일으키기 위해 몇 가지 포인트를 공유하겠습니다:

- 반영 토큰을 최적화하는 방법. Self-RAG는 네 가지 반영 토큰을 설계했습니다. [검색] 토큰 외에도 세 가지([IsREL], [IsSUP], [IsUSE])는 특정 유사성이 있습니다. 더 적은 반영 토큰을 사용하거나 다른 의미를 나타내는 반영 토큰을 고려하는 것이 타당한 방향일 수 있습니다.
- 비평가 모델이 LLM을 사용하는 이유는 무엇인가요? 제 생각에는 [IsUSE]와 같은 토큰이 공통 지식에 많이 의존하기 때문일 수 있습니다. 질의에 대한 답변의 유용성을 판단하는 것은 더 작은 모델이 수행할 수도 있습니다. 그러나 이러한 모델은 일반적인 지식을 부족하게 습득하며 종래의 특정 교육 자료만을 학습합니다. 따라서 비평가 모델로 LLM을 사용하는 것이 합리적일 수 있습니다.
- 비평가 모델 크기 선택. Self-RAG는 7B 및 13B 모델로 테스트되어 우수한 결과를 얻었습니다. 그러나 만약 더 작은 LLM인 3B로 전환하면 어떤 차이를 관찰할 수 있을까요? 마찬가지로, 더 큰 LLM인 33B로 전환했을 때 얼마나 개선을 기대할 수 있을까요?
- 인간 피드백을 통한 강화학습(RLHF)을 사용하지 않는 이유는 무엇인가요? 논문에서는 작업 예제를 통해 대상 언어 모델을 학습하는 것을 제안합니다. 이 예제는 비평가 모델에서 오프라인으로 반영 토큰이 추가된 것입니다. 이로 인해 RLHF 대비 훨씬 낮은 교육 비용이 발생합니다. 또한, self-RAG의 반영 토큰은 추론 중 생성을 제어할 수 있게 만들어주며 RLHF는 훈련 중 인간의 선호도 조정에 초점을 두고 있습니다. 그러나 논문에는 RLHF와 관련된 비교 실험 내용이 포함되어 있지 않습니다.

# 결론

본문은 직관적인 예시로 시작하여 Self-RAG의 기본적인 과정을 소개하고 코드 설명을 보완하는 내용을 담고 있습니다. 또한 제 생각과 통찰을 공유하였습니다.

<div class="content-ad"></div>

RAG 기술에 관심이 있다면, 내 다른 기사들도 살펴보세요.

또한, 최신 AI 관련 콘텐츠는 내 뉴스레터에서 찾을 수 있어요.

마지막으로, 어떠한 오류나 누락이 있거나 궁금한 사항이 있으시면 댓글 섹션에서 자유롭게 토론해 주세요.