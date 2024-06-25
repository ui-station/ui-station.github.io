---
title: "오픈 소스 LLM을 활용한 자연어를 SQL 쿼리로 변환하기"
description: ""
coverImage: "/assets/img/2024-05-18-NaturalLanguagetoSQLQueryusinganOpenSourceLLM_0.png"
date: 2024-05-18 18:19
ogImage:
  url: /assets/img/2024-05-18-NaturalLanguagetoSQLQueryusinganOpenSourceLLM_0.png
tag: Tech
originalTitle: "Natural Language to SQL Query using an Open Source LLM"
link: "https://medium.com/@khadkechetan/natural-language-to-sql-query-using-an-open-source-llm-6b4b91a5519a"
---

# 소개

데이터 활용의 동적인 풍경에서는 데이터베이스와 손쉽게 상호 작용할 수 있는 능력이 중요합니다. 전통적으로 이 상호 작용은 구조화된 쿼리 언어(SQL)에 대한 심층적인 이해가 필요하여 많은 사용자들에게 진입 장벽이 되었습니다. 그러나 자연어 처리(NLP)를 SQL 쿼리 엔진에 적용하여 이 풍경이 변화되었으며, 이를 통해 사용자들이 자연어 명령을 사용하여 데이터베이스와 소통할 수 있게 되었습니다. 이 첨단 기술은 인간의 언어를 SQL 쿼리로 순조롭게 번역하여 데이터를 검색하고 조작하는 방식을 혁신하고 있습니다.

자연어 처리(NLP)에서 Mistral 7B 및 Microsoft Phi-3과 같은 모델은 주요 역할을 하며 성능과 효율성의 경계를 재정의하고 있습니다.

Mistral 7B는 NLP 작업에서 뛰어난 성능과 정밀도로 높이 평가 받고 있습니다. 그룹화된 쿼리 어텐션(GQA) 및 슬라이딩 윈도우 어텐션(SWA)과 같은 혁신적인 기능들을 갖춘 Mistral 7B는 수학 및 코드 생성을 포함한 다양한 벤치마크에서 우수한 성과를 거두고 있습니다. Code-Llama 7B의 코딩 능력에 가까워짐과 동시에 NLP 발전에서의 중요성을 강조하며 다양한 분야에서 우수성을 유지하고 있습니다.

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

Phi-3 는 작은 언어 모델(SLMs) 분야에서의 Microsoft의 최신 혁신으로, AI의 풍경을 변화시키는 대단한 제품입니다. Phi-3-mini, Phi-3-small 및 Phi-3-medium으로 구성된 이 모델군은 간결한 구성으로 뛰어난 성능을 제공합니다. 38억 개의 파라미터를 자랑하는 Phi-3-mini는 더 큰 모델들과 견줄 만한 성능을 발휘하면서도 스마트폰에서 효율적으로 동작합니다. Phi-3의 성공 뒤에는 견고함, 안전성 및 대화 능력을 중시하는 정교하게 선별된 학습 데이터셋이 있습니다. Phi-3-small 및 Phi-3-medium은 Phi-3의 능력을 더욱 확장하여 다양한 응용 분야에 대응합니다. 정교하게 설계된 아키텍처와 학습 방법을 통해 Phi-3은 AI 기술의 큰 발전을 상징하며, 다양한 생성형 AI 작업에 대한 우수한 성능과 효율성을 약속합니다.

NLP와 SQL의 교차점을 탐색하여 Mistral 7B와 Microsoft Phi-3의 활용에 대해 알아봅니다. 이러한 모델들은 자연어 쿼리를 구조화된 SQL 쿼리로 원활하게 변환하여 데이터베이스 쿼리 작업에서 향상된 효율성과 정확도를 제공합니다.

![](/assets/img/2024-05-18-NaturalLanguagetoSQLQueryusinganOpenSourceLLM_0.png)

# 학습 목표

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

이 블로그 포스트에서는 오픈 소스 Mistral 7B 모델을 NL2SQL 작업에 활용하는 복잡성을 탐색할 것입니다. 또한 NL2SQL 애플리케이션을 위해 모델을 맞춤화하고 훈련하는 방법에 대해 논의할 것입니다. 기사의 나머지 부분은 다음과 같은 내용을 다룹니다.

# 동기부여

오픈 소스 LLMs를 활용하면 자연어 명령을 SQL 쿼리로 변환하는 복잡한 프로세스를 실행할 수 있습니다. 이 혁신적인 기술은 사용자가 수동 쿼리 작성 없이 데이터 요구 사항을 자연스럽게 표현하도록 자동화하며, 이로써 사용자의 입력을 분석하고 의미론적으로 정확한 SQL 쿼리를 생성하는 복잡한 알고리즘과 대규모 언어 모델이 활용됩니다. 이는 변환 프로세스를 간소화시키고 광범위한 사용자들에게 광범위한 SQL 지식이 없어도 데이터를 이용할 수 있게 합니다. 오픈 소스 LLMs는 편리함을 제공하며 데이터 접근성과 운영 효율성을 크게 향상시킵니다. SQL 전문 지식의 장벽을 제거함으로써 이 기술은 데이터 접근성을 민주화시키고 각 분야의 사용자들이 데이터를 검색하고 통찰을 얻는 데 도움을 줍니다. 실시간 통찰을 찾는 비즈니스 분석가나 데이터 집합을 탐색하는 일반 사용자를 위한 것이든, 자연어 명령의 직관적인 성격은 데이터 검색을 간단하게 합니다.

또한 이러한 모델에서 내재된 자동화는 쿼리 실행을 가속화하여 전반적인 효율성과 생산성을 높입니다. 오픈 소스 LLMs의 영향력은 광범위하며 다양한 산업 전반에 혁신과 변화를 격려합니다. 이 기술은 재무, 건강 관리 및 전자 상거래 분야와 같이 데이터 주도적 의사 결정이 중요한 분야에서 이해하기 쉬운 인사이트를 추출할 수 있도록 이해권자를 돕습니다. 더 나아가, 고급 분석 플랫폼과 인공 지능 시스템과의 통합을 통해 조직을 데이터 주도적 우수성으로 이끕니다. 탐구 문화를 육성하고 데이터 상호작용을 간소화함으로써 오픈 소스 LLMs는 데이터 자산의 모든 잠재력을 발휘함으로써 산업 전반에 혁신과 성장을 촉진합니다.

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

