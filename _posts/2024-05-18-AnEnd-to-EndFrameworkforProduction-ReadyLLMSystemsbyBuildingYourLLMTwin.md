---
title: "프로덕션에 적합한 LLM 시스템을 위한 엔드 투 엔드 프레임워크 LLM 트윈 구축하기"
description: ""
coverImage: "/assets/img/2024-05-18-AnEnd-to-EndFrameworkforProduction-ReadyLLMSystemsbyBuildingYourLLMTwin_0.png"
date: 2024-05-18 20:03
ogImage:
  url: /assets/img/2024-05-18-AnEnd-to-EndFrameworkforProduction-ReadyLLMSystemsbyBuildingYourLLMTwin_0.png
tag: Tech
originalTitle: "An End-to-End Framework for Production-Ready LLM Systems by Building Your LLM Twin"
link: "https://medium.com/decodingml/an-end-to-end-framework-for-production-ready-llm-systems-by-building-your-llm-twin-2cc6bb01141f"
---

## LLM TWIN COURSE: BUILDING YOUR PRODUCTION-READY AI REPLICA

→ LLM Twin 무료 코스의 첫 번째 강의

당신의 LLM Twin은 무엇인가요? LLM Twin은 당신의 스타일, 성격 및 목소리를 포함하여 당신처럼 쓰는 AI 캐릭터입니다.

![이미지](/assets/img/2024-05-18-AnEnd-to-EndFrameworkforProduction-ReadyLLMSystemsbyBuildingYourLLMTwin_0.png)

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

## 이 강의가 다른 이유는 무엇인가요?

무료 강의 "LLM Twin: Building Your Production-Ready AI Replica"를 완료하면 LLMs, 벡터 DB 및 LLMOps의 좋은 실천법에 의해 구동되는 스스로의 프로덕션 준비 AI 복제본을 설계, 훈련 및 배포하는 방법을 배울 수 있습니다.

## 이 강의를 통해 어떤 것을 배우게 되나요?

데이터 수집부터 배포까지 실제 LLM 시스템을 설계하고 구축하는 방법을 배우실 수 있습니다.

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

MLOps의 최상의 실천법을 활용하는 방법을 배울 것입니다. 실험 추적기, 모델 레지스트리, 즉시 모니터링, 그리고 버전 관리 등이 있습니다.

최종 목표는 무엇일까요? 자신만의 LLM 쌍을 구축하고 배포하는 것입니다.

LLM 쌍의 아키텍처는 4개의 파이썬 마이크로서비스로 나뉩니다:

- 데이터 수집 파이프라인: 다양한 소셜 미디어 플랫폼에서 디지털 데이터를 수집합니다. ETL 파이프라인을 통해 데이터를 정리, 정규화하고 NoSQL DB에 로드합니다. CDC 패턴을 사용하여 데이터베이스 변경 사항을 큐로 전송합니다. (AWS에 배포됨)
- 피처 파이프라인: 바이트왁스 스트리밍 파이프라인을 통해 큐에서 메시지를 소비합니다. 각 메시지는 실시간으로 정리되고 청크화되며 Superlinked를 사용하여 삽입되고 Qdrant 벡터 DB에 로드됩니다. (AWS에 배포됨)
- 트레이닝 파이프라인: 디지털 데이터를 기반으로 사용자 정의 데이터세트를 생성합니다. QLoRA를 사용하여 LLM을 세밀하게 조정합니다. Comet ML의 실험 추적기를 사용하여 실험을 모니터링합니다. 최상의 모델을 Comet의 모델 레지스트리에 저장 및 평가합니다. (Qwak에 배포됨)
- 인퍼런스 파이프라인: Comet의 모델 레지스트리에서 세밀하게 조정된 LLM을 로드하고 양자화합니다. 이를 REST API로 배포합니다. RAG를 사용하여 프롬프트를 향상시키고, LLM 쌍을 사용하여 콘텐츠를 생성합니다. Comet의 프롬프트 모니터링 대시보드를 사용하여 LLM을 모니터링합니다. (Qwak에 배포됨)

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

4개의 마이크로서비스를 통해 3가지 서버리스 도구를 통합하는 방법을 배울 수 있습니다:

