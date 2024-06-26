---
title: "마이크로서비스 아키텍처에서의 소통 스타일 가이드"
description: ""
coverImage: "/assets/img/2024-05-27-AGuidetoCommunicationStylesinMicroservicesArchitecture_0.png"
date: 2024-05-27 16:41
ogImage:
  url: /assets/img/2024-05-27-AGuidetoCommunicationStylesinMicroservicesArchitecture_0.png
tag: Tech
originalTitle: "A Guide to Communication Styles in Microservices Architecture"
link: "https://medium.com/@joudwawad/a-guide-to-communication-styles-in-microservices-architecture-9a8ae4bc21b2"
---

![A Guide to Communication Styles in Microservices Architecture](/assets/img/2024-05-27-AGuidetoCommunicationStylesinMicroservicesArchitecture_0.png)

마이크로서비스 아키텍처에서 의사소통은 중요한 요소로, 다양한 토론이 진행되어 상호 서비스 상호 작용에 대한 가장 효과적인 방법을 선택하는 데 초점을 맞춥니다. 이 입문용 블로그 포스트에서는 마이크로서비스를 위한 최적의 의사소통 전략을 탐색하고 요약하여 언제 어디서 각 의사소통 스타일을 효과적으로 활용해야 하는지에 대한 통찰을 제공할 것입니다.

# 상호 작용 스타일

마이크로서비스 아키텍처 내에서 서비스가 어떻게 의사소통하는지 효과적으로 이해하기 위해 사용 가능한 상호 작용 스타일에 익숙해지는 것이 중요합니다. 각 스타일은 고유한 장단점을 가지고 있습니다. 이러한 세부 사항을 정확히 이해하는 것은 서비스에 가장 적합한 의사소통 메커니즘에 대한 정보를 얻기 전 중요합니다. 이 기본 지식은 선택한 방법이 시스템의 특정 요구 사항과 도전에 잘 부합되도록 보장합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

그러나 상호 작용 스타일은 두 가지 측면으로 분류할 수 있어요. 첫 번째 측면은 상호 작용이 일대일인지 일대다인지에요:

- 일대일 — 각 클라이언트 요청이 정확히 한 서비스에서 처리돼요.
- 일대다 — 각 요청이 여러 서비스에서 처리돼요.

두 번째 측면은 상호 작용이 동기식인지 비동기식인지에요.

- 동기식 — 클라이언트는 서비스로부터 적시에 응답을 기대하며 대기하는 동안 차단될 수 있어요.
- 비동기식 — 클라이언트는 차단되지 않고, 응답이 있다면 즉시 전달되지 않을 수도 있어요.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

다음은 서로 다른 차원을 보여주는 표입니다.

각각에 대해 간단히 얘기해 봅시다.

## 일대일 상호작용

- 요청/응답 — 서비스 클라이언트가 서비스에 요청을 보내고 응답을 기다립니다. 클라이언트는 시시각각 응답이 도착할 것으로 기대합니다. 대기하는 동안 블록될 수도 있습니다. 일반적으로 서비스가 서로 긴밀하게 결합되어 있는 상호작용 방식입니다.
- 비동기 요청/응답 — 서비스 클라이언트가 요청을 보내고, 서비스가 비동기적으로 응답합니다. 클라이언트는 대기하는 동안 블록되지 않습니다. 왜냐하면 서비스가 응답을 오랫동안 보내지 않을 수도 있기 때문입니다.
- 일방향 통지 — 서비스 클라이언트가 요청을 보내지만, 응답을 기대하거나 보내지 않습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 일대다 상호작용

- 발행/구독 — 클라이언트가 알림 메시지를 발행하고, 이를 관심 있는 서비스가 소비합니다.
- 발행/비동기 응답 — 클라이언트가 요청 메시지를 발행하고, 그리고 나서 관심 있는 서비스로부터 일정 시간 동안 응답을 기다립니다.

# 동기적 원격 프로시저 호출 패턴을 사용한 통신