# 1. NL2SQL을 위한 사전 훈련 모델 (Mistral 7B)

Mistral AI가 개발한 70억 개의 파라미터를 가진 언어 모델인 Mistral 7B는 인공 지능 분야에서 강력한 모델로 빠르게 인기를 얻고 있습니다.

- 기본 모델로 위치 지정된 Mistral 7B는 자연어 처리에서 중요한 역할을 하는 가장 중요한 구조적 모델로 자리 잡았으며 대규모 언어 모델 환경 내에서 필수적인 코어 빌딩 블록의 중요성을 보여줍니다.
- 건축적 접근 방식으로 차별화된 Mistral 7B는 빠른 추론을 위해 그룹화된 쿼리 어텐션 (GQA)과 긴 시퀀스를 효율적으로 처리하기 위한 슬라이딩 윈도우 어텐션 (SWA)과 같은 혁신적인 기능을 활용하여 우수한 성능을 발휘합니다.
- 주로 영어에 초점을 맞추지만 코딩 능력도 갖춘 Mistral 7B는 특히 다른 모델들보다 더 넓은 컨텍스트에서 텍스트를 이해하고 생성할 수 있는 높은 문맥 윈도우를 가지고 있어 두각을 나타냅니다.
- 73억 개의 파라미터로 인상적인 Mistral 7B는 최신 언어 모델을 대표하는데, Apache 2.0 라이센스 하에 제한 없이 사용할 수 있습니다.
- Mistral 7B는 모든 평가된 벤치마크에서 최고의 오픈 13B 모델 (Llama-2)보다 우수한 성과를 거두며 최고의 34B 모델 (Llama-1)보다 추론평가, 수학 및 코드 생성에서 뛰어난 성능을 보여줍니다.
- Mistral-7B는 Llama2-13B보다 우수한 성능을 보이며 CodeLlama-7B와 경쟁력 있는 성과를 보이며 특히 추론, 수학 및 코드 생성 벤치마크에서 뛰어납니다.
- 더 큰 모델들에 비해 크기는 작지만, Mistral 7B는 텍스트 요약, 분류, 텍스트 완성 및 코드 완성을 포함한 다양한 자연어 작업에서 우수한 성과를 거둡니다.
- 이 모델이 자연어 쿼리를 구조화된 SQL 명령어로 변환하는 효과를 탐색하여 능력을 자세히 살펴봅시다.

## Sliding Window Attention

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

- Mistral 7B은 전통적인 주의 메커니즘에서 발생하는 도전에 효과적으로 대처할 수 있는 슬라이딩 윈도우 어텐션(Sliding Window Attention, SWA) 메커니즘을 포함하고 있습니다. 전자는 토큰 수가 증가함에 따라 추론 중 지연 시간이 증가하고 처리량이 감소할 수 있으며, 시퀀스 길이와 메모리와 관련된 연산이 이차적으로 증가하고 메모리가 선형적으로 증가할 수 있습니다. 반면에 SWA는 각 토큰의 주의를 이전 레이어의 W개 토큰을 최대한으로 제한하여 주어진 윈도우 크기 W를 넘어서 주의를 확장합니다.
- SWA는 트랜스포머의 계층 구조를 활용하여 위치 i의 숨겨진 상태가 입력 레이어의 토큰을 W x k 토큰까지 액세스할 수 있도록 지원합니다. 최종 레이어에서 W = 4096의 윈도우 크기로, SWA는 이론적으로 대략 131K 토큰의 주의 범위를 달성할 수 있습니다. 실제적으로 W = 4096 및 FlashAttention과 xFormers의 최적화 기법을 사용하여, 16K 토큰 시퀀스의 경우 바닐라 주의 기준에 비해 주목할만한 2배의 속도 향상이 가능합니다. 따라서, SWA는 주의 메커니즘의 성능을 혁신적으로 향상시킬 수 있는 강력하고 효율적인 접근 방식입니다.

### b. 롤링 버퍼 캐시

- 롤링 버퍼 캐시를 구현함으로써, Mistral 7B는 고정된 주의 범위를 전략적으로 사용하여 캐시 크기를 효과적으로 제어합니다. 이 캐시는 W로 표시된 고정된 크기로, 캐시 내에서 특정 시간 단계 i에서 시간 단계 i mod W에 키와 값들을 효율적으로 저장합니다. 시퀀스가 진행되고 i가 W를 초과할 때, 캐시는 롤링 버퍼 메커니즘을 사용하여 이전 값들을 덮어쓰고 무한정으로 확장되는 것을 방지합니다. W = 3으로 설명된 이 접근 방식은 32k 토큰 시퀀스에 대해 8배의 캐시 메모리 사용량 감소를 실현함으로써, 모델의 품질을 희생하지 않고 달성합니다. 고정된 주의 범위는 효율적인 메모리 이용을 보장할 뿐만 아니라 Mistral 7B가 길이가 다른 시퀀스를 처리하는 데에 원활하게 기능하는 데에 기여합니다.

### c. 사전 채움 및 청크 분할

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

