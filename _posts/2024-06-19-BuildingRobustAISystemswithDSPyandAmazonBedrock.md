---
title: "DSPy와 Amazon Bedrock을 사용하여 견고한 AI 시스템 구축하기"
description: ""
coverImage: "/assets/img/2024-06-19-BuildingRobustAISystemswithDSPyandAmazonBedrock_0.png"
date: 2024-06-19 12:16
ogImage:
  url: /assets/img/2024-06-19-BuildingRobustAISystemswithDSPyandAmazonBedrock_0.png
tag: Tech
originalTitle: "Building Robust AI Systems with DSPy and Amazon Bedrock"
link: "https://medium.com/@dgallitelli95/building-robust-ai-systems-with-dspy-and-amazon-bedrock-d0376f158d88"
---

## 프롬프트 매직에서 프롬프트 엔지니어링으로 변경

![이미지](/assets/img/2024-06-19-BuildingRobustAISystemswithDSPyandAmazonBedrock_0.png)

인공 지능이 다양한 산업을 혁신하면서, AI 모델을 개발하고 배포하기 위한 견고하고 확장 가능한 도구에 대한 필요성은 이제껏 없었습니다. 이 분야의 주목할만한 발전로는 Stanford의 최신 데이터 과학 도구인 DSpy와 AWS의 기계 학습을 위한 혁신적인 기반인 Amazon Bedrock이 있습니다. 이 블로그 글은 DSpy와 Amazon Bedrock 사이의 특징, 기능 및 독특한 시너지에 대해 파헤치며, 개발자와 데이터 과학자가 AI의 경계를 넓히는 데 어떻게 도움을 주는지 강조합니다.

# DSPy란 무엇인가요?

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
%pip 설치 dspy-ai
```

DSPy는 스탠포드 NLP에서 개발한 오픈 소스 라이브러리로, 데이터 과학 워크플로우를 만들고 관리하는 프로세스를 간소화하기 위해 설계되었습니다. 이는 세 가지 핵심 구성 요소인 Signatures, Modules 및 Optimizers을 중심으로 구축되어 있습니다.

## Signatures

DSPy의 서명은 언어 모델(LM) 작업의 입력/출력 동작을 모듈식이고 적응적인 방식으로 정의합니다. Signatures는 길고 취약한 프롬프트에 의존하는 대신, 깨끗하고 재현 가능한 코드를 허용합니다. 서명의 예로는 질문 답변을 위한 `»question -` answer»`나 요약을 위한 `»document -` summary»`가 있습니다. 작업 요구 사항에 따라 서명은 간단하거나 복잡할 수 있습니다.

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

## 모듈

DSPy의 모듈은 LM 프로그램의 구성 요소입니다. 각 모듈은 chain-of-thought나 retrieval-augmented generation과 같은 특정 프롬프팅 기술을 추상화합니다. 모듈은 다양한 시그니처를 처리할 수 있으며, PyTorch와 같은 프레임워크의 신경망 레이어처럼 더 큰 프로그램으로 구성될 수 있습니다. 이를 통해 유연하고 확장 가능한 프로그램 구성이 가능해집니다.

## 옵티마이저

DSPy의 옵티마이저는 DSPy 프로그램의 매개변수를 세밀하게 조정하여 프로그램의 출력을 최적화합니다. 그들은 기울기 하강법과 이산 최적화 기술의 조합을 사용하여 메트릭을 최대화하거나 일반적으로 프로그램의 출력을 평가하는 함수에 점수를 부여합니다. 다양한 종류의 옵티마이저가 제공되며, 각각 다른 데이터 시나리오와 최적화 요구에 맞게 맞춤화됩니다. 옵티마이저가 가장 잘 작동하도록하려면 일부 학습 입력을 제공해야 합니다.

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

# Amazon Bedrock을 사용하는 방법

## 구성

첫 번째 단계는 DSPy를 구성하여 기본적으로 Amazon Bedrock을 사용하도록 설정하는 것입니다:

```js
import dspy

bedrock_haiku = dspy.AWSAnthropic(
    aws_provider = dspy.Bedrock(region_name="us-west-2"),
    model="anthropic.claude-3-haiku-20240307-v1:0",
)
dspy.configure(lm=bedrock_haiku)
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

LLM 구성이 마무리되었으니 문제 해결을 시작할 수 있어요.

## 서명 및 모듈

공식 DSPy 설명서에서 제안하는 대로 "DSPy를 사용하는 8 단계"를 따라서 작업을 정의해 보겠습니다. 처음에는 간단하게 질문과 답변 프로그램을 만들어 보죠. 따라서 우리의 입력은 질문이 되고, 출력은 답변이 될 거예요. 이를 위해 우리의 서명과 모듈을 정의할 수 있습니다:

