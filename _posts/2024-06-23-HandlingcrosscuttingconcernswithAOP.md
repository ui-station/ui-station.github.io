---
title: "AOP를 활용한 공통 관심사 처리 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HandlingcrosscuttingconcernswithAOP_0.png"
date: 2024-06-23 20:42
ogImage: 
  url: /assets/img/2024-06-23-HandlingcrosscuttingconcernswithAOP_0.png
tag: Tech
originalTitle: "Handling cross cutting concerns with AOP"
link: "https://medium.com/@ankita.rai.139/handling-cross-cutting-concerns-with-aop-ba5c0785139d"
---


# 목차

- 소개
- AOP의 다양한 구성 요소
     - ` Aspect
     - ` Join Point
     - ` Pointcut
     - ` Advice
- 로깅 프레임워크를 사용하여 AOP 이해하기
- AOP와 필터의 차이점은 무엇인가요?
- 결론

# 소개

관점 지향 프로그래밍(Aspect-Oriented Programming, AOP)은 모듈성을 높이기 위해 교차하는 관심사를 측면(Aspect)으로 분리할 수 있도록 하는 프로그래밍 표준입니다. 전통적인 프로그래밍 패러다임에서는 로깅, 보안 및 트랜잭션 관리와 같은 공통 관심사가 다양한 모듈에 걸쳐 분산되어 코드베이스를 유지 및 이해하기 어렵게 만듭니다.

<div class="content-ad"></div>

전통적인 로깅 방식:

```js
private User getUserById(Integer userId){
    Long startTime = System.currentTimeMillis();
    logger.info("Starting method getUserById at {}", startTime);
    User user = myDao.getUserById(userId);
    Long endTime = System.currentTimeMillis();
    logger.info("Ending method getUserById with response {} at time {}", user, endTime);
    return user;
}
```

그러나 관점 지향 프로그래밍(Aspect-Oriented Programming, AOP)을 활용하면 교차 우선 관심사항(Cross-cutting concerns)을 분리된 단위인 어스펙트(Aspects)로 캡슐화할 수 있습니다. 이를 통해 코드를 더욱 깔끔하고 모듈화할 수 있으며, 각 어스펙트가 특정한 교차 우선 관심사항을 다룹니다.

AOP를 사용한 로깅:

<div class="content-ad"></div>

```java
@Logging(printResponse = true, logLevel = LOG.INFO)
private User getUserById(Integer userId){
  User user = myDao.getUserById(userId);
  return user;
}
```

# AOP의 다양한 구성 요소

AOP의 다양한 구성 요소를 연극 공연의 예를 들어 설명해 보겠습니다.

Join Point: 연극 무대에 있는 자신을 상상해보세요. 배우들이 하는 장면 변화나 특정 동작은 모두 join point로 작용합니다. 예를 들어, 캐릭터가 무대에 나오거나 내려갈 때, 특정 대사를 말할 때가 있습니다.


<div class="content-ad"></div>

포인트컷: 지금 이 시점에서 연극 대본이 포인트컷 역할을 합니다. 이는 특정 대사나 활동의 타이밍을 설정합니다. 예를 들어, 특정 캐릭터가 자정에 무대에 나와야 한다는 것을 시사할 수 있습니다.

조언: 배우들의 연기가 조언을 대변합니다. 그들은 지정된 대본 위치(포인트컷)에서 지시된 대사를 전달하거나 행동을 수행합니다. 따라서 특정 캐릭터가 자정에 무대에 나와야 한다면, 해당 캐릭터를 연기하는 배우는 대본의 지시(포인트컷)에 따라 정해진 대로 행동하게 됩니다.

프로그램은 예시로 든 이 극장 공연을 나타냅니다. 각 장면이나 행동은 조인 포인트가 되며, 대본은 언제 행동이 일어나야 하는지를 나타내는 포인트컷이며, 배우의 연기는 그 시간에 수행되는 조언입니다.

이제 각 구성 요소에 대해 자세히 알아보겠습니다:

<div class="content-ad"></div>

## 1.) Aspect

Aspect(관점)은 횡단 관심사(cross-cutting concerns)를 캡슐화합니다. 지정된 결합 지점(join points)에서 기본 코드에 적용해야 하는 동작을 정의하는 코드를 포함합니다. Aspect에는 조언(advice)과 포인트컷(pointcuts)이 포함됩니다.

## 2.) Join Point

Join Point(결합 지점)는 프로그램 실행 중 특정 지점인데, 메서드 실행, 메서드 호출, 객체 인스턴스화 및 필드 접근 같은 지점에서 Aspect를 적용할 수 있습니다. Aspect는 결합 지점을 정의하여 영향을 미치는 결합 지점을 명시합니다.

<div class="content-ad"></div>

## 3.) 포인트컷

포인트컷은 측면이 적용되어야 하는 코드의 기준을 지정합니다. 메서드 시그니처, 클래스 이름, 주석 또는 기타 기준을 기반으로 할 수 있습니다.

아래는 응용 프로그램에서 조인 포인트를 선택하는 데 사용할 수 있는 다양한 유형의 포인트컷입니다.

