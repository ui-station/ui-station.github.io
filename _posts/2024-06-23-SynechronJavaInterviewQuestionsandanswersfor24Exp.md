---
title: "시네크론 자바 인터뷰 질문 및 답변 경력 2-4년"
description: ""
coverImage: "/assets/img/2024-06-23-SynechronJavaInterviewQuestionsandanswersfor24Exp_0.png"
date: 2024-06-23 20:27
ogImage:
  url: /assets/img/2024-06-23-SynechronJavaInterviewQuestionsandanswersfor24Exp_0.png
tag: Tech
originalTitle: "Synechron Java Interview Questions and answers for( 2–4 Exp)"
link: "https://medium.com/@rathod-ajay/synechron-java-interview-questions-and-answers-for-2-4-exp-b1dfb2766d0c"
---

"안녕하세요 여러분, 이 글에서는 Synechron 기술 회사에서 Java 개발자 직무를 위한 인터뷰 질문과 답변을 공유하겠습니다. 함께 알아보세요."

![이미지](/assets/img/2024-06-23-SynechronJavaInterviewQuestionsandanswersfor24Exp_0.png)

Java의 Stream API는 Java 8에서 도입된 기능입니다. 이는 Stream이라는 새로운 추상화를 제공하여 데이터를 선언적인 방식으로 처리할 수 있게 합니다. Stream API는 객체 컬렉션을 처리하는 데 사용되며, 복잡한 작업을 일련의 단계로 수행할 수 있도록 합니다.

Java의 Stream은 일련의 요소를 지원하며 순차적 및 병렬 집계 작업을 지원합니다. 이러한 작업에는 데이터 필터링, 매핑, 정렬 또는 기타 데이터 조작이 포함될 수 있습니다."

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

여기 내부적으로 작동 방식을 설명합니다:

- 생성: 스트림은 배열, 컬렉션 또는 I/O 채널과 같은 소스에서 생성됩니다. 소스는 스트림 객체로 래핑됩니다.
- 구성: 스트림 객체는 일련의 변환 작업으로 구성됩니다. 이러한 변환 작업은 즉시 적용되지 않고 대신 작업 파이프라인으로 저장됩니다. 각 작업은 스트림을 입력으로 사용하고 스트림을 출력으로 생성하는 함수입니다.
- 실행: 스트림 객체에서 collect, reduce, forEach 등과 같은 최종 작업이 호출되면 처리가 시작됩니다. 데이터는 파이프라인의 모든 작업을 통해 한 번에 하나씩 파이프로 이동합니다. 작업은 "게으른(lazy)" 방식으로 처리되며 필요할 때만 실행됩니다.
- 사용: 스트림이 사용되고 다시 사용할 수 없습니다. 데이터를 다시 탐색해야 하는 경우 새로운 스트림을 생성해야 합니다.

```java
List<String> names = Arrays.asList("John", "Jane", "Adam", "Eve");
List<String> uppercaseNames = names.stream()  // 스트림 생성
    .filter(name -> name.startsWith("A"))      // "A"로 시작하는 이름 필터링
    .map(String::toUpperCase)                  // 대문자로 변환
    .collect(Collectors.toList());             // 결과를 리스트로 수집
```

이 예제에서는 스트림 API를 사용하여 이름 목록을 필터링하고 대문자로 변환하는 방법을 보여줍니다. filter와 map 메서드는 중간 작업으로 스트림을 구성하며, collect 메서드는 처리를 트리거하고 결과를 새로운 리스트로 수집하는 최종 작업입니다.

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

SOLID은 소프트웨어 시스템을 설계할 때 유지보수 및 확장이 용이한 다섯 가지 원칙을 나타내는 약어입니다. 다음은 예와 함께 나열되어 있어요:

- SRP(Single Responsibility Principle): 한 클래스는 변경되는 이유가 하나여야 합니다. 이것은 클래스가 한 가지 작업 또는 책임만 가지도록 해야 한다는 의미입니다.
- 예시: User 클래스가 있을 때, 해당 클래스는 사용자와 직접 관련된 것만 책임져야 합니다. 예를 들어 데이터베이스에서 사용자 데이터를 로드하는 것은 해당 클래스가 담당하면 안됩니다. 별도의 UserRepository 클래스가 해당 책임을 져야 합니다.
- OCP(Open-Closed Principle): 소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장에 열려 있어야 하지만 수정에는 닫혀 있어야 합니다. 이것은 기존 코드를 수정하지 않고 새로운 기능을 추가할 수 있어야 한다는 것을 의미합니다.
- 예시: Shape 클래스가 있고 새로운 모양을 추가하려는 경우, Shape를 상속하는 새 클래스를 추가함으로써 Shape 클래스나 기존 클래스를 수정하지 않고도 가능해야 합니다.
- LSP(Liskov Substitution Principle): 하위 유형은 상위 유형으로 대체 가능해야 합니다. 이것은 프로그램이 기본 클래스를 사용하고 있을 때 하위 클래스를 사용할 수 있어야 하며, 프로그램이 오류 없이 작동해야 한다는 것을 의미합니다.
- 예시: Bird 클래스에 fly() 메서드가 있는 경우 Bird를 상속하는 Penguin 클래스는 fly() 메서드를 지원하지 않는다면 LSP를 위반하고 있습니다. 해결책으로는 비행하는 새들을 위한 별도의 FlyingBird 클래스를 만드는 것이 될 수 있습니다.
- ISP(Interface Segregation Principle): 클라이언트는 사용하지 않는 인터페이스에 의존하도록 강요되어서는 안 됩니다. 이는 클래스가 사용하지 않는 메서드를 구현할 필요가 없어야 한다는 것을 의미합니다.
- 예시: IPrinter 인터페이스에 print(), fax(), scan() 메서드가 있고, SimplePrinter 클래스가 인쇄만 지원하는 경우 ISP를 위반하고 있습니다. 해결책으로는 IPrinter, IFax, IScanner에 대한 별도의 인터페이스를 만드는 것이 될 수 있습니다.
- DIP(Dependency Inversion Principle): 고수준 모듈은 저수준 모듈에 의존해서는 안 됩니다. 둘 다 추상화에 의존해야 합니다. 이는 구현이 아니라 추상화에 의존해야 한다는 의미입니다.
- 예시: NotificationService 클래스가 직접 EmailService 클래스를 사용하여 이메일을 보내는 경우 DIP를 위반하고 있습니다. 해결책으로는 EmailService가 구현하는 IMessageService 인터페이스를 사용하고, NotificationService는 EmailService보다는 IMessageService에 의존해야 합니다.

이 원칙들은 지침이며 엄격한 규칙은 아닙니다. 다른 목표를 달성하기 위해 원칙을 위반하는 것이 합리적인 경우도 있을 수 있습니다. 그러나 이러한 원칙을 이해하면 더 유지보수 가능하고 확장 가능한 소프트웨어를 설계하는 데 도움이 될 거예요.

Java 8에서는 특히 java.util.function, java.util.stream, java.time 패키지에서 새로운 클래스와 인터페이스가 소개되었습니다. 여기에 중요한 것들이 몇 가지 있습니다:

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

- java.util.function 패키지의 함수형 인터페이스:

  - Predicate`T`
  - Function`T,R`
  - Consumer`T`
  - Supplier`T`
  - UnaryOperator`T`
  - BinaryOperator`T`

- java.util.stream 패키지의 Stream API 클래스 및 인터페이스:

  - Stream`T`
  - IntStream
  - LongStream
  - DoubleStream
  - Collector`T,A,R`
  - Collectors

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

java.time 패키지의 Date and Time API 클래스:

- LocalDate
- LocalTime
- LocalDateTime
- ZonedDateTime
- Period
- Duration
- Instant
- ZoneId

java.util 패키지의 Optional 클래스:

- Optional`T`

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

jdk.nashorn.api.scripting 패키지에 새로운 JavaScript 엔진 클래스가 추가되었습니다:

- NashornScriptEngineFactory

이러한 새로운 클래스와 인터페이스는 함수형 프로그래밍, 스트림 처리 및 개선된 날짜 및 시간 API와 같은 강력한 새로운 기능을 제공합니다.

wait() 및 join()은 자바의 멀티스레딩에서 사용되는 두 가지 메서드로, 각각 다른 목적을 제공합니다:

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

- wait(): 이 메서드는 Object 클래스에 정의되어 있으며 스레드 간 통신에 사용됩니다. 스레드가 wait()를 호출하면 휴면 상태로 들어가며 개체에 보유한 락을 해제하여 다른 스레드가 락을 가져가고 고유한 중요한 부분을 실행할 수 있도록 합니다. 해당 스레드는 같은 개체에 대해 notify() 또는 notifyAll()이 호출될 때까지 대기 상태에 머무릅니다.

예시:

```js
synchronized(object) {
    while(<조건이 참이 아닌 동안>)
        object.wait();
    // 로직 처리
}
```

join(): 이 메서드는 Thread 클래스에 정의되어 있으며 현재 스레드를 지정된 스레드가 종료될 때까지 일시 정지시키는 데 사용됩니다. 즉, 스레드 A가 스레드 B에 대해 join()을 호출하면 A는 B가 실행을 완료할 때까지 기다립니다.

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

예시:

```java
Thread t1 = new Thread(new MyRunnable());
t1.start();
try {
    t1.join();
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
// t1이 종료될 때까지 logic 계속 진행
```

요약하면, wait()은 스레드 간 통신에 사용되며 객체의 잠금을 해제하며, join()은 스레드가 종료될 때까지 기다리는 데 사용되며 어떤 잠금도 해제하지 않습니다.

만약 해당 객체에 대해 동기화를 먼저 실행하지 않고 객체에 대해 wait()를 호출하는 경우, 런타임에 IllegalMonitorStateException이 발생합니다. 이는 wait(), notify(), notifyAll() 메서드가 동기화를 달성하도록 설계되어 다중 스레드 환경에서 사용되며, synchronized 컨텍스트 내에서만 호출할 수 있기 때문입니다.

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

요기에는 Java 문서에 대한 wait() 메서드 설명이 있어요:

현재 스레드는 이 객체의 모니터를 소유해야 합니다. 스레드는 이 모니터의 소유권을 해제하고 이 객체의 모니터에서 대기 중인 스레드를 깨우기 위해 notify() 메서드 또는 notifyAll() 메서드를 호출하여 다른 스레드에 의해 깨어날 때까지 기다립니다. 그럼 스레드는 다시 모니터의 소유권을 재획득할 때까지 대기하고 실행을 다시 시작합니다.

따라서 객체에 대해 작동 중인 4개의 스레드가 있고 synchronized 블록이나 메서드 없이 wait()를 호출하면 IllegalMonitorStateException이 발생합니다.

또한, notify() 또는 notifyAll()을 호출하지 않는 경우, 객체에서 대기 중인 스레드는 깨우기 위한 통지할 것이 없기 때문에 대기 상태에 계속 남게 됩니다. 이는 스레드가 영원히 대기하는 상황을 초래할 수 있으며, 이를 스레드 기아라고 일컫는 경우가 많습니다.

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

Spring Framework과 Spring Boot은 모두 엔터프라이즈급 애플리케이션을 구축하는 데 사용되는 Java 프레임워크입니다. 두 프레임워크는 관련이 있지만 다른 목적을 가지고 있습니다.

Spring Framework: 이는 Java 애플리케이션을 구축하는 데 사용되는 포괄적인 프레임워크입니다. 의존성 주입, 관점 지향 프로그래밍, 데이터 액세스, 트랜잭션 관리, 웹 애플리케이션을 위한 MVC 등 다양한 기능을 제공합니다. 그러나 Spring Framework를 사용하여 프로젝트를 설정하는 것은 많은 구성을 수동으로 처리해야 하기 때문에 복잡할 수 있습니다.