클라이언트가 서비스에 요청을 보내고, 서비스는 요청을 처리한 후 응답을 되돌립니다. 일부 클라이언트는 응답을 기다리는 동안 블록될 수 있으며, 다른 클라이언트는 반응적인 논블로킹 아키텍처를 가지고 있을 수 있습니다. 그러나 메시징을 사용할 때와는 다르게, 클라이언트는 응답이 시간 이내에 도착할 것이라고 가정합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

다음 다이어그램은 RPI 작동 방식을 보여줍니다. 클라이언트의 비즈니스 로직은 PRI 프록시 어댑터 클래스에 구현된 프록시 인터페이스를 호출합니다. RPI 프록시는 서비스에 요청을 보냅니다.

요청은 RPI 서버 어댑터 클래스에 의해 처리되며, 이 클래스는 인터페이스를 통해 서비스의 비즈니스 로직을 호출합니다. 그런 다음 RPI 프록시에 응답을 보내고, 프록시는 클라이언트의 비즈니스 로직에 결과를 반환합니다.

프록시 인터페이스는 일반적으로 기본 통신 프로토콜을 캡슐화합니다. 선택할 수 있는 다양한 프로토콜이 있습니다. 여기서는 가장 인기 있는 REST와 gRPC 프로토콜에 중점을 두고 있습니다.

# REST API

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

REST에서의 중요한 개념 중 하나는 자원(resource)입니다. 자원은 일반적으로 고객(Customer)이나 제품(Product)과 같은 단일 비즈니스 객체 또는 비즈니스 객체 모음을 나타냅니다. REST는 자원을 조작하기 위해 HTTP 동사를 사용하며, 이는 URL을 사용하여 참조됩니다. 예를 들어 GET 요청은 자원의 표현을 반환하며, 이는 종종 XML 문서 또는 JSON 객체 형식으로 제공되지만 바이너리와 같은 다른 형식을 사용할 수도 있습니다. POST 요청은 새 자원을 생성하고, PUT 요청은 자원을 업데이트합니다.

REST API에서의 과제

- 하나의 요청으로 여러 자원 검색하기
  REST 자원은 종종 고객(Customer) 및 주문(Orders)과 같은 비즈니스 객체에 집중하기 때문에 하나의 요청으로 여러 관련 객체를 검색하는 것이 어려울 수 있습니다. 예를 들어 주문과 해당 고객을 얻기 위해서는 일반적으로 여러 API 호출이 필요합니다. 공통적인 해결책은 고객이 연관 자원을 단일 호출로 가져올 수 있도록 API를 개선하는 것입니다. "expand" 쿼리 매개변수를 사용하여 관련 자원을 지정하는 GET 요청을 이용하는 것이 일반적입니다. 많은 경우에 효과적이지만 이 방법은 구현하기 복잡하고 시간이 소요될 수 있으며, 이로 인해 데이터 검색을 보다 간편하게 하는 GraphQL과 같은 대안 기술의 등장에 영향을 미쳤습니다.
- 작업을 HTTP 동사에 매핑하기
  REST API 설계에서 주목할 만한 도전 과제는 비즈니스 객체에서 특정 작업을 올바른 HTTP 동사에 할당하는 방법입니다. 예를 들어, 주문 업데이트는 취소 또는 수정과 같은 다양한 작업을 수반할 수 있으며, 모든 업데이트가 반드시 멱등성(idempotent)을 보장하는 것은 아닙니다. 이는 HTTP PUT 방법을 사용하는 데 필요합니다. 일반적인 접근 방식은 구분된 업데이트 작업을 위한 하위 자원을 생성하는 것입니다. 예를 들어, 취소(POST /orders/'orderId'/cancel)나 수정(POST /orders/'orderId'/revise)에 POST를 사용하는 것입니다. 또 다른 방법은 작업을 URL 쿼리 매개변수로 포함하는 것입니다. 그러나 이러한 방법은 REST 원칙을 완전히 준수하지 않을 수 있습니다. 작업을 HTTP 동사에 매핑하는 점의 어려움은 gRPC와 같은 대체 기술의 인기에 영향을 미쳤습니다.

## REST의 장단점

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

REST를 사용하는 것에는 많은 이점이 있습니다:

- 간단하고 익숙합니다.
- 예를 들어 Postman 플러그인을 사용하여 브라우저 내에서 HTTP API를 테스트하거나, JSON 또는 다른 텍스트 형식을 사용하는 경우 curl을 사용하여 명령줄에서 테스트할 수 있습니다.
- 직접 요청/응답 스타일의 통신을 지원합니다.
- HTTP는 물론 방화벽 친화적입니다.
- 시스템 구조를 단순화하여 중간 브로커가 필요 없습니다.

하지만 REST를 사용하는 것에는 몇 가지 단점이 있습니다:

- 요청/응답 스타일의 통신만 지원합니다.
- 가용성이 감소합니다. 클라이언트와 서비스가 중간 역할 없이 직접 통신하기 때문에 메시지를 버퍼링할 중간자가 없어야 하며 교환 기간 동안 둘 다 실행되어 있어야 합니다.
- 클라이언트는 서비스 인스턴스의 위치(URL)를 알아야 합니다. 이는 현대적인 응용프로그램에서 중요한 문제입니다. 클라이언트는 서비스 인스턴스를 찾기 위해 서비스 검색 메커니즘을 사용해야 합니다.
- 한 요청에서 여러 리소스를 가져오는 것이 어려울 수 있습니다.
- 여러 업데이트 작업을 HTTP 동사에 매핑하는 것이 때때로 어려울 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# gRPC 사용하기

REST API는 여러 개의 업데이트 작업을 효과적으로 처리하기 위해 제한된 HTTP 동사들에 어려움을 겪곤 합니다. gRPC는 API 중심 접근 방식을 강조하는 이진 메시지 기반 프로토콜을 사용하여 대안을 제공합니다. 구글에서 개발한 언어 중립 직렬화 시스템인 Protocol Buffers(Protobuf)를 활용하여 개발자가 Protocol Buffers 기반 인터페이스 정의 언어(IDL)로 API를 정의할 수 있도록 합니다. 이 설정은 Protocol Buffer 컴파일러를 사용하여 Java, C#, NodeJS, GoLang 등 다양한 프로그래밍 언어로 클라이언트 및 서버 코드를 자동으로 생성할 수 있게 합니다. gRPC API는 HTTP/2 상에서 작동하며, 간단한 요청/응답 및 스트리밍 RPC를 지원하여 서버가 메시지 스트림을 클라이언트에게 보내거나 그 반대로 클라이언트가 메시지 스트림을 서버에 보낼 수 있습니다. 이 기술은 강력한 유형화된 메서드를 가진 잘 정의된 서비스 인터페이스를 지원하므로, 마이크로서비스 아키텍처에서 다양하고 복잡한 통신 패턴을 처리하기 위한 견고한 프레임워크를 제공합니다.

## gRPC의 장단점

gRPC에는 여러 가지 이점이 있습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 업데이트 작업이 풍부한 API를 설계하는 것은 간단합니다.
- 특히 큰 메시지를 교환할 때 효율적이고 간결한 IPC 메커니즘이 있습니다.
- 양방향 스트리밍은 RPI와 메시징 스타일의 양쪽 통신을 가능하게 합니다.
- 다양한 언어로 작성된 클라이언트와 서비스 간의 상호 운용성을 제공합니다.

gRPC에는 몇 가지 단점이 있습니다:

- JavaScript 클라이언트가 gRPC 기반 API를 소비하는 데 REST/JSON 기반 API보다 더 많은 작업이 필요합니다.
- 오래된 방화벽은 HTTP/2를 지원하지 않을 수 있습니다.

gRPC는 REST의 매력적인 대안이지만, REST처럼 동기식 통신 메커니즘이므로 일부 실패 문제도 겪을 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 비동기 메시징 패턴을 사용하여 소통하기

메시징을 사용하면 서비스들은 메시지를 비동기적으로 교환하며 소통합니다. 메시징 기반 응용 프로그램은 일반적으로 메시지 브로커를 사용하는데, 이는 서비스들 간의 중계자 역할을 합니다. 서비스 클라이언트는 메시지를 보내어 서비스에 요청을 합니다. 서비스 인스턴스가 응답을 할 것으로 예상되면, 별도의 메시지를 다시 클라이언트에게 보내어 응답합니다. 통신이 비동기적이기 때문에 클라이언트는 응답을 기다리는 것으로 블록되지 않습니다. 대신 클라이언트는 즉시 응답을 받지 못할 것으로 가정하고 작성됩니다.