\js
qa = dspy.Predict("question -> answer")
\

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

아래 예시에서 예측은 우리의 모듈이며, 예측을 생성하는 것이 목표이며, 서명은 질문 - 답변입니다. 이는 우리가 DSPy에게 질문에서 답변을 찾고 있다는 것을 간결하게 설명하는 줄임표기법입니다. qa를 출력하면 다음 출력이 나타납니다:

```js
Predict(StringSignature(question -> answer
    instructions='주어진 필드 `question`으로 `answer` 필드를 생성하십시오.'
    question = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'input', 'prefix': 'Question:', 'desc': '${question}'})
    answer = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'output', 'prefix': 'Answer:', 'desc': '${answer}'})
))
```

DSPy는 질문과 답변이 문자열임을 추론하고, 지시사항에서 강조된 프롬프트를 언어 모델의 입력으로 사용합니다. 타입을 직접 제어할 수도 있습니다:

```js
dspy.TypedPredictor("question:str -> answer:int")

# 출력
TypedPredictor(StringSignature(question -> answer
    instructions='주어진 필드 `question`으로 `answer` 필드를 생성하십시오.'
    question = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'input', 'prefix': 'Question:', 'desc': '${question}'})
    answer = Field(annotation=int required=True json_schema_extra={'__dspy_field_type': 'output', 'prefix': 'Answer:', 'desc': '${answer}'})
))
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

Module Predict에서 정의된 프롬프트를 통해, DSPy는 프롬프트 엔지니어링 프로세스를 반복하고 제어할 수 있는 개념을 소개합니다. 이 클래스를 사용하여 다음 질문에 대한 답변을 생성해보겠습니다:

```js
qa(question="Sergio Mattarella는 누구인가?").answer

# 출력
주어진 질문에 대한 답변은 다음과 같습니다:
질문: Sergio Mattarella는 누구인가?
답변: Sergio Mattarella는 현재 이탈리아의 대통령입니다. 그는 2015년부터 대통령으로 재직하고 있습니다.
```

## 서명 및 모듈을 위한 고급 구성

이제 프로그램의 동작을 수정하기 위해 서명과/또는 모듈을 사용자 정의할 수 있습니다.

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

dspy.Modules에 대해 이야기해보겠습니다. 이 라이브러리에서 제공하는 다른 모듈을 사용하거나 사용자 정의 모듈을 만들 수 있습니다:

- dspy.Predict: 기본 예측자입니다. 서명을 수정하지 않습니다. 학습의 주요 형태(즉, 지시 및 데모의 저장 및 LM 업데이트)를 처리합니다.
- dspy.ChainOfThought: 서명의 응답을 확정하기 전 단계별로 생각하도록 LM에 가르칩니다.
- dspy.ProgramOfThought: 실행 결과에 따라 응답을 결정할 코드를 출력하도록 LM에 가르칩니다.
- dspy.ReAct: 주어진 서명을 구현하기 위해 도구를 사용할 수 있는 에이전트입니다.
- dspy.MultiChainComparison: ChainOfThought에서 여러 출력을 비교하여 최종 예측을 생성할 수 있습니다.

이전 출력인 dspy.Predict와 dspy.ChainOfThought를 비교해보겠습니다. 그러나 질문을 바꿔볼까요:

```js
question = "True or False: The numbers in this group add up to an even number: 17, 9, 10, 12, 13, 4, 2."

predictor = dspy.Predict("question -> answer")
predictor(question=question)

# 결과
Prediction(
    answer='Question: True of False: The numbers in this group add up to an even number: 17, 9, 10, 12, 13, 4, 2.\nAnswer: True. The numbers 17, 9, 10, 12, 13, 4, and 2 add up to 67, which is an even number.'
)

------

cot = dspy.ChainOfThought("question -> answer")
cot(question=question)