- 시퀀스 생성 과정에서는 문맥 정보에 기반하여 순차적으로 토큰을 예측하는데, (k, v) 캐시를 사용하여 효율적으로 최적화됩니다. 알려진 프롬프트로 미리 채워진 캐시를 활용하여 효율성을 높입니다. 긴 프롬프트를 관리하기 위해 지정된 윈도우 크기를 사용하여 작은 청크로 나누고, 각 청크를 사용하여 캐시를 미리 채웁니다. 이 전략적 접근 방식은 시퀀스 생성 프로세스 중 캐시 내부 및 현재 청크 전체에서 주의력을 계산하는 것을 포함합니다. 이 방법을 활용함으로써 Mistral 7B는 시퀀스 생성의 효율성을 향상시키며, 캐시에 저장된 미리 알려진 프롬프트를 효율적으로 활용하여 각 예측된 토큰을 이전 토큰과 조화롭게 정렬합니다.
- 언어 모델의 동적인 환경에서 Mistral 7B의 등장은 성능과 효율성 면에서 큰 도약을 의미합니다. 포괄적인 평가 파이프라인을 통해 Mistral 7B는 자신의 능력을 입증하며, 이전 제품인 Llama 2 7B 및 Llama 2 13B뿐만 아니라 Llama 1 34B와 같은 핵심 벤치마크에서 뛰어난 성능을 보여줌으로써 뛰어난 경쟁력을 나타냅니다.
- Mistral 7B의 우월성은 모든 측정 항목에 걸쳐 명백히 드러나며, 해당 분야의 선도주자로서의 지위를 재확인합니다. 다양한 벤치마크에 대한 면밀한 재평가 과정은 Mistral 7B의 탁월한 능력을 일관되게 입증하며, 경쟁사를 뒤로 남깁니다.

## 크기 및 효율성 분석

- Mistral 7B의 매력 중요 요소 중 하나는 혁신적인 "동등한 모델 크기" 계산 방식을 통한 효율성입니다. 추론, 이해 및 STEM 추론 등에서 평가한 결과, Mistral 7B는 세 배 이상 크기의 Llama 2 모델과 동등한 성능을 보여줍니다. 이 효율성은 과도한 매개변수 부담 없이 뛰어난 결과를 제공할 수 있는 Mistral 7B의 능력을 입증합니다.
- Mistral 7B의 효율성을 더 자세히 살펴보면, 평가 결과에서 지식 압축에 대한 흥미로운 통찰력을 확인할 수 있습니다. 지식 벤치마크에서 1.9배 낮은 압축률을 달성하지만, 이는 Mistral 7B의 의도적으로 제한된 매개변수 수에 기인합니다. 이 제한은 저장된 지식 양을 제한하지만, Mistral 7B는 집중하고 효과적으로 매개변수를 활용하여 보상합니다.

# 평가의 차이점

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

불일치 사항을 투명하게 다루면서, 평가 규정의 변화를 유의하는 것이 중요합니다. 어떤 벤치마크에서는 Llama 2의 MBPP와 Mistral 7B의 평가 결과 사이에 차이가 발생합니다. TriviaQA에서 손으로 검증된 데이터를 사용하는 것이 Mistral 7B의 성능 지표의 신뢰성에 기여하는 강건한 평가 과정을 확인하게 됩니다.

# 데이터셋

아래 열로 구성된 구조 데이터베이스를 사용할 계획입니다. 다음 테이블에서 다양한 검색을 수행할 것입니다.

```js
transaction = [
  "transaction_id",
  "transaction_amount",
  "transaction_date",
  "transaction_type",
  "transaction_status",
  "transaction_description",
  "transaction_source_account",
  "transaction_destination_account",
  "transaction_currency",
  "transaction_fee",
];
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

# 코드 구현

- 패키지 설치하기

```js
!pip install git+https://github.com/huggingface/transformers.git
!pip install deepspeed --upgrade
!pip install accelerate
!pip install sentencepiece
!pip install langchain
!pip install torch
!pip install bitsandbytes
```

2. 패키지 불러오기

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
import os
import re
import torch
from difflib import SequenceMatcher
from langchain.chains import LLMChain
from langchain import PromptTemplate, LLMChain
from langchain.llms import HuggingFacePipeline
from langchain_core.prompts import PromptTemplate
from transformers import LlamaTokenizer, LlamaForCausalLM, pipeline
```

3. 모델 불러오기

```js
base_model = LlamaForCausalLM.from_pretrained(
  "mistralai/Mistral-7B-Instruct-v0.1",
  (load_in_8bit = True),
  (device_map = "auto")
);
tokenizer = LlamaTokenizer.from_pretrained(
  "mistralai/Mistral-7B-Instruct-v0.1"
);
pipe = pipeline(
  "text-generation",
  (model = base_model),
  (tokenizer = tokenizer),
  (max_length = 500),
  (temperature = 0.3),
  (top_p = 0.95),
  (repetition_penalty = 1.2)
);
local_llm = HuggingFacePipeline((pipeline = pipe));
```

4. SequenceMatcher

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

이 Python 함수는 difflib 모듈의 SequenceMatcher 클래스를 활용하여 쿼리와 지정된 사전의 열 이름 간의 유사도 점수를 계산하여 쿼리 이해력과 대체를 향상시킵니다.

```js
def find_columns_match(question, input_dict):
try:
  question_list = re.split(r'\s|,|\.', question)
  for index, string2 in enumerate(question_list):
    for string1 in input_dict.get('table1_columns'):
      score = SequenceMatcher(None,string1.lower(), string2.lower()).ratio()*100
      if score > 91:
        question_list[index] = string1 + ","
  return " ".join(question_list)

except:
 return question
```

이 Python 함수 query_generator은 제공된 테이블명, 열 목록 및 질문에 기반하여 SQL 쿼리를 생성합니다. 이는 템플릿 문자열을 활용하여 쿼리 생성 프로세스를 구조화하며, 테이블 명, 열 목록 및 질문에 대한 자리 표시자를 포함합니다. 그런 다음 PromptTemplate 객체를 사용하여 이러한 자리 표시자를 채워넣고 LLMChain을 통해 대형 언어 모델 (LLM)과 상호 작용하여 SQL 쿼리를 생성합니다. 마지막으로 생성된 SQL 쿼리를 출력합니다.

