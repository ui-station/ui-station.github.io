---
title: "요구사항 및 API 분석하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-RequirementsAPIAnalysis_0.png"
date: 2024-06-22 23:43
ogImage:
  url: /assets/img/2024-06-22-RequirementsAPIAnalysis_0.png
tag: Tech
originalTitle: "Requirements , API: Analysis"
link: "https://medium.com/analysts-corner/requirements-api-analysis-df62b5808fa2"
---

이전 장: https://medium.com/analysts-corner/requirements-api-definitions-3f75a7308ae6

이 장에서는 먼저 API에 대한 요구 사항이 필요한 이유와 요구 사항 분류 프레임워크에서의 위치를 이해할 것입니다. 그런 다음 요구 사항 엔지니어링 프로세스 및 고려해야 할 구체적인 측면을 검토할 것입니다.

# 왜 API를 위한 요구 사항이 필요한가요?

API 요구 사항에 대해 비즈니스 분석가들이 갑자기 작업을 시작하는 이유에 대해 답해보겠습니다. 약 10년 전, IT에서 비즈니스 분석가로서 경력을 시작했을 때, 일반적인 BA 책임의 일부가 아니었습니다. 지금은 더 많은 BAs, POs 및 PMs의 채용 공고에서 API 지식이 필요하다는 것을 알 수 있습니다. 그리고 그런 일이 일어난 이유에 대한 주관적 설명이 있습니다.

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

당시 소프트웨어 아키텍처 접근 방식은 온프레미스 배포된 단일 애플리케이션이었습니다. 간단히 말해, 그것은 데스크톱이나 웹 UI 클라이언트와 통신하는 백엔드 서비스가 함께하는 형태였습니다. 해당 백엔드는 FTP를 통해 파일 업로드와 같은 여러 내부 또는 외부 서비스와 통합되었습니다.

비즈니스 분석가는 비즈니스 능력 및 그들이 UI 측에서 나타나는 방식에 집중했습니다. 또한 매핑 및 요청 논리와 같은 통합을 위한 요구 사항을 다루었습니다. 백엔드, UI 클라이언트 및 통합 시스템 사이의 상세 상호작용은 기술 담당자의 책임이었습니다.

그러나 클라우드, SaaS 및 마이크로서비스 아키텍처(MSA)가 시스템 디자인의 주요 트렌드로 부상하면서 상황이 변하기 시작했습니다:

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

최근의 트렌드로 인해 시스템끼리의 연결이 이전보다 더 많아졌습니다. 일부 서비스는 API를 우선으로 하는데, 이는 API를 통해서만 사용할 수 있다는 뜻입니다. 그러므로 API는 더 높은 요구사항을 갖는 비즈니스 모델이 되었습니다. 이에 따라 이해관계자, 규정, 제한 등이 발생하며, 비즈니스 분석가, 제품 소유자, 제품 관리자들이 이를 책임집니다.

API 클라이언트의 수가 웹, 모바일 어플리케이션, 사물 인터넷 등을 포함하여 급격히 증가했습니다. 게다가, 마이크로서비스는 시스템 내에서 서로 API를 통해 통신합니다. 그 결과, 각각의 이해관계자와 특정 요구사항을 갖춘 수직 및 수평 방향의 다양한 외부 및 내부 클라이언트들이 존재합니다.

또 다른 중요한 측면은 API가 시스템에 침입할 수 있는 직접적인 경로를 제공하기 때문에 공격에 취약하다는 점입니다. 그러므로, API를 디자인하고 구현하는 것 뿐만 아니라 그들의 보안을 보장하는 것이 중요합니다.

요약하자면, 클라우드, MSA, SaaS의 부상으로 인한 API의 "비즈니스화", 클라이언트 수의 증가, 높은 보안 위험, 그리고 산업 및 정부의 규정은 API를 비즈니스 분야의 모든 종류의 사람들에게 관심을 가지게 만들었습니다.

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

그것은 그 변화에 대한 철저한 설명은 아니지만, 나의 시각에서 그렇게 보입니다. 댓글에 당신의 생각을 보고 싶어요.

# 요구 사항 분류

