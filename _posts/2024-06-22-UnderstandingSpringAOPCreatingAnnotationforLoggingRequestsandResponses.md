---
title: "Spring AOP 이해하기 요청과 응답을 로깅하는 애너테이션 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-UnderstandingSpringAOPCreatingAnnotationforLoggingRequestsandResponses_0.png"
date: 2024-06-22 22:17
ogImage:
  url: /assets/img/2024-06-22-UnderstandingSpringAOPCreatingAnnotationforLoggingRequestsandResponses_0.png
tag: Tech
originalTitle: "Understanding Spring AOP: Creating Annotation for Logging Requests and Responses"
link: "https://medium.com/dev-genius/understanding-spring-aop-creating-annotation-for-logging-requests-and-responses-d2e4221afe3d"
---

자바 개발자들의 강력한 무기 중 하나는 Aspect-Oriented Programming (AOP)입니다. Spring Boot와 원활하게 통합되면 게임 체인저가 될 수 있습니다. 이 글에서는 Spring Boot AOP를 이해하기 위해 Annotation을 구축하여 재사용할 수 있는데, 어떤 요청 매핑에도 로깅을 위해 요청, 응답 및 상태를 기록할 수 있습니다.

![이미지](/assets/img/2024-06-22-UnderstandingSpringAOPCreatingAnnotationforLoggingRequestsandResponses_0.png)

# AOP란 무엇인가요?

Aspect-Oriented Programming (AOP)은 개발자가 로깅, 오류 처리 및 보안과 같은 교차 관심 사항을 비즈니스 로직과 분리하여 모듈화할 수 있는 프로그래밍 패러다임입니다. AOP에서 이러한 관심 사항인 Aspect는 독립적으로 정의되고 특정 지점에서 코드에 엮이며 코드 중복을 줄이고 유지보수성을 향상시킵니다. 이 접근 방식은 코드 조직화를 개선하고 소프트웨어 개발에서 관심의 분리를 장려합니다.

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

# AOP 개념

구현에 들어가기 전에 알아야 할 몇 가지 개념이 있습니다.

## 측면(Aspect)

로그인 또는 보안과 같은 교차 관심사를 캡슐화하는 모듈입니다. 측면은 코드에서 어떻게 하는지와 어디에서 하는지를 정의합니다. Spring AOP에서는 @Aspect 주석이 달린 일반 클래스를 사용하여 측면을 구현합니다.

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

## 조인 포인트

프로그램 실행 중 발생하는 특정 지점을 말합니다. 메소드 호출, 객체 인스턴스화 또는 예외 처리와 같은 것을 포함합니다. Aspect는 특정 조인 포인트를 대상으로 행동을 적용하기 위해 지정합니다. Spring AOP에서 조인 포인트는 항상 메소드 실행을 나타냅니다.

## 어드바이스

특정 조인 포인트에서 Aspect가 취하는 조치를 말합니다. "before", "after" 등과 같은 다양한 유형의 어드바이스가 있습니다. Spring AOP에서 어드바이스는 조인 포인트 주변의 인터셉터 체인을 유지하며 작동합니다.

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

다양한 종류의 조언이 있습니다:

- 이전 조언 (Before Advice): 조인 포인트 이전에 실행되며, 주요 로직을 실행하기 전에 동작이나 유효성 검사를 수행할 수 있는 기회를 제공합니다.
- 반환 후 조언 (After returning Advice): 조인 포인트의 정상 실행이 완료된 후에 실행됩니다. 메서드가 성공적으로 완료될 때 수행할 동작에 유용합니다.
- 던지기 후 조언 (After throwing Advice): 조인 포인트에서 예외가 발생하면 실행됩니다. 예외 처리 또는 로깅에 사용됩니다.
- 이후 조언 (After Advice): 조인 포인트의 결과(정상 또는 예외)와 관계없이 실행됩니다. 정리 또는 마무리 작업에 사용됩니다.
- 주변 조언 (Around Advice): 모든 조언 중에서 가장 강력한 기능을 가지고 있습니다. 조인 포인트를 포함하여 조인 포인트의 실행에 대한 완전한 제어를 제공합니다. 메서드의 동작을 수정하거나 완전히 대체할 수 있습니다.

