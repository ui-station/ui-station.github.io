---
title: "2024년을 위한 자바 개발자를 위한 상위 60개의 스프링 프레임워크 인터뷰 질문책에 있는 모든 질문 포함"
description: ""
coverImage: "/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_0.png"
date: 2024-05-27 15:53
ogImage:
  url: /assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_0.png
tag: Tech
originalTitle: "Top 60 Spring-Framework Interview Questions for Java Developers 2024(Contain All the Questions from the Book)"
link: "https://medium.com/@rathod-ajay/top-60-spring-framework-interview-questions-for-java-developers-2024-contain-all-the-questions-from-f15621f77d2a"
---

## 안녕하세요! Java 개발자라면, 백엔드 개발에 필수적인 스프링 프레임워크를 정복하는 것이 중요합니다. 스프링 부트 프로젝트의 기초 역할을 하는데, 스프링 프레임워크는 깊게 파고들 필수 기술입니다. 이 기사에는 스프링 프레임워크 인터뷰 질문이 모두 포함되어 있습니다. 이를 통해 인터뷰에 성공할 수 있을 것입니다. 함께 더 알아보세요!

![이미지](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_0.png)

스프링 부트로의 진입 전에 스프링 프레임워크를 배우는 것은 집을 짓기 전에 견고한 기초를 쌓는 것과 같습니다. 스프링 프레임워크는 자바 개발에서 중요한 개념과 작동방식을 가르쳐주며 의존성 처리와 코드 구성 등을 다룹니다.

이 지식을 통해 더 효율적이고 빠른 스프링의 버전인 스프링 부트가 왜 이런 방식으로 작업을 하는지 이해할 수 있습니다.

<div class="content-ad"></div>

테이블 태그를 Markdown 형식으로 변경해 보겠습니다.


It’s like learning the basics before using a time-saving tool that’s built on those basics. So, by mastering Spring first, you’ll have a better grip on Spring Boot and make smarter choices when creating modern applications.

Let’s dive into the Spring framework interview questions,

# What is Spring Framework?

The Spring Framework provides a comprehensive programming and configuration model for modern Java-based enterprise applications — on any kind of deployment platform.


<div class="content-ad"></div>

Spring의 중요 요소 중 하나는 애플리케이션 수준에서의 인프라 지원입니다. Spring은 기업 애플리케이션의 "배관 시스템"에 집중하여 팀이 애플리케이션 수준의 비즈니스 로직에 집중할 수 있도록 하며, 특정 배포 환경에 불필요한 결합을 만들지 않습니다.

# 제어 반전이란 무엇인가요?

전통적인 프로그래밍에서는 애플리케이션이 필요로 하는 모든 객체를 수동으로 생성하고 관리해야 합니다. 이런 방식은 복잡하고 오류가 발생하기 쉬울 수 있습니다.

다음은 IOC가 어떻게 보이는지 보여주는 다이어그램입니다,

<div class="content-ad"></div>

<img src="/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_1.png" />

스프링 프레임워크의 IoC 컨테이너는 객체를 생성하고 관리하는 책임을 가져오면서 이 프로세스를 간소화합니다. 단순히 Spring에 필요한 객체를 알려주면 Spring이 해당 객체를 만들어주고 코드에 제공해줍니다. 이를 의존성 주입이라고 합니다.

의존성 주입은 코드를 더 모듈식으로 만들어 유지보수를 더 쉽게 합니다. 또한 객체를 올바르게 생성하는 걱정을 덜어주어 오류의 위험을 줄입니다.

IoC는 의존성 주입(DI)로도 알려져 있습니다. 객체가 생성자 인수, 팩토리 메소드에 대한 인수 또는 생성 또는 팩토리 메소드에서 반환된 후 객체 인스턴스에 설정된 속성을 통해 단지 그 종속성(즉, 작동하는 다른 객체)을 정의하는 프로세스입니다. 컨테이너는 빈을 생성할 때 이러한 종속성을 주입합니다. 이 프로세스는 기본적으로 빈 자체가 직접 클래스를 사용하여 종속성을 제어하거나 위치를 찾는 것의 역(따라서 제어의 역전)입니다.

<div class="content-ad"></div>

# 스프링 IOC 컨테이너란 무엇인가요?

org. spring framework.context.ApplicationContext 인터페이스는 스프링 IoC 컨테이너를 나타내며 빈을 인스턴스화, 구성 및 조립하는 역할을 합니다.

![image](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_2.png)

컨테이너는 구성 메타데이터를 읽어서 인스턴스화, 구성 및 조립할 객체에 대한 지침을 얻습니다. 구성 메타데이터는 XML, Java 주석 또는 Java 코드로 표현됩니다. 이를 통해 응용 프로그램을 구성하는 객체와 해당 객체 간의 풍부한 상호 종속성을 표현할 수 있습니다.

<div class="content-ad"></div>

# 스프링에서 구성을 정의하는 방법은 몇 가지가 있을까요?

두 가지 방법이 있습니다:

1. XML 기반 구성 : 빈 정의를 여러 XML 파일에 걸쳐 설정하는 것이 유용할 수 있습니다. 각 개별 XML 구성 파일은 종종 아키텍처에서 논리적인 레이어나 모듈을 나타냅니다.

2. Java 기반 구성: XML 파일 대신 Java를 사용하여 애플리케이션 클래스 외부에 빈을 정의할 수 있습니다. 이러한 기능을 사용하려면 @Configuration, @Bean, @Import, @DependsOn 어노테이션을 참조하십시오.

<div class="content-ad"></div>

# 의존성 주입이란?

의존성 주입(Dependency injection, DI)은 객체가 생성될 때 생성자 인수, 팩토리 메서드의 인수 또는 생성 또는 팩토리 메서드에서 반환된 후 객체 인스턴스에 설정된 속성을 통해 다른 객체인 의존성을 정의하는 프로세스입니다. 컨테이너는 그러한 의존성을 빈을 생성할 때 주입합니다. 이 프로세스는 빈이 자체적으로 직접 클래스의 생성 또는 의존성의 위치를 제어하지 않고, 클래스의 직접 생성 또는 Service Locator 패턴을 사용하여 의존성을 제어하는 것의 역전(즉, 제어의 역전)입니다.

![이미지](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_3.png)

DI 원칙에 따라 코드가 더 깔끔해지고, 객체가 의존성이 주어질 때 결합이 더 효과적해집니다. 객체는 자체 의존성을 찾지 않으며 의존성의 위치나 클래스를 알지 못합니다. 결과적으로 클래스는 인터페이스나 추상 기본 클래스에 대한 의존성이 있을 때 특히 의존성이 단위 테스트에서 사용될 수 있도록 스텁 또는 모의 구현을 허용하는 경우 테스트하기 쉬워집니다.

<div class="content-ad"></div>

DI는 주로 생성자 기반 의존성 주입과 Setter 기반 의존성 주입 두 가지 주요 변형이 있습니다. 일반적으로 다이어그램은 다음과 같이 보입니다.

## 제어의 역전과 의존성 주입의 차이는 무엇인가요?

![다이어그램](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_4.png)

## 의존성 주입의 유형과 그것을 사용함으로써 얻는 이점은 무엇인가요?

<div class="content-ad"></div>

의존성 주입(DI)은 객체가 자신의 종속성을 생성하는 대신 종속성을 제공받을 수 있는 디자인 패턴입니다. 여러 종류의 의존성 주입이 있으며, 각각의 장단점이 있습니다:

생성자 주입: 이 종류의 주입에서는 종속성이 클래스의 생성자로 전달됩니다. 이렇게 하면 클래스가 항상 필요한 종속성을 보유하게 되며 클래스 불변성을 강제하는 데 유용할 수 있습니다.

다음 예시는 생성자 주입으로만 의존성을 주입할 수 있는 클래스를 보여줍니다:

```js
public class SimpleMovieLister {
// SimpleMovieLister는 MovieFinder에 종속성이 있습니다.
private final MovieFinder movieFinder;
// Spring 컨테이너가 MovieFinder를 주입할 수 있도록하는 생성자
public SimpleMovieLister(MovieFinder movieFinder) {
this.movieFinder = movieFinder;
}
// 주입된 MovieFinder를 실제로 사용하는 비즈니스 로직은 생략되었습니다...
}
```

<div class="content-ad"></div>

Setter 주입: 이 유형의 주입에서는 종속성이 클래스가 인스턴스화된 후에 setter 메서드로 전달됩니다. 이를 통해 종속성을 실행 중에 변경할 수 있어서 클래스를 다른 문맥에서 재사용할 수 있습니다.

```java
public class SimpleMovieLister {
    // SimpleMovieLister는 MovieFinder에 종속성이 있습니다
    private MovieFinder movieFinder;

    // Spring 컨테이너가 MovieFinder를 주입할 수 있도록 setter 메서드
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // 주입 된 MovieFinder를 실제로 사용하는 비즈니스 로직은 생략되었습니다
}
```

# 생성자 기반 또는 setter 기반 DI 중 어떤 것이 더 나은가요?

생성자 기반 및 setter 기반 DI를 혼합하여 사용할 수 있기 때문에 필수적인 종속성에는 생성자를 사용하고 선택적 종속성에는 setter 메서드나 구성 메서드를 사용하는 것이 좋은 지침입니다. @Autowired 주석을 setter 메서드에 사용하여 속성을 필수 종속성으로 만들 수 있지만, 프로그래밍 방식으로 인수의 유효성을 검사하는 생성자 주입이 선호됩니다.

<div class="content-ad"></div>

스프링 팀은 일반적으로 생성자 주입을 옹호합니다. 왜냐하면 이렇게 하면 애플리케이션 구성 요소를 변경 불가능한 객체로 구현할 수 있고 필수 종속성이 null이 되지 않도록 할 수 있기 때문입니다. 더불어 생성자 주입된 구성 요소는 항상 클라이언트(호출) 코드에 완전히 초기화된 상태로 반환됩니다.

# 메서드 주입이란 무엇인가요?

메서드 주입은 스프링 및 다른 프레임워크에서 일반적으로 사용되는 설계 패턴으로, 객체에 종속성을 주입하는 데 사용됩니다. 필드 주입이나 생성자 주입과 달리 메서드 주입은 특정 메서드 인수에 종속성을 주입합니다.

# 컨테이너 내부에서 제어의 역전이 어떻게 작동하나요?

<div class="content-ad"></div>

