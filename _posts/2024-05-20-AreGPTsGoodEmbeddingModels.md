---
title: "GPTs가 좋은 임베딩 모델인가요"
description: ""
coverImage: "/assets/img/2024-05-20-AreGPTsGoodEmbeddingModels_0.png"
date: 2024-05-20 20:30
ogImage: 
  url: /assets/img/2024-05-20-AreGPTsGoodEmbeddingModels_0.png
tag: Tech
originalTitle: "Are GPTs Good Embedding Models"
link: "https://medium.com/towards-data-science/are-gpts-good-embedding-models-28d8ef6f3f63"
---


## 세부 사항에 귀신이 있는 놀라운 실험

![이미지](/assets/img/2024-05-20-AreGPTsGoodEmbeddingModels_0.png)

많은 임베딩 모델이 제공되고 있으므로, 기계 학습 응용 프로그램에 적합한 모델을 선택하는 것은 어려울 수 있습니다. 다행히 MTEB 리더보드는 다양한 자연어 처리 작업에 대한 포괄적인 랭킹 지표를 제공합니다.

![이미지](/assets/img/2024-05-20-AreGPTsGoodEmbeddingModels_1.png)

<div class="content-ad"></div>

사이트를 방문하면 상위 다섯 임베딩 모델이 Generative Pre-trained Transformers (GPTs)임을 알 수 있습니다. 이것이 GPT 모델이 임베딩에 가장 적합하다고 생각하게 할 수도 있습니다. 그러나 이것이 정말 사실인지 알아보기 위해 실험을 진행해봅시다.

# GPT 임베딩

임베딩은 문장의 텐서 표현으로, 텍스트 토큰 ID를 변환하여 텐서 공간으로 투영하는 것입니다.

텍스트를 신경망 모델에 입력하고 순전파를 수행하면 임베딩 벡터를 얻을 수 있습니다. 그러나 실제 과정은 조금 더 복잡합니다. 한 단계씩 자세하게 알아봅시다:

<div class="content-ad"></div>

- 텍스트를 토큰 ID로 변환합니다.
- 토큰 ID를 신경망에 전달합니다.
- 신경망의 출력값을 반환합니다.

첫 번째 단계에서는 이를 달성하기 위해 토크나이저를 사용할 것입니다. model_inputs는 "일부 질문" 텍스트 내용의 텐서 표현입니다.

```js
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("mistralai/Mistral-7B-Instruct-v0.1")

messages = [
        {
            "role": "user",
            "content": "일부 질문.",
        },
]

encodeds = tokenizer.apply_chat_template(messages, return_tensors="pt")
model_inputs = encodeds.to("cuda")
```

두 번째 단계는 간단합니다. model_inputs를 신경망에 순전파합니다. 생성된 토큰의 로짓에는 .logits를 통해 액세스할 수 있습니다. torch.no_grad()는 모델 가중치를 업데이트하고 싶지 않기 때문에 모델이 추론 모드에 있음을 의미합니다.

<div class="content-ad"></div>

```js
import torch

with torch.no_grad():
    return model(model_inputs).logits
```

세 번째 단계는 조금 까다롭습니다. GPT 모델은 디코더 전용이며 토큰 생성이 자기 회귀적입니다. 간단히 말해, 완료된 문장의 마지막 토큰은 문장 내의 모든 이전 토큰을 본 적이 있습니다. 따라서 마지막 토큰의 출력에는 이전 토큰들로부터의 모든 친화도 점수(어텐션)가 포함되어 있습니다.

Hugging Face에서 구현된 GPT의 출력 차원은 (배치 크기, 입력 토큰 크기, 어휘 크기)입니다. 모든 배치의 마지막 토큰 출력을 얻으려면 텐서 슬라이스를 수행할 수 있습니다.

```js
import torch
with torch.no_grad():
    return model(model_inputs).logits[:, -1, :]
```

<div class="content-ad"></div>

# 이 GPT 임베딩의 품질

이 GPT 임베딩의 품질을 측정하려면 코사인 유사도를 사용할 수 있어요. 코사인 유사도가 높을수록 문장의 의미가 더 가깝다는 뜻이에요.

```js
import torch
def compute_cosine_similarity(vec1, vec2):
    cos = torch.nn.CosineSimilarity(dim=1, eps=1e-6)
    return cos(vec1, vec2)
```

우리가 질문과 답변 쌍 목록을 순회하고 결과를 확인하는 유틸리티 함수를 만들어봐요. 이 실험에는 오픈소스로 공개된 위대한 모델 중 하나인 Mistral 7b v0.1이 사용돼요.

<div class="content-ad"></div>

