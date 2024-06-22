---
title: "Spring Boot로 API 게이트웨이 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-APIgatewayinSpringboot_0.png"
date: 2024-06-22 22:18
ogImage: 
  url: /assets/img/2024-06-22-APIgatewayinSpringboot_0.png
tag: Tech
originalTitle: "API gateway in Spring boot"
link: "https://medium.com/@ankithahjpgowda/api-gateway-in-spring-boot-3ea804003021"
---


![API Gateway](/assets/img/2024-06-22-APIgatewayinSpringboot_0.png)

API는 응용 프로그램 간에 일반적인 통신 방식입니다. 마이크로서비스 아키텍처의 경우 여러 서비스가 있으며 클라이언트는 이러한 서비스를 호출하기 위해 모든 하위 응용 프로그램의 호스트 이름을 알아야 합니다.

이 통신을 간소화하기 위해 클라이언트와 서버 사이에 모든 API 요청을 관리하는 컴포넌트인 API 게이트웨이를 선호합니다. 또한 다음과 같은 다른 기능을 가질 수 있습니다:

- 보안 — 인증, 권한 부여
- 라우팅 — 라우팅, 요청/응답 조작, 회로 차단기
- 관찰성 — 지표 집계, 로깅, 추적화

<div class="content-ad"></div>

API 게이트웨이의 아키텍처적 이점:

- 복잡성 감소
- 정책의 중앙 집중화된 제어
- 단순화된 문제 해결

API 게이트웨이에는 Spring Cloud Gateway, Zuul API Gateway, APIGee, EAG (Enterprise API Gateway)와 같은 다양한 구현 유형이 있습니다.

본 문서에서는 Spring Cloud API 게이트웨이를 구현하는 방법, 들어오는 요청 필터링, 요청/응답 조작, 인증 처리에 대해 살펴볼 것입니다.

<div class="content-ad"></div>

아래에서 전체 에코시스템을 시각화할 수 있습니다:

![에코시스템 다이어그램](/assets/img/2024-06-22-APIgatewayinSpringboot_1.png)

위 다이어그램에서 총 5개의 서비스가 있습니다.

- 서비스 레지스트리 — 각 프로젝트 내의 각 마이크로서비스 인스턴스의 가용 상태를 추적하는 응용 프로그램입니다.
- API 게이트웨이 — 들어오는 요청을 수신하고 인증(활성화되어 있다면)을 수행하고 실제 마이크로서비스로 요청을 전달합니다. 응답을 받으면 소비자에게 반환합니다.
- 인증 서버 — 인증을 처리하는 응용 프로그램입니다.
- 첫 번째와 두 번째 마이크로서비스 — 서로 다른 기능을 갖는 두 개의 일반 내부 응용 프로그램입니다.

<div class="content-ad"></div>

모든 애플리케이션은 시작 시 서비스 레지스트리에 등록됩니다. API 요청을 받으면 다음과 같은 단계가 발생합니다:

- 소비자가 API 게이트웨이를 통해 애플리케이션을 호출합니다.
- API 게이트웨이는 수신 URL이 인증이 필요한지 확인합니다. 필요하다면 인증 서버를 호출하여 유효성을 검사합니다.
- 유효한 토큰이라면 필터를 적용한 후 해당 애플리케이션으로 요청을 전달합니다.
- 유효하지 않은 토큰일 경우 소비자에게 권한이 없음을 응답합니다.
- 내부 마이크로서비스에서 응답을 받으면 필터를 적용한 후 해당 소비자에게 반환합니다.

게이트웨이에서의 필터는 로깅, 요청/응답 세부 사항 조작/사용자 정의와 같은 작업이 포함될 수 있습니다.

Service registry

<div class="content-ad"></div>

유레카 서버로 작동하는 애플리케이션이에요. pom.xml에 다음 종속성이 있어야 해요.

