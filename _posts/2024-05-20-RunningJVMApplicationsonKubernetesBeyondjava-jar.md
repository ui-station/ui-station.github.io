---
title: "Kubernetes에서 JVM 애플리케이션 실행하기 java -jar을 넘어서"
description: ""
coverImage: "/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_0.png"
date: 2024-05-20 17:17
ogImage:
  url: /assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_0.png
tag: Tech
originalTitle: "Running JVM Applications on Kubernetes: Beyond java -jar"
link: "https://medium.com/codex/running-jvm-applications-on-kubernetes-beyond-java-jar-a095949f3e34"
---

## 쿠버네티스로 오케스트레이션된 컨테이너 환경에서 JVM 애플리케이션을 실행하는 중요한 팁 몇 가지를 발견하세요

[이미지](/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_0.png)을 확인해보세요.

## 대상 독자

쿠버네티스 상의 컨테이너 환경에서 JVM 애플리케이션을 실행하고 있고 성능 및 비용을 더 잘 최적화할 수 있을 것 같다고 느끼시나요?

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

여러분은 JVM 애플리케이션을 이러한 환경으로 이전하는 것을 고려 중이지만 효율적으로 어떻게 할지 고민하고 계신가요?

쿠버네티스에서 실행되는 JVM 애플리케이션의 지연시간 및 처리량 문제에 고민하고 계신가요?

또는 간단히 쿠버네티스 환경에서 JVM 애플리케이션을 최적화하는 방법에 대해 더 알고 싶으신가요?

그럼, 이 게시물이 여러분을 위한 것입니다!

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

우리가 시작하기 전에, 몇 가지 매우 구체적인 시나리오에서는 아래 제공된 모든 팁이 합리적이지 않을 수 있다는 점을 명확히 해야 합니다. 그러나 대부분의 경우에는 쿠버네티스로 조정된 컨테이너화된 환경 내에서 JVM 내에서 실행 중인 서비스가 있는 사용 사례에서 다음 정보가 매우 유용할 것으로 믿습니다.

다음에 제시할 팁을 더 잘 이해하기 위해 몇 가지 기본 개념을 맞추는 것이 중요합니다:

- Java (프로그래밍 언어) 대 Java 플랫폼/JVM: 자바 프로그래밍 언어를 자바 플랫폼과 혼동해서는 안됩니다. 요즘에는 JVM이 자바 이외의 Kotlin, Scala, Groovy, Clojure 등과 같은 여러 프로그래밍 언어를 실행하고 지원할 수 있습니다. 이 글에서는 프로그래밍 언어 자체보다는 실행 환경(런타임)과 관련하여 주로 JVM에 대해 논의할 것입니다.

![image](/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_1.png)

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

그런 면에서 팁으로 넘어가 보겠습니다.

## 1) 에르고노믹스: 영웅과 악당

오늘날, 단순히 java -jar 명령을 실행하여 JVM 애플리케이션을 실행할 때, JVM에는 실행 환경을 기반으로 적절한 구성을 찾으려는 에르고노믹스라는 기능이 있습니다.

처음에는 아주 좋은 것 아닌가? 라고 생각할 수 있습니다. 그러나 이에 대한 답은: 상황에 따라 다릅니다. 이 기능은 세밀한 튜닝에 대해 걱정하지 않고 애플리케이션을 빌드하고 실행하는 데 도움이 될 수 있지만, 대규모 환경에서는 결과가 최적이 아닐 수 있습니다.

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

JVM에서 Ergonomics가 수행하는 일부 자동 구성 조정 사항 중에는 성능 및 자원 소비에 직접 영향을 미칠 수 있는 Garbage Collector 선택 및 Heap 크기와 관련된 것이 있습니다. 함께 조금 더 자세히 살펴보겠습니다.

Garbage Collector 선택

GC의 선택은 두 가지 조건에 기반합니다: JVM에서 사용 가능한 메모리 양과 CPU의 개수입니다.

규칙은 다음과 같이 작동합니다:
Java 8 이전: CPU 수가 2 이상이고 메모리 양이 1792MB보다 크면 선택된 GC는 ParallelGC가 됩니다. 이 두 조건 중 하나라도 위의 값보다 낮은 경우에는 선택된 GC가 SerialGC가 됩니다.

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

Java 9부터: 조건은 사실상 동일하지만 CPU 수가 2 이상이고 메모리 양이 1792MB보다 큰 경우에는 ParallelGC 대신 G1GC가 선택된 GC로 지정됩니다.