```python
import torch
from termcolor import colored
from transformers import AutoModelForCausalLM, AutoTokenizer

model = AutoModelForCausalLM.from_pretrained(
    "mistralai/Mistral-7B-Instruct-v0.1"
)

tokenizer = AutoTokenizer.from_pretrained("mistralai/Mistral-7B-Instruct-v0.1")

def generate_last_token_embeddings(question):
    messages = [
        {
            "role": "user",
            "content": question,
        },
    ]
    encodeds = tokenizer.apply_chat_template(messages, return_tensors="pt")
    model_inputs = encodeds.to("cuda")
    with torch.no_grad():
        return model(model_inputs).logits[:, -1, :]

def get_similarities(questions, answers):
    for question in questions:
        for answer in answers:
            q_embedding, a_embedding = (
                generate_last_token_embeddings(question),
                generate_last_token_embeddings(answer),
            )
            similarity = compute_cosine_similarity(q_embedding, a_embedding)
            print(colored(f"question: {question} and ans: {answer}", "green"))
            print(colored(f"result: {similarity}", "blue"))

questions = ["Where is the headquarter of OpenAI?", "What is GPU?"]
answers = [
    "OpenAI is based at San Francisco.",
    "A graphics processing unit (GPU) is an electronic circuit that can perform mathematical calculations quickly",
]
get_similarities(questions, answers)
```

![image](/assets/img/2024-05-20-AreGPTsGoodEmbeddingModels_2.png)

# 결과 및 관찰

첫 번째 질문과 대답 쌍에 대한 결과:```

<div class="content-ad"></div>

- 질문: "OpenAI의 본사는 어디에 있나요?"
- 답변: "OpenAI는 샌프란시스코에 본부를 두고 있습니다."
- 코사인 유사도: 0.96

두 번째 질문과 대답 쌍에 대해:

- 질문: "GPU란 무엇인가요?"
- 답변: "그래픽 처리 장치 (GPU)는 빠르게 수학적 계산을 수행할 수 있는 전자 회로입니다."
- 코사인 유사도: 0.94

관련 없는 쌍에 대해:

<div class="content-ad"></div>

- 질문: “OpenAI의 본사는 어디에 있습니까?”
- 대답: “그래픽 처리 장치(GPU)는 수학적 계산을 빠르게 수행할 수 있는 전자 회로입니다.”
- 코사인 유사도: 0.90

최악의 쌍의 경우:

- 질문: “GPU가 무엇인가요?”
- 대답: “OpenAI는 샌프란시스코에 기반을 두고 있습니다.”
- 코사인 유사도: 0.93

이러한 결과는 GPT 모델을 임베딩 모델로 사용하면 관련 및 관련 없는 쌍을 구별하는 면에서 큰 결과를 얻을 수 없을 수 있다는 것을 나타냅니다. 그러나 왜 GPT 모델은 여전히 상위 5위 내에 있습니까?

<div class="content-ad"></div>

# 대조 손실이 구조에 도움이 됩니다

```js
tokenizer = AutoTokenizer.from_pretrained("intfloat/e5-mistral-7b-instruct")
model = AutoModelForCausalLM.from_pretrained(
  "intfloat/e5-mistral-7b-instruct"
)
```

```img
/assets/img/2024-05-20-AreGPTsGoodEmbeddingModels_3.png
```

다른 모델 e5-mistral-7b-instruct을 사용하여 동일한 평가 절차를 반복했더니, 이 모델은 MTEB leaderboard의 최상위 오픈소스 모델 중 하나로, mistral 7b instruct로부터 미세 조정되었습니다. 이 모델을 사용한 결과, 관련 질문과 쌍의 코사인 유사도는 각각 오픈AI와 GPU 질문에 대해 0.88 및 0.84입니다. 관련없는 질문과 답변 쌍에 대한 유사도는 0.56 및 0.67로 감소합니다. 이 결과는 e5-mistral-7b-instruct이 임베딩에 대해 훨씬 향상된 모델이라는 것을 시사합니다. 이런 개선이 된 이유는 무엇일까요?

<div class="content-ad"></div>

```markdown
![Embedding Model](/assets/img/2024-05-20-AreGPTsGoodEmbeddingModels_4.png)

해당 e5-mistral-7b-instruct 논문을 살펴보면, 핵심은 contrastive loss를 사용하여 mistral 모델을 추가 조정하는 데 있습니다.

이 블로그 게시물에서는 이 개념을 자세히 다루었습니다. sim 함수는 두 벡터 간의 코사인 거리를 계산합니다. 대조 손실에서 분모는 양성 예와 음성 예 사이의 코사인 거리를 나타냅니다. 대조 손실의 이유는 비슷한 벡터가 가능한 한 1에 가까워지도록 하고 싶기 때문입니다. 왜냐하면 log(1) = 0이 최적의 손실을 나타내기 때문입니다.

# 결론
```

<div class="content-ad"></div>

이 게시물에서는 GPT를 임베딩 모델로 사용할 때 일반적인 함정을 강조했습니다. 내가 한 평가는 GPT를 대조 손실로 미세 조정할 때 임베딩이 더 의미 있고 차별적일 수 있다는 것을 제안합니다. GPT 모델의 강점과 한계를 이해하고 대조 손실과 같은 사용자 지정 손실을 활용함으로써, 머신러닝 프로젝트에 임베딩 모델을 선택하고 활용할 때 보다 정보를 얻을 수 있습니다. 이 게시물이 여러분이 응용 프로그램에 현명하게 GPT 모델을 선택하는 데 도움이 되기를 바라며 피드백을 기다리겠습니다! :)