```js
def query_generator(tble, cols, question):

  template = """Generate a SQL query using the following table name: {Table}, and columns as a list: {Columns}, to answer the following question:
  {question}.

  Output Query:

  """

  prompt = PromptTemplate(template=template, input_variables=["Table", "question", "Columns"])

  llm_chain = LLMChain(prompt=prompt, llm=local_llm)

  response = llm_chain.run({"Table": tble, "question": question, "Columns": cols})
  print(response)
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

# 표

transaction = [
"transaction_id",
"transaction_amount",
"transaction_date",
"transaction_type",
"transaction_status",
"transaction_description",
"transaction_source_account",
"transaction_destination_account",
"transaction_currency",
"transaction_fee"
]

    inputs = ["transaction_id가 10인 경우 transaction_amount, transaction_date, transaction_type,transaction_description을 검색하는 SQL 쿼리 생성",
             "transaction_status가 'completed'인 경우 transaction_id, transaction_date, transaction_type, transaction_source_account을 검색하는 SQL 쿼리 생성",
             "transaction_type 및 평균 transaction_amount의 개수를 검색하고 transaction_type로 정렬하는 SQL 쿼리 생성",
             "각 소스 계정별 총 거래 금액 목록을 검색하고 총 거래 금액을 내림차순으로 정렬하는 SQL 쿼리 생성",
             "각 거래 유형별 최대 거래 금액을 검색하고 거래 유형으로 정렬하는 SQL 쿼리 생성"]

    for input in inputs:
        query_generator("transaction",transaction ,question=find_columns_match(input,transaction))

# 응답

- 다음과 같은 테이블 이름을 사용하고 컬럼을 나열한 리스트를 사용하여 SQL 쿼리를 생성하십시오: transaction 및 [‘transaction_id’, ‘transaction_amount’, ‘transaction_date’, ‘transaction_type’, ‘transaction_status’, ‘transaction_description’, ‘transaction_source_account’, ‘transaction_destination_account’, ‘transaction_currency’, ‘transaction_fee’], 다음 질문에 대한 응답을 위해 SQL 쿼리를 생성하십시오: (‘transaction_id가 10인 경우 transaction_amount, transaction_date, transaction_type,transaction_description을 검색하는 SQL 쿼리 생성’).

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
출력 쿼리:

  SELECT transaction_amount, transaction_date, transaction_type, transaction_description FROM transaction WHERE transaction_id = 10;
```

2. 다음과 같은 테이블 이름인 transaction과 열 목록인 [‘transaction_id’, ‘transaction_amount’, ‘transaction_date’, ‘transaction_type’, ‘transaction_status’, ‘transaction_description’, ‘transaction_source_account’, ‘transaction_destination_account’, ‘transaction_currency’, ‘transaction_fee’]을 사용하여 다음 질문에 대한 SQL 쿼리를 생성하십시오:
   (‘transaction_status가 ‘completed’인 경우 transaction_id, transaction_date, transaction_type, transaction_source_account를 검색하는 SQL 쿼리를 생성하십시오’).

```js
출력 쿼리:
  SELECT transaction_id, transaction_date, transaction_type, transaction_source_account FROM transaction WHERE transaction_status = 'completed'
```

3. 다음과 같은 테이블 이름인 transaction과 열 목록인 [‘transaction_id’, ‘transaction_amount’, ‘transaction_date’, ‘transaction_type’, ‘transaction_status’, ‘transaction_description’, ‘transaction_source_account’, ‘transaction_destination_account’, ‘transaction_currency’, ‘transaction_fee’]을 사용하여 다음 질문에 대한 SQL 쿼리를 생성하십시오:
   (‘transaction_type의 count와 평균 transaction_amount를 가져오고 transaction_type으로 정렬하십시오’).

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
결과 쿼리:

  SELECT transaction_type, AVG(transaction_amount) AS avg_transaction_amount, COUNT(*) AS total_count
  FROM transaction
  GROUP BY transaction_type
  ORDER BY transaction_type;
```

4. 다음 테이블 이름과 열 목록을 사용하여 SQL 쿼리를 생성하십시오: transaction 및 열: ['transaction_id', 'transaction_amount', 'transaction_date', 'transaction_type', 'transaction_status', 'transaction_description', 'transaction_source_account', 'transaction_destination_account', 'transaction_currency', 'transaction_fee'], 다음 질문에 답하십시오:
   (‘리스트에서 각 소스 계정의 총 거래 금액을 내림차순으로 정렬하여 조회하는 SQL 쿼리를 생성하세요’).

```js
결과 쿼리:

       SELECT transaction_source_account, SUM(transaction_amount) AS TotalTransactionAmount
        FROM transaction
        GROUP BY transaction_source_account
        ORDER BY TotalTransactionAmount DESC;
```

5. 다음 테이블 이름과 열 목록을 사용하여 SQL 쿼리를 생성하십시오: transaction 및 열: ['transaction_id', 'transaction_amount', 'transaction_date', 'transaction_type', 'transaction_status', 'transaction_description', 'transaction_source_account', 'transaction_destination_account', 'transaction_currency', 'transaction_fee'], 다음 질문에 답하십시오:
   (‘각 거래 유형의 최대 거래 금액을 찾아 거래 유형으로 정렬하는 SQL 쿼리를 생성하세요’).

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
출력 쿼리:

   SELECT transaction_type, MAX(transaction_amount) AS max_transaction_amount
   FROM transaction
   GROUP BY transaction_type
   ORDER BY transaction_type;
```

일반적인 추출은 효과적이지만, 연구 결과, 데이터를 세부 조정하여 LLM을 수행하면 우수한 결과를 얻을 수 있습니다. 세밀 조정 접근법을 채용해 봅시다.

# 2 Fine-tune NL2SQL with Phi-3

Phi-3를 만나보세요, Microsoft의 최신 오픈 AI 모델의 주요 성과입니다. Phi-3-mini, Phi-3-small 및 Phi-3-medium을 통해, 이 작은 언어 모델 (SLM)의 Phi-3 패밀리는 AI 모델의 세계를 혁신하도록 설계되었습니다. 38억 개의 파라미터를 사용하고 33조 개의 토큰으로 훈련된 Phi-3-mini는 높은 성능을 발휘하며 Mixtral 8x7B 및 GPT-3.5와 같은 큰 모델과 같은 성능을 보여줍니다. 게다가, 이 모델은 스마트폰 장치에서 효율적으로 작동할 수 있습니다. Phi-3의 성공은 훈련 데이터셋에 기인합니다. Phi-2의 데이터셋의 진화된 버전입니다. 상세히 걸러낸 웹 데이터 및 합성 입력을 통해 이러한 모델은 강도, 안전 및 대화 능력에 우선순위를 두어 다양한 응용프로그램에 적합합니다. 7B 및 14B 파라미터를 가진 Phi-3-small 및 Phi-3-medium은 효율 유지와 함께 Phi-3의 기능을 더욱 향상시키도록 설계되었습니다.

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