![이미지](/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_2.png)

힙 크기

일부 예외를 제외하고 대부분의 경우, -Xmx 매개변수나 -XX:MaxRAMPercentage 플래그를 사용하여 원하는 힙 크기를 지정하지 않을 때, Ergonomics는 사용 가능한 메모리의 ¼를 최대 힙 값으로 구성합니다. 예를 들어, 컨테이너의 메모리 제한이 2GB로 설정된 경우, Ergonomics는 최대 힙 크기를 512MB로 구성합니다.

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

언급된 예외 사항은 다음과 같습니다:

- 컨테이너에 최대 256MB의 사용 가능한 메모리가 있는 경우, 최대 힙 값은 50%가 됩니다.
- 컨테이너에 256MB에서 512MB 사이의 사용 가능한 메모리가 있는 경우, 최대 힙 값은 약 127MB가 됩니다.

하지만 이 모든 것이 미치는 영향은 무엇일까요?

GC의 자동 선택에 관해, SerialGC는 장기간 일시 중지 시간을 발생시켜 고출력 서버 환경에서 성능이 좋지 않을 수 있다는 점을 염두에 두어야 합니다.

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

하지만, 여기서 궁금증이 생길 수도 있어요: 내 컨테이너가 1000m의 제한을 가지고 있고 결과적으로 JVM은 하나의 CPU만 사용 가능한 상황에서, 멀티 스레딩 리소스를 활용하는 GC를 사용하는 장점은 무엇일까요?

이에 대한 답변은 구체적인 세부 사항에 대해서는 들어가지 않겠지만, 요약하자면, 컨테이너 관점에서 혼란이 있습니다; 많은 사람들이 1000m(1000 밀리코어)을 1 CPU로 오해합니다. 그러나 1000m은 계산 용량 시간을 나타내며, 이는 노드의 모든 사용 가능한 CPU들 사이에서 분산될 수 있는 것입니다.

이러한 혼란이 생기는 이유는 JVM이 1000m을 1개의 사용 가능한 CPU로 해석하기 때문입니다. 1001m는 2개의 CPU로, 2001은 3개로 해석됩니다.

이를 알고 있으면, JVM이 CPU가 1000m으로 제한된 컨테이너에서도 멀티 스레딩 GC를 활용할 수 있습니다. 이를 달성하기 위해, -XX:ActiveProcessorCount 플래그를 사용하여 JVM의 사용 가능한 CPU 수를 강제로 설정할 수 있으며, 이때 값은 1보다 큰 값을 전달합니다.

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

SerialGC 외에도 1000m 이하로 다른 GC를 사용할 수 있지만, 사용 사례에 따라 CPU 사용 가능성이 낮아 성능이 저하될 수 있음을 염두에 두는 것이 중요합니다. 사용 가능 할당량(CFS 할당량)을 짧게 초과하여 응용 프로그램 쓰로틀링이 발생할 수 있습니다.

JVM이 사용 중인 GC 구현을 확인하려면 kubectl exec를 사용하여 컨테이너에 액세스하고 다음 명령을 실행하십시오:

```js
java -XX:+PrintFlagsFinal -version | grep “Use*.*GC “
```

아래 예시와 유사한 출력을 받게 되며, 사용되고 있는 구현이 부울 값으로 표시됩니다:

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

![이미지](/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_3.png)

출력물에서 볼 수 있는 예제를 통해 사용 중인 구현은 SerialGC이며, Ergonomics를 통해 구성되었습니다.

힙 크기에 대해, 사용 가능한 메모리의 ¼만 사용한다면, 우리는 자원을 낭비할 수 있습니다. 컨테이너는 애플리케이션을 실행하기 위해 구축된 것이며, 나머지 메모리를 소비할 병렬 프로세스는 가지고 있으면 안 됩니다. 그러나 항상 기억해야 할 중요한 것은 JVM이 힙만으로 구성되지 않으며, 힙 외의 다른 구성 요소도 있어서 우리는 이를 비힙으로 부르고 있습니다. 힙과 비힙 뿐만 아니라 '운영 체제'와 관련된 일부 프로세스도 메모리를 소비하나 작은 양이지만 있습니다. 다음 팁에서 살펴보겠습니다.

![이미지](/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_4.png)

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

JVM의 최대 힙 크기를 확인하려면 kubectl exec를 사용하여 컨테이너에 액세스하고 다음 명령을 실행하세요:

```js
java -XX:+PrintFlagsFinal -version | grep “ MaxHeapSize”
```

