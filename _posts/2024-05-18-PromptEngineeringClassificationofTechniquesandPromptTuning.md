---
title: "프롬프트 엔지니어링 기술 분류 및 프롬프트 튜닝"
description: ""
coverImage: "/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_0.png"
date: 2024-05-18 20:29
ogImage:
  url: /assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_0.png
tag: Tech
originalTitle: "Prompt Engineering: Classification of Techniques and Prompt Tuning"
link: "https://medium.com/the-modern-scientist/prompt-engineering-classification-of-techniques-and-prompt-tuning-6d4247b9b64c"
---

<img src="/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_0.png" />

현재 계속 발전 중인 prompt engineering 분야는 기술의 명확한 분류가 부족합니다. 다양한 논문과 웹사이트를 살펴보면, 이러한 기술들이 서로 다르며 구조가 부족함을 알 수 있습니다. 이러한 혼란 때문에 실무자들은 종종 가장 간단한 방법에 고수합니다. 이 글에서는 prompt engineering 기술의 개요와 명확한 분류를 제안하여 여러분이 이 개념을 파악하고 어플리케이션에서 효과적으로 사용할 수 있도록 도와드리겠습니다. 또한, prompt tuning 및 평가를 포함한 Data Science 프로세스로써 prompt engineering을 수행하는 방법에 대해 이야기하겠습니다.

<img src="/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_1.png" />

많은 연구 노력에도 불구하고, 대형 언어 모델은 여전히 일부 문제에 직면하고 있습니다. 그들의 주요 함정은 다음과 같습니다:

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

- 자료 인용. LLMs는 외부 자원을 참조한 것처럼 신뢰성 있는 콘텐츠를 생성할 수 있지만, 인터넷에 접속할 수 없기 때문에 자료를 인용할 수 없다는 것을 기억하는 것이 중요합니다.
- 편향. LLMs는 답변에서 편견을 드러낼 수 있으며 종종 편견적이거나 선입견 있는 콘텐츠를 생성할 수 있습니다.
- 환각. LLMs는 가끔 “환각”을 일으키거나 알 수 없는 질문에 대해 거짓 정보를 생성할 수 있습니다.
- 수학 및 상식 문제. 고급 기술을 갖추었음에도 불구하고, LLMs는 가끔 심지어 가장 간단한 수학적이거나 상식적인 문제를 해결하는 데 어려움을 겪을 수 있습니다.
- 프롬프트 해킹. 사용자에 의해 조작되거나 “해킹” 당할 수 있어 개발자의 지시를 무시하고 특정한 콘텐츠를 생성할 수 있습니다.

대부분의 프롬프트 엔지니어링 기술은 환각과 수학 및 상식적인 작업 해결 두 가지 문제에 대응합니다. 프롬프트 해킹을 완화하기 위한 특정 기술들이 있지만, 이는 별도로 논의할 주제입니다.

# 공통 규칙

구체적인 기술에 대해 논의하기 전에, 명확하고 구체적인 지침을 작성하는 데 도움이 되는 프롬프트의 공통 규칙에 대해 이야기해 봅시다:

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

제가 알기로는 표를 마크다운 형식으로 변경해 주세요.

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

- 단일 프롬프트 기술은 하나의 프롬프트에 대한 응답을 최적화하는 것을 목표로 합니다.
- 다음은 몇 가지 프롬프트를 결합하는 기술입니다. 이들은 공통적으로 작업을 해결하기 위해 모델(또는 모델)을 몇 번 쿼리하는 개념에 있습니다.
- 마지막으로, 다양한 대형 언어 모델과 외부 도구를 결합하는 방법론 그룹이 있습니다.

# 단일 프롬프트 기술

![프롬프트 기술](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_3.png)

어떤 기술들이 단일 프롬프트에서 작업을 해결하기 위해 사용되나요?

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

- 제로 샷,
- 퓨 샷,
- 체인 오브 소트,
- 프로그램 지원 언어.

하나씩 공부해 봅시다.

제로 샷 프롬프팅

자연어 지시를 사용하는 가장 간단한 기술입니다.

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

![Image 1](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_4.png)

Few-Shot Prompting

LLMs are extremely good at one-shot learning but they still may fail at complicated tasks. The idea of few-shot learning is to demonstrate to the model similar tasks with correct answers (Brown et al. (2020)).

![Image 2](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_5.png)

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

Min 등(2022) 논문에서는 데모의 레이블이 잘못되었을 때 분류 및 다중 선택 과제의 성능에 거의 영향을 미치지 않음을 보여줍니다. 대신, 데모가 레이블 공간의 몇 가지 예, 입력 테스트의 분포 및 순서의 전반적인 형식을 제공하는 것이 중요합니다.

Chain of Thought Prompting (CoT)