## 메시징 개요

- Gregor Hohpe와 Bobby Woolf의 책 "Enterprise Integration Patterns"에서 요약

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 메시지 소개

메시지는 헤더와 본문으로 구성됩니다.

헤더는 이름-값 쌍과 데이터를 설명하는 메타데이터의 모음입니다. 메시지 발신자가 제공하는 이름-값 쌍 외에도 메시지 헤더에는 발신자 또는 메시징 인프라에서 생성된 고유한 메시지 ID와 선택적으로 응답이 작성되어야 하는 메시지 채널을 지정하는 이름-값 쌍이 포함됩니다.

메시지 본문은 텍스트 또는 이진 형식으로 전송되는 데이터입니다.

다양한 종류의 메시지가 있습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- **문서** - 데이터만 포함한 일반 메시지입니다. 수신자가 어떻게 해석할지 결정합니다. 명령에 대한 응답이 문서 메시지의 예입니다.
- **명령** - RPC 요청과 동등한 메시지입니다. 호출할 작업과 해당 매개변수를 지정합니다.
- **이벤트** - 무언가 주목할 만한 일이 발생했음을 나타내는 메시지입니다. 이벤트는 종종 도메인 객체의 상태 변경을 나타내는 도메인 이벤트이거나 주문 또는 고객과 같은 도메인 객체의 상태 변경을 나타냅니다.

이 블로그 게시물에서는 주로 명령(Command) 및 이벤트(Event)에 초점을 맞출 것입니다.

## 메시지 채널에 대해

메시지는 채널을 통해 교환됩니다. 송신자에서의 비즈니스 로직은 송신 포트 인터페이스를 호출하며, 해당 포트는 내부 통신 메커니즘을 캡슐화합니다.
송신 포트는 메시지 송신기 어댑터 클래스에 의해 구현되며, 이 클래스는 메시지 채널을 통해 수신자에게 메시지를 보냅니다. 메시지 채널은 메시징 인프라의 추상화입니다. 수신자의 메시지 핸들러 어댑터 클래스가 호출되어 메시지를 처리합니다. 이는 소비자의 비즈니스 로직에 의해 구현된 수신 포트 인터페이스를 호출합니다. 채널로부터 메시지를 보내는 송신자는 여러 명일 수 있습니다. 마찬가지로 채널에서 메시지를 수신하는 수신자도 여러 명일 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

Point-To-Point 및 Publish-Subscribe 두 가지 채널 유형을 이해하는 것이 매우 중요합니다.

- Point-to-Point 채널은 채널에서 읽고 있는 소비자 중 정확히 한 명에게 메시지를 전달합니다. 서비스는 일대일 상호 작용 스타일을 위해 Point-to-Point 채널을 사용합니다. 예를 들어, 명령 메시지는 종종 Point-to-Point 채널을 통해 전송됩니다.
- Publish-Subscribe 채널은 각 메시지를 연결된 모든 소비자에게 전달합니다. 서비스는 일대다 상호 작용 스타일을 위해 Publish-Subscribe 채널을 사용합니다. 예를 들어, 이벤트 메시지는 일반적으로 Publish-Subscribe 채널을 통해 전송됩니다.

이제 비동기 통신의 개념 및 메시지 채널에 대한 명확한 이해를 가졌으므로, 비동기 통신 프레임워크에서 제공하는 다양한 통신 메커니즘의 구현을 탐색하는 것이 적절합니다.

## 요청/응답 및 비동기 요청/응답 구현하기

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

클라이언트와 서비스가 요청/응답 또는 비동기 요청/응답을 사용하여 상호 작용할 때, 클라이언트는 요청을 보내고 서비스는 응답을 다시 보냅니다. 두 상호 작용 스타일 간의 차이점은 요청/응답의 경우 클라이언트가 서비스가 즉시 응답할 것으로 기대하는 데 반해, 비동기 요청/응답에서는 그러한 기대가 없다.
메시징은 본질적으로 비동기이므로 비동기적인 요청/응답만을 제공합니다. 그러나 클라이언트는 응답을 받을 때까지 블로킹될 수 있습니다.

