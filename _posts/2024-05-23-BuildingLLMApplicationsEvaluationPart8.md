---
title: "LLM 애플리케이션 개발 평가 파트 8"
description: ""
coverImage: "/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_0.png"
date: 2024-05-23 17:47
ogImage:
  url: /assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_0.png
tag: Tech
originalTitle: "Building LLM Applications: Evaluation (Part 8)"
link: "https://medium.com/@vipra_singh/building-llm-applications-evaluation-part-8-fcfa2f22bd1c"
---

Learn Large Language Models (LLM) through the lens of a Retrieval Augmented Generation (RAG) Application.

# 이 시리즈의 게시물

- 소개
- 데이터 준비
- 문장 변형기
- 벡터 데이터베이스
- 검색 및 검색
- LLM
- 오픈 소스 RAG
- 평가 (이 게시물)
- LLM 제공
- 고급 RAG

# 목차

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

# 콘텐츠 요약

1. 개요
2. LLM 평가와 벤치마킹의 비교
3. LLM 벤치마킹
   - 언어 이해 및 QA 벤치마킹
     - TruthfulQA
     - MMLU (Massive Multitask Language Understanding)
     - DROP
   - 상식 및 추론 벤치마킹
     - ARC (AI2 Reasoning Challenge)
     - HellaSwag
     - BIG-Bench Hard (Imitation Game 벤치마크 이상)
     - WinoGrande
     - GSM8k
   - 코딩 벤치마킹
     - HumanEval
     - CodeXGLUE
   - 대화 및 챗봇 벤치마킹
     - Chatbot Arena (by LMSys)
     - MT Bench
     - Language Model Evaluation Harness (by EleutherAI)
     - Stanford HELM
     - PromptBench (by Microsoft)
4. LLM 벤치마크의 제한
5. LLM 평가 지표
6. 지표 점수 계산 방법
   - 통계 평가자
     - 음성 오류율 (WER)
     - 정확도 일치
     - 난해함
     - BLEU
     - ROUGE
     - METEOR
   - 모델 기반 평가자
     - 추론 점수
     - BLEURT
     - QA-QG
   - LLM-Evals
     - G-Eval
     - Prometheus
   - 통계 및 모델 기반 평가자 결합
     - BERTScore
     - MoverScore
     - GPTScore
     - SelfCheckGPT
     - QAG Score
7. LLM 기반 애플리케이션 평가
   - 평가 지표 선택
   - 평가 방법 평가!
   - 평가 세트 작성
8. LLM 평가 프레임워크
   - Deepeval
     - 충실성
     - 답변 관련성
     - 문맥 정확도
     - 문맥 호출
     - 문맥 관련성
   - 파인튜닝 지표
     - 환각
     - 유해성
     - 편견
   - Ragas
     - 충실성
     - 답변 관련성
     - 문맥 정확도
     - 문맥 관련성
     - 문맥 호출
9. 결론
10. 제작진

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_0.png)

이전 블로그에서 여러 개의 RAG 애플리케이션을 성공적으로 구축했습니다. 이젠 해당 애플리케이션을 평가하는 과정을 알아봅시다. 우리가 대규모 언어 모델로 생성된 결과의 신뢰성에 대해 살펴봅시다. 우리는 전통적인 머신 러닝, 딥 러닝 및 LLM의 차이점을 아래 표를 통해 살펴보겠습니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_1.png)

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

LLM의 등장으로 이전에는 불가능하다고 여겨졌던 문제들을 해결할 수 있는 장이 열렸습니다. 하지만 한 가지 의문이 남아있습니다. LLM 기반 애플리케이션을 효과적으로 평가하는 방법은 무엇일까요?

이 기사를 통해 LLM을 평가하는 방법과 최신 기술들, 사용 가능한 프레임워크, 그리고 LLM 기반 애플리케이션을 평가하는데 있는 어려움에 대해 알아보겠습니다.

# 1. 개요

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_2.png)

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

𝗟𝗹𝗠(𝗟𝗮𝗿𝗴𝗲 𝗟𝗮𝗻𝗴𝘂𝗮𝗴𝗲 𝗠𝗼𝗱𝗲𝗹)을 𝘂𝘀𝗲𝗿𝗳𝘂𝗹𝗹𝘆 𝗲𝘃𝗮𝗹𝘂𝗮𝘁𝗲 𝗵𝗮𝗿𝗱 𝗈𝗇 𝗈𝗂𝘃𝗂𝗇𝗀 𝖺 𝗅𝖺𝗋𝗀𝖾 𝗅𝖺𝗇𝗀𝗎𝖺𝗀𝖾 𝗆𝗈𝖽𝖾𝗅 𝖻𝗎𝗍 𝗇𝗈𝗍 𝗌𝗎𝗋𝖾 where to start? 𝖧𝖾𝗋𝖾’𝗌 𝗒𝗈𝗎𝗋 𝖾𝗌𝗌𝖾𝗇𝗍𝗂𝖺𝗅 𝗀𝗎𝗂𝖽𝖾! 🚀

Model selection is crucial, as it will impact the result of your project. 𝖳𝗁𝖾𝗇, 𝗇𝖾𝗑𝗍 𝗌𝗍𝖾𝗉 𝗂𝗌 𝗁𝗈𝗐 𝗍𝗈 𝖾𝗏𝖺𝗅𝗎𝖺𝗍𝖾 𝗍𝗁𝖺𝗍 𝗆𝗈𝖽𝖾𝗅. 𝖳𝗁𝖾𝗇, 𝗇𝖾𝗑𝗍 𝗌𝗍𝖾𝗉 𝗂𝗌 𝗁𝗈𝗐 𝗍𝗈 𝖾𝗏𝖺𝗅𝗎𝖺𝗍𝖾 𝗍𝗁𝖺𝗍 𝗆𝗈𝖽𝖾𝗅. 𝗆𝖺𝗇𝗒 𝗉𝗋𝖺𝖼𝗍𝗂𝗍𝗂𝗈𝗇𝖾𝗋𝗌 𝗂𝗇𝗂𝗍𝗂𝖺𝗅𝗅𝗒 𝗋𝖾𝗅𝗂𝖾𝗌 𝗈𝗇 𝗉𝗋𝗈𝗆𝗉𝗍 𝖾𝗇𝗀𝗂𝗇𝖾𝖾𝗋𝗂𝗇𝗀 𝖺𝗌 𝖺 𝗆𝖾𝗍𝗁𝗈𝖽 𝗍𝗈 𝖾𝗏𝖺𝗅𝗎𝖺𝗍𝖾 𝗍𝗁𝖾𝗂𝗋 𝗆𝗈𝖽𝖾𝗅 𝖼𝗁𝗈𝗂𝖼𝖾. 𝖡𝗎𝗍 𝖺 𝗆𝗈𝗋𝖾 𝖼𝗈𝗆𝗉𝗋𝖾𝗁𝖾𝗇𝗌𝗂𝗏𝖾 𝖾𝗏𝖺𝗅𝗎𝖺𝗍𝗂𝗈𝗇 𝗌𝗍𝗋𝖺𝗍𝖾𝗀𝗒 𝗂𝗌 𝗇𝖾𝖼𝖾𝗌𝗌𝖺𝗋𝗒.

Model evaluation is complex, involving various metrics that cater to different priorities — whether it’s prioritizing accuracy, cost-efficiency, or performance. 𝖳𝗁𝖾 𝖽𝗂𝗋𝖾𝖼𝗍𝗂𝗈𝗇 𝗒𝗈𝗎 𝖼𝗁𝗈𝗈𝗌𝖾 𝗌𝗁𝗈𝗎𝗅𝖽 𝖺𝗅𝗂𝗀𝗇 𝗐𝗂𝗍𝗁 𝗒𝗈𝗎𝗋 𝗌𝗉𝖾𝖼𝗂𝖿𝗂𝖼 𝗇𝖾𝖾𝖽𝗌, 𝖾𝗇𝗌𝗎𝗋𝗂𝗇𝗀 𝗍𝗁𝖺𝗍 𝗍𝗁𝖾 𝗆𝗈𝖽𝖾𝗅 𝖺𝗒𝗈𝗎 𝗌𝖾𝗅𝖾𝖼𝗍 𝗇𝗈𝗍 𝗈𝗇𝗅𝗒 𝖿𝗂𝗍𝗌 𝖻𝗎𝗍 𝖾𝗇𝗁𝖺𝗇𝖼𝖾𝗌 𝗒𝗈𝗎𝗋 𝗎𝗌𝖾 𝖼𝖺𝗌𝖾.

𝖧𝖾𝗋𝖾’𝗌 𝖺 𝗌𝗍𝗋𝖾𝖺𝗆𝗅𝗂𝗇𝖾𝖽 𝖺𝗉𝗉𝗋𝗈𝖺𝖼𝗁 𝗍𝗈 𝖾𝗏𝖺𝗅𝗎𝖺𝗍𝗂𝗇𝗀 𝗟𝗹𝗠𝗌 𝗐𝗁𝖾𝗇 𝗒𝗈𝗎’𝗋𝖾 𝖽𝖾𝖼𝗂𝖽𝗂𝗇𝗀 𝗈𝗇 𝗍𝗁𝖾 𝖻𝖾𝗌𝗍 𝖿𝗂𝗍 𝖿𝗈𝗋 𝗈𝗎𝗋 𝖺𝗉𝗉𝗅𝗂𝖼𝖺𝗍𝗂𝗈𝗇 (𝗆𝗈𝗋𝖾 𝖽𝖾𝗍𝖺𝗂𝗅𝗌 𝗂𝗇 𝗍𝗁𝖾 𝖺𝖻𝗈𝗏𝖾 𝖽𝗂𝖺𝗀𝗋𝖺𝗆):

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

1️⃣ **모델 평가 방법 선택하기?**
✅ **예**: 모델이 이산 출력을 제공하는 경우, 전통적인 정확도 지표를 사용하십시오. 그렇지 않은 경우, ROUGE와 같은 다른 유사성 지표를 고려하십시오.
❌ **아니요**: 2️⃣단계로 이동하세요.

2️⃣ **LLM 판단자 또는 평가자를 구현해야 합니까?**
✅ **예**: LLM 판단자 또는 평가자를 구현하십시오.
❌ **아니요**: 3️⃣단계로 이동하세요.

3️⃣ **텍스트 평가자 선택하기?**
✅ **예**: 텍스트 품질, 가독성 또는 불가해성과 같은 독립적인 측정 항목을 선택하십시오.
❌ **아니요**: 인간 평가가 가장 신뢰할 수 있는 피드백을 제공하지만 시간이 많이 소요되고 더 많은 비용이 듭니다. 하지만 인간 입력이 필요하기 때문에 가장 확실한 피드백을 제공합니다.

큰 언어 모델을 위한 평가 도구를 탐색 중이신가요? Amazon Bedrock가 좋은 선택일 수 있습니다. 여러분의 요구 사항에 맞는 다양한 유연한 옵션을 제공합니다:

🔹 **성능 평가**: 비용, 지연 및 사용 설정을 기반으로 비교하기
🔹 **프로그래밍 평가**: 사용 사례나 LLM을 반복하면서 프로그래밍 평가 사용하기
🔹 **사용자 테스트**: 첫번째 프로토타입 테스트를 시작하기 위해 인간 평가자들을 참여시키기

아래에서 이러한 기술들에 대해 자세하게 논의해보도록 합시다.

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

# 2. LLM Benchmarking 대 평가

LLM 벤치마킹과 평가는 얽혀 있지만, 그들의 목적 사이에는 섬세한 차이가 있습니다:

벤치마킹은 표준화된 테스트에 관한 것입니다. 특정 작업에서 LLM의 성능을 평가하기위해 미리 정의된 데이터 세트와 측정 항목을 사용하는 것을 의미합니다. 이는 언어 모델에 읽기, 쓰기 및 수학 (하지만 언어를 위한 것!) 과제 집합을 제공하는 것과 같습니다.

여기에 벤치마킹이 제공하는 것은:

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

비교: 벤치마킹은 연구자들이 다른 LLM의 성능을 동일한 작업에서 비교할 수 있게 합니다. 이는 어떤 모델이 특정 영역에서 뛰어나다는 것을 확인하는 데 도움이 됩니다.
측정 결과: 벤치마킹은 명확한 그림을 제공하는 수치적으로 측정된 점수들을 제공하여 LLM의 강점과 약점을 보여줍니다.

반면에, 평가는 좀 더 넓은 범위를 갖습니다. 이는 단순히 테스트를 실행하는 것을 넘어 LLM의 능력을 보다 포괄적으로 평가하는 것을 의미합니다. 여기서 연구자들은 다음을 고려합니다:

실제 세계 적용 가능성: LLM이 실제 사용 사례를 모방하는 상황에서 얼마나 잘 수행되는가?
공정성과 편견: LLM이 출력물에서 편견을 나타내는가?
해석 가능성: 연구자들은 LLM이 답변에 도달하는 방법을 이해할 수 있는가?

평가는 종종 벤치마킹이 마련한 기초 위에 구축됩니다. 연구자들은 벤치마크 점수를 시작점으로 사용할 수 있지만, 벤치마크가 반드시 잡아내지 못하는 측면들을 더 깊이 파고들 것입니다.

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