제어의 역전(IoC)은 응용 프로그램 코드에서 외부 컨테이너로 제어가 전달되는 디자인 패턴입니다. Java 응용 프로그램의 경우, 해당 컨테이너는 보통 IoC 컨테이너 또는 의존성 주입(DI) 컨테이너로 불립니다.

IoC 컨테이너는 객체의 생성과 관리를 담당하며, 이를 위해 객체가 어떻게 생성되고 서로 연결되는지를 정의하는 일련의 구성 규칙에 의존합니다.

다음은 IoC가 IoC 컨테이너 내에서 동작하는 방법입니다:

구성: IoC 컨테이너를 사용하려면, 객체가 어떻게 생성되고 연결되어야 하는지를 정의하는 일련의 규칙으로 구성해야 합니다. 이 구성은 일반적으로 XML 또는 Java 어노테이션을 사용하여 수행됩니다.

<div class="content-ad"></div>

객체 생성: 애플리케이션이 컨테이너로부터 객체를 요청하면, 컨테이너는 구성 규칙을 사용하여 요청된 객체의 새 인스턴스를 만듭니다.

의존성 주입: 컨테이너는 새로 생성된 객체에 필요한 모든 의존성을 주입합니다. 이러한 의존성은 일반적으로 구성 규칙에서 정의됩니다.

객체 수명주기 관리: 컨테이너는 생성한 객체의 수명주기를 관리합니다. 이는 애플리케이션이 필요에 따라 객체를 생성, 초기화 및 소멸하는 책임을 가지는 것을 의미합니다.

제어의 역전: 컨테이너를 통해 객체를 생성하고 관리하도록 의존하는 것으로, 애플리케이션 코드는 더 이상 객체 생성 프로세스를 직접 제어하지 않습니다. 대신, 컨테이너가 이 책임을 맡고, 애플리케이션 코드는 단순히 컨테이너로부터 필요한 객체를 요청합니다.

<div class="content-ad"></div>

# 다양한 스프링 모듈은 무엇인가요?

스프링 프레임워크는 여러 모듈로 구성된 포괄적인 자바 프레임워크로, 각각 특정한 기능과 기능을 제공합니다. 이러한 모듈은 주요 목적에 따라 카테고리로 구성되어 있습니다. 다음은 주요 스프링 모듈 및 각각의 역할에 대한 개요입니다:

![Spring Modules](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_5.png)

핵심 컨테이너(Core Container): 핵심 컨테이너 모듈은 스프링 프레임워크의 기초를 형성하며, 의존성 주입, 빈 라이프사이클 관리 및 리소스 관리와 같은 필수 서비스를 제공합니다. 애플리케이션 전체에서 객체를 생성, 구성 및 관리하는 역할을 수행합니다.

<div class="content-ad"></div>

데이터 접근/통합: 데이터 접근/통합 모듈은 관계형 데이터베이스, 객체-관계 매핑 (ORM) 프레임워크, 메시징 시스템 등 다양한 데이터 원본과의 데이터 접근 및 통합을 간소화하는 데 중점을 둡니다. JDBC 추상화, ORM 통합, 메시지 지향 미들웨어 (MOM) 지원과 같은 기능을 제공합니다.

웹 (MVC/Remoting): 웹 모듈은 Model-View-Controller (MVC) 패턴을 사용하여 웹 애플리케이션을 구축하고 분산 애플리케이션을 위한 리모팅 기능을 제공합니다. Servlet, Portlet, Struts와 같은 다양한 웹 기술을 지원합니다.

AOP (Aspect-Oriented Programming): AOP 모듈은 로깅, 보안, 트랜잭션 관리와 같은 관점이 교차되는 관심사를 모듈화하는 기술인 관점 지향 프로그래밍을 가능하게 합니다. 관점 선언, 관점(Aspect) 조합, 관점 실행과 같은 기능을 제공합니다.

계측 (Instrumentation): 계측 모듈은 Spring 애플리케이션을 모니터링하고 성능을 관리하는 데 도움을 줍니다. 빈 생명주기 추적, 메모리 프로파일링, 성능 메트릭 수집과 같은 기능을 제공합니다.

<div class="content-ad"></div>

테스트: 테스트 모듈은 Spring 애플리케이션의 테스트를 위한 도구와 프레임워크를 제공하며, 단위 테스트, 통합 테스트 및 웹 애플리케이션 테스트에서 의존성 주입을 지원합니다.

# 스프링 MVC, 스프링 AOP 및 스프링 Core 모듈이 무엇인가요?

이 질문을 하는 이유는 이 세 가지 모듈이 스프링 기반 애플리케이션을 개발하는 데 매우 중요하기 때문입니다.

스프링 MVC, 스프링 AOP 및 스프링 Core는 강력하고 확장 가능한 자바 애플리케이션을 구축하는 데 중요한 역할을 하는 스프링 프레임워크의 세 가지 불가결한 모듈입니다.

<div class="content-ad"></div>

스프링 MVC (Model-View-Controller)

스프링 MVC는 Model-View-Controller (MVC) 아키텍처를 구현하는 웹 프레임워크로, 애플리케이션 논리, 사용자 인터페이스 표현 및 데이터 관리를 분리하는 인기 있는 패턴입니다. 웹 요청 처리를 계층적으로 다루어 유지보수 가능하고 테스트 가능한 웹 애플리케이션 개발을 용이하게 합니다.

스프링 MVC의 주요 기능:

Dispatcher Servlet: 요청 처리 및 적절한 컨트롤러로의 디스패치를 중앙에서 처리합니다.

<div class="content-ad"></div>

컨트롤러 클래스: 사용자 요청을 처리하고 데이터를 처리하며 모델과 상호 작용하여 적절한 뷰를 선택합니다.

뷰 기술: JSP, FreeMarker, Thymeleaf, Velocity와 같은 다양한 템플릿 엔진을 지원하여 동적 콘텐츠를 렌더링합니다.

Spring AOP (Aspect-Oriented Programming)

Spring AOP는 관점 지향 프로그래밍 (AOP)의 구현을 제공합니다. 이는 로깅, 보안, 트랜잭션 관리와 같은 교차 관심사를 모듈화하는 기술입니다. 개발자들은 이러한 관심사를 측면으로 캡슐화하고 응용 프로그램 실행 흐름 내에서 특정 지점에 적용할 수 있습니다.

<div class="content-ad"></div>

Spring AOP의 주요 기능은 다음과 같습니다:

- Aspect 선언: 관점(Aspect)을 정의하여, 적용해야 하는 교차 관점 문제 및 적용해야 하는 지점(pointcuts)을 지정합니다.

- Aspect 결합(weaving): Aspect를 애플리케이션의 실행 흐름에 통합하여, 특정 조인 포인트(join points)에서 Aspect의 동작을 적용합니다.

- Aspect 실행: Aspect 조언(advice)의 호출을 처리하여, 조인 포인트에서 취해야 할 행동을 정의합니다.

<div class="content-ad"></div>

다음은 Spring Core의 주요 기능입니다:

- 의존성 주입: 객체에 필요한 의존성을 자동으로 제공하여 코드 복잡성을 줄이고 모듈성을 향상시킵니다.

<div class="content-ad"></div>

빈 라이프사이클 관리: Spring 애플리케이션 컨텍스트 내의 객체 생성, 초기화, 파괴 및 범위를 처리합니다.

자원 관리: 데이터베이스 연결, 파일 및 메시징 큐와 같은 자원을 관리하여 자원 획득 및 해제를 간편하게 합니다.

# @ComponentScan을 통한 컴포넌트 스캔 작동 방식은?

주석 발견: Spring 프레임워크는 주석 기반 구성을 사용하여 Spring 빈을 식별합니다. 지정된 패키지 및 하위 패키지에서 @Component, @Service, @Repository, @Controller 또는 다른 스테레오타입 주석이 지정된 클래스를 검사하여 응용 프로그램에서 빈의 역할을 나타냅니다.

<div class="content-ad"></div>

빈 생성 및 등록: Spring Framework는 어노테이션으로 지정된 클래스를 발견하면 해당 클래스의 인스턴스를 생성하고 애플리케이션 컨텍스트에 Spring 빈으로 등록합니다. 애플리케이션 컨텍스트는 모든 관리되는 빈의 레지스트리를 유지하여 의존성 주입에 접근할 수 있게 합니다.

빈 구성: Spring Framework는 등록된 빈에 기본 구성 규칙을 적용합니다. 이러한 규칙에는 의존성 주입, 빈 생명주기 관리 및 리소스 관리가 포함됩니다. 개발자는 어노테이션, XML 구성 파일 또는 프로그래밍 방식을 사용하여 빈 구성을 더 잠재적으로 사용자 정의할 수 있습니다.

```java
구성 클래스:
@Configuration
@ComponentScan("com.example.springapp")
public class AppConfig {
}
```

이 구성 클래스는 두 개의 어노테이션을 정의합니다:

<div class="content-ad"></div>

- `@Configuration`: 이 클래스를 Spring의 빈 정의 원천으로 표시합니다.

- `@ComponentScan`: 구성 요소 스캔의 기본 패키지를 지정합니다. Spring은 이 패키지 및 하위 패키지를 스캔하여 `@Component`, `@Service`, `@Repository`, 또는 `@Controller`로 주석이 달린 클래스를 찾아 Spring 빈으로 등록합니다.

# ApplicationContext은 무엇이며 Spring 내에서 어떻게 사용할까요?

ApplicationContext는 빈 관리 및 Spring 애플리케이션에 서비스를 제공하는 핵심 컨테이너입니다. 빈 구성, 의존성 주입 및 자원 관리를 간단화합니다.

<div class="content-ad"></div>

이렇게 사용해야 합니다.

```js
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
MyService myService = context.getBean("myService", MyService.class);
myService.doSomething();
context.close();
```

이 코드는 XML 구성 파일에서 ApplicationContext를 생성하고, 이름으로 빈을 검색하여 빈의 메소드를 호출하고, ApplicationContext를 닫는 방법을 보여줍니다.

# BeanFactory란 무엇이며 Spring에서 어떻게 사용하는가?

<div class="content-ad"></div>

BeanFactory는 Spring 애플리케이션에서 빈을 관리하기 위한 기본 컨테이너를 나타내는 인터페이스입니다. 빈을 생성, 검색 및 구성하는 메서드를 제공합니다. ApplicationContext는 보다 고급이며 일반적으로 사용되는 컨테이너이지만, BeanFactory는 기본적인 빈 관리를 위한 더 간단한 인터페이스를 제공합니다.

