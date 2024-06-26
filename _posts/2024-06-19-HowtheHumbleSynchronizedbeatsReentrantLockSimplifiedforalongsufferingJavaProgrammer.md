---
title: "포기하지 않고 노력하시는 자바 프로그래머를 위해 단순하게 설명하는 방식으로, 겸손한 동기화가 ReentrantLock을 이기는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtheHumbleSynchronizedbeatsReentrantLockSimplifiedforalongsufferingJavaProgrammer_0.png"
date: 2024-06-19 21:49
ogImage:
  url: /assets/img/2024-06-19-HowtheHumbleSynchronizedbeatsReentrantLockSimplifiedforalongsufferingJavaProgrammer_0.png
tag: Tech
originalTitle: "How the Humble Synchronized beats ReentrantLock.. Simplified for a long suffering Java Programmer"
link: "https://medium.com/@apusingh1967/how-synchronized-beats-reentrantlock-simplified-for-a-long-suffering-java-programmer-c83d069a6fc0"
---

2009년에는 독일 철도 기업 직원 교대 관리를 위해 중요한 미션이었고, Java 1.5를 사용하기로 선택했습니다. 제네릭스가 도입되었고, 병행성 유틸리티들이 등장했습니다. 기본적으로 ReentrantLock을 사용하여 다중 스레드 시나리오를 관리하기 시작했습니다. 철도 회사는 여러 사용자가 동시에 데이터에 액세스해야 하는 필요가 있었는데, 예를 들어 여러 사무원이 기차 노선의 일부에 (기관사, 안내원, 요리사, 서빙원) 업무를 할당하려고 했습니다. 실제로 오래된 synchronized 키워드는 폐기되었다고 여겨졌습니다. 하지만 실제로는 그렇지 않았습니다. 그리고 결과적으로 대부분의 상황에서 synchronized가 새롭고 반짝거리는 ReentrantLock보다 우수하다는 것이 밝혀졌습니다.😊

기술적인 세부 사항 대부분은 참고문헌에 남겨두었습니다. 솔직히 말해서 우리는 그 복잡한 것들을 알 필요가 없습니다. 누구도 면접에서 묻지 않을 것이기 때문입니다.

본문의 목적은 대규모 분산 시스템의 하수구에서 작업하고 있는 Java 프로그래머에게 곧바로 `관련된` 정보를 전달하고 진지한 압박 속에서 기술을 자랑하는 기회를 갖겠다는 것입니다.

## 대부분의 상황에서 synchronized가 ReentrantLock보다 우수합니다 😊

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

CopyOnWriteArrayList에서 코드를 인용합니다. Java 1.6부터 대부분의 API 개발자들이 ReentrantLock을 사용하기 시작했는데, 이를 사용하는 것이 새롭고 멋진 것으로 여겨졌기 때문입니다. 그러나 Java 1.9에서는 synchronized로 다시 되돌아가기 시작했습니다 (ReentrantLock에서 synchronized로 다시 되돌아감 참조). 이는 synchronized 작동 방식에 대한 이해 부족 및 Doug Lee와 같은 Java Guru들이 자신을 적용하지 않았다는 점을 반영합니다. ReentrantLock 작동 방식에 대해 설명할 것입니다. 이 글에서 lock 객체 및 synchronized를 사용한 코드와 'lock'에 대한 간결한 설명이 있는 Java 21 버전을 살펴보십시오.

이제 위대한 Doug Lee가 회개했고, synchronized와 ReentrantLock 사이의 장단점이 비슷하다면 synchronized를 기본값으로 사용한다고 말합니다.

하지만 문제는, 왜 고대의 synchronized가 어떤 상황에서 새롭고 멋진 ReentrantLock보다 우수한 것인가입니다?!

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

짧은 답변: synchronized는 JVM이 관리하는 객체로, JVM은 이를 통해 락 상승 및 감소와 같은 다양한 기능을 동적으로 처리할 수 있습니다. 반면 ReentrantLock은 비교 및 설정을 사용하는 일반적인 자바 코드에 갇혀 있습니다. synchronized 키워드를 사용할 때, JVM은 biased lock(더 이상 사용되지 않음), thin lock 또는 fat lock을 사용할지 여부를 결정합니다. 이 세 가지에 대해 알아봅시다.

락 상승

JVM은 얼마나 많은 경합이 있는지에 대한 정보를 수집합니다. 스레드가 biased lock(해당 스레드만 관심을 가짐)을 가지고 있고 두 번째 스레드가 오면, 락이 thin lock으로 상승됩니다. 그러나 더 많은 수의 스레드가 오는 경우, 바로 fat lock으로 상승될 수 있습니다. thin lock도 마찬가지입니다. thin lock은 루프에서 회전하면서 CPU를 소비합니다. 어느 시점에서 경합이 높아지면 락이 fat lock으로 상승됩니다. 마찬가지로, 락이 감소될 수도 있습니다.

## Biased Lock

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

'Synchronize' 메서드를 사용하여 여러 스레드가 공유 자원을 안전하게 사용할 수 있습니다. 이 메서드를 사용하면 잠금 객체의 머리글에 스레드 ID가 저장되어 공유 자원에 대한 액세스가 제어됩니다. 아래 이미지에서 여러 사용자(스레드)가 동일한 전화 부스를 사용하려고 하지만 한 번에 하나의 사용자만 사용할 수 있습니다.

![이미지](/assets/img/2024-06-19-HowtheHumbleSynchronizedbeatsReentrantLockSimplifiedforalongsufferingJavaProgrammer_0.png)

이 접근법은 매우 빠릅니다. 같은 스레드가 동일한 잠금을 호출할 때마다 간단하게 다음과 같이 작동합니다:

```js
if (threadId == notedThreadId) {
  // 계속 진행
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

자바15부터 Biased Locks는 사용되지 않으며 현재는 아마도 제거되었습니다. 자바 ≤ 1.3 시대에는 Java 컬렉션이 동기화되어 있었기 때문에 유용했습니다: Vector, Hashtable. 그러나 대부분 단일 스레드로 사용됩니다. 동기화되지 않은 java.util ArrayList, HashSet, HashMap 등을 사용하면 됩니다. 이러한 컬렉션은 엄격히 단일 스레드 안전하며 biased lock는 JVM에서 코딩하고 유지하는 비용이 아마도 그 가치가 없을 것입니다.

## Thin Lock

이미 biased lock로 보호된 lock을 얻으려는 두 번째 스레드가 있을 때, 두 스레드 모두에 대한 lock은 thin lock으로 승격되고, 첫 번째 스레드가 lock의 소유자가 됩니다. 반대로, 경합이 심한 경우 biased lock에서 fat lock으로 승격될 수 있습니다.

현재 biased lock가 사용되지 않으므로 스레드가 lock을 획들하려고 하면 직접 thin lock을 먼저 사용하게 됩니다. Thin lock은 비교적 무거운 작업입니다. 비교 및 설정이 필요합니다. 그러나 CAS는 여전히 CPU 명령어이며 시스템 호출이 아닙니다. 시스템 호출은 비용이 많이 듭니다. 여기에 비교 및 설정을 사용하여 thin lock을 획들하는 의사 코드 예시가 있습니다.

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
public class Person {

    private AtomicBoolean locked = new AtomicBoolean(false);
    private int id;
    private String name;

    public Person(int id, String name){
        this.id = id;
        this.name = name;
    }

    public String getDetailsAsString(){
        while(!locked.compareAndSet(false, true)); // tricky tricky
        try {
          return this.id + ", " + this.name;
        } finally {
          locked.set(false);
        }
    }
}
```

얇은 락은 비교 및 설정을 사용합니다. 조금 까다로운 면이 있어 이해하기 어려울 수 있습니다. 그러나 이는 바쁜 락입니다. 락을 빨리 얻기를 희망하며 CPU를 일정 시간 동안 소비합니다. 상황에 따라 바쁜/스핀 락은 좋을 수도 있습니다. 왜냐하면 쓰레드를 코어에서 제거하고 다른 쓰레드를 코어에 올리는 데 시간이 소요되기 때문입니다.

![이미지](/assets/img/2024-06-19-HowtheHumbleSynchronizedbeatsReentrantLockSimplifiedforalongsufferingJavaProgrammer_1.png)

## 두꺼운 락

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

Fat Lock은 OS 뮤텍스를 사용합니다. 이것은 시스템 콜을 의미합니다. Thin Lock은 CAS만 사용합니다. 즉, 하드웨어 명령어입니다. 시스템 콜이 연관되지 않습니다. 이는 CPU 상태 변경이 필요하지 않음을 의미합니다. 프로그램 카운터, 베이스 포인터, 레지스터 등이 그것입니다. 하지만 Fat Lock은 시스템 콜이고, 시스템 콜은 비용이 많이 듭니다. 하지만 이해할 필요는 없습니다. 왜냐하면 우리는 애플리케이션 개발자이고 JVM C++ 프로그래머는 아니기 때문입니다.

Fat Lock은 스레드를 스레드의 대기 링크드 리스트에 넣습니다. OS는 이 목록에서 스케줄링합니다. 따라서 이는 바쁜 스핀이 없고 CPU 소모가 없음을 의미합니다. 스레드는 즉시 CPU를 해제합니다.

![image](/assets/img/2024-06-19-HowtheHumbleSynchronizedbeatsReentrantLockSimplifiedforalongsufferingJavaProgrammer_2.png)

## 그래서 Intrinsic (synchronized) Locking이 대부분에서 ReentrantLock을 능가할 수 있는 이유는 무엇일까요?

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

내재 락은런타임에서 계산된 경합에 따라 동적으로 thin lock 또는 fat lock을 수행하거나 스케일을 조절하거나 해제할 수 있습니다. 반면 재진입 락은 그렇지 못합니다. 이는 내재 락이 JVM 객체이기 때문입니다. 재진입 락은 그냥 일반적인 자바 코드에 불과합니다. 이것을 열어서 그 소스 코드를 읽을 수 있습니다.

## 내재 및 재진입 락을 언제 사용해야 하는가

재진입락 사용 시점:

- tryLock, lockInterruptibly, 시간 제한이 있는 tryLock, 락의 공정성과 같은 고급 기능이 필요할 때

내재 락 사용 시점:

- (재)-세상에서 가장 위대한 자바 프로그래머 Doug Lee의 말을 인용하여

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
package java.util.concurrent;

//@author Doug Lea
public class CopyOnWriteArrayList<E> {

    /**
     * 모든 변경 작업을 보호하는 잠금입니다. (내장 모니터나 ReentrantLock 중 어느 것이든 사용 가능할 때는 builtin 모니터를 약간 선호합니다.)
     */
    final transient Object lock = new Object();

    public E set(int index, E element) {
        synchronized (lock) {
```

이후

일부 사람들은 Java21 프로젝트 Loom을 통해 JVM이 내포된 잠금보다 ReentrantLock을 사용할 때 더 잘 작동할 수 있다고 의견을 제시합니다.

## 참고문헌:

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

여러 유형의 JVM 락
JVM에서 동기화 최적화에 대한 심층적인 탐구
Java 동기화 메커니즘의 진화