간단한 용어로 말하면, 벤치마킹은 표준화된 테스트를 사용하여 양적 평가를 제공하며, 평가는 LLM의 전체적인 장단점 및 현실 세계 적용에 대한 적합성에 대한 좀 더 질적인 이해를 제공합니다.

# 3. LLM 벤치마킹

![LLM 벤치마크](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_3.png)

LLM 벤치마크는 추론 및 이해력과 같은 다양한 기술에 대한 LLM의 성능을 평가하기 위해 설계된 일련의 표준화된 테스트이며, 이 능력을 측정하기 위해 특정 점수자나 지표를 활용합니다.

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

벤치마크에 따라 메트릭은 정확한 일치 비율과 같은 통계 기반 측정부터 다른 LLM들에 의해 평가되는 더 복잡한 메트릭까지 다양할 수 있습니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_4.png)

다양한 벤치마크는 모델 능력의 다양한 측면을 평가합니다. 이러한 측면에는 다음이 포함됩니다:

- 추론 및 상식: 이러한 벤치마크는 LLM이 논리를 적용하고 일상 지식을 활용하여 문제를 해결하는 능력을 평가합니다.
- 언어 이해 및 질문 답변(QA): 이는 모델이 텍스트를 해석하고 정확하게 질문에 대답하는 능력을 평가합니다.
- 코딩: 이 벤치마크는 LLM의 코드 해석 및 생성 능력을 평가합니다.
- 대화 및 챗봇: 이러한 벤치마크는 LLM의 대화에 참여하고 일관된 유의미한 응답을 제공하는 능력을 시험합니다.
- 번역: 이러한 벤치마크는 모델이 한 언어에서 다른 언어로 텍스트를 정확하게 번역하는 능력을 평가합니다.
- 수학: 이 벤치마크는 기본 산술부터 미적분과 같은 더 복잡한 수학 영역까지 모델이 수학 문제를 해결하는 능력에 중점을 둡니다.
- 논리: 논리 벤치마크는 모델이 귀납 및 추론과 같은 논리적 추론 기술을 적용하는 능력을 평가합니다.
- 표준화된 시험: SAT, ACT 또는 기타 교육 평가도 모델의 성능을 평가하고 벤치마크 하는 데 사용됩니다.

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

![LLM Benchmarking](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_5.png)

일부 벤치마크는 수십 개의 테스트만 포함할 수도 있고, 다른 것은 수백 개 또는 수천 개의 작업을 포함할 수도 있습니다. 중요한 점은 LLM 벤치마킹이 서로 다른 도메인과 작업에서 LLM 성능을 평가하기 위한 표준화된 프레임워크를 제공한다는 것입니다.

프로젝트에 적합한 적절한 벤치마크를 선택하는 것은 다음을 의미합니다:

- 목표에 부합: LLM이 뛰어날 필요가 있는 구체적인 작업과 일치하는지 확인하는 것.
- 작업 다양성 수용: 광범위한 작업 스펙트럼을 갖춘 벤치마크를 찾아 LLM을 다각도로 평가하는 것.
- 도메인에 부합: 언어 이해, 텍스트 생성 또는 코딩과 같이 애플리케이션의 세계와 조화를 이루는 벤치마크를 선택하는 것.

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

고등학생들을 위한 SAT 같은 것이 LLM에 대한 것이라고 생각해 보세요. 그들은 모델 능력의 모든 가능한 측면을 평가할 수는 없지만 확실히 가치 있는 통찰력을 제공합니다. 이제 Claude 3의 성능이 여러 벤치마크를 통해 다른 SOTA 모델들과 어떻게 비교되는지 알아보겠습니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_6.png)

다음 섹션에서는 4가지 가장 중요한 영역(언어 이해, 추론, 코딩, 대화) 전반에 걸친 주요 LLM 벤치마크를 논의할 것입니다. 이러한 벤치마크들은 산업 응용 프로그램에서 널리 활용되며 기술 보고서에서 자주 인용됩니다. 이들은 다음을 포함합니다:

## 3.1. 언어 이해 및 QA 벤치마크

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

## 3.1.1. 진실QA

[2022년 발행] ∙ 논문 ∙ 코드 ∙ 데이터셋

<img src="/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_7.png" />

목적

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

- 정확하고 진실한 답변을 제공하는 능력을 기반으로 모델을 평가합니다.
- 잘못된 정보를 대항하고 윤리적 AI 사용을 촉진하는 데 중요합니다.

데이터셋

- 원본 데이터셋은 38개 카테고리에 걸쳐 총 817개의 질문으로 구성되어 있습니다.
- 카테고리에는 건강, 법률, 금융, 정치 등이 포함됩니다.
- 질문은 사람들이 거짓된 믿음이나 오해로 인해 잘못된 대답을 제공할 수 있는 영역을 대상으로 합니다.

성과

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

- 원본 논문에서 성능이 가장 우수했던 모델인 GPT-3은 인간의 기준치인 94%에 비해 단 58%의 성공률을 달성했습니다.

점수 부여

- 최종 점수는 모델이 생성하는 진실한 출력의 비율에 기초하여 계산됩니다.
- "GPT-Judge"로 불리는 파인튜닝된 GPT-3이 답변의 진실성을 판단하는 데 사용됩니다.

## 3.1.2. MMLU (Massive Multitask Language Understanding)

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

[2021년에 발표됨] ∙ 논문 ∙ 코드 ∙ 데이터셋

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_8.png)

- 목표: 사전 훈련 지식을 기반으로 모델을 평가하며, 제로샷 및 퓨샷 설정에 중점을 둠.
- 벤치마크: 다양한 주제(과학, 인문학, 사회과학 등 57가지)의 객관식 문제를 통해 모델을 평가하는 포괄적인 벤치마크로, 초급부터 고급 단계까지의 난이도를 제시함.
- 지식 간격 식별: 다양하고 상세한 주제 범위로, 특정 영역의 모델 지식에 어떤 간격이 있는지 확인하는 데 이 벤치마크가 완벽합니다.
- 점수 매기기: MMLU는 대형 언어 모델(LLM)을 단순히 정답 비율을 기반으로 점수를 매김. 출력은 정확해야만 올바르다고 간주됨(위 예시의 ‘D’).

MMLU가 접근하기 어려워 보인다면, 좋은 소식이 있어요. DeepEval 내에서 몇 가지 주요 벤치마크가 구현되어 있어, 우리가 선택한 어떤 LLM도 몇 줄의 코드로 쉽게 벤치마킹할 수 있습니다.

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

먼저 DeepEval을 설치하세요:

```js
pip install deepeval
```

그리고 벤치마크를 실행하세요:

```js
from deepeval.benchmarks import MMLU
from deepeval.benchmarks.tasks import MMLUTask

# 특정 작업 및 샷을 가진 벤치마크 정의
benchmark = MMLU(
    tasks=[MMLUTask.HIGH_SCHOOL_COMPUTER_SCIENCE, MMLUTask.ASTRONOMY],
    n_shots=3
)

# 'mistral_7b'를 사용자 지정 모델로 바꿔주세요
benchmark.evaluate(model=mistral_7b)
print(benchmark.overall_score)
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

더 많은 구현 세부사항을 보려면 DeepEval MMLU 문서를 방문해주세요.

## 3.1.3. DROP

- 설명: DROP은 LLMs가 문단 내 이산적 추론을 수행해야 합니다. 이는 질문에서 참조 해결 및 덧셈, 계산, 또는 정렬과 같은 작업 수행을 포함하며 문단 내용에 대한 포괄적 이해가 필요합니다.
- 평가 설정: 3-shot
- 메트릭: 9536개의 문단 이해 질문에 대한 F1 점수
- 논문: DROP: 문단에 대한 이산적 추론이 필요한 독해 평가

열린 LLM 리더보드의 일곱 가지 주요 벤치마크 작업을 마무리합니다. 이 테스트들은 LLM의 지식 뿐만 아니라 추론, 이해 및 문제 해결 능력도 평가합니다.

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

마크다운 형식으로 변경:

Additional noteworthy language understanding and QA benchmarks: GLUE, SuperGLUE, SQuAD, and GPT tasks, CoQA, QuAC, TriviaQA

### 3.2. Common-sense and Reasoning Benchmarks

#### 3.2.1. ARC (AI2 Reasoning Challenge)

[Published in 2018] ∙ Paper ∙ Code

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

- 설명: AI2 Reasoning Challenge (ARC)는 초등학교 수준의 다항식 과학 문제들을 간단한 것부터 복잡한 것까지 포함하는 LLMs를 테스트합니다. 예를 들어, "광합성은 식물 성장을 돕기 위해 무엇을 생산합니까?"와 같은 질문이 있을 수 있으며 (a) 물 (b) 산소 (c) 단백질 (d) 설탕으로 선택지가 주어집니다.
- 평가 설정: 25번 시도
- 측정 항목: 3548개의 질문에 대한 정확도로, 그 중 33%가 도전적인 것으로 지정됩니다.
- 논문: 당신이 질문에 답했다고 생각하십니까? ARC, AI2 Reasoning Challenge를 시도해보세요.

데이터셋은 681MB이며 두 세트로 구분된 질문으로 구성되어 있습니다:

- ARC-Easy
- ARC-Challenge

예시 질문:

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

![Image](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_9.png)

질문이 있고, 여러 선택지와 정답이 있습니다.

## 3.2.2. HellaSwag

[2019년 발표] ∙ 논문 ∙ 코드 ∙ 데이터셋

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

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_10.png)

HellaSwag는 문장 완성을 통해 LLM 모델의 상식 추론 능력을 평가합니다. LLM 모델이 4개의 선택지 중에서 적절한 끝을 선택할 수 있는지를 테스트합니다. 이는 10,000개의 문장에 걸쳐 진행됩니다.

당시 SOTA 모델은 사전 훈련을 통해 50% 이상을 달성하기 어려웠지만, GPT-4는 2023년 10번의 프롬프팅만으로 95.3%의 기록을 세웠습니다. MMLU와 유사하게, HellaSwag는 LLM의 정확 답변 비율에 따라 점수를 매깁니다.

DeepEval을 통해 HellaSwag 벤치마크를 활용하는 방법은 다음과 같습니다:

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
from deepeval.benchmarks import HellaSwag
from deepeval.benchmarks.tasks import HellaSwagTask

# 특정 작업 및 샷으로 벤치마크 정의
benchmark = HellaSwag(
    tasks=[HellaSwagTask.TRIMMING_BRANCHES_OR_HEDGES, HellaSwagTask.BATON_TWIRLING],
    n_shots=5
)

# 'mistral_7b'를 사용자 지정 모델로 바꿔주세요
benchmark.evaluate(model=mistral_7b)
print(benchmark.overall_score)
```

더 많은 정보를 얻으려면 DeepEval의 HellaSwag 문서 페이지를 방문해보세요.

## 3.2.3. BIG-Bench Hard (Beyond the Imitation Game Benchmark)

[2022년 발표] 논문 ∙ 코드 ∙ 데이터셋

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

BIG-Bench Hard (BBH)은 원래 BIG-Bench 스위트에서 23가지 도전 과제를 선정했습니다. 이는 이미 언어 모델의 능력을 초과하는 204개의 평가 과제로 구성되어 있었습니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_11.png)

BIG-Bench가 발표된 시점에서 SOTA 언어 모델 중 한 가지도 이 23가지 과제 중 아무 것도 인간평가자의 평균을 넘지 못했습니다. 흥미로운 점은 BBH의 저자들이 동일한 LLM을 사용하여 이 23가지 과제 중 17가지에서 인간을 능가할 수 있었습니다. Chain-of-Thought (CoT) 프롬프팅을 사용했기 때문입니다.

BBH의 예상 출력은 다른 객관식 문제 기반 벤치마크보다 훨씬 다양하지만, 정확한 일치의 비율에 따라 모델을 평가합니다. CoT 프롬프팅은 모델의 출력을 예상된 형식으로 제한하는 데 도움을 줍니다.

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

BBH 벤치마크를 사용하려면:

```js
from deepeval.benchmarks import BigBenchHard
from deepeval.benchmarks.tasks import BigBenchHardTask

# 특정 작업과 샷을 가진 벤치마크 정의
benchmark = BigBenchHard(
    tasks=[BigBenchHardTask.BOOLEAN_EXPRESSIONS, BigBenchHardTask.CAUSAL_JUDGEMENT],
    n_shots=3,
    enable_cot=True
)

# 'mistral_7b'를 사용자 정의 모델로 교체
benchmark.evaluate(model=mistral_7b)
print(benchmark.overall_score)
```

다시 한번 DeepEval의 BBH 문서 페이지에서 더 많은 정보를 얻을 수 있습니다.

## 3.2.4. WinoGrande

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

