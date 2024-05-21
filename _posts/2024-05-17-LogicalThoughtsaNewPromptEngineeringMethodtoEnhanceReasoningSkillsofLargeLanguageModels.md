---
title: "논리적인 사고 - 대형 언어 모델의 추론 능력 강화를 위한 새로운 프롬프트 엔지니어링 방법"
description: ""
coverImage: "/assets/img/2024-05-17-LogicalThoughtsaNewPromptEngineeringMethodtoEnhanceReasoningSkillsofLargeLanguageModels_0.png"
date: 2024-05-17 19:51
ogImage: 
  url: /assets/img/2024-05-17-LogicalThoughtsaNewPromptEngineeringMethodtoEnhanceReasoningSkillsofLargeLanguageModels_0.png
tag: Tech
originalTitle: "Logical Thoughts — a New Prompt Engineering Method to Enhance Reasoning Skills of Large Language Models"
link: "https://medium.com/@keganmessmer/logical-thoughts-a-new-prompt-engineering-method-to-enhance-reasoning-skills-of-large-language-5d9b315a0a9d"
---


큰 언어 모델 환시 문제를 해결하기 위해 프롬프트 엔지니어들이 얼마나 빠르게 작업하고 있는지 알아보세요.

## TL;DR

Chat-GPT와 같은 대규모 언어 모델(Large Language Models, LLM)이 최근 몇 년 동안 인기를 얻고 있습니다. 불행하게도 LLM은 논리 추론을 필요로 하는 작업에 직면하면 종종 "환시"하는 경향이 있어 전문적인 응용 프로그램에서 신뢰할 수 없는 경우가 많습니다.

인간-언어 모델 상호작용을 개선하려는 목적으로 일하는 프롬프트 엔지니어들은 LLM의 논리 추론을 개선하기 위한 새로운 방법을 개발했습니다.

<div class="content-ad"></div>