- ML 플랫폼으로서의 Comet ML;
- 벡터 DB로서의 Qdrant;
- ML 인프라로서의 Qwak;

## 누구를 위한 것인가요?

대상: MLE, DE, DS 또는 SWE로서, LLMOps의 좋은 원칙을 사용하여 제품 준비 상태의 LLM 시스템을 설계하고 싶은 분들을 대상으로 합니다.

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

수준: 중급

필수 조건: Python, ML 및 클라우드에 대한 기본 지식

## 어떻게 학습하시겠습니까?

이 강의에는 11개의 실습 수업 및 GitHub에서 액세스할 수 있는 오픈 소스 코드가 포함되어 있습니다.

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

모두 자신의 속도로 모든 것을 읽을 수 있어요.

→ 이 과정을 최대한 효율적으로 활용하려면 강의를 따라가며 저장소를 복제하고 실행하는 것을 권장해요.

## 비용은?

기사와 코드는 완전히 무료에요. 언제나 무료로 제공될 거에요.

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

그러나 코드를 실행하면서 읽으려면, 추가 비용이 발생할 수 있는 몇 가지 클라우드 도구를 사용한다는 것을 알아두어야 합니다.

클라우드 컴퓨팅 플랫폼(AWS, Qwak)은 pay-as-you-go 요금제를 제공합니다. Qwak은 무료 컴퓨팅 시간을 제공합니다. 따라서 우리는 비용을 최소화하기 위해 최선을 다하였습니다.

다른 서버리스 도구(Qdrant, Comet)의 경우, 무료로 사용할 수 있는 프리미엄 버전을 사용할 것입니다.

## 선생님들을 만나보세요!

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

Decoding ML 우산 아래에서 개설 된 이 과정을 개발 한 사람들은 다음과 같습니다:

- Paul Iusztin | 시니어 ML & MLOps 엔지니어
- Alex Vesa | 시니어 AI 엔지니어
- Alex Razvant | 시니어 ML & MLOps 엔지니어

# 수업

이 과정은 총 11개의 수업으로 구성되어 있습니다. 매체 기사 하나당 하나의 수업으로 나뉩니다.

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

- LLM 시스템의 제품용 엔드 투 엔드 프레임워크 구축을 통한 LLM Twin
- 생성 모델 AI 시대의 데이터 파이프라인의 중요성
- 변경 데이터 캡처: 이벤트 주도 아키텍처 가능
- 실시간으로 LLM 및 RAG의 파이썬 스트리밍 파이프라인의 뛰어난 솔루션
- 구현해야 할 4가지 고급 RAG 알고리즘
- 특징 저장소의 역할 LLM 세부 조정에
- LLM 세부 조정 [모듈 3] ...작업 중
- LLM 평가 [모듈 4] ...작업 중
- 양자화 [모듈 5] ...작업 중
- 디지털 트윈 추론 파이프라인 구축 [모듈 6] ...작업 중
- REST API로 디지털 트윈 배포 [모듈 6] ...작업 중

첫 번째 레슨에서는 당신이 수강 기간 동안 구축할 프로젝트인 제품용 LLM Twin/AI 복제품을 소개할 것입니다.

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

이후에는 3-파이프라인 디자인이 무엇인지와 이것이 표준 ML 시스템에 어떻게 적용되는지 설명할 것입니다.

마지막으로, LLM 프로젝트 시스템 디자인에 대해 자세히 살펴볼 것입니다.

소셜 미디어 데이터 수집 파이프라인 디자인에 대한 모든 아키텍처 결정과 LLM 마이크로서비스에 3-파이프라인 아키텍처를 적용하는 방법을 설명할 것입니다.

다음 수업에서는 각 구성 요소의 코드를 검토하고 AWS 및 Qwak에 구현하고 배포하는 방법을 배울 것입니다.

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

## 목차

- 무엇을 구축할 계획인가요? LLM twin 개념
- 3-파이프라인 아키텍처
- LLM twin 시스템 디자인

# 1. 무엇을 구축할 계획인가요? LLM twin 개념