다음과 같이 예제와 비슷한 출력을 받게 될 것입니다:

<img src="/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_5.png" />

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

위의 예시에서 보는 것처럼, MaxHeapSize가 바이트로 표시되었고 Ergonomics를 통해 구성되었습니다.

팁 요약:

- 고유련한 서버 환경에서 SerialGC를 사용하지 않도록 주의하세요. JVM이 1개의 CPU에만 제한되지 않도록합니다. 이를 달성하는 여러 방법이 있습니다. 예를 들어 컨테이너의 CPU 제한을 1001m 이상으로 조정하거나 -XX:ActiveProcessorCount 플래그를 1보다 큰 값으로 사용하는 등입니다.
- 컨테이너의 사용 가능한 메모리가 1792MB 미만이고 특정 GC 버전을 강제하지 않으면, Ergonomics가 SerialGC를 선택할 수도 있음을 인식하세요.
- JVM 인수를 통해 원하는 GC 구현을 지정할 수도 있습니다. 권장 사항으로는 4GB 이하의 힙에는 ParallelGC를 사용하고 4GB 이상의 힙에는 G1을 사용합니다. 더 많은 GC 구현이 있지만, 대부분의 사용 사례를 커버해야 합니다. 특정 GC 사용을 강제하는 예시 몇 가지는 다음과 같습니다:

```js
# Serial GC
java -XX:+UseSerialGC -jar meuapp.jar
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

```js
# 병렬 GC
java -XX:+UseParallelGC -jar meuapp.jar
# 병렬 G1 GC
java -XX:+UseG1GC -jar meuapp.jar
# 병렬 UseShenandoahGC GC
java -XX:+UseShenandoahGC -jar meuapp.jar
# 병렬 Z GC
java -XX:+UseZGC -jar meuapp.jar
```

- 성능 향상과 애플리케이션 스로틀링을 피하기 위해 CPU 제한이 2000m 미만인 컨테이너를 피하세요. 많은 상황에서, 1000m CPU 제한이 있는 두 개의 컨테이너보다 2000m CPU 제한이 있는 컨테이너에 단일 JVM이 있는 것이 더 유익할 수 있습니다. 때로는 적은 게 더 나은 선택일 수도 있습니다 — 한 번 생각해보세요.
- 자원 낭비를 피하려면, -Xmx 매개변수나 -XX:MaxRAMPercentage 플래그를 사용하여 JVM 힙 크기를 적절하게 구성하세요. 다음 팁에서 적절한 힙 크기에 대해 더 이야기하겠습니다.

```js
# 예시
```

```js
# MaxRAMPercentage 사용
java -XX:MaxRAMPercentage=50.0 -jar meuapp.jar
# xmx 사용
java -Xmx1g -jar meuapp.jar
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

## 2) 적절한 메모리 크기 설정: 힙 이상의 JVM의 수명

첫 번째 팁을 읽으면 아마도 궁금해 할 것입니다: 리소스 최적화를 위해 컨테이너의 사용 가능한 메모리의 100%에 대한 힙 크기를 구성하는 것은 왜 아닌가요?

그 질문에 대한 답변은 이 팁의 끝에서 알려드리겠습니다. 그 전에 JVM을 구성하는 메모리 영역을 간단히 설명하고 싶습니다.

1번 팁에 언급된 것처럼 힙 이외에 JVM의 메모리 영역에는 힙 이외라고도 불리는 네이티브 메모리 또는 오프-힙 메모리가 포함됩니다. 비-힙 내에서는 메타스페이스, 코드 캐시, 스택 메모리, GC 데이터 등 여러 가지 중요한 구성 요소가 있습니다.

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

이러한 구성 요소 각각에 대해 자세히 다루지는 않겠습니다. 이 글의 목적은 단순히 힙 이외에도 JVM의 다른 영역이 있고, 이 경우 애플리케이션이 실행되는 컨테이너에서 호스트로부터 메모리를 소비한다는 점을 명확히하는 것입니다.

이제 당신은 생각할 수 있습니다: "컨테이너의 사용 가능한 메모리를 힙과 비힙으로 나눌 수 없나요?"

답은 "아니요"입니다. 이 컨테이너의 JVM이 실행되는 기본 이미지는 활성 상태를 유지하기 위해 일정량의 메모리를 소비하는 에뮬레이션 운영 체제도 고려해야 합니다.

그래서 이제 초반 질문에 대한 대답은: 컨테이너의 사용 가능한 메모리의 100%에 힙 크기를 구성하는 것이 왜 좋지 않을까요?

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

