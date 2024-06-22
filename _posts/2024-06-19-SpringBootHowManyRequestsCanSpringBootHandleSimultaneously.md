---
title: "Spring Boot Spring Boot가 동시에 처리할 수 있는 요청의 양은 얼마나 될까요"
description: ""
coverImage: "/assets/img/2024-06-19-SpringBootHowManyRequestsCanSpringBootHandleSimultaneously_0.png"
date: 2024-06-19 09:57
ogImage: 
  url: /assets/img/2024-06-19-SpringBootHowManyRequestsCanSpringBootHandleSimultaneously_0.png
tag: Tech
originalTitle: "Spring Boot: How Many Requests Can Spring Boot Handle Simultaneously?"
link: "https://medium.com/@haiou-a/spring-boot-how-many-requests-can-spring-boot-handle-simultaneously-a57b41bdba6a"
---


![이미지](/assets/img/2024-06-19-SpringBootHowManyRequestsCanSpringBootHandleSimultaneously_0.png)

Spring Boot은 Java 개발에서 중요한 프레임워크로, 개발자에게 효율적이고 사용자 친화적인 도구를 제공합니다. 따라서 관련 인터뷰 질문들도 중요합니다.

오늘은 전통적인 인터뷰 질문을 살펴보겠습니다: Spring Boot가 동시에 처리할 수 있는 요청은 얼마인가요?

정확히 말하면, Spring Boot가 동시에 처리할 수 있는 요청의 수는 Spring Boot 프레임워크 자체에 의해 결정되는 것이 아닙니다. 그 대신 중첩된 웹 컨테이너에 달려 있습니다 (웹 컨테이너의 동작이 Spring Boot의 동작을 결정하기 때문에 이 두 질문의 답을 같은 것으로 간주할 수 있습니다).

<div class="content-ad"></div>

# 1. 세 가지 주요 웹 컨테이너

현재 시장에는 세 가지 주요 웹 컨테이너가 있습니다: Tomcat, Undertow 및 Jetty.

이 중에서 Tomcat은 Spring Boot 프레임워크의 기본 웹 컨테이너입니다.

그들의 차이점은 다음과 같습니다:

<div class="content-ad"></div>

## 1.1 Tomcat

톰캣은 아파치 소프트웨어 재단의 오픈소스 프로젝트이며 가장 널리 사용되는 서블릿 컨테이너 중 하나입니다. Java 서블릿 및 JavaServer Pages (JSP) 사양을 완전히 구현합니다.

톰캣은 서블릿 컨테이너뿐만 아니라 경량 응용 프로그램 서버이기도 하지만 다른 경량 서버에 비해 약간 더 무겁다고 여겨집니다.

톰캣은 SSL, 연결 풀링 등 다양한 기업 수준의 기능을 지원하여 대규모 복잡한 기업 응용 프로그램을 실행하기에 적합합니다.

<div class="content-ad"></div>

그 안정성과 성숙성은 기업 수준의 응용 프로그램을 통해 증명되었습니다. 이를 통해 많은 기업들이 선호하는 웹 컨테이너가 되었습니다.

## 1.2 Undertow

Undertow은 레드햇에서 개발된 유연하고 고성능의 웹 서버 및 리버스 프록시 서버입니다.

WildFly 응용 프로그램 서버의 기본 웹 컨테이너입니다. Undertow는 낮은 메모리 사용량과 높은 동시성을 위해 설계되었으며, RESTful API 서비스와 같은 대량의 짧은 연결을 처리하는 데 뛰어납니다.

<div class="content-ad"></div>

언더토우는 Servlet 3.1, WebSocket 및 non-blocking IO (NIO)를 지원하며 HTTP/2 프로토콜을 지원하는 현대적인 서버 중 하나입니다.

그 설계 철학은 모듈화되고 임베디드할 수 있는 솔루션을 제공하여 기존 시스템에 쉽게 통합할 수 있고 마이크로서비스 아키텍처에 적합합니다.

## 1.3 젯티

젯티는 Eclipse Foundation이 유지보수하는 오픈 소스 경량 웹 서버 및 Servlet 컨테이너입니다.

<div class="content-ad"></div>

다음은 표의 Markdown 형식입니다.

지티는 임베딩 기능과 높은 설정 가능성으로 유명하며, 개발 단계, 테스트 환경 또는 가벼운 애플리케이션과 같이 빠른 시작과 경량 배포가 필요한 시나리오에서 자주 사용됩니다.