- 설명: WinoGrande는 인공 지능의 상식적 추론을 테스트하며, 모델이 유인오그라드 스키마 도전 과제(WSC)를 해결하도록 도전합니다. 예를 들어, 한 가지 작업은 다음 문장을 완성하는 것을 포함할 수 있습니다: "문이 창문보다 더 큰 소리로 열렸습니다 왜냐하면 \_\_\_(옵션: 문 또는 창문)에 더 많은 기름이 있었기 때문입니다."
- 평가 설정: 5번 시도
- 측정 항목: 1267개의 질문에 대한 정확도
- 논문: WinoGrande: 규모의 있는 적대적 유인오그라드 스키마 도전 과제

## 3.2.5. GSM8k

- 설명: GSM8K는 LLMs의 다단계 수학적 추론을 테스트하기 위해 초등학교 수학 문제를 제시합니다. 예를 들어: "한 벌의 파란색 섬유가 2개의 두께와 그 절반만큼의 흰색 섬유가 있습니다. 총으로 몇 개의 두께이 필요합니까?" 정답은 3입니다.
- 평가 설정: 5번 시도
- 측정 항목: 1319개의 질문에 대한 정확도
- 논문: Training Verifiers to Solve Math Word Problems

추가 언급할 만한 상식적 추론 벤치마크: CommonsenseQA, COPA, SNLI, MultiNLI, RACE, ANLI, PIQA, COSMOS QA

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

# 3.3. 코딩 평가

## 3.3.1. HumanEval

[2021년 발표됨] 논문 ∙ 코드 ∙ 데이터셋

HumanEval은 모델의 코드 생성 능력을 평가하기 위해 설계된 164가지 독특한 프로그래밍 과제로 구성되어 있습니다. 이러한 과제는 알고리즘부터 프로그래밍 언어의 이해에 이르기까지 다양한 스펙트럼을 다룹니다.

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

아래는 HumanEval과 유사한 컬렉션에 있는 예제 작업과 모델이 생성한 솔루션입니다:

생성된 코드:

```python
def sum_list(numbers: List[float]) -> float:
    return sum(numbers)
```

HumanEval은 생성된 코드의 품질을 Pass@k Metric을 사용하여 평가합니다. 이 메트릭은 기본적인 텍스트 유사성뿐만 아니라 기능적 정확성을 강조하기 위해 설계되었습니다.

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

## 3.3.2. CodeXGLUE

[2021년에 발표됨] 논문 ∙ 코드 ∙ 데이터셋

![CodeXGLUE 이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_12.png)

CodeXGLUE는 Microsoft, Developer Division, Bing의 협력으로 개발되었으며 코드 완성, 코드 번역, 코드 요약 및 코드 검색과 같은 다양한 코딩 시나리오에서 모델을 직접 테스트하고 비교할 수 있는 10가지 다른 작업에 걸쳐 14개의 데이터셋을 제공합니다.

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

CodeXGLUE 평가 메트릭은 코딩 작업에 따라 정확일치부터 BLUE 점수까지 다양합니다.

추가로 주목할 만한 코딩 벤치마크: CodeBLEU, MBPP, Py150, MathQA, Spider, DeepFix, Clone Detection, CodeSearchNet

GenAI 모델은 다음 4개 데이터셋에서의 성능 평균으로 순위가 매겨집니다:

- ARC (25-shot)
- HellaSwag (10-shot)
- MMLU (5-shot)
- TruthfulQA (0-shot)

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

25번 슛은 데이터 세트에서 (질문, 해결책) 쌍을 25개 씩 프롬프트에 삽입하는 것을 의미합니다.

<img src="/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_13.png" />

# 3.4. 대화 및 챗봇 벤치마크

## 3.4.1. 챗봇 아레나 (by LMSys)

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

[2024년에 발표됨] 논문 ∙ 코드

챗봇 아레나는 20만 개 이상의 인간 투표를 사용하여 언어 모델 순위를 매기는 오픈 플랫폼입니다. 사용자들은 ChatGPT나 Claude와 같은 AI 모델 쌍을 익명으로 퀴즈를 내고 평가할 수 있습니다. 모델 신원을 알지 못한 채로 투표하며, 모델 신원이 숨겨져 있는 경우에만 랭킹을 위해 투표가 집계됩니다. 따라서 이는 모델을 객관적으로 점수를 매기는 메트릭을 사용하는 전통적인 기준이 아닙니다! 점수는 본질적으로 "추천 받은 횟수"입니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_14.png)

## 3.4.2. 기계 번역 벤치(markdown)

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

[2021년에 발행] 논문 ∙ 코드 ∙ 데이터셋

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_15.png)

MT-bench는 LLM(Large Language Model)을 심사위원으로 활용하여 채팅 어시스턴트의 품질을 다중 턴 개방형 질문 시리즈를 통해 평가합니다. 이 방법은 채팅 어시스턴트가 복잡한 상호작용을 다룰 수 있는 능력을 테스트합니다. MT-bench는 대화의 GPT-4 점수를 10점 척도로 평가하고 모든 턴의 평균 점수를 계산하여 최종 점수를 얻습니다.

이러한 모든 벤치마크는 특정 기술을 평가하는 데 매우 유용하지만, 기존의 벤치마크가 우리 프로젝트의 독특한 요구에 완벽하게 부합하지 않는다면 어떨까요?

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

상당히 주목할만 한 대화와 챗봇 벤치마크: DSTC, ConvAI, PersonaChat

### 3.4.3. 언어 모델 평가 하네스 (by EleutherAI)

언어 모델 평가 하네스는 LLMs를 다양한 평가 작업에 대해 벤치마크하는 통합 프레임워크를 제공합니다. 나는 '작업'이라는 단어를 강조했는데, 하네스에 시나리오라는 개념은 없습니다 (LM Evaluation Harness 대신에 Harness를 사용할 것입니다).

하네스 아래에서 우리는 여러 가지 작업을 볼 수 있습니다. 각 작업 또는 일련의 하위 작업은 생성 능력, 다양한 분야의 추론 등 LLM을 다른 영역에서 평가합니다.

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

각 작업 아래의 모든 하위 작업(때로는 작업 자체도 해당)에는 벤치마크 데이터셋이 있으며, 작업은 일반적으로 평가에 대한 중요한 연구들과 관련이 있습니다. Harness는 모든 이러한 데이터셋, 구성 및 평가 전략(예: 벤치마크 데이터셋을 평가하는 데 연결된 메트릭과 같은)을 통합하고 구조화하는 데 큰 노력을 기울입니다.

뿐만 아니라 Harness는 다양한 종류의 LLM 백엔드(예: VLLM, GGUF 등)를 지원합니다. 프롬프트 변경 및 실험을 통해 많은 맞춤 가능성을 제공합니다.

이것은 Misral 모델을 HellaSwag 작업(일반 상식 능력을 판단하는 작업)에서 쉽게 평가할 수 있는 작은 예제입니다:

```js
lm_eval --model hf \
    --model_args pretrained=mistralai/Mistral-7B-v0.1 \
    --tasks hellaswag \
    --device cuda:0 \
    --batch_size 8
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

LM 평가 하네스에 영감을 받아 BigCode 프로젝트의 BigCode 평가 하네스가 개발되었습니다. 이 프레임워크는 코드 생성 작업을 평가하기 위한 유사한 API 및 CLI 방법을 제공하려고 합니다. 코드 생성을 위한 평가는 매우 구체적인 주제이므로 다음 블로그에서 자세히 논의할 수 있도록 하겠습니다. 계속해서 주목해 주십시오!

## 3.4.4. 스탠포드 HELM

HELM 또는 언어 모델의 종합적 평가는 "시나리오"를 사용하여 LMs가 적용될 수 있는 곳을 개요화하고 벤치마킹 환경에서 LLMs가 무엇을 수행해야 하는지를 지정하는 "메트릭"을 사용합니다. 시나리오는 다음으로 구성된:

- 작업 (시나리오와 일치하는)
- 도메인 (텍스트의 장르가 무엇이고 누가 썼으며 언제 작성되었는지로 구성됨)
- 언어 (작업을 어떤 언어로 수행할지)

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

HELM은 그 후 사회적 관련성(예: 사용자 앞 애플리케이션에 대한 신뢰성을 고려한 시나리오), 범위(다국어 지원), 그리고 실행 가능성(즉, 모든 데이터포인트를 하나씩 실행하는 대신 계산된 최적의 중요한 일부 데이터포인트를 선택하여 평가하는 등)을 기반으로 시나리오와 메트릭의 하위 집합을 우선 순위를 정해보려고 노력합니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_16.png)

뿐만 아니라, 이 HELM은 LLM 성능에 대한 절대 신뢰성을 제공할 수 있는 정확도만으로 충분하지 않기 때문에 거의 모든 시나리오에 대해 7가지 메트릭(정확도, 보정, 견고성, 공정성, 편향, 유해성, 효율성)를 다루려고 노력합니다.

## 3.4.5. PromptBench (마이크로소프트)

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

![2024-05-23-BuildingLLMApplicationsEvaluationPart8_17](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_17.png)

PromptBench은 LLM을 벤치마킹하기 위한 또 다른 통합 라이브러리입니다. HELM과 Harness와 매우 유사하며, 다양한 LLM 프레임워크를 지원합니다 (예: Hugging Face, VLLM 등). 다른 프레임워크와 구별되는 점은 과제를 평가하는 것뿐만 아니라 다양한 Prompt Engineering 방법을 지원하며, LLM을 다양한 프롬프트 수준 적대 공격에서 평가한다는 것입니다. 또한 프로덕션 수준의 사용 사례를 더 쉽게 만드는 여러 평가 파이프라인을 구성할 수 있습니다.

# 4. LLM 벤치마크의 한계

벤치마크는 LLM의 능력을 평가하는 데 기본적이지만, 제한 사항도 있습니다:

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

- 도메인 관련성: 벤치마크는 종종 LLM이 적용되는 독특한 도메인과 맥락과 일치하지 않아 법률 분석이나 의학 해석과 같은 작업에 필요한 구체성이 부족하다. 이것은 LLM 성능을 폭넓은 전문화 응용 프로그램 범위에서 정확하게 평가하는 벤치마크를 작성하는 데 어려움을 강조한다.
- 수명 짧음: 벤치마크가 처음 출시되면 보통 모델이 인간 기준에 미치지 못하는 것으로 나타난다. 그러나 조금의 시간이 지나면, 예를 들어 1~3년 정도면, 고급 모델이 초기의 어려움을 쉽게 극복할 정도로 발전한다 (계속되는 한 예). 이러한 지표가 더 이상 도전이 되지 않을 때 새로운 유용한 벤치마크를 개발하는 것이 필요해진다.

그럼에도 불구하고 전멸과 절망만은 아닙니다. 합성 데이터 생성과 같은 혁신적인 방법을 통해 이러한 제약을 극복할 수 있습니다.

# 5. LLM 평가 지표

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_18.png)

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

LLM 평가 지표는 우리가 중요하게 생각하는 기준에 따라 LLM의 출력물을 평가합니다. 예를 들어, 만약 우리의 LLM 애플리케이션이 뉴스 기사들의 페이지를 요약하는 데 사용된다면, 우리는 원본 텍스트로부터 충분한 정보를 포함하는지, 그리고 어떠한 모순이나 환각이 포함되어 있는지를 기준으로 하는 LLM 평가 지표가 필요할 것입니다.

게다가, 만약 우리의 LLM 애플리케이션이 RAG 기반 아키텍처를 갖고 있다면, 아마도 검색 컨텍스트의 품질에 대한 점수도 필요할 것입니다. 포인트는, LLM 평가 지표가 LLM 애플리케이션을 그 애플리케이션이 수행할 작업에 기반하여 평가한다는 것입니다. (LLM 애플리케이션 자체가 LLM일 수도 있다는 점을 유의하세요!)

좋은 평가 지표는:

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

- 양적. 작업을 평가할 때 메트릭은 항상 점수를 계산해야 합니다. 이 접근 방식을 통해 우리는 LLM 애플리케이션이 "충분히 좋은지"를 결정하기 위한 최소 통과 임계값을 설정할 수 있습니다. 또한, 우리가 반복하고 구현을 개선함에 따라 이러한 점수가 어떻게 변하는지 모니터링할 수 있습니다.

- 신뢰할 수 있는. LLM 출력이 얼마나 예측할 수 없는지에 상관없이, LLM 평가 메트릭이 일관성 없는 것은 원치 않는 결과입니다. 따라서 LLM을 사용하여 평가된 메트릭 (일명 LLM-Evals)인 G-Eval과 같이 전통적인 점수 메서드보다 더 정확하지만, 종종 일관성이 없어서 대부분의 LLM-Evals가 부족한 부분이 있습니다.

- 정확한. 신뢰할 수 있는 점수는 우리의 LLM 애플리케이션의 성능을 정확하게 나타내지 않는다면 무의미합니다. 좋은 LLM 평가 메트릭을 훌륭하게 만드는 비밀은 가능한 한 인간의 기대에 부합하도록 만드는 것입니다.

그렇다면, LLM 평가 메트릭이 신뢰할 수 있고 정확한 점수를 어떻게 계산할 수 있을까요?

## 6. 메트릭 점수를 계산하는 여러 방법

메트릭 점수를 계산하는 데 사용할 수 있는 다양한 기본 방법이 있습니다. 일부는 신경망을 활용하며 임베딩 모델 및 LLM을 포함하고, 다른 몇 가지는 통계 분석에 전적으로 기반을 둔 것입니다.

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

<img src="/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_19.png" />

과제 종속 메트릭

과제 종속 메트릭은 LLM 애플리케이션의 특정 목표와 밀접하게 관련되어 있습니다. 예를 들어, 텍스트 분류는 정확도와 F1 점수를 우선시할 수 있으며, 텍스트 생성은 가독성이나 의미적 정확성과 같은 구체적인 목표에 따라 난해도, BLEU 또는 ROUGE 점수에 중점을 둘 수 있습니다. 이러한 메트릭은 LLM의 응용 프로그램의 원하는 결과와 얼마나 밀접하게 일치하는지를 기반으로 선택됩니다.

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

위 내용을 친근하게 번역하면 다음과 같습니다:

이 섹션을 마치면 각 방법을 살펴보고 최상의 방법에 대해 이야기할 거에요, 그러니 계속 읽어보세요!

### 6.1. 통계적 평가자

시작하기 전에, 통계적 평가 방법은 중요하지 않다는 점을 먼저 말씀 드리고 싶어요. 급하신 분들은 “G-Eval” 섹션으로 바로 건너뛸 수 있습니다. 이는 통계적 방법은 추론이 필요할 때 항상 성능이 좋지 않기 때문에 대부분의 LLM 평가 기준에 대해 점수화하는 데 너무 부정확합니다.

이제 함께 살펴보겠습니다:

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

## 6.1.1. 단어 오류율 (WER)

WER은 후보 단어를 기준 문자열로 변환하는 데 필요한 삽입, 삭제, 대체 및 필요에 따라 전치의 수인 편집 거리를 측정하는 WER 기반 메트릭의 집합입니다.

## 6.1.2. 정확도 일치

생성된 텍스트를 참조 텍스트와 일치시켜 후보 텍스트의 정확도를 측정합니다. 참조 텍스트와의 차이는 오답으로 처리됩니다. 이것은 추출 및 짧은 형식의 답변에 적합하며 참조 텍스트와의 최소 또는 전혀 차이가 없을 것으로 예상될 때만 사용됩니다.

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

## 6.1.3. Perplexity

헷갈리기(perplexity, PPL)는 언어 모델을 평가하는 가장 일반적인 지표 중 하나입니다. 이에 대해 자세히 살펴보기 전에 주의해야 할 점은 이 지표가 전형적인 언어 모델(가끔은 자기 회귀적 또는 인과 언어 모델이라고도 함)에 특히 적용되며, BERT와 같은 가려진 언어 모델에는 잘 정의되지 않는다는 것입니다(모델 요약 참조).

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_21.png)

이는 데이터와 모델 예측 간의 교차 엔트로피의 지수화와 동등합니다. 헷갈리기와 Bit Per Character (BPC) 및 데이터 압축과의 관계에 대한 추가 직관을 위해 The Gradient 블로그의 이 멋진 글을 확인해보세요.

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

Colab Notebook 지원: Perplexity.ipynb

## 6.1.4. BLEU

BLEU(BiLingual Evaluation Understudy) 점수는 기계 번역 품질을 평가하는 데 널리 사용되는 측정 항목입니다. 참조 번역(Reference)에 대한 기계 번역 텍스트(후보)를 평가합니다. IBM 연구원들이 개발한 BLEU는 기계 생성 텍스트와 높은 품질의 참조 번역 사이의 n-그램 오버랩을 측정하여 번역 정확도를 평가합니다. BLEU는 주로 정밀도에 중점을 둡니다. BLEU는 간단하고 효과적이어서 기계 번역 분야의 표준 벤치마크로 널리 알려져 있습니다. 그러나 BLEU는 주로 표면적인 어휘적 유사성을 평가하며 언어의 깊이 있는 의미론적 및 문맥적 세부 사항을 종종 간과합니다.

후보(Candidate): 이것은 우리의 번역 시스템에서 나온 출력물로, 평가하려는 것입니다.

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

참고: 이들은 일반적으로 인간에 의해 수행되는 고품질의 번역이며, 우리는 후보 텍스트와 비교합니다. 강건성을 위해 여러 개의 참조 번역이 있을 수 있습니다.

계산

후보 및 참조 번역을 단어(토큰)로 분할합니다. 토큰화는 두 세트의 텍스트에 대해 일관되어야 합니다.

n-그램 정밀도(P)를 계산합니다.

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

각 n-그램 길이에 대해 (일반적으로 1에서 4까지):

- 후보에서 참조되는 n-그램의 수를 세어서 참조에서 후보에 모두 나타나는 공통 n-그램의 수를 셉니다.
- 이 숫자를 후보 번역의 총 n-그램 수로 나누어 각 n-그램 길이에 대한 정밀도를 얻습니다.

위반 벌칙 (BP)

- 후보 번역이 참조 번역보다 짧은 경우, 우리는 지나치게 짧은 번역을 선호하지 않도록 벌칙을 부여해야 합니다.
- BP 공식: BP = exp(1−r/c) if c ` r, else BP = 1
- 여기서 c는 후보 번역의 길이이고 r은 유효한 참조 길이입니다.