Spring Boot: 이는 Spring Framework 위에 구축된 프로젝트입니다. Spring 애플리케이션의 설정 및 개발을 단순화시켜 코드 구성에 대한 기본값을 제공하고, Spring 애플리케이션을 실행시키기 위해 필요한 노력을 줄입니다. 또한 스탠드얼론 서버 (Tomcat, Jetty 또는 Undertow)를 제공하여 Spring 애플리케이션을 직접 메인 메서드에서 실행할 수 있고, 애플리케이션 배포 및 모니터링을 단순화하기 위한 많은 추가 기능을 제공합니다.

이것이 주요한 차이점입니다:

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

- 설정: 스프링은 설정을 위해 많은 수동 구성이 필요합니다. 그러나 스프링 부트는 이 복잡성을 줄이기 위해 자동 구성을 제공합니다.
- 프로젝트 설정: 스프링에서 프로젝트를 설정하는 것은 약간 복잡할 수 있으며 내부 동작에 대한 깊은 이해가 필요합니다. 스프링 부트는 Spring Initializr 웹 기반 프로젝트 생성기를 통해 이 과정을 쉽게 만듭니다.
- 독립형: 스프링 부트 애플리케이션은 내장 서버가 포함된 jar로 패키징할 수 있습니다. 기존의 스프링 MVC 애플리케이션에서는 애플리케이션을 별도 서버에 배포해야 합니다.
- 의존성 관리: 스프링 부트는 starter POMs 기능을 제공하여 Maven 구성 및 의존성 관리를 간소화합니다.
- 프로덕션 준비: 스프링 부트는 상자에서 건강 상태 확인 및 메트릭과 같은 기능을 기본 제공하여 프로덕션 환경에 적합합니다.

만약 Employee 클래스의 hashCode() 메서드가 항상 같은 값을 반환한다면, 데이터를 잃거나 잘못된 결과를 얻는 것은 아닙니다. 그러나 해시 기반의 컬렉션인 HashMap, HashSet 등의 성능이 저하될 수 있습니다.

이것이 그 이유입니다:

예를 들어 HashMap에서 hashCode()는 키-값 쌍을 저장해야 하는 버킷을 결정하는 데 사용됩니다. 만약 모든 키에 동일한 해시 코드가 있는 경우, 모두 동일한 버킷에 들어가게 됩니다. 이로 인해 HashMap은 성능 측면에서 연결 목록으로 변하게 되며, 모든 항목이 해당 단일 버킷의 노드 체인으로 저장됩니다. 이는 모든 조회, 삽입 또는 삭제가 모든 항목을 선형 검색해야 하므로 매우 비효율적입니다.

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

객체를 HashMap의 키로 사용할 때는 hashCode()와 equals() 메서드를 올바르게 재정의해야 합니다. hashCode() 메서드는 키를 버킷에 고르게 분배하여 성능을 향상시키는 데 중요하며, equals()는 키의 동등성을 결정하는 데 사용됩니다.

따라서 동일한 해시코드를 반환하더라도 데이터 손실이나 잘못된 결과는 발생하지 않지만, 해시 기반 컬렉션을 사용할 때 성능이 저하될 수 있습니다.

애플리케이션의 성능을 최적화하는 데 몇 가지 단계가 포함됩니다:

- 프로파일링: 애플리케이션의 병목 현상을 식별합니다. CPU 사용량, 메모리 누수, 느린 데이터베이스 쿼리 등이 포함될 수 있습니다. VisualVM, JProfiler 또는 YourKit과 같은 도구를 사용하여 Java 애플리케이션을 프로파일링할 수 있습니다.
- 코드 최적화: 비효율적인 코드를 찾아 최적화합니다. 이는 더 효율적인 데이터 구조 사용, 알고리즘의 시간 복잡도 감소, 데이터베이스 호출 감소 등을 포함할 수 있습니다.
- 동시성: 적절한 곳에서 멀티스레딩을 사용하여 여러 코어를 활용하고 작업을 병렬로 수행합니다.
- 캐싱: 비싼 작업의 결과를 저장하기 위해 캐싱을 구현하여 같은 입력이 다시 발생할 때 반복을 피합니다.
- 데이터베이스 최적화: 데이터베이스 쿼리를 최적화하고 인덱싱을 사용하며 필요에 따라 정규화 또는 비정규화를 수행합니다.
- 최신 라이브러리/프레임워크 사용: 일반적으로 성능 향상이 있는 최신 라이브러리나 프레임워크를 사용하도록 항상 노력해야 합니다.

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