Chain-of-Thought 프롬프팅은 중간 추론 단계를 통해 복잡한 추론 능력을 활성화합니다. 이 기술은 모델이 각 단계를 반복하고 추론할 수 있도록 하는 것을 목표로 합니다.

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_6.png)

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

CoT은 제로샷 또는 퓨샷 러닝과 함께 사용됩니다. 제로샷 CoT의 아이디어는 모델이 해결책에 이르기 위해 단계별로 생각하도록 제안하는 것입니다. 이 접근법의 저자들(Kojima et al. (2022))은 산술, 기호 및 기타 논리 추론 작업에서 제로샷 LLM의 성능을 크게 능가하는 것을 입증했습니다.

퓨샷 CoT를 선택하는 경우, 설명이 포함된 다양한 예제를 갖도록 해야 합니다(Wei et al. (2022)). 이 접근법의 경험적인 이득은 산술, 상식 및 기호적 추론 작업에서 두드러집니다.

프로그램 지원 언어 모델 (PAL)

프로그램을 지원하는 언어 모델은 코드로 자연어 설명을 확장하는 것으로 Chain-of-Thought 프롬프팅을 확장하는 접근법입니다(Gao et al. (2022)).

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

![image](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_7.png)

The technique can be implemented using LangChain PALChain class.

# Multiple Prompt Techniques

The next group of prompts is based on different strategies of combining prompts of one or a few of models:

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

- 투표. 정확한 답변을 얻기 위해 투표를 적용하는 아이디어입니다. 기술: 자기일관성.
- 분할정복. 이 그룹의 프롬프트는 복잡한 작업을 몇 가지 프롬프트로 분할하는 것에 기반합니다. 기술: 방향성 자극, 지식 생성, 프롬프트 연결, 테이블 연결 및 Least-to-Most 프롬프팅.
- 자가평가. 이 접근 방식은 출력물이 지시 사항을 충족하는지 확인하는 단계를 프레임워크에 포함하는 것을 제안합니다. 기술: 반성, 사고의 나무.

자기일관성(SC)

자기일관성은 "복잡한 추론 문제는 일반적으로 해당 독특한 올바른 답변으로 이어지는 여러 가지 다른 사고 방식을 허용한다"는 직관에 기반합니다 (Wang et al. (2022)).

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_8.png)

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

같은 Chain-of-Thought 프롬프트를 여러 번 수행하여 다양한 추론 경로를 생성한 후 투표를 통해 가장 일관된 답변을 선택합니다.

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_9.png)

Wang et al. (2022)에서는 산술 및 상식 작업에 대한 자기 일관성 적용의 이득이 일반적인 벤치마크에서 4%~18%였습니다.

## Directional Stimulus Prompting (DSP)

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

다음 컨셉은 "Divide and Conquer"입니다. DSP에서는 두 단계가 있습니다: 자극(예: 키워드)을 생성하고 그들을 사용하여 응답의 품질을 개선합니다.

리 등은 2023년에 요약, 대화 응답 생성 및 연상 추론 작업을 위해 방향성 자극 프롬프팅을 제안했습니다. 이는 두 개의 모델로 구성됩니다:

- 조정 가능한 소형 정책 LM은 자극(힌트)을 생성하기 위해 훈련됩니다.
- 블랙 박스로 고정된 대형 LM은 질문과 이전 단계에서의 자극에 기반하여 요약을 생성하는 데 사용됩니다.

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_10.png)

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

정책 모델은 지도 학습을 통해 라벨이 지정된 데이터를 사용하여 최적화하고, LLM의 출력을 기반으로 오프라인 또는 온라인 보상을 받아 강화 학습을 통해 미세 조정할 수 있습니다.

생성된 지식 프롬프팅 (GK)

"분할 정복" 개념 하에 다음 프롬프팅 기술인 생성된 지식은 Liu 등(2022)에서 제안되었습니다. 이 아이디어는 별도의 프롬프트를 사용하여 먼저 지식을 생성한 다음 더 나은 응답을 얻기 위해 사용하는 것입니다.

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

생성된 지식 유도에는 두 단계가 포함됩니다:

- 지식 생성: 몇 가지 샷의 데모를 사용하여 언어 모델에서 관련 질문에 대한 지식을 생성합니다.
- 지식 통합: 각 지식 명세를 사용하여 두 번째 언어 모델을 활용하여 예측을 수행한 다음, 가장 높은 확신을 갖는 예측을 선택합니다.

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_12.png)

본 방법은 지식 통합에 대한 작업 특정 감독이나 구조화된 지식베이스에 대한 접근이 필요하지 않지만, 대규모, 최신 기술 모델의 성능을 개선시킵니다. 공감 추리 작업에서 더 나은 성과를 나타냅니다.

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