지티는 서블릿 사양, 웹소켓을 지원하며 NIO를 기반으로 하여 큰 동시 접속을 처리하는 데 우수한 성능을 발휘합니다.

지티는 설계에서 유연성과 확장성을 강조하며, 특정 요구 사항을 충족하기 위해 API를 통해 쉽게 사용자 정의할 수 있어 클라우드 환경, 지속적 통합 및 데브옵스에서 인기를 얻고 있습니다.

![이미지](/assets/img/2024-06-19-SpringBootHowManyRequestsCanSpringBootHandleSimultaneously_1.png)

<div class="content-ad"></div>

# 2. 최대 연결 수 및 최대 대기 수

Spring Boot 프레임워크의 기본 웹 컨테이너 인 Tomcat을 예로 들어, 동시에 처리할 수 있는 요청 수는 spring-configuration-metadata.json 파일에 구성되어 있습니다. 이 파일에서 'server.tomcat.max-connections' (Tomcat의 최대 연결 수)를 검색하면 아래와 같은 결과가 나옵니다:

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-19-SpringBootHowManyRequestsCanSpringBootHandleSimultaneously_3.png)

이것은 기본적으로 Tomcat이 최대 8192개의 연결을 허용한다는 것을 의미합니다 (8192 = 8 * 1024).

여기서 한 가지 생각할 수 있습니다. '기본적으로 Spring Boot는 8192개의 요청을 동시에 처리할 수 있다.' 라고 생각할 수 있지만, 이는 잘못된 생각입니다. 왜 일까요?

Tomcat은 최대 8192개의 연결을 허용할 수 있지만, Tomcat은 또한 최대 대기 숫자를 가지고 있습니다. 이것은 8192에 도달하면 요청의 연결을 저장할 수 있는 대기 큐가 있는 것을 의미합니다.


<div class="content-ad"></div>

그래요, Spring Boot가 동시에 처리할 수 있는 연결 수는 Tomcat의 최대 연결 수와 Tomcat의 최대 대기 수와 같습니다.

그렇다면 최대 대기 수는 얼마인가요?

그럼, 'server.tomcat.accept-count' (Tomcat의 최대 대기 수)를 spring-configuration-metadata.json 파일에서 계속 찾아보겠습니다. 검색 결과는 아래에 표시됩니다:

![이미지](/assets/img/2024-06-19-SpringBootHowManyRequestsCanSpringBootHandleSimultaneously_4.png)

<div class="content-ad"></div>

그 말은 기본적으로 Tomcat의 최대 대기 수가 100임을 의미합니다.

## 3. 동시 요청 처리

따라서 우리는 다음을 결론 지었습니다: 기본적으로 Spring Boot가 동시에 처리할 수 있는 요청 수 = 최대 연결 수(8192) + 최대 대기 수(100), 즉 8292입니다.

물론, 이 두 값은 아래 구성에서 보여지는 Spring Boot 구성 파일에서 수정할 수 있습니다.

<div class="content-ad"></div>

```yaml
server:
  tomcat:
    max-connections: 2000 # 최대 연결 수
    accept-count: 200 # 최대 대기 번호
```

# 4. 추가 지식: 웹 컨테이너 설정하기

Spring Boot 프레임워크에서 웹 컨테이너를 Jetty 또는 Undertow로 설정하는 방법은 무엇인가요? 함께 살펴보겠습니다.

# 4.1 컨테이너를 Jetty로 설정하기

<div class="content-ad"></div>

Spring Boot 프레임워크의 웹 컨테이너를 Jetty로 설정하려면, 다음과 같이 pom.xml 파일을 수정하면 됩니다:

```js
<dependencies>
    <!-- Spring Boot Starter Web를 사용하지만 Tomcat은 제외합니다 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <!-- Tomcat 제외 설정 -->
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <!-- Jetty starter 종속성 추가 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
</dependencies>
```

다시 말해, 기본 Tomcat을 제외하고 Jetty 종속성을 추가하면 됩니다.

# 4.2 Undertow 컨테이너로 설정하기

<div class="content-ad"></div>

Spring Boot 프레임워크의 웹 컨테이너를 Undertow로 설정하려면 Jetty 구현 방법과 비슷합니다. 아래에 표시된대로 pom.xml 파일을 수정하면 됩니다:

```js
<dependencies>
    <!-- Spring Boot Starter Web을 사용하되 Tomcat은 제외 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <!-- Undertow 스타터 의존성 추가 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-undertow</artifactId>
    </dependency>
</dependencies>
```