클라이언트와 서비스는 쌍으로 메시지를 교환하여 비동기 요청/응답 스타일의 상호 작용을 구현합니다. 다이어그램에서 보듯이, 클라이언트는 작업을 수행할 명령 메시지와 해당 매개변수를 지정하는 콘텐츠를 서비스가 소유한 포인트 투 포인트 메시징 채널로 보냅니다. 서비스는 요청을 처리하고 결과를 포함하는 응답 메시지를 클라이언트가 소유한 포인트 투 포인트 채널로 보냅니다.

위 다이어그램에서 클라이언트는 응답 메시지를 보낼 서비스에 알려주어야 하며 응답 메시지를 요청과 일치시켜야 합니다. 다행히, 이 두 문제를 해결하는 것은 그렇게 어렵지 않습니다. 클라이언트는 응답 채널 헤더가 있는 명령 메시지를 보냅니다.
서버는 응답 메시지를 작성할 때, 메시지 식별자와 동일한 값이 있는 상관 ID를 포함하여 응답 채널에 작성합니다. 클라이언트는 상관 ID를 사용하여 응답 메시지를 요청과 일치시킵니다.

클라이언트와 서비스가 메시징을 사용하여 통신하기 때문에 상호 작용은 본질적으로 비동기적입니다. 이론적으로 메시징 클라이언트는 응답을 받을 때까지 블로킹될 수 있지만, 실제로는 클라이언트가 응답을 비동기적으로 처리합니다. 게다가, 응답은 일반적으로 클라이언트의 여러 인스턴스 중 하나에 의해 처리됩니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 단방향 통지 구현

비동기 메시징을 사용하여 단방향 통지를 구현하는 것은 간단합니다. 클라이언트는 일반적으로 명령 메시지를 보내고, 해당 메시지를 서비스가 소유한 점대점 채널로 전송합니다. 서비스는 해당 채널을 구독하고 메시지를 처리합니다. 답신을 보내지 않는다는 점에 유의하세요.
이를 "비동기 요청/응답"에서 본 다이어그램과 비슷한 방식이라고 생각할 수 있지만, 답신 채널이 없다는 차이가 있습니다.

## 게시/구독 구현

클라이언트는 게시/구독 채널로 메시지를 게시하고, 여러 소비자가 읽습니다.
서비스는 게시/구독을 사용하여 도메인 이벤트를 게시하는데, 이는 도메인 객체의 변경을 나타냅니다. 도메인 이벤트를 발행하는 서비스는 도메인 클래스에서 파생된 채널을 소유하며, 특정 도메인 객체의 이벤트에 관심이 있는 서비스는 해당 채널을 구독하면 됩니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

<img src="/assets/img/2024-05-27-AGuidetoCommunicationStylesinMicroservicesArchitecture_1.png" />

## Publish/Async Response 구현하기

퍼블리시/비동기 응답 상호작용 스타일은 퍼블리시/서브스크라이브와 요청/응답 요소를 결합하여 구현하는 더 높은 수준의 상호작용 스타일입니다.
클라이언트는 응답 채널 헤더를 지정하는 메시지를 퍼블리시-서브스크라이브 채널에 발행합니다. 소비자는 요청 채널로의 응답 메시지를 포함하여 상관 ID를 포함한 응답 메시지를 작성합니다.
클라이언트는 요청과 응답 메시지를 일치시키기 위해 상관 ID를 사용하여 응답을 수집합니다.

비동기 API를 갖는 응용 프로그램의 각 서비스는 이러한 구현 기법 중 하나 이상을 사용합니다. 작업을 호출하는 비동기 API를 가진 서비스에는 요청을 위한 메시지 채널이 있습니다. 마찬가지로 이벤트를 발행하는 서비스는 이벤트 메시지 채널에 이를 발행할 것입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 메시지 브로커 사용하기

