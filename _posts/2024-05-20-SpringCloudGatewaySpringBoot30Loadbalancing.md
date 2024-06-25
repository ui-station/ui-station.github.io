---
title: "스프링 클라우드 게이트웨이  스프링 부트 30  로드 밸런싱"
description: ""
coverImage: "/assets/img/2024-05-20-SpringCloudGatewaySpringBoot30Loadbalancing_0.png"
date: 2024-05-20 15:39
ogImage:
  url: /assets/img/2024-05-20-SpringCloudGatewaySpringBoot30Loadbalancing_0.png
tag: Tech
originalTitle: "Spring Cloud Gateway + Spring Boot 3.0 + Load balancing"
link: "https://medium.com/@mominjahid/spring-cloud-gateway-spring-boot-3-0-load-balancing-46a5cbb9798f"
---

![Spring Cloud Gateway](/assets/img/2024-05-20-SpringCloudGatewaySpringBoot30Loadbalancing_0.png)

Spring Cloud Gateway는 Spring Boot 애플리케이션에서 API 게이트웨이를 구축하는 강력하고 유연한 솔루션을 제공합니다. 다양한 기능을 갖춘 Spring Cloud Gateway는 라우팅, 요금 제한, 보안 및 장애 관리를 처리할 수 있습니다.

![Spring Cloud Gateway](/assets/img/2024-05-20-SpringCloudGatewaySpringBoot30Loadbalancing_1.png)

마이크로서비스 아키텍처 시대에는 견고하고 확장 가능한 시스템 구축이 중요합니다. Spring Boot와 Spring Cloud의 발전으로 개발자들은 이전 Netflix Zuul에 의존하지 않고도 견고한 마이크로서비스 기반 애플리케이션을 생성할 수 있는 강력한 도구를 사용할 수 있습니다. 이 블로그 포스트에서는 Spring Boot 3.0과 함께 Spring Cloud Gateway를 사용하여 현대적인 마이크로서비스 아키텍처를 어떻게 만들 수 있는지 살펴보겠습니다.

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

Spring Boot이 계속 발전함에 따라 더 최신 기술과 모범 사례에 적응하는 것이 중요합니다. Spring Boot 3.0에서는 마이크로서비스를 구축하기 위한 현대적인 솔루션에 중점을 두고 있습니다. Spring WebFlux 기반으로 구축된 강력한 API 게이트웨이인 Spring Cloud Gateway가 그러한 솔루션 중 하나로, 라우팅, 필터링, 로드 밸런싱과 같은 기능을 제공합니다.

게이트웨이를 설명하기 위해 적합한 아키텍처가 필요합니다.

- Eureka 서버: 마이크로서비스를 관리하기 위한 서비스 레지스트리 및 디스커버리 서버입니다. Eureka 서버와 함께 스프링 부트 프로젝트를 생성하세요.
- Payment MS: 테스트를 위한 사용자 정의 마이크로서비스로, 웹 및 Eureka 클라이언트 종속성 및 설정이 있는 간단한 마이크로서비스입니다.
- Spring Cloud Gateway: 적절한 마이크로서비스로 요청을 라우팅하는 API 게이트웨이입니다.

아래 종속성을 가진 Spring Boot 애플리케이션을 만들어 보세요. Eureka는 서비스 디스커버리 서버로 사용될 것입니다.

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

의존성 테이블을 다음과 같이 변경해보세요:

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>

- @EnableEurekaServer 주석을 사용하여 주 클래스에 Eureka 서버를 활성화하세요.

```java
@SpringBootApplication
@EnableEurekaServer
public class MyEurekaServerApplication {
```

```java
 public static void main(String[] args) {
  SpringApplication.run(MyEurekaServerApplication.class, args);
 }
}
```

그리고 My application.properties 파일에도 변경을 적용해주세요.

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

```bash
// application.properties
eureka.client.fetch-registry=false
eureka.client.register-with-eureka=false
server.port=8761
spring.application.name=MY-EUREKA-SERVER
```

아래 종속성을 사용하여 Spring Boot 어플리케이션을 생성하세요.

```js
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

```js
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
```

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

서버 포트를 출력하는 REST API를 작성해보세요. 이것은 테스트 중에 호출된 서비스의 어떤 인스턴스인지 식별하는 데 도움이 될 것입니다 [로드 밸런싱]

```java
@RestController
@RequestMapping("/payment")
public class PayController {
```

```java
@Value("${server.port}")
private String serverPort;

@GetMapping("/say")
public String getMethodName() {
    return new String("안녕하세요, Payment MS에서 온 메시지입니다. "+serverPort);
}
```

그리고 Eureka 서버 세부 정보를 추가하고 메인 클래스에 Eureka 클라이언트를 활성화하는 주석을 추가하는 것을 잊지 마세요 (@EnableDiscoveryClient)

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

```java
@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMsApplication {

 public static void main(String[] args) {
  SpringApplication.run(PaymentMsApplication.class, args);
 }
}
```

- 그리고 나의 MS를 위한 어플리케이션 프로퍼티

```java
spring.application.name=PAYMENT-SERVICE
eureka.client.service-url.defaultZone=http://localhost:8761/eureka
server.port=9811 (어플리케이션을 실행하기 전에 다른 포트를 사용 중입니다)
eureka.instance.ip-address=localhost
```

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

<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

<!--
https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-starter-gateway -->
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-gateway</artifactId>
   <version>4.1.2</version>
</dependency>

그래서 최신 버전(내 경우 3.2.4v)의 스프링 부트 프로젝트를 만들고 위의 종속성을 추가합니다.

- application.yml 파일에 라우팅 구성을 추가하십시오. `service-name` 프로토콜을 사용하여 API 게이트웨이에 서비스 검색을 지시합니다. 두 개의 payment-service 인스턴스가 있으므로 부하 처리가 필요하고 동적으로 처리해야 합니다.

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

```yaml
spring:
  application:
    name: gateway
  cloud:
    gateway:
      routes:
        - id: payment-service
          uri: lb://payment-service
          predicates:
            - Path=/payment/**
```

- API Gateway는 마이크로서비스로도 불리우며 유레카 서버에 등록되어야 합니다. 메인 클래스에 `@EnableDiscoveryClient`를 추가하지 않는 것을 잊지 마세요.

```java
@SpringBootApplication
@EnableDiscoveryClient
public class ApiGtApplication {
```

```java
 public static void main(String[] args) {
  SpringApplication.run(ApiGtApplication.class, args);
 }
}
```

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

# 지금 테스트해 보세요!

진행 방법:

- 유레카 서버 실행
- 포트 9810 및 9811에서 결제 서비스 실행
- API 게이트웨이 서비스 실행

![이미지](/assets/img/2024-05-20-SpringCloudGatewaySpringBoot30Loadbalancing_2.png)

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

브라우저에서 게이트웨이 엔드포인트를 방문해서 확인해보세요 (http://localhost:8080/payment/say)

/payment은 술어 패턴입니다 — 이 패턴이 발생하면 API 게이트웨이는 요청을 지불 서비스로 라우팅하고 지불 서비스에는 컨트롤러 /payment과 GET 메소드 /say가 있습니다. 이것이 실행되어 응답을 게이트웨이로 반환합니다. 유레카는 API 게이트웨이로 인스턴스 정보를 보내기 위한 것이며 내부 통신을 위한 것이 아닙니다.

독자 여러분의 읽어주셔서 감사합니다. 도움이 되었다면 공유하고 좋아요를 눌러주세요.
