---
title: "기업용 프롬프트 엔지니어링 관행"
description: ""
coverImage: "/assets/img/2024-05-18-EnterprisePromptEngineeringPractices_0.png"
date: 2024-05-18 19:57
ogImage:
  url: /assets/img/2024-05-18-EnterprisePromptEngineeringPractices_0.png
tag: Tech
originalTitle: "Enterprise Prompt Engineering Practices"
link: "https://medium.com/@cobusgreyling/enterprise-prompt-engineering-practices-2ea7593f5e9b"
---

# 소개

대규모 언어 모델(LLM)과 상호작용하는 것은 본질적으로 프롬프트에 매우 의존합니다. 프롬프트는 모델로부터 특정한 동작이나 출력을 유도하기 위한 자연어 지침입니다.

프롬프트는 비전문가에게 LLM에 접근할 수 있게 돕긴 하지만, 복잡하거나 특정 작업에 대한 효과적인 프롬프트를 만드는 것은 어렵습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

모델을 원하는 결과물로 이끄는 것에는 기술, 지식 및 반복적 개선이 필요합니다.

IBM 연구팀은 사용자가 프롬프트를 반복하면서 어떻게 사용하는지 연구했으며, 이 연구는 다음을 이해하는 데 도움이 됩니다.

- 프롬프트 사용 및
- 모델 동작, 그리고
- 효율적인 프롬프트 엔지니어링에 필요한 지원

일반적으로 프롬프트에는 내장 예시, 템플릿, 필요한 출력물에 대한 설명, 지시사항 및 In-Context Learning을 위한 문맥 데이터가 포함될 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 결과

결과는 두 부분으로 나뉩니다.

먼저, 연구는 관찰된 프롬프트 편집 세션들에 대한 광범위한 양적 분석을 제공합니다.

이어서 연구는 리뷰 및 주석 프로세스에서 나온 더 포괄적인 결과에 대해 심층적으로 다루며 질적 관측을 통합합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 프롬프트 편집 세션은 일반적으로 상당한 기간 동안 진행되었으며, 평균 세션은 약 43.4분의 시간이 소요되었습니다.
- 사용자들은 종종 모델 매개변수를 조정하는 대신에 또는 함께 프롬프트를 편집하는 데 집중합니다.
- 사용자들은 원하는 결과를 얻기 위해 프롬프트를 조금씩 반복적으로 변경하는 경향이 있어, 프롬프트 세부 조정 반복 과정에서 불규칙적으로 행동하지 않는 것으로 나타났습니다.
- 사용자들은 프롬프트를 개선하는 동안 추론 매개변수를 자주 조정했으며, 관찰된 세션 중 93%가 이러한 매개변수를 하나 이상 변경한 것으로 나타났습니다.
- 가장 자주 변경된 매개변수는 대상 언어 모델 (모델 ID)이었으며, 이어서 최대 새 토큰 및 반복 패널티 매개변수가 조정되었습니다.

성공적인 결과를 얻으려면 사용자들이 프롬프트를 조금씩 반복적으로 변경하는 경향이 있는 것으로 보입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

매개변수 변경은 변경이 발생한 세션의 백분율로 기록되었습니다.

사용자들은 대부분 대상 언어 모델을 수정하고 생성할 토큰의 최대 수를 조정하며 반복 패널티를 미세 조정했습니다. 또한, 중단 시퀀스, 온도 및 디코딩 방법을 변경하는 것이 자주 관찰되었습니다.

각 프롬프트 구성 요소에 초점을 맞춘 편집 횟수. 사용자들은 기본적으로 맥락을 편집했으며 작업 지시사항은 상대적으로 적게 수정되었습니다.

이것은 다시 한번 보여주는 것입니다. 인컨텍스트 러닝(ICL)을 유지하는 관점에서 맥락은 매우 중요하며, 그 다음이 작업 지시사항이라는 것을 입증합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

아래 그래프는 사용된 모델 수를 보여줍니다. 대부분의 세션은 두 개의 모델을 살펴본 것이 흥미로운데, 아마도 쉬운 A/B 테스트 접근법을 수행했을 것입니다.

가장 많이 사용된 수정 작업과 텍스트의 문맥이 보강되거나 변경되거나 수정 또는 제거되어야 하는 작업은 편집 유형 중 가장 많이 사용된 것입니다.

# 콘텍스트

분석된 프롬프트와 사용 사례 대부분은 콘텍스트 기반입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