![Image 1](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_13.png)

Prompt Chaining

Prompt chaining is a simple yet powerful technique in which you should split your task into subproblems and prompt the model with them one by one.

![Image 2](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_14.png)

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

프롬프트 체이닝은 상세한 프롬프트로 인해 LLM이 처리하기 어려워하는 복잡한 작업을 수행하는 데 유용합니다. 또한 LLM 애플리케이션의 투명성을 높이고, 제어 가능성과 신뢰성을 증가시킬 수 있습니다.

![image](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_15.png)

최소에서 최대 프롬핑

최소에서 최대 프롬핑은 모델이 작업을 하위 문제로 나누는 방법을 결정해야 하는 단계를 추가하여 조금 더 진보합니다.

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

![Zhou et al. (2022) - Experimental Results](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_16.png)

Zhou et al. (2022)의 실험 결과에 따르면, least-to-most prompting은 symbol manipulation, compositional generalization 및 math reasoning과 관련된 작업에서 잘 수행되고 있습니다.

![Chain-of-Table Prompting](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_17.png)

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

최근 연구에서 (Wang et al. (2024))는 새로운 접근 방식을 제안했습니다. 여기서는 표 형식 데이터가 중간 생각의 대리인으로 추론 체인에서 명시적으로 사용됩니다.

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_18.png)

이 알고리즘에는 다음과 같은 두 단계의 사이클이 포함되어 있습니다:

- LLM이 입력 쿼리 및 이전 작업 이력 (작업 체인)을 기반으로 작업 풀에서 다음 운영을 샘플링하는 동적 계획,
- 인수 생성은 이전 단계에서 선택된 작업에 대해 인수를 생성하는 과정을 포함합니다 (예: 새로운 열 이름). 이를 위해 LLM을 사용하고 프로그래밍 언어를 적용하여 작업을 실행하고 해당 중간 테이블을 생성합니다.

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

![Image](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_19.png)

The next two approaches implement the concept of Self-Check — there’s a step in the framework that checks the solution. Example of Cgain-of-Table implementation can be found by the link.

Tree of Thoughts (ToT)

Tree of Thoughts generalizes over the Chain-of-Thought approach allowing the model to explore multiple reasoning steps and self-evaluate choices.

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

ToT 기법을 실행하려면 네 가지 질문에 대해 결정해야 합니다:

- 중간 과정을 사고 단계로 어떻게 분해할 것인가,
- 각 상태에서 잠재적인 생각을 어떻게 생성할 것인가,
- 상태를 어떻게 휴리스틱하게 평가할 것인가(상태 평가자 프롬프트 사용),
- 어떤 검색 알고리즘을 사용할 것인가 (Yao et el. (2023))

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_20.png)

입력 프롬프트는 문제를 해결하기 위한 중간 단계의 설명과 샘플된 생각 또는 그들의 생성 방법에 대한 지침이 포함되어야 합니다. 상태 평가자 프롬프트는 다음 단계에 대한 선택할 프롬프트에 대한 지침을 제공해야 합니다.

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

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_21.png)

Yao et al. (2023)의 실험에서는 ToT가 복잡한 계획이나 탐색을 필요로 하는 작업에 대해 성공을 보여주었습니다. LangChain에는 langchain_experimental.tot.base.ToTChain 클래스에 Tree-of-Thought 기술을 구현하고 있습니다.

반성

반성은 언어 에이전트를 언어적 피드백을 통해 강화하는 프레임워크입니다. 반성 에이전트는 작업 피드백 신호에 대해 언어적으로 반성한 후, 자신의 반성적 텍스트를 에피소딕 메모리 버퍼에 유지하여 후속 시행에서 더 나은 의사 결정을 유도합니다(Shinn et al. (2023)).

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

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_22.png)

Reflexion 프레임워크는 세 가지 독립적인 모델로 구성되어 있습니다:

- Actor: 상태 관측에 따라 텍스트와 액션을 생성하는 LLM 모델 (CoT와 ReAct 사용),
- Evaluator: Actor가 생성한 출력을 점수로 평가하는 LLM 모델,
- Self-Reflection: Actor의 자기 개선을 돕기 위해 언어적인 강화 신호를 생성하는 LLM 모델입니다.

![이미지](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_23.png)

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

Reflexion은 순차적인 의사 결정, 코딩, 언어 추론이 필요한 작업에서 잘 수행됩니다.

링크를 통해 구현 내용을 확인해보세요.

# 외부 도구를 활용한 LLM 프레임워크

이 섹션에서 두 가지 접근 방식, 즉 Retrieval Augmented Generation과 ReAct를 다룰 예정입니다.

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

# 검색 증진 생성 (RAG)

RAG는 정보 검색 구성 요소와 텍스트 생성 모델을 결합합니다.