다음은 예시입니다.

```java
BeanFactory factory = new XmlBeanFactory("applicationContext.xml");
MyService myService = factory.getBean("myService", MyService.class);
myService.doSomething();
```

# 빈이란 무엇인가요?

<div class="content-ad"></div>

빈: Spring에서 빈은 Spring IoC 컨테이너 내에서 관리되는 관리 객체입니다. Spring 프레임워크에 의해 생성, 관리 및 연결되는 클래스의 인스턴스입니다. 빈은 Spring 애플리케이션의 기본 구성 요소이며, 주로 주석 또는 XML 구성을 사용하여 구성 및 정의됩니다.

# 빈 스코프란 무엇인가요?

Spring 프레임워크에서 빈 스코프는 Spring IoC 컨테이너 내에서 빈의 수명주기와 가시성을 정의합니다. Spring 프레임워크는 각기 다른 목적과 동작을 가진 여러 내장 빈 스코프를 제공합니다.

다음은 Spring Framework에서 가장 일반적으로 사용되는 빈 스코프입니다:

<div class="content-ad"></div>

싱글톤:

(기본값) Spring IoC 컨테이너마다 개체 인스턴스에 대해 단일 빈 정의를 스코프 지정합니다.

프로토타입:

임의의 개체 인스턴스 수에 대해 단일 빈 정의를 스코프 지정합니다.

<div class="content-ad"></div>

요청:

하나의 빈 정의를 하나의 HTTP 요청 수명 주기에 한정합니다. 즉, 각 HTTP 요청은 하나의 빈 정의를 사용하여 생성된 별도의 인스턴스를 가지게 됩니다. 웹-aware Spring ApplicationContext의 맥락에서만 유효합니다.

세션:

하나의 빈 정의를 HTTP 세션의 수명 주기에 한정합니다. 웹-aware Spring ApplicationContext의 맥락에서만 유효합니다.

<div class="content-ad"></div>

애플리케이션:

하나의 빈 정의를 ServletContext의 라이프사이클에 범위를 지정합니다. 웹 기반 Spring ApplicationContext의 컨텍스트에서만 유효합니다.

웹소켓:

하나의 빈 정의를 웹소켓의 라이프사이클에 범위를 지정합니다. 웹 기반 Spring ApplicationContext의 컨텍스트에서만 유효합니다.

<div class="content-ad"></div>

# 스프링 빈 라이프사이클이란 무엇인가요?

스프링 프레임워크에서 빈은 Spring IoC 컨테이너에 의해 관리되는 객체입니다. 빈의 라이프사이클은 빈이 생성되어 소멸될 때까지 발생하는 일련의 이벤트입니다.

- 스프링 빈 라이프사이클은 인스턴스화, 구성, 소멸 세 단계로 나눌 수 있습니다.

- 인스턴스화: 이 단계에서 Spring IoC 컨테이너는 빈의 인스턴스를 생성합니다. 스프링 프레임워크는 빈을 인스턴스화하는 여러 방법을 지원합니다. 생성자를 통해, 정적 팩토리 메서드를 통해, 또는 인스턴스 팩토리 메서드를 통해 등등.

<div class="content-ad"></div>

- 설정: 이 단계에서는 Spring IoC 컨테이너가 새로 생성된 빈을 구성합니다. 이는 의존성 주입을 수행하고 빈 후처리기를 적용하며 초기화 및 소멸 콜백을 등록하는 것을 포함합니다.

- 소멸: 이 단계에서는 Spring IoC 컨테이너가 빈 인스턴스를 파괴합니다. 이는 Spring 빈 라이프사이클의 마지막 단계입니다.

이 세 단계 외에도 Spring 프레임워크는 개발자가 빈에 대한 사용자 정의 초기화 및 소멸 로직을 지정할 수 있도록 여러 콜백을 제공합니다. 이러한 콜백에는 다음이 포함됩니다:

- @PostConstruct: 빈이 구성된 후에 호출되며 모든 종속성이 주입된 후에 호출됩니다.

<div class="content-ad"></div>

- init-method: 빈이 생성되고 모든 의존성이 주입된 후 호출할 메서드를 지정합니다.

- destroy-method: 빈이 소멸되기 직전에 호출할 메서드를 지정합니다.

- @PreDestroy: 빈이 소멸되기 전에 호출됩니다.

Spring 빈 생명주기는 Spring IoC 컨테이너에 의해 제어되며, 빈의 생명주기를 생성, 구성 및 관리합니다. 개발자들은 빈 생명주기 콜백을 활용하여 빈에 사용자 정의 초기화 및 소멸 로직을 추가할 수 있으며, 객체의 생명주기를 쉽게 관리하고 리소스가 올바르게 처리되도록 할 수 있습니다.

<div class="content-ad"></div>

# 애플리케이션 컨텍스트에서 빈 라이프사이클은 무엇인가요?

애플리케이션 컨텍스트 내의 빈 라이프사이클은, 빈이 생성부터 소멸까지 거치는 다양한 단계를 가리킵니다. 애플리케이션 컨텍스트는 이 라이프사이클을 관리하며, 빈이 적절한 시간에 올바르게 초기화되고 사용되며 소멸되도록 보장합니다.

빈 라이프사이클의 단계:

- 빈 생성: 빈이 생성되고, 생성자 주입, 세터 주입 또는 다른 방법을 통해 초기화됩니다.

<div class="content-ad"></div>

- 빈 구성: 빈의 속성이 설정되고 필요한 초기화 콜백이 호출됩니다.

- 빈 사용: 응용 프로그램에서 빈을 사용하여 지정된 기능을 제공합니다.

- 빈 소멸: 빈이 더 이상 필요하지 않을 때 파괴되고, 리소스를 해제하고 필요한 정리 작업을 수행합니다.

빈 라이프사이클에서 애플리케이션 컨텍스트의 역할:

<div class="content-ad"></div>

- 애플리케이션 컨텍스트는 빈 생명주기를 관리하는 데 중요한 역할을 합니다. 빈의 생성, 구성, 사용 및 소멸을 제어하기 위한 다양한 메커니즘을 제공합니다.

- 빈 구성 메타데이터: 애플리케이션 컨텍스트는 빈에 대한 구성 메타데이터를 저장하며, 빈이 어떻게 생성, 구성 및 관리되어야 하는지를 정의합니다.

- 빈 생명주기 후크: 애플리케이션 컨텍스트는 빈 생명주기의 다양한 단계에서 초기화 및 소멸 콜백과 같이 사용자 정의 동작을 정의하는 후크를 제공합니다.

- 빈 범위 관리: 애플리케이션 컨텍스트는 빈의 범위를 관리하여 애플리케이션 내에서의 가시성과 수명을 결정합니다.

<div class="content-ad"></div>

- Bean Lifecycle Listeners: 애플리케이션 컨텍스트는 빈 생명주기 리스너를 지원하여 다른 빈의 생명주기 변경 사항을 알릴 수 있습니다.

## BeanFactory와 ApplicationContext의 차이는?

![이미지](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_6.png)

## 스프링의 기본 빈 스코프는 무엇인가요?

<div class="content-ad"></div>

Spring의 기본 빈 스코프는 싱글톤입니다. 이는 빈의 인스턴스가 한 번만 만들어지고 스프링 컨테이너에 의해 관리된다는 것을 의미합니다. 요청 횟수에 상관 없이 동일한 빈 인스턴스가 사용됩니다. 이 스코프는 상태가 없는 빈에 유용하며, 내부 상태를 유지하지 않고 응용 프로그램의 다양한 부분에서 안전하게 공유될 수 있는 빈에 적합합니다.

다음 다이어그램은 하나의 인스턴스가 생성되는 방법을 보여줍니다.

![diagram](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_7.png)

빈이 어떻게 스프링 내에서 로드되는지 설명해 주시겠어요? 그리고 레이지 로딩과 이거 로딩의 차이점을 설명해 주실 수 있나요?

<div class="content-ad"></div>

봄에는 빈 로딩이 있어요:

봄 프레임워크는 빈 인스턴스화라는 프로세스를 사용하여 빈을 만들고 애플리케이션에서 사용할 수 있게 해요. 이 프로세스에는 다음 단계가 포함돼요:

빈 스캔: 봄 프레임워크는 애플리케이션 컨텍스트를 스캔하여 @Component, @Service, @Repository, @Controller와 같은 스프링 주석이 달린 클래스를 찾아요.

빈 인스턴스 생성: 봄 프레임워크는 찾은 각 빈 클래스의 인스턴스를 생성해요.

<div class="content-ad"></div>

빈 구성: 스프링 프레임워크는 각 빈을 구성하여 속성을 설정하고 의존성을 주입합니다.

빈 초기화: 스프링 프레임워크는 초기화 콜백을 호출하여 각 빈을 초기화합니다.

# 지연 로딩 vs 즉시 로딩? (중요한 면접 질문)

Spring에서 빈은 지연로딩 또는 즉시로딩으로 설정할 수 있습니다. 지연로딩은 애플리케이션이 처음 요청할 때까지 빈이 생성되지 않는 것을 의미합니다. 즉시로딩은 애플리케이션이 시작될 때 빈이 생성됩니다.

<div class="content-ad"></div>

Spring의 기본 동작은 빈을 Eager로 로드하는 것입니다. 이는 실제로 필요한 빈만 만들기 때문에 애플리케이션의 성능을 향상시킬 수 있습니다. 그러나 lazy loading은 생성된 빈을 추적하기 어렵게 만들어 애플리케이션을 디버깅하기 어렵게 만들 수도 있습니다.

Eager loading은 애플리케이션이 시작하는 즉시 필요한 빈에 유용할 수 있습니다. 예를 들어, 애플리케이션이 시작되자마자 데이터베이스에 연결해야 할 수도 있습니다. 이 경우 데이터베이스에 연결하는 책임을 지는 빈을 Eager로 로드하는 것이 합리적일 것입니다.

# Lazy 로딩 및 Eager 로딩 지정 방법

@Lazy 주석을 사용하여 빈을 lazy 로드할지 또는 eager로 로드할지 지정할 수 있습니다. 예를 들어, 다음 코드는 lazy로 로드되는 빈을 생성합니다:

<div class="content-ad"></div>

```java
@Lazy
@Service
public class MyService {
}
```