BA 이론에 뛰어들기 전에, API 요구 사항이 기능적이지 않다는 점을 명확히 해보죠. UI가 가장 가까운 비유입니다. 시스템 기능(기능)과 사용자가 그에 접근하는 여러 가지 방법이 있습니다. 이전 장에서, 우리는 API를 배포 채널로 확인했습니다. 그리고 이것은 UI, Command-line interface (CLI), Chatbot 등 모든 공개 인터페이스에 대해 동일합니다.

API 디자인을 통해 기능 요구 사항을 표현할 수 있습니다. 그러나 API 자체는 시스템 기능의 직접적인 관심사가 아닙니다. UI 비유로 돌아가보죠: 디자인 패턴, 사용할 컴포넌트, 또는 색 구성표는 UX에 중요하지만, 우리는 그것들을 기능 요구 사항이라고 부르지 않습니다.

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

예를 들어, "시스템은 X로 필터링된 항목 목록을 반환하고 Z로 정렬한다"는 기능 요구 사항입니다. 그러나 이 목록을 UI와 API로 어떻게 반환하는지는 다를 수 있습니다. 단일 프런트 엔드가 그 API 엔드포인트를 활용하는 경우, UI 관점에서 설명하는 것만으로 충분합니다. 이 단일 클라이언트가 여기서 주요 이해관계자 역할을 합니다. 그러나 여러 클라이언트가 목록을 사용하려면, 그들과 소통하고 그들이 얻어야 하는 것과 이유를 맞추는 데 추가 시간을 투자해야 합니다. 그 결과는 UI에 목록 항목을 어떻게 반환하는지와는 크게 다를 수 있습니다.

API 요구 사항은 기능적이 아니기 때문에 어떤 유형의 요구 사항에 속하는 것일까요? 몇 가지 출처를 살펴봅시다.

켐 웨거스(K. Wiegers)와 C. 호캔슨(C. Hokanson)의 최근 "소프트웨어 요구 사항 필수"에서는 "외부 인터페이스 요구 사항"을 정의합니다.

K. Wiegers와 J. Beatty의 고전적인 "소프트웨어 요구 사항" 3판은 비기능적 요구 사항과 인터페이스 간의 상호 연결성을 강조합니다.

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

ISO/IEC/IEEE 29148:2018 표준 "시스템 및 소프트웨어 공학 - 수명 주기 프로세스 - 요구 공학"에 따르면 Interface requirements(인터페이스 요구 사항)이라는 요구 유형이 있습니다(5.2.8.3):

인터페이스 요구 사항은 비기능적인 것이 아닙니다. 왜냐하면 인터페이스는 자체적인 품질 속성을 가지고 있기 때문입니다. 보안, 호환성, 사용성, 규정 준수 등이 그에 해당합니다. 인터페이스는 특정 기능의 품질 속성에 불과하지 않습니다. 그러나 엉망인 인터페이스는 사용자 경험을 저하시킬 수 있습니다, UI 또는 API에 대한 이야기를 할 때도 그렇습니다.

또한 선택된 기술과 디자인 패턴은 인터페이스에 대한 제약 사항을 내포하고 있습니다. 따라서 소프트웨어 아키텍처는 분석 프로세스에 영향을 미치며, 특히 API의 경우에 그렇습니다. 예를 들어, API가 동기화되어야 하는지 비동기화되어야 하는지(이벤트 기반)는 아키텍처 및 사용된 프로토콜에 따라 달라집니다.

인터페이스 요구 사항(여기에 UI 및 CLI가 포함됨)에서는 시스템이 소비자로서 동작할 때 통합 요구 사항을 분리하는 것이 합리적입니다. 제조자인 경우 API 요구 사항(이전 섹션을 참조하십시오)은 다소 흥미로운 결과를 나타냅니다. 후자의 결과는 운영 API 엔드포인트를 의미합니다. 한편, 시스템 간의 작업 상호 작용은 첫 번째 결과입니다.

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

# 요구 엔지니어링

BABOK v3 (IIBA, 2015)은 API 또는 통합 요구 사항을 분류하지 않습니다. 하지만 API 및 일반적인 인터페이스에 대해 이야기하는 인터페이스 분석 기법 (10.24)을 설명합니다. 이 분석은 다음과 같은 3단계로 구성됩니다:

- 상호작용 주체의 상호작용을 연구하여 어떤 인터페이스가 필요한지 식별하고 이해하기 위해 준비 작업을 수행합니다.
- 각 향후 인터페이스의 기능을 설명하고 인터페이스 유형과 초기 설계를 평가하여 식별 작업을 수행합니다.
- 입력, 출력, 검증 등을 포함한 인터페이스를 정의합니다.

요구 사항 개발은 "전통적인" 비즈니스 분석 프로세스와 크게 다르지 않습니다. 요구 수집, 분석, 명세 및 검증 단계가 있습니다. 현재 우리는 주로 첫 단계에 주안점을 두고 있습니다. 명세와 추가적인 검증은 어떤 특징을 가지고 있지만, HTTP 프로토콜 기본 및 API 구조에 대해 자세히 알아볼 필요가 있습니다. 이 내용은 이후 챕터에서 다룰 것입니다.

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

# 시작점

여기에 고려해야 할 많은 질문들이 있어요:

- 이게 왜 필요한가요?

예를 들어, 저희는 제품을 통해 생성되는 데이터를 UI가 아닌 유료 공개 API로 외부의 제3자들이 얻을 수 있는 접속점을 만들고 싶어해요. 간단히 말해, 새로운 유통 채널을 추가하고 수익을 창출하고 싶어요.

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

- 누구를 위해 이것을 하는 건가요?

예를 들면, 의료 데이터 브로커는 우리의 데이터를 활용하여 시장 예측 모델을 구축할 것입니다.

- 이것이 무엇을 할까요?

예를 들면, 제공된 기간에 대한 세트 구매 기록을 반환할 것입니다.

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

- 이들이 어떻게 할 것인가요?

예를 들어, 토큰으로 승인하고 사업 ID와 시작 및 종료 날짜를 제공합니다.

## 선행 요건

다음 단계는 API 호출의 선결 조건을 정의하는 것입니다 — 인증 및 권한 부여:

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

- 인증은 클라이언트를 확인하고 API와 통신할 수 있도록 허용하는 것을 의미합니다.
- 인가는 특정 클라이언트가 특정 API 요청을 수행하고 특정 데이터를 볼 수 있도록 권한을 부여하는 것입니다.
- 또 다른 중요한 포인트는 소비자가 호출을 수행하는 데 필요한 모든 입력 데이터를 가지고 있는지 여부입니다.
- 새로운 API 엔드포인트에 관련된 새 권한이 필요한지 또는 기존 권한을 재사용해야 하는지를 이해해야 합니다.

# 기능적 차이

또 다른 중요한 질문은 시스템이 이미 그러한 기능을 제공하는지여부입니다. 그렇다면 어떤 개선이 필요한지 알아봐야 합니다.

현재 시스템 기능과 원하는 인터페이스 간에 차이가 있다면, 해결책 요구 사항의 문제입니다. 그래서 전통적인 BA(비즈니스 분석) 방법을 사용해야 합니다.

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

예를 들어, 새로운 API 엔드포인트에 대한 새로운 검색 기준이 있다면, 해당 기준이 검색 인덱스에 포함되도록 해야 합니다. 그것은 이미 백엔드와 관련된 문제입니다.

인터페이스를 통해 해결책을 설명하려면, 모든 측면을 다루기에는 충분하지 않을 것입니다. 최소한 데이터 모델인 데이터 사전이나 엔터티 관계도를 갖고 새 API가 사용될 장소를 설명하는 좋은 사용 사례가 필요합니다.

과제는 한 개 또는 여러 API 엔드포인트가 활용될 사용 사례를 확인하고 설명하는 것입니다. 요즘에는 API가 매우 촘촘하기 때문에 단일 API만으로는 맥락이 명확하지 않습니다. 따라서 특정 엔티티와 관련된 특정 작업이 여러 API 호출로 이루어지는 시나리오가 더 낫습니다.

# 계약 및 검은 상자

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

API를 단순하게 살펴보면 다음과 같이 보일 수 있어요

![API Analysis](/assets/img/2024-06-22-RequirementsAPIAnalysis_1.png)