# 결과
Prediction(
    rationale="Question: True or False: The numbers in this group add up to an even number: 17, 9, 10, 12, 13, 4, 2.\nReasoning: Let's think step by step in order to determine if the numbers in this group add up to an even number.\n1. We need to add up all the numbers in the group: 17 + 9 + 10 + 12 + 13 + 4 + 2 = 67.\n2. 67 is an odd number, not an even number.",
    answer='False, the numbers in this group do not add up to an even number.'
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

두 출력 결과를 보면, 답변이 다르며, 후자가 올바른 것을 알 수 있습니다. 이는 DSPy가 Chain of Thought (CoT)를 통해 우리가 제공한 프롬프트를 확장하기 때문입니다. CoT를 사용하면 LM(Language Model)에게 답변을 제공하기 전에 "단계별로" 추론하도록 강요합니다. 이 근거는 답변에서 제공되며, 더 자세한 지침은 cot.extended_signature에서 확인할 수 있습니다.

```js
cot.extended_signature

# 출력
StringSignature(question -> rationale, answer
    instructions='`question` 필드를 주어 `answer` 필드를 생성하십시오.'
    question = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'input', 'prefix': '질문:', 'desc': '${question}'})
    rationale = Field(annotation=str required=True json_schema_extra={'prefix': "추론: 정답을 만들기 위해 '단계별로 생각해 봅시다. ${produce the answer}. We ...", '__dspy_field_type': 'output'})
    answer = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'output', 'prefix': '답변:', 'desc': '${answer}'})
)
```

dspy.Signature의 경우, 예를 들어 RAG에 매우 유용한 컨텍스트를 소개하려면 축약 표기를 확장할 수 있습니다:

```js
dspy.Predict("context, question -> answer")

# 출력
Predict(StringSignature(context, question -> answer
    instructions='`context`, `question` 필드를 주어 `answer` 필드를 생성하십시오.'
    context = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'input', 'prefix': '컨텍스트:', 'desc': '${context}'})
    question = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'input', 'prefix': '질문:', 'desc': '${question}'})
    answer = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'output', 'prefix': '답변:', 'desc': '${answer}'})
))
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

아니면 더 많은 제어를 위해 더 긴 표기를 사용해보세요:

```js
class BasicQA(dspy.Signature):
    """문맥에 기반한 짧은 답변으로 질문에 대답합니다"""
    context = dspy.InputField()
    question = dspy.InputField()
    answer = dspy.OutputField(desc="문맥에서 추출된 짧은 답변")

# 출력
BasicQA(context, question -> answer
    instructions='문맥에 기반한 짧은 답변으로 질문에 대답합니다'
    context = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'input', 'prefix': '문맥:', 'desc': '${context}'})
    question = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'input', 'prefix': '질문:', 'desc': '${question}'})
    answer = Field(annotation=str required=True json_schema_extra={'desc': '문맥에서 추출된 짧은 답변', '__dspy_field_type': 'output', 'prefix': '답변:'})
)
```

서명은 TypedPredictor 모듈 덕분에 pydantic 표기를 지원합니다:

```js
import dspy
from pydantic import BaseModel, Field
from dspy.functional import TypedPredictor
from datetime import datetime
from textwrap import dedent

class TravelInformation(BaseModel):
    origin: str = Field(pattern=r"^[A-Z]{3}$")
    destination: str = Field(pattern=r"^[A-Z]{3}$")
    date: str
    confidence: float = Field(gt=0, lt=1)

class TravelSignature(dspy.Signature):
    """ 주어진 이메일에서 모든 여행 정보를 추출합니다 """
    email: str = dspy.InputField()
    flight_information: list[TravelInformation] = dspy.OutputField()

predictor = TypedPredictor(TravelSignature)
predictor(email=dedent("""
    Amazon Web Services Airlines로 예약해 주셔서 감사합니다.
    2024년 6월 18일 바리에서 라스베이거스로 가는 XYZ123 편에 예약이 완료되었으며 탑승을 환영합니다.
    즐거운 여행 되세요.
"""))
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

## 리트리버

프로그램은 dspy.Retrieve 클래스를 확장하여 검색 시스템을 구현할 수도 있습니다. 사용 가능한 리트리버의 최신 목록을 확인하려면 DSPy GitHub 저장소의 dspy.retrievers 모듈을 참조해주세요.