이 기사의 기반인 원본 논문 "Enhancing Zero-Shot Chain-of-Thought Reasoning in Large Language Models through Logic"은 Zhao 등의 연구진에 의해 2023년 9월에 발표되었으며 다음에서 찾을 수 있습니다: [링크](https://arxiv.org/pdf/2309.13339).

## 배경

최근 몇 년간 대형 언어 모델(Large Language Models, LLM)이 인기를 얻고 있으며 가정, 학교 및 직장에서 일상생활에 영향을 미치고 있습니다. 특히 Chat-GPT는 불가결한 가정 이름이 되었습니다.

LLM은 극도로 방대한 데이터셋을 활용하며 수십억 개 또는 심지어 수조 개의 기계 학습 매개변수로 훈련됩니다. 이 방대한 훈련을 통해 인간 언어의 미묘한 복잡성을 포착하여 인간 대화를 닮은 사용자와의 상호작용을 가능케 합니다.

<div class="content-ad"></div>

LLM(거문고 언어 모델)은 다양한 정보를 검색하는 능력을 갖고 있어 신비롭게 보일 수 있지만, 심각한 응용에 사용하기에 제약이 있는 특성을 가지고 있습니다. 근본적으로 LLM은 인간과 같은 지식을 갖고 있지 않습니다. 그들은 단순히 자신의 훈련 데이터와 프롬프트에서 파생된 텍스트를 생성합니다.

이러한 이유로 LLM은 인간 언어의 내재적 논리에 완전히 의존하며, 이는 "환영"의 경우를 초래할 수 있습니다. 여기서 환영은 잘못된 결과를 생성하거나 일반적으로 인간이 쉽게 처리하는 논리적 단어 문제를 해결하지 못하는 상황을 의미합니다. 이러한 논리적 도전은 프롬프트를 다시 구성함으로써 완화될 수 있으며, 이는 본질적으로 LLM이 논리적 사고를 하도록 강요하는 것입니다.

LLM의 보급화를 고려할 때, 현대 기계 학습에서 중요한 프롬프트 엔지니어링이라는 전용 학문 분야가 LLM의 성능을 향상시키고 실용적 목적을 위해 그 출력을 정제하는 데 집중하고 있음을 발견하는 것은 놀라운 일이 아닙니다. 이 분야는 Chat-GPT와 같은 기존 LLM을 보다 효과적으로 활용하기 위한 해결책을 제공하기 때문에, 일종의 없던 기능을 개발하는 대신 기존 LLM을 개선하는 것이 필요한데 그것은 방대한 데이터, 처리 능력, 시간이 필요하기 때문입니다.

본 기사에서 논의된 프롬프트 엔지니어링 논문은 LLM 환영 문제를 해결하기 위해 논리적 원칙을 프롬프트 디자인에 통합하려는 목표를 가지고 있습니다.

<div class="content-ad"></div>

## 방법

해당 논문은 LLM을 위한 제로샷 연쇄사고 프롬프팅을 개선하기 위해 특별히 고안된 새로운 프롬프트 엔지니어링 방법인 Logical Thoughts(LoT)을 소개합니다.

Wei 등이 처음 제시한 연쇄사고(CoT) 프롬프팅은 퓨샷 프롬프팅의 한 형태입니다. 제로샷 프롬프팅과는 달리 퓨샷 프롬프팅은 LLM이 해결해야 할 질문을 제기하기 전에 비슷한 질문-답변 쌍의 "예시"를 제공하는 것을 포함합니다. CoT 프롬프팅에서 예시 답변은 문제를 단계별로 설명합니다. 이를 통해 LLM은 실제 질문에 단계별로 응답해야 하며, 예시 답변을 모방합니다. LLM에게 문제를 단계별로 처리하도록 강요함으로써 응답의 정확도를 크게 향상시킬 수 있습니다.

Kojima 등이 제시한 제로샷 연쇄사고 프롬프팅은 기존 예시를 제공하지 않고 프롬프트에 "한 단계씩 생각해 봅시다"라는 구문을 추가하여 이 효과를 흉내 낼려고 시도합니다. 이 새로운 방법이 개선하려는 것은 이 "제로샷" 연쇄사고 프롬프팅이며, 원래의 "퓨샷" CoT 프롬프팅이 아님을 이해하는 것이 중요합니다.

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-17-LogicalThoughtsaNewPromptEngineeringMethodtoEnhanceReasoningSkillsofLargeLanguageModels_0.png)

LoT은 LLM에게 일반적인 제로샷 CoT 프롬프팅에 따라 문제를 단계별로 해결하도록 합니다. LLM이 초기 단계별 솔루션을 제시한 후, 후속 프롬프트에서는 LLM에게 각 단계를 확인하고 필요에 따라 수정하도록 요청합니다. 이는 LLM에게 각 단계에 대해 긍정적 및 부정적 리뷰를 제공하도록 지시한 다음, 올바른 리뷰를 정당화하고 잘못된 리뷰를 비판하도록 지시하며, 원본 문제의 가정을 고려합니다. 그런 다음 필요한 경우 LLM에게 올바른 리뷰를 사용하여 단계를 수정하도록 요청합니다. 단계가 수정되거나 확인된 후, 원본 문제가 LLM에게 다시 제시되고, 이미 수정되거나 확인된 각 단계가 함께 제시됩니다.

![이미지](/assets/img/2024-05-17-LogicalThoughtsaNewPromptEngineeringMethodtoEnhanceReasoningSkillsofLargeLanguageModels_1.png)

기본적으로, LoT은 LLM을 문제 해결 프로세스를 진행하도록 안내하여 각 단계를 검증하는 데 자체 논리를 사용하도록 요청합니다.


<div class="content-ad"></div>

## 결과

논문은 정확도를 기반으로 LoT 방법을 표준 제로샷 CoT 프롬팅과 비교한 결과를 평가합니다. 연구자들은 Vicuna-7b, Vicuna-13b, Vicuna-33b, GPT-3.5-turbo 및 GPT-4 모델을 GSM8K, AQuA, Date, SocialQA, CauseEffect, Objects, Letter 및 OddOut 데이터셋과 짝지어서 해당 방법을 평가했습니다.