힙 크기를 사용 가능한 메모리의 100%로 설정하면, 비-힙 및 운영 체제도 컨테이너 메모리를 사용한다는 것을 감안해야 하므로 OOM(메모리 부족)에 의해 컨테이너가 종료될 수 있습니다.

![image](/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_6.png)

그러나 새로운 질문이 발생할 수 있습니다: 이 메모리를 힙, 비-힙 및 운영 체제 사이에 어떻게 분배해야 할까요?

문헌에서는 사용 가능한 메모리의 75%를 힙에 구성하는 것이 안전한 값이라고 언급되는 경우를 본 적이 있습니다. 그러나 실무에서는 60% 이상의 값 사용 시 문제가 발생했습니다. 저는 개인적으로 전문 경험을 통해 힙에 50%를 할당하는 것이 안전한 값이라고 생각합니다. 초기에는 매우 보수적으로 보일 수 있지만, 이보다 높은 값 사용 가능성을 배제하지는 않습니다. 그러나 이를 확인하는 것이 중요합니다. 확인을 위해 부하 테스트를 활용할 수 있습니다.

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

<img src="/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_7.png" />

힙 크기에 대해 또 하나 중요한 사항은 매우 작은 힙이 가비지 수집기에 과도한 작업을 유발할 수 있어 CPU 사용량이 늘어나고 애플리케이션 성능이 저하될 수 있다는 것을 이해하는 것입니다. 반면에 매우 큰 힙은 애플리케이션 시작 시간에 상당한 영향을 미칠 수 있고 긴 가비지 수집 시간을 유발할 수 있습니다.

팁 요약:

- 컨테이너의 메모리 사용량을 최적화하려면 JVM의 힙 크기를 50%에서 75% 사이로 구성하고 나머지 값은 비힙 및 운영 체제를 위해 예약하세요.
- 구성된 힙 크기가 애플리케이션에 적합한지 검증하기 위해 부하 테스트를 수행하고 테스트 실행 중 OOM (메모리 부족 오류)이 발생하는지 확인하세요. JVM 메모리 소비를 추적하기 위해 모니터링 도구를 사용하고 Kubernetes API를 통해 Kubernetes Metrics Server를 사용하여 파드 메트릭을 모니터링할 수 있습니다.
- 애플리케이션 성능이 저하되지 않도록 매우 작은 힙을 피하세요.
- 애플리케이션 시작 시간에 영향을 미치지 않고 오랜 가비지 수집 시간을 유발하지 않도록 매우 큰 힙을 피하세요.

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

## 3) Xms와 Xmx가 동일하다면: 필요한 양만 말해주세요

이 팁을 살펴보기 전에, Xms와 Xmx에 대한 기본 내용을 알아보겠습니다.
이 두 매개변수는 JVM에게 최소 (Xms) 및 최대 (Xmx) 힙 크기에 대한 정보를 제공하는 데 사용됩니다.

이렇게 작동합니다. Xms는 JVM에 의해 힙에 할당된 초기 메모리 양을 나타내고, Xmx는 할당할 수 있는 최대 양을 나타냅니다. JVM은 Xms에 정의된 값으로 초기 메모리를 할당하고, 프로그램 실행 중에 필요에 따라 이 값은 Xmx에 정의된 값까지 증가할 수 있습니다. JVM이 Xmx를 초과하는 값으로 할당하려고 할 때 OutOfMemoryError가 발생할 수 있습니다.

컨테이너 사용이 오늘날과 같이 널리 사용되기 전에는 JVM에서 실행되는 애플리케이션이 종종 동일한 서버를 공유했습니다. 이러한 시나리오에서는 Xms 매개변수에는 작은 값을 설정하고 Xmx에는 큰 값을 설정하여 리소스 활용 및 동시에 실행 중인 프로세스 간의 공유를 더 잘 목표로했습니다. 따라서 JVM은 필요할 때만 메모리를 할당하고 사용이 끝나면 호스트로 메모리를 반환했습니다.

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

컨테이너를 다룰 때 상황이 달라집니다. 대부분의 경우에는 동일한 컨테이너 내에서 실행 중인 다른 관련 병렬 프로세스가 없습니다. 따라서 프로그램 실행 중에 호스트에 동적으로 메모리를 할당하고 반환할 필요가 없습니다. JVM이 메모리 할당 작업을 처리할 필요가 없도록 하려면 Xms의 값이 Xmx의 값과 같도록 구성할 수 있습니다.

팁 요약:

- JVM이 메모리 할당 및 해제 작업을 처리하는 것을 피하려면 Xms의 값이 Xmx의 값과 같도록 설정하세요. 이를 위해 JVM 구성에서 매개변수 -Xms 및 -Xmx를 사용하십시오.

```bash
# 예시
java -Xms2g -Xmx2g -jar meuapp.jar
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

## 4) JVM 컨테이너에서 CPU 과부하: 위험한 실천 방법이지만 일부 경우에는 허용됨

이 팁을 진행하기 전에 Kubernetes의 두 가지 개념인 "Pod 및 컨테이너 자원 관리"와 "서비스 품질(QoS)"를 설명하는 것이 중요합니다. 이러한 개념은 다음에 다루게 될 주제를 더 잘 이해하는 데 도움이 될 것입니다.

Pod 및 컨테이너 자원 관리

이 시점에서 CPU 및 메모리에 대한 요청과 제한 개념을 설명하고 싶습니다. 기본적으로 요청은 컨테이너가 실행되는 데 필요한 최소 자원량을 나타내며, 제한은 클러스터 내에서 컨테이너가 소비할 수 있는 최대 자원량을 나타냅니다.

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

메모리 요청 및 제한은 쿠버네티스에서 Pod를 만들 때 사용하는 YAML 파일의 "resources" 섹션에서 구성할 수 있습니다.

간단한 예시는 다음과 같습니다:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example
spec:
  containers:
    - name: container-name
      image: image-name:latest
      resources:
        requests:
          memory: "1Gi"
          cpu: "1"
        limits:
          memory: "2Gi"
          cpu: "2"
```

이 시점에서, 저는 팁 1에서 다룬 주제와 관련된 질문을 하고 싶습니다. 거기서는 JVM에 대한 인체공학과 사용 가능한 호스트 리소스에 따라 설정된 기본 구성에 대해 이야기했습니다. 질문은 다음과 같습니다:

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

컨테이너 내에서 사용자 정의 없이 JVM을 실행하고, Ergonomics가 설정을 정의하도록 하는 경우, 컨테이너의 요청 및 제한 값이 YAML 예제에 명시된 값과 같다고 가정하면, JVM이 채택하는 GC 설정 및 최대 힙 크기는 무엇일까요? 컨테이너에 정의된 요청 또는 제한 값이 고려될까요?

이 질문에 대한 답변을 얻기 위해 JVM 버전 17을 사용하고 YAML에서의 구성으로 컨테이너를 실행해보겠습니다. 그런 다음, kubectl exec를 통해 컨테이너에 액세스하여 선택된 GC와 최대 힙 크기를 확인할 것입니다.

JVM이 1GB 메모리와 1 CPU에 대한 요청을 존중한다면, 선택된 GC는 SerialGC이 되고 최대 힙 크기는 256MB (1GB의 ¼)가 될 것입니다.

그러나 JVM이 2GB 메모리와 2 CPU의 제한을 존중한다면, 선택된 GC는 G1GC가 되고 최대 힙 크기는 512MB (2GB의 ¼)가 될 것입니다.

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

아래는 실행 후의 파드 및 컨테이너 구성입니다:

![Pod Configuration](/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_8.png)

Ergonomics에 의해 실행된 JVM 구성 결과는 다음과 같습니다:

![JVM Configuration](/assets/img/2024-05-20-RunningJVMApplicationsonKubernetesBeyondjava-jar_9.png)

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

JVM은 컨테이너의 한계 값을 고려했다는 것이 눈에 띄었습니다. 따라서 선택된 GC는 G1GC이었고, 최대 힙 크기는 512MB로 설정되었는데, 이는 2GB의 ¼에 해당합니다.

이제 리소스 관리 주제에서 잠시 쉬고 두 번째 개념에 대해 이야기해 봅시다.

QoS(Quality of Service)

Kubernetes에서의 QoS(Quality of Service)는 요청하고 사용하는 리소스를 기반으로 Pod를 세 가지 범주로 분류하는 방법으로, 이 범주는 각 Pod의 우선 순위를 결정하는 데 사용됩니다. 이러한 분류로는 Guaranteed, Burstable 및 BestEffort가 있습니다.

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

각 QoS 범주로 Pod를 분류하는 규칙은 다음과 같은 조건에 의해 정의됩니다:

- 보장된(Guaranteed): 이 수준에서 CPU와 메모리 모두에 대해 요청(request) 및 제한(limit) 값이 지정되어야 합니다. 예시:

```js
리소스: 요청: 메모리: "3Gi";
CPU: "2";
제한: 메모리: "3Gi";
CPU: "2";
```

- 터프너블(Burstable): 이 수준은 보장된으로 분류되기 위한 규칙을 충족하지 못하는 Pod를 위한 것입니다. Pod의 적어도 한 컨테이너는 메모리 또는 CPU에 대한 요청(request)나 제한(limit)이 있어야 합니다. 예시:

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

resources:
requests:
memory: "3Gi"

# Burstable의 또 다른 예:

resources:
requests:
memory: "3Gi"
cpu: "1"
limits:
memory: "3Gi"
cpu: "2"

# 이 예제에서 메모리 요청과 제한은 같지만

# CPU 요청이 제한보다 작으므로,

# 팟은 Burstable로 분류됩니다.

- BestEffort: 컨테이너 내에 CPU 또는 메모리에 대한 요청 또는 제한 구성이 없는 경우 팟은 BestEffort로 분류됩니다. 적어도 하나의 컨테이너에 요청이나 제한 구성이 있는 경우 분류가 Burstable로 변경됩니다.

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

하지만 결국 QoS의 중요성과 수준 범주는 무엇인가요?

클러스터의 노드 중 하나가 과부하 상태거나 자원 부족 상태인 경우 Kubernetes 스케줄러는 QoS 우선 순위를 기반으로 제거할 팟을 선택할 수 있습니다. 이는 다음과 같이 정의됩니다:

- Best-Effort: 이러한 팟은 가장 낮은 우선 순위를 가지며 가장 먼저 제거됩니다. 자원 요청이나 제한이 정의되지 않았기 때문에 클러스터에 영향을 미치지 않고 쉽게 제거할 수 있습니다.

- Burstable: 이러한 팟은 중간 우선 순위를 가지며 최소한의 자원 요청 구성을 가지고 있기 때문에 Best-Effort 팟 이후에 제거됩니다.

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

보장된: 이러한 팟은 CPU 및 메모리에 대한 요청 및 제한 값이 동일하여 실행에 필요한 충분한 자원이 확보되어 있기 때문에 가장 높은 우선 순위를 갖고 마지막으로 제거됩니다.

좋아요, 이제 위의 두 개념인 "팟 및 컨테이너 자원 관리"와 "QoS(서비스 품질)"에 대해 논의했으니, 모든 팟을 보장된 상태로 구성하는 것이 가장 좋은 옵션이 될 수 있을지 궁금할 것입니다. 이 수준은 팟에 대한 더 큰 보장과 안정성을 제공하므로, 옳습니까?

그래서 CPU와 메모리에 대한 요청 및 제한을 항상 동일하게 유지하여 모든 팟을 보장된 상태로 구성하는 것은 클러스터 내의 노드가 더 적은 팟을 수용하게 하고, 클러스터 내에서 더 많은 노드가 필요하게 함으로써 환경을 매우 비싸게 만들 수 있습니다. 추가적으로, 이 구성은 자원의 효율적인 활용을 방해할 수 있는 미활용 자원을 남겨 다른 팟과 공유할 수 있는 효과적이지 않은 자원 활용을 초래할 수 있습니다.

반대로, 모든 팟을 보장된 상태로 구성하는 것은 실패와 지연 없이 항상 실행에 필요한 자원을 보장하므로 애플리케이션 안정성을 보장합니다. 게다가 이 구성은 높은 가용성과 성능이 필요한 중요한 애플리케이션에 적합한 옵션이 될 수 있으며, 팟에 최대 우선 순위를 보장합니다.

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

자 그럼 팁으로 넘어가기 전에, 제가 제안하는 것을 정말로 이해하실 수 있도록 하나의 추가적인 개념에 대해 더 이야기를 나누고 싶습니다.

쿠버네티스 클러스터에서 오버부킹(Ovebooking) 개념에 대해 이야기해보겠습니다.

일반적으로 오버부킹은 사용 가능한 것보다 더 많은 리소스를 할당하는 기술로, 할당된 모든 리소스가 동시에 사용되지는 않을 것으로 예상합니다. 예를 들어 항공사에서는 비행기의 총 좌석 수보다 더 많은 좌석을 판매하는 오버부킹을 사용하여 모든 승객이 비행기에 탑승하지는 않을 것을 가정합니다.

쿠버네티스의 맥락에서, 오버부킹은 파드 내의 컨테이너에 할당된 CPU 및 메모리 리소스에 적용될 수 있습니다. 이는 컨테이너에 대한 요청 및 제한에 기반하여 클러스터에 사용 가능한 총 리소스보다 더 많은 리소스를 할당할 수 있는 것을 의미합니다.

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