메시지 기반 애플리케이션은 일반적으로 메시지 브로커를 사용하는데, 이를 통해 서비스가 통신하는 인프라 서비스입니다. 그러나 브로커 기반 아키텍처는 유일한 메시징 아키텍처가 아닙니다. 브로커를 사용하지 않는 메시징 아키텍처도 있습니다. 이 경우 서비스는 다른 서비스와 직접 통신합니다 (이 블로그 포스트에서는 이 주제를 다루지 않습니다).

## 브로커 기반 메시징 개요

메시지 브로커는 모든 메시지가 흐르는 중간 매개체입니다. 발신자는 메시지를 메시지 브로커에 작성하고, 메시지 브로커가 수신자에게 전달합니다. 메시지 브로커를 사용하는 중요한 이점 중 하나는 발신자가 소비자의 네트워크 위치를 알 필요가 없다는 것입니다. 또 다른 이점은 메시지 브로커가 메시지를 버퍼링하여 소비자가 처리할 수 있을 때까지 메시지를 저장한다는 것입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

많은 메시지 브로커를 선택할 수 있습니다. 인기있는 오픈 소스 메시지 브로커의 예시는 다음과 같습니다:

- ActiveMQ
- RabbitMQ
- Apache Kafka

각 브로커는 다른 트레이드오프를 가지고 있습니다. 예를 들어, 매우 낮은 레이턴시 브로커는 순서 보장을 유지하지 않을 수 있고, 메시지 전달을 보장하지 않으며 메시지를 메모리에만 저장할 수 있습니다.
메시지 전달을 보장하고 메시지를 신뢰성 있게 디스크에 저장하는 브로커는 높은 레이턴시를 가지고 있을 것입니다.

어떤 종류의 메시지 브로커가 가장 잘 맞는지는 애플리케이션의 요구사항에 따라 다릅니다. 심지어 애플리케이션의 다른 부분도 서로 다른 메시징 요구사항을 가질 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 메시지 브로커를 사용한 메시지 채널 구현

각 메시지 브로커는 메시지 채널 개념을 다르게 구현합니다. 표에서 볼 수 있듯이, JMS 메시지 브로커인 ActiveMQ와 같은 메시지 브로커는 대기열과 토픽을 가지고 있습니다. RabbitMQ와 같은 AMQP 기반의 메시지 브로커는 거래소와 대기열이 있습니다. Apache Kafka는 토픽을 가지고 있고, AWS Kinesis는 스트림을, AWS SQS는 대기열을 가지고 있습니다.
또한, 이 챕터에서 설명한 메시지와 채널 추상화보다 더 유연한 메시징을 제공하는 메시지 브로커도 있습니다.

여기서 설명된 대부분의 메시지 브로커는 포인트 투 포인트 및 게시-구독 채널을 모두 지원합니다. 유일한 예외는 AWS SQS로, 이는 포인트 투 포인트 채널만을 지원합니다.

# 메시지 브로커의 문제점

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 경쟁 수신기와 메시지 순서 정렬

한 가지 도전 과제는 메시지 수신기의 확장과 동시에 메시지 순서를 보존하는 방법입니다. 메시지를 동시에 처리하기 위해 서비스의 여러 인스턴스를 사용하는 것은 보편적인 요구사항입니다.
게다가 심지어 단일 서비스 인스턴스도 여러 스레드를 사용하여 여러 메시지를 동시에 처리할 수 있습니다. 여러 스레드와 서비스 인스턴스를 사용하여 메시지를 동시에 처리하는 것은 응용 프로그램의 처리량을 증가시킵니다.
그러나 메시지를 동시에 처리하는 과제는 각 메시지가 한 번에 한 번씩 순서대로 처리되는 것을 보장하는 것입니다.

예를 들어, 특정 지점 간편 채널(point-to-point channel)에서 읽는 세 개의 서비스 인스턴스가 있다고 가정해보겠습니다. 그리고 송신자가 주문 생성, 주문 업데이트 및 주문 취소 이벤트 메시지를 순차적으로 발행합니다.
간단한 메시징 구현은 각 메시지를 서로 다른 수신기에 동시에 전달할 수 있습니다. 네트워크 문제나 가비지 컬렉션으로 인한 지연 때문에 메시지가 순서대로 처리되지 않을 수 있으며, 이는 이상한 동작을 초래할 수 있습니다. 이론적으로, 한 서비스 인스턴스가 주문 취소 메시지를 처리하기 전에 다른 서비스가 주문 생성 메시지를 처리할 수 있습니다!

