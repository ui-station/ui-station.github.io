---
title: "Spring Boot3에서 트레이싱 활용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-TracinginSpringBoot3_0.png"
date: 2024-06-23 20:31
ogImage: 
  url: /assets/img/2024-06-23-TracinginSpringBoot3_0.png
tag: Tech
originalTitle: "Tracing in Spring Boot3"
link: "https://medium.com/javarevisited/tracing-in-spring-boot3-097205dc08f4"
---


![2024-06-23-TracinginSpringBoot3_0](/assets/img/2024-06-23-TracinginSpringBoot3_0.png)

제 초창기 전문가 시절을 돌이켜보면, 프로덕션 문제 해결 방식에서 조금 놀라운 점이 있었습니다. 모든 것을 다 다루는 프로젝트에서 일했는데, 고객과의 인터페이싱부터 코딩, 배포, 그리고 문제 해결까지 모든 것을 처리했죠. 문제가 발생하면 명쾌한 해결책이 없는 상황이었고, 그때의 나는 프로덕션 데이터베이스 덤프를 떠서 해당 데이터베이스를 이용해 내 컴퓨터에서 애플리케이션을 실행한 다음 고객과 통화하면서 그들이 하는 작업을 재현하고 문제점을 확인하기 위해 필요한 디버그 지점 및 출력문을 사용했어요. 그 방식은 그 당시에는 효과적이었습니다. 애플리케이션은 작고 사용자 베이스가 제한적이었으며, 단일 개발자로는 완전히 관리할 수 있었지만, 그때는 여전히 로깅의 중요성을 이해하지 못했죠.

몇 년 후, 이제는 여러 개발자, 제품 소유자, 스크럼 마스터, 운영팀, 인프라팀, 그리고 물론 수백만 명의 사용자가 참여하는 프로젝트에서 일하고 있습니다. 중요한 점은 개발자에게는 프로덕션 환경이 대부분 접근 불가능하며, 또한 고객이 개발자와 직접 커뮤니케이션할 수 없으므로 우리를 미치게 만든다는 사실입니다.

운영팀은 이 두 영역 사이의 다리 역할을 했습니다. 그들의 주요 업무는 시스템, 애플리케이션 로그, 필요시 데이터베이스를 조사하여 문제를 해결하는 것이었죠. 해결책을 찾지 못하거나 향상 시야를 가지고 있다면 해당 데이터로 개발자에게 접근하게 됐습니다.

<div class="content-ad"></div>

이 환경에서 작업하면서 로그의 중요성을 점차 알게 되었습니다. 로그는 응용프로그램이나 거래의 이벤트를 추적하는 데 도움이 됩니다. 적절한 분석을 통해 패턴을 찾거나 이상 현상을 예측하는 데 도움이 됩니다.

하지만 분산 추적에 대해 아직 많이 알지 못했습니다. 다양한 식별자(전화번호, 사용자 ID 등)를 추가하여 운영팀이 고객 문제를 적절히 조사할 수 있도록 했습니다.

그러나 이 방법에도 문제가 있었습니다. 동일한 고객이 시스템과 다양한 상호 작용을 할 수 있어, 해당 식별자로 여러 로그 스트림이 생성되었습니다. 따라서 문제에 대한 정확한 로그를 찾는 것은 여전히 번거로웠습니다. 여러 애플리케이션이 관련될 때의 고통은 시작도 못 했죠. 서비스 호출 간 페이로드에 수동으로 UUID를 추가하고 로그를 남겼습니다.

결국에는 내 현재 접근 방식이 부족하다는 것이 명백해졌습니다. 내 일부 응용프로그램은 다른 팀이 유지보수하는 다른 애플리케이션 사이에서 미들웨어로 작용했습니다. 그들이 내 불편함을 용인해야 할 이유가 있을까요?

<div class="content-ad"></div>

그런 다음, Sleuth를 발견했어요. 정말 놀라웠죠; 단 하나의 의존성을 추가하고 로깅을 구성하기만 하면 어플리케이션 전체에 대한 추적을 활성화할 수 있었어요. 여러 개의 마이크로서비스를 사용하더라도 서비스 간 추적 ID를 전파하기 위한 추가적인 조치가 필요하지 않았어요.

Spring Boot 3 이전에는 프로젝트를 시작할 때마다 항상 Spring Cloud Sleuth를 포함해 분산 추적을 활성화했어요.

그런 다음 Spring Boot 3이 나오면서 Sleuth가 Micrometer로 이관되었어요. 이 기사에서는 Micrometer 추적을 활용해 Spring Boot 3에서 Sleuth와 유사한 기능을 어떻게 구현하는지 살펴볼 거예요.

