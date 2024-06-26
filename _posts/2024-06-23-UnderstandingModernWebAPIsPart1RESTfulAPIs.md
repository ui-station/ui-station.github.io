---
title: "현대 웹 API 이해하기 Part 1 RESTful API 사용법"
description: ""
coverImage: "/assets/img/2024-06-23-UnderstandingModernWebAPIsPart1RESTfulAPIs_0.png"
date: 2024-06-23 22:09
ogImage:
  url: /assets/img/2024-06-23-UnderstandingModernWebAPIsPart1RESTfulAPIs_0.png
tag: Tech
originalTitle: "Understanding Modern Web APIs Part 1: RESTful APIs"
link: "https://medium.com/@shiiyan/understanding-modern-web-apis-part-1-restful-apis-43f311a3237c"
---

# 소개

APIs (Application Programming Interface)는 현대 웹 개발의 필수 요소가 되었습니다. 이를 통해 클라이언트와 서버를 분리하여 클라이언트가 UI/UX에 집중할 수 있도록 하며, 복잡한 비즈니스 로직을 이해하지 않고도 백엔드와 상호작용할 수 있게 합니다.

다양한 종류의 웹 API 중에서 RESTful, GraphQL 및 gRPC가 가장 두드러지는데요. 이 블로그에서는 RESTful API에 대해 자세히 살펴보면서 원칙, 이점 및 실용적인 사용법을 탐구해 보겠습니다.

# RESTful API란 무엇인가요?

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

REST은 Representational State Transfer의 약자입니다. 이 용어는 2000년 Roy Fielding의 박사 학위 논문에서 처음 소개되었습니다. REST는 API를 구축하기 위한 도구나 기술이 아니라 웹 자원 사이의 일관된 인터페이스를 설계하기 위한 제약 조건과 원칙의 집합입니다.

# RESTful API 설계의 주요 원칙

## 자원 중심 디자인

RESTful API에서 모든 것이 자원으로 간주되며 URI (Uniform Resource Identifier)로 식별될 수 있습니다. 자원은 컬렉션 또는 개별 항목이 될 수 있습니다. 예를 들어:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- collections: /customers
- item: /customers/1

리소스 간에도 연관 관계가 있습니다.

- 연결된 컬렉션: /customers/1/orders
- 연결된 항목: /customers/1/orders/2

리소스는 단순히 데이터베이스의 테이블이 아닙니다. 대신 API를 통해 주소 지정 및 조작할 수 있는 데이터 모음을 나타냅니다. 응용 프로그램이 도메인 주도 설계(DDD)를 사용하는 경우, 도메인(또는 집계)을 리소스의 경계를 정의하는 주요 고려 사항으로 삼는 것이 좋습니다. 이 접근 방식은 비즈니스 도메인과 논리적으로 일치하고 유지 관리하기 쉬운 API를 만듭니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

또한, 프론트엔드 요구 사항을 충족하기 위해 API 설계를 최적화하는 것이 중요합니다. 이는 API 성능을 개선하고 전반적인 사용자 경험을 향상시킬 수 있습니다.

## HTTP 표준

RESTful API는 표준 HTTP 메소드를 활용하여 CRUD(Create, Read, Update, Delete) 작업을 수행합니다.

- POST: 새로운 리소스 생성
- GET: 단일 또는 여러 리소스 검색
- PUT: 단일 또는 여러 리소스 업데이트
- DELETE: 단일 또는 여러 리소스 삭제

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

이러한 API들은 주로 HTTP/1.1에 의존하며, 이는 클라이언트와 서버 간의 각 상호작용이 하나의 요청과 응답 쌍으로 이루어진 텍스트 기반 통신 프로토콜입니다. RESTful API는 일반적으로 JSON 형식으로 데이터를 교환합니다. 또한 미디어 유형을 활용하여 데이터 형식을 지정합니다.

- Content-Type: 리소스의 미디어 유형을 나타냅니다 (예: application/json, application/problem+json)
- Accept: 클라이언트가 허용할 미디어 유형을 지정합니다 (예: application/json)

## 상태 없음(Statelessness)

RESTful API에서 요청은 상태를 유지하지 않아야 합니다. 각 요청에는 처리에 필요한 모든 정보가 포함되어 있어 백엔드가 각 요청을 독립적으로 처리할 수 있게 합니다. 이는 요청이 순차적이지 않아도 되고, 병렬로 여러 서버가 요청을 처리할 수 있도록 합니다. HTTP가 본질적으로 상태를 유지하지 않기 때문에 RESTful API가 HTTP 프로토콜에 적합하게 만듭니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 장점

## 간편성

RESTful API는 복잡한 리소스를 다룰 때라도 쉽게 이해하고 구현할 수 있습니다. 표준 HTTP 메서드와 명확한 URL 구조의 사용은 개발자가 신속하게 대상 리소스를 확인하고 수행 중인 작업을 이해할 수 있도록 도와줍니다.

## 널리 사용되며 신뢰성이 검증됨

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

RESTful API는 산업 전반에 널리 채택되어, 다양한 성공 사례와 모범 사례가 있습니다. 라우팅, 데이터 직렬화, API 테스트와 같은 일반적 작업을 위한 준비된 솔루션도 많이 있습니다.

## 확장성

상태가 없는 특성 때문에 RESTful API는 쉽게 확장할 수 있습니다. 웹 애플리케이션은 여러 웹 서버에 요청을 분산하여 많은 수의 요청을 수용할 수 있습니다. 따라서 RESTful API는 클라우드 환경과 분산 시스템에 잘 어울립니다.

# 더불어

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## API 스키마

RESTful API는 데이터 교환을 위해 JSON을 일반적으로 사용하며 유연한 스키마를 제공합니다. 그러나 이 유연성은 때로는 프론트엔드와 백엔드 간 불일치로 이어질 수 있습니다. 예를 들어 프론트엔드 엔지니어가 응답의 일부로 문서화된 속성이 실제 백엔드 응답에서 누락된 문제에 직면할 수 있습니다.

이러한 문제를 완화하기 위해 API 주도 설계는 백엔드 및 프론트엜드 구성 요소를 구현하기 전에 API 스키마를 먼저 정의하는 것을 권장합니다. OpenAPI와 같은 도구는 이러한 프로세스를 용이하게하기 위해 HTTP API를 설명하는 공식 표준을 제공하여 개발자가 API 스키마를 명확하게 지정하고 API 모델 및 클라이언트를 자동으로 생성할 수 있도록 지원합니다. 이를 통해 양쪽 모두가 미리 정의된 계약에 맞추어 효율적으로 개발하고 일치하게 할 수 있습니다.

## 버전 관리

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

웹 API를 개발할 때는 항상 역호환성을 보장하는 것이 중요합니다. 이러한 호환성을 유지할 수 없을 때는 API 버전 관리가 필요합니다. 호환되지 않는 변경 사항이 발생할 때마다 버전 번호가 증가합니다.

RESTful API는 URL 버전 관리, 쿼리 문자열 버전 관리 및 HTTP 헤더 버전 관리와 같은 다양한 버전 관리 방법을 지원합니다. URL 버전 관리의 예로는 api.example.com/v1/customers가 있습니다.

# 학습 자료

웹에서 찾은 가장 좋은 RESTful API 학습 자료 중 하나입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

위 링크를 확인해보세요: [Azure API 디자인에 대한 최상의 모범 사례](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design). 여기에 유용한 정보가 많이 있을 거예요! 😊
