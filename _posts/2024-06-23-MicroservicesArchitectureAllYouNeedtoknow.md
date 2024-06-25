---
title: "마이크로서비스 아키텍처 모두가 알아야 할 필수 개념과 방법"
description: ""
coverImage: "/assets/img/2024-06-23-MicroservicesArchitectureAllYouNeedtoknow_0.png"
date: 2024-06-23 22:48
ogImage:
  url: /assets/img/2024-06-23-MicroservicesArchitectureAllYouNeedtoknow_0.png
tag: Tech
originalTitle: "Microservices Architecture : All You Need to know"
link: "https://medium.com/@ankitagarwal702/microservices-architecture-all-you-need-to-know-8efa2ec4181f"
---

<img src="/assets/img/2024-06-23-MicroservicesArchitectureAllYouNeedtoknow_0.png" />

단일 버그가 전체 시스템을 충돌시킬 수 있는 단일체 응용 프로그램을 업데이트하는 악몽에 직면해 본 적이 있나요? 여러분, 응용 프로그램을 보다 견고하고 확장 가능하며 유지보수하기 쉽게 만들 수있는 방법이 있다는 것을 말해 드릴까요? 마이크로서비스의 세계로 환영합니다.

컬러풀한 비유로 들어가 봅시다.

니가 소개해 준 마이크로서비스는 대형 응용 프로그램의 느슨하게 결합된 작은 응용 프로그램들입니다. 각 마이크로서비스는 단일 기능 또는 프로세스에 책임을지어야 합니다. 코드 또는 데이터를 공유하면 안되며, 데이터 또는 코드를 공유하다가 데이터베이스가 다운되면 각 서비스/시스템 전체가 실패할 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

마이크로서비스의 경우 독립성과 자율성이 코드 재사용보다 더 중요합니다. 이들은 서로 직접 통신할 수 없지만 이벤트/메시지 버스를 활용하여 서로 통신합니다.

## 전형적인 마이크로서비스 아키텍처

![이미지](/assets/img/2024-06-23-MicroservicesArchitectureAllYouNeedtoknow_1.png)

전형적인 마이크로서비스 아키텍처는 다음 구성 요소 또는 빌딩 블록으로 구성됩니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 클라이언트

클라이언트는 웹 애플리케이션, 모바일 애플리케이션 또는 챗봇과 같은 다양한 인터페이스를 통해 시스템과 상호 작용하는 최종 사용자들을 말합니다. 이러한 인터페이스를 통해 사용자들은 마이크로서비스 아키텍처에서 제공되는 서비스에 액세스할 수 있습니다.

## 컨테이너 호스트

마이크로서비스는 일반적으로 애플리케이션을 실행하는 가벼운, 휴대 가능하고 일관된 환경인 컨테이너에 배포됩니다. Docker와 Kubernetes는 이러한 컨테이너를 관리하기 위한 인기 있는 선택지입니다. Docker는 컨테이너화를 제공하고, Kubernetes는 이러한 컨테이너를 조정하고 관리하여 효율적이고 신뢰성 있게 실행되도록 합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 데이터베이스

마이크로서비스 아키텍처에서 각 서비스는 자체 데이터베이스를 가질 수 있습니다. 이를 통해 각 서비스에 가장 적합한 데이터베이스 유형을 선택할 수 있습니다. 예를 들어 한 서비스가 PostgreSQL과 같은 관계형 데이터베이스를 사용할 수 있고, 다른 서비스는 MongoDB와 같은 NoSQL 데이터베이스를 사용할 수 있습니다. 이러한 분리는 서비스가 느슨하게 결합되어 독립적으로 확장될 수 있음을 보장합니다.

## API 게이트웨이

API 게이트웨이는 클라이언트와 마이크로서비스 간의 통신을 HTTP를 통해 할 수 있도록 중개자 역할을 합니다. 클라이언트가 각 마이크로서비스를 직접 호출하는 대신 API 게이트웨이에 요청을 보냅니다. API 게이트웨이는 이후 이러한 HTTP 요청을 적절한 마이크로서비스로 라우팅합니다. 이는 클라이언트 측 로직을 단순화시키는데 그치지 않고 인증, 로깅, 속도 제한, 부하 분산 등과 같은 다양한 관심사를 위한 단일 진입점을 제공합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 인증 서비스