Amazon Bedrock와 함관해서 리트리버를 사용하려면 사용자 정의 SentenceVectorizer 클래스를 만들어야 합니다. 미리 해당 작업을 수행해 두었습니다. (그런데, 이를 DSPy 팀이 공식적으로 구현하길 원하시면 PR #1151에 +1을 부탁드립니다):

```python
import boto3
import json
import numpy as np
from typing import List, Optional
from dsp.modules.sentence_vectorizer import BaseSentenceVectorizer

class AmazonBedrockVectorizer(BaseSentenceVectorizer):
    '''
    이 벡터화기는 텍스트를 임베딩으로 변환하기 위해 Amazon Bedrock API를 사용합니다.
    '''
    SUPPORTED_MODELS = [
        "amazon.titan-embed-text-v1", "amazon.titan-embed-text-v2:0",
        "cohere.embed-english-v3", "cohere.embed-multilingual-v3"
    ]

    def __init__(
        self,
        model_id: str = 'amazon.titan-embed-text-v2:0',
        embed_batch_size: int = 128,
        region_name: str = 'us-west-2',
        aws_access_key_id: Optional[str] = None,
        aws_secret_access_key: Optional[str] = None,
    ):
        self.model_id = model_id
        self.embed_batch_size = embed_batch_size

        # Bedrock 클라이언트 초기화
        self.bedrock_client = boto3.client(
            service_name='bedrock-runtime',
            region_name=region_name,
            aws_access_key_id=aws_access_key_id,
            aws_secret_access_key=aws_secret_access_key
        )

    def __call__(self, inp_examples: List["Example"]) -> np.ndarray:
        text_to_vectorize = self._extract_text_from_examples(inp_examples)
        embeddings_list = []

        n_batches = (len(text_to_vectorize) - 1) // self.embed_batch_size + 1
        for cur_batch_idx in range(n_batches):
            start_idx = cur_batch_idx * self.embed_batch_size
            end_idx = (cur_batch_idx + 1) * self.embed_batch_size
            cur_batch = text_to_vectorize[start_idx: end_idx]

            # Bedrock API Body 구성
            if self.model_id not in self.SUPPORTED_MODELS:
                raise Exception(f"지원하지 않는 모델: {self.model_id}")

            if self.model_id == "amazon.titan-embed-text-v1":
                if self.embed_batch_size == 1:
                    body = json.dumps({"inputText": cur_batch[0]})
                else:
                    raise Exception(f"모델 {self.model_id}은 배치 크기 1을 전용으로 지원합니다.")
            elif self.model_id == "amazon.titan-embed-text-v2:0":
                if self.embed_batch_size == 1:
                    body = json.dumps({
                        "inputText": cur_batch[0],
                        "dimensions": 512
                    })
                else:
                    raise Exception(f"모델 {self.model_id}은 배치 크기 1을 전용으로 지원합니다.")
            elif self.model_id.startswith("cohere.embed"):
                body = json.dumps({
                    "texts": cur_batch,
                    "input_type": "search_document"
                })
            else:
                raise Exception("여기서 어떻게 나타났나요?")


            # Bedrock API 호출
            response = self.bedrock_client.invoke_model(
                body=body,
                modelId=self.model_id,
                accept='application/json',
                contentType='application/json'
            )

            response_body = json.loads(response['body'].read())
            if self.model_id.startswith("cohere.embed"):
                cur_batch_embeddings = response_body['embeddings']
            elif self.model_id.startswith("amazon.titan-embed-text"):
                cur_batch_embeddings = response_body['embedding']
            else:
                raise Exception(f"아직 구현되지 않았습니다! Amazon Bedrock 문서에서 모델 {self.model_id}의 응답 형식을 확인하세요: https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters.html")
            embeddings_list.extend(cur_batch_embeddings)

        embeddings = np.array(embeddings_list, dtype=np.float32)
        return embeddings

    def _extract_text_from_examples(self, inp_examples: List) -> List[str]:
        if isinstance(inp_examples[0], str):
            return inp_examples
        return [" ".join([example[key] for key in example._input_keys]) for example in inp_examples]
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

아래와 같이 코드를 사용하여 선호하는 DSPy 검색기에서 이 클래스를 사용할 수 있습니다:

```js
from dspy.retrieve.faiss_rm import FaissRM

document_chunks = [
    "..."
]

frm = FaissRM(
    document_chunks=document_chunks,
    vectorizer=AmazonBedrockVectorizer(
        embed_batch_size=128, model_id="cohere.embed-english-v3"
        # OR:
        # embed_batch_size=1, model_id="amazon.titan-embed-text-v2:0"
    )
)
print(frm(["여기에 질문을 입력하세요"]))
```

## 사용자 정의 프로그램

이 지식을 활용하여 프로그램의 동작을 정의하는 사용자 정의 클래스를 정의할 수 있습니다! 예를 들어, RAG 클래스는 다음과 같이 보일 것입니다:

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
class RAG(dspy.Module):
    def __init__(self, num_passages=3):
        # 'Retrieve' will use the user's default retrieval settings unless overriden.
        self.retrieve = dspy.Retrieve(k=num_passages)
        # 'ChainOfThought' with signature that generates answers given retrieval & question.
        self.generate_answer = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        context = self.retrieve(question).passages
        return self.generate_answer(context=context, question=question)
```

이 코드를 실행하기 전에 선호하는 검색기를 구성해야 합니다.

# 결론

DSPy와 Amazon Bedrock은 인공지능(AI) 개발 도구의 진화에서 중요한 발전을 나타냅니다. DSPy의 데이터 과학 능력과 Bedrock의 확장 가능하고 효율적인 모델 관리를 결합하여 개발자와 데이터 과학자는 복잡한 AI 과제에 대처할 강력한 도구 상자를 갖추게 됩니다. 이러한 도구들이 계속 발전함에 따라, 그들은 의심할 여지 없이 AI의 미래를 형성하는 데 중추적인 역할을 할 것입니다.

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

자세한 정보는 DSPy GitHub 저장소와 Amazon Bedrock 문서를 살펴보세요. 이 흥미로운 분야에서의 미래 업데이트와 진전에 주목해 주세요!
