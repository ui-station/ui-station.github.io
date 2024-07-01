---
title: "API를 활용한 증분 데이터 로딩 스크립트를 작성할 때 반드시 해결해야 할 3가지 필수 질문"
description: ""
coverImage: "/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_0.png"
date: 2024-07-01 15:56
ogImage: 
  url: /assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_0.png
tag: Tech
originalTitle: "3 Essential Questions to Address When Building an API-Involved Incremental Data Loading Script"
link: "https://medium.com/towards-data-science/3-essential-questions-to-address-when-building-an-api-involved-incremental-data-loading-script-03723cad3411"
---


# 개요

- 애플리케이션(예: 광고 성과, 판매 수치 등)에서 데이터를 데이터베이스로 동기화하려고 합니다.
- 애플리케이션은 데이터를 검색하기 위한 API 엔드포인트를 제공합니다.
- 데이터는 매일 데이터베이스로 동기화되어야 합니다.
- 데이터베이스에는 ❗"새로운" 데이터(또는 변경 사항)❗ 만로드하려고 합니다. 동기화할 때 매번 전체 데이터 집합을 다시로드하고 싶지 않습니다.

여러분은 Python에서 어떻게 처리할 것인가요?

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 💥 질문 1: 데이터를 점진적으로 동기화하기 위해 API로부터 어떤 것이 필요한가요?

점진적으로 로딩하는 스크립트를 개발하기 전에, 작업 중인 API 엔드포인트의 동작을 이해해야합니다.

❗모든 API가 점진적으로 로딩을 지원할 수 있는 것은 아닙니다.

## 👉 답변: 점진적 로딩을 지원하는 쿼리 매개변수

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

판매 실적을 추적하는 응용 프로그램(또는 "소스" 응용 프로그램)의 예제를 살펴보겠습니다. 이 응용 프로그램에서 각 레코드는 제품과 해당 판매량을 나타냅니다. 생성된 필드(created_at)와 업데이트된 필드(updated_at)는 레코드가 생성 및 업데이트된 시기를 나타냅니다.

판매 데이터의 변경 사항은 일반적으로 두 가지 주요 방식으로 발생합니다:

- 새 제품이 목록에 추가됩니다.
- 기존 레코드의 판매 데이터가 업데이트되어, updated_at에 새 값이 생성됩니다. 이를 통해 새 변경 사항을 추적할 수 있습니다. 이 정보가 없으면 어떤 레코드가 수정되었는지 알 수 없습니다.

👁️👁️ 아래는 소스 응용 프로그램의 데이터베이스에서의 예제 판매 테이블입니다.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

↪️ 어제의 데이터: 2개의 레코드

![어제의 데이터](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_0.png)

↪️ 오늘의 데이터: 새 레코드 추가 및 기존 레코드 수정

![오늘의 데이터](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_1.png)

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

🟢 핵심 내용: 만약 API 엔드포인트가 updated_at 매개변수를 기반으로 쿼리를 허용한다면, 이전 동기화에서 저장된 가장 최근 updated_at 값보다 나중에 갱신된 updated_at 값을 가진 레코드만 검색하기 위해 요청을 보내는 방식으로 점진적으로 데이터를 로드할 수 있습니다. 이 문맥에서 updated_at은 점진적 커서로 참조되며, 그 값은 다음 동기화까지 유지되는 상태로 알려져 있습니다.

updated_at 필드는 점진적 커서로 일반적으로 선택됩니다. id나 sales와 같은 다른 쿼리 매개변수는 마지막 동기화 이후 추가되거나 업데이트된 레코드를 알려주지 못하기 때문에 데이터를 점진적으로 요청하는 데 도움을 줄 수 없습니다.

어떤 쿼리 매개변수가 데이터를 점진적으로 로드하는 데 필요한가요?

![이미지](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_2.png)

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 🤷 페이지네이션 메커니즘

API는 성능을 향상하기 위해 결과를 작은 단위로 반환하는 경우가 많습니다. 예를 들어, 한 번에 10,000 개의 레코드를 반환하는 대신 API는 각 요청당 최대 100 개의 레코드로 응답을 제한할 수 있으며, 여러 일괄처리를 거쳐 순환해야 합니다.

이를 관리하기 위해 전형적으로 (항상 그렇지는 않음) 두 개의 쿼리 매개변수인 limit과 skip(또는 offset)을 사용해야 합니다.