## 포인트컷

조언이 적용해야 하는 조인 포인트를 나타내는 집합입니다. 조언은 포인트컷 표현식과 연관되어 있으며, 포인트컷과 일치하는 모든 조인 포인트에서 실행됩니다. 스프링은 기본적으로 AspectJ 포인트컷 표현식 언어를 사용합니다.

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

## 소개

기존 클래스에 새로운 방법이나 필드를 도입하여, 측면(Aspect)이 클래스의 소스 코드를 수정하지 않고 기능을 추가할 수 있도록 합니다. Spring AOP를 사용하면 어떤 조언 대상 객체에 대해 새로운 인터페이스를 추가할 수 있습니다.

## 엮기(Weaving)

측면(Aspect)을 주요 응용 프로그램 코드와 통합하는 과정을 말합니다. 컴파일 시간, 로드 시간 또는 실행 시간에 발생할 수 있습니다. Spring AOP는 실행 시간에 엮기(Weaving)를 수행합니다.

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

## 대상 객체

애스펙트에 의해 조언을 받는 객체입니다. Spring AOP는 런타임 위빙만 지원하기 때문에 이 객체는 항상 프록시된 객체가 될 것입니다.

## AOP 프록시

Aspect contracts를 구현하기 위해 AOP 프레임워크에 의해 생성된 객체입니다. Spring에서 AOP 프록시는 JDK 다이내믹 프록시 또는 CGLIB 프록시가 될 것입니다.

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

# 사용자 정의 어노테이션

이 글에서는 사용자 정의 어노테이션에 대해 이해해야 합니다. 기본 사항을 알아보겠습니다.

Spring Boot에서 사용자 정의 어노테이션을 사용하면 코드에 자체 마커나 메타데이터를 정의할 수 있습니다. 주요 개념을 살펴보겠습니다.

- 어노테이션 선언: 어노테이션을 선언하려면 @interface를 사용합니다.
- 대상(Target): 어노테이션이 사용될 위치를 지정합니다. 예: @Target(ElementType.METHOD)은 메서드에만 사용할 수 있도록 제한합니다. 클래스에 사용하려면 TYPE을, 필드에 사용하려면 FIELD를 사용할 수 있습니다.
- 보존(Retention): 어노테이션 정보가 유지되는 시점을 정의합니다. 예: @Retention(RetentionPolicy.RUNTIME)은 실행 시점에 사용 가능하게 합니다.
- 속성(Attributes): 어노테이션 내에서 속성을 정의합니다. 예: String value() default “기본 값”;

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

# 구현

이제 구현을 진행해 봅시다.

## 의존성

먼저, spring AOP를 종속성으로 추가하겠습니다.

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

