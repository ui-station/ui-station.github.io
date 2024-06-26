---
title: "Kotlin 코루틴 핵심요소 알아야 할 모든 것"
description: ""
coverImage: "/assets/img/2024-05-18-KotlinCoroutineEssentialsEverythingyouneedtoknow_0.png"
date: 2024-05-18 15:32
ogImage:
  url: /assets/img/2024-05-18-KotlinCoroutineEssentialsEverythingyouneedtoknow_0.png
tag: Tech
originalTitle: "Kotlin Coroutine Essentials: Everything you need to know"
link: "https://medium.com/proandroiddev/kotlin-coroutine-essentials-everything-you-need-to-know-c8a98fb6cda5"
---

<img src="/assets/img/2024-05-18-KotlinCoroutineEssentialsEverythingyouneedtoknow_0.png" />

안녕하세요! 이 글은 코루틴 시리즈에서 두 번째 글입니다. 코루틴을 완전히 이해하기 위해 확인해볼 수 있는 다음 글 목록입니다.

- 코루틴, 무엇인가, 어떻게 사용하며 왜 사용하는가?
- 코루틴 핵심 요소(이 글).
- 코루틴 내부 동작 방식.

이미 코루틴이 무엇이고 어디에 어떻게 사용하는지 알고 있다고 가정하고 있습니다. 아직 익숙하지 않다면 이 글을 읽어보세요.

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

우선, 잘 알려진 Coroutine 빌더를 사용하여 우리의 코루틴을 만들어 보겠습니다:

# Launch:

launch 코루틴 빌더는 결과를 반환하지 않는 새로운 코루틴을 시작하는 데 사용됩니다. 이는 실행 후 바로 잊을 수 있는 작업에 사용되며, 긴 실행 함수를 호출하고 반환 값을 신경 쓰지 않아도 되는 경우에 사용됩니다.

```js
fun main() {
    println("시작")

    // 코루틴 실행
    GlobalScope.launch {
        delay(1000) // 일부 백그라운드 작업 시뮬레이션
        println("코루틴이 완료되었습니다")
    }

    println("끝")
}

//시작
//끝
//...
//코루틴이 완료되었습니다
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

# 비동기:

비동기 코루틴 빌더(async)는 Deferred 값을 반환하는 코루틴을 시작하는 데 사용됩니다. 우리는 지연된 값에 대해 중단 함수 await를 호출하여 기다리고 결과를 가져올 수 있습니다.

```js
fun main() {
    println("시작")

    val deferredResult = GlobalScope.async {
        delay(1000) // 일부 백그라운드 작업 시뮬레이션
        "코루틴 완료"
    }

    // 그 동안 다른 작업 수행

    // 지연된 값에서 결과를 검색
    runBlocking {
        val result = deferredResult.await() // await는 중단 함수입니다
        println(result)
    }

    println("끝")
}
//시작
//코루틴 완료
//끝
```

# CoroutineScope:

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

이것을 코루틴의 어머니로 생각해보세요. CoroutineScope는 생성 중인 모든 코루틴을 추적하며, 마치 어머니가 자녀를 돌보는 것과 같습니다.

진행 중인 작업(실행 중인 코루틴)은 언제든지 scope.cancel()을 호출하여 취소할 수 있습니다.

특정 계층의 앱에서 코루틴의 수명주기를 시작하고 제어하고 싶을 때마다 CoroutineScope를 생성해야 합니다. 안드로이드에서는 viewModelScope, lifecycleScope 또는 전체 애플리케이션 수명주기를 위한 GlobalScope가 있습니다.

CoroutineScope를 생성할 때는 생성자의 매개변수로 CoroutineContext를 사용합니다. 다음 코드로 새로운 scope 및 코루틴을 생성할 수 있습니다:

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
// Job과 Dispatcher는 CoroutineContext로 결합되어 CoroutineContext에 대해 곧 논의할 것입니다.
val scope = CoroutineScope(Job() + Dispatchers.Main)
val job = scope.launch {
    // 새로운 코루틴
}
```

## Job:

코루틴 내에서 Job 인스턴스는 코루틴 자체를 나타냅니다. Job은 코루틴에 대한 핸들입니다. launch 또는 async로 생성하는 각 코루틴에 대해 고유하게 식별되고 라이프사이클을 관리하는 Job 인스턴스가 반환됩니다.

Job은 일련의 상태를 거칠 수 있습니다: New, Active, Completing, Completed, Cancelling 및 Cancelled. 우리는 상태 자체에는 액세스할 수 없지만, Job의 속성에 액세스할 수 있습니다: isActive, isCancelled 및 isCompleted.

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

작업/Coroutine의 상태:

![Job/Coroutine States](/assets/img/2024-05-18-KotlinCoroutineEssentialsEverythingyouneedtoknow_1.png)

작업 유형:

- Job: 단일 coroutine을 나타내며 시작, 대기 및 취소와 같은 라이프사이클을 제어할 수 있습니다. 간단한 비동기 작업에 사용할 수 있습니다.
- DeferredJob: 타입 T의 결과를 생성하는 coroutine을 나타내며 await 함수를 사용하여 결과를 대기할 수 있는 방법을 제공합니다. Deferred는 작업을 병행 및 비동기적으로 수행하고 결과를 얻어야 할 때 사용됩니다.
- SupervisorJob: 자식 coroutines을 위한 부모 작업으로 사용되는 작업 유형입니다. 일반 작업과 달리 자식 coroutine의 실패 또는 취소가 부모 및 다른 자식에게 전파되지 않습니다. 작업 트리의 특정 가지에서 실패를 격리하고 싶을 때 유용합니다.
- CompletableJob: 명시적으로 complete() 함수를 사용하여 완료할 수 있는 작업 유형입니다. 사용자 정의 구현이나 작업의 라이프사이클을 처리하는 사용자 정의 방법을 만들고 싶을 때 자주 사용됩니다.

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