아래 코드는 즉시 로딩되는 빈을 생성합니다:

```java
@Service
public class MyEagerService {
}
```

레이지 로딩과 이거 로딩을 언제 사용해야 하는지:

<div class="content-ad"></div>

- 애플리케이션이 시작될 때 즉시 필요하지 않은 bean에는 lazy loading을 사용하세요.
- 애플리케이션이 시작될 때 즉시 필요한 bean에는 eager loading을 사용하세요.
- 시작 비용이 높은 bean에는 eager loading을 사용하세요.
- 자주 사용되지 않는 bean에는 lazy loading을 사용하세요.

**@Autowired 어노테이션이 작동하는 방식**

@Autowired 어노테이션은 리플렉션(reflection)을 사용하여 적절한 의존성을 주입하도록 작동합니다. 먼저, 해당 종속성과 동일한 유형의 bean을 찾습니다. 동일한 유형의 bean을 찾을 수 없는 경우, 이름이 의존성과 일치하는 qualifier를 갖는 bean을 찾습니다. 그래도 주입할 bean을 찾을 수 없는 경우 예외가 발생합니다.

**자동 주입의 종류**

<div class="content-ad"></div>

@Autowired 어노테이션이 지원하는 네 가지 자동 연결 유형이 있습니다:

- byType: 이것은 기본 자동 연결 유형입니다. Spring Framework는 종속성과 동일한 유형의 빈을 찾습니다.

- byName: Spring Framework는 종속성과 동일한 이름을 가진 빈을 찾습니다.

- byConstructor: Spring Framework는 종속성의 유형과 일치하는 단일 인수를 사용하는 생성자를 찾습니다.

<div class="content-ad"></div>

- byQualifier: 스프링 프레임워크는 지정된 값과 일치하는 한정자를 가진 빈을 찾습니다.

## 어떻게 자동 와이어링에서 빈을 제외할 수 있을까요?

Spring에서 빈을 자동 와이어링에서 제외하는 방법은 다음과 같습니다:

1. XML 구성에서 autowire-candidate를 false로 설정하기:

<div class="content-ad"></div>

XML 구성에서 제왈하고 싶은 'bean' 요소에 autowire-candidate="false" 속성을 추가하세요:

```js
<bean id="myBean" class="com.example.MyBean" autowire-candidate="false"></bean>
```

2. @Lazy 주석 사용하기 (지연 초기화를 위해):

빈 구성을 주석으로 사용하는 경우, 빈 클래스나 해당 @Bean 메서드에 @Lazy를 주석으로 추가하세요:

<div class="content-ad"></div>

```java
@Lazy
@Bean
public MyBean myBean() {
    return new MyBean();
}
```

3. Disabling auto-configuration for a specific bean:

In Spring Boot, use `@SpringBootApplication(exclude = 'MyBeanAutoConfiguration.class')` to exclude a specific auto-configuration class.

# Difference between `@Autowire` and `@Inject` in Spring?

<div class="content-ad"></div>

@Autowired 및 @Inject 어노테이션은 둘 다 Spring 애플리케이션에서 의존성 주입에 사용됩니다. 그러나 이 두 어노테이션 간에는 몇 가지 주요 차이점이 있습니다.

@Autowired(스프링 애플리케이션 개발 시 사용)

@Autowired 어노테이션은 스프링 전용 어노테이션입니다. 이 어노테이션은 Spring 컨테이너에서 관리되는 빈에 의존성을 주입하는 데 사용됩니다. @Autowired 어노테이션은 byType, byName, byConstructor, byQualifier라는 네 가지 자동 연결 유형과 함께 사용할 수 있습니다.

@Inject(스프링이 아닌 애플리케이션 개발 시 사용)

<div class="content-ad"></div>

`@Inject` 어노테이션은 표준 JSR-330 어노테이션입니다. 이 어노테이션은 JSR-330을 지원하는 의존성 주입 컨테이너에서 관리되는 빈에 의존성을 주입하는 데 사용됩니다. `@Inject` 어노테이션은 byType 및 byName 자동 연결과 함께만 사용할 수 있습니다.

# 싱글톤 Bean은 스레드 안전한가요?

아니요, 싱글톤 빈은 기본적으로 스레드 안전하지 않습니다. 이는 두 스레드가 동시에 싱글톤 빈에 액세스하려고 시도하는 경우 충돌하는 결과가 발생할 수 있다는 것을 의미합니다. 이는 싱글톤 빈이 모든 스레드에서 공유되며, 한 스레드가 싱글톤 빈에 대한 변경을 가하면 다른 모든 스레드에서도 해당 변경 사항이 보인다는 것을 의미합니다.

# 싱글톤과 프로토타입 빈의 차이점은 무엇인가요?

<div class="content-ad"></div>


![2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_8.png](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_8.png)

# 스프링에서 @Bean 어노테이션이란?

@Bean 어노테이션은 Spring Framework 어노테이션으로, 메소드를 빈 정의로 표시하는 역할을 합니다. 빈 정의는 Spring 컨테이너에게 빈을 생성하고 관리하는 방법을 알려줍니다. 메소드에 @Bean 어노테이션을 붙이면 Spring 컨테이너는 메소드에서 반환된 빈 클래스의 인스턴스를 생성하고 라이프사이클을 관리합니다.

```js
@Configuration
public class MyConfiguration {
@Bean
public MyService myService() {
return new MyServiceImpl();
}
}
```



<div class="content-ad"></div>

이 예시에서 myService() 메소드는 @Bean으로 주석 처리되어 있습니다. 이는 Spring 컨테이너에 MyServiceImpl 클래스의 인스턴스를 생성하고 라이프사이클을 관리하도록 알려줍니다. 그런 다음 해당 빈은 @Autowired 주석을 사용하여 다른 빈에 주입될 수 있습니다.

# @Configuration 주석이 무엇인가요?

@Configuration 주석은 Spring Framework 주석으로, 클래스를 구성 클래스로 표시합니다. 구성 클래스는 Spring 애플리케이션에서 빈을 정의하는 데 사용됩니다. 클래스에 @Configuration이 주석으로 달려 있을 때, Spring 컨테이너는 @Bean으로 주석이 달린 메소드를 검색하여 그 메소드를 사용하여 빈을 정의합니다.

```js
@Configuration
public class MyConfiguration {
@Bean
public MyService myService() {
return new MyServiceImpl();
}
}
```

<div class="content-ad"></div>

# Spring 프로필을 구성하는 방법은?

Spring 프로필은 애플리케이션 구성의 일부를 분리하고 특정 환경에서만 사용할 수 있도록하는 방법을 제공합니다.

Spring 프로필을 구성하는 두 가지 방법이 있습니다:

1. Spring Boot 속성 파일을 사용하기

<div class="content-ad"></div>

Spring Boot 속성 파일은 일반적으로 application.properties 또는 application.yml로 명명되며 Spring 프로필을 구성하는 편리한 방법입니다. 속성 파일을 사용하여 프로필을 구성하려면 다음 속성을 추가하면 됩니다:

예:
spring.profiles.active=profile1,profile2

Markdown 형식으로 변경해 드릴까요?

# @component, @profile, @value 어노테이션은 무엇인가요?

<div class="content-ad"></div>


![image](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_9.png)

# @Value 어노테이션 내부의 $와 #은 무엇을 하는 것인가요?

$ 기호는 속성 값을 다양한 소스(예: 속성 파일, 환경 변수 및 시스템 속성)에서 주입하는 데 사용됩니다. 예를 들어, 다음 코드는 application.properties 파일에서 app.name 속성의 값을 주입합니다:

예:


<div class="content-ad"></div>

```java
@Value("$'app.name'")

private String appName;

```

# 기호는 SpEL(Spandom Expression Language) 표현식에 액세스하는 데 사용됩니다. SpEL은 속성 값에 대한 복잡한 작업을 수행하는 데 사용할 수 있는 강력한 표현 언어입니다. 예를 들어, 다음 코드는 app.name 속성의 값을 주입하고 대문자로 변환합니다:

예시.

<div class="content-ad"></div>

```java
@Value("#'‘$'app.name'’.toUpperCase()'")

private String appNameUppercase;
```

# 스프링에서 stateless bean이란 무엇인가요? 그것의 이름을 말하고 설명해주세요.

Spring Framework에서 stateless bean은 메소드 호출 사이에 상태를 유지하지 않는 bean입니다. 이는 bean이 이전 호출에 대한 정보를 저장하지 않고 각 메소드 호출이 독립적으로 처리된다는 것을 의미합니다.

<div class="content-ad"></div>

Stateless beans are typically used for services that perform actions or calculations, but do not maintain any state between invocations. This can include services that perform mathematical calculations, access external resources, or perform other tasks that do not require the bean to maintain state.

Stateless beans can be implemented as singleton beans, and multiple clients can share the same instance of the bean. Since stateless beans do not maintain any state, they can be easily scaled horizontally by adding more instances of the bean to handle the increased load.

Stateless beans also have the advantage of being simpler and easier to reason about, since they do not have to worry about maintaining state between invocations. Additionally, since stateless beans do not maintain any state, they can be easily serialized and replicated for high availability and scalability.

# Spring에서 빈을 주입하는 방법은 무엇인가요?

<div class="content-ad"></div>

봄(Spring)에서 빈(bean)은 의존성 주입(Dependency Injection, DI) 패턴을 사용하여 다른 빈에 주입(또는 연결)됩니다. DI는 클래스가 스스로 생성하는 대신 의존성을 제공받을 수 있는 설계 패턴입니다.

Spring은 다른 빈에 빈을 주입하는 여러 가지 방법을 제공합니다.

생성자 주입(Constructor injection): 빈을 생성자 인수로 전달하여 다른 빈에 주입할 수 있습니다. 스프링은 의존성이 있는 빈의 인스턴스를 자동으로 생성하고 생성자에 전달합니다.

```java
public class BeanA {
private final BeanB beanB;
public BeanA(BeanB beanB) {
this.beanB = beanB;
}
}
```

<div class="content-ad"></div>

Setter 주입: 한 빈(Bean)을 다른 빈에 세터 메소드 인자로 전달하여 주입할 수 있습니다. 스프링(Spring)은 자동으로 세터 메소드를 호출하고 의존성 있는 빈(Bean)을 전달합니다.

```java
public class BeanA {
    private BeanB beanB;
    @Autowired
    public void setBeanB(BeanB beanB) {
        this.beanB = beanB;
    }
}
```

