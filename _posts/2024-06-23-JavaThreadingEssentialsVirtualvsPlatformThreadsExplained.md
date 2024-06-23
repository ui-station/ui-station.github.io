---
title: "자바 스레딩 필수 버추얼 스레드와 플랫폼 스레드 비교 분석"
description: ""
coverImage: "/assets/img/2024-06-23-JavaThreadingEssentialsVirtualvsPlatformThreadsExplained_0.png"
date: 2024-06-23 20:34
ogImage: 
  url: /assets/img/2024-06-23-JavaThreadingEssentialsVirtualvsPlatformThreadsExplained_0.png
tag: Tech
originalTitle: "Java Threading Essentials: Virtual vs. Platform Threads Explained"
link: "https://medium.com/stackademic/java-threading-essentials-virtual-vs-platform-threads-explained-32365d8f92be"
---


이 기사에서는 쓰레드와 "가상 쓰레드"라고 불리는 최근 기능에 대해 알아볼 것입니다. 이 플랫폼 쓰레드와 가상 쓰레드가 어떻게 성격이 다르며 어떻게 애플리케이션의 성능 향상에 기여하는지에 대해 알아보겠습니다.

![이미지](/assets/img/2024-06-23-JavaThreadingEssentialsVirtualvsPlatformThreadsExplained_0.png)

## 클래식 쓰레드 배경:

외부 API를 호출하거나 데이터베이스 상호작용과 같은 시나리오를 살펴보고 실행 중인 쓰레드 수명주기를 살펴보겠습니다.

<div class="content-ad"></div>

- 메모리에 생성되고 서비스를 제공할 수 있는 스레드입니다.
- 요청이 접근하면 스레드 중 하나에 매핑되어 외부 API를 호출하거나 데이터베이스 쿼리를 실행합니다.
- 스레드는 서비스나 데이터베이스로부터 응답을 받을 때까지 대기합니다.
- 응답을 받자마자 후속 활동을 실행한 후 풀로 다시 반환합니다.

![이미지](/assets/img/2024-06-23-JavaThreadingEssentialsVirtualvsPlatformThreadsExplained_1.png)

위 생명주기에서 스레드가 아무것도 하는 대기 단계인 단계 3을 관찰하십시오. 이것은 주로 대기하여 시스템 리소스를 비효율적으로 사용하므로 단점이며 수명 주기 중 대부분의 스레드가 응답을 기다리며 아무것도 하지 않습니다.

Java 19 이전에는 스레드를 생성하는 표준 방법 또는 존재하는 스레드가 Native Threads 또는 Platform Threads로 불립니다. 이 아키텍쳐 스타일에서 Platform 스레드와 OS 스레드 사이에 1:1 매핑이 있습니다. 이는 운영 체제 스레드가 완료될때까지 기다리기만 하고 아무 것도하지 않아서 미사용 상태이며 이를 무겁고 비싸게 만듭니다.

<div class="content-ad"></div>

## 가상 스레드:

자바의 가상 스레드는 자바가 동시성 및 멀티스레딩을 다루는 방식에서 중대한 발전을 나타냅니다. 오라클의 프로젝트 룸(Projext Loom)의 일환으로 소개된 가상 스레드는 고성능의 동시 애플리케이션을 작성, 유지 및 관찰하기 위한 도전에 대응하기 위한 이니셔티브로, 개발자가 동시성을 더 쉽게 다룰 수 있도록 설계되었습니다.

가상 스레드는 운영 체제가 아닌 Java 가상 머신(JVM)에 의해 관리되는 가볍고 자원소모가 적은 스레드입니다. 플랫폼 스레드와는 달리 가상 스레드는 쉽게 생성 및 해제할 수 있습니다. 그들은 적은 수의 플랫폼 스레드에 매핑되어 수천 개 또는 수백만 개의 작업을 더 적은 자원으로 동시에 처리할 수 있게 합니다.

![가상 vs 플랫폼 스레드](/assets/img/2024-06-23-JavaThreadingEssentialsVirtualvsPlatformThreadsExplained_2.png)

