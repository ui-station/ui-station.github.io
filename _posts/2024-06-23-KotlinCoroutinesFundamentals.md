---
title: "기초부터 배우는 Kotlin 코루틴 사용법"
description: ""
coverImage: "/assets/img/2024-06-23-KotlinCoroutinesFundamentals_0.png"
date: 2024-06-23 23:18
ogImage:
  url: /assets/img/2024-06-23-KotlinCoroutinesFundamentals_0.png
tag: Tech
originalTitle: "Kotlin Coroutines — Fundamentals"
link: "https://medium.com/@anitaa_1990/kotlin-coroutines-fundamentals-ca25e6c5ffa6"
---

코루틴은 이미 오랫동안 존재하고 있고 여러 다양한 기사들이 그 주변에 존재합니다. 그러나 그것에는 깊은 학습 곡선이 존재하여 Coroutines의 기본 원리를 실제로 이해하는 데 꽤 많은 시간이 걸렸습니다. 그래서 내가 이해한 바에 따라 몇 가지 배운 점을 공유하고 싶다고 생각했습니다.

# 코루틴이란 무엇인가?

코루틴은 협력하는 함수를 의미합니다. 비동기 작업을 처리하는 더 효율적이고 읽기 쉬운 방법을 제공합니다. 쓰레드와 유사하다는 점에서, 일련의 코드 블록을 실행하는데 사용되며 나머지 코드와 동시에 작동합니다. 그러나 코루틴은 특정 쓰레드에 묶이지 않습니다. 한 쓰레드에서 실행을 일시 중단할 수 있고 다른 쓰레드에서 다시 실행할 수 있습니다. 코루틴은 코틀린 1.3 버전에서 출시되었습니다.

# 코루틴의 장점

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

- 코루틴은 가볍습니다 - 지원하는 ​​일시 정지로 인해 한 쓰레드에서 많은 코루틴을 실행할 수 있습니다. 여기서 일시 정지란 몇 가지 명령을 실행한 다음 해당 코루틴을 실행 중간에 중지하고 원하는 때 이어서 실행할 수 있다는 것을 의미합니다. 일시 정지는 많은 동시 작업을 지원하면서도 차단보다 메모리를 절약합니다.
- 코루틴은 메모리 누수가 적습니다 - 코루틴은 구조화된 동시성 원칙을 따르며, 각 코루틴은 특정 컨텍스트 내에서 특정한 수명을 갖고 시작되어야 합니다. 구조화된 동시성은 코루틴의 수명이 특정 범위에 묶여 범위 자체가 완료되기 전에 범위 내에서 시작된 모든 코루틴이 완료되도록 하는 방식입니다. 이는 코루틴 누출을 방지하고 리소스 관리를 단순화하는 데 도움이 됩니다.
- Android에서 코루틴은 Main 안전성을 제공합니다 - 코루틴은 메인 스레드를 블로킹하고 응용 프로그램이 응답하지 않게 만들 수 있는 긴 시간 소요 작업을 관리하는 데 도움이 됩니다. Main 안전성을 통해 어느 suspend 함수도 메인 스레드에서 호출할 수 있도록 할 수 있습니다.
- 코루틴은 내장된 취소 지원을 제공합니다 - 코루틴의 가장 중요한 메커니즘 중 하나는 취소입니다. Android에서 거의 모든 코루틴이 어떤 뷰와 관련이 있으며 이 뷰가 파괴되면 해당 코루틴은 필요하지 않으므로 취소되어야 합니다. 이는 개발자들에게 많은 노력이 필요했던 중요한 기능이지만, 코루틴은 간단하고 안전한 취소 메커니즘을 제공합니다.
- 코루틴은 협력적으로 멀티태스킹됩니다 - 이는 코루틴이 협력적으로 실행할 일련의 명령을 실행하고 운영 체제가 코루틴에 의해 수행되는 작업이나 프로세스의 스케줄링을 제어하지 않음을 의미합니다. 대신, 프로그램과 해당 코루틴을 실행하는 플랫폼에 의존합니다. 따라서 코루틴은 다른 쓰레드가 실행되도록 제어를 양보하여 스케줄러에 다시 제어를 위임할 수 있습니다. 운영 체제의 스케줄러는 이러한 쓰레드들이 작업을 수행할 수 있도록 책임을 집니다. 필요한 경우 일시 중단하여 같은 리소스를 다른 쓰레드가 사용할 수도 있습니다.

# 코루틴의 키워드

코루틴에 대해 학습할 때 마주치게 될 몇 가지 일반적인 키워드입니다.

<img src="/assets/img/2024-06-23-KotlinCoroutinesFundamentals_0.png" />

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