BLEU 점수

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

- BLEU 스코어는 n-gram 정밀도의 기하 평균을 사용하여 계산되며 간결성 패널티로 곱해집니다.
- BLEU 스코어 = BP * exp((1/n)*∑log⁡(pi)), 여기서 n은 1부터 4까지 (n-grams)
- 여기서 pi는 n-gram에 대한 정밀도입니다.

BLEU 스코어의 범위: 보통 0부터 1까지이며, 0은 번역된 텍스트와 참조 번역 간에 오버랩이 없음을 나타내며 가장 낮은 점수를 나타내어 매우 나쁜 번역 품질을 시사합니다. 1은 참조 번역과 완벽하게 일치한다는 것을 나타내며 가장 높은 점수를 나타내어 이상적인 번역 품질을 시사합니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_22.png)

```js
from nltk.translate.bleu_score import sentence_bleu

# 샘플 참조 및 생성된 문장
reference = [["A", "fast", "brown", "fox", "jumps", "over", "a",
"lazy", "dog", "."]]
generated = [["The", "quick", "brown", "fox", "jumps", "over", "the",
"lazy", "dog", "."]]

# BLEU 스코어 계산
bleu_score = sentence_bleu(reference, generated)
print('BLEU 스코어:', bleu_score)
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

## 6.1.5. ROUGE

ROUGE (Recall-Oriented Understudy for Gisting Evaluation)은 자동 요약 및 기계 번역을 평가하는 데 사용되는 측정 항목입니다. 이는 자동 생성된 요약 또는 번역을 일반적으로 사람이 쓴 참조 요약과 비교합니다. ROUGE는 모델이 생성한 텍스트와 참조 텍스트 간의 n-그램, 단어 순서 및 단어 쌍과 같은 중첩 단위 수를 셈으로써 요약의 품질을 측정합니다. ROUGE를 사용하는 가장 흔한 변형은 다음과 같습니다:

- **ROUGE-N**: n-그램(단어 구)에 중점을 둡니다. ROUGE-1 및 ROUGE-2(각각 단일어와 이중어)가 가장 일반적입니다.

- **ROUGE-L**: Longest Common Subsequence(LCS)를 기반으로 하는데요, 이는 문장 수준 구조 유사성을 자연스럽게 고려하고 가장 긴 순차적 n-그램을 자동으로 식별합니다.

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

일반적으로 ROUGE는 세 가지 메트릭을 보고합니다.

정밀도(Precision): 모델이 생성한 요약에서 레퍼런스 요약에도 있는 n-gram의 비율을 의미합니다.

재현율(Recall): 레퍼런스 요약에 있는 n-gram 중 모델이 생성한 요약에도 있는 것의 비율을 의미합니다.

F-점수(F1 점수): 정밀도와 재현율의 조화평균으로, 두 가지를 균형있게 고려합니다.

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

ROUGE 점수는 0부터 1까지의 범위를 가지며, 0은 기계 생성 텍스트와 참조 텍스트 사이에 중첩이 없음을 나타내며, 1은 참조 텍스트와 완벽히 일치함을 나타냅니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_23.png)

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_24.png)

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_25.png)

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
from nltk.translate.bleu_score import corpus_bleu

# 샘플 참조 및 생성된 요약
reference_summaries = [['빠른 갈색 여우가 게으른 개를 뛰어넘는다.']]
generated_summaries = [['빠른 갈색 여우가 게으른 개를 뛰어넘는다.']]

# BLEU 점수 계산
bleu_score = corpus_bleu(reference_summaries, generated_summaries)
print('BLEU 점수:', bleu_score)
```

## 6.1.6. METEOR

METEOR(Metric for Evaluation of Translation with Explicit Ordering)은 기계 번역을 평가하는 고급 메트릭으로, BLEU 점수의 일부 한계를 해소하기 위해 개발되었습니다. BLEU와 달리 METEOR은 정확한 단어 일치 뿐만 아니라 어간 및 동의어를 고려하여 번역을 평가하므로 보다 광범위한 언어 유사성을 포착합니다. 정밀도와 재현율을 균형 있게 평가하며, 단어 순서의 차이에 대한 벌점을 도입하여 번역의 유창성을 평가합니다. METEOR은 문장 수준에서 인간 판단과 더 높은 상관관계를 갖는 것으로 알려져 있어 번역 품질 평가에 있어 소수점 아래까지 세밀하고 포괄적인 메트릭스로서의 지위를 갖고 있습니다. 그러나 그 복잡성으로 인해, BLEU와 같은 단순한 메트릭스에 비해 더 많은 계산 노력이 필요합니다.

Alignment-Based: METEOR은 후보 번역과 참조 번역 간의 단어들 간의 일치를 생성하여, 정확, 어간, 동의어 및 패러프레이즈 일치에 초점을 맞춥니다.

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

Recall과 Precision: BLEU가 precision만을 고려하는 반면, METEOR은 precision과 recall을 모두 고려합니다. 이 이중 접근 방식은 평가를 균형있게 돕습니다.

조화평균: METEOR은 recall과 precision의 조화평균을 사용하며, recall에 높은 가중치를 둡니다 (recall에 더 많은 중요성을 부여하기 위한 조정된 조화평균). 이는 BLEU와는 다르게 수정된 형태의 precision을 사용합니다.

단어 순서 차이에 대한 패널티: METEOR은 잘못된 단어 순서에 대한 패널티를 포함하고 있어 번역의 순조성에 민감합니다.

언어 독립적: 처음에는 영어로 개발되었지만, METEOR은 언어별 매개변수와 자원을 지원하기 위해 여러 언어로 확장되었습니다.

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

일치하는 항목 계산

후보 번역에서 정확히 일치하는 단어 수를 세세요.

정밀도와 재현율 계산

- 정밀도 (P): 후보 번역에 나타나는 단어의 비율을 의미합니다.
- 재현율 (R): 참조 번역에 나타나는 단어의 비율을 의미합니다.

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

# Precision과 Recall의 조화평균을 계산하세요

Harmonic Mean은 다음과 같이 계산됩니다: Harmonic Mean=10*P*R/(R+9\*P). 이는 Precision보다 Recall에 더 많은 가중치를 둡니다.

단어 순서에 대한 패널티

단어 순서의 차이에 대한 패널티가 부가됩니다. 이 패널티는 다음과 같이 계산됩니다: 패널티=0.5⋅(# of chunks/# of matches)\*\*3; 여기서 "chunk"는 후보 단어에서 참조와 동일한 순서로 이웃하는 단어의 집합을 의미합니다.

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

최종 METEOR 점수

최종 점수는 다음과 같이 계산됩니다: Score=(1−Penalty)\*F-mean

- Levenshtein 거리(또는 편집 거리) 스코어는 단어나 텍스트 문자열을 다른 것으로 변경하기 위해 필요한 단일 문자 편집(삽입, 삭제 또는 대체)의 최소 수를 계산합니다. 이는 철자 교정 또는 문자의 정확한 정렬이 중요한 작업을 평가하는 데 유용할 수 있습니다.

순수한 통계 스코어러는 매우 제한된 추론 능력을 가지고 있기 때문에 의미론을 고려하지 않으며 종종 긴 및 복잡한 LLM 출력을 평가하기에 정확도가 충분하지 않습니다.

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

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_26.png)

```python
from meteor import meteor_score

# 참조 및 생성된 문장 예시
reference_sentence = 'A fast brown fox jumps over a lazy dog.'
generated_sentence = 'The quick brown fox jumps over the lazy dog.'

# METEOR 점수 계산
meteor_score = meteor_score.meteor_score([reference_sentence], generated_sentence)
print('METEOR Score:', meteor_score)
```

## 6.2. 모델 기반 점수 산정기

순전히 통계적인 산정기들은 신뢰성이 있지만 의미론을 고려하는 데 어려움을 겪어 정확하지 않다. 이 절에서는 정반대인 산정기들을 살펴보겠습니다. 순전히 NLP 모델에 의존하는 산정기들은 비교적 더 정확하지만 확률적인 성격으로 인해 신뢰성이 떨어질 수 있습니다.

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