# CoroutineContext:

CoroutineContext은 코루틴 실행의 컨텍스트를 나타내는 인터페이스입니다. 코루틴의 동작을 정의하는 요소 집합을 제공합니다. 이 컨텍스트는 코루틴 실행을 관리하는 데 중요하며 동시성, 스레드 풀링 및 스케줄링을 처리합니다.

구성 요소는 다음과 같습니다:

- Job — 코루틴의 수명을 제어합니다.
- CoroutineDispatcher — 작업을 적절한 스레드로 보냅니다.
- CoroutineName — 코루틴의 이름으로 디버깅에 유용합니다.
- CoroutineExceptionHandler — 처리되지 않은 예외를 처리합니다.

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

Coroutine은 새 작업 및 부모로부터 상속된 다른 것들의 CoroutineContext입니다.

CoroutineScope는 코루틴을 생성할 수 있고 코루틴 내에서 더 많은 코루틴을 생성할 수 있기 때문에 암시적인 작업 계층이 생성됩니다. 다음 코드 스니펫에서는 CoroutineScope를 사용하여 새 코루틴을 생성하는 것 외에도 코루틴 내에서 더 많은 코루틴을 생성하는 방법을 살펴보십시오:

```js
val scope = CoroutineScope(Job() + Dispatchers.Main)
val job = scope.launch {
    // CoroutineScope를 부모로 가지는 새 코루틴
    val result = async {
        // launch에 의해 시작된 코루틴을 부모로 가지는 새 코루틴
    }.await()
}
```

해당 계층의 루트는 일반적으로 CoroutineScope입니다. 이 계층 구조를 다음과 같이 시각화할 수 있습니다:

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

![Kotlin Coroutine Essentials](/assets/img/2024-05-18-KotlinCoroutineEssentialsEverythingyouneedtoknow_2.png)

코루틴은 작업 계층 구조에서 실행됩니다. 부모는 CoroutineScope 또는 다른 코루틴이 될 수 있습니다.

# 부모 CoroutineContext:

작업 계층 구조에서 각 코루틴은 CoroutineScope 또는 다른 코루틴이 될 수 있는 부모를 가지고 있습니다. 그러나 코루틴의 결과 CoroutineContext는 부모의 CoroutineContext와 다를 수 있습니다. 왜냐하면 이 공식에 따라 계산되기 때문입니다:

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

위와 같이 표현됩니다:

- 일부 요소는 기본 값을 가집니다: Dispatchers.Default는 CoroutineDispatcher의 기본값이며, "coroutine"은 CoroutineName의 기본값입니다.
- 상속된 CoroutineContext는 해당 Coroutine을 생성한 coroutine의 CoroutineContext입니다.
- Coroutine 빌더에 전달된 인수는 상속된 컨텍스트의 해당 요소들보다 우선합니다. (아래 예시 참조)

참고: CoroutineContext는 + 연산자를 사용하여 결합할 수 있습니다. CoroutineContext는 요소의 집합이므로, + 연산자 오른쪽 요소들이 왼쪽 요소를 재정의하여 새로운 CoroutineContext가 생성됩니다.

예시: (Dispatchers.Main, "name") + (Dispatchers.IO) = (Dispatchers.IO, "name")

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

![Coroutine Context](/assets/img/2024-05-18-KotlinCoroutineEssentialsEverythingyouneedtoknow_3.png)

이 CoroutineScope에서 시작된 모든 코루틴은 CoroutineContext에 적어도 이러한 요소가 있을 것입니다. CoroutineName은 기본 값에서 가져오므로 회색으로 표시됩니다.

새 코루틴의 부모 CoroutineContext를 알았으니, 그 코루틴의 실제 CoroutineContext는 다음과 같습니다:

위 이미지에 표시된 CoroutineScope를 사용하여 위와 같이 새 코루틴을 생성하면:

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
val job = scope.launch(Dispatchers.IO) {
    // new coroutine
}
```

그 코루틴의 부모 CoroutineContext 및 실제 CoroutineContext는 무엇입니까? 아래 이미지로 해답을 확인하세요!

![해답 이미지](/assets/img/2024-05-18-KotlinCoroutineEssentialsEverythingyouneedtoknow_4.png)

CoroutineContext 안의 Job과 부모 컨텍스트는 항상 동일한 인스턴스가 아니며, 새로운 Coroutine이 항상 새로운 Job 인스턴스를 가져오게 됩니다. (새로운 Job은 녹색이고 부모 Job은 빨강색입니다.)

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

`Dispatchers.IO`가 스코프의 `CoroutineDispatcher`를 덮어씌워서, 결과적으로 부모 CoroutineContext에는 Dispatchers.IO가 포함되어 있습니다.

코루틴과 관련된 중요한 개념들을 이해하셨기를 바라며, 앞으로는 이를 효율적으로 활용하여 멋진 애플리케이션을 만드시기 바랍니다. 앞으로 제공될 흥미로운 콘텐츠를 기대해 주세요. 함께 해주셔서 감사합니다!