<div class="content-ad"></div>

## 가상 스레드의 장점 Platform 스레드 대비

자바에서 가상 스레드의 장점은 많습니다. 먼저, 가상 스레드를 통해 시스템 자원을 더 효율적으로 사용할 수 있습니다. 가상 스레드는 가벼워서 플랫폼 스레드보다 적은 메모리와 CPU 자원을 소비합니다. 이 효율성으로 인해 동시성의 정도를 높일 수 있어 하나의 JVM에서 많은 수의 동시 작업을 실행할 수 있습니다.

둘째, 가상 스레드는 자바에서 동시 프로그래밍을 간단하게 만듭니다. 개발자들은 비동기 프로그래밍 모델의 복잡성을 다루지 않고 동기식 코드를 작성하는 것과 유사한 간단하고 명령형 스타일로 코드를 작성할 수 있습니다. 이 간소화를 통해 교착상태나 경쟁 조건과 같은 일반적인 동시성 관련 버그의 발생 가능성을 줄일 수 있습니다.

게다가, 가상 스레드는 CPU 이용률을 향상시켜줍니다. 기존의 스레드 모델에서는 많은 수의 스레드 사이의 관리와 컨텍스트 스위칭으로 많은 CPU 시간이 소비될 수 있습니다. 가상 스레드는 컨텍스트 스위칭과 관련된 오버헤드를 줄여 동시 작업을 효율적으로 실행할 수 있도록 합니다.

<div class="content-ad"></div>

## 실용적인 방법:

만약 우리가 일을 완료하기 위해 클래식 플랫폼 스레드를 생성하고 싶다면, 아래와 같이 할 수 있습니다. PlatformThreadDemo.java 파일을 만들고 아래 내용을 복사하세요.

```js
package org.vaslabs;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class PlatformThreadDemo {
    private static final Logger logger = LoggerFactory.getLogger(PlatformThreadDemo.class);

    public static void main(String[] args) {
        attendMeeting().start();
        completeLunch().start();
    }

    private static Thread attendMeeting(){
        var message = "Platform Thread [Attend Meeting]";
        return new Thread(() -> {
            logger.info(STR."{} | \{message}", Thread.currentThread());
        });
    }

    private static Thread completeLunch(){
        var message = "Platform Thread [Complete Lunch]";
        return new Thread(() -> {
            logger.info(STR."{} | \{message}", Thread.currentThread());
        });
    }

    // using builder pattern to create platform threads
    private static void attendMeeting1(){
        var message = "Platform Thread [Attend Meeting]";
        Thread.ofPlatform().start(() -> {
            logger.info(STR."{} | \{message}", Thread.currentThread());
        });
    }

    private static void completeLunch1(){
        var message = "Platform Thread [Complete Lunch]";
        Thread.ofPlatform().start(() -> {
            logger.info(STR."{} | \{message}", Thread.currentThread());
        });
    }
}
```

위 예제는 플랫폼 스레드를 생성하는 두 가지 방법을 보여줍니다.
1. Thread 생성자를 사용하여 람다 런너블을 전달하는 방법
2. Thread의 빌더 메서드 ofPlatform()을 사용하는 방법

<div class="content-ad"></div>

더 복잡한 코딩을 보고 가상 스레드를 동시에 생성하는 방법을 알아보겠습니다. DailyRoutineWorkflow.java 파일을 생성하고 아래 코드를 복사하세요.