이것은 놀라운 사실이 아닐 것입니다만, LLM 기반이 아닌 스코어러들은 통계 스코어러에 언급된 이유로 인해 LLM-Evals보다 성능이 떨어집니다. LLM이 아닌 스코어러에는 다음이 포함됩니다:

## 6.2.1. Entailment score

연역 점수: 이 방법은 언어 모델의 자연어 추론 능력을 활용하여 NLG를 판단합니다. 이 방법에는 다양한 변형이 있지만, 기본 개념은 NLI 모델을 사용하여 생성물에 대한 연역 점수를 참조 텍스트와 비교하여 산출하는 것입니다. 이 방법은 텍스트 요약과 같은 텍스트 중심 생성 작업에서 충실성을 보장하는 데 매우 유용할 수 있습니다.

![image](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_27.png)

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

## 6.2.2. BLEURT

BLEURT (Bilingual Evaluation Understudy with Representations from Transformers)은 문장 간의 비트라이빌 의미 유사성을 포착할 수 있는 새로운 머신 러닝 기반의 자동 측정 항목입니다. 이 메트릭은 WMT Metrics Shared Task 데이터셋(공공 컬렉션의 평가로 학습) 및 사용자가 제공한 추가 평가를 기반으로 학습됩니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_28.png)

기계 학습을 기반으로 한 메트릭을 생성하는 것은 기본적인 도전이 됩니다. 메트릭은 다양한 작업 및 도메인에서 일관된 성능을 보이고 시간이 지남에 따라 잘 동작해야 합니다. 그러나 훈련 데이터의 양은 제한적입니다. 실제로 공개 데이터는 희소합니다 — 작성 시점에서 가장 큰 인간 평가 컬렉션인 WMT Metrics Task 데이터셋은 뉴스 도메인에 대해서만 약 26만 개의 인간 평가를 포함합니다. 이는 미래의 NLG 시스템을 평가하기에 적합한 메트릭을 훈련시키기에는 너무 한정적합니다.

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

이 문제를 해결하기 위해 전이학습을 사용합니다. 첫째로, 우리는 BERT의 문맥 단어 표현을 사용합니다. 이는 언어 이해를 위한 최첨단 비지도 표현 학습 방법으로 이미 NLG 측정 항목에 성공적으로 통합되었습니다(YiSi나 BERTscore 등).

둘째로, BLEURT의 견고성을 높이기 위한 혁신적인 사전 훈련 방식을 소개합니다. 실험 결과, 공개적으로 제공된 인간 평가에 직접 회귀 모델을 훈련하는 것은 역동적인 방법입니다. 왜냐하면 어느 도메인에서, 어느 시기에 메트릭이 사용될지 우리가 제어할 수 없기 때문입니다. 도메인 드리프트가 존재할 때 정확도가 떨어지는 경향이 있습니다. 이는 텍스트가 훈련용 문장 쌍과 다른 도메인에서 가져온 경우에 해당됩니다. 품질 드리프트가 발생할 때, 훈련 중에 사용된 것보다 예측해야 하는 등급이 더 높은 경우에도 정확도가 떨어질 수 있습니다. 이는 일반적으로 ML 연구가 진전되고 있음을 나타내는 좋은 소식이 될 수 있는 특징입니다.

BLEURT의 성공은 수백만 개의 합성 문장 쌍을 사용해 모델을 "워밍업"한 뒤 인간 평가를 세밀하게 조정하는 데에 달려 있습니다. 우리는 위키피디아 문장에 무작위로 변형을 가해 훈련 데이터를 생성했습니다. 인간 평가를 수집하는 대신, BLU를 포함한 문헌에서 제시된 메트릭과 모델의 모음을 사용하여 훈련 예제의 수를 비용을 매우 낮출 수 있게 했습니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_29.png)

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

## 6.2.3. QA-QG

질문 응답 — 질문 생성 (QA-QG) (Honovich et. al): 이 패러다임은 후보의 일관성을 참조 텍스트와 비교하기 위해 사용할 수 있습니다. 이 방법은 먼저 후보 텍스트에서 (답 후보, 질문) 쌍을 형성한 다음, 참조 텍스트에서 주어진 동일한 질문 세트에 대해 생성된 답변을 비교하고 확인하여 작동합니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_30.png)

불일치 점수 외에도, 이러한 접근 방식에는 몇 가지 단점이 있습니다. 예를 들어, NLI 스코어러는 긴 텍스트를 처리할 때 정확도에 어려움을 겪을 수도 있고, BLEURT는 학습 데이터의 품질과 대표성에 제한이 있을 수 있습니다.

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

그러니 우리 LLM-Evals에 대해 이야기해 보겠습니다.

# 6.3. LLM-Evals

## 6.3.1. G-Eval

G-Eval은 최근 개발된 프레임워크인 "GPT-4를 사용한 NLG 평가 및 인간 정렬 강화"라는 논문에서 사용된 LLMs를 평가하는 LLM 출력을 사용하는 프레임워크입니다.

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

<img src="/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_31.png" />

G-Eval은 평가 단계를 생성하기 위해 사고 연쇄(Chain of Thoughts, CoTs)를 사용한 다음, 이러한 단계를 사용하여 최종 점수를 결정하는 형식 채우기 패러다임을 통해 점수를 산출합니다 (이는 G-Eval이 작동하기 위해 여러 정보 조각이 필요하다는 멋진 표현입니다). 예를 들어, G-Eval을 사용하여 LLM 출력 일관성을 평가하는 경우, 평가 단계를 생성하기 위해 평가 기준과 평가할 텍스트를 포함하는 프롬프트를 작성한 후, 이러한 단계에 기반하여 1에서 5까지의 점수를 LLM을 사용하여 산출합니다.

이 예를 통해 G-Eval 알고리즘을 사용해봅시다. 먼저, 평가 단계를 생성하기 위해:

- 원하는 LLM에 평가 작업 소개(예: 일관성에 따라 1-5로 이 출력을 평가)
- 기준 정의(예: "일관성 - 실제 출력의 모든 문장의 집합적 품질")

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

(원본 G-Eval 논문에서는 저자들이 실험에 GPT-3.5 및 GPT-4만 사용했으며, 저는 다양한 LLMs를 활용하여 G-Eval에 대해 직접 실험해본 결과, 이러한 모델을 사용하는 것을 강력히 권장드립니다.)

평가 단계를 생성한 후:

- 평가 단계와 평가 단계에 나열된 모든 함께 연결하여 프롬프트를 만듭니다 (예: LLM 출력의 논리 평가를 평가한다면, LLM 출력이 필요한 인수가 될 것입니다).
- 프롬프트 끝에 1에서 5 사이의 점수를 생성하도록 요청합니다. 5가 1보다 우수하다는 것을 의미합니다.
- (선택사항) LLM에서 생성된 토큰의 확률을 가져와 점수를 정규화하고 가중 합산하여 최종 결과를 취합합니다.

3단계는 옵션입니다. 왜냐하면 출력 토큰의 확률을 얻기 위해 LLM의 원시 모델 임베딩에 액세스해야 하며, 2024년 기준으로 이는 아직 OpenAI API를 통해 사용할 수 없습니다. 그러나 이 단계는 LLM 점수에 미세 조정된 점수를 제공하며 LLM 점수의 편향을 최소화하기 때문에 도입된 것입니다 (논문에서 언급된 바와 같이, 1-5 척도에 대한 3은 높은 토큰 확률을 가지고 있다고 알려져 있습니다).

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

이 논문에서 언급된 모든 전통적이고 비-LLM 평가 방법을 능가하는 G-Eval의 성능을 보여주는 결과입니다:

![그림](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_32.png)

G-Eval은 LLM-Eval로써 LLM 출력물의 전체 의미를 고려할 수 있기 때문에 훨씬 더 정확합니다. 그리고 이것은 매우 타당한 주장입니다. LLM보다 능력이 훨씬 떨어지는 평가자를 사용하는 비-LLM 평가 방법이 LLM이 생성한 텍스트의 전체 범위를 어떻게 이해할 수 있을까요?

G-Eval은 동료들과 비교했을 때 인간 판단과 훨씬 더 관련이 있습니다. 그러나 LLM에 평가 점수를 매기도록 요청하는 것은 명백히 임의적이기 때문에 여전히 신뢰성이 떨어질 수 있습니다.

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

그렇다는 것은, G-Eval의 평가 기준이 얼마나 유연한지 고려할 때, 저는 개인적으로 DeepEval에 G-Eval을 적용하여 작업 중인 오픈 소스 LLM 평가 프레임워크로 구현했습니다.

```js
# 설치
pip install deepeval
# OpenAI API 키를 환경 변수로 설정
export OPENAI_API_KEY="..."
```

```js
from deepeval.test_case import LLMTestCase, LLMTestCaseParams
from deepeval.metrics import GEval

test_case = LLMTestCase(input="LLM에 입력하는 내용", actual_output="LLM의 출력 내용")
coherence_metric = GEval(
    name="일관성",
    criteria="일관성 - 실제 출력의 모든 문장의 품질을 종합한 것",
    evaluation_params=[LLMTestCaseParams.ACTUAL_OUTPUT],
)

coherence_metric.measure(test_case)
print(coherence_metric.score)
print(coherence_metric.reason)
```

또 다른 주요 장점은 LLM-Eval을 사용하면, LLM은 평가 점수에 대한 이유를 생성할 수 있다는 것입니다.

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

## 6.3.2. 프로메테우스

프로메테우스는 GPT-4의 평가 능력을 제공할 때 적절한 참조 자료(참조 답변, 점수 기준)가 제공된다면 비교할 수 있는 완전한 오픈 소스 LLM입니다. 그것은 G-Eval과 유사하게 케이스에 대해 무관합니다. 프로메테우스는 Llama-2-Chat을 기본 모델로 사용하고 100만 건의 피드백(생성된 GPT-4에 의해)을 피드백 컬렉션 내에서 섬세하게 조정했습니다.

프로메테우스 연구 논문에서 간략한 결과는 다음과 같습니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_33.png)

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

프로메테우스는 G-Eval과 같은 원칙을 따르지만, 몇 가지 차이점이 있습니다:

- G-Eval은 GPT-3.5/4를 사용하는 프레임워크이지만, 프로메테우스는 평가를 위해 세밀하게 조정된 LLM입니다.
- G-Eval은 CoT를 통해 점수 평가 및 평가 단계를 생성하지만, 프로메테우스의 점수 평가 기준은 프롬프트에 제공됩니다.
- 프로메테우스는 참고/예시 평가 결과가 필요합니다.

개인적으로는 아직 시도해 보지 않았지만, 프로메테우스는 허깅페이스에서 이용할 수 있습니다. 프로메테우스를 구현해보지 않은 이유는, 프로메테우스가 OpenAI의 GPT와 같은 독점 모델에 의존하는 대신 평가를 오픈소스로 만드는 데 중점을 두었기 때문입니다. 최고의 LLM-Evals를 구축하려는 누군가에게는 좋은 선택이 아니었습니다.

# 6.4. 통계 및 모델 기반 스코어 결합

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

지금까지 우리는 통계적 방법이 신뢰할만 하지만 정확하지 않다는 것과, 비-LLM 모델 기반 접근 방법이 믿을만 하지 않지만 더 정확하다는 것을 보았습니다. 이전 섹션과 유사하게, BERTScore와 같은 비-LLM 점수 산정 방법이 있습니다:

## 6.4.1. BERTScore

BERTScore (Zhang et al. 2019): 이는 bi-encoding 기반 접근 방법으로, 후보 텍스트와 참조 텍스트가 DL 모델에 따로 입력되어 임베딩을 얻습니다. 토큰 수준의 임베딩은 이후에 쌍별 코사인 유사도 행렬을 계산하는 데 사용됩니다. 그런 다음, 참조에서 후보에 가장 유사한 토큰의 유사도 점수를 선택하여 정밀도, 재현율 및 F1 점수를 계산합니다.

![그림](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_34.png)

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

## 6.4.2. MoverScore

MoverScore (Zhao et al. 2019): 워드 무버스 디스턴스 개념을 사용하여, 임베디드된 단어 벡터 간의 거리가 어느 정도 의미론적으로 유의미하다고 제안합니다. ( vector(king) — vector(queen) = vector(man) ) 그리고 n-그램 간의 유클리드 유사도를 계산하기 위해 문맥 임베딩을 사용합니다. 단어 간 일대일 단어 경기를 허용하는 BERTscore와 달리, MoverScore는 소프트/일부 정렬을 사용하기 때문에 다 대 일 매핑을 허용합니다.

![image](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_35.png)

BERTScore와 MoverScore 점수기 모두 BERT와 같은 사전 훈련된 모델로부터의 문맥 임베딩에 의존하여 문맥적인 인식과 편향에 취약합니다. 그렇다면 LLM-Evals는 어떨까요?

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

## 6.4.3. GPTScore

G-Eval이 직접 작업을 평가하는 방식인 것과는 달리, GPTScore는 대상 텍스트를 생성하는 조건부 확률을 사용하여 평가 지표로 사용합니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_36.png)

## 6.4.4. SelfCheckGPT

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