```js
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

그리고 메인 클래스에 `@EnableEurekaServer` 어노테이션이 있어야 해요.

기본적으로 유레카 서버는 자신을 디스커버리에 등록하는데, 이를 비활성화하기 위해 application.properties에 아래 속성을 포함해야 해요.

<div class="content-ad"></div>

```js
eureka.client.registerWithEureka = false
eureka.client.fetchRegistry = false
```

다른 애플리케이션을 Eureka 클라이언트로 만들려면 pom.xml에 아래 종속성을 포함하세요:

```js
  <dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
```

그리고, application.properties 파일에 Eureka 서버 URL을 제공해주세요:

<div class="content-ad"></div>


eureka.client.serviceUrl.defaultZone= <eureka-서버-가동-호스트-및-포트>

API 게이트웨이:

Spring Cloud API 게이트웨이에는 pom.xml에 아래 종속성이 필요합니다.

```xml
<dependencies>
  <dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-gateway</artifactId>
  </dependency>
</dependencies>
 <dependencyManagement>
  <dependencies>
   <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-dependencies</artifactId>
    <version>${spring-cloud.version}</version>
    <type>pom</type>
    <scope>import</scope>
   </dependency>
  </dependencies>
 </dependencyManagement>
```

<div class="content-ad"></div>

application.yml 파일에서 모든 내부 마이크로서비스 이름, 경로 및 uri 세부 정보를 다음과 같이 제공해주세요:

```js
spring:
  application:
    name: gateway-service
  cloud:
    gateway:
      routes:
        - id: first
          predicates:
            - Path=/first/
          uri: localhost:8081
        - id: second
          predicates:
            - Path=/second/
          uri: localhost:8082
        - id: auth-server
          predicates:
            - Path=/login/
          uri: localhost:8088
```

그리고 Gateway가 제공하는 모든 라우트를 제공하기 위해 RouteLocator 유형의 빈이 필요합니다. 요청/응답을 처리하려면 필요에 따라 필터를 포함할 수 있습니다:

```js
@Bean
public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
    return builder.routes()
            .route("first-microservice", r -> r.path("/first")
                    .and().method("POST")
                    .and().readBody(Student.class, s -> true).filters(f -> f.filters(requestFilter, authFilter))
                    .uri("http://localhost:8081"))
            .route("first-microservice", r -> r.path("/first")
                    .and().method("GET").filters(f-> f.filters(authFilter))
                    .uri("http://localhost:8081"))
            .route("second-microservice", r -> r.path("/second")
                    .and().method("POST")
                    .and().readBody(Company.class, s -> true).filters(f -> f.filters(requestFilter, authFilter))
                    .uri("http://localhost:8082"))
            .route("second-microservice", r -> r.path("/second")
                    .and().method("GET").filters(f-> f.filters(authFilter))
                    .uri("http://localhost:8082"))
            .route("auth-server", r -> r.path("/login")
                    .uri("http://localhost:8088"))
            .build();
}
```

<div class="content-ad"></div>

필터:

A. 요청 본문을 로깅하려면:

본문을 읽기 위해 ResourceLocator 빈에서 readBody()를 true로 설정해야 합니다. 이렇게 하면 ServerWebExchange 객체가 요청 본문을 "cachedRequestBodyObject" 속성에 캐시합니다.

```js
package com.example.springcloudgatewayoverview.filter;

import com.example.springcloudgatewayoverview.model.Company;
import com.example.springcloudgatewayoverview.model.Student;
import org.springframework.cloud.gateway.filter.GatewayFilter;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Component
public class RequestFilter implements GatewayFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        Object body = exchange.getAttribute("cachedRequestBodyObject");
        System.out.println("in request filter");
        if(body instanceof Student) {
            System.out.println("body:" + (Student) body);
        }
        else if(body instanceof Company) {
            System.out.println("body:" + (Company) body);
        }
        return chain.filter(exchange);
    }
}
```

<div class="content-ad"></div>

B. 응답 본문을 로깅하려면:

```js
package com.example.springcloudgatewayoverview.filter;