- 검색. 검색 단계에서 시스템은 일반적으로 벡터 검색을 사용하여 질문에 답할 수 있는 관련 문서를 검색합니다.
- 생성. 다음으로, 관련 문서는 초기 질문과 함께 LLM에 컨텍스트로 전달됩니다 (Lewis et al. (2021)).

대부분의 경우 RAG-시퀀스 방식이 사용됩니다. 이는 k개의 문서를 검색하여 사용자 쿼리에 답변하는 모든 출력 토큰을 생성하는 것을 의미합니다.

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

마크다운 형식에서 테이블 태그를 변경하십시오.

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

ReAct

요 등(2022)은 ReAct라는 프레임워크를 소개했습니다. 이 프레임워크에서 LLMs는 추론 트레이스와 과제별 동작을 교차적으로 생성하는 데 사용됩니다. 추론 트레이스는 모델이 행동 계획을 유도, 추적, 업데이트하고 예외를 처리하는 데 도움을 주며, 동작은 외부 소스(예: 지식 베이스 또는 환경)와 상호 작용하고 추가 정보를 수집하는 데 도움을 줍니다.

<img src="/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_26.png" />

ReAct 프레임워크는 사용 가능한 도구(예: 검색 엔진, 계산기, SQL 에이전트) 중 하나를 선택하고 적용하여 결과를 분석하여 다음 동작을 결정할 수 있습니다.

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

<img src="/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_27.png" />

ReAct은 단순한 Wikipedia API와 상호 작용하여, 사고 체인 추론에서 환각 및 오류 전파의 일반적인 문제를 극복하며, 이러한 추론 흔적이 없는 기준선보다 해석 가능한 인간과 유사한 작업 해결 궤적을 생성합니다 (Yao et al. (2022)).

Langchain 도구를 사용한 ReAct 구현 예시를 확인해보세요.

# Prompt Tuning and Evaluation

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

프롬프트 엔지니어링 기술의 선택은 LLM의 응용 및 사용 가능한 리소스에 크게 의존합니다. 프롬프트를 실험해 본 적이 있다면, 대형 언어 모델은 인간이 생성한 프롬프트의 작은 변화에 민감하며 최적이 아니거나 주관적일 수 있다는 것을 알고 있을 것입니다.

어떤 프롬프팅 기술을 선택하든, 애플리케이션을 개발 중이라면 프롬프트 엔지니어링을 데이터 과학 프로세스로 생각하는 것이 매우 중요합니다. 즉, 테스트 세트를 생성하고 메트릭을 선택하며 프롬프트를 조정하고 그 영향을 테스트 세트에 대해 평가하는 것을 의미합니다.

![image](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_28.png)

프롬프트를 테스트하기 위한 메트릭은 응용 프로그램에 크게 의존할 것이지만, 여기에는 몇 가지 가이드라인이 있습니다(Data Science Summit 2023):

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

![image](/assets/img/2024-05-18-PromptEngineeringClassificationofTechniquesandPromptTuning_29.png)

- Faithfulness and relevancy:
  - how factually accurate the generated answer is,
  - how relevant the generated answer is to the question.

2. Retrieval — for RAG and ReAct pipelines mainly but can be applied to generated knowledge and directional stimulus prompting:

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

- 정밀도 — 검색된 문서가 얼마나 관련 있는지,
- 재현율 — 모든 관련 문서가 검색되었는지.

3. 내부적 사고:

- 에이전트 및 도구 선택 정확도 — ReAct의 경우,
- 도구 인수 추출 — 정확한 인수가 문맥에서 검색되고 올바르게 변환되었는지 — ReAct의 경우,
- 장기 대화에서 사실 기억 — ReAct의 경우,
- 올바른 논리적 단계 — ReAct 및 Chain-of-Thought 프롬프팅의 경우.

4. 비기능적:

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

- 답변의 스타일과 어조,
- 편향성의 부재,
- 준수 및 안전 점검,
- 빠른 삽입 테스트.

사용 사례에 따라 측정 항목을 선택하고 테스트 셋에서 당신의 프롬프트 변경이 테스트 응답의 품질을 저하시키지 않도록 영향을 추적하세요.

# 요약

모든 기술을 다 다루지는 못했다고 주장하지는 않습니다. 기술이 너무 많아 곧 누군가가 전체 교과서를 출판할 것이기 때문입니다. 그러나 만약 당신이 이것을 읽어오셨다면, 모든 기술의 개념들이 상당히 흔하고 직관적임을 알게 되었을 것입니다. 나는 좋은 프롬프트를 작성하는 모든 규칙을 작은 목록으로 요약할 수 있습니다:

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

앞서까지 함께 해주셔서 감사합니다! 앞으로도 즐겁게 프롬프팅하세요!