- 중지 함수
- Coroutine Scope (Dispatchers, Job 포함)
- Coroutine 빌더
- Coroutine Context

## `중지` 함수

중지 함수는 일시 중지되고 나중에 계속할 수 있는 함수입니다. 중지 함수는 해당 함수가 중지될 수 있다는 것을 나타내며, 비차단 작업이 완료될 때까지 기다리는 동안 다른 코루틴이 실행될 수 있습니다. 중지 함수가 실행되는 동안 해당 코루틴은 실행되던 스레드를 해제하고 다른 코루틴이 그 스레드에 액세스할 수 있도록 합니다(코루틴은 협력적이기 때문입니다).

중지 함수의 구문은 일반 함수의 구문과 동일하지만 `suspend` 키워드가 추가됩니다. 중지 함수는 코루틴이나 다른 중지 함수에서만 호출할 수 있습니다.

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

```kotlin
suspend fun doSomething(): Int {
    delay(1000L) // 여기서 유용한 작업을 수행하는 것처럼 가정합니다
    return 13
}
```

## 코루틴 스코프

코루틴 스코프는 코루틴의 수명 주기/수명을 정의합니다. 일련의 코루틴 및 해당 컨텍스트의 수명을 제어하는 역할을 합니다. CoroutineScope는 생성된 모든 코루틴을 추적합니다. 따라서 스코프를 취소하면 생성된 모든 코루틴이 취소됩니다. 부모 코루틴 내에서 자식 코루틴이 시작될 때 부모 스코프를 상속합니다(별도로 지정하지 않은 경우) 따라서 부모 코루틴이 중지되면 자식 코루틴도 중지됩니다.

Android에서는 코루틴에는 세 가지 기본 스코프가 있습니다:

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

- 글로벌 범위: GlobalScope는 전체 애플리케이션 수명 동안 지속되는 미리 정의된 코루틴 범위입니다. 편리할 수 있지만 일반적으로 구조화된 동시성을 보장하기 위해 사용자 정의 코루틴 범위를 사용하는 것이 권장됩니다.

```kotlin
GlobalScope.launch {
        val config = fetchConfigFromServer() // 네트워크 요청
        updateConfiguration(config)
    }
```

- LifeCycle 범위: LifecycleOwner(프래그먼트 액티비티)의 수명에 바인딩됩니다. 프래그먼트 액티비티가 파괴되면 이 범위의 코루틴도 취소됩니다. LifecycleScope를 사용하면 특별한 실행 조건을 사용할 수도 있습니다:

* launchWhenCreated는 라이프사이클이 최소한 생성 상태에 있을 때 코루틴을 시작하고 파괴 상태에 있을 경우 일시 중단합니다.
* launchWhenStarted는 라이프사이클이 최소한 시작 상태에 있을 때 코루틴을 시작하고 중지 상태에 있을 경우 일시 중단합니다.
* launchWhenResumed는 라이프사이클이 최소한 재개 상태에 있을 때 코루틴을 시작하고 일시 중단합니다.

```kotlin
lifecycleScope.launchWhenResumed {
  println("로딩 중..")
  delay(3000)
  println("작업 완료")
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

- ViewModel Scope: ViewModel의 수명에 바인딩됩니다. ViewModel이 해제되면이 범위 내의 코루틴도 취소됩니다.

```js
viewModelScope.launch {
  println("로딩 중..")
  delay(3000)
  println("작업이 완료되었습니다")
}
```

## 코루틴 빌더

코루틴 빌더는 새로운 코루틴을 초기화하거나 생성하기 위한 함수입니다. 코루틴의 실행을 시작하고 제어하는 편리한 방법을 제공합니다.

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

- `launch`: 새로운 coroutine을 동시에 시작합니다. 예를 들어, 현재 스레드를 차단하지 않고 시작됩니다. 해당 작업이 취소될 때 자동으로 취소되며 결과 작업이 취소될 때 결과를 반환하지 않습니다. `launch`의 반환 유형은 `Job`입니다. 따라서 해당 작업과 상호 작용하여 coroutine의 라이프사이클을 제어할 수 있습니다. `job.cancel()`을 호출하여 쉽게 취소할 수 있습니다. `launch`는 ViewModel에서 비-suspending 코드에서 suspending 코드로 연결을 생성하는 데 자주 사용됩니다.

```js
launch {
    delay(1000L)
    println("Hello World!")
}
```

- `runBlocking`: 새로운 coroutine을 실행하고 현재 스레드를 해당 완료까지 차단합니다. 다시 말해, 해당 coroutine에서 실행되는 스레드는 주어진 기간 동안 해당 괄호 내의 모든 코드 블록이 실행을 완료할 때까지 차단됩니다.

```js
fun main() = runBlocking { // this: CoroutineScope
    doWorld()
}