import com.example.springcloudgatewayoverview.model.Company;
import com.example.springcloudgatewayoverview.model.Student;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.reactivestreams.Publisher;
import org.springframework.core.io.buffer.DataBuffer;
import org.springframework.core.io.buffer.DataBufferFactory;
import org.springframework.core.io.buffer.DefaultDataBuffer;
import org.springframework.core.io.buffer.DefaultDataBufferFactory;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.http.server.reactive.ServerHttpResponseDecorator;
import org.springframework.web.server.ServerWebExchange;
import org.springframework.web.server.WebFilter;
import org.springframework.web.server.WebFilterChain;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

import java.nio.charset.StandardCharsets;
import java.util.List;

public class PostGlobalFilter implements WebFilter {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, WebFilterChain chain) {
        String path = exchange.getRequest().getPath().toString();
        ServerHttpResponse response = exchange.getResponse();
        ServerHttpRequest request = exchange.getRequest();
        DataBufferFactory dataBufferFactory = response.bufferFactory();
        ServerHttpResponseDecorator decoratedResponse = getDecoratedResponse(path, response, request, dataBufferFactory);
        return chain.filter(exchange.mutate().response(decoratedResponse).build());
    }

    private ServerHttpResponseDecorator getDecoratedResponse(String path, ServerHttpResponse response, ServerHttpRequest request, DataBufferFactory dataBufferFactory) {
        return new ServerHttpResponseDecorator(response) {

            @Override
            public Mono<Void> writeWith(final Publisher<? extends DataBuffer> body) {

                if (body instanceof Flux) {

                    Flux<? extends DataBuffer> fluxBody = (Flux<? extends DataBuffer>) body;

                    return super.writeWith(fluxBody.buffer().map(dataBuffers -> {

                        DefaultDataBuffer joinedBuffers = new DefaultDataBufferFactory().join(dataBuffers);
                        byte[] content = new byte[joinedBuffers.readableByteCount()];
                        joinedBuffers.read(content);
                        String responseBody = new String(content, StandardCharsets.UTF_8);//응답 수정 및 수정된 응답 반환
                        System.out.println("requestId: "+request.getId()+", method: "+request.getMethodValue()+", req url: "+request.getURI()+", response body :"+ responseBody);
                        try {
                            if(request.getURI().getPath().equals("/first") && request.getMethodValue().equals("GET")) {
                                List<Student> student = new ObjectMapper().readValue(responseBody, List.class);
                                System.out.println("student:" + student);
                            }
                            else if(request.getURI().getPath().equals("/second") && request.getMethodValue().equals("GET")) {
                                List<Company> companies = new ObjectMapper().readValue(responseBody, List.class);
                                System.out.println("companies:" + companies);
                            }
                        } catch (JsonProcessingException e) {
                            throw new RuntimeException(e);
                        }
                        return dataBufferFactory.wrap(responseBody.getBytes());
                    })).onErrorResume(err -> {

                        System.out.println("error while decorating Response: {}"+err.getMessage());
                        return Mono.empty();
                    });

                }
                return super.writeWith(body);
            }
        };
    }

}
```

C. API 호출 전에 인증하기:

다음과 같이 인증 필터를 생성하십시오:

<div class="content-ad"></div>

```java
package com.example.springcloudgatewayoverview.filter;

import com.example.springcloudgatewayoverview.util.AuthUtil;
import com.example.springcloudgatewayoverview.util.JWTUtil;
import com.example.springcloudgatewayoverview.validator.RouteValidator;
import io.jsonwebtoken.Claims;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.cloud.gateway.filter.GatewayFilter;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Component
@RefreshScope
public class AuthFilter implements GatewayFilter {

    @Autowired
    RouteValidator routeValidator;

    @Autowired
    private JWTUtil jwtUtil;

    @Autowired
    private AuthUtil authUtil;

    @Value("${authentication.enabled}")
    private boolean authEnabled;

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        if(!authEnabled) {
            System.out.println("Authentication is disabled. To enable it, make \"authentication.enabled\" property as true");
            return chain.filter(exchange);
        }
        String token ="";
        ServerHttpRequest request = exchange.getRequest();

