---
title: "Ontologies 및 언어 모델을 활용하여 정확한 질문 응답하기"
description: ""
coverImage: "/assets/img/2024-05-23-LeveragingOntologiesandLanguageModelsforAccurateQuestionAnswering_0.png"
date: 2024-05-23 17:20
ogImage:
  url: /assets/img/2024-05-23-LeveragingOntologiesandLanguageModelsforAccurateQuestionAnswering_0.png
tag: Tech
originalTitle: "Leveraging Ontologies and Language Models for Accurate Question Answering"
link: "https://medium.com/@alcarazanthony1/leveraging-ontologies-and-language-models-for-accurate-question-answering-de41056c370f"
---

금일의 데이터 중심 세계에서는 방대한 양의 정보를 활용하여 정확하게 질문에 답하는 능력이 중요합니다. 대형 언어 모델(LLM)을 활용한 질문 응답(QA) 시스템은 이러한 측면에서 큰 가능성을 보여주고 있습니다.

하지만, 이러한 시스템의 정확도와 신뢰성을 보장하는 것은 여전히 중요한 과제입니다.

data.world의 최근 연구에 따르면, 온톨로지와 지식 그래프를 활용하면 LLM 기반 QA 시스템의 성능을 크게 향상시킬 수 있음을 입증했습니다.

본 글에서는 온톨로지 기반의 쿼리 유효성 검증과 LLM을 활용한 쿼리 복구를 결합한 혁신적인 접근 방식을 탐구하여, 질문 응답에서 전례없는 수준의 정확성을 달성하는 방법을 살펴보겠습니다.

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

# 배경

질문 응답 시스템은 구조화된 또는 구조화되지 않은 데이터 소스에서 정보를 추출하여 사용자 쿼리에 정확하고 관련성 높은 응답을 제공하는 것을 목표로 합니다. 기존의 QA 시스템은 종종 자연어 쿼리의 복잡성과 모호성에 노출되어 최적의 결과를 얻기 어려웠습니다. 지식 그래프와 온톨로지의 등장은 도메인 지식을 표현하고 추론하는 강력한 프레임워크를 제공하여 보다 정교한 QA 방법을 가능케 하였습니다 [2].

대규모 언어 모델인 GPT-4와 같은 모델은 자연어 처리 분야를 혁신적으로 변화시켰으며, 인간과 유사한 텍스트를 이해하고 생성하는 놀라운 능력을 보여주었습니다. 대형 언어 모델은 SQL 또는 SPARQL 쿼리를 사용하여 구조화된 데이터베이스에서 질문에 답변하는 등 다양한 QA 작업에 적용되었습니다 [3]. 그러나 LLM이 생성한 쿼리의 정확도는 기반이 되는 데이터 스키마와 의미에 대한 명확한 지식 부족으로 제한될 수 있습니다.

# Ontology-based Query Check (OBQC)

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

<img src="/assets/img/2024-05-23-LeveragingOntologiesandLanguageModelsforAccurateQuestionAnswering_0.png" />

LLM 기반 QA 시스템의 한계를 해결하기 위해 연구자들은 Ontology 기반 쿼리 체크 (OBQC) 접근 방식을 제안했습니다 [1]. OBQC는 온톨로지에 인코딩된 의미 정보를 활용하여 LLM이 생성한 SPARQL 쿼리의 정확성을 검증합니다. 이 과정은 몇 가지 주요 단계로 이루어집니다:

1. 생성된 SPARQL 쿼리에서 기본 그래프 패턴 (BGP)을 추출하여 쿼리의 그래프 패턴을 나타냅니다 [1].
2. :쿼리( BGP가 RDF로 바뀐 것을 나타냄)와 :온톨로지(온톨로지 자체를 나타냄)의 두 개의 명명된 그래프를 캡슐화한 결합 그래프를 구성합니다 [1].
3. SPARQL 쿼리로 구현된 온톨로지 일관성 규칙을 적용하여 :쿼리와 :온톨로지 그래프 사이의 위반을 확인합니다 [1].
4. 생성된 쿼리에서 구체적인 오류를 식별하고 각 규칙 위반에 대한 사람이 읽을 수 있는 설명을 생성합니다 [1].