지금은 컨트롤러와 서비스가 있는데, 일부 로그를 사용하고 있어요. 포스트맨으로 컨트롤러 엔드포인트에 요청을 보내면 다음과 같은 로그를 얻게 돼요:

<div class="content-ad"></div>

```js
2024-02-23T23:28:18.043+06:00  INFO 14443 --- [rest-api] [nio-8080-exec-2] x.r.t.restapi.web.MessageController   : Received message: Message(header=some header, content=some content)
2024-02-23T23:28:18.046+06:00  INFO 14443 --- [rest-api] [nio-8080-exec-2] x.r.t.r.service.MessageServiceImpl    : Handling message Message(header=some header, content=some content)
```

추적이 없다는 것을 명확하게 알 수 있어요. 이제 다음 종속성을 추가할 거에요:

```js
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-brave</artifactId>
    <scope>compile</scope>
</dependency>
```

동일한 엔드포인트를 한 번 더 호출해보세요. 그런데 여전히 운이 없거나 로그가 없어요!

<div class="content-ad"></div>

인터넷 검색 결과를 통해 작업을 수행하기 위해 액추에터도 추가해야 한다는 것을 알아냈습니다.

```js
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-actuator</artifactId>
     <scope>compile</scope>
</dependency>
```

동일한 엔드포인트를 다시 요청하면 다음과 같은 로그가 생성됩니다:

```js
2024-02-24T00:11:06.050+06:00  INFO 18463 --- [rest-api] [nio-8080-exec-1] [65d8dfb96a137925ba56091d26f33e80-ba56091d26f33e80] x.r.t.restapi.web.MessageController   : Received message: Message(header=some header, content=some content)
2024-02-24T00:11:06.053+06:00  INFO 18463 --- [rest-api] [nio-8080-exec-1] [65d8dfb96a137925ba56091d26f33e80-ba56091d26f33e80] x.r.t.r.service.MessageServiceImpl    : Handling message Message(header=some header, content=some content)
```

<div class="content-ad"></div>

여기 보세요. [65d8dfb96a137925ba56091d26f33e80-ba56091d26f33e80] 이 부분에는 트레이스 ID와 스팬 ID가 포함되어 있습니다.

이제 다른 서비스를 호출할 수 있는지 확인해 봅시다. 그리고 트레이스 ID가 거기로 전달되는지도 확인해 봅니다.

저는 rest-api-2 라는 다른 서비스를 만들었습니다. 해당 서비스에는 필요한 컨트롤러, 서비스 및 필요한 종속성이 포함되어 있습니다.

이제 새롭고 화려한 rest-client를 사용하여 rest-api에서 rest-api-2로 HTTP 호출을 해보겠습니다.

<div class="content-ad"></div>

rest-api에서 MessageServiceImpl을 수정한 내용은 다음과 같습니다:

```js
@Service
@Slf4j
public class MessageServiceImpl implements MessageService {

 private final RestClient restClient;
 public MessageServiceImpl(@Value("${rest-api-2.url}") String restApi2Url) {
     this.restClient  = RestClient.builder().baseUrl(restApi2Url)
         .defaultHeader("Content-Type", MediaType.APPLICATION_JSON_VALUE).build();

 }

 @Override
 public void handleMessage(Message message) {
     log.info("처리할 메시지: {}", message);
     this.restClient.post().uri( "/process-message").body(message).retrieve();

 }

}
```

여기서 생성자에서 rest 클라이언트의 인스턴스를 선언했습니다. 그런 다음, 서비스에서 rest-api-2로 호출을 수행했습니다.

rest-api의 로그:

<div class="content-ad"></div>

```js
2024-02-24T14:31:13.060+06:00  INFO 29880 --- [rest-api] [nio-8080-exec-5] [65d9a95114390c055da56f5c4e138be3-5da56f5c4e138be3] x.r.t.restapi.web.MessageController   : Received message: Message(header=some header, content=some content)
2024-02-24T14:31:13.061+06:00  INFO 29880 --- [rest-api] [nio-8080-exec-5] [65d9a95114390c055da56f5c4e138be3-5da56f5c4e138be3] x.r.t.r.service.MessageServiceImpl    : Handling message Message(header=some header, content=some content)
```

rest-api-2의 로그:

```js
2024-02-24T14:31:13.072+06:00  INFO 29563 --- [rest-api-2] [nio-8081-exec-7] [65d9a9513ed86cb5ea2477e08166b9d2-ea2477e08166b9d2] x.r.t.r.web.MessageProcessorController   : Received message for processing Message(header=some header, content=some content)
2024-02-24T14:31:13.072+06:00  INFO 29563 --- [rest-api-2] [nio-8081-exec-7] [65d9a9513ed86cb5ea2477e08166b9d2-ea2477e08166b9d2] x.r.t.r.s.MessageProcessorServiceImpl : Processing Message Message(header=some header, content=some content)
```