이 과정의 목표는 당신만의 AI 레플리카를 구축하는 것입니다. 우리는 그것을 할 수 있도록 LLM을 사용할 것이며, 따라서 이 과정의 이름이 LLM Twin: 생산 준비가 완료된 AI 레플리카 구축입니다.

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

LLM 쌍이 무엇인지 알고 싶으신가요?

간단히 말씀드리면, LLM 쌍은 여러분과 비슷한 방식으로 글을 쓰는 인공지능 캐릭터가 될 거에요.

여러분 그 자신이 되는 게 아니라, 여러분의 글 스타일과 성격을 활용하는 쓰기 기계예요.

구체적으로 말하면, 여러분이 자신의 목소리로 소셜미디어 글이나 기술 기사(이렇게 작성된 것처럼)를 쓰는 AI 판본을 만드실 수 있을 거에요.

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

ChatGPT을 직접 사용하지 않는 이유가 무엇인가요? 궁금하시다면…

LLM을 사용하여 기사나 글을 생성할 때 결과물이 다음과 같은 경향이 있습니다:

- 매우 일반적이고 미흡하게 나옵니다.
- 허상으로 인한 잘못된 정보가 포함될 수 있습니다.
- 원하는 결과를 얻기 위해 번거로운 프롬프팅이 필요할 수 있습니다.

하지만 이런 문제를 해결하기 위해 우리가 할 일은 ↓↓↓

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

먼저, LinkedIn, Medium, Substack 및 GitHub에서 수집한 디지털 데이터로 LLM을 세밀하게 조정할 것입니다.

이를 통해 LLM은 당신의 쓰기 스타일과 온라인 개성과 일치하게 될 것입니다. LLM을 통해 당신 온라인 버전처럼 대화하는 법을 배울 것입니다.

2024년 Meta가 Messenger 앱에서 발표한 AI 캐릭터의 우주를 보신 적이 있나요? 만약 아직이라면, 여기 [2]에서 더 자세히 알아볼 수 있습니다.

어느 정도 그것이 우리가 구축하려는 것입니다.

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

하지만 우리의 사용 사례에서는 당신의 목소리를 반영하고 표현하는 소셜 미디어 게시물이나 글을 쓰는 LLM 쌍에 초점을 맞추겠습니다.

예를 들어, 우리는 당신의 LLM 쌍에게 LLM에 관한 LinkedIn 게시물을 작성하도록 요청할 수 있습니다. LLM에 관한 어떤 일반적이고 표현되지 않은 게시물(예: ChatGPT가 무엇을 할 것인지) 대신에 당신의 목소리와 스타일을 사용할 것입니다.

두 번째로, 우리는 환각을 피하기 위해 외부 정보에 액세스하기 위해 LLM에게 벡터 DB에 액세스할 수 있게 할 것입니다. 따라서 LLM이 구체적인 데이터에 기반하여만 쓸 수 있도록 할 것입니다.

최종적으로, 정보를 얻기 위해 벡터 DB에 액세스하는 것 외에도 생성 프로세스의 기본 블록 역할을 하는 외부 링크를 제공할 수 있습니다.

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

예를 들어, 위의 예시를 다음과 같이 수정해 볼 수 있어요: "이 링크의 기사를 기반으로 LLMs에 관한 1000단어 LinkedIn 게시물을 작성해주세요: [URL]."

기대되시나요? 시작해봅시다!🔥

# 2. 3단계 파이프라인 구조

우리 모두는 머신러닝 시스템이 얼마나 엉망이 될 수 있는지 알고 있어요. 이 때 3단계 파이프라인 구조가 필요해요.

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

3-파이프라인 디자인은 ML 시스템에 구조와 모듈성을 제공하면서 MLOps 프로세스를 개선합니다.

## 문제점

MLOps 도구의 발전에도 불구하고, 프로토타입에서 프로덕션으로의 전환은 여전히 어려움을 겪고 있습니다.

2022년에는 모델 중 54%만이 프로덕션 환경으로 이동한다고 합니다. 우웅.

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

그래서 무슨 일이 생길까요?

입에 먼저 나오는 것은 아마도:

- 모델이 충분히 성숙하지 않다
- 보안 위험(예: 데이터 개인 정보 보호)
- 충분한 데이터가 없다

어느 정도는 이 사실이 맞습니다.

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

그러나 현실은 많은 시나리오에서...

...ML 시스템의 아키텍처는 연구를 염두에 두고 구축되거나 ML 시스템이 오프라인에서 온라인으로 리팩터링하기 매우 어려운 거대한 단일체가 됩니다.

그러므로 좋은 소프트웨어 엔지니어링 프로세스와 명확히 정의된 아키텍처가 적합한 도구와 높은 정확도의 모델 사용만큼 중요합니다.

## 솔루션

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

→ 3-파이프라인 아키텍처

3-파이프라인 디자인이 무엇인지 알아봅시다.

개발 과정을 단순화하고, 당신의 단일 ML 파이프라인을 3가지 구성 요소로 나누는 데 도움이 되는 정신적인 지도입니다:

1. 피처 파이프라인
2. 트레이닝 파이프라인
3. 인퍼런스 파이프라인

...또한 피처/트레이닝/인퍼런스 (FTI) 아키텍처로 알려져 있습니다.

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

#1. 피처 파이프라인은 데이터를 피처와 레이블로 변환하여, 해당 내용을 피처 스토어에 저장하고 버전을 관리합니다. 피처 스토어는 피처들의 중앙 저장소로 기능하며, 피처들은 피처 스토어를 통해서만 액세스하고 공유할 수 있습니다.

#2. 트레이닝 파이프라인은 피처 스토어에서 특정 버전의 피처와 레이블을 가져와서 훈련된 모델 가중치를 출력하며, 이러한 가중치는 모델 레지스트리에 저장되고 버전을 관리합니다. 모델은 모델 레지스트리를 통해서만 액세스하고 공유할 수 있습니다.

#3. 추론 파이프라인은 피처 스토어에서 특정 버전의 피처를 사용하고 모델 레지스트리에서 특정 버전의 모델을 다운로드합니다. 최종 목표는 클라이언트에 예측을 출력하는 것입니다.

이것이 3개의 파이프라인 디자인이 아름다운 이유입니다:

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

- 직관적입니다.
- 모든 머신 러닝 시스템은 이 3가지 구성 요소로 축소될 수 있어 더 높은 수준에서 구조를 가져옵니다.
- 3가지 구성 요소 간에 투명한 인터페이스를 정의하여 여러 팀이 협업하기 쉬워집니다.
- 머신 러닝 시스템은 처음부터 모듈화를 염두에 두고 구축되었습니다.
- 필요에 따라 3가지 구성 요소를 여러 팀 사이로 쉽게 분할할 수 있습니다.
- 각 구성 요소는 작업에 가장 적합한 기술 스택을 사용할 수 있습니다.
- 각 구성 요소는 독립적으로 배포, 확장 및 모니터링할 수 있습니다.
- 피처 파이프라인은 배치, 스트리밍 또는 둘 다로 쉽게 구현할 수 있습니다.

하지만 가장 중요한 이점은...

...이 패턴을 따라가면 여러분의 머신 러닝 모델이 노트북에서 제작 환경으로 옮겨질 것을 100% 확신할 수 있습니다.

↳ 3-파이프라인 디자인에 대해 더 자세히 알고 싶으시다면, FTI 아키텍처의 창시자 중 한 명인 Jim Dowling이 작성한 훌륭한 [3] 글을 추천합니다.

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

# 3. LLM Twin System design

LLM 시스템에 3-파이프라인 아키텍처를 어떻게 적용하는 지 알아봅시다.

LLM twin의 아키텍처는 다음과 같이 4개의 Python 마이크로서비스로 구성됩니다:

- 데이터 수집 파이프라인
- 특징 추출 파이프라인
- 훈련 파이프라인
- 추론 파이프라인

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

보시다시피, 데이터 수집 파이프라인은 3-파이프라인 디자인을 따르지 않습니다. 이게 사실이에요.

그것은 ML 시스템 이전에 위치한 데이터 파이프라인을 나타냅니다.

