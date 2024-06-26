---
title: "AWS Powertools를 사용해 Serverless API 문서화 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-ServerlessAPIDocumentationwithPowertoolsforAWS_0.png"
date: 2024-06-23 00:18
ogImage:
  url: /assets/img/2024-06-23-ServerlessAPIDocumentationwithPowertoolsforAWS_0.png
tag: Tech
originalTitle: "Serverless API Documentation with Powertools for AWS"
link: "https://medium.com/towards-aws/serverless-api-documentation-with-powertools-for-aws-c14df17f0feb"
---

![서버리스 API 문서화](/assets/img/2024-06-23-ServerlessAPIDocumentationwithPowertoolsforAWS_0.png)

높은 품질의 API 문서는 특히 람다 함수가 API를 제공하는 서버리스 아키텍처에서 고객 만족도를 향상시킵니다. 이러한 API를 문서화하는 것은 언제나 깃털로 소설을 쓰는 것 같았습니다. 그러나 이제는 AWS용 Powertools가 OpenAPI 문서화 유틸리티를 출시하면서 그렇지 않게 됐습니다.

이 게시물에서는 Python 람다 함수 기반 API에 대한 OpenAPI 문서를 생성하는 방법을 소개합니다. AWS 람다 및 Pydantic용 Powertools를 활용합니다.

다음 게시물에서는 이 프로세스를 자동화하고 서비스 CI/CD 파이프라인에 추가하여 제작 게이트 및 문서 게시를 생산물에 추가하는 방법을 논의할 것입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

<img src="/assets/img/2024-06-23-ServerlessAPIDocumentationwithPowertoolsforAWS_1.png" />

이 블로그 글은 원래 "Ran The Builder" 웹사이트에 게시되었습니다.

# API 문서 작성의 필요성

API 변경이 필요한 기능을 설계할 때, 나는 그것들을 문서화합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

저는 API 우선 접근 방식을 채택하는 것이 중요하다고 강하게 믿습니다. AWS 서버리스 영웅인 Allen Helton은 API 우선 접근 방식의 가치에 대해 훌륭한 글을 썼습니다. 이 방식은 내부 또는 외부 API 사용자가 새로운 API를 발행할 때까지 차단되지 않고 자사의 API와 통합을 개발하고 계획할 수 있도록 합니다. API 문서를 보내고 그들이 작업할 수 있게 해 주는 것입니다.

API 문서는 시스템의 제공되는 것을 언제나 상세히 이해하는 데도 도움이 됩니다. 미래의 통합, 새로운 기능 개발, 그리고 신규 팀원을 온보딩하는 데 훌륭합니다.

## OpenAPI — 표준의 왕

OpenAPI가 API를 설명하는 표준 형식으로 자리잡고 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

REST API와 관련된 내용을 설명하는 간단한 JSON 또는 YML 형식의 파일이에요!

- 이용 가능한 엔드포인트
- 각 엔드포인트에 대한 작업
- 각 작업의 입력 및 출력 매개변수
- 인증 방법
- 태그 또는 그룹별로 엔드포인트를 구성하고 샘플 요청을 생성할 수 있어요.

Swagger.io는 이 문서 파일을 시각화할 수 있는 도구에요.

더미 서비스를 위한 포맷의 라이브 데모를 확인할 수 있어요! 이런식으로 보여요:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>


![](/assets/img/2024-06-23-ServerlessAPIDocumentationwithPowertoolsforAWS_2.png)

이제 API를 문서화하는 방법을 이해했으니 람다 함수를 백엔드로 사용하는 서버리스 API에이 프로세스를 적용하는 방법에 대해 이야기해 보겠습니다.

# API 문서 생성

여러 가지 API 문서 생성 방법을 살펴보겠습니다.


<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

내 의견으로는 Python 기반 API 문서를 생성하는 가장 좋은 방법은 서비스 코드에서 생성하는 것입니다. Pydantic을 사용하여 입력 및 응답 스키마를 정의하고, 이 스키마를 OpenAPI 형식으로 내보내는 옵션이 내재되어 있어서 제 입장에서는 큰 장점입니다. 따라서 우리가 선택하는 모든 솔루션에 이 도구와 통합해야 합니다.