SelfCheckGPT은 조금 독특해요. 이것은 LLM 출력물을 사실 확인하기 위해 사용되는 간단한 샘플링 기반 접근법입니다. 이는 상상된 출력물은 재현할 수 없다고 가정하며, LLM이 특정 개념을 알고 있다면 샘플링된 응답은 유사할 것이며 일관된 사실을 포함할 가능성이 높다고 가정합니다.

SelfCheckGPT은 환각을 감지하는 데 적합성을 확인할 때 레퍼런스 없이 처리하는 흥미로운 방법입니다. 이것은 생산 환경에서 굉장히 유용합니다.

![](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_37.png)

그러나 G-Eval 및 프로메테우스가 use case에 중립적인 것을 알 수 있습니다. SelfCheckGPT는 그렇지 않습니다. 이것은 환각을 감지하기 위해 적합하며, 요약, 일관성 등 다른 use case를 평가하는 데는 적합하지 않습니다.

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

## 6.4.5. QAG 점수

QAG(Question Answer Generation) 점수는 LLMs의 뛰어난 추론 능력을 활용하여 신뢰성 있는 LLM 출력을 평가하는 점수 계산기입니다. 답변(일반적으로 '예' 또는 '아니오')을 사용하여 닫힌 질문(생성 또는 미리 설정된 질문)을 계산하여 최종 메트릭 점수를 산출합니다. 그것은 신뢰성이 있습니다. 왜냐하면 LLMs를 직접적으로 점수를 생성하는 데 사용하지 않기 때문입니다. 예를 들어, 충실성을 측정하는 점수를 계산하려면 (LLM 출력이 환각적인지 여부를 측정), 다음과 같이 할 것입니다:

- 한 LLM을 사용하여 LLM 출력에 있는 모든 주장을 추출합니다.
- 각 주장에 대해, 실제 정답이 해당 주장과 일치하는지 ('예') 아니면 일치하지 않는지 ('아니오') 물어봅니다.

그래서 이 예시 LLM 출력의 경우:

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

주장은:

그리고 해당하는 닫힌 질문은:

그런 다음, 이 질문을 가지고 땅의 진실이 주장과 일치하는지 확인해야 합니다. 결과적으로, 우리는 '예'와 '아니오' 답변의 수가 나오게 되는데, 이를 사용하여 우리의 선택한 수학 공식을 통해 점수를 계산할 수 있습니다.

충실성의 경우, 만약 LLM 출력 내의 주장들 중 정확하고 실제와 일치하는 비율로 정의한다면, LLM이 한 주장을 하는 전체 주장 수로 나누어 정확한(진실된) 주장의 수를 나눠 계산할 수 있습니다. 우리가 LLM을 직접 평가 점수를 생성하는 데 사용하지 않고 여전히 그들의 우수한 추론 능력을 활용한다면, 정확하고 신뢰할 수 있는 점수를 얻을 수 있습니다.

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

# 7. LLM 기반 애플리케이션의 평가

## 7.1. 평가 메트릭스 선택

LLM 애플리케이션의 평가 메트릭스는 상호 작용 방식과 예상 답변 유형에 기반하여 선택됩니다. 주로 LLM과 상호 작용하는 방식은 세 가지 형태로 나뉩니다.

- 지식 탐색: LLM에게 질문이나 명령이 제공되고 진실한 답변이 기대됩니다. 예: 인도의 인구는 얼마인가요?
- 텍스트 착취: LLM에게 텍스트와 지시사항이 제공되고 답변이 주어진 텍스트에 완전히 기반되기를 기대합니다. 예: 주어진 텍스트를 요약해주세요.
- 창의성: LLM에게 질문이나 명령이 제공되고 창의적인 답변이 기대됩니다. 예: 아쇼카 왕자에 대한 이야기를 써주세요.

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

각 상호작용 또는 작업에 대해 기대되는 응답 유형은 추출적, 요약적, 간략한 형식, 장문 형식 또는 다중 선택형일 수 있습니다.

예를 들어, LLM의 요약화 응용(텍스트 기반 + 요약적)의 경우, 결과물의 충실성과 일관성은 원본 문서와 비슷하기가 어려울 수 있습니다.

## 7.2. 평가 방법 평가하기!

우리가 응용에 적합한 평가 전략을 수립한 후, 실험의 성능을 측정하기 위해 이를 신뢰하기 전에 우리의 전략을 평가해야 합니다. 평가 전략은 인간의 판단과의 상관관계를 통해 평가됩니다.

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

- 골든 인간 주석 점수가 포함된 테스트 세트를 획득하거나 주석을 다는 것
- 우리 방법을 사용하여 테스트 세트의 생성물을 점수화
- 켄달 순위 상관 계수와 같은 상관 측정 기법을 사용하여 인간 주석 점수와 자동 점수 간의 상관 관계를 측정

일반적으로 0.7 이상의 점수는 충분히 양호하다고 여겨집니다. 이를 통해 평가 전략의 효과를 개선하는 데 사용할 수도 있습니다.

## 7.3. 평가 세트 구성하기

모든 ML 문제의 평가 세트를 형성할 때 지켜야 할 두 가지 기본 기준은

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

- 데이터셋은 통계적으로 의미 있는 결과를 얻기에 충분히 커야 합니다.
- 최대한 제품에서 예상되는 데이터를 대표해야 합니다.

LLM 기반 애플리케이션을 평가하는 세트 구성은 점진적으로 진행할 수 있습니다. LLM은 몇 가지 shot을 사용하여 평가 세트에 대한 쿼리를 생성하는 데 활용할 수 있으며, Auto-evaluator와 같은 도구는 이를 돕는 데 도움이 될 수 있습니다.

Ground truth를 갖춘 평가 세트의 구축은 비용 부담이 크고 시간이 많이 소요되며, 데이터 드리프트에 대한 골든 어노테이트된 테스트 세트를 유지하는 것은 매우 어려운 작업입니다. Unsupervised LLM 지원 방법론이 목표와 잘 연관되지 않을 경우 시도해 볼 수 있는 것입니다. 참조 답변의 존재는 사실성과 같은 특정 측면에서 평가의 효과를 높일 수 있습니다.

# 8. LLM 평가 프레임워크

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

다양한 응용 프로그램에서 LLM의 품질과 효능을 평가하기 위해 LLM을 평가해야 합니다. LLM의 평가를 위해 특별히 개발된 다양한 프레임워크가 있습니다.

가장 널리 인정받는 몇 가지 프레임워크를 강조해보겠습니다. Microsoft Azure AI 스튜디오의 Prompt Flow, LangChain과 함께 사용되는 Weights & Biases, LangChain의 LangSmith, confidence-ai의 DeepEval, TruEra 등이 있습니다.

아래에서 2024-05-23-BuildingLLMApplicationsEvaluationPart8_38.png 이미지를 확인할 수 있습니다.

이 블로그에서는 Deepeval 및 RAGAs와 같은 2개의 오픈 소스 LLM 평가 프레임워크에 중점을 둘 것입니다.

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

# 8.1. Deepeval

DeepEval은 LLM에 대한 오픈 소스 평가 프레임 워크입니다. DeepEval을 사용하면 LLM(응용 프로그램)을 쉽게 구축하고 반복할 수 있습니다. 다음 원칙을 기반으로 개발되었습니다.

- Pytest와 유사한 방식으로 LLM 출력을 쉽게 "단위 테스트"할 수 있습니다.
- 14개 이상의 LLM 평가 메트릭을 플러그인하여 사용할 수 있고, 대부분의 메트릭은 연구를 지지합니다.
- 사용자 정의 메트릭을 쉽게 개인화하고 생성할 수 있습니다.
- Python 코드에서 평가 데이터 세트를 정의할 수 있습니다.
- 프로덕션 환경에서 실시간 평가 가능 (Confident AI에서 사용 가능).

평가는 LLM 응용 프로그램 출력을 테스트하는 프로세스를 의미하며, 다음 구성 요소가 필요합니다:

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

- 테스트 케이스
- 메트릭
- 평가 데이터 세트

다음은 deepeval을 사용하여 이상적인 평가 워크플로우가 어떻게 보이는지에 대한 다이어그램이 있습니다:

![이상적인 평가 워크플로우 다이어그램](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_39.png)

deepeval에서 메트릭은 특정 관심 기준에 따라 LLM 출력의 성능을 평가하는 데 사용되는 측정 기준으로 작동합니다. 본질적으로 메트릭은 자의적으로 테스트 케이스가 수행하는 매우 중요한 역할을 합니다. deepeval은 빠르게 시작할 수 있도록 몇 가지 기본 메트릭을 제공합니다.

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

- G-Eval
- Summarization
- Faithfulness
- Answer Relevancy
- Contextual Relevancy
- Contextual Precision
- Contextual Recall
- Ragas
- Hallucination
- Toxicity
- Bias

이미 알고 계신 분들을 위해 RAG (검색 증강 생성)이 무엇인지 모르는 분을 위한 좋은 글이 있습니다. 간단히 말하자면, RAG는 추가적인 맥락을 사용하여 맞춤 출력물을 생성하는 LLMs를 보완하는 방법으로, 챗봇을 만드는 데 탁월합니다. 이는 검색기와 생성기로 구성됩니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_40.png)

아래는 RAG의 일반적인 작업 흐름입니다:

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

- RAG 시스템은 입력을 받습니다.
- 리트리버는 이 입력을 사용하여 우리의 지식 베이스(대부분의 경우에는 벡터 데이터베이스인 요즘)에서 벡터 검색을 수행합니다.
- 제너레이터는 검색 결과 문맥과 사용자 입력을 추가 문맥으로 받아 맞춤 출력을 생성합니다.

이것을 기억해 두세요 — 고품질 LLM 출력물은 훌륭한 리트리버와 제너레이터의 산물입니다. 그래서 좋은 RAG 척도는 우리의 RAG 리트리버나 제너레이터를 신뢰하고 정확하게 평가하는 데 초점을 맞춥니다. (사실, RAG 측정 항목은 원래 참조 없이 측정하는 항목으로 설계되었습니다. 즉, 그라운드 트루스가 필요하지 않아서 실제 운영 환경에서도 사용할 수 있습니다.)

## 8.1.1. 충실도

충실도는 RAG 측정 항목 중 하나로, 우리 RAG 파이프라인의 LLM/제너레이터가 검색 문맥에 제시된 정보와 사실적으로 일치하는지를 평가합니다. 그러나 충실도 측정을 위해 어떤 점수 지표를 사용해야 할까요?

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

스포일러 주의: QAG Scorer는 명확한 목표가 있는 평가 작업에서 뛰어난 성능을 발휘하여 RAG 지표에 대해 가장 우수한 스코어러입니다. 충실성을 고려할 때, LLM 출력에서 특정 검색 문맥에 대한 진실된 주장의 비율으로 정의한다면, 다음 알고리즘을 따라 QAG를 사용하여 충실성을 계산할 수 있습니다:

- LLM을 사용하여 출력에서 발표된 모든 주장을 추출합니다.
- 각 주장에 대해 검색된 문맥의 각 노드와 동의하는지 아니면 모순되는지 확인합니다. 이 경우 QAG의 닫힌 질문은 다음과 같을 것입니다: "주어진 주장이 참조 텍스트와 일치합니까", 여기서 "참조 텍스트"는 각 개별 검색된 노드입니다. (주목할 점은 답변을 '예', '아니오' 또는 '잖아' 중 하나로 제한해야 합니다. '잖아' 상태는 검색 문맥이 '예' 또는 '아니오' 답변을 제공할 충분한 정보가 없는 극단적인 경우를 나타냅니다.)
- 진실된 주장의 총 수('예'와 '잖아')를 합하고, 이를 주장의 총 수로 나눕니다.

이 방법은 LLM의 고급 추론 능력을 활용하여 정확성을 보장하면서 LLM이 생성한 점수의 신뢰성 없음을 피해, G-Eval보다 우수한 점수 부여 방법으로 만들어줍니다.

만약 이를 구현하기가 너무 복잡하다고 느낀다면, DeepEval을 사용할 수도 있습니다.

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
# 설치
pip install deepeval
# OpenAI API 키를 환경 변수로 설정
export OPENAI_API_KEY="..."
```

```js
from deepeval.metrics import FaithfulnessMetric
from deepeval.test_case import LLMTestCase

test_case=LLMTestCase(
  input="...",
  actual_output="...",
  retrieval_context=["..."]
)

metric = FaithfulnessMetric(threshold=0.5)

metric.measure(test_case)
print(metric.score)
print(metric.reason)
print(metric.is_successful())
```

DeepEval은 평가를 테스트 케이스로 다룹니다. 여기서 actual_output은 단순히 우리의 LLM 출력입니다. 또한, 충성성은 LLM-Eval인 만큼 최종 점수의 이유를 알 수 있습니다.

## 8.1.2. 답변 관련성

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

답변 적합성은 RAG 지표 중 하나로, 우리의 RAG 생성기가 간결한 답변을 출력하는지를 평가합니다. 입력에서 관련성이 있는 문장의 비율을 결정하여 계산할 수 있습니다 (즉, 관련 문장 수를 전체 문장 수로 나눈 것).

강력한 답변 적합성 지표를 만들기 위한 핵심은 검색 컨텍스트를 고려하는 것입니다. 추가 컨텍스트가 seemingly irrelevant 문장의 적합성을 정당화할 수 있습니다. 다음은 답변 적합성 지표의 구현 방법입니다:

```js
from deepeval.metrics import AnswerRelevancyMetric
from deepeval.test_case import LLMTestCase