쓰레드 덤프를 가져오려면:

- jstack: 이것은 JDK와 함께 제공되는 명령줄 유틸리티입니다. 실행 중인 Java 애플리케이션의 쓰레드 덤프를 가져 오는 데 사용할 수 있습니다. 명령어는 jstack `pid`이며 여기서 pid는 Java 애플리케이션의 프로세스 ID입니다.
- VisualVM: 이것은 실행 중인 Java 애플리케이션을 모니터링하는 데 사용할 수있는 그래픽 도구입니다. 또한 쓰레드 덤프를 가져 오는 데도 사용할 수 있습니다.
- JConsole: 이것은 JDK와 함께 제공되는 또 다른 그래픽 도구입니다. 실행 중인 Java 애플리케이션을 모니터링하고 쓰레드 덤프를 가져 오는 데 사용할 수 있습니다.

Java 8에서 "스트림"이라는 용어는 일반적으로 java.util.stream 패키지에 소개 된 새로운 추상화를 가리킵니다. 이를 사용하여 요소 스트림에 대해 함수 스타일 작업을 수행할 수 있습니다. Stream API는 객체 컬렉션을 처리하는 데 사용됩니다. 스트림은 다양한 메서드를 지원하는 객체 시퀀스로, 원하는 결과를 생성하는 데 파이프라인으로 연결될 수 있습니다.

Java 8에서 사용할 수있는 세 가지 유형의 스트림이 있습니다:

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

- Sequential Stream: 순차 스트림은 단일 파이프라인을 갖고 있으며 요소를 순차적으로만 처리할 수 있습니다. stream() 메서드를 호출할 때 기본적으로 생성됩니다.

```java
List<String> list = Arrays.asList("A", "B", "C");

Stream<String> stream = list.stream();
```

- Parallel Stream: 병렬 스트림은 여러 파이프라인을 가지고 있어 요소를 병렬로 처리할 수 있습니다. 멀티코어 머신에서 대용량 데이터셋에 대해 순차 스트림보다 빠를 수 있습니다. parallelStream() 메서드를 호출할 때 생성됩니다.

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
List<String> list = Arrays.asList("A", "B", "C");

Stream<String> parallelStream = list.parallelStream();

Infinite Stream: These are streams that don’t have a fixed size, as in they can keep on growing. The Stream.iterate and Stream.generate methods can be used to create infinite streams.

Stream<Integer> infiniteStream = Stream.iterate(0, i -> i + 2);
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

각 유형의 스트림은 다른 종류의 작업을 위해 설계되었습니다. 연속 및 병렬 스트림은 일반적으로 유한한 수의 요소와 함께 사용되며, 무한 스트림은 필요할 때 값 시퀀스를 생성하는 데 사용됩니다.

만약 t가 현재 실행 중인 스레드를 가진 Thread 객체라면, t.join()은 현재 스레드가 t의 스레드가 종료될 때까지 실행을 일시 중지시킵니다.

다음은 간단한 예제입니다:

```js
Thread t1 = new Thread(new MyRunnable());
t1.start();
try {
t1.join(); // 현재 스레드는 t1이 실행을 완료할 때까지 기다립니다
} catch (InterruptedException e) {
Thread.currentThread().interrupt();
}
// t1의 실행이 완료된 후 로직 계속 수행
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

이 예에서 현재 스레드는 t1이 실행을 마칠 때까지 기다립니다. 이는 다른 스레드를 시작하여 일부 작업을 수행하고 그 결과가 필요한 경우에 유용할 수 있습니다.

또한 join()에 대한 오버로드된 버전이 있으며, 다른 스레드가 작업을 완료할 때까지 기다리기 원하는 최대 시간을 지정할 수 있습니다:

- join(long millis) 이 스레드가 종료될 때까지 millis 밀리초만큼 기다립니다.
- join(long millis, int nanos) 이 스레드가 종료될 때까지 millis 밀리초와 nanos 나노초만큼 최대 기다립니다.

만약 지정된 시간 내에 스레드가 작업을 완료하지 않으면, join() 호출은 어쨌든 반환되고 현재 스레드는 실행을 계속합니다.

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

자바 7에서 소개된 Objects 클래스는 객체를 다루는 정적 메서드를 제공하는 유틸리티 클래스입니다. 이 유틸리티에는 객체의 해시 코드를 계산하는 데 사용되는 null-안전 혹은 null-허용 메서드, 객체에 대한 문자열을 반환하는 메서드, 두 객체를 비교하는 메서드 등이 포함되어 있습니다.

다음은 Objects 클래스에서 자주 사용되는 메서드 몇 가지입니다:

equals(Object a, Object b): 두 개체가 equals() 메서드에 따라 동일한지 확인합니다. 이 메서드는 null-안전입니다. 즉, 두 개체가 모두 null이면 true를 반환하고, 하나는 null이면 false를 반환합니다.

Objects.equals(“test”, new String(“test”)); // true를 반환

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
Objects.equals(null, "test"); // false 반환

Objects.equals(null, null); // true 반환

hashCode(Object o): null이 아닌 인수의 해시 코드를 반환하고, null 인수의 경우 0을 반환합니다. null 안전 해시 코드 계산에 유용합니다.

Objects.hashCode(null); // 0 반환
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

```java
Objects.hashCode("test"); // 문자열 "test"의 해시 코드를 반환합니다.

toString(Object o): null이 아닌 인수에 대해 toString을 호출한 결과를 반환하고, null 인수에 대해 "null"을 반환합니다.

Objects.toString(null); // "null"을 반환합니다.

Objects.toString("test"); // "test"를 반환합니다.
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

requireNonNull(T obj): 지정된 객체 참조가 null이 아닌지 확인합니다. 이 방법은 주로 메서드 및 생성자에서 매개변수 유효성을 검사하기 위해 설계되었습니다.

Objects.requireNonNull(null); // NullPointerException 발생

Objects.requireNonNull("test"); // "test" 반환

compare(T a, T b, Comparator`? super T` c): 지정된 Comparator로 두 객체를 비교하며, null에 안전합니다.

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

`Objects` 클래스는 null-안전한 메소드를 제공하여 더 깨끗하고 견고한 코드를 작성하는 데 도움이 됩니다.

`flat` 및 `flatMap`은 데이터의 컬렉션이나 스트림에서 사용되는 연산입니다. 그들 사이의 차이점은 중첩 구조물을 어떻게 처리하는지에 있습니다.

- Flat: `flat` 연산(Java에는 없지만 다른 언어에는 존재)은 일반적으로 여러 단계로 중첩되어 있는 구조를 하나의 단계로 줄이는 작업을 합니다. 구조를 한 단계 평준화합니다. 예를 들어, 리스트의 리스트가 있다면, `flat` 연산은 모든 내부 리스트 요소를 포함하는 단일 리스트를 제공합니다.
- FlatMap: `flatMap` 연산은 `map` 및 `flat` 연산의 조합입니다. 먼저 구조 안의 각 요소에 함수를 적용하고 결과를 평준화합니다. 이는 각 요소에 적용할 함수가 컬렉션이나 스트림을 생성할 때 유용합니다.