```js
package org.vaslabs;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.TimeUnit;

public class DailyRoutineWorkflow {
    static final Logger logger = LoggerFactory.getLogger(DailyRoutineWorkflow.class);

    static void log(String message) {
        logger.info(STR."{} | \{message}", Thread.currentThread());
    }


    private static void sleep(Long duration) throws InterruptedException {
        TimeUnit.MILLISECONDS.sleep(duration);
    }

    private static Thread virtualThread(String name, Runnable runnable) {
        return Thread.ofVirtual().name(name).start(runnable);
    }

    static Thread attendMorningStatusMeeting() {
        return virtualThread(
                "Morning Status Meeting",
                () -> {
                    log("아침 상태 회의에 참석하겠습니다.");
                    try {
                        sleep(1000L);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    log("아침 상태 회의를 마쳤습니다.");
                });
    }

    static Thread workOnTasksAssigned() {
        return virtualThread(
                "Work on the actual Tasks",
                () -> {
                    log("할당된 작업에 대해 실제 작업을 시작합니다.");
                    try {
                        sleep(1000L);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    log("할당된 작업에 대한 실제 작업을 마쳤습니다.");
                });
    }

    static Thread attendEveningStatusMeeting() {
        return virtualThread(
                "Evening Status Meeting",
                () -> {
                    log("저녁 상태 회의에 참석하겠습니다.");
                    try {
                        sleep(1000L);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    log("저녁 상태 회의를 마쳤습니다.");
                });
    }

    static void concurrentRoutineExecutor() throws InterruptedException {
        var morningMeeting = attendMorningStatusMeeting();
        var actualWork = workOnTasksAssigned();
        var eveningMeeting = attendEveningStatusMeeting();
        morningMeeting.join();
        actualWork.join();
        eveningMeeting.join();
    }
}
```

위의 코드는 팩토리 메서드를 사용한 가상 스레드 생성을 보여줍니다. 팩토리 메서드 외에도 java.util.concurrent.ExecutorService를 사용하여 가상 스레드를 구현할 수 있습니다. 이를 통해 ExecutorService를 사용해 동일한 기능을 아래와 같이 구현할 수 있습니다.

```js
package org.vaslabs;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class DailyRoutineWorkflowUsingExecutors {
    static final Logger logger = LoggerFactory.getLogger(DailyRoutineWorkflowUsingExecutors.class);

    static void log(String message) {
        logger.info(STR."{} | \{message}", Thread.currentThread());
    }

    private static void sleep(Long duration) throws InterruptedException {
        TimeUnit.MILLISECONDS.sleep(duration);
    }

    public static void executeJobRoute() throws ExecutionException, InterruptedException {
        try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
            var morningMeeting = executor.submit(() -> {
                log("아침 상태 회의에 참석하겠습니다.");
                try {
                    sleep(1000L);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                log("아침 상태 회의를 마쳤습니다.");
            });

            var actualWork = executor.submit(() -> {
                log("할당된 작업에 대해 실제 작업을 시작합니다.");
                try {
                    sleep(1000L);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                log("할당된 작업에 대한 실제 작업을 마쳤습니다.");
            });

            var eveningMeeting = executor.submit(() -> {
                log("저녁 상태 회의에 참석하겠습니다.");
                try {
                    sleep(1000L);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                log("저녁 상태 회의를 마쳤습니다.");
            });

            morningMeeting.get();
            actualWork.get();
            eveningMeeting.get();
        }
    }
}
```

<div class="content-ad"></div>

## 결과에 대한 심층 분석

위의 코드를 팩토리 메서드나 ExecutorServices를 사용하여 실행하면 아래와 비슷한 결과가 나올 것입니다.

![이미지](/assets/img/2024-06-23-JavaThreadingEssentialsVirtualvsPlatformThreadsExplained_3.png)