인증 서비스는 마이크로서비스에 대한 클라이언트 액세스를 보호하는 데 매우 중요합니다. 이는 클라이언트 애플리케이션을 인증하고 인증 토큰(일반적으로 JWT)을 제공합니다. 이 토큰은 각 HTTP 요청에 포함되어야 하며 마이크로서비스에 안전하게 액세스할 수 있도록 합니다.

## 작동 방식

- 토큰 요청: 클라이언트 애플리케이션은 먼저 인증 서비스에 HTTP 요청을 보내어 액세스 토큰을 획득합니다. 이 요청에는 일반적으로 클라이언트의 자격 증명(예: 사용자 이름과 비밀번호)이 포함됩니다.
- 액세스 토큰: 성공적으로 인증된 경우, 인증 서비스는 클라이언트에게 액세스 토큰(JWT)을 발급합니다.
- 마이크로서비스 액세스: 클라이언트는 이 액세스 토큰을 API 게이트웨이의 후속 HTTP 요청 헤더에 포함시킵니다. API 게이트웨이는 요청을 원하는 마이크로서비스로 라우팅하기 전에 토큰을 유효성 검사합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 토큰 만료 :

액세스 토큰은 일반적으로 구성 가능한 시간 동안만 유효합니다. 예를 들어 토큰이 5분 동안 유효하다면, 클라이언트는 토큰이 만료되기 전에 여러 요청을 만들 수 있어 매 호출마다 새 토큰 요청을 할 필요가 없어집니다. 따라서 액세스 빈도와 만료 시간 사이에 균형을 유지하는 것이 중요합니다. 만료 시간이 짧을수록 요청이 더 안전해집니다.

## 이벤트 버스

이벤트 버스는 비동기, 이벤트 기반 메시징을 통해 마이크로서비스간의 통신을 용이하게합니다. 마이크로서비스가 강하게 결합되지 않으면서 상호 작용할 수 있도록 보장하는 데 중요한 역할을 하며, 전반적인 확장성과 유연성을 향상시킵니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 작동 방식

- 이벤트 생성 : 마이크로서비스들은 이벤트 생성기능을 통해 이벤트를 생성하거나 발행합니다. 이러한 이벤트는 새로운 사용자 생성, 거래 완료, 재고 수준 변화 등 다양한 상태 변경 또는 동작을 나타낼 수 있습니다.
- 이벤트 소비 : 다른 마이크로서비스들은 이벤트 버스에 구독하고 특정 이벤트를 감지합니다. 관심 있는 이벤트가 발행되면, 해당하는 구독 중인 마이크로서비스는 해당 이벤트를 처리하고 소비합니다.
- 겹합되지 않은 통신 : 발행 마이크로서비스는 이벤트를 소비하는 마이크로서비스를 알 필요가 없습니다. 마찬가지로 소비하는 마이크로서비스들은 이벤트의 출처를 알 필요가 없습니다. 이러한 겹합 없는 통신은 마이크로서비스들이 서로 영향을 미치지 않고 독립적으로 발전할 수 있도록 보장합니다.

# 안티 패턴 (신화)

안티 패턴은 일반적으로 반복되는 문제에 대한 해결책으로 매우 비생산적이거나 생산성이 낮은 것입니다. 이러한 것들을 고려해서는 안됩니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 모든 것들은 **database를 제외하고 작아져야 합니다**: 하나의 database만 있다면, 그것은 잠재적인 단일 장애점이 될 수 있습니다. 모든 Microservices가 동일한 단일 database에 결합되어 있으면 개별적으로 실패할 수 없습니다.
- Microservices가 마법처럼 나쁜 개발 관행을 해결해 주지는 않습니다\*\*: 오래된 아키텍처에서 나쁜 개발이나 배포 습관이 Microservices 아키텍처로 전이된다면, 더 큰 해를 불러올 수 있습니다. 아키텍처 선택과 관계없이 코딩 및 배포 최상의 관행은 항상 최우선 과제여야 합니다.
- Microservices는 단일 시스템 기능 또는 프로세스를 해결하는 데 중점을 두기 때문에 개발 팀 간의 조정이 필요하지 않습니다\*\*: 신 개념이므로 항상 팀간의 조정이 필요합니다. 소프트웨어 아키텍트가 모든 팀에서 동일한 높은 기준과 최상의 관행에 따라 Microservices를 설계하는 것이 좋습니다.
- Microservices 기술을 핵심에 두면 안 됩니다\*\*: Microservices는 도구와 기술 선택의 유연성을 제공하지만, 주요 초점이 되어서는 안 됩니다. 주요 초점은 시스템 기능을 Microservices로 어떻게 분해할지 및 각 Microservice의 목적을 정의하는 데 있어야 합니다.