데이터 엔지니어링 팀이 주로 구현하며, 이 파이프라인은 대시보드 또는 ML 모델을 구축하는 데 필요한 데이터를 수집, 정리, 정규화하고 저장하는 것이 목표입니다.

하지만 작은 팀의 구성원이라고 하면, 데이터 수집부터 모델 배포까지 모든 것을 직접 구축해야 할 수도 있겠죠.

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

그렇기 때문에 데이터 파이프라인이 FTI 아키텍처와 어떻게 잘 맞고 상호 작용하는지를 보여 드리겠습니다. 이제 각 구성 요소를 자세히 살펴봐서 개별적으로 어떻게 작동하고 서로 상호 작용하는지 이해해 보겠습니다. ↓↓↓

## 3.1. 데이터 수집 파이프라인

그 범위는 주어진 사용자의 데이터를 크롤링하는 것입니다:

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

- Medium (기사)
- Substack (기사)
- LinkedIn (게시물)
- GitHub (코드)

각 플랫폼마다 고유하므로, 우리는 각 웹사이트를 위해 다른 Extract Transform Load (ETL) 파이프라인을 구현했습니다.

🔗 ETL 파이프라인에 대한 1분 소요 읽기 [4]

그러나 각 플랫폼에 대한 기본 단계는 동일합니다.

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

따라서 각 ETL 파이프라인에 대해 다음과 같은 기본 단계를 추상화할 수 있습니다:

- 자격 증명을 사용하여 로그인
- Selenium을 사용하여 프로필을 크롤링
- HTML을 구문 분석하기 위해 Beautiful Soup 사용
- 추출된 HTML을 정리하고 표준화
- 정규화된 (그럼에도 불구하고 원시) 데이터를 Mongo DB에 저장

중요 사항: 개인 정보 보호 문제로 인해 대다수 플랫폼에서 다른 사람의 데이터에 접근할 수 없기 때문에 우리는 단지 우리 자신의 데이터만을 수집합니다. 그러나 이는 우리에게 완벽한 선택입니다. LLM 트윈을 구축하기 위해서는 우리 자신의 디지털 데이터만 필요합니다.

왜 Mongo DB를 사용할까요?

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

우리는 텍스트와 같이 구조화되지 않은 데이터를 빠르게 저장할 수 있는 NoSQL 데이터베이스를 원했습니다.

데이터 파이프라인은 피쳐 파이프라인과 어떻게 통신할건가요?

우리는 모든 Mongo DB의 변경 사항을 피쳐 파이프라인에 알리기 위해 Change Data Capture (CDC) 패턴을 사용할 것입니다.

🔗 CDC 패턴에 대한 1분 간의 읽기 [5]

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

CDC를 간략히 설명하자면, 감시자는 Mongo DB에 발생하는 모든 CRUD 작업을 24/7 감지합니다.

감시자는 수정된 내용을 알려주는 이벤트를 발생시킵니다. 이 이벤트를 RabbitMQ 큐에 추가할 거에요.

기능 파이프라인은 계속해서 큐를 듣고, 메시지를 처리하여 Qdrant vector DB에 추가할 거에요.

예를 들어, 우리가 Mongo DB에 새 문서를 작성할 때, 감시자는 새 이벤트를 생성합니다. 이벤트가 RabbitMQ 큐에 추가되고, 최종적으로 기능 파이프라인이 소비하고 처리합니다.

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

이를 통해 Mongo DB와 Vector DB가 항상 동기화되도록 보장할 수 있습니다.

CDC 기술을 사용하면, 일괄 ETL 파이프라인(데이터 파이프라인)에서 스트리밍 파이프라인(특징 파이프라인)으로 전환합니다.

CDC 패턴을 사용하면, Mongo DB와 vector DB 간의 차이를 계산하기 위한 복잡한 일괄 파이프라인을 구현하는 것을 피할 수 있습니다. 이 접근 방식은 대규모 데이터를 처리할 때 빠르게 느려질 수 있습니다.

데이터 파이프라인은 어디에 배포될 것인가요?

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

데이터 수집 파이프라인과 RabbitMQ 서비스는 AWS에 배포될 예정입니다. 또한 MongoDB의 프리미엄 서버리스 버전을 사용할 것입니다.