LLM이 생성한 쿼리를 온톨로지의 의미와 비교함으로써 OBQC는 도메인이나 범주 클래스 불일치, 호환되지 않는 속성 사용, 정의되지 않은 속성 등 다양한 유형의 오류를 감지할 수 있습니다. 이 유효성 검사 프로세스는 생성된 쿼리가 기저지식 그래프와 일관된지 확인하여 QA 시스템의 전체 정확성을 향상시키는 데 도움을 줍니다.

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

# LLM Repair

OBQC는 생성된 쿼리의 오류를 식별하는 데 능숙하지만, 이러한 오류를 수정하는 메커니즘을 제공하지는 않습니다. 여기서 LLM Repair 구성 요소가 필요합니다. LLM Repair는 OBQC에 의해 제공된 오류 설명을 기반으로 쿼리를 반복적으로 다듬고 수정하는 능력을 활용합니다 [1].

수리 과정은 오류 설명과 부정확한 SPARQL 쿼리를 포함하는 프롬프트를 작성하여 시작됩니다. 이 프롬프트는 그런 다음 LLM에 공급되며, LLM은 의도한 의미와 구조를 보존하면서 식별된 문제를 해결하기 위해 쿼리를 다시 작성하려고 시도합니다 [1]. 수정된 쿼리는 그런 다음 OBQC로 반환되어 확인을 받으며, 유효한 쿼리를 얻거나 최대 반복 횟수에 도달할 때까지 반복적인 피드백 루프를 형성합니다 [1].

LLM Repair는 LLM이 자연어 설명을 이해하고 일관된 응답을 생성하는 능력에 활용합니다. 질문이나 온톨로지에 명시적인 액세스가 필요하지 않고 오류 설명을 활용함으로써, 쿼리를 수정하는 데 효과적으로 학습할 수 있습니다 [1].

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

수리 과정이 일정 횟수의 반복 이후 유효한 쿼리를 생성하지 못하면, LLM 수리는 "알 수 없음" 또는 "불확실" 응답을 반환하여 신뢰할만한 답변을 생성할 수 없음을 표시합니다 [1]. 이 실패 안전 기구는 수리 시도가 실패할 경우 잘못된 또는 오해를 일으킬 수 있는 결과를 시스템이 제공하는 것을 방지합니다.

# 실험 설정 및 결과

OBQC 및 LLM 수리 접근 방식의 효과를 평가하기 위해 연구자들은 Chat with the Data 벤치마크를 사용한 실험을 진행했습니다 [1]. 이 벤치마크에는 기업용 SQL 스키마, 질문-답변 쌍, 그리고 OWL 온톨로지 매핑이 포함되어 있습니다. 테스트된 QA 시스템은 SPARQL 제로샷 프롬프트로 GPT-4였으며, 쿼리는 가상화된 지식 그래프에서 실행되었습니다 [1].

실험 결과는 정확도와 오류 감소에서 상당한 향상을 보여주었습니다:

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

- OBQC 및 LLM Repair을 사용하여 전체 실행 정확도가 42.88%에서 72.55%로 향상되었습니다 [1].
- 시스템에서 잘못된 것으로 식별된 알 수 없는 쿼리가 전체 오류율인 19.44% 중 8%를 차지했습니다 [1].
- 고복잡도 스키마에 관한 질문의 정확도 향상이 특히 유의미했습니다 [1].

추가 분석 결과, OBQC의 도메인 관련 규칙이 수리의 70%를 담당하는 가장 일반적인 규칙임이 밝혀졌습니다 [1]. 이는 온톨로지에서 도메인 지식을 정확하게 모델링하는 것이 QA 성능을 향상시키는 데 중요한 역할을 한다는 것을 시사합니다.

# 현실 세계의 영향과 응용

OBQC 및 LLM Repair의 유망한 결과들은 이미 현실 세계 응용분야에서 찾아보실 수 있습니다. 기업용 데이터 카탈로그 및 발견 솔루션의 선도 업체인 data.world은 이러한 구성 요소를 AI Context Engine에 통합하였습니다 [1]. AI Context Engine은 구조화된 데이터와의 신뢰할 수 있는 대화를 지원하여 고객이 질문을 하고 정확하고 컨텍스트 인식형 답변을 받을 수 있도록 합니다.

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