# Microservices의 이점은 무엇인가요?

- 복잡성 감소: 각 Microservice당 더 작은 코드베이스로 복잡성이 줄어듭니다.
- 모듈성 향상: 시스템을 이해, 개발 및 테스트하기 쉽게 만들어 모듈성을 향상시킵니다.
- 확장성: 높은 확장성을 갖는 아키텍처를 만듭니다.
- 분할 정복: 분할 정복 원칙을 적용하여 대규모 및 복잡한 애플리케이션의 지속적인 전달 및 개발을 가능하게 합니다.
- 독립적 배포: 서비스는 독립적으로 배포될 수 있습니다.

# 왜 Microservices는 RESTful API로 개발되었나요?

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

마이크로서비스는 종종 작은 RESTful API로 개발됩니다. RESTful API는 Representational State Transfer로 알려진 아키텍처 스타일을 기반으로 하는 웹 API 또는 서비스입니다. 클라이언트 응용 프로그램이 RESTful API와 HTTP를 통해 통신하는 방법을 정의합니다. 그렇다면 왜 RESTful API가 마이크로서비스에 적합한 것일까요? 주요 이유를 알아봅시다:

- 간결함: CRUD에 기반한 HTTP 동사이므로 이해하기 쉽습니다.
- 상태 없음: REST는 상태를 유지하지 않도록 설계되어 있으며 클라이언트와 서버의 역할을 분리합니다. 이는 서버가 클라이언트의 상태를 알 필요가 없거나 클라이언트가 서버의 상태를 걱정할 필요가 없음을 의미합니다.
- 성능 및 확장성: REST 읽기는 성능과 확장성을 위해 캐싱될 수 있습니다.
- 데이터 형식 유연성: REST는 많은 데이터 형식을 지원하지만 JSON의 주요 사용은 브라우저 클라이언트에서 더 나은 지원을 가능하게 합니다.

# 직접적인 클라이언트 액세스의 문제 및 API 게이트웨이 사용의 이유

- 직접적인 클라이언트 액세스는 많은 마이크로서비스 엔드포인트를 추적해야 하는 경우 클라이언트 통합의 복잡성을 증가시킵니다.
- 이 경우 클라이언트는 자체적으로 로드 밸런싱 및 장애 감지를 구현해야 합니다.
- 클라이언트가 여러 서비스에 공개적으로 액세스하려면 이러한 서비스들이 각자의 보안 문제를 다루어야 하며 SSL 종료 및 인증을 포함합니다.
- API 게이트웨이는 클라이언트 애플리케이션이 마이크로서비스에 액세스하는 데 사용할 수 있는 통합된 진입점을 생성합니다.
- 이는 클라이언트 요청을 원하는 백엔드 마이크로서비스로 라우팅하는 리버스 프록시 역할을 합니다.
- API 게이트웨이는 클라이언트 인증, 로드 밸런싱 및 SSL 종료와 같은 중요한 기능을 수행할 수도 있습니다.
- API 게이트웨이는 지정된 게이트웨이 경로에 허용되는 HTTP 동사의 유형을 제한할 수도 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 추가 읽을거리

- 완전한 고수준 시스템 디자인 재생 목록

---

PS: 안녕하세요! 저는 Ankit Agarwal이라고 합니다. 소프트웨어 공학에 대한 열정을 가진 시니어 소프트웨어 엔지니어입니다.
이 기사가 도움이 되셨나요? 멋집니다! 언제든지 제가 가진 소프트웨어 엔지니어링에 대한 지식과 열정을 공유하는 것을 기쁘게 생각합니다. 더 배우고 싶거나 연결하고 싶다면 LinkedIn에서 저를 찾아주십시오 @ https://www.linkedin.com/in/ankit-agarwal-87331a17b/