## 3.2. 기능 파이프라인

기능 파이프라인은 Bytewax를 사용하여 구현되었습니다 (Python 인터페이스를 갖춘 Rust 스트리밍 엔진). 따라서, 우리의 특정 사용 사례에서는 이를 스트리밍 입력 파이프라인으로도 참조할 것입니다.

이것은 데이터 수집 파이프라인과 완전히 다른 서비스입니다.

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

데이터 파이프라인과 어떻게 통신하나요?

이전 설명대로, 기능 파이프라인은 RabbitMQ 큐를 통해 데이터 파이프라인과 통신합니다.

현재, 스트리밍 파이프라인은 데이터가 어떻게 생성되었는지나 어디에서 왔는지에 관심이 없습니다.

그저 특정 큐를 듣고, 그곳에서 메시지를 소비하고 처리해야 한다는 것만 알고 있습니다.

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

이렇게 하면 두 구성 요소를 완전히 분리할 수 있습니다. 미래에는 여러 소스에서 메시지를 큐에 쉽게 추가할 수 있으며, 스트리밍 파이프라인이 이를 어떻게 처리해야 하는지 알게 될 것입니다. 유일한 규칙은 큐에 있는 메시지가 항상 동일한 구조/인터페이스를 준수해야 한다는 것입니다.

기능 파이프라인의 범위는 무엇인가요?

이것은 RAG 시스템의 인계 구성 요소를 나타냅니다.

큐를 통해 전달된 원시 데이터를 가져 와서 다음과 같은 작업을 수행할 것입니다:

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

- 데이터 정리하기;
- 청크로 나누기;
- Superlinked의 임베딩 모델을 사용해 임베딩하기;
- Qdrant 벡터 DB에 로드하기.

각 유형의 데이터(게시물, 기사, 코드)는 각자의 클래스 세트를 통해 독립적으로 처리됩니다.

모두 텍스트 기반이지만, 각 데이터 유형마다 독특한 특징이 있기 때문에 데이터를 정리, 청크화, 임베딩하는 데 각기 다른 전략을 사용해야 합니다.

어떤 종류의 데이터가 저장될 것인가요?

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

학습 파이프라인은 피쳐 스토어에만 액세스할 수 있습니다. 우리의 경우, Qdrant 벡터 DB로 표현됩니다.

벡터 DB는 NoSQL DB로 사용할 수도 있다는 것을 기억하세요.

이 두 가지를 염두에 두고, 우리는 Qdrant에 데이터의 2개 스냅샷을 저장할 것입니다:

1. 정제된 데이터(인덱스로 벡터를 사용하지 않고 NoSQL 방식으로 저장).

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

2. 정리된, 청크 처리된 및 내장된 데이터 (Qdrant의 벡터 인덱스를 활용)

학습 파이프라인은 표준 및 보강 프롬프트에서 LLM을 세밀 조정하고자 하므로 두 형식의 데이터에 액세스해야 합니다.

정리된 데이터로 프롬프트 및 답변을 생성할 것입니다.

청크 처리된 데이터로는 프롬프트를 보강할 것입니다 (일명 RAG).

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

스트리밍 파이프라인을 배치 파이프라인 대신 구현해야 하는 이유는 무엇인가요?

그 이유는 주로 2가지가 있습니다.

첫 번째 이유는 CDC 패턴과 결합해서 서로 다른 두 개의 데이터베이스를 동기화하는 가장 효율적인 방법이기 때문입니다. 그렇지 않으면 대규모 데이터를 처리할 때 확장 가능하지 않은 배치 폴링 또는 푸싱 기술을 구현해야 할 수 있습니다.

CDC + 스트리밍 파이프라인을 사용하면 소스 데이터베이스의 변경 사항만 처리하고 여분의 작업이 발생하지 않습니다.

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

두 번째 이유는 그렇게 함으로써 소스와 벡터 데이터베이스가 항상 동기화된 상태가 유지됩니다. 따라서 RAG를 수행할 때 최신 데이터에 항상 액세스할 수 있습니다.

왜 Bytewax를 사용해야 하는가?