        if(routeValidator.isSecured.test(request)) {
            System.out.println("validating authentication token");
            if(this.isCredsMissing(request)) {
                System.out.println("in error");
                return this.onError(exchange,"Credentials missing",HttpStatus.UNAUTHORIZED);
            }
            if (request.getHeaders().containsKey("userName") && request.getHeaders().containsKey("role")) {
                token = authUtil.getToken(request.getHeaders().get("userName").toString(), request.getHeaders().get("role").toString());
            }
            else {
                token = request.getHeaders().get("Authorization").toString().split(" ")[1];
            }

            if(jwtUtil.isInvalid(token)) {
                return this.onError(exchange,"Auth header invalid",HttpStatus.UNAUTHORIZED);
            }
            else {
                System.out.println("Authentication is successful");
            }

            this.populateRequestWithHeaders(exchange,token);
        }
        return chain.filter(exchange);
    }

    private Mono<Void> onError(ServerWebExchange exchange, String err, HttpStatus httpStatus) {
        ServerHttpResponse response = exchange.getResponse();
        response.setStatusCode(httpStatus);
        return response.setComplete();
    }

    private String getAuthHeader(ServerHttpRequest request) {
        return  request.getHeaders().getOrEmpty("Authorization").get(0);
    }


    private boolean isCredsMissing(ServerHttpRequest request) {
        return !(request.getHeaders().containsKey("userName") && request.getHeaders().containsKey("role")) && !request.getHeaders().containsKey("Authorization");
    }

    private void populateRequestWithHeaders(ServerWebExchange exchange, String token) {
        Claims claims = jwtUtil.getALlClaims(token);
        exchange.getRequest()
                .mutate()
                .header("id",String.valueOf(claims.get("id")))
                .header("role", String.valueOf(claims.get("role")))
                .build();
    }
}
```

Endpoints 중 일부는 토큰 없이 호출을 허용해야 합니다. (예: 로그인 URL, 헬스 체크 URL 등). 이러한 엔드포인트들을 아래 목록에 추가할 것입니다:

```java
package com.example.springcloudgatewayoverview.validator;

import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.function.Predicate;

@Component
public class RouteValidator {
    public static final List<String> unprotectedURLs = List.of("/login");

    public Predicate<ServerHttpRequest> isSecured = request -> unprotectedURLs.stream().noneMatch(uri -> request.getURI().getPath().contains(uri));
}
```

JWTUtil:
```java
```

<div class="content-ad"></div>

```java
package com.example.springcloudgatewayoverview.util;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import java.util.Date;

@Component
public class JWTUtil {

    @Value("${jwt.secret}")
    private String secret;

    public Claims getAllClaims(String token) {
        return Jwts.parserBuilder().setSigningKey(secret).build().parseClaimsJws(token).getBody();
    }

    private boolean isTokenExpired(String token) {
        return this.getAllClaims(token).getExpiration().before(new Date());
    }

    public boolean isInvalid(String token) {
        return this.isTokenExpired(token);
    }

}
```

Authentication server:

이 서비스는 내부 마이크로서비스에 액세스하기 위한 토큰을 제공합니다.

실행:


<div class="content-ad"></div>

Once all applications are running, you can access the service registry by entering http://localhost:8761 in your browser. Here, you can find information about all the currently running services:

![Service Registry](/assets/img/2024-06-22-APIgatewayinSpringboot_2.png)

The API Gateway is running on localhost at port 8080. You can access the endpoints of the First or Second microservice from http://localhost:8080.

![API Gateway Endpoints](/assets/img/2024-06-22-APIgatewayinSpringboot_3.png)

<div class="content-ad"></div>

APIGateway 콘솔의 요청/응답 로그:

![2024-06-22-APIgatewayinSpringboot_4](/assets/img/2024-06-22-APIgatewayinSpringboot_4.png)

인증이 포함된 샘플 요청:

![2024-06-22-APIgatewayinSpringboot_5](/assets/img/2024-06-22-APIgatewayinSpringboot_5.png)

<div class="content-ad"></div>

서비스의 모든 코드 기반은 여기에서 사용 가능합니다.

읽어 주셔서 감사합니다. 즐거운 탐험하세요!!!