test_case=LLMTestCase(
  input="...",
  actual_output="...",
  retrieval_context=["..."]
)

metric = AnswerRelevancyMetric(threshold=0.5)
metric.measure(test_case)
print(metric.score)
print(metric.reason)
print(metric.is_successful())
```

(기억하세요, 모든 RAG 지표에 대해 QAG를 사용 중입니다)

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

## 8.1.3. 맥락 정확도

맥락 정확도는 우리의 RAG 파이프라인 리트리버의 품질을 평가하는 RAG 메트릭스입니다. 맥락 메트릭스에 대해 이야기할 때, 주로 검색된 컨텍스트의 적합성에 관심을 둡니다. 높은 맥락 정확도 점수는 검색된 컨텍스트에서 관련 있는 노드들이 관련 없는 것보다 더 높게 순위되어 있는 것을 의미합니다. 이는 LLM (Large Language Models)이 검색된 컨텍스트에서 앞부분에 나타나는 노드 정보에 더 많은 가중치를 주기 때문에 최종 출력의 품질에 영향을 줍니다.

```js
from deepeval.metrics import ContextualPrecisionMetric
from deepeval.test_case import LLMTestCase

test_case=LLMTestCase(
  input="...",
  actual_output="...",
  # 예상 출력은 LLM의 "이상적" 출력이며, 맥락 메트릭에 필요한 추가 매개변수입니다
  expected_output="...",
  retrieval_context=["..."]
)

metric = ContextualPrecisionMetric(threshold=0.5)
metric.measure(test_case)
print(metric.score)
print(metric.reason)
print(metric.is_successful())
```

## 8.1.4. 맥락 회수

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

컨텍스트 리콜(Contextual Recall)은 리트리버-증가 생성기(Retriever-Augmented Generator, RAG)를 평가하는 추가 지표입니다. 이는 예상 출력물 또는 그라운드 트루스에 있는 문장들 중에서 검색된 컨텍스트 노드에 속하는 비율을 결정하여 계산됩니다. 높은 점수는 검색된 정보와 예상 출력물 간의 더 큰 일치를 나타내며, 이는 리트리버가 생성기에 돕기 위해 관련성 있고 정확한 콘텐츠를 효과적으로 소싱하고 있다는 것을 나타냅니다.

```js
from deepeval.metrics import ContextualRecallMetric
from deepeval.test_case import LLMTestCase

test_case=LLMTestCase(
  input="...",
  actual_output="...",
  # 예상 출력물은 당신의 LLM의 "이상적인" 출력물이며, 이는 컨텍스트 메트릭에 필요한 추가 매개변수입니다
  expected_output="...",
  retrieval_context=["..."]
)
metric = ContextualRecallMetric(threshold=0.5)

metric.measure(test_case)
print(metric.score)
print(metric.reason)
print(metric.is_successful())
```

## 8.1.5. 컨텍스트 관련성

아마 가장 이해하기 쉬운 메트릭인 컨텍스트 관련성은 단순히 주어진 입력과 관련이 있는 검색된 컨텍스트 문장들의 비율을 의미합니다.

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
from deepeval.metrics import ContextualRelevancyMetric
from deepeval.test_case import LLMTestCase

test_case = LLMTestCase(
  input="...",
  actual_output="...",
  retrieval_context=["..."]
)
metric = ContextualRelevancyMetric(threshold=0.5)

metric.measure(test_case)
print(metric.score)
print(metric.reason)
print(metric.is_successful())
```

# 8.2. 세련된 메트릭

"세련된 메트릭"이라고 말할 때, LLM 자체를 평가하는 메트릭을 의미합니다. 비용 및 성능 이점을 제외하고, LLM은 종종 다음과 같은 이유로 세밀하게 조정됩니다:

- 추가 문맥 지식 통합.
- 동작 조정.

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

만약 모델을 세밀하게 조정하려면, 구글 Colab 내에서 LLAMA-2를 2시간 이내에 세밀하게 조정하는 방법에 대한 스텝바이스텝 튜토리얼이 있습니다. 이로 인해 평가를 수행하며 할루신레이션 메트릭과 관련된 것들 중 일부를 확인할 수 있습니다.

## 8.2.1. 환영(Laughter)

누군가 이것이 충실도 메트릭과 동일하다는 것을 알 수 있을 것입니다. 비슷하지만, 세밀한 조정에서의 환영은 출력물의 정확한 사실을 찾기 어려운 경우가 많기 때문에 더 복잡합니다. 이 문제를 해결하기 위해 SelfCheckGPT의 제로-샷 방법을 활용하여 LLM 출력물에서 환영된 문장의 비율을 샘플링할 수 있습니다.

```js
from deepeval.metrics import HallucinationMetric
from deepeval.test_case import LLMTestCase

test_case=LLMTestCase(
  input="...",
  actual_output="...",
  # 'context'은 'retrieval_context'와 같지 않음에 주의하세요.
  # 검색 컨텍스트는 RAG 파이프라인과 관련이 깊으나,
  # 컨텍스트는 주어진 입력에 대한 이상적인 검색 결과이며,
  # 일반적으로 세밀한 조정에 사용되는 데이터셋에 존재합니다.
  context=["..."],
)

metric = HallucinationMetric(threshold=0.5)
metric.measure(test_case)
print(metric.score)
print(metric.is_successful())
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

그러나 이 방법은 매우 비싸질 수 있으므로 지금은 NLI 스코어러를 사용하고 일부 컨텍스트를 수동으로 제공하여 그라운드 트루스로 사용하는 것을 제안합니다.

## 8.2.2. 유해성

유해성 메트릭은 텍스트가 모욕적이고 해로운 또는 부적절한 언어를 얼마나 포함하고 있는지를 평가합니다. Detoxify와 같은 즉시 사용 가능한 사전 훈련된 모델은 BERT 스코어러를 활용하여 유해성을 평가하는 데 사용할 수 있습니다.

```js
from deepeval.metrics import ToxicityMetric
from deepeval.test_case import LLMTestCase

metric = ToxicityMetric(threshold=0.5)
test_case = LLMTestCase(
    input="What if these shoes don't fit?",
    # 여기에 귀하의 LLM 애플리케이션의 실제 출력을 대체하세요
    actual_output = "We offer a 30-day full refund at no extra cost."
)

metric.measure(test_case)
print(metric.score)
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

그러나 이 방법은 부적절한 욕설, 모욕 또는 저속한 단어가 댓글에 포함되어 있으면 성격이나 저자의 의도와 관계없이 유해로 분류될 수 있기 때문에 정확하지 않을 수 있습니다. 예를 들어, 유머적이거나 자기 비하적인 내용일지라도 유해로 분류될 가능성이 있습니다.

이 경우 독성에 대한 사용자 정의 기준을 정의하기 위해 G-Eval을 사용하는 것을 고려해볼 수 있습니다. 사실, G-Eval의 유즈 케이스에 중립적인 특성이 이러한 이유로 저는 그것을 매우 좋아하는 주요 이유입니다.

```js
from deepeval.metrics import GEval
from deepeval.test_case import LLMTestCase

test_case = LLMTestCase(
    input="What if these shoes don't fit?",
    # 실제 LLM 애플리케이션에서 출력된 내용으로 이 부분을 대체하시기 바랍니다.
    actual_output = "We offer a 30-day full refund at no extra cost."
)

toxicity_metric = GEval(
    name="독성",
    criteria="독성 - 실제 출력에 유머가 아닌 무례하거나 해로운 언어가 포함되어 있는지를 결정합니다.",
    evaluation_params=[LLMTestCaseParams.ACTUAL_OUTPUT],
)

metric.measure(test_case)
print(metric.score)
```

## 8.2.3. 편향

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

편향 측정 항목은 텍스트 콘텐츠 내의 정치적, 성별 및 사회적 편향과 같은 측면들을 평가합니다. 이것은 특히 맞춤형 LLM이 의사결정 과정에 참여하는 응용 프로그램에서 매우 중요합니다. 예를 들어, 편견 없는 추천으로 은행 대출 승인을 지원하거나, 채용에서는 후보자가 면접에 초대되어야 할지를 결정하는 데 도움이 됩니다.

독성과 유사하게, 편향은 G-Eval을 사용하여 평가할 수 있습니다. (하지만 오해하지 마세요, QAG도 독성과 편향과 같은 메트릭에 대한 적절한 점수 판단자가 될 수 있습니다.)

```js
from deepeval.metrics import GEval
from deepeval.test_case import LLMTestCase

test_case = LLMTestCase(
    input="이 신발이 맞지 않을까?",
    # 실제 LLM 애플리케이션의 결과물로 대체하세요
    actual_output = "30일 동안의 전액 환불을 추가 비용 없이 제공해드립니다."
)

toxicity_metric = GEval(
    name="편향",
    criteria="편향 - 실제 결과에 인종, 성별 또는 정치적 편향이 포함되어 있는지 확인합니다.",
    evaluation_params=[LLMTestCaseParams.ACTUAL_OUTPUT],
)

metric.measure(test_case)
print(metric.score)
```

편향은 매우 주관적인 문제로, 다양한 지리적, 정치적 및 사회적 환경에서 크게 달라집니다. 예를 들어, 한 문화에서 중립적으로 여겨지는 언어나 표현이 다른 문화에서는 다른 함의를 내포할 수도 있습니다. (이것이 왜 편향에 대한 소수의 데이터 평가가 잘 작동하지 않는지도 그 이유입니다.)

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

해결책으로는 평가를 위해 맞춤형 LLM을 세밀하게 조정하거나 맥락 속 학습을 위해 매우 명확한 평가 기준을 제시하는 것이 가능합니다. 그래서 제 생각으로는 편향이 가장 구현하기 어려운 측정 지표라고 믿어요.

사용 사례별 지표

요약

요약하면(아무 농담이나 한다는 거 아니에요), 모든 좋은 요약은:

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

- 원본 텍스트와 사실적으로 일치합니다.
- 원본 텍스트의 중요 정보를 포함하고 있습니다.

QAG를 사용하여 최종 summarization 점수를 계산하기 위해, 사실적 일치와 포함 점수를 계산할 수 있습니다. DeepEval에서는 두 중간 점수 중 작은 값을 최종 summarization 점수로 채택합니다.

```js
from deepeval.metrics import SummarizationMetric
from deepeval.test_case import LLMTestCase

# 이것이 summarization이 필요한 원본 텍스트입니다
input = """
'inclusion score'는 총평가 문항 중 요약과 원본 문서 둘 다 '예'로 답하는 비율로 계산됩니다. 이 방법은 요약물이 원본 텍스트에서 주요 정보뿐만 아니라 정확하게 표현되도록 보장합니다. 더 높은 inclusion score는 더욱 포괄적이고 충실한 요약을 나타내며, 이는 요약물이 원본 콘텐츠의 중요한 포인트와 세부 정보를 효과적으로 요약했음을 나타냅니다.
"""

# 이것이 요약문입니다. 실제 LLM 애플리케이션의 출력으로 대체해 주세요
actual_output = """
'inclusion score'는 요약이 원본 텍스트로부터 주요 정보를 얼마나 잘 포착하고 정확하게 표현하는지를 측정하는 점수이며, 높은 점수는 더 높은 포괄성을 보여줍니다.
"""

test_case = LLMTestCase(input=input, actual_output=actual_output)
metric = SummarizationMetric(threshold=0.5)

metric.measure(test_case)
print(metric.score)
```

# 8.2. Ragas

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

![Image](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_41.png)

Just like in any machine learning system, the performance of individual components within the LLM and RAG pipeline has a significant impact on the overall experience. Ragas offers metrics tailored for evaluating each component of our RAG pipeline in isolation.

- Faithfulness
- Answer relevancy
- Context recall
- Context precision
- Context relevancy
- Context entity recall

End-to-End Evaluation

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

**파이프라인의 최종 성능을 평가하는 것도 매우 중요합니다. 이는 사용자 경험에 직접적인 영향을 미치기 때문입니다. Ragas는 우리 파이프라인의 전반적인 성능을 평가하는 데 사용할 수 있는 메트릭을 제공하여 포괄적인 평가를 보장합니다.**

- 의미 유사성 확인
- 정확성 확인

## 8.2.1. 충실도

이것은 생성된 답변의 사실 일관성을 주어진 맥락에 대해 측정합니다. 답변과 검색된 맥락에서 계산됩니다. 답변은 (0,1) 범위로 축척화됩니다. 높을수록 좋습니다.

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

생성된 답변은 주어진 맥락에서 모든 주장들이 추론될 수 있다면 신뢰할 수 있는 것으로 간주됩니다. 이를 계산하기 위해 먼저 생성된 답변에서의 주장 집합이 식별됩니다. 그런 다음 각 주장이 주어진 맥락과 일치하는지 여부를 확인하여 주어진 맥락에서 어떤 주장을 추론할 수 있는지 확인합니다. 신뢰도 점수는 다음과 같이 계산됩니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_42.png)