필드 주입: 한 빈을 다른 빈에 @Autowired 어노테이션을 사용하여 필드에 주입할 수 있습니다. 스프링은 해당 필드에 의존성 있는 빈을 자동으로 설정합니다.

```java
public class BeanA {
    @Autowired
    private BeanB beanB;
}
```

<div class="content-ad"></div>

인터페이스 주입: 빈은 인터페이스를 구현함으로써 다른 빈에 주입될 수 있습니다. Spring은 의존성 빈을 필드에 자동으로 설정해 줍니다.

```java
public class BeanA implements BeanBUser {
@Autowired
private BeanB beanB;
}
```

중요한 점은 위의 방법을 임의 조합하여 사용할 수 있지만, 사용 사례에 따라 적합한 방법을 선택해야 한다는 것입니다.

또한, Spring은 자동으로 빈을 연결하기 위해 Autowiring이라는 기술을 사용합니다. Autowiring은 타입, 이름 또는 생성자를 통해 수행할 수 있습니다.

<div class="content-ad"></div>

기본적으로 Spring은 빈을 유형별로 자동 연결하려고 시도하지만, 동일한 유형의 빈이 여러 개인 경우 구성 파일에서 정의된 빈의 이름을 사용하여 이름별로 자동 연결을 시도합니다.

# 빈 간 순환 종속성 처리 방법?

예를 들어, 빈 A가 빈 B에 종속되고 빈 B가 빈 A에 종속되는 경우를 생각해 봅시다. 스프링 컨테이너는 어떻게 즉시 로딩과 지연 로딩을 처리할까요?

빈 간 순환 종속성은 두 개 이상의 빈이 서로 상호 종속성을 갖는 경우 발생하며, 이로 인해 이러한 빈들의 생성 및 초기화에 문제가 발생할 수 있습니다.

<div class="content-ad"></div>

Spring에서 빈 간의 순환 종속성을 처리하는 여러 가지 방법이 있습니다:

- Lazy Initialization: 순환에 관여하는 빈 중 하나에 @Lazy 주석을 사용하여 필요할 때만 초기화할 수 있습니다.


@Lazy
@Autowired


<div class="content-ad"></div>

private BeanA beanA;

생성자 주입: setter나 필드 주입 대신 생성자 주입을 사용하면 의존성이 제대로 제공될 때까지 빈이 완전히 초기화되도록 할 수 있습니다.

```java
public class BeanA {
    private final BeanB beanB;

    public BeanA(BeanB beanB) {
        this.beanB = beanB;
    }
}
```

프록시 사용: 프록시를 사용하여 한 빈의 초기화를 실제로 필요할 때까지 지연시켜 순환을 중단할 수 있습니다. Spring AOP를 사용하여 순환이 발생하는 빈 중 하나에 대한 프록시를 생성할 수 있습니다.

<div class="content-ad"></div>

BeanFactory를 사용하세요: 빈을 직접 주입하는 대신, 실제로 필요할 때 BeanFactory를 사용하여 빈을 검색할 수 있습니다.

```java
public class BeanA {

    private BeanB beanB;

    @Autowired
    public BeanA(BeanFactory beanFactory) {
        this.beanB = beanFactory.getBean(BeanB.class);
    }
}
```

Spring 부트 애플리케이션을 시작/로드하기 전에 어떤 메서드를 호출할까요?

<div class="content-ad"></div>

Spring Boot에서는 Spring Boot 애플리케이션을 시작하거나 로드하기 전에 호출할 수 있는 여러 메서드가 있습니다. 가장 일반적으로 사용되는 메서드 중 일부는 다음과 같습니다:

main() 메서드: main() 메서드는 일반적으로 Spring Boot 애플리케이션의 진입점입니다. SpringApplication.run() 메서드를 호출하여 Spring Boot 애플리케이션을 시작하는 데 사용됩니다.

@PostConstruct 메서드: @PostConstruct 주석은 빈이 구성된 후에 호출되어야 하는 메서드를 표시하는 데 사용할 수 있습니다. 이를 사용하여 애플리케이션이 시작하기 전에 필요한 초기화를 수행할 수 있습니다.

CommandLineRunner 인터페이스: CommandLineRunner 인터페이스는 빈에 의해 구현될 수 있으며 Spring Application 컨텍스트가 로드된 후에 특정 코드를 실행하는 데 사용될 수 있습니다.

<div class="content-ad"></div>

ApplicationRunner 인터페이스: ApplicationRunner 인터페이스는 빈에 의해 구현될 수 있어 Spring 응용 프로그램 컨텍스트가로드되고 응용 프로그램 인수가 처리 된 후에 특정 코드를 실행하는 데 사용할 수 있습니다.

@EventListener: @EventListener 어노테이션은 ApplicationStartingEvent, ApplicationReadyEvent 등과 같은 특정 응용 프로그램 이벤트를 수신하도록 메서드를 등록하는 데 사용할 수 있습니다.

# Spring 프레임워크에서 예외를 처리하는 방법

Spring 프레임워크에서 예외를 처리하는 여러 가지 방법이 있습니다.

<div class="content-ad"></div>

try-catch 블록: 예외를 처리하고 처리할 수 있도록 try-catch 블록을 사용할 수 있습니다. 특정 예외를 처리하는 데 유용하며 특정 메소드 내에서 발생할 가능성이 있는 예외를 처리할 수 있습니다.

@ExceptionHandler 어노테이션: @Controller 클래스의 메소드에 @ExceptionHandler 어노테이션을 사용하여 동일한 클래스 내의 다른 메소드에서 발생하는 예외를 처리할 수 있습니다. 여러 메소드에서 특정 예외를 중앙에서 처리하는 데 유용합니다.

@ControllerAdvice 어노테이션: 글로벌 예외 처리기를 정의하기 위해 @ControllerAdvice 어노테이션을 클래스에 사용할 수 있습니다. 응용 프로그램의 여러 컨트롤러에서 특정 예외를 중앙에서 처리하는 데 유용합니다.

HandlerExceptionResolver 인터페이스: HandlerExceptionResolver 인터페이스를 구현하여 전체 응용 프로그램에 대한 전역 예외 처리기를 만들 수 있습니다. 응용 프로그램 전체에서 특정 예외를 중앙에서 처리하는 데 유용합니다.

<div class="content-ad"></div>

에러 페이지: 응용 프로그램에서 특정 예외가 발생할 때 특정 페이지로 리디렉션하는 ErrorPage를 정의할 수 있습니다. 이 접근 방식은 예외가 발생했을 때 사용자 친화적인 에러 페이지를 표시하는 데 유용합니다.

@ResponseStatus 어노테이션: 예외 클래스에 @ResponseStatus 어노테이션을 사용하여 예외가 발생했을 때 반환되어야 하는 HTTP 상태 코드를 정의할 수 있습니다.

# 스프링에서 필터는 어떻게 작동하나요?

스프링에서 필터는 요청과 응답의 인터셉터 역할로, 실제 응용 프로그램 로직에 도달하기 전후에 처리합니다. 이들은 미리 정의된 규칙에 따라 요청을 수정, 거부 또는 허용할 수 있는 "수문장"과 같은 역할을 합니다.

<div class="content-ad"></div>

아래는 간략히 설명드리겠습니다:

1. 설정:

   - 필터를 Spring 구성에서 빈으로 정의합니다.

   - 필터가 실행되는 순서를 지정합니다.

<div class="content-ad"></div>

2. 요청 흐름:

- 클라이언트가 요청을 보내면 지정된 순서대로 각 필터를 거칩니다.

- 각 필터는:

- 요청 헤더, 본문 및 기타 속성을 검사할 수 있습니다.

<div class="content-ad"></div>

**변경 내용 또는 헤더를 수정합니다.**

**요청을 계속 처리할지 종료할지 결정합니다.**

**3. 응답 흐름:**

**· 요청이 응용 프로그램 논리에 도달하고 응답을 받으면, 응답이 역순으로 다시 필터를 통해 흐릅니다.**

<div class="content-ad"></div>

필터는 다시 다음을 수행할 수 있습니다:

- 응답 헤더 및 본문을 검사합니다.
- 응답 내용이나 헤더를 수정합니다.

4. 일반적인 사용 사례:

<div class="content-ad"></div>

- 보안 필터: 사용자 인증을 확인하고 액세스 권한을 부여하며 보안 취약점을 방지합니다.

- 로깅 필터: 요청 및 응답에 대한 정보를 기록하여 디버깅 및 분석에 사용합니다.

- 압축 필터: 대역폭 사용량을 줄이기 위해 응답을 압축합니다.

- 캐싱 필터: 성능을 향상시키기 위해 자주 액세스되는 리소스를 캐싱합니다.

<div class="content-ad"></div>

혜택:

- 요청과 응답을 가로채고 수정: 애플리케이션 동작에 더 많은 제어를 제공합니다.

- 공통 작업 중앙 집중화: 보안, 로깅 등의 코드 중복을 피합니다.

- 여러 필터를 연결: 여러 필터를 결합하여 복잡한 처리 논리를 구현할 수 있습니다.

<div class="content-ad"></div>

# 디스패쳐 서블릿이란?

디스패쳐 서블릿은 스프링 MVC 애플리케이션의 중심 "프론트 컨트롤러" 역할을 합니다. 이는 모든 들어오는 HTTP 요청을 받고 적절한 컨트롤러 클래스에 위임하는 서블릿입니다. 디스패쳐 서블릿은 각 요청에 대해 적절한 핸들러 메소드를 식별하고 호출하여 요청이 올바르게 처리되도록 하는 역할을 합니다.

다음은 Java 구성의 예제로, 서블릿 컨테이너에서 자동으로 감지되는 디스패쳐 서블릿을 등록하고 초기화하는 방법을 보여줍니다.

```js
public class MyWebApplicationInitializer implements WebApplicationInitializer {
@Override
public void onStartup(ServletContext servletContext) {
// 스프링 웹 애플리케이션 구성 로드
AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
context.register(AppConfig.class);
// 디스패쳐 서블릿 생성 및 등록
DispatcherServlet servlet = new DispatcherServlet(context);
ServletRegistration.Dynamic registration = servletContext.addServlet("app", servlet);
registration.setLoadOnStartup(1);
registration.addMapping("/app/*");
}
}
```

<div class="content-ad"></div>

# 스프링에서 @Controller 주석은 무엇인가요?