첫 번째 옵션은 API Gateway에서 기본으로 제공됩니다.

API Gateway에는 멋진 기능이 있습니다. 배포한 후 콘솔이나 여기에 설명된대로 API를 통해 OpenAPI 문서를 내보낼 수 있습니다. 그러나 Pydantic을 지원하지 않고, 세부 사항 스키마 (입력 또는 출력) 중 많은 것들이 쉽게 구성되지 않습니다. JSON 스키마를 직접 정의하고 요청 검증을 활성화해야 합니다. 마지막으로, 핸들러 코드가 아닌 인프라 코드로부터 생성되므로 좋지만 완벽하지는 않습니다.

좋은 기능이지만, 더 많은 것이 필요합니다. 다른 방법을 검토해 봅시다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

두 번째 옵션은 FastAPI와 같은 프레임워크에서 나옵니다.

대부분의 이러한 Python 프레임워크 및 도구들은 FastAPI, Flask-RESTPlus/Flask-RESTx, Django REST Framework, Connexion과 같이 설계되어 있어 웹 응용 프로그램 및 API를 구축하고 소켓을 통해 수신되는 HTTP 요청을 수신할 수 있습니다.

FastAPI에 초점을 맞춰 봅시다.

FastAPI는 Pydantic 스키마를 지원하여 페이로드를 설명하고 코드에서 직접 OpenAPI 문서를 생성할 수 있습니다. 이 접근 방식은 문서 작성 프로세스를 간소화하고 코드와 동기화를 유지합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

그러나 Lambda에서 웹 서버를 실행하기 때문에 이미 Lambda 서비스가 대신 수신되는 HTTP 요청을 수신하기 위해 소켓을 여는 방식으로 작동합니다. 이것은 서버 기반 프레임워크이며, 콜드 스타트, Lambda ZIP 파일 크기 및 지연 시간에 부정적인 영향을 미칩니다. 가능은 하지만, 필요한가 싶진 않습니다.

내 의견으로는, 핸들러 코드로부터 생성된 OpenAPI 문서를 제공하는 네이티브 서버리스 프레임워크가 필요합니다. 빠르고 Lambda의 호출 모델과 일치하며, 이에 따라 자체적으로 소켓을 열어서는 안 됩니다.

어떻게 그것을 실현할 수 있는지 살펴봅시다.

# 서버리스 OpenAPI 문서

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

저희 목표는 API Gateway와 Lambda 함수로 구성된 서버리스 API에 대한 OpenAPI 문서를 생성하는 것입니다. HTTP 입력 페이로드 스키마와 모든 가능한 HTTP 응답(상태 코드 및 JSON 페이로드)를 정의할 것입니다. 우리는 Pydantic을 사용하여 모든 스키마를 정의할 것입니다.

OpenAPI 문서는 새로운 API 엔드포인트인 '/swagger' 아래에서 제공될 것입니다.

다음 블로그 포스트에서는 문서 내보내기 및 이 전체 프로세스를 자동화하는 방법에 대해 논의할 것입니다.

이제 목표를 이해했으니 코드를 작성해 봅시다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## Powertools EventHandler 소개

우리는 AWS Lambda용 Powertools 라이브러리를 사용합니다. Powertools for Lambda는 서버리스 운영을 위한 관측 가능성, 로깅, 아이덤포턴시, 입력 유효성 검사 및 기타 여러 가지 기능을 제공하는 라이브러리입니다.

Powertools의 이벤트 핸들러 유틸리티를 사용할 것입니다.

이벤트 핸들러 유틸리티는 API Gateway REST/HTTP API, ALB 및 Lambda Function URL에 대한 보일러플레이트를 줄이기 위한 가벼운 루팅을 제공합니다. 이 유틸리티는 마이크로 함수(한 개 또는 몇 개의 라우트) 및 모놀리식 함수(모든 라우트)와 함께 작동합니다. 가장 흥미로운 점은 OpenAPI와 Pydantic 스키마를 사용한 요청/응답의 데이터 유효성 검사 기능을 제공한다는 것입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

오픈API 문서는 비교적 새로운 기능입니다. 이는 API Gateway에서 '/swagger' 엔드포인트를 제공하여 OpenAPI 문서를 출력합니다.