정보 로그를 주의 깊게 살펴보면 "|" (파이프 기호)의 양쪽에 두 섹션이 표시됩니다. 첫 번째 섹션은 VirtualThread[#26]/runnable@ForkJoinPool-1-worker-3와 같이 가상 스레드에 대한 정보를 설명합니다. 이 섹션은 가상 스레드[#26]가 platform 스레드인 runnable@ForkJoinPool-1-worker-3에 매핑됨을 나타내며, 다른 섹션은 로그의 정보 부분입니다.

<div class="content-ad"></div>

## 스레드 로컬 및 가상 스레드:

자바의 ThreadLocal은 변수를 스레드별로 저장할 수 있는 메커니즘입니다. ThreadLocal 변수에 접근하는 각 스레드는 변수의 독립적으로 초기화된 복사본을 얻게 되며, 이를 통해 다른 스레드의 동일한 변수에 영향을 주지 않고 접근하고 수정할 수 있습니다. 이는 사용자 세션 또는 데이터베이스 연결과 같이 스레드별 상태를 유지하고자 하는 경우에 특히 유용합니다.

그러나 Project Loom의 일환으로 소개된 가상 스레드와 함께 ThreadLocal을 사용할 때 동작이 크게 변경됩니다. 가상 스레드는 자바 가상 머신(JVM)에 의해 관리되는 가벼운 스레드로, 운영 체제의 스레드 관리에 묶여 있는 전통적인 플랫폼 스레드와 달리 대량으로 스케줄되도록 설계되었습니다.

가상 스레드는 수백만 개로 생성될 수 있기 때문에 ThreadLocal의 사용은 메모리 누수를 발생시킬 수 있습니다.

<div class="content-ad"></div>

```java
package org.vaslabs;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.util.concurrent.TimeUnit;

public class ThreadLocalDemo {
    private static ThreadLocal<String> stringThreadLocal = new ThreadLocal<>();

    static final Logger logger = LoggerFactory.getLogger(DailyRoutineWorkflow.class);

    static void log(String message) {
        logger.info(STR."{} | \{message}", Thread.currentThread());
    }

    private static void sleep(Long duration) throws InterruptedException {
        TimeUnit.MILLISECONDS.sleep(duration);
    }

    public static void virtualThreadContext() throws InterruptedException {
        var virtualThread1 = Thread.ofVirtual().name("thread-1").start(() -> {
            stringThreadLocal.set("thread-1");
            try {
                sleep(1000L);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            log(STR."thread name is \{stringThreadLocal.get()}");
        });
        var virtualThread2 = Thread.ofVirtual().name("thread-2").start(() -> {
            stringThreadLocal.set("thread-2");
            try {
                sleep(1000L);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            log(STR."thread name is \{stringThreadLocal.get()}");
        });
        virtualThread1.join();
        virtualThread2.join();
    }
}
```

## 결론:

요약하면, 이 Java 스레딩 모델에 관한 기사는 Virtual Threads, Platform Threads 및 ThreadLocal의 세부 사항을 살펴봄으로써 Java에서 동시성 프로그래밍의 진화하는 풍경을 밝혀줍니다. Virtual Threads은 가벼우면서 효율적인 동시성을 제공하며 리소스 집약적인 Platform Threads의 본질과는 대조적인 점을 보여줍니다. 이들은 대규모의 동시 작업을 최소한의 리소스 오버헤드로 실행하여 프로그래밍 모델을 간소화하고 응용프로그램 확장성을 향상시키는 방식으로 Java의 스레드 처리 방법을 혁신합니다.

그러나 이 새로운 맥락에서 ThreadLocal 사용의 복잡성은 신중한 고려가 필요함을 강조합니다. ThreadLocal은 기존 스레딩에서 스레드별 데이터를 유지하는 강력한 도구로 남아 있지만, 가상 스레드에서는 더 복잡해지므로 상태 및 컨텍스트 관리를 위한 대체 전략이 필요합니다. 이러한 개념들은 Java의 동시성 패러다임에서 중대한 변화를 나타내며, 개발자들이 보다 반응성이 뛰어나고 확장 가능하며 효율적인 애플리케이션을 구축할 수 있는 새로운 기회를 제공합니다.


<div class="content-ad"></div>

# 스택아데믹

끝까지 읽어주셔서 감사합니다. 마지막으로:

- 작가를 응원하고 팔로우해주시길 바랍니다! 👏
- 트위터(X), 링크드인, YouTube에서 팔로우해주세요.
- 전 세계적으로 프로그래밍 교육을 무료로 제공하는 방법에 대해 자세히 알아보려면 Stackademic.com을 방문해주세요.