rest-api-2의 traceId와 spanId가 있는 것을 확인할 수 있어요. 하지만, 이들은 일치하지 않아요! 각 요청마다 새로운 추적 컨텍스트를 시작하고 있어요.

<div class="content-ad"></div>

이는 첫 번째 애플리케이션에서의 traceId가 두 번째 애플리케이션으로 전파되지 않는 것을 의미합니다.

이를 확인해 보겠습니다.

rest-api-2 애플리케이션을 중지하고, 해당 애플리케이션이 실행되던 포트를 듣기 시작한 것을 netcat이라는 도구로 확인했습니다:

![이미지](/assets/img/2024-06-23-TracinginSpringBoot3_1.png)

<div class="content-ad"></div>

이제 엔드포인트가 호출되면 다음과 같은 내용을 받습니다:

<img src="/assets/img/2024-06-23-TracinginSpringBoot3_2.png" />

보시다시피, traceId를 포함한 일부 헤더가 있어야 하는데 없습니다. 요청에 추적 컨텍스트가 포함된 적절한 헤더가 있는지 확인해야 합니다.

이건 그렇게 어렵지 않아요. 우리는 스프링 부트가 자동으로 구성한 기본 restClient 빌더를 사용해야 합니다. 이것은 traceId를 전파하기 위한 필요한 지식을 갖고 있습니다. 다음과 같이 할 수 있어요:

<div class="content-ad"></div>

```js
public MessageServiceImpl(@Value("${rest-api-2.url}") String restApi2Url, RestClient.Builder restClientBuilder) {
     this.restClient  = restClientBuilder.baseUrl(restApi2Url)
         .defaultHeader("Content-Type", MediaType.APPLICATION_JSON_VALUE).build();

 }
```

우리는 서비스의 생성자를 통해 기본 RestClient.Builder를 받았습니다. 코드의 나머지 부분은 간단합니다. 이제 요청을 초기화하면 다음과 같은 것을 볼 수 있습니다:

<img src="/assets/img/2024-06-23-TracinginSpringBoot3_3.png" />

이제 우리에게 traceId를 포함한 traceparent라는 헤더가 있습니다. 이제 netcat을 중지하고 rest-api-2 애플리케이션을 시작하겠습니다.

<div class="content-ad"></div>

첫 번째 애플리케이션 로그:

![2024-06-23-TracinginSpringBoot3_4.png](/assets/img/2024-06-23-TracinginSpringBoot3_4.png)

두 번째 애플리케이션 로그:

![2024-06-23-TracinginSpringBoot3_5.png](/assets/img/2024-06-23-TracinginSpringBoot3_5.png)

<div class="content-ad"></div>

그리고 빙고!

두 애플리케이션 간에 트레이스 ID가 일치하는 것을 볼 수 있습니다. 단, spanId는 일치하지 않지만, 이는 예상한 바입니다.

서비스를 리팩토링할 수 있는 좋은 시기라고 생각합니다. 여기서 rest-client를 선언하는 대신, 구성 클래스에서 재사용 가능한 빈으로 선언할 것입니다.

Rest client 구성 클래스는 다음과 같습니다:

<div class="content-ad"></div>

```java
@Configuration
public class RestClientConfig {
 @Bean("restApi2Client")
 RestClient restApi2Client(@Value("${rest-api-2.url}") String restApi2Url, RestClient.Builder restClientBuilder) {
     return restClientBuilder.baseUrl(restApi2Url)
         .defaultHeader("Content-Type", MediaType.APPLICATION_JSON_VALUE).build();
 }

}
```

And refactored MessageServiceImpl class:

```java
@Service
@Slf4j
public class MessageServiceImpl implements MessageService {

 private final RestClient restClient;

 public MessageServiceImpl(@Qualifier("restApi2Client") RestClient restClient) {
     this.restClient = restClient;
 }


 @Override
 public void handleMessage(Message message) {
     log.info("Handling message {}", message);
     this.restClient.post().uri( "/process-message").body(message).retrieve();

 }

}
```

Cleaner right ?

<div class="content-ad"></div>

이것은 단순한 데모입니다. Micrometer를 활용하여 다른 관측 가능성 사용 사례에 대해 더 많이 쓸 예정이에요.

여기 모든 코드가 있는 레포입니다.

즐거운 코딩하세요!

![이미지](https://miro.medium.com/v2/resize:fit:960/1*5D9ZTssrYkDI-7hN0fAVkw.gif)