## Phi 3 Architecture and Evaluation

Phi-3 패밀리는 품질과 비용을 균형있게 유지하도록 설계된 다양한 모델을 제공하여 생성형 AI 애플리케이션을 개발하는 고객을 위한 옵션을 제공합니다.

Phi-3-mini: 이 모델은 38억 개의 파라미터를 갖추고 33조 개의 토큰으로 이루어진 광범위한 데이터셋을 기반으로 훈련되었습니다. 32개의 레이어, 32개의 어텐션 헤드, 그리고 3072개의 히든 디멘션을 갖는 트랜스포머 디코더 아키텍처를 채택했습니다. 디폴트 콘텍스트 길이는 4천 개의 토큰이며, 32K 어휘 사전을 사용하는 토크나이저를 활용합니다. 추가로, 128K 토큰의 콘텍스트 길이를 갖춘 확장 버전인 Phi-3-mini-128K도 있습니다.

Phi-3-small: 70억 개의 파라미터로 훈련된 Phi-3-small은 48조 개의 토큰을 사용합니다. 이 모델은 100K 어휘 사전과 8천 개의 디폴트 콘텍스트 길이를 갖추었습니다. 아키텍처는 32개의 레이어, 32개의 어텐션 헤드, 그리고 4096개의 히든 디멘션으로 이루어져 있습니다. 이 모델은 메모리 사용량을 최적화하기 위해 그룹화된 쿼리 어텐션과 번갈아가며 쓰이는 밀집/희소 어텐션을 활용합니다.

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

Phi-3-medium: 이 미리보기 모델은 140억 개의 매개변수를 자랑하며 4.8조 개의 토큰으로 학습되었습니다. 40개의 레이어, 40개의 어텐션 헤드, 그리고 임베딩 크기는 5120입니다.

## 훈련 방법:

- 훈련 데이터 구성: Phi-3 모델의 훈련 데이터는 신중하게 선별됩니다. 교육 수준별로 분류된 웹 데이터와 합성 LLM 생성 데이터로 구성되며 두 가지 이질적이고 순차적인 단계로 사전 훈련을 거칩니다.
- 사전 훈련 단계: 제1 단계는 일반 지식과 언어 이해에 중점을 둔 웹 소스를 사용합니다. 제2 단계는 논리 추론 및 특정 기술을 가르치기 위해 제1 단계의 웹 데이터와 합성 데이터를 더 많이 활용합니다.
- 사후 훈련 단계: 사전 훈련 후, Phi-3-mini는 감독형 세밀 조정 (SFT) 및 직접 선호도 최적화 (DPO)를 거쳤습니다. SFT는 수학, 코딩, 추론, 대화, 모델 신원, 안전 도메인 간에 높은 품질의 데이터를 선별하는 과정을 포함합니다.
- DPO는 채팅 형식 데이터, 추론, 그리고 책임 있는 AI 노력에 초점을 맞춥니다.
- 맥락 확장: Phi-3-mini의 맥락 창 크기가 Long Rope 방법론을 사용하여 4k 토큰에서 128k 토큰으로 확장되었습니다. 이 확장은 맥락의 길이가 크게 증가함에도 일관된 성능을 유지합니다.
- 데이터 최적화: 훈련 데이터는 모델의 규모를 위한 "데이터 최적" 지점으로 보정됩니다. 웹 데이터는 지식과 추론의 적절한 균형을 보장하기 위해 필터링됩니다. 특히 작은 모델의 경우 이는 매우 중요합니다.
- 다른 모델과의 비교: Phi-3의 접근 방식은 이전 작업과 대조적으로, 해당 규모에 대한 데이터 품질에 중점을 두며 컴퓨팅이나 과도한 훈련 방법보다 데이터 최적화를 강조합니다. 벤치마크 비교는 Phi-3가 작은 모델 용량을 위한 최적화를 잘 보여줍니다.
- Phi-3-medium 미리보기: 140억 개의 매개변수를 가진 Phi-3-medium은 Phi-3-mini와 유사하게 훈련되었지만 더 큰 규모로 이루어집니다. 일부 벤치마크에서는 7B에서 14B 매개변수로의 전환에서 큰 개선이 없어 계속해서 데이터 혼합을 개선 중임을 시사합니다.
- 사후 향상: 모델은 채팅 능력, 견고성, 그리고 안전성을 향상시키기 위해 감독형 세밀 조정 및 DPO를 통한 선호도 조정을 거칩니다.

## 안전성

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

Phi-3-mini은 Microsoft의 책임 있는 AI 원칙에 따라 만들어진 AI 모델입니다. 이 프로젝트는 개발 초기부터 안전을 우선시하는 원칙을 중요시하여 만들어졌습니다. 모델이 윤리 기준을 준수하고 잠재적인 피해를 최소화할 수 있는 능력을 보장하기 위해 포괄적인 전략이 채택되었습니다.

모델 학습 후에는, 해당 모델이 책임 있는 AI 기준을 충족하는지 확인하기 위해 면밀한 안전 조정이 이루어집니다. 게다가, Microsoft의 독립된 레드 팀이 Phi-3-mini를 검토하여 강화 및 안전 프로토콜을 강화할 수 있는 부분을 식별합니다.

자동화된 테스팅과 잠재적인 피해의 다양한 범주에 대한 평가는 프로세스의 중요한 부분입니다. 이러한 테스트는 모델의 출력물로부터 발생하는 모든 위험을 감지하고 해결하는 데 목표를 두고 있습니다.

더 나아가, Phi-3-mini는 의견 데이터 세트를 활용하여 응답을 더욱 개선합니다. 특정 테스트 중 확인된 잠재적인 피해 범주에 대응하기 위해 내부에서 생성된 데이터 세트가 사용됩니다.

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

## 코드 구현

- 패키지 설치