여기 Java에서의 예시입니다:

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
List<String> list = Arrays.asList("Hello World", "Java Stream");
// map 작업
List<String[]> mapResult = list.stream()
.map(s -> s.split(" "))
.collect(Collectors.toList());
// flatMap 작업
List<String> flatMapResult = list.stream()
.flatMap(s -> Arrays.stream(s.split(" ")))
.collect(Collectors.toList());
```

이 예제에서 map 작업은 각 문자열을 단어 배열로 분할하여 List`String[]`을 생성합니다. flatMap 작업은 각 문자열을 단어로 분할하지만 결과를 List`String`으로 평평하게 만들어 각 단어가 개별 요소로 있는 List를 생성합니다.

따라서 flatMap의 주요 차이점은 스트림의 각 요소를 여러 요소로 변환하거나(또는 전혀 변환하지 않는 상황) 평면적인 결과 스트림으로 종료하고 중첩 구조가 아닌 결과 내용을 얻고 싶을 때 사용할 수 있다는 것입니다.

자바에서 java.util.concurrent 패키지는 멀티스레딩에 유용한 여러 유형의 블로킹 큐를 제공합니다. 블로킹 큐는 큐가 비어 있고 Dequeue하려고 하면 블로킹되거나, 큐가 이미 가득 차있고 Enqueue하려고 하면 블로킹되는 큐입니다.

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

자바에서 사용되는 블로킹 큐의 종류는 다음과 같아요:

- ArrayBlockingQueue: 배열을 기반으로 하는 유계된 블로킹 큐이에요. 이 큐는 FIFO(먼저 들어온 것이 먼저 나가는) 순서로 요소들을 처리해요. '유계된'이란 큐가 고정된 수의 요소를 초과해서 저장할 수 없다는 것을 의미해요.

- LinkedBlockingQueue: 링크드 노드를 기반으로 하는 선택적으로 유계된 블로킹 큐예요. 선택적 용량 제한 생성자 인수는 큐가 너무 많이 확장되는 것을 방지하기 위한 방법으로 사용돼요. 용량 제한이 없으면 연결된 큐는 일반적으로 배열 기반 큐보다 더 많은 요소를 보관해요.

- PriorityBlockingQueue: PriorityQueue 클래스와 동일한 규칙을 사용하며 블로킹 검색 작업을 지원하는 무제한 블로킹 큐에요. 이 큐는 논리적으로는 무제한이지만 리소스 고갈로 인해 추가가 실패할 수도 있어요.

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

DelayQueue: 지연된 요소들의 무한 대기 큐로, 요소의 지연이 만료되었을 때에만 요소를 가져올 수 있습니다.

SynchronousQueue: 각 삽입 작업은 다른 스레드에 의한 해당 제거 작업을 기다려야 하며, 그 반대도 마찬가지입니다. 동기식 큐는 내부 용량을 가지지 않으며, 하나의 용량조차도 없습니다.

LinkedTransferQueue: 링크된 노드를 기반으로 한 무한 TransferQueue입니다. 이 큐는 어떤 생성자에 대해선 FIFO(먼저 들어온 것이 먼저 나가는) 순서로 요소를 정렬합니다.

LinkedBlockingDeque: 링크된 노드를 기반으로 한 선택적으로 유한한 블로킹 덱입니다.

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

각 종류의 블로킹 큐는 다중 스레드 프로그래밍에서 작업 요구 사항에 따라 고유한 사용 사례를 갖습니다.

# 읽어 주셔서 감사합니다

- 👏 이야기에 박수를 보내고 저를 팔로우해주세요 👉
- 📰 미디엄에서 더 많은 콘텐츠를 읽어보세요 (자바 개발자 인터뷰에 관한 60편의 이야기)

제 책은 여기서 찾아볼 수 있습니다:

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

- 아마존에서 이 책을 구입하여 기본 Java 개발자 인터뷰를 준비하세요 (킨들북).
- Gumroad에서 PDF 형식으로 Spring-Boot Microservice 인터뷰를 준비하는 방법 안내서를 확인하세요.
- Gumroad (PDF 형식) 및 아마존 (킨들 eBook)에서 Spring-Boot Microservice 인터뷰를 준비하는 방법 안내서를 확인하세요.
- 🔔 저를 팔로우하세요: LinkedIn | Twitter | Youtube