실제 서비스에서 이벤트 핸들러 및 데이터 유효성 검사를 구현해 봅시다.

AWS 람다 쿡북 템플릿 프로젝트를 사용하여 OpenAPI 문서를 지원하도록 추가할 것입니다. 쿡북은 서버리스를 시작하는 데 세 번의 클릭만으로 모든 최상의 실전 서버리스 서비스가 요구하는 모든 최상의 실전 서버리스 서비스가 요구하는 모범 사례와 유틸리티를 포함한 템플릿 프로젝트입니다.

인프라 구성부터 시작해 봅시다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## OpenAPI 엔드포인트 인프라

공식 문서에 SAM 코드 샘플이 있지만, 저는 AWS CDK를 사용합니다.

아래는 CDK REST API 구성에 추가할 함수입니다.

이 함수를 사용하여 GET ‘/swagger’ HTTP 호출에 응답하고 OpenAPI 문서를 생성하는 람다 핸들러를 선택해야 합니다. 우리는 이벤트 핸들러 유틸리티를 사용하는 람다 함수를 연결해야 합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

/swagger, /swagger.css, /swagger.js 세 개의 'GET' 엔드포인트를 해당 기능과 매핑해야 합니다.

5번 라인에서는 새로운 엔드포인트를 추가할 REST API 게이트웨이 개체를 이 함수에 전달하며, 이 엔드포인트를 처리할 람다 함수 클래스를 받습니다.

7번부터 14번 라인까지, 세 개의 엔드포인트를 추가하고 HTTP GET 명령을 사용하여 람다 함수를 연결합니다.

전체 코드는 여기에서 찾을 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 이벤트 핸들러 코드

이제 모든 인프라가 구성되었으니 이벤트 핸들러 코드를 추가하고 API 문서화를 시작해 봅시다.

3번 줄에서는 이벤트 핸들러 API 게이트웨이 리졸버를 생성하고 입력 이벤트와 출력 응답 유효성 검사(Pydantic 사용)를 얻기 위해 유효성을 활성화합니다.

4번 줄에서는 '/swagger' 엔드포인트를 통해 OpenApi 생성을 활성화하고 생성된 문서에 제목을 전달합니다. 문서에 따르면 완전한 OpenAPI 정의를 얻으려면 검증을 활성화해야 합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

여기서 전체 코드를 찾을 수 있어요.

## 람다 핸들러 코드

람다 핸들러 코드를 작성하고 문서화해봅시다.

새로운 고객 주문을 생성하는 HTTP POST '/api/orders' 엔드포인트에 대해 문서화하겠습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

여기에는 많은 내용이 있지만, 꽤 간단해요. 우리는 가능한 한 많은 OpenAPI 정보를 추가할 거예요. 특정 API, 설명, JSON HTTP 본문의 입력 스키마, 그리고 모든 가능한 HTTP 응답 스키마를 Pydantic으로 설명할 거예요.

8번째 줄에서, 우리는 이전 파일에서 초기화한 이벤트 핸들러를 가져와요.

13번째 줄에서, 우리는 앱 정의를 시작해요. 먼저, 이 API를 HTTP POST로 표시해요.

14번째 줄에서, 우리는 API를 '/API/orders/' 경로에 응답할 수 있도록 설정해요.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

15번 라인에서는 OpenAPI 문서에 표시될 API 설명을 설정합니다.

18~31번 라인에서는 모든 API 응답을 정의합니다.

19~22번 라인에서는 HTTP 200 OK 응답을 정의합니다. 이렇게하면 모든 HTTP 응답 정의를 제어할 수 있습니다. 이 응답을 정의하지 않으면 이벤트 핸들러가 자동으로 422 및 200 응답을 생성하고 501 응답을 자동으로 생성하지는 않음을 유의하세요. 422는 입력 유효성 검사 기능에서 내장된 응답이며, 200 응답은 35번 라인에서 정의한 'CreateOrderOutput' Pydantic 스키마를 사용하여 구축됩니다.

23~26번 라인에서는 HTTP 코드 422를 사용하여 HTTP 입력 유효성 검사 응답을 정의합니다. 'InvalidApiRequest' Pydantic 스키마를 사용하여 설명했습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