예를 들어, 4GB의 메모리와 4개의 CPU가 있는 노드가 있다고 상상해보세요. 이 노드에서 하나의 컨테이너를 가진 두 개의 포드가 각각 다음과 같은 리소스 구성을 갖고 있습니다:

```js
리소스: 요청: 메모리: "1Gi";
CPU: "1";
제한: 메모리: "1Gi";
CPU: "3";
```

CPU 제한이 3으로 설정되어 있습니다. 두 개의 포드가 실행 중이며 각각 위의 구성을 갖는 한 개의 컨테이너를 고려할 때, 포드 수를 CPU 제한으로 곱하면 결과적으로 6개의 CPU가 되는데, 이는 노드에서 사용 가능한 CPU의 최대 개수를 초과합니다.

Kubernetes에서 Overbooking을 다룰 때 중요한 점은 CPU가 "압축 가능" 리소스로 간주되지만 메모리는 그렇지 않다는 것입니다. 즉, Kubernetes는 컨테이너가 요청한 양의 CPU를 받도록 보장하고 나머지를 제한할 것입니다. 그러나 컨테이너가 CPU 제한을 초과하기 시작하면 Kubernetes는 해당 컨테이너의 사용을 제한하기 시작할 것이며, 이는 응용 프로그램의 성능 하락으로 이어질 수 있습니다. 단, 해당 컨테이너는 종료되거나 제거되지 않는다는 점을 강조해야 합니다.

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

CPU와 달리 메모리는 압축할 수 없는 자원입니다. 이는 노드가 사용 가능한 메모리를 모두 소진하면 Kubernetes가 메모리 공간을 확보하기 위해 어떤 컨테이너를 종료할지 결정해야 한다는 것을 의미합니다.

대부분의 경우 JVM 애플리케이션은 데이터 처리가 많거나 복잡한 계산이 필요한 경우를 제외하고 메모리를 더 많이 요구합니다. 그래서 개발 환경을 효율적으로 관리하려면 다음 팁을 활용해보세요.

클러스터를 효율적으로 활용하기 위해, 메모리에 대해서는 요청 값(request)을 제한 값(limits)과 동일하게 설정하고, CPU에 대해서는 오버북(overbook)해서 요청 값을 제한 값보다 낮게 설정하여 Pod를 Burstable 수준으로 유지하세요. 이렇게 함으로써 CPU 자원을 Pod 간에 공유할 수 있어 클러스터 효율성이 향상될 것입니다.

만약 모든 Pod가 동시에 CPU 제한값을 사용해야 하는 경우(이는 드문 경우입니다), 과도한 CPU 소비를 노드 자동 확장의 트리거로 사용하여 환경의 부하를 균형잡을 수 있습니다. 이러한 시나리오에서 유일한 부작용은 노드를 확장할 때까지 일시적으로 컨테이너가 제한될 수 있다는 것입니다.

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

이 전략은 처음에는 논란이 될 수 있지만, CPU의 가격이 메모리에 비해 비례적으로 높기 때문에 환경 비용을 극적으로 최적화할 수 있습니다.

팁 요약

- 특히 테스트 환경에서 비용을 줄이려면 쿠버네티스 환경에서 JVM 애플리케이션을 실행하는 파드를 Burstable 수준에서 구성하는 것을 고려하십시오. 메모리는 동등한 요청 및 한도를 포함하며 CPU의 경우 요청을 한계보다 낮게 설정하여 오버부킹하세요.

## 5) JVM 및 HPA: 표준 사용해야 할 경우, 메모리보다 CPU를 우선시하세요

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

이 팁을 진행하기 전에 Horizontal Pod Autoscaler(HPA)에 대해 간단히 이야기하려고 합니다.

HPA는 쿠버네티스의 기능 중 하나로, 팟의 CPU 또는 메모리 사용량에 기반하여 애플리케이션의 복제본 수를 자동으로 조정하는 데 도움을 줍니다. 특정 수요를 충족시키기 위해 복제본 수를 증가하거나 감소시킵니다. 처리 능력이나 메모리에 대한 더 높은 수요가 있을 때 HPA는 복제본 수를 늘립니다. 수요가 줄어들면 HPA는 복제본 수를 감소시킵니다. 이렇게 함으로써 HPA는 피크 수요 기간에도 서비스 가용성을 유지합니다.