@Controller 주석은 스프링의 스테레오타입 주석으로, 클래스가 웹 컨트롤러로 작동한다는 것을 나타냅니다. 주로 Spring MVC 애플리케이션에서 사용되며 클래스를 HTTP 요청의 핸들러로 표시하는 데 사용됩니다. 클래스가 @Controller로 주석 처리되면 Spring 컨테이너에 의해 스캔되어 특정 HTTP 요청을 처리하는 메서드로 식별될 수 있습니다.

Spring MVC는 @Controller 및 @RestController 컴포넌트가 요청 매핑, 요청 입력, 예외 처리 등을 표현하기 위해 주석을 사용하는 주석 기반 프로그래밍 모델을 제공합니다. 주석이 지정된 컨트롤러는 유연한 메서드 시그니처를 가지고 있으며 기본 클래스를 확장하거나 특정 인터페이스를 구현할 필요가 없습니다. 다음 예시는 주석을 사용하여 정의된 컨트롤러를 보여줍니다:

예시.

<div class="content-ad"></div>


```js
@Controller
public class HelloController {
@GetMapping("/hello")
public String handle(Model model) {
model.addAttribute("message", "Hello World!");
return "index";
}
}
```

# 컨트롤러가 들어오는 요청과 적절한 메소드를 매핑하는 방법은?

@RequestMapping 주석을 사용하여 요청을 컨트롤러 메소드에 매핑할 수 있습니다. URL, HTTP 메소드, 요청 매개변수, 헤더 및 미디어 유형에 따라 일치시킬 수 있는 다양한 속성이 있습니다. 클래스 수준에서 사용하여 공유 매핑을 나타낼 수도 있고, 메소드 수준에서 사용하여 특정 엔드포인트 매핑으로 좁힐 수도 있습니다. 요청 매핑 프로세스:

또한 @RequestMapping의 HTTP 메소드별 바로 가기 변형도 있습니다.



<div class="content-ad"></div>



- @GetMapping
- @PostMapping
- @PutMapping
- @DeleteMapping



<div class="content-ad"></div>



## @PatchMapping

**Request Reception:** The DispatcherServlet receives an incoming HTTP request containing the request URI, HTTP method (GET, POST, PUT, DELETE, etc.), and request parameters.

**Mapping Lookup:** The DispatcherServlet utilizes a HandlerMapping component to lookup the appropriate handler method for the received request. The HandlerMapping maintains a registry of mappings between request patterns and handler methods.

**Pattern Matching:** The HandlerMapping compares the request URI and HTTP method against the registered request patterns. It uses pattern matching rules to identify the most specific matching pattern.



<div class="content-ad"></div>

핸들러 메서드 식별: 일치하는 패턴을 식별한 후 HandlerMapping은 해당 핸들러 메서드를 레지스트리에서 검색합니다. 이 핸들러 메서드는 들어오는 요청을 처리하는 책임이 있습니다.

메서드 호출: DispatcherServlet은 식별된 핸들러 메서드를 호출하며, 요청 객체를 인수로 전달합니다. 핸들러 메서드는 요청의 로직을 처리하고 적절한 응답을 생성합니다.

응답 처리: 핸들러 메서드가 실행을 완료하면, DispatcherServlet은 생성된 응답 객체를 수신합니다. 적절한 헤더와 콘텐츠를 설정하여 응답을 준비하고 클라이언트에게 응답을 전송합니다.

이것은 샘플 프로그램입니다.

<div class="content-ad"></div>

```kotlin
@RestController
@RequestMapping("/persons")
class PersonController {
@GetMapping("/{id}")
public Person getPerson(@PathVariable Long id) {
// …
}
@PostMapping
@ResponseStatus(HttpStatus.CREATED)
public void add(@RequestBody Person person) {
// …
}
}
```

## Spring MVC에서 @Controller와 @RestController의 차이점은 무엇인가요?

@Controller:

전통적인 MVC 애플리케이션에 대한 컨트롤러로 클래스를 지정합니다.

<div class="content-ad"></div>

일반적으로 메서드는 뷰 이름(String)을 반환하며, 이는 뷰 리졸버에 의해 해결되어 뷰를 렌더링합니다 (예: HTML 페이지).

요청 경로와 동일한 경우 뷰 이름을 반환하는 void를 반환할 수도 있습니다.

개별 메서드에 @ResponseBody를 사용하여 데이터를 직접 반환할 수도 있지만, 이것은 기본 동작이 아닙니다.

@RestController:

<div class="content-ad"></div>

레스트풀 웹 서비스를 구축하는 데 특화된 컨트롤러입니다.

모든 핸들러 메서드에 @ResponseBody가 암시적으로 적용되어 데이터를 일반적으로 JSON 또는 XML 형식으로 응답 본문에 직접 반환합니다.

데이터를 반환할 때 뷰 해상도나 수동 설정이 필요하지 않습니다.

레스트 API 개발을 간단하게 만들어 줍니다.

<div class="content-ad"></div>

각각을 사용하는 시점:

- 뷰를 렌더링하고 HTML 콘텐츠를 반환하는 전통적인 웹 애플리케이션에는 @Controller를 사용합니다.

- JSON이나 XML과 같은 형식으로 주로 데이터를 반환하는 RESTful API를 구축할 때는 @RestController를 사용합니다.

예시:

<div class="content-ad"></div>

```java
@Controller
public class MyController {
@GetMapping("/hello")
public String hello() {
return "hello"; // "hello" 뷰 이름을 반환합니다
}
}

@RestController
public class MyRestController {
@GetMapping("/greeting")
public String greeting() {
return "Hello, World!"; // JSON 또는 XML로 "Hello, World!"을 반환합니다
}
}
```

# @Requestparam과 @Pathparam 어노테이션의 차이점은?

@Requestparam:

@Requestparam 어노테이션을 사용하여 서블릿 요청 매개변수(쿼리 매개변수 또는 폼 데이터)를 컨트롤러의 메소드 인수에 바인딩할 수 있습니다.



<div class="content-ad"></div>

여기 코드 예시입니다.

```java
@Controller
@RequestMapping("/pets")
public class EditPetForm {
    @GetMapping
    public String setupForm(@RequestParam("petId") int petId, Model model) {
        Pet pet = this.clinic.loadPet(petId);
        model.addAttribute("pet", pet);
        return "petForm";
    }
}
```

`@RequestParam`을 사용하여 petId를 바인딩합니다.

기본적으로 이 주석을 사용하는 메소드 파라미터는 필수입니다. 하지만 `@RequestParam` 주석의 `required` 플래그를 `false`로 설정하거나 `java.util.Optional` 래퍼로 인자를 선언함으로써 메소드 파라미터가 옵션임을 지정할 수 있습니다.

<div class="content-ad"></div>

@Pathparam

기능:

- 요청 URI 경로에서 변수를 Spring 컨트롤러의 메소드 매개변수로 매핑할 수 있습니다.
- 이를 통해 API에서 동적 데이터를 처리하는 더 깔끔하고 유연한 방법을 제공합니다.

사용 방법:

<div class="content-ad"></div>

- @Pathparam 어노테이션을 메소드 매개변수에 적용합니다.
- 어노테이션 내에서 URI 경로에서 변수의 이름을 지정하여 매개변수에 바인딩합니다.

![이미지](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_10.png)

# 세션 스코프는 무엇에 사용됩니까?

세션 스코프는 특정 HTTP 세션에 바인드된 객체의 라이프사이클을 관리하는 방법입니다. 세션 스코프에서 객체가 생성되면 세션에 저장되어 동일 세션에 속하는 모든 요청에서 접근할 수 있습니다. 이는 사용자별 정보를 저장하거나 여러 요청 간에 상태를 유지하는 데 유용할 수 있습니다.

<div class="content-ad"></div>

# Spring에서 @Component, @Service, @Controller, @Repository 어노테이션의 차이는 무엇입니까?

![이미지](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_11.png)

# Spring-MVC 흐름 상세히 설명

Spring MVC는 Java 웹 애플리케이션을 구축하기 위한 인기있는 웹 프레임워크입니다. 이는 Model-View-Controller 아키텍처를 제공하여 응용 프로그램 로직을 모델, 뷰, 컨트롤러 세 가지 구성 요소로 분리합니다. Spring MVC 흐름은 다음 단계를 포함합니다:

<div class="content-ad"></div>


![2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_12.png](/assets/img/2024-05-27-Top60Spring-FrameworkInterviewQuestionsforJavaDevelopers2024ContainAlltheQuestionsfromtheBook_12.png)

고객이 요청을 보냅니다: 사용자가 브라우저 또는 다른 클라이언트 응용 프로그램을 통해 Spring MVC 애플리케이션에 요청을 보냅니다.

DispatcherServlet이 요청을 받습니다: DispatcherServlet은 Spring MVC 아키텍처의 중앙 컨트롤러입니다. 클라이언트로부터 요청을 받고 요청을 처리할 컨트롤러를 결정합니다.

HandlerMapping이 적절한 컨트롤러를 선택합니다: HandlerMapping 구성 요소는 Spring 구성 파일에서 구성된 URL 패턴을 기반으로 요청 URL을 적절한 컨트롤러에 매핑합니다.


<div class="content-ad"></div>

컨트롤러는 요청을 처리합니다: 컨트롤러는 요청을 처리하고 필요한 처리 로직을 수행합니다. 모델 구성 요소와 상호작용하여 데이터를 검색하거나 업데이트할 수 있습니다.

모델은 데이터를 업데이트합니다: 모델 구성 요소는 데이터를 관리하고 컨트롤러가 데이터를 검색하거나 업데이트할 수 있는 인터페이스를 제공합니다.

뷰 리졸버는 적절한 뷰를 선택합니다: 뷰 리졸버 구성 요소는 컨트롤러에서 반환된 논리적 뷰 이름을 실제 뷰 템플릿에 매핑합니다.

뷰가 응답을 렌더링합니다: 뷰 템플릿이 렌더링되어 응답을 생성합니다. 모델 구성 요소에서 데이터를 포함할 수 있습니다.

<div class="content-ad"></div>

DispatcherServlet은 응답을 보냅니다: DispatcherServlet은 JSP, HTML 또는 JSON과 같은 적절한 뷰 기술을 통해 응답을 클라이언트에 다시 보냅니다.