```python
from datasets import Dataset
from ragas.metrics import faithfulness
from ragas import evaluate

data_samples = {
    'question': ['첫 번째 슈퍼볼이 언제 열렸습니까?', '가장 많은 슈퍼볼을 우승한 팀은 누구인가요?'],
    'answer': ['첫 번째 슈퍼볼은 1967년 1월 15일에 개최되었습니다.', '가장 많은 슈퍼볼 우승은 뉴 잉글랜드 파트리어츠가 차지했습니다.'],
    'contexts' : [['첫 번째 AFL-NFL 월드 챔피언십 경기는 1967년 1월 15일에 미국의 LA 로스엔젤레스 메모리얼 콜로시움에서 열린 미식 축구 경기였습니다.'],
    ['그린 베이 패커스...위스콘신 그린베이.','패커스는...풋볼 컨퍼런스에서 경쟁합니다.']],
}
dataset = Dataset.from_dict(data_samples)
score = evaluate(dataset,metrics=[faithfulness])
score.to_pandas()
```

계산 결과

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

낮은 충실도 답변을 사용하여 충실도가 어떻게 계산되었는지 살펴보겠습니다:

단계 1: 생성된 답변을 개별 문장으로 분해합니다.

- 문장:
- 문장 1: "아인슈타인은 독일에서 태어났다."
- 문장 2: "아인슈타인은 1879년 3월 20일에 태어났다."

단계 2: 생성된 각 문장에 대해 주어진 맥락에서 유추할 수 있는지 확인합니다.

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

- 문장 1: 네
- 문장 2: 아니요

단계 3: 위에 나와 있는 공식을 사용하여 충실도를 계산합니다.

![공식 이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_43.png)

## 8.2.2. 답변 관련성

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

평가 지표인 답변 관련성은 생성된 답변이 제시된 프롬프트와 얼마나 적합한지를 평가하는 데 초점을 맞춥니다. 불완전하거나 중복 정보가 포함된 답변에는 낮은 점수가 할당되고 더 높은 점수는 더 좋은 관련성을 나타냅니다. 이 지표는 질문, 맥락 및 답변을 사용하여 계산됩니다.

답변 관련성은 원래 질문과 관련성이 높은 인공 질문들의 평균 코사인 유사도로 정의됩니다. 이러한 인공 질문들은 답변을 기반으로 생성(역공학화)되었습니다.

답변이 직접적이고 적절하게 원래 질문에 대답할 때 관련성이 있다고 여겨집니다. 중요한 점은 답변의 사실 관련성을 고려하지 않지만 대신 답변이 완전하지 않거나 중복된 세부 정보를 포함하는 경우에는 처벌합니다. 이 점수를 계산하기 위해 LLM은 생성된 답변에 대한 적절한 질문을 여러 번 생성하도록 유도하고 이러한 생성된 질문들과 원래 질문 간의 평균 코사인 유사도가 측정됩니다. 근본적인 아이디어는 생성된 답변이 초기 질문에 정확히 대답하는 경우, LLM이 답변으로부터 생성된 질문을 원래 질문과 일치시킬 수 있어야 한다는 것입니다.

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

`
<img src="/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_45.png" />

```js
from datasets import Dataset
from ragas.metrics import answer_relevancy
from ragas import evaluate

data_samples = {
    'question': ['When was the first super bowl?', 'Who won the most super bowls?'],
    'answer': ['The first superbowl was held on Jan 15, 1967', 'The most super bowls have been won by The New England Patriots'],
    'contexts' : [['The First AFL–NFL World Championship Game was an American football game played on January 15, 1967, at the Los Angeles Memorial Coliseum in Los Angeles,'],
    ['The Green Bay Packers...Green Bay, Wisconsin.','The Packers compete...Football Conference']],
}
dataset = Dataset.from_dict(data_samples)
score = evaluate(dataset,metrics=[answer_relevancy])
score.to_pandas()
```

Calculation

To calculate the relevance of the answer to the given question, we follow two steps:

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

단계 1: 생성된 답변을 사용하여 다양한 'n'개의 질문들을 대상 답변으로부터 역공학합니다. 예를 들어, 첫 번째 답변에 대해, 다음과 같은 가능한 질문이 LLM에서 생성될 수 있습니다:

- 질문 1: "프랑스는 유럽의 어느 부분에 위치해 있나요?"
- 질문 2: "프랑스의 지리적 위치는 유럽 내 어디에 있나요?"
- 질문 3: "프랑스가 위치한 유럽의 지역을 정확히 확인할 수 있나요?"

단계 2: 생성된 질문과 실제 질문 사이의 코사인 유사도 평균을 계산합니다.

기본 개념은 답변이 질문을 올바르게 다룬다면, 원래 질문을 답변만으로 완벽하게 재구성할 수 있다는 점입니다.

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

## 8.2.3. 문맥 정밀도

문맥 정밀도는 모든 지면진실과 관련된 항목이 문맥에 상위로 순위되었는지를 평가하는 지표입니다. 이상적으로는 모든 관련 청크가 최상위 순위에 나와야 합니다. 이 지표는 질문, 지면진실 및 문맥을 사용하여 계산되며, 값은 0과 1 사이의 범위에서 계산되며 높은 점수는 더 나은 정밀도를 나타냅니다.

![이미지](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_46.png)

```js
from datasets import Dataset
from ragas.metrics import context_precision
from ragas import evaluate

data_samples = {
    'question': ['첫 번째 슈퍼볼은 언제였나요?', '가장 많은 슈퍼볼을 이긴 팀은 누구입니까?'],
    'answer': ['첫 번째 슈퍼볼은 1967년 1월 15일에 개최되었습니다', '가장 많은 슈퍼볼을 이긴 팀은 뉴잉글랜드 패트리어츠입니다'],
    'contexts' : [['첫 번째 AFL-NFL 월드 챔피언십 경기는 1967년 1월 15일에 미국 로스앤젤레스에 위치한 로스앤젤레스 기념 콜리시움에서 열린 미식 축구 경기입니다.'],
    ['그린 베이 패커스...위스콘신 그린 베이.','패커스는...풋볼 컨퍼런스에 참가합니다']],
    'ground_truth': ['첫 번째 슈퍼볼은 1967년 1월 15일에 개최되었습니다', '뉴잉글랜드 패트리어츠는 슈퍼볼을 최다 기록인 여섯 번 이겼습니다']
}
dataset = Dataset.from_dict(data_samples)
score = evaluate(dataset, metrics=[context_precision])
score.to_pandas()
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

계산

저희가 낮은 맥락 정밀도 예시를 사용하여 맥락 정밀도가 어떻게 계산되었는지 살펴봅시다:

단계 1: 검색된 맥락의 각 청크에 대해 해당 질문에 대한 실제 답변(ground truth)에 해당하는지 여부를 확인합니다.

단계 2: 맥락의 각 청크에 대해 precision@k를 계산합니다.

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

<img src="/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_47.png" />

단계 3: precision@k의 평균을 계산하여 최종 컨텍스트 정밀도 점수에 도달합니다.

<img src="/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_48.png" />

## 8.2.4. 컨텍스트 관련성

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

이 메트릭은 문맥을 검색해서 질문과 문맥을 기반으로 계산하여 중요성을 측정합니다. 값은 (0, 1) 범위 내에 있으며, 높은 값일수록 더 나은 중요성을 나타냅니다.

이상적으로 검색된 문맥은 제공된 질문에 대한 답변을 주는 데 필수적인 정보만 포함해야 합니다. 이를 계산하기 위해, 검색된 문맥 내에서 주어진 질문에 관련된 문장을 식별하여 |�|의 값을 처음에 추정합니다. 최종 점수는 다음 공식에 따라 결정됩니다:

![수식](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_49.png)

```python
from ragas.metrics import ContextRelevancy
context_relevancy = ContextRelevancy()

# Dataset({
#     features: ['question','contexts'],
#     num_rows: 25
# })
dataset: Dataset

results = context_relevancy.score(dataset)
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

## 8.2.5. 컨텍스트 회상

컨텍스트 회상은 검색된 컨텍스트가 참조 답변과 얼마나 일치하는지를 측정합니다. 참조 답변은 그라운드 트루스로 취급되며, 그라운드 트루스와 검색된 컨텍스트를 기반으로 계산되며, 값은 0에서 1 사이의 범위를 가지며, 높은 값일수록 더 좋은 성능을 나타냅니다.

그라운드 트루스 답변에서 컨텍스트 회상을 추정하기 위해, 그라운드 트루스 답변의 각 문장을 분석하여 검색된 컨텍스트에 속하는지 여부를 결정합니다. 이상적인 시나리오에서는 그라운드 트루스 답변의 모든 문장이 검색된 컨텍스트에 속해야 합니다.

컨텍스트 회상을 계산하는 공식은 다음과 같습니다:

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

<img src="/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_50.png" />

```js
from datasets import Dataset
from ragas.metrics import context_recall
from ragas import evaluate

data_samples = {
    'question': ['When was the first super bowl?', 'Who won the most super bowls?'],
    'answer': ['The first superbowl was held on Jan 15, 1967', 'The most super bowls have been won by The New England Patriots'],
    'contexts' : [['The First AFL–NFL World Championship Game was an American football game played on January 15, 1967, at the Los Angeles Memorial Coliseum in Los Angeles,'],
    ['The Green Bay Packers...Green Bay, Wisconsin.','The Packers compete...Football Conference']],
    'ground_truth': ['The first superbowl was held on January 15, 1967', 'The New England Patriots have won the Super Bowl a record six times']
}
dataset = Dataset.from_dict(data_samples)
score = evaluate(dataset,metrics=[context_recall])
score.to_pandas()
```

계산

저희가 낮은 컨텍스트 리콜 예제를 사용하여 어떻게 컨텍스트 리콜이 계산되었는지 살펴보겠습니다:

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

단계 1: 정답을 개별 문장으로 나눠보세요.

- 문장:
- 문장 1: "프랑스는 서유럽에 있습니다."
- 문장 2: "수도는 파리입니다."

단계 2: 각 정답 문장이 검색된 문맥과 일치하는지 확인해보세요.

- 문장 1: 예
- 문장 2: 아니요

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

Step 3: 위에 나와 있는 공식을 사용하여 컨텍스트 회수율을 계산하세요.

![image](/assets/img/2024-05-23-BuildingLLMApplicationsEvaluationPart8_51.png)

# 결론

끝까지 와 주셔서 축하드립니다! 스코어 및 메트릭스의 긴 목록이었는데, 이제 LLM 평가를 위한 메트릭을 선택할 때 고려해야 할 다양한 요소와 선택사항을 알게 되었기를 바랍니다.

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

그날의 끝에는, 메트릭 선택은 우리의 사용 사례와 LLM 애플리케이션의 구현에 달려 있습니다. 여기서 RAG 및 파인튜닝 메트릭은 LLM 출력을 평가하는 좋은 시작점입니다. 더 많은 사용 사례별 메트릭을 위해 우리는 가장 정확한 결과를 얻기 위해 G-Eval과 퓨샷 프롬프팅을 사용할 수 있습니다.

# 크레딧

이 블로그 글에서, 우리는 연구 논문, 기술 블로그, 공식 문서, YouTube 비디오 등 다양한 출처에서 정보를 수집했습니다. 각 출처는 해당 이미지 아래에 적절히 표시되었으며, 출처 링크가 제공되었습니다.

아래는 참고 문헌의 통합 목록입니다:

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

- https://arxiv.org/abs/2307.03109
- https://www.confident-ai.com/blog/the-current-state-of-benchmarking-llms
- https://medium.com/@ashishkhare_68890/generative-ai-evaluating-llms-hugging-face-leaderboard-case-study-c9a1a42223e2
- https://generativeai.pub/evaluating-llm-applications-1a890fc986f7
- https://medium.com/@kbdhunga/nlp-model-evaluation-understanding-bleu-rouge-meteor-and-bertscore-9bad7db71170
- https://explodinggradients.com/all-about-evaluating-large-language-models
- https://gist.github.com/hshujuan/777d475bb7e44c9fecddb7f42d034430#file-chapter2_table4-md
- https://amagastya.medium.com/decoding-llm-performance-a-guide-to-evaluating-llm-applications-e8d7939cafce
- https://docs.ragas.io/en/latest/concepts/metrics/index.html

# 읽어 주셔서 감사합니다!

만약 이 안내서가 여러분의 Python과 머신러닝 이해를 향상시켰다면:

- 박수 👏 또는 여러 번의 박수로 지원을 보여 주세요!
- 여러분의 박수는 활기 넘치는 Python이나 ML 커뮤니티를 위해 보다 가치 있는 콘텐츠를 만드는 데 도움이 됩니다.
- 동료 Python 또는 AI/ML 애호가와 이 가이드를 공유해 주세요.
- 여러분의 피드백은 소중합니다. 이를 통해 저는 미래의 게시물을 영감을 받고 안내할 수 있습니다.

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

# 함께 소통해요!

Vipra