```js
<!-- Spring AOP -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

## 사용자 정의 어노테이션

그런 다음 사용자 정의 어노테이션을 생성할 것입니다.

```js
package com.example.demo.annotations;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * @author Pratiyush Prakash
 *
 * 이 어노테이션은 요청과 응답을 로깅하는 데 사용됩니다.
 *
 * METHOD에만 적용되며,
 * 런타임에서 사용 가능합니다.
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LogRequestResponse {
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

## 양상

이제 Aspect를 생성할 수 있습니다. 여기서는 Annotation에 대한 Pointcut을 생성하고 메서드의 요청과 응답을 로깅하기 위한 어드바이스를 생성할 것입니다.

```js
package com.example.demo.aspects;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
importorg.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Component;

/**
 * @author Pratiyush Prakash
 *
 * 이 Aspect는 LogRequestResponse 어노테이션을 적용한 모든 요청과 응답을 로깅하는 모듈입니다.
 */
@Aspect
@Component
public class RequestResponseLoggingAspect {

    // LogRequestResponse 어노테이션에 대한 Pointcut 정의
    @Pointcut("@annotation(com.example.demo.annotations.LogRequestResponse)")
    public void logAnnotationPointcut() {
    }

    // Before 어드바이스
    @Before("logAnnotationPointcut()")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Before " + joinPoint.getSignature().getName());
    }


    // 일반적인 After 어드바이스
    @AfterReturning(pointcut = "logAnnotationPointcut()", returning = "result")
    public void logAfterReturning(JoinPoint joinPoint, Object result) {
        // 응답을 로깅합니다.
        System.out.println("After " + joinPoint.getSignature().getName() +  "메서드가 반환한 값 " + result);

        // 응답 상태를 로깅합니다.
        if (result instanceof ResponseEntity) {
            ResponseEntity<Object> response = (ResponseEntity<Object>) result;
            System.out.println("응답 상태: " + response.getStatusCode());
        }
    }

}
```

## 어플리케이션

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

이제 테스트하고 싶은 엔드포인트에 주석을 추가할 수 있습니다.

```js
package com.example.demo.web;

import java.util.HashMap;
import java.util.Map;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.example.demo.annotations.LogRequestResponse;

/**
 * @Author Pratiyush Prakash
 *
 * 여기에 모든 엔드포인트가 위치합니다
 */
@RestController
@RequestMapping("/api/v1")
public class DemoController {

    /**
     * 이것은 샘플 보안된 API 호출입니다
     * @return 문자열
     */
    @LogRequestResponse
    @GetMapping(value = "/secured/hello-world")
    public String securedCall() {
        return "hello world secured!!";
    }

    /**
     * 이것은 샘플 비보안 API 호출입니다
     * @return 문자열
     */
    @LogRequestResponse
    @GetMapping(value = "/hello-world")
    public String unsecuredCall() {
        return "hello world!!";
    }

    /**
     * 이것은 객체를 반환하는 샘플 GET 호출입니다
     * @return 이름과 나이의 맵
     */
    @LogRequestResponse
    @GetMapping(value = "/demo-object")
    public Map<String, Integer> demoObjectMap() {
        Map<String, Integer> map = new HashMap<>();
        map.put("Pratiyush", 29);
        return map;
    }

    /**
     * 다른 응답 상태를 테스트하기 위한 샘플 GET 호출입니다
     * @return ResponseEntity
     */
    @LogRequestResponse
    @GetMapping(value = "/demo-response-entity")
    public ResponseEntity<String> demoResponseEntity(@RequestParam Integer code) {
        switch (code) {
            case 200:
                return new ResponseEntity<String>("Hello world!!", HttpStatus.ACCEPTED);
            case 400:
                return new ResponseEntity<>(HttpStatus.NOT_FOUND);
            default:
                return new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
        }
    }

}
```

여기까지 코드 작업이 완료되었습니다.

## 데모

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

이제 우리의 구현이 제대로 작동하는지 확인할 수 있습니다. 애플리케이션을 빌드하고 실행할 수 있습니다. Swagger UI를 열어 엔드포인트를 호출할 수도 있습니다. 그리고 로그에서 요청과 응답을 확인할 수 있는지 확인할 수 있습니다.

![이미지](/assets/img/2024-06-22-UnderstandingSpringAOPCreatingAnnotationforLoggingRequestsandResponses_1.png)

요약하자면, Aspect는 프로젝트를 모듈화하고 로깅, 보안, 오류 처리와 같은 공통 관심을 분리해야 하는 경우에 매우 유용합니다. 이 글에서는 로깅 사용 사례를 다뤘습니다. 여러분의 프로젝트에서 이와 같은 시나리오가 있는 경우 한번 시도해 보세요.

# 참고문헌

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

- [Spring Framework AOP](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/aop.html)
- [Demystifying Proxy in Spring](https://medium.com/dev-genius/demystifying-proxy-in-spring-3ab536046b11)
- You can access my code [here](https://github.com/iepratiyush/spring-demo)