Spring MVC 흐름은 순환적인 프로세스이며, 클라이언트가 애플리케이션으로 추가 요청을 보낼 수 있으며, 이 주기가 반복됩니다.

# 가장 일반적인 Spring MVC 어노테이션은 무엇인가요?

컨트롤러 어노테이션:

<div class="content-ad"></div>

@Controller: 클래스를 컨트롤러로 지정하여 HTTP 요청을 처리하고 응답을 렌더링하는 역할을 담당합니다.

@RestController: @Controller의 특수한 버전으로, 핸들러 메서드에 @ResponseBody를 암시적으로 추가하여 데이터를 직접 응답 본문에 쓰도록 지정합니다. 주로 JSON 또는 XML 형식으로 작성됩니다.

요청 매핑 어노테이션:

@RequestMapping: URL 패턴, HTTP 메서드(GET, POST, PUT, DELETE 등), 요청 매개변수 및 헤더에 기반하여 웹 요청을 특정 컨트롤러 메서드에 매핑합니다.

<div class="content-ad"></div>


@GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping: 특정 HTTP 메서드에 매핑하는 편리한 바로 가기입니다.

@PathVariable: URL의 경로 세그먼트 변수에 메서드 매개변수를 바인딩합니다.

@RequestParam: 요청 URL의 쿼리 매개변수에 메서드 매개변수를 바인딩합니다.

데이터 바인딩 어노테이션:


<div class="content-ad"></div>

@ModelAttribute: 객체를 사용하여 모델 속성을 채워 뷰에서 사용할 수 있도록 합니다.

@RequestParam: 요청 매개변수를 메서드 매개변수에 바인딩합니다.

@RequestHeader: 요청 헤더를 메서드 매개변수에 바인딩합니다.

@RequestBody: 요청 본문을 메서드 매개변수에 매핑하며 주로 JSON 또는 XML 데이터에 사용됩니다.

<div class="content-ad"></div>

응답 처리 어노테이션:

- **@ResponseBody**: 메서드의 반환 값이 뷰 해상도를 우회하고 직접 응답 본문에 작성되어야 함을 나타냅니다.

- **@ResponseStatus**: 응답의 HTTP 상태 코드를 설정합니다.

예외 처리 어노테이션:

<div class="content-ad"></div>

@ExceptionHandler: 특정 유형의 예외를 처리하는 메서드를 정의하여 오류를 관리하는 중앙화된 방법을 제공합니다.

다른 유용한 주석:

- @SessionAttribute: 세션 속성에 액세스합니다.
- @ModelAttribute: 컨트롤러의 모든 핸들러 메서드에 모델에 속성을 추가합니다.

<div class="content-ad"></div>

@InitBinder: 컨트롤러의 데이터 바인딩 및 유효성 검사를 사용자 정의합니다.

@CrossOrigin: 컨트롤러나 특정 핸들러 메소드에 대해 교차 출처 요청을 활성화합니다.

# 싱글톤 빈 범위가 여러 병렬 요청을 처리할 수 있을까요?

Spring의 싱글톤 빈은 모든 요청 간에 공유되는 단일 인스턴스를 갖습니다. 즉, 두 요청이 동시에 처리되더라도 동일한 빈 인스턴스를 공유하며 빈의 상태에 대한 액세스가 요청 사이에서 공유됩니다.

<div class="content-ad"></div>

그러나 싱글톤 빈이 상태를 유지하고 있고 그 상태가 요청 사이에 공유된다면, 이는 경합 조건 및 기타 동시성 문제로 이어질 수 있다는 점을 유의해야 합니다. 예를 들어, 두 요청이 동시에 동일한 데이터를 수정하려고 할 때 데이터 불일치 문제가 발생할 수 있습니다.

이러한 문제를 피하기 위해서는 상태를 유지하는 싱글톤 빈이 스레드 안전하게 설계되었는지 확인하는 것이 중요합니다. 이를 위해 동기화 또는 다른 동시성 제어 메커니즘을 활용할 수 있습니다. synchronized 키워드, Lock 또는 ReentrantLock 클래스, 또는 빈이 데이터베이스 작업을 수행하는 경우 @Transactional 어노테이션을 사용하는 것이 한 가지 방법입니다.

반면에, 싱글톤 빈이 상태를 유지하지 않는 상태라면 여러 병렬 요청을 문제없이 처리할 수 있습니다. 빈의 상태에 의존하지 않는 공유 기능을 제공하는 데 사용될 수 있습니다.

결론적으로, 싱글톤 빈은 여러 병렬 요청을 처리할 수 있지만, 빈의 상태를 인식하고, 공유 상태가 있는 경우 스레드 안전하게 설계되어 있는지 확인하는 것이 중요합니다.

<div class="content-ad"></div>

# Spring Framework 내부에서 사용된 디자인 패턴에 대해 알려주세요.

Spring Framework는 기능을 제공하기 위해 여러 디자인 패턴을 활용합니다. Spring에서 사용되는 주요 디자인 패턴 중 일부는 다음과 같습니다:

- 제어의 역전 (IoC): 이 패턴은 Spring Framework 전반에서 사용되어 응용 프로그램 코드를 프레임워크와 그 구성 요소로부터 분리합니다. IoC 컨테이너는 빈의 라이프사이클을 관리하고 그들 사이의 의존성을 주입하는 역할을 합니다.

- 싱글톤: Spring IoC 컨테이너 내에서 빈의 단일 인스턴스만 생성되도록 하기 위해 싱글톤 패턴이 사용됩니다. 싱글톤 패턴은 클래스의 단일 인스턴스를 생성하여 응용 프로그램 전체에서 공유하는 데 사용됩니다.

<div class="content-ad"></div>

- Factory: 팩토리 패턴은 Spring에서 구성에 기반한 다른 클래스의 객체를 생성하는 데 사용됩니다. Spring은 빈을 생성하기 위한 팩토리 메소드 디자인 패턴을 기반으로 한 팩토리 패턴을 제공합니다.

- Template Method: 템플릿 메소드 패턴은 Spring에서 다양한 유형의 작업에 대한 공통 구조를 제공하는 데 사용됩니다. Spring은 JdbcTemplate, Hibernate Template 등 공통 구조를 제공하는 여러 템플릿 클래스를 제공합니다.

- Decorator: 데코레이터 패턴은 기존 빈에 추가 기능을 추가하는 데 Spring에서 사용됩니다. Spring AOP (Aspect-Oriented Programming) 모듈은 데코레이터 패턴을 사용하여 프록시를 통해 기존 빈에 추가 기능을 추가합니다.

- Observer: 옵저버 패턴은 Spring에서 빈의 상태 변경을 다른 빈에 알리는 데 사용됩니다. Spring은 옵저버 패턴을 구현하는 데 사용할 수 있는 ApplicationEvent 및 ApplicationListener 인터페이스를 제공합니다.

<div class="content-ad"></div>

- Command: 명령(Command) 패턴은 Spring에서 특정 코드 조각의 실행을 명령 객체(command object)에 캡슐화하는 데 사용됩니다. 이 패턴은 Spring에서 재사용 가능하고 테스트 가능한 코드를 생성하는 데 사용됩니다.

- Facade: Facade(퍼사드) 패턴은 Spring에서 복잡한 시스템의 인터페이스를 간단하게 만드는 데 사용됩니다. Spring 프레임워크는 Facade 패턴을 사용하여 구성 요소와 상호 작용하는 간소화된 인터페이스를 제공합니다.

이것들은 Spring에서 사용되는 디자인 패턴의 몇 가지 예일 뿐이며, 더 많이 있습니다. Spring 프레임워크는 이러한 패턴을 활용하여 일관된 간단한 방식으로 응용 프로그램을 구축할 수 있도록 하여, 복잡한 시스템을 관리하기 쉽게 합니다.

# 스프링 프레임워크에서 공장(Factory) 디자인 패턴이 동작하는 방식은 무엇인가요?

<div class="content-ad"></div>

봄(Spring)에서는 구성에 따라 서로 다른 클래스의 객체를 생성하는 데 팩토리 디자인 패턴을 사용합니다. Spring IoC 컨테이너는 빈(beans)을 생성하기 위해 팩토리 패턴을 사용하는데, 이는 팩토리 메소드 디자인 패턴에 기반을 둡니다.

팩토리 메소드는 팩토리 인터페이스를 기반으로 다른 클래스의 객체를 생성하는 방법을 제공하는 디자인 패턴입니다. Spring에서 IoC 컨테이너는 팩토리로 작용하며, 팩토리 인터페이스는 BeanFactory 또는 ApplicationContext 인터페이스로 표현됩니다.

IoC 컨테이너는 빈을 생성하고 관리하는 역할을 담당합니다. 구성에서 빈을 정의할 때, IoC 컨테이너는 해당 빈의 인스턴스를 생성하기 위해 팩토리 패턴을 사용합니다. 그런 다음 IoC 컨테이너는 빈의 라이프사이클을 관리하며, 의존성 주입, 빈 초기화, 그리고 필요하지 않게 되면 빈을 소멸시킵니다.

다음은 봄(Spring)에서 팩토리 디자인 패턴을 사용하여 빈을 정의하는 예시입니다:

<div class="content-ad"></div>

```js
@Configuration
public class MyConfig {
@Bean
public MyService myService() {
return new MyService();
}
}
```

이 예에서 myService() 메서드는 @Bean으로 주석이 달려 있습니다. 이는 Spring에게 IoC 컨테이너가 생성될 때 MyService 클래스의 인스턴스를 만들도록 지시합니다. IoC 컨테이너는 팩토리 패턴을 사용하여 인스턴스를 생성하고 수명주기를 관리합니다.

Spring에서 프록시 디자인 패턴을 사용하는 또 다른 방법은 FactoryBean 인터페이스를 사용하는 것입니다. FactoryBean 인터페이스를 사용하면 팩토리 메서드에 의해 생성되는 bean을 생성할 수 있습니다. FactoryBean 인터페이스는 getObject()라는 하나의 메서드를 정의하며, 이 메서드는 Spring 애플리케이션 컨텍스트에서 빈으로 공개해야 하는 객체를 반환합니다.

Spring에서 프록시 디자인 패턴을 사용하는 방법에 대해 궁금해 하셨군요! 해당 내용에 대해 설명 드리겠습니다.

<div class="content-ad"></div>