```js
 !pip install -q -U bitsandbytes
 !pip install -q -U transformers
 !pip install -q -U xformers
 !pip install -q -U peft
 !pip install -q -U accelerate
 !pip install -q -U datasets
 !pip install -q -U trl
 !pip install -q -U einops
 !pip install -q -U nvidia-ml-py3
 !pip install -q -U huggingface_hub
```

2. 패키지 가져오기

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
from datasets import load_dataset
from transformers import AutoTokenizer, AutoModelForCausalLM, BitsAndBytesConfig, TrainingArguments, Trainer, DataCollatorForLanguageModeling
from pynvml import *
import time, torch
from trl import SFTTrainer
from peft import LoraConfig, PeftModel, get_peft_model, prepare_model_for_kbit_training
from peft import AutoPeftModelForCausalLM
```

3. 데이터셋 불러오기

```python
dataset = load_dataset("b-mc2/sql-create-context")
dataset
```

4. 데이터셋 형식화

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
def create_prompt(sample):
      system_prompt_template = """<s>
            아래는 작업을 설명하는 지시사항입니다. 요청을 적절히 완료하는 응답을 작성하십시오.
            ### 지시사항: <<user_question>>
            ### 데이터베이스 스키마:
            <<database_schema>>
            ### 응답:
            <<user_response>>
            </s>
            """
      user_message = sample['question']
      user_response = sample['answer']
      database_schema = sample['context']
      prompt_template = system_prompt_template.replace("<<user_question>>",f"{user_message}").replace("<<user_response>>",f"{user_response}").replace("<<database_schema>>",f"{database_schema} ")

      return {"inputs":prompt_template}


instruct_tune_dataset = dataset.map(create_prompt)
print(instruct_tune_dataset)

def print_gpu_utilization():
    nvmlInit()
    handle = nvmlDeviceGetHandleByIndex(0)
    info = nvmlDeviceGetMemoryInfo(handle)
    print(f"GPU 메모리 사용량: {info.used//1024**2} MB.")
```

5. 토크나이저와 모델 로드

```js
base_model_id = "microsoft/Phi-3-mini-4k-instruct"

# 토크나이저 로드
tokenizer = AutoTokenizer.from_pretrained(base_model_id, use_fast=True)
# fp16로 모델 로드
model = AutoModelForCausalLM.from_pretrained(base_model_id, trust_remote_code=True, torch_dtype=torch.float16, device_map={"": 0})
print(print_gpu_utilization())
```

6. 모델 추론

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

# 프롬프트 정의

```bash
    prompt = [
        "코코넛 밀크로 만든 치킨 카레 레시피를 작성해주세요.",
        "다음 문장을 프랑스어로 번역해주세요: '나는 빵과 치즈를 좋아해요!'",
        "유명한 20명의 인물을 인용해보세요.",
        "지금 달은 어디에 있나요?"
    ]

    # 변수 초기화
    duration = 0.0
    total_length = 0

    # 프롬프트 반복
    for i in range(len(prompt)):
        # 프롬프트 토큰화 및 GPU로 이동
        inputs = tokenizer(prompt[i], return_tensors="pt").to("cuda:0")

        # 입력 텐서 인덱스를 torch.long으로 변환
        inputs = {k: v.to(torch.long) for k, v in inputs.items()}

        # 시작 시간
        start_time = time.time()

        # 자동 캐스팅을 사용하여 추론 수행
        with torch.cuda.amp.autocast(enabled=False):  # 자동 캐스팅 비활성화
            output = model.generate(**inputs, max_length=500)

        # 소요 시간과 총 길이 계산
        duration += float(time.time() - start_time)
        total_length += len(output)

        # 프롬프트당 토큰 속도 계산
        tok_sec_prompt = round(len(output) / float(time.time() - start_time), 3)

        # 프롬프트당 토큰 속도 출력
        print("프롬프트 --- %s 토큰/초 ---" % (tok_sec_prompt))

        # 디코드된 출력 출력
        print(tokenizer.decode(output[0], skip_special_tokens=True))

    # 평균 토큰 속도 계산
    tok_sec = round(total_length / duration, 3)
    print("평균 --- %s 토큰/초 ---" % (tok_sec))
```

9. Fine-tuning되지 않은 Text to SQL

```bash
    prompt = [
        """
        다음은 작업을 설명하는 지시사항입니다. 요청을 적절히 완료하는 응답을 작성하십시오.
        ### 지시사항 :
        각 도시의 역 중 가장 높은 위도를 가진 역순으로 모든 도시를 나열하십시오.
        데이터베이스 스키마:
        CREATE TABLE station (city VARCHAR, lat INTEGER)
        ### 응답:
        SELECT city, lat FROM station ORDER BY lat DESC;
        """,
        """
        다음은 작업을 설명하는 지시사항입니다. 요청을 적절히 완료하는 응답을 작성하십시오.
        ### 지시사항 :
        '각 선수가 20점 이상 및 10점 미만을 가지고 있으며 상위 10위 안에 있는 포지션은 무엇입니까?
        데이터베이스 스키마:
        CREATE TABLE player (POSITION VARCHAR, Points INTEGER, Ranking INTEGER)
        ### 응답:
        SELECT POSITION, Points, Ranking
        FROM player
        WHERE Points > 20 AND Points < 10 AND Ranking IN (1,2,3,4,5,6,7,8,9,10)
        """,
        """
        다음은 작업을 설명하는 지시사항입니다. 요청을 적절히 완료하는 응답을 작성하십시오.
        ### 지시사항 :
        노래를 가장 많이 연주한 밴드 맴버의 이름을 찾아보세요.
        데이터베이스 스키마:
        CREATE TABLE Songs (SongId VARCHAR); CREATE TABLE Band (firstname VARCHAR, id VARCHAR); CREATE TABLE Performance (bandmate VARCHAR)
        ### 응답:
        SELECT b.firstname
        FROM Band b
        JOIN Performance p ON b.id = p.bandmate
        GROUP BY b.firstname
        ORDER BY COUNT(*) DESC
        LIMIT 1;
        """
    ]

    for i in range(len(prompt)):
      model_inputs = tokenizer(prompt[i], return_tensors="pt").to("cuda:0")
      start_time = time.time()
      output = model.generate(**model_inputs, max_length=500, no_repeat_ngram_size=10, pad_token_id=tokenizer.eos_token_id, eos_token_id=tokenizer.eos_token_id)[0]
      duration += float(time.time() - start_time)
      total_length += len(output)
      tok_sec_prompt = round(len(output)/float(time.time() - start_time),3)
      print("프롬프트 --- %s 토큰/초 ---" % (tok_sec_prompt))
      print(print_gpu_utilization())
      print(tokenizer.decode(output, skip_special_tokens=False))

    tok_sec = round(total_length/duration,3)
    print("평균 --- %s 토큰/초 ---" % (tok_sec))

    # Fine-tuning

    base_model_id = "microsoft/Phi-3-mini-4k-instruct"

    tokenizer = AutoTokenizer.from_pretrained(base_model_id, add_eos_token=True, use_fast=True, max_length=250)
    tokenizer.padding_side = 'right'
    tokenizer.pad_token = tokenizer.eos_token

    compute_dtype = getattr(torch, "float16") # Ampere (또는 최신) GPU를 사용하는 경우 bfloat16로 변경
    bnb_config = BitsAndBytesConfig(
            load_in_4bit=True,
            bnb_4bit_quant_type="nf4",
            bnb_4bit_compute_dtype=compute_dtype,
            bnb_4bit_use_double_quant=True,
    )
    model = AutoModelForCausalLM.from_pretrained(
              base_model_id, trust_remote_code=True, quantization_config=bnb_config, revision="refs/pr/23", device_map={"": 0}, torch_dtype="auto", flash_attn=True, flash_rotary=True, fused_dense=True
    )
    print(print_gpu_utilization())

    model = prepare_model_for_kbit_training(model)
```