Bytewax는 Python 인터페이스를 노출하는 Rust로 구축된 스트리밍 엔진입니다. Bytewax를 사용하는 이유는 Rust의 놀라운 속도와 신뢰성을 파이썬의 사용 편의성과 생태계와 결합시킨 점에 있습니다. Python 개발자에게는 매우 가볍고 강력하며 사용하기 쉽습니다.

기능 파이프라인은 어디에 배포될 것인가요?

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

기능 파이프라인이 AWS에 배포될 예정입니다. 또한 우리는 Qdrant의 무료 서버리스 버전을 사용할 것입니다.

## 3.3. 훈련 파이프라인

훈련 기능에 접근할 수 있는 방법은 무엇인가요?

3.2절에서 강조한 대로, 모든 훈련 데이터는 기능 저장소에서 접근할 수 있습니다. 저희 경우에는 기능 저장소인 Qdrant 벡터 DB에 다음과 같은 데이터가 포함됩니다:

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

- 우리는 프롬프트 및 답변을 생성할 정돈된 디지털 데이터를 사용할 것입니다.
- RAG를 위해 청크와 임베디드된 데이터를 사용하여 정돈된 데이터를 보완할 것입니다.

우리는 주요 데이터 유형(게시물, 기사, 코드)마다 다른 벡터 DB 검색 클라이언트를 구현할 것입니다.

각 유형의 고유한 특성 때문에 벡터 DB를 쿼리하기 전에 각 유형을 다르게 전처리해야 합니다.

또한, 우리는 각 클라이언트에 대해 벡터 DB에서 어떤 것을 쿼리하고 싶은지에 기반한 사용자 정의 동작을 추가할 것입니다. 그러나 이에 대해 자세히는 해당 레슨에서 설명하겠습니다.

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

훈련 파이프라인은 무엇을 할까요?

훈련 파이프라인에는 벡터 DB로부터 검색된 데이터를 전처리하여 프롬프트로 변환하는 데이터-투-프롬프트 레이어가 포함되어 있습니다.

또한 HuggingFace 데이터셋을 입력으로 사용하고 QLoRA를 사용하여 주어진 LLM(예: Mistral)을 세밀하게 튜닝하는 LLM 세밀 조정 모듈이 포함될 것입니다. HuggingFace를 사용함으로써 다양한 LLM 사이를 쉽게 전환할 수 있기 때문에 특정 LLM에 너무 많은 집중이 필요하지 않습니다.

모든 실험은 Comet ML의 실험 추적기에 로그가 남겨질 것입니다.

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

저희는 미세 조정된 LLM의 결과를 평가하기 위해 더 큰 LLM(예: GPT4)을 사용할 것입니다. 이러한 결과는 Comet의 실험 추적기에 기록될 것입니다.

생산용 후보 LLM은 어디에 저장될까요?

우리는 여러 실험을 비교한 뒤 최적의 결과를 선택하여 모델 레지스트리를 위한 LLM 생산용 후보를 발표할 것입니다.

이후, Comet의 프롬프트 모니터링 대시보드를 사용하여 LLM 생산용 후보를 수동으로 검토할 것입니다. 최종 수동 검사가 통과되면, 우리는 모델 레지스트리의 LLM을 수락된 상태로 표시할 것입니다.

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

CI/CD 파이프라인이 실행되어 새로운 LLM 버전이 추론 파이프라인에 배포될 것입니다.

훈련 파이프라인은 어디에 배포될까요?

훈련 파이프라인은 Qwak에 배포될 예정입니다.

Qwak은 ML 모델을 훈련하고 배포하는 서버리스 솔루션입니다. 작업 확장을 쉽게 할 수 있으며 건설에 집중할 수 있습니다.

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

위 내용은 다음을 위해 Comet ML의 프리미엄 버전을 사용할 것입니다:

- 실험 추적기;
- 모델 레지스트리;
- 실시간 감시.

## 3.4. 추론 파이프라인

추론 파이프라인은 LLM 시스템의 최종 구성 요소입니다. 클라이언트가 상호 작용할 구성 요소입니다.

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