아파치 카프카(Apache Kafka)나 AWS Kinesis와 같은 현대적인 메시지 브로커에서 사용되는 일반적인 해결책은 샤딩(파티셔닝)된 채널을 사용하는 것입니다.
다음 다이어그램은 이 작동 방식을 보여줍니다. 이 해결책에는 세 부분이 있습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 샤드 채널은 두 개 이상의 샤드로 구성되어 있으며 각 샤드는 채널처럼 작동합니다.
- 발신자는 메시지 헤더에 샤드 키를 지정하며, 이는 일반적으로 임의의 문자열 또는 바이트 시퀀스입니다. 메시지 브로커는 샤드 키를 사용하여 메시지를 특정 샤드/파티션에 할당합니다. 예를 들어, 샤드 키의 해시를 샤드 수로 나눈 나머지를 계산하여 샤드를 선택할 수 있습니다.
- 메시징 브로커는 수신자의 여러 인스턴스를 그룹화하고 동일한 논리적 수신자로 처리합니다. 예를 들어 Apache Kafka는 소비자 그룹이라는 용어를 사용합니다. 메시지 브로커는 각 샤드를 단일 수신자에 할당합니다. 수신자가 시작하거나 종료될 때 샤드를 재할당합니다.

이 예에서 각 주문 이벤트 메시지는 orderId를 샤드 키로 사용합니다. 특정 주문에 대한 각 이벤트는 동일한 샤드에 발행되며, 단일 소비자 인스턴스가 해당 샤드를 읽습니다.
결과적으로 이러한 메시지는 순서대로 처리되도록 보장됩니다.

## 중복 메시지 처리

메시지를 사용할 때 대면해야 하는 또 다른 과제는 중복 메시지 처리입니다. 메시지 브로커는 이상적으로 각 메시지를 한 번만 전달해야 하지만, 정확히 한 번 메시지를 보장하는 것은 보통 너무 비용이 많이 듭니다. 대신 대부분의 메시지 브로커는 적어도 한 번 메시지를 전달할 것을 약속합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

시스템이 정상적으로 작동할 때 최소한 한 번의 전달을 보장하는 메시지 브로커는 각 메시지를 한 번만 전달합니다. 그러나 클라이언트, 네트워크 또는 메시지 브로커의 장애로 인해 메시지가 여러 번 전달될 수 있습니다.
예를 들어, 클라이언트가 메시지를 처리하고 데이터베이스를 업데이트한 후에 메시지를 확인하기 전에 충돌하는 경우가 있습니다.
메시지 브로커는 확인되지 않은 메시지를 다시 전달하며, 클라이언트가 다시 시작되거나 클라이언트의 복제본 중 하나로 전달합니다.

가능하다면, 메시지가 재전달될 때 순서가 유지되는 메시지 브로커를 사용하는 것이 이상적입니다.

예를 들어, 클라이언트가 동일한 주문에 대한 주문 생성 이벤트를 처리한 후 주문 취소 이벤트를 처리하고, 어떤 이유로든 주문 생성 이벤트를 확인하지 않은 상황을 상상해보겠습니다.
메시지 브로커는 주문 생성 및 주문 취소 이벤트를 다시 전달해야 합니다. 주문 생성만 재전달하는 경우, 클라이언트가 주문 취소를 취소할 수 있습니다.

중복 메시지를 처리하는 몇 가지 다른 방법이 있습니다:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 아이덴포턴트 메시지 핸들러를 작성하십시오.
- 메시지를 추적하고 중복을 제거하십시오.

각 옵션을 상세히 살펴보고, 이후에 각각에 대한 별도의 블로그 포스트를 작성할 예정입니다.

## 아이덴포턴트 메시지 핸들러 작성