간단한 예제를 통해 설명해드리겠습니다:

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

첫 번째 요청사항:

- limit=100
- skip=0

두 번째 요청사항, 이미 동기화한 처음 100개의 레코드를 건너뛰기 위해:

- limit=100
- skip=100

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

이 패턴은 각 배치마다 제한을 따라 건너뛰기 값을 증가시키면서 모든 레코드가 검색될 때까지 계속됩니다.

🟢 인사이트: API가 응답을 반환하는 방식을 이해해야 누락된 레코드가 발생하지 않습니다. API가 페이지네이션을 관리하는 데 사용할 수 있는 방법은 skip 및 offset이외에도 다양합니다. 하지만 이는 다음에 이야기할 주제입니다.

## 🤷 경로 매개변수

경로 매개변수는 API의 URL에 직접 포함되며 일반적으로 데이터의 다른 세그먼트(파티션)를 구별하는 데 사용됩니다. 예를 들어, 마케팅 계정 내의 다양한 캠페인을 지정하거나 소스 애플리케이션에서 관리되는 다른 하위 계정을 지정할 수 있습니다.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

아래 예시에서는 경로 파라미터로 applicationId와 campaignId가 사용됩니다.

https://yourbaseurl.myapp/v1/applications/'applicationId'/campaigns/'campaignId'/sales

🟢 요점: 동일한 API에서 다른 경로 파라미터를 사용해 데이터를 동기화할 때, 하나의 테이블에 동기화할지 아니면 별도의 테이블에 동기화할지를 결정해야 합니다 (sales_campaign_1, sales_campaign_2 등).

# 💥 질문 2: 추출된 레코드를 목적지 테이블에 어떻게 쓰고 싶습니까?

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

이제 위에서 언급한 매개변수를 사용하여 API 요청을 통해 여러 레코드를 이미 추출했다고 가정해 봅시다. 이제 목적지 테이블에 어떻게 쓸지를 결정할 때입니다.

## 👉 답변: 병합/중복 제거 모드 (권장)

이 질문은 Write disposition 또는 Sync 모드의 선택과 관련이 있습니다. 즉시 대답하면, 데이터를 점진적으로 로드하려고 한다면 추출한 데이터를 추가 모드 또는 병합 모드(중복 제거 모드로도 알려짐) 중 하나로 쓰는 것이 좋을 것입니다.

그러나 우리는 옵션을 더 신중히 살펴보고 점진적으로 로드하기에 가장 적합한 방법이 무엇인지 결정해 봅시다.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

인기있는 라이트 배치 방법은 다음과 같아요.

- 🟪 덮어쓰기/대체: 대상 테이블의 모든 기존 레코드를 삭제한 후 추출된 레코드를 삽입해요.
- 🟪 추가: 추출된 레코드를 단순히 대상 테이블에 추가해요.
- 🟪 병합/중복제거: 새로운(*) 레코드를 삽입하고 기존 레코드를 업데이트해요.

(*) 어떻게 새 레코드를 알 수 있을까요?: 보통, 주요 키를 사용하여 그것을 결정해요. dlt를 사용한다면, 병합 키와 주요 키를 구분하는 병합 전용키와 중복 제거 전 병합을 위한 중복 제거 정렬 등을 포함한 더 정교한 병합 전략을 사용할 수 있어요. 이 부분은 다른 튜토리얼에서 다루기로 해요.

(**) 이것은 간단한 설명이에요. 더 많은 정보를 원하시면 dlt가 이 병합 전략을 어떻게 처리하는지 자세히 알고 싶다면 여기를 더 읽어보세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

👁️👁️ 안녕하세요! 다양한 쓰기 방식 결과를 이해하는 데 도움이 되는 예제를 준비해봤어요.

↪️ 2024.06.19: 첫 번째 동기화를 수행했어요.

🅰️ 소스 애플리케이션의 데이터

![이미지](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_3.png)

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

🅱️ 데이터가 목적지 데이터베이스로로드되었습니다.

어떤 동기화 전략을 선택하더라도 목적지의 테이블은 원본 테이블의 복사본입니다.

![이미지](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_4.png)

2개의 레코드를 동기화한 결과, 최신 업데이트 날짜인 2024-06-03의 상태가 저장되었습니다.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