10. LoRA 매개변수

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
peft_config = LoraConfig(
  (lora_alpha = 16),
  (lora_dropout = 0.05),
  (r = 16),
  (bias = "none"),
  (task_type = "CAUSAL_LM"),
  (target_modules = ["q_proj", "k_proj", "v_proj", "dense", "fc1", "fc2"])
);
```

9. Training Parameters

```js
training_arguments = TrainingArguments(
            output_dir="./phi3-results",
            save_strategy="epoch",
            per_device_train_batch_size=4,
            gradient_accumulation_steps=12,
            log_level="debug",
            save_steps=100,
            logging_steps=25,
            learning_rate=1e-4,
            eval_steps=50,
            optim='paged_adamw_8bit',
            fp16=True, #change to bf16 if are using an Ampere GPU
            num_train_epochs=1,
            max_steps=400,
            warmup_steps=100,
            lr_scheduler_type="linear",
            seed=42)
```

10. Data Prepare for the training

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
train_dataset = instruct_tune_dataset.map(
  (batched = True),
  (remove_columns = ["answer", "question", "context"])
);
train_dataset;
```

11. Fine-Tuned

```js
trainer = SFTTrainer(
    model=model,
    train_dataset=train_dataset["train"],
    #eval_dataset=dataset['test'],
    peft_config=peft_config,
    dataset_text_field="inputs",
    max_seq_length=1024,
    tokenizer=tokenizer,
    args=training_arguments,
    packing=False
)

trainer.train()
```

12. Test inference with the fine-tuned adapter

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
base_model_id = "microsoft/Phi-3-mini-4k-instruct";
tokenizer = AutoTokenizer.from_pretrained(base_model_id, (use_fast = True));

compute_dtype = getattr(torch, "float16");
bnb_config = BitsAndBytesConfig(
  (load_in_4bit = True),
  (bnb_4bit_quant_type = "nf4"),
  (bnb_4bit_compute_dtype = compute_dtype),
  (bnb_4bit_use_double_quant = True)
);
model = AutoModelForCausalLM.from_pretrained(
  base_model_id,
  (trust_remote_code = True),
  (quantization_config = bnb_config),
  (device_map = { "": 0 })
);
adapter = "/content/phi3-results/checkpoint-400";
model = PeftModel.from_pretrained(model, adapter);
```

13. 수행하기

```js
database_schema = 'CREATE TABLE station (city VARCHAR, lat INTEGER)'
user_question = "List all the cities in a decreasing order of each city's stations' highest latitude."

prompt_template = f"""
Below is an instruction that describes a task. Write a response that appropriately completes the request.
### Instruction:
{user_question}
Database Schema:
{database_schema}
### Response:
"""

question = "'What are the positions with both players having more than 20 points and less than 10 points and are in Top 10 ranking"
context = "CREATE TABLE player (POSITION VARCHAR, Points INTEGER, Ranking INTEGER)"

prompt_template1 = f"""
Below is an instruction that describes a task. Write a response that appropriately completes the request.
### Instruction:
{question}
Database Schema:
{context}
### Response:
"""

context = '''CREATE TABLE Songs (SongId VARCHAR); CREATE TABLE Band (firstname VARCHAR, id VARCHAR); CREATE TABLE Performance (bandmate VARCHAR)'''
question = "Find the first name of the band mate that has performed in most songs."

prompt_template2 = f"""
Below is an instruction that describes a task. Write a response that appropriately completes the request.
### Instruction:
{question}
Database Schema:
{context}
### Response:
"""

prompt = []
prompt.append(prompt_template)
prompt.append(prompt_template1)
prompt.append(prompt_template2)

for i in range(len(prompt)):
  model_inputs = tokenizer(prompt[i], return_tensors="pt").to("cuda:0")
  start_time = time.time()
  output = model.generate(**model_inputs, max_length=500, no_repeat_ngram_size=10, pad_token_id=tokenizer.eos_token_id, eos_token_id=tokenizer.eos_token_id)[0]
  duration += float(time.time() - start_time)
  total_length += len(output)
  tok_sec_prompt = round(len(output)/float(time.time() - start_time),3)
  print("Prompt --- %s tokens/seconds ---" % (tok_sec_prompt))
  print(print_gpu_utilization())
  print(tokenizer.decode(output, skip_special_tokens=False))