연구 결과는 LoT 방법을 사용할 때 대부분의 모델-데이터셋 조합에서 정확도가 향상된다는 것을 보여줍니다. 특히, OddOut 데이터셋에서 Vicuna-13b 모델을 사용했을 때 +16.28%의 정확도 향상이 있었습니다. 반면, Objects 데이터셋에서 GPT-3.5-turbo 모델을 사용했을 때 -2.50%의 정확도 감소가 관찰되었습니다.

이러한 결과는 LoT 방법이 다양한 모델과 데이터셋에 걸쳐 LLM 응답의 정확도를 향상시키는 데 효과적임을 보여줍니다. 그러나 모델과 데이터셋에 따라 개선의 정도가 다르다는 것을 기억해야 합니다.

<div class="content-ad"></div>

## 토론

사고 연결 추진이 프롬프팅 방식에 작은 변화만으로 LLM의 추론 능력을 크게 향상시켰지만, LoT 방법은 더 복잡한 프레임워크를 도입하여 실제적인 측면에서는 덜 실용적일 수 있습니다. 게다가 이 연구는 LoT 프롬프팅을 퓨-샷 CoT 프롬프팅과 비교하지 않았기 때문에 LoT가 퓨-샷 CoT 프롬프팅보다 더 효과적인지 여부가 분명하지 않습니다. LoT 프롬프팅은 매우 특화된 범위와 복잡한 구현 과정으로 인해 현실 세계에서 그다지 활용될 가능성이 낮을 수 있습니다.

전반적으로, LoT는 LLM의 내재적인 논리 추론 한계를 해결하기 위한 또 다른 해결책에 불과합니다. 이상적인 시나리오에서 LLM이 인간과 같이 논리를 사용할 수 있기를 바라지만, 단순히 더 많은 매개변수로 훈련된 더 큰 모델을 만들더라도 달성될 수 있는지 여부는 불확실합니다. 아니면 보다 근본적인 논리 통합이 필요한지도 모릅니다.

당분간은 LoT와 같은 접근 방식이 기존 LLM의 유틸리티를 최적화하는 데 효과적인 전략으로 기능할 수 있습니다. 특히 의학과 같이 의도적으로 실수를 줄이기 위해 AI로부터 도움을 받을 때 조심스런 경향이 있는 분야에 유용할 수 있는데, 이러한 프롬프팅 엔지니어링 방법은 고객 서비스 응용 프로그램의 효율성을 향상시킬 수 있는 AI 챗봇에도 활용될 수 있습니다. 이 경우, 이러한 프롬프팅 방식은 사용자와 LLM 자체 사이의 버퍼로 구현되어야 할 것으로 생각됩니다.

<div class="content-ad"></div>

앞으로 몇 년 동안의 프롬프트 엔지니어링 진화가 흥미로울 것입니다. 이는 LLMs가 더욱 신뢰할 만한 수준으로 발전하여 더욱 비판적인 응용 프로그램에도 사용될 수 있는 길을 열어 줄 수도 있고, 반대로 LLMs가 발전하여 프롬프트 엔지니어링이 쓸모 없어지는 지점까지 발전 할 수도 있습니다. AI가 일상생활에 더욱 통합되는 시대에 LLM 효과성을 향상시키기 위한 노력의 한 부분으로 LoT를 이해하는 것이 중요합니다.

## 참고문헌

Kojima, T., Gu, S. S., Reid, M., Matsuo, Y., & Iwasawa, Y. (2022). Large Language Models are Zero-Shot Reasoners. https://arxiv.org/abs/2205.11916

Wei, J., Wang, X., Schuurmans, D., Bosma, M., Ichter, B., Xia, F., Chi, E., Le, Q., & Zhou, D. (2022). Chain of Thought Prompting Elicits Reasoning in Large Language Models. https://arxiv.org/abs/2201.11903

<div class="content-ad"></div>

Zhao, X., Li, M., Lu, W., Weber, C., Lee, J., Chu, K., & Wermter, S. (2023). 대규모 언어 모델에서 논리를 통한 Zero-Shot Chain-of-Thought Reasoning 강화. [arXiv 링크](https://arxiv.org/abs/2309.13339)