↪️ 2024.06.2일: 두 번째 동기화를 진행합니다.

🅰️ 소스 응용 프로그램의 데이터

![이미지](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_5.png)

✍️ 소스 테이블의 변경 사항:

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 레코드 id=1이 업데이트되었습니다 (매출액).
- 레코드 id=2가 삭제되었습니다.
- 레코드 id=3이 삽입되었습니다.

이번 동기화에서는 **updated_at**이 2024년 6월 3일인 레코드만 추출합니다 (마지막 동기화 상태로 저장됨). 따라서 레코드 id=1 및 id=3만 추출됩니다. 소스 데이터에서 레코드 id=2가 제거되었기 때문에 이 변경사항을 인식할 방법이 없습니다.

두 번째 동기화 시, 이제 쓰기 전략 간 차이를 확인할 수 있습니다.

🅱️ 목적지 데이터베이스에 데이터가 로드되었습니다

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

❗ 시나리오 1: 덮어쓰기

![image](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_6.png)

이번에 추출된 2개의 레코드로 대상 테이블이 덮어쓰여질 것입니다.

❗ 시나리오 2: 추가하기

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

![img1](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_7.png)

추출된 2개의 레코드가 대상 테이블에 추가됩니다. 기존 레코드는 영향을 받지 않습니다.

❗ 시나리오 3: 병합 또는 중복 제거

![img2](/assets/img/2024-07-01-3EssentialQuestionstoAddressWhenBuildinganAPI-InvolvedIncrementalDataLoadingScript_8.png)

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

id가 1 및 3인 레코드 2개가 추출되어 대상에 있는 기존 레코드를 대체합니다. 이 과정을 병합 또는 중복 제거라고 합니다. 대상 테이블의 id가 2인 레코드는 그대로 유지됩니다.

🟢 포인트: 병합(중복 제거) 전략은 증분 데이터 로드 파이프라인에서 효과적일 수 있지만, 테이블이 매우 크면 이러한 중복 제거 과정에 상당한 시간이 소요될 수 있습니다.

# 💥 질문 3: 코드에서 어떻게 구현할까요?

## 👉 답변: dlt — 경량이며, 문서화가 잘 되어 있으며, 지원을 위해 활성화된 커뮤니티가 있는 것을 이용하세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

다이어그램을 참조하여 상위 수준을 확인한 후 아래에 자세한 설명과 함께 코드로 들어가보세요.

빠른 참고: dlt는 구조화를 위해 용어 source와 resources를 사용합니다. 리소스는 일반적으로 API 엔드포인트에 해당하며 데이터를 대상 데이터베이스의 테이블에 작성합니다. 소스는 리소스의 집합입니다.

아래 그림에서 우리가 논의한 두 가지 질문에 대한 답을 볼 수 있습니다:

- 질문 1에 대한 답변: 날짜 커서를 사용하여 API 엔드포인트에 요청을 보내 데이터를 점진적으로 가져옵니다 (그리고 점진적인 실행을 위해 커서 값을 지속적으로 유지하는 상태로도 알려져 있습니다).
- 질문 2에 대한 답변: 병합 전략을 사용하여 대상 테이블에 데이터를 작성하세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

💥 여기에는 제 주석이 달린 DLT 구현 스니펫이 있어요 💥

🟣️ 전체 코드는 여기 저장소에서 확인할 수 있어요 🟣

✅ 이로서 튜토리얼을 마치겠습니다. 점진적 로딩 스크립트를 구성하는 다양한 구성 요소 및 코드 구현에 대해 배우셨길 바라며,

DLT로 점진적 로딩 스크립트를 작성하는 방법에 대해 더 알고 싶다면 여기서 문서를 확인해보세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 나에 대해

안녕하세요, 베를린에 거주 중인 Daniel Le입니다. 현재, 저는 기계 학습에 대한 큰 열정을 가지고 데이터 엔지니어로 일하고 있습니다.

저는 독일 베를린에 거주하고 있으며 새로운 기술에 관심이 많으며 이를 실제 문제 해결에 어떻게 적용할 수 있는지 고민합니다.

궁금한 점이 있거나 이러한 관심사를 더 자세히 논의하고 싶다면 망설이지 말고 LinkedIn에서 저와 연락해 주세요.

<!-- ui-station 사각형 -->
<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 참고

- https://dlthub.com/docs/general-usage/incremental-loading