메시지를 처리하는 응용 프로그램 로직이 아이덴포턴트하다면, 중복된 메시지는 해를 끼치지 않습니다. 응용 프로그램 로직이 아이덴포턴트하다는 것은 동일한 입력 값으로 여러 번 호출하더라도 추가적인 효과가 없다는 것을 의미합니다. 예를 들어, 이미 취소된 주문을 다시 취소하는 것은 아이덴포턴트한 작업입니다. 또한, 클라이언트가 제공한 ID로 주문을 생성하는 것도 아이덴포턴트한 작업입니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

메시지 핸들러가 idempotent할 때 여러 번 안전하게 실행될 수 있습니다, 단 메시지 브로커가 메시지를 재전달할 때 순서를 유지해야 합니다.

하지만, 응용 프로그램 로직은 종종 idempotent하지 않습니다. 또는 메시지 브로커가 메시지를 재전달할 때 순서를 보장하지 않는 경우도 있습니다. 중복 또는 순서가 올바르지 않은 메시지는 버그를 유발할 수 있습니다. 이러한 상황에서는 메시지를 추적하고 중복 메시지를 삭제하는 메시지 핸들러를 작성해야 합니다.

## 메시지 추적 및 중복 삭제

예를 들어 소비자 신용카드를 승인하는 메시지 핸들러를 고려해보겠습니다. 각 주문에 대해 카드를 정확히 한 번 승인해야 합니다. 이 응용 프로그램 로직의 경우 각 호출 시 다른 효과가 있습니다. 중복 메시지로 인해 메시지 핸들러가 이 로직을 여러 차례 실행하면 응용 프로그램이 잘못 동작할 수 있습니다. 이러한 종류의 응용 프로그램 로직을 실행하는 메시지 핸들러는 중복 메시지를 감지하고 삭제함으로써 독립적이어야 합니다.
간단한 해결책은 메시지 소비자가 처리한 메시지를 메시지 ID를 사용하여 추적하고 중복을 버리는 것입니다. 예를 들어, 각 처리된 메시지의 메시지 ID를 데이터베이스 테이블에 저장할 수 있습니다. 다음 다이어그램은 전용 테이블을 사용하여 이러한 작업을 하는 방법을 보여줍니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

소비자가 메시지를 처리할 때, 메시지 ID를 데이터베이스 테이블에 기록합니다. 이는 비즈니스 엔티티를 생성하고 업데이트하는 트랜잭션의 일부로서 수행됩니다. 이 예제에서 소비자는 PROCESSED_MESSAGES 테이블에 메시지 ID를 포함한 행을 삽입합니다. 만약 메시지가 중복이면, 삽입이 실패하고 소비자는 메시지를 폐기할 수 있습니다.

또 다른 옵션은 메시지 핸들러가 전용 테이블 대신 애플리케이션 테이블에 메시지 ID를 기록하는 것입니다. 이 접근 방법은 NoSQL 데이터베이스를 사용할 때 유용합니다. NoSQL 데이터베이스는 트랜잭션 모델이 제한되어 두 개의 테이블을 업데이트하는 것을 지원하지 않기 때문입니다.

# 결론

요약하자면, 마이크로서비스 아키텍처에서 통신 스타일을 선택하는 것은 애플리케이션 전체의 효율성과 확장성에 중요합니다. 이 게시물에서 동기 호출부터 비동기 메시징까지 다양한 통신 메커니즘을 탐색해봤습니다. 각각의 장단점과 적합한 사용 사례가 있습니다. 올바른 통신 전략은 성능을 향상시킬 뿐만 아니라 서비스 상호작용에서 탄력성과 유연성을 보장합니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

커뮤니케이션 스타일을 선택할 때는 서비스 상호작용의 성격, 실시간 데이터 필요성, 그리고 서비스 복잡성과 같은 요소를 고려하는 것이 중요합니다. 조직의 요구 사항과 기술적 발전에 맞게 진화할 수 있는 견고한 아키텍처를 육성하는 것이 목표입니다.

# 참고 자료

- Microservices Patterns — Chris Richardson
- Building Microservices: Designing Fine-Grained Systems 2nd Edition — Sam Newman
- Building Event-Driven Microservices: Leveraging Organizational Data at Scale
- Enterprise Integration Patterns — Gregor Hohpe