i) 실행 포인트컷: 이는 응용 프로그램에서 메서드를 실행하는 기준에 따라 조인 포인트를 선택합니다.

<div class="content-ad"></div>

```js
@Before("execution(* com.user.service.*.*(..))")
public Object performAuth(JoinPoint joinPoint) throws Throwable {
    // Advice implementation
}
```

“execution(* com.user.service.*.*(..))”: 이 부분은 포인트컷 표현식입니다. 왼쪽부터 오른쪽으로 살펴보겠습니다.

execution - 이 부분은 메서드 실행을 기반으로 포인트컷을 정의하고 있다는 키워드입니다.

* - 이 와일드카드는 메서드의 반환 타입을 나타냅니다. 여기서 *는 어떤 반환 타입이던지를 의미합니다.

<div class="content-ad"></div>

com.user.service.* - 이 부분은 대상 클래스가 위치한 패키지를 지정합니다 (com.user.service). 마지막 점 뒤의 *는 이 패키지 내의 모든 클래스 이름을 의미합니다.

.* - 이 부분은 메서드 이름을 지정합니다. 여기서 .*는 모든 메서드 이름을 의미합니다.

(..) - 이 부분은 메서드 매개변수를 지정합니다. (..)은 모든 타입의 매개변수를 가진 임의의 개수의 매개변수를 의미합니다.

ii) 포인트컷 내에서: 이 포인트컷 표현식은 표현식에 정의된 패키지 이름이나 클래스를 기반으로 조인 포인트를 선택합니다. 해당 패키지/클래스 내의 모든 메서드 실행을 선택합니다.

<div class="content-ad"></div>

```java
@Around("within(com.cache.service.*)")
public Object warmupAndDestroyCache(ProceedingJoinPoint joinPoint) throws Throwable {
    // Advice implementation
}
```

"within(com.cache.service.*)" : 이 표현은 com.cache.service 패키지 내에서 모든 메서드를 선택합니다.

iii) 어노테이션 포인트컷: 이 포인트컷은 지정된 어노테이션으로 주석이 달린 모든 메서드와 일치하는 조인 포인트를 선택합니다.

```java
@After("@annotation(com.user.annotations.AuditPurchase)")
public Object logPurchaseHistory(JoinPoint joinPoint) throws Throwable {
    // Advice implementation
}
```

<div class="content-ad"></div>

“@annotation(com.user.annotations.AuditPurchase)” : 이 표현은 AuditPurchase 어노테이션이 지정된 모든 메서드를 선택합니다.

iv) @within Pointcut : 이 포인트컷은 대상 클래스에 특정 어노테이션이 있는 조인 포인트를 선택합니다. 지정된 어노테이션이 지정된 클래스 내에서 메서드 실행될 때 이 포인트컷에 의해 일치됩니다.

```js
@AfterThrowing(pointcut = "@within(com.user.annotations.LogError)", throwing = "ex")
public void logError(JoinPoint joinPoint, Throwable ex) {
   // Advice implementation
}
```

위에서 정의된 것 외에도 사용 사례에 따라 적용할 수 있는 여러 종류의 포인트컷 표현식이 있습니다.

<div class="content-ad"></div>

## 4.) 조언

조언은 특정 조인 포인트에서 취해지는 작업입니다. 일부 조건이 충족될 때 응용 프로그램에 추가하고 싶은 동작입니다. 조언은 일반적으로 포인트컷 표현과 관련이 있으며, 이 표현은 코드베이스 내에서 조언이 적용되어야 하는 위치를 정의합니다. 조언은 해당 포인트컷 표현과 일치하는 조인 포인트에서 실행될 수 있습니다.

다양한 유형의 조언:

- Before advice: 이 유형의 조언은 조언된 메서드가 호출되기 전에 실행됩니다. 입력 유효성 검사, 메서드 실행 시작 로깅 등과 같은 작업을 실행하는 데 일반적으로 사용됩니다.

<div class="content-ad"></div>

```java
@Before("@within(com.user.annotations.ValidateUserId)")
public void validateUserId(JoinPoint joinPoint) {
    Integer userId = ((MethodSignature) joinPoint.getSignature()).getMethod().getAnnotation(ValidateUserId.class).userId();
    // validation 작업에 대한 나머지 로직
}
```

- After advice: 이 유형의 어드바이스는 조언된 메서드가 성공 또는 실패에 관계없이 완료된 후에 실행됩니다. 주로 리소스 정리 작업에 사용됩니다.

```java
@After("execution(* com.user.service.*.*(..))")
public void clearCache(JoinPoint joinPoint) {
    // Advice 구현
}
```

- After returning advice: 이 유형의 어드바이스는 어드바이스된 메서드가 예외를 던지지 않고 성공적으로 완료된 후에 실행됩니다. 메서드 실행 결과를 기록하거나 반환된 값에 기반한 후속 처리 작업을 수행하는 데 유용합니다.

<div class="content-ad"></div>

```java
@AfterReturning(pointcut = "execution(* com.user.service.utils.regex.*.*(..))", returning = "result")
public void removeHtmlTagsFromResult(JoinPoint joinPoint, Object result) {
    // Advice implementation
}
```