JVM 애플리케이션을 다룰 때는 보통 CPU보다 더 많은 메모리를 소비하는 경우가 많습니다. 따라서 HPA를 메모리에 기반으로 스케일링하도록 구성하는 것이 유혹적일 수 있습니다. 그러나, JVM은 쓰레기 수집 및 메모리 할당 프로세스로 인해 메모리 소비에 변동이 발생할 수 있습니다. 이는 메모리를 HPA의 트리거로 사용하여 HPA를 불안정하게 만들어 애플리케이션의 수평 스케일링 프로세스를 방해할 수 있습니다.

이를 알아두면, 기본적으로 쿠버네티스에서는 메모리와 CPU 메트릭을 고려하여 스케일을 조정할 수 있기 때문에, 우리의 유일한 선택은 CPU를 HPA 트리거 구성의 대안으로 고려하는 것입니다.

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

하지만 CPU를 HPA 트리거로 사용하도록 권장하는 것은 유일한 옵션인 것으로 생각하지 마세요. 실제로 쿠버네티스에서 JVM 애플리케이션을 수평 확장하는 흥미로운 전략이 있습니다. CPU를 메트릭으로 사용하는 것이 관련됩니다. 이에 대해 자세히 알아보겠습니다.

이 기본 아이디어는 다음과 같습니다. 고수요 시나리오에서 JVM은 메모리를 강하게 할당하기 시작하면서 응용 프로그램의 가비지 컬렉터(GC)에 대한 작업이 증가하고 풀 가비지 컬렉션 주기(Full GC)를 포함한 CPU 집약적인 프로세스가 발생합니다. 여기서 HPA 역할이 의미를 갖기 시작합니다. 이 문맥에서 HPA는 응용 프로그램의 수요를 나타내는 지표로 CPU 메트릭을 기반으로 응용 프로그램을 수평 확장하는 데 사용할 수 있습니다.

다음은 이 HPA 구성의 예시입니다:

```js
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: nome-do-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nome-do-deployment
  minReplicas: 1
  maxReplicas: 5
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
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

이 예제에서 HPA는 배치의 CPU 사용률을 모니터링하고 자동으로 팟 레플리카 수를 조정하여 CPU 사용률을 50%로 유지합니다. 최소 레플리카 수는 1이며, 최대는 5로 설정되어 있어 요구에 따라 응용 프로그램이 수평 확장될 수 있습니다.

HPA는 주기적으로 배치의 CPU 사용률을 모니터링하고, 일정 기간 동안 사용률이 임계값 아래로 유지되면 HPA는 천천히 레플리카 수를 줄이기 시작하여 정의된 최소값에 도달할 때까지 줄입니다. Kubernetes는 기본적으로 스케일 다운 프로세스를 시작하기 전에 5분간 대기합니다.

팁 요약

- JVM(Java Virtual Machine) 애플리케이션의 성능을 향상시키기 위해 Kubernetes의 Horizontal Pod Autoscaler(HPA)에 CPU 메트릭을 기본 구성으로 사용하여 고수요 시나리오에서 애플리케이션 성능을 향상시킬 수 있습니다. 이는 트래픽 급증 시 JVM에 의한 강력한 메모리 할당이 쓰레기 수집기의 작업 부하를 크게 증가시킬 수 있어 전체 가비지 컬렉션 주기(Full GC)와 과도한 CPU 사용량이 발생할 수 있기 때문입니다.

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

## 결론

이 포스트가 너무 길어서 죄송합니다. 제 목적은 포괄적이고 자세한 내용을 제공하는 것이었습니다. 이러한 지식은 오랜 연구와 경력을 통해 얻었으며, 제가 공유한 팁들이 도움이 되었기를 바랍니다.

이것은 단순히 시작에 불과하며, 가능한 제2편에서 이와 같은 팁을 더 공유할 계획입니다.

비판, 제안이 있거나 단순히 내용을 좋아했다면 의견을 남겨 주시기 바랍니다. 함께 공유하겠습니다.

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

## 참고 자료

- Microsoft Learn의 Microsoft for Java Developers
- Kubernetes의 Kubernetes Documentation
- Bruno Borges의 YouTube 동영상인 (178) Secrets of Performance Tuning Java on Kubernetes (해당 주제에 대해 본 적이 가장 좋았던 콘텐츠)
- Google Cloud Blog의 Kubernetes requests vs limits: Why adding them to your Pods and Namespaces matters
- Java inside docker: What you must know to not FAIL
- DigitalOcean Kubernetes에서 워크로드를 자동으로 확장하는 방법