suspend fun doWorld() {
    delay(1000L)
    println("Hello Kotlin!")
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

- async: 컨테이너{launch} 함수처럼, 새로운 코루틴을 시작하는 데 사용되며, 차이점은 Job 대신 deferred를 반환한다는 것입니다. deferred는 결과를 나중에 전달할 것을 약속하는 비차단(future)인데, 결과 deferred가 취소되면 실행 중인 코루틴도 취소됩니다. async 빌더를 사용하면 반환된 값을 얻으려면 `await`를 호출하면 됩니다.

```kotlin
fun main() = runBlocking<Unit> {

    val time = measureTimeMillis {
        val one = async { doSomethingUsefulOne() }
        val two = async { doSomethingUsefulTwo() }
        println("The answer is ${one.await() + two.await()}")
    }
    println("Completed in $time ms")

}
```

- delay: 특정 시간 동안 함수를 중단하고 그 후에 다시 시작하는 용도로 사용되는 중단 함수입니다. 백그라운드 스레드를 차단하지 않고 다른 코루틴들이 실행되고 백그라운드 스레드를 사용할 수 있도록 합니다.

```kotlin
launch {
    delay(2000L)
    println("World 2")
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

- withContext: 현재 코루틴 내에서 코루틴 컨텍스트를 전환하는 데 사용되는 지연 함수입니다. 현재 코루틴을 일시 중단하고 지정된 컨텍스트로 전환한 후 새 컨텍스트에서 실행을 계속합니다. 일반적으로 해당 함수를 사용하여 코루틴이 실행될 디스패처를 전환합니다. withContext를 사용하면 콜백을 도입하지 않고 코드가 어느 스레드에서 실행될지 제어할 수 있기 때문에 데이터베이스에서 읽기나 네트워크 요청과 같은 매우 작은 함수에 적용할 수 있습니다.

```kotlin
fun main() = runBlocking {
    val data = withContext(Dispatchers.IO) {
        fetchData()
    }
    println("Response = $data")
}

suspend fun fetchData(): String {
    return "Hello world!"
}
```

## 코루틴 컨텍스트

코루틴 컨텍스트는 코루틴의 동작과 특성을 정의하는 요소들의 세트입니다. 디스패처, 작업, 예외 처리기 및 코루틴 이름과 같은 요소를 포함합니다. 이 컨텍스트는 코루틴이 어떻게 어디에서 실행될지 결정하는 데 사용됩니다.

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

디스패처

코루틴 디스패처는 코루틴이 실행될 스레드를 결정하는 역할을 합니다. 디스패처에는 4가지 유형이 있습니다:

- 메인 디스패처(Main Dispatchers) — 메인 디스패처는 코루틴을 메인 스레드에서 실행합니다. 메인 디스패처는 대부분의 UI 작업을 수행합니다.
- IO 디스패처(IO Dispatchers) — IO 디스패처는 IO 스레드에서 코루틴을 시작합니다. 이 디스패처는 필요할 때 생성되는 스레드 풀을 사용합니다. 파일 읽기 또는 쓰기, 데이터베이스 쿼리 수행, 네트워크 요청 등 실행 스레드를 차단할 수 있는 I/O 작업에 적합합니다.
- 기본 디스패처(Default Dispatchers) — 다른 디스패처가 명시적으로 지정되지 않은 경우 사용되는 기본 디스패처입니다. 공유 백그라운드 스레드 풀을 활용합니다. CPU 자원이 필요한 계산 집약적인 코루틴에 적합한 선택지입니다.
- 비제한 디스패처(Unconfined Dispatcher) — 코루틴이 어떤 스레드에서도 실행되도록 허용합니다. 각 resume마다 다른 스레드에서 실행될 수 있습니다. 특정 스레드에 한정된 CPU 사용이나 공유 데이터 업데이트가 필요 없는 코루틴에 적합합니다.

```js
fun main() = runBlocking {
    launch { // 부모의 콘텍스트인 메인 runBlocking 코루틴
        println("main runBlocking      : 현재 스레드 ${Thread.currentThread().name}에서 작업 중")
    }
    launch(Dispatchers.Unconfined) { // 제한 없음 -- 메인 스레드에서 작동
        println("Unconfined            : 현재 스레드 ${Thread.currentThread().name}에서 작업 중")
    }
    launch(Dispatchers.Default) { // DefaultDispatcher에 디스패치됩니다
        println("Default               : 현재 스레드 ${Thread.currentThread().name}에서 작업 중")
    }
    launch(newSingleThreadContext("MyOwnThread")) { // 자체 새로운 스레드를 가져옵니다
        println("newSingleThreadContext: 현재 스레드 ${Thread.currentThread().name}에서 작업 중")
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

코루틴을 실행하려면 Executor.asCoroutineDispatcher() 확장 기능을 사용하여 코루틴 디스패처로 변환하여 쓰레드 풀에서 실행할 수도 있습니다. 프라이빗 쓰레드 풀은 다음을 사용하여 생성할 수 있습니다:

- newSingleThreadContext(): 내장된 yield 지원이 있는 전용 쓰레드를 사용하여 코루틴 실행 환경을 만듭니다. 원시 자원(스레드 자체)을 할당하는 믹서된 API로 조심스럽게 관리가 필요합니다.
- newFixedThreadPoolContext: 고정 크기의 쓰레드 풀을 사용하여 코루틴 실행 환경을 설정하여, 코루틴의 병렬 실행을 가능하게 하면서 스레드 자원을 주의 깊게 관리합니다.

코루틴 작업

생성된 매 코루틴마다 Job 인스턴스가 반환되는데, 이를 통해 해당 코루틴을 고유하게 식별하고 라이프사이클을 관리할 수 있습니다. Job은 대기열에서의 코루틴을 가리키는 핸들 역할을 합니다. Job은 다음과 같은 상태를 가집니다: New, Active, Completing, Completed, Cancelling, Cancelled. 상태 자체에는 접근할 수 없지만, Job의 속성: isActive, isCancelled 및 isCompleted에 접근할 수 있습니다.

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

```kotlin
val job = launch { // 새로운 코루틴을 시작하고 해당 작업에 대한 참조를 유지합니다
    delay(1000L)
    println("안녕, 세상아!")
}
job.join() // 자식 코루틴이 완료될 때까지 기다립니다
println("완료")
```

SupervisorJob — 이것은 자식 코루틴에 대한 감독자 역할을 하는 Job의 구현입니다. 일반 Job과 유일한 차이점은 그 자식들이 서로 독립적으로 실패할 수 있다는 것입니다. 자식의 실패나 취소는 감독자 작업의 실패를 일으키거나 다른 자식에 영향을 주지 않으므로 감독자는 자식의 실패에 대한 고유 정책을 작성할 수 있습니다.

```kotlin
fun main() = runBlocking {
    val supervisorJob = SupervisorJob()

    val coroutine1 = launch(supervisorJob) {
        println("코루틴 1")
        throw RuntimeException("코루틴 1에서 오류 발생")
    }

    val coroutine2 = launch(supervisorJob) {
        println("코루틴 2")
        delay(500)
        println("코루틴 2 완료")
    }

    coroutine1.join()
    coroutine2.join()

    println("부모 코루틴: ${supervisorJob.isActive}") // 출력: 부모 코루틴: true
}
```

## 코루틴 취소

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

코루틴에서 취소는 Job을 통해 관리됩니다(Job은 코루틴에 대한 핸들이며 수명주기가 있습니다). 우리는 코루틴을 Job의 .cancel() 함수를 호출하여 취소할 수 있습니다. 여러 개의 코루틴을 시작할 때, 전체 스코프 내에 생성된 모든 자식 코루틴을 취소하기 위해 의존할 수 있습니다.

취소는 CancellationException을 throw하는 것 이상의 것이 아닙니다. 여기서의 중요한 차이점은 코루틴이 CancellationException을 throw하면 정상적으로 취소된 것으로 간주되며, 다른 예외는 실패로 간주됩니다. 코루틴 라이브러리에서 제공되는 일시정지 함수는 취소할 수 있지만, 코드를 작성할 때는 항상 취소와 협력하는 것을 고려해야 합니다.

- 코드를 취소할 수 있게 만드는 한 가지 방법은 현재 Job의 상태를 명시적으로 확인하는 것입니다. CoroutineContext와 CoroutineScope에서 모두 isActive() 확장 함수를 사용할 수 있습니다.
- 다른 일반적인 취소 확인 방법은 ensureActive()를 호출하는 것입니다. 이것은 Job, CoroutineContext 및 CoroutineScope에 사용할 수 있는 확장 함수입니다.

취소에 대한 자세한 내용은 여기와 여기에서 찾을 수 있습니다.

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

친구들이 읽어주셔서 감사합니다! 코루틴에 대해 다뤄본 건 정말 많았지만, 유용한 정보였길 바랍니다. 댓글란에 의견을 남겨주세요.

코딩 즐기세요!