- After throwing advice: 이 유형의 어드바이스는 조언된 메서드가 예외를 던질 때 실행됩니다. 에러 로깅, 예외 처리 등에 유용합니다.

```java
@AfterThrowing(pointcut = "execution(* com.user.service.*.*(..))", throwing = "exception")
public void logError(JoinPoint joinPoint, Exception exception) {
    // Advice implementation
}
```

- Around advice: 이 유형의 어드바이스는 조언된 메소드의 호출을 완전히 제어합니다. 입력 및 출력을 포함하여 특정한 로직을 수행하기 위해 메소드 호출을 가로챕니다. 원래 메소드 호출 전후에 사용자 정의 로직을 수행하고, 원래 메소드 호출을 계속할지 여부를 제어할 수 있습니다.


<div class="content-ad"></div>

# 로깅 프레임워크를 활용한 AOP 이해

여기서는 메서드에서 소요된 시간을 기록하는 어노테이션 기반 로깅 프레임워크를 생성할 것입니다. 해당 메서드의 입력 및 출력 매개변수를 출력하면서 소요된 시간을 기록합니다.

단계 1: 어스펙트 생성

먼저 로깅 어스펙트 클래스를 생성하여 로깅을 수행하는 어드바이스를 포함시킵니다.

<div class="content-ad"></div>

```java
@Aspect
@Component
public class LoggingAspect {
  // 어드바이스와 포인트컷 표현을 포함한 메소드
}
```

단계 2: 어드바이스와 포인트컷 정의

이 단계에서는 "@annotation(com.user.service.annotations.LogTime)" 포인트컷 표현을 가진 @Around 어드바이스를 가지는 doLogging 메소드를 생성할 것입니다.

```java
@Aspect
@Component
public class LoggingAspect {

  @Around(value = "@annotation(com.user.service.annotations.LogTime)")
  public Object doLogging(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
 
    MethodSignature signature = (MethodSignature) proceedingJoinPoint.getSignature();
    Method method = signature.getMethod();
    LogTime loggableMethod = method.getAnnotation(LogTime.class);

    // 이전 처리
    LogWriter.write(proceedingJoinPoint.getTarget().getClass(), method.getName() + "() 실행 시작 ");

    if (proceedingJoinPoint.getArgs() != null && proceedingJoinPoint.getArgs().length > 0) {
      StringBuilder sb = new StringBuilder();
      for (int i = 0; i < proceedingJoinPoint.getArgs().length; i++) {
        sb.append(method.getParameterTypes()[i].getName()).append(":").append(proceedingJoinPoint.getArgs()[i]);
        if (i < proceedingJoinPoint.getArgs().length - 1)
          sb.append(", ");
      }

      LogWriter.write(proceedingJoinPoint.getTarget().getClass(), method.getName() + "() args " + sb);
    }

    long startTime = System.currentTimeMillis();
    
    // 주요 메소드 실행
    Object result = proceedingJoinPoint.proceed();

    long endTime = System.currentTimeMillis();

    // 결과 표시
    if (result != null) {
      LogWriter.write(proceedingJoinPoint.getTarget().getClass(), method.getName() + "() 결과 : " + result);
    }

    // 이후 표시
    LogWriter.write(proceedingJoinPoint.getTarget().getClass(), method.getName() + "() 실행 완료 및 실행 시간 " + (endTime - startTime) + " 밀리초 소요");

    return result;
  }
}
```

<div class="content-ad"></div>

단계 3: 어노테이션 생성

이번 단계에서는 우리가 어느 메서드에든 로깅 어드바이스를 적용할 수 있는 사용자 지정 LogTime 어노테이션을 만들고 있습니다.

```java
@Component
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface LogTime {

}
```

단계 4: 어드바이스 대상 메서드 어노테이션하기

<div class="content-ad"></div>

마지막으로, 실행 시간을 추적할 메서드에 사용자 정의 LogTime 주석을 적용해야 합니다.

```js
@LogTime
public User getUserById(Long userId) {
  Query query = Query.query(Criteria.where("userId").is(userId)
  return mongoTemplate.findOne(query, User.class);
}
```

결과 예시 로그:

<img src="/assets/img/2024-06-23-HandlingcrosscuttingconcernswithAOP_0.png" />

<div class="content-ad"></div>

# AOP은 필터와 어떻게 다른가요?

![이미지](/assets/img/2024-06-23-HandlingcrosscuttingconcernswithAOP_1.png)

# 결론

마지막으로, 관점지향 프로그래밍(AOP)은 소프트웨어 개발에서 교차 관심사를 모듈화하는 강력한 패러다임을 제공합니다. AOP는 로깅, 보안, 트랜잭션 관리와 같은 관심사를 핵심 비즈니스 로직과 분리함으로써 코드의 모듈성, 재사용성, 유지보수성을 향상시킵니다. 관점, 포인트컷, 어드바이스를 사용함으로써 AOP는 개발자들이 이러한 교차 관심사를 주 응용 로직과 별도로 캡슐화할 수 있게 하며, 보다 깨끗하고 유지보수가 쉬운 코드베이스를 유도합니다.