tok_sec = round(total_length/duration,3)
print("Average --- %s tokens/seconds ---" % (tok_sec))
```

14. 모델 저장하기

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
import locale
import shutil
from huggingface_hub import notebook_login
from google.colab import drive

# Set the preferred encoding to UTF-8
locale.getpreferredencoding = lambda: "UTF-8"

# Log in to the notebook
notebook_login()

# Push the fine-tuned adapter to the Hugging Face Hub
trainer.push_to_hub(commit_message="fine-tuned adapter")

# Mount Google Drive
drive.mount('/content/drive')

# Move the trained model to Google Drive
shutil.move('/content/phi3-results', '/content/drive/MyDrive/PHI-3')

# Load the trained model
trained_model = AutoPeftModelForCausalLM.from_pretrained("/content/drive/MyDrive/PHI-3/phi3-results/checkpoint-400",
                                                         low_cpu_mem_usage=True,
                                                         return_dict=True,
                                                         torch_dtype=torch.float16,
                                                         device_map='auto',)

# Merge and unload the trained model
lora_merged_model = trained_model.merge_and_unload()

# Save the merged model
lora_merged_model.save_pretrained("/content/drive/MyDrive/PHI-3/phi3-results/lora_merged_model", safe_serialization=True)

# Save the tokenizer for the merged model
tokenizer.save_pretrained("/content/drive/MyDrive/PHI-3/phi3-results/lora_merged_model")

# Push the merged model to the Hugging Face Hub
lora_merged_model.push_to_hub(repo_id="", commit_message="merged model")

# Push the tokenizer to the Hugging Face Hub
tokenizer.push_to_hub(repo_id="", commit_message="merged model")
```

15. Perform Inference on Fine-tuned Model

```js
peft_config = LoraConfig(
            lora_alpha=16,
            lora_dropout=0.05,
            r=16,
            bias="none",
            task_type="CAUSAL_LM",
    )

peft_model_id = "username/phi3-results"
config = peft_config.from_pretrained(peft_model_id)

model = AutoModelForCausalLM.from_pretrained(config.base_model_name_or_path,
                                             return_dict=True,
                                             load_in_4bit=True,
                                             device_map="auto",
                                             )

tokenizer = AutoTokenizer.from_pretrained(peft_model_id)

model = PeftModel.from_pretrained(model, peft_model_id)

print(model.get_memory_footprint())

for i in range(len(prompt)):
    model_inputs = tokenizer(prompt[i], return_tensors="pt").to("cuda:0")
    start_time = time.time()
    output = model.generate(**model_inputs, max_length=500, no_repeat_ngram_size=10, pad_token_id=tokenizer.eos_token_id, eos_token_id=tokenizer.eos_token_id)[0]
    duration += float(time.time() - start_time)
    total_length += len(output)
    tok_sec_prompt = round(len(output)/float(time.time() - start_time), 3)
    print("Prompt --- %s tokens/seconds ---" % (tok_sec_prompt))
    print(print_gpu_utilization())
    print(f"RESPONSE:\n {tokenizer.decode(output, skip_special_tokens=False)[len(prompt[i]):].split('</')[0]}")

tok_sec = round(total_length/duration, 3)
print("Average --- %s tokens/seconds ---" % (tok_sec))
```

# Conclusion

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

자연어 처리(NLP)와 SQL 쿼리 엔진의 결합은 데이터베이스와 상호 작용하는 것을 더 쉽고 효율적으로 만들었습니다. 이전에는 SQL에 대한 심층적인 이해가 필요했기 때문에 많은 사용자들에게 어려움이 있었습니다. 그러나 Mistral 7B와 Microsoft Phi-3와 같은 오픈 소스 대형 언어 모델(LLMs)은 이를 바꿨습니다. 이 모델들은 자연어 쿼리를 구조화된 SQL 쿼리로 신속하게 변환하여, 방대한 SQL 전문 지식이 필요 없게 했습니다.

Mistral 7B와 Microsoft Phi-3는 NLP 작업에서 우수한 성능을 발휘하는 탁월한 모델들입니다. 그들은 Grouped-Query Attention과 Sliding Window Attention과 같은 기능을 갖추어 더욱 효율적입니다. 크기가 작은 Microsoft Phi-3도 NLP 성능과 효율성에서 새로운 기준을 세우며, 복잡한 벤치마크에서 더 큰 모델들을 능가합니다.

오픈 소스 LLMs를 고급 분석 플랫폼과 AI 시스템에 통합함으로써 기업은 손쉽게 통찰을 추출할 수 있습니다. 이 기술은 금융, 건강 관리, 전자 상거래와 같은 산업들이 데이터 기반 결정을 내리는 방식을 변화시켰습니다. 이러한 모델들이 다양한 부문에 미치는 영향은 상당하며 혁신과 변혁을 촉진했습니다.

NLP와 SQL의 융합을 통해 오픈 소스 LLMs는 데이터 접근을 민주화시키고 효율성, 생산성, 기업 성공을 촉진했습니다. 이는 데이터 자산의 최대 잠재력을 발휘하도록 허용하여 이해당사자들이 실행 가능한 통찰을 추출하기 쉬워지고, 여러 부문에서 탐구와 혁신의 문화를 육성했습니다.

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

노트북: phi3

제 이전 📝 글들을 확인해주세요.

# 참고 자료

- https://arxiv.org/pdf/2310.06825.pdf
- https://artgor.medium.com/paper-review-mistral-7b-6acdf2f3132d
- https://medium.com/dair-ai/papers-explained-mistral-7b-b9632dedf580
- https://www.datacamp.com/tutorial/mistral-7b-tutorial
- https://www.analyticsvidhya.com/blog/2023/11/from-gpt-to-mistral-7b-the-exciting-leap-forward-in-ai-conversations/
- https://medium.com/@rubentak/mistral-7b-the-best-7-billion-parameter llm-yet-8b0aa03016f9
- https://clarifai.com/mistralai/completion/models/mistral-7B-Instruc
- https://iamgeekydude.com/2023/06/02/alpaca-llm-load-model-using-langchain-hf/
- https://news.microsoft.com/source/features/ai/the-phi-3-small-language-models-with-big-potential/
- https://huggingface.co/microsoft/Phi-3-mini-128k-instruct