분석의 결과는 설명된 입력/요청 구조와 결과/응답입니다. 이는 우리가 API 계약이라고도 부를 수 있는 블랙 박스를 나타냅니다.

- API 계약은 생산자와 소비자 간의 기대되는 입력과 결과에 대한 합의입니다.
- 계약은 엄격하게 형식화되며, 클라이언트는 API를 사용하기 시작할 때 이를 준수하기로 동의합니다.
- API 생산자는 계약의 일관성을 유지하는 책임을 집니다.
- API 계약을 어기면 법적인 처벌이 가해질 수 있습니다.
- API 계약은 구현 전에 정의될 수 있어서 양쪽이 병행하여 작업할 수 있어요.

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

거기 중요한 두 가지가 있어요:

- 요청/응답 모델, 즉 API 모델은 데이터베이스의 데이터 모델과 동일하지 않아요. API는 데이터베이스 위에 구축된 추상화로, 외부 세계와의 상호 작용을 보호하는 역할을 해요. 모델은 이름, 노출된 속성 집합, 그리고 일부 데이터 유형에서도 차이가 있을 수 있어요.
- 새로운 API는 기존 시스템 API와 일관성을 유지해야 해요. 특정한 방식으로 불리우는 것이 있다면, 그에 따라야 해요. 가능한 혼란을 피하고 소비자들의 인지 부하를 줄일 수 있도록 도와줄 거예요.

API 모델은 다음과 같이 설명할 수 있어요:

- 공식적인 방법으로는 XML 및 JSON과 같은 데이터 형식 또는 OpenAPI와 같은 명세 형식이 있어요. OpenAPI는 HTTP에 대한 가장 일반적인 공식 API 명세 형식이에요.
- 비공식적인 방법으로는 Excel 스프레드시트나 텍스트 형식 등이 있을 수 있어요. 비공식 포맷은 일반적으로 OpenAPI를 모방하지만 보다 사용자 친화적인 방식으로 표현돼요.

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

요청 로직은 블랙 박스(글래스 박스) 내부에서 무엇이 발생하는지를 처리합니다. 이것은 다음과 같이 설명될 수 있습니다:

- 비공식적으로(여러 단계를 포함한 텍스트 설명)
- 시각적으로: UML 활동 또는 순서 다이어그램 등
- 형식화된 명세서 형태

요청 로직은 Entity 객체에 대한 간단한 CRUD 명령일 수도 있습니다. 또한 몇 개의 내부 API가 관련된 정교한 집계된 요청일 수도 있습니다. 이 내용은 이후 장에서 다루겠습니다.

# 너무 촉박하지 않게요

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

UI 작업을 하면 API보다 변경 사항을 소개하기가 쉽습니다. 사용자 경험을 파괴할 위험이 있지만, 일반적으로 사용자가 새로운 것에 적응하는 데 들어가는 인지적 노력에 관한 문제입니다. 그들은 당신의 변화에 대해 돈을 내지 않습니다.

반면에 소비자들은 사용 중인 API의 파괴적인 변경 사항에 적응하거나 새로운 것으로 마이그레이션하는 데 대가를 지불합니다. 클라이언트 코드 변경부터 자동 테스트, 문서 업데이트 등 추가적인 통합 및 유지 보수 노력이 필요합니다.

따라서 모든 "반복적 유연성"은 API에 대해서는 그리 많이 다루지 않습니다. 그들은 분명히 자신들의 수명 주기 동안 진화합니다. 그러나 API 계약을 자주 파괴하는 것은 지속 가능성을 달성하는 좋은 방법이 아닙니다. 이를 완화하는 방법이 있지만, 확실히 모든 API 생산자가 이를 염두에 둬야 합니다.

요구 엔지니어링 관점에서, 특히 여러 소비자가 사용할 유료 공개 API인 경우에는 보다 철저한 분석이 필요합니다.

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

# 주제에 대해 읽기

- API 요구 사항의 정의
- 비기능 요구 사항 및 API
- 파괴적인 변경 및 역 하위 호환성
- IIBA 벨라루스의 "요구 사항 및 API" 웨비나 요약

다음 장: 구조 (진행 중)

원문 게시물: https://ilyazakharau.com.