프록시 디자인 패턴은 기존 객체에 추가 기능을 제공하기 위해 Spring에서 사용됩니다. Spring 프레임워크는 AOP(관점 지향 프로그래밍) 기능을 제공하기 위해 프록시 패턴을 사용하여, 로깅, 보안, 트랜잭션 관리와 같은 교차 관심 사항을 응용 프로그램에 모듈식이고 재사용 가능한 방식으로 추가할 수 있습니다.

Spring에서 AOP 프록시는 IoC 컨테이너에 의해 생성되며, 대상 빈에 대한 메서드 호출을 가로채도록 사용됩니다. 이를 통해 대상 빈으로의 메서드 호출 전 또는 후에 로깅이나 보안 검사와 같은 추가 동작을 추가할 수 있습니다.

AOP 프록시는 JDK 동적 프록시, CGLIB 프록시 또는 AspectJ 프록시 중 하나의 프록시 유형을 사용하여 생성됩니다.

JDK 동적 프록시: 이것은 Spring에서의 기본 프록시 유형이며, 인터페이스를 프록시화하는 데 사용됩니다.

<div class="content-ad"></div>

CGLIB 프록시: 이 프록시 유형은 클래스를 프록시함으로써 작동하며 대상 빈의 서브클래스를 생성함으로써 작동합니다.

AspectJ 프록시: 이 프록시 유형은 AspectJ 라이브러리를 사용하여 프록시를 생성하며 애플리케이션에서 AspectJ 포인트컷 및 어드바이스를 사용할 수 있습니다.

Spring은 AOP 기능을 제공하기 위해 프록시 패턴을 사용하여 대상 빈을 감싸는 프록시 객체를 생성합니다. 프록시 객체는 대상 빈으로 전달된 메서드 호출을 가로채고 대상 빈으로 메서드 호출이 이루어지기 전 또는 후에 추가 동작, 예를 들어 로깅이나 보안 확인을 호출합니다.

다음은 Spring AOP를 사용하여 빈에 로깅을 추가하는 예제입니다:

<div class="content-ad"></div>

```java
@Aspect
@Component
public class LoggingAspect {
@Before("execution(* com.example.service.*.*(..))")
public void logBefore(JoinPoint joinPoint) {
log.info("Started method: " + joinPoint.getSignature().getName());
}
}
```

이 예제에서 LoggingAspect 클래스는 @Aspect 및 @Component로 주석이 달려 있어 Spring 빈으로 만들어집니다. @Before 어노테이션이 사용되어 logBefore() 메서드가 대상 빈으로의 메서드 호출 전에 실행되어야 함을 지정합니다. logBefore() 메서드는 JoinPoint 인자를 사용하여 호출 중인 메서드의 이름을 기록합니다.

# 싱글톤 빈을 프로토타입 빈에서 호출하거나 그 반대로 프로토타입 빈을 싱글톤 빈에서 호출하면 몇 개의 객체가 반환됩니까?

싱글톤 빈이 프로토타입 빈 또는 그 반대로부터 호출될 때 동작은 의존성 주입 방식에 따라 다릅니다.



<div class="content-ad"></div>

싱글톤 빈이 프로토타입 빈에 주입되면, 프로토타입 빈이 생성될 때마다 싱글톤 빈의 동일한 인스턴스를 수신합니다. 이는 싱글톤 빈은 애플리케이션 컨텍스트 시작 시에 한 번만 생성되며, 이후에는 동일한 인스턴스가 프로토타입 빈에 주입되기 때문입니다.

반면에, 프로토타입 빈이 싱글톤 빈에 주입되면, 싱글톤 빈이 호출될 때마다 새로운 프로토타입 빈 인스턴스가 생성됩니다. 이는 프로토타입 빈이 컨테이너에 의해 관리되지 않고, 의존성이 주입될 때마다 새 인스턴스가 생성되기 때문입니다.

다음은 이를 설명하는 예시입니다:

```java
@Component
@Scope("singleton")
public class SingletonBean {
// 싱글톤 빈 코드
}
@Component
@Scope("prototype")
public class PrototypeBean {
@Autowired
private SingletonBean singletonBean;
// 프로토타입 빈 코드
}
```

<div class="content-ad"></div>

이 예제에서 프로토타입 빈이 생성되고 싱글톤 빈과 주입되면 각 생성마다 같은 싱글톤 빈의 인스턴스를 받게 됩니다. 그러나 싱글톤 빈이 생성되고 프로토타입 빈과 주입되면 각 호출마다 새로운 프로토타입 빈의 인스턴스를 받게 됩니다.

하나의 응용 프로그램 컨텍스트에서 싱글톤 및 프로토타입 스코프를 혼합하는 것은 예상치 못한 동작을 유발할 수 있으며 필요하지 않은 한 피하는 것이 좋습니다. 응용 프로그램 컨텍스트 전체에서 일관된 스코프를 사용하는 것이 가장 좋습니다.

# 스프링 부트 대 스프링을 선택하는 이유는?

다음은 스프링 프레임워크를 선택하는 몇 가지 이유입니다:

<div class="content-ad"></div>

- 애플리케이션에 포괄적인 기능과 기능 세트가 필요합니다.
- 필요한 컴포넌트만 선택하여 모듈식 애플리케이션을 구축하고 싶습니다.
- 애플리케이션에서 높은 유연성과 사용자 정의가 필요합니다.

Spring Boot를 선택해야 하는 몇 가지 이유가 있습니다:

<div class="content-ad"></div>

- 많은 구성을 하지 않고도 독립적인 Spring 애플리케이션을 빠르게 설정하고 싶을 때
- 미리 구성된 종속성과 합리적인 기본값을 활용하고 싶을 때
- 애플리케이션을 간편하게 자체 실행 가능한 실행 가능한 JAR 파일로 배포하고 싶을 때

전체적으로 Spring과 Spring Boot은 기업 수준의 애플리케이션을 구축하는 데 사용할 수 있는 강력한 프레임워크입니다. 둘 사이의 선택은 애플리케이션의 특정 요구 사항과 필요한 유연성 및 사용자 정의 수준에 따라 다를 수 있습니다.

<div class="content-ad"></div>

# Spring에서 RestTemplate이란 무엇인가요?

RestTemplate은 Spring에서 외부 REST API로 HTTP 요청을 보내는 강력한 도구입니다. 이는 낮은 수준의 HTTP 세부 사항에 대한 고수준 추상화를 제공하여 클라이언트 측 통신을 간단화합니다. 외부 서비스와 상호 작용하기 위한 편리한 스위스 아미 나이프로 상상해보세요.

주요 용도:

- 외부 RESTful API에서 데이터 소비하기

<div class="content-ad"></div>

- 내 애플리케이션 내부 마이크로서비스와 상호 작용하기
- RESTful API 테스트하기
- 외부 시스템과 사용자 정의 통합 구축하기

간단히 말해서: RestTemplate은 Spring에서 HTTP 요청을 보내는 번거로움을 덜어주며, 애플리케이션을 외부 세계와 연결하는 편리하고 유연한 방법을 제공합니다.

<div class="content-ad"></div>

# 스프링과 스프링 부트에서 사용 가능한 모든 HTTP 클라이언트는 무엇인가요?

다음은 사용 가능한 다양한 클라이언트들입니다:

- RestTemplate
- WebClient
- HttpClient
- RestClient
- OkHttp

# 스프링 REST에서 HttpMessageConverter란 무엇인가요?

<div class="content-ad"></div>

HTTP 요청과 응답을 Java 객체와 그에 해당하는 메시지 형식 (예: JSON, XML) 간의 변환을 처리하는 중요 인터페이스에요.

이는 컨트롤러 계층과 메시지 페이로드 간의 다리 역할을 하며, 데이터 호환성을 보장해요.

작동 방식:

수신 요청 처리:

<div class="content-ad"></div>

- 요청이 도착하면 Spring은 요청의 Content-Type 헤더를 기반으로 적합한 HttpMessageConverter를 찾습니다.

- 일치하는 항목을 찾으면 컨버터가 요청 본문을 읽고 컨트롤러가 처리할 수 있는 Java 객체로 변환합니다.

응답 처리:

- 컨트롤러가 객체를 반환할 때 Spring은 다시 요청의 Accept 헤더나 기본 변환기를 기반으로 적절한 HttpMessageConverter를 찾습니다.

<div class="content-ad"></div>

**테이블 태그를 Markdown 형식으로 변경해주세요.**

<div class="content-ad"></div>


- FormHttpMessageConverter (폼 데이터용)
- ByteArrayHttpMessageConverter (바이너리 데이터용)
- Jaxb2RootElementHttpMessageConverter (XML용, JAXB 사용)

# Spring MVC를 사용하여 RESTful 웹 서비스를 소비하는 방법?


<div class="content-ad"></div>

1. RestTemplate 주입하기:

RestTemplate 인스턴스를 가져와서 HTTP 요청을 하는 스프링의 중심 클래스입니다.

의존성 주입을 사용하여 컨트롤러나 서비스 클래스에 주입하세요.

2. HTTP 요청 만들기:

<div class="content-ad"></div>

다양한 HTTP 작업에 RestTemplate 메서드를 사용해보세요:

getForObject(url, responseType): GET 요청에 대한 데이터를 가져옵니다.

postForObject(url, requestBody, responseType): POST 요청에 데이터를 보냅니다.

put(url, requestBody): PUT 요청을 사용하여 데이터를 업데이트합니다.

<div class="content-ad"></div>

3. 응답 데이터 매핑:

RestTemplate은 기대되는 응답 유형에 따라 응답 본문을 자동으로 Java 객체로 변환합니다.

<div class="content-ad"></div>

```js
@RestController
public class MyController {
    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/fetch-data")
    public User fetchUserData() {
        String url = "https://api.example.com/users/123";
        User user = restTemplate.getForObject(url, User.class);
        return user;
    }
}
```

# Thanks for reading

<div class="content-ad"></div>

- 👏 이야기에 박수를 보내주시고 저를 팔로우해주세요 👉
- 📰 제 미디엄에서 더 많은 콘텐츠를 읽어보세요 (Java 개발자 인터뷰에 관한 50개의 이야기)

여기서 제 책을 찾을 수 있어요:

- Amazon에서 여기서 Guide To Clear Java Developer Interview(킨들북)와 Gumroad(PDF 형식)에서.
- Gumroad(PDF 형식)에서와 Amazon(킨들 이북)에서 Guide To Clear Spring-Boot Microservice Interview.
- 🔔 팔로우: LinkedIn | Twitter | Youtube