몇몇 data.world 고객은 AI Context Engine을 도입하여, 다양한 사용 사례에서 향상된 QA 능력을 활용하고 있습니다 [1]. 이는 실제 환경에서 온톨로지 기반 검증과 LLM 기반 복구를 결합한 가치와 영향을 보여줍니다.

## 미래 방향성과 도전 과제

현재 방법이 높은 성공률을 보여주고 있지만, 미래 연구와 개선을 위한 여러 분야가 아직 남아 있습니다. 하나의 주요 도전 과제는 OBQC를 보다 복잡한 온톨로지 구조에 대응할 수 있도록 확장하는 것입니다. 이는 OWL 어키오름이 포함된 온톨로지 조합(연합, 교집합 또는 다른 논리 연산자)에 대한 처리를 의미합니다 [1]. 이 도전에 대응함으로써 시스템이 쿼리를 풍부하고 표현력 있는 온톨로지에 대해 유효성을 검증하는 능력을 더욱 강화할 수 있을 것입니다.

또 다른 중요한 방향은 해당 접근 방식을 다른 도메인과 데이터 집합으로 일반화하는 것입니다. 현재 실험은 기업용 SQL 스키마와 질문-답변 쌍을 다루는 Chat with the Data 벤치마크에 주로 초점을 맞추었습니다 [1]. 시스템의 성능을 다양한 도메인과 데이터 소스 범위에서 평가함으로써 보다 넓은 적용 가능성과 견고성을 평가할 수 있을 것입니다.

확장성과 계산 비용에 대한 조사도 필요합니다. 온톨로지와 데이터 집합의 크기와 복잡성이 증가함에 따라, OBQC와 LLM Repair 구성 요소의 효율성은 점점 더 중요해집니다 [1]. 최적화된 알고리즘 및 병렬 처리 기술을 개발함으로써 대규모 응용 프로그램에 대한 시스템의 실용성과 반응성을 보장하는 데 도움이 될 수 있습니다.

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

체계 기반 쿼리 유효성 검사와 LLM(언어 모델) 기반 쿼리 수리의 결합은 질문 응답 분야에서 중요한 발전을 나타냅니다. 오전톨로지에 인코딩된 의미 지식과 LLM의 생성적 능력을 활용하여, 이 방법은 전례없는 수준의 정확도와 신뢰성을 실현합니다.

실험 결과와 현실 세계에서의 채택은 이 기법이 신뢰할 수 있고 맥락에 맞는 구조화된 데이터와의 대화를 가능케 함에 대한 엄청난 잠재력을 입증합니다. 조직이 통찰을 도출하고 데이터 기반 결정을 내리는 데 QA 시스템에 점점 더 의존함에 따라, 정확성의 중요성은 지나치게 강조될 수 없습니다.

그러나 이 방법의 전체 잠재력을 실현하기 위해서는 계속된 연구 및 개발 노력이 필요합니다. 더 복잡한 온톨로지 구조를 처리하고, 다양한 도메인에 적용할 수 있도록 일반화하고, 확장성 문제를 해결하는 것이 미래 작업의 주요 분야입니다.

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

OBQC와 LLM Repair의 성공은 의미론, 온톨로지, 그리고 지식 그래프가 정확하고 신뢰할 수 있는 QA 시스템을 구축하는 데 중요한 역할을 한다는 점을 강조합니다. 이러한 기본기술에 투자하고 계속해서 가능한 한 경계를 넓힘으로써, 우리는 자연어 인터페이스의 진정한 잠재력을 발휘하고 사용자들이 필요로 하는 정보에 원활하게 액세스할 수 있도록 돕는 것이 가능합니다.

# 참고문헌

[1] Dean Allemang과 Juan F. Sequeda. 2024. “Increasing the LLM Accuracy for Question Answering: Ontologies to the Rescue!” 기술 보고서.
[2] Aidan Hogan 등. 2021. “Knowledge Graphs.” 데이터, 의미론, 그리고 지식에 관한 합성 강의. Morgan & Claypool Publishers.
[3] Juan Sequeda, Dean Allemang, 그리고 Brad Jesson. 2023. “A Benchmark to Understand the Role of Knowledge Graphs on Large Language Model’s Accuracy for Question Answering on Enterprise SQL Databases.” arXiv 사전인쇄 arXiv:2406.01688.