프롬프트 안에 입력, 배경 데이터 또는 예시가 통합되어 있었으며, 작업 지시와는 분리되었습니다.

모든 분석된 세션에서 맥락이 가장 자주 편집되는 구성 요소로 부각되었으며, 기업 업무에 대한 그 중요성을 강조했습니다.

맥락 추가의 두 가지 주요한 패턴:

- 대화 시뮬레이션 및
- 예시 추가.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

테이블 태그를 마크다운 형식으로 변경하실 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

22%의 편집이 여러 번의 변화를 만들고 나서야 다시 프롬프트를 제출하는, 다중 편집이었습니다.

평균적으로, 이러한 다중 편집에는 약 2.29개의 변경이 포함되었으며, 대부분은 컨텍스트에 대한 편집을 포함했습니다.

다중 편집은 효율적으로 보일 수 있지만, 결과물에 미치는 영향을 추적하기를 어렵게 할 수 있습니다. 추가로, 편집의 약 1/5은 추론 매개변수의 변경과 함께 이루어졌으며, 이는 변경을 관리하고 모델 동작에 미치는 영향을 이해하기 위한 체계적 접근의 필요성을 시사합니다.

# 롤백

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

약 11%의 프롬프트 편집이 이전 변경 사항을 취소하거나 다시 실행하는 것을 포함했습니다. 이에도 불구하고 이러한 작업은 개별 편집으로 계산되었습니다.

이러한 행동은 과거 결과를 기억하는 데 어려움이 있거나 어떤 편집이 출력물을 개선할 수 있는지에 대한 불확실성을 시사할 수도 있습니다.

재미있게도, 덜 자주 편집되는 프롬프트 구성 요소는 이전 변경 사항을 취소하는 편집 비율이 더 높았습니다.

예를 들어, instruction:handle-unknown에 대한 편집의 40%가 되돌려졌으며, instruction:output-length에 대해 25%가, 라벨에 대해 24%가, instruction:persona 편집에 대해서는 18%가 되돌려졌습니다.

<!-- ui-station 사각형 -->

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

이 연구는 1523개의 개별 프롬프트로 구성된 57개의 프롬프트 편집 세션을 분석했습니다. 이 프롬프트 편집 세션은 프롬프트 실험 및 개발을 용이하게 하는 엔터프라이즈 LLM 도구를 사용하였습니다.

사용자들은 종종 모델 파라미터를 조정하는 대신 또는 나란히 프롬프트를 편집하는 데 초점을 맞춥니다.

이러한 편집 중 많은 것들은 완전한 개조보다는 단일 프롬프트에 대한 소규모 조정이나 반복입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

품질 분석 결과를 살펴보면 사용자들이 주로 예시, 기반 문서, 그리고 입력 쿼리와 같은 프롬프트 컨텍스트를 수정한다는 것을 강조합니다.

의외로도, 컨텍스트 편집은 지시사항 편집보다 많은 것으로 드러났는데, 이는 과제 또는 출력 형식, 길이, 또는 페르소나와 같은 요소를 설명하는 것과 관련이 있습니다.

라벨 편집 및 프롬프트 구성 요소 정의는 또한 일반적입니다.

이러한 통찰력은 현재의 프롬프트 편집 관행을 살펴보고 더 효과적인 프롬프트 엔지니어링 지원을 위한 미래 방향을 제시해줍니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

⭐️ 제 LinkedIn에서 큰 언어 모델에 관한 업데이트를 받아보세요 ⭐️

![이미지](/assets/img/2024-05-18-EnterprisePromptEngineeringPractices_1.png)

저는 현재 Kore AI의 최고 전도사입니다. 인공지능과 언어가 교차하는 모든 영역에 대해 탐구하고 쓰고 있습니다. 대형 언어 모델(Large Language Models), 챗봇(Chatbots), 음성봇(Voicebots), 개발 프레임워크, 데이터 중심의 잠재 공간 등 다양한 주제를 다룹니다.

![이미지](/assets/img/2024-05-18-EnterprisePromptEngineeringPractices_2.png)

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![2024-05-18-EnterprisePromptEngineeringPractices_3](/assets/img/2024-05-18-EnterprisePromptEngineeringPractices_3.png)

![2024-05-18-EnterprisePromptEngineeringPractices_4](/assets/img/2024-05-18-EnterprisePromptEngineeringPractices_4.png)