27–30 줄에서 우리는 HTTP 501에 대해 동일한 작업을 수행합니다.

32 줄에서는 이 API를 'CRUD'라는 그룹의 일부로 태그합니다. 여러 개의 API가 있는 경우 태그를 사용하면 하나의 긴 목록이 아닌 여러 하위 목록에서 API를 제시하는 것이 더 쉬워집니다.

39 줄에서는 핸들러의 엔트리 함수를 정의합니다. 리졸버는 HTTP 경로와 명령에 따라 올바른 이벤트 핸들러 하위 함수를 호출합니다. 여기서 더 자세히 알아볼 수 있습니다. 우리 경우에는 모든 호출이 13–36 줄에서 정의한 함수로 라우팅됩니다.

34–35 줄에서 핸들러가 예상하는 입력을 정의하기 위해 타입 힌트를 사용합니다. 데이터 유효성 검사를 활성화했기 때문에 36 줄에 도달하면 파이다닉 오브젝트가 파싱되고 직렬화되어 일반 이벤트가 아닌 딕셔너리 형식으로 손에 넣게 됩니다. 제가 Annotated와 body typing 클래스를 사용하여 이벤트 핸들러가 본문 페이로드에 'CreateOrderInput' 클래스가 필요하며 JSON 딕셔너리이며 문자열이 아님을 알려주었습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

API 요청과 응답은 모두 그들을 정의하는 Pydantic 스키마를 가지고 있습니다. Pydantic 스키마 정의는 여기와 여기에서 찾을 수 있습니다.

전체 핸들러 코드는 여기에서 찾아볼 수 있습니다.

저는 아키텍처 레이어 컨셉에 따라 이 핸들러와 로직을 작성했는데, 이 내용은 AWS re:Invent 2023 세션에서도 다뤘어요. 자세한 내용은 여기를 클릭해 더 알아보세요.

# OpenAPI Endpoint in Action

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

이제 남은 일은 코드를 배포하고 스웨거 엔드포인트에 액세스하는 것뿐입니다.

다음과 같은 모습일 것입니다:

테이블 태그를 Markdown 형식으로 변경해보세요.

스키마를 클릭하면 Pydantic 정의 출력이 제대로 된 OpenAPI 스키마로 표시되는 것을 확인할 수 있습니다. 설명, 제한 사항 및 유형이 포함됩니다.

여기에서 실시간 버전의 스웨거를 확인할 수도 있습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

https://tzmt8vspl4.execute-api.us-east-1.amazonaws.com/prod/swagger

# 제한 사항

이 새로운 유틸리티에 꽤 감명받았어요. 그러나 몇 가지 제한 사항이 있습니다.

이는 해결 가능하지만 Powertools 팀이나 커뮤니티로부터의 개발이 필요합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

예를 들어, OpenAPI 명세의 전체가 생성되지는 않습니다. 그럼에도 불구하고 저장소에 여전히 열려 있는 문제들이 있으며, 커뮤니티의 도움을 요청하고 있기 때문에 시도해보고 싶다면 의미 있는 첫 번째 기여가 될 수 있습니다!

이제 더 중요한 문제로 넘어가겠습니다.

작성 시점에서 여러 마이크로 함수를 사용하면 OpenAPI 생성이 지원되지 않습니다. 단일 람다만 지원됩니다. 제가 제안한 해결책으로 GitHub 이슈를 만들었으며, 이 문제에 대한 긍정적인 피드백을 주시면 사물을 움직이는 데 도움이 될 것입니다.

# 요약

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

이 게시물에서는 AWS용 Powertools가 핸들러 코드에서 OpenAPI 문서를 생성하는 데 어떻게 도움을 줄 수 있는지 알아보았습니다. 이는 개발자가 코드와 해당 설명 문서를 소유하고, 무엇보다도 두 가지를 항상 동기화하는 방법에 대한 지원을 제공합니다.

다음 글에서 제가 이 접근 방식을 어떻게 더 발전시킬 수 있는지, 상당히 중요한 것을 추가하여 코드와 API 설명 문서 사이의 소중한 동기화를 보호할 수 있는 방법에 대해 논의할 예정입니다. 함께 해주세요!