이것은 REST API 아래에 랩핑될 것입니다. 클라이언트들은 HTTP 요청을 통해 이를 호출할 수 있으며, 이는 ChatGPT나 비슷한 도구들과 유사한 경험입니다.

기능에 어떻게 접근할까요?

기능 저장소에 접근하기 위해, 훈련 파이프라인에서와 같이 Qdrant 벡터 DB 검색 클라이언트들을 사용할 것입니다.

이 경우 RAG를 수행하기 위해 청크 데이터에 접근하는데 기능 저장소가 필요할 것입니다.

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

얼마나 미세 조정된 LLM에 액세스할 수 있나요?

미세 조정된 LLM은 항상 해당 태그(예: accepted)와 버전(예: v1.0.2, latest 등)을 기반으로 모델 레지스트리에서 다운로드됩니다.

미세 조정된 LLM은 어떻게 로드되나요?

우리는 여기서 추론 세계에 있습니다.

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

LLM의 속도와 메모리 사용량을 최대한 최적화하려고 합니다. 그래서 모델 레지스트리에서 LLM을 다운로드한 후 양자화할 것입니다.

추론 파이프라인의 구성 요소는 무엇인가요?

첫 번째는 RAG를 수행하기 위해 벡터 데이터베이스에 액세스하는 검색 클라이언트입니다. 이는 교육 파이프라인에서 사용된 모듈과 동일합니다.

Qdrant에서 검색된 도큐먼트를 프롬프트로 매핑할 쿼리가 있으면 해당 쿼리 후보를 매핑해줄 계층이 있습니다.

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

LLM이 답변을 생성한 후, 우리는 Comet의 프롬프트 모니터링 대시보드에 기록하고 클라이언트에게 반환할 것입니다.

예를 들어, 클라이언트는 추론 파이프라인에 다음을 요청할 수 있습니다:

"LLM에 관한 1000단어의 LinkedIn 게시물 작성", 그리고 추론 파이프라인은 생성된 게시물을 반환하기 위해 위에서 설명한 모든 단계를 거칠 것입니다.

추론 파이프라인은 어디에 배포될 것인가요?

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

추론 파이프라인은 Qwak로 배포됩니다.

기본 설정으로, Qwak은 자동 확장 솔루션과 생산 환경 자원을 모니터링하는 멋진 대시보드도 제공합니다.

훈련 파이프라인에 대해서는, 우리는 실시간 모니터링 대시보드를 제공하는 Comet의 서버리스 프리미엄 버전을 사용할 것입니다.

# 결론

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

LLM Twin의 무료 코스 1번째 기사입니다.

이 강의에서는 이 코스 동안 구축할 내용을 소개했습니다.

간단히 ML 시스템을 설계하는 방법에 대해 논의한 후

최종적으로 이 코스의 시스템 디자인을 살펴보고 각 마이크로서비스의 아키텍처 및 서로 상호작용 방법을 소개했습니다.

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

- 데이터 수집 파이프라인
- 피쳐 파이프라인
- 훈련 파이프라인
- 추론 파이프라인

제2 장에서는 데이터 수집 파이프라인을 더 자세히 살펴보고, 다양한 소셜 미디어 플랫폼에 크롤러를 구현하는 방법을 배우고, 수집한 데이터를 정리하여 Mongo DB에 저장하고, 마지막으로 AWS에 배포하는 방법을 안내해 드릴 거에요.

이 기사를 즐겁게 보셨나요? 그렇다면...

↓↓↓

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

5천 명 이상의 엔지니어들과 함께하여, 프로덕션급 머신 러닝에 대한 검증된 컨텐츠를 살펴보세요. 매주 업데이트되는 콘텐츠를 놓치지 마세요:

# 참고문헌

[1] 당신의 LLM 트윈 코스 — GitHub 저장소 (2024년), Decoding ML GitHub 조직

[2] Meta에서 새로운 AI 경험 소개(2023년), Meta

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

- [3] Jim Dowling, From MLOps to ML Systems with Feature/Training/Inference Pipelines (2023), Hopsworks

- [4] Extract Transform Load (ETL), Databricks Glossary

- [5] Daniel Svonava and Paolo Perrone, Understanding the different Data Modality / Types (2023), Superlinked
