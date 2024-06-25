---
title: "Kotlin Flows 기본 개념 이해하기"
description: ""
coverImage: "/assets/img/2024-06-23-KotlinFlowsFundamentals_0.png"
date: 2024-06-23 23:34
ogImage:
  url: /assets/img/2024-06-23-KotlinFlowsFundamentals_0.png
tag: Tech
originalTitle: "Kotlin Flows — Fundamentals"
link: "https://medium.com/@anitaa_1990/kotlin-flows-fundamentals-7d9b984f12bb"
---

지난 주에는 Kotlin 코루틴에 대해 더 알아보았어요. 이전 글에서는 CoroutineContext, CoroutineScope, Coroutine Builder 등 코루틴의 기초 중 일부에 초점을 맞췄죠. 약속대로, 지금은 그에 대한 후속 글로 Flows에 대해 다루려고 해요.

# Flows가 뭔가요?

비동기로 계산할 수 있는 데이터의 스트림을 Flow라고 합니다. Flow는 LiveData와 RxJava 스트림과 같이 옵저버 패턴을 구현할 수 있게 해줍니다. 옵저버 패턴은 상태 변경이 일어날 때 해당 상태를 관찰하는 객체(소스)와 그 상태 변경을 자동으로 알리는 의존 객체들(수집자)으로 이루어진 소프트웨어 디자인 패턴입니다. Flow는 일시 중지 함수를 사용하여 값을 비동기적으로 생성하고 소비합니다.

Flow를 생성하려면 먼저 프로듀서를 만들어야 해요.

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

val randomFlow: Flow<Int> = flow {
repeat(10) { it ->
emit(it+1) // flow에 대한 요청 결과를 내보낸다
delay(1000) // 코루틴을 1초 동안 일시 중단한다
}
}

Flow를 수집하려면 먼저 Flow가 내부적으로 코루틴에서 작동하기 때문에, Coroutine을 시작해야 합니다. collect 연산자는 emit된 값들을 수집하는 데 사용됩니다.

```kotlin
lifecycleScope.launch {
    viewModel.uiStateFlow.collect { it ->
        binding.uiText.text = it.toString()
    }
}
```

Flow에는 두 가지 서로 다른 유형이 있습니다.

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

- Cold Flow — 값을 수집하기 시작할 때까지 값을 생성하지 않습니다. 오직 하나의 구독자만 가질 수 있으며 데이터를 저장하지 않습니다.

```js
// Regular Flow 예시
val coldFlow = flow {
     emit(0)
     emit(1)
     emit(2)
}

launch { // 처음으로 collect를 호출
    coldFlow.collect { value ->
        println("cold flow collector 1이 받은 값: $value")
    }

    delay(2500)

  // 두 번째로 collect를 호출
  coldFlow.collect { value ->
        println("cold flow collector 2이 받은 값: $value")
    }
}

// 결과
// 두 수집자는 처음부터 모든 값을 받게 됩니다.
// 두 수집자에 대해 해당 Flow는 처음부터 시작합니다.
flow collector 1이 받은 값: [0, 1, 2]
flow collector 1이 받은 값: [0, 1, 2]
```

- Hot Flow — 아무도 수집하지 않아도 값을 생성합니다. 여러 구독자를 가질 수 있으며 데이터를 저장할 수 있습니다.

```js
// SharedFlow 예시
val sharedFlow = MutableSharedFlow<Int>()

sharedFlow.emit(0)
sharedFlow.emit(1)
sharedFlow.emit(2)
sharedFlow.emit(3)
sharedFlow.emit(4)

launch {
    sharedFlow.collect { value ->
        println("SharedFlow 수집자 1이 받은 값: $value")
    }

    delay(2500)

  // 두 번째로 collect를 호출
  sharedFlow.collect { value ->
        println("SharedFlow 수집자 2이 받은 값: $value")
    }
}

// 결과
// 수집자는 수집을 시작한 곳부터 값을 받습니다.
// 여기서 1번째 수집자는 모든 값을 받게 됩니다. 하지만 2번째 수집자는
// 2500밀리초 후에 수집을 시작했기 때문에 그 이후에 방출된 값만을 받습니다.
SharedFlow 수집자 1이 받은 값: [0, 1, 2, 3, 4]
SharedFlow 수집자 2이 받은 값: [2, 3, 4]
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

어떤 cold flow라도 stateIn() 및 shareIn() 연산자를 사용하여 각각 뜨거운 flow로 변환할 수 있습니다.

## SharedFlow 및 StateFlow

- StateFlow — StateFlow는 한 번에 하나의 값을 보유하는 상태를 나타내는 뜨거운 flow입니다. 또한 conflated flow이며, 새 값이 발행될 때 가장 최근 값이 보존되고 즉시 새 수집기에 발행됩니다. 상태에 대한 단일 진실 원천을 유지하고 모든 수집기를 자동으로 최신 상태로 업데이트해야 할 때 유용합니다. 항상 초기 값이 있으며 최신으로 발행된 값만 저장합니다.

```js
class HomeViewModel : ViewModel() {

    private val _textStateFlow = MutableStateFlow("Hello World")
    val stateFlow =_textStateFlow.asStateFlow()

    fun triggerStateFlow(){
        _textStateFlow.value="State flow"
    }
}

// Activity/Fragment에서 StateFlow 수집
class HomeFragment : Fragment() {
    private val viewModel: HomeViewModel by viewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

lifecycleScope.launchWhenStarted {

  // Flow를 트리거하고 값 수집 시작

  // collectLatest()는 Kotlin의 Flow API의 고차 함수로
  // Flow로부터 발행된 값을 수집하고 최신 값에 대해 변환할 수 있는 함수입니다.
  // 모든 발행된 값들을 수집하는 collect()와 유사하지만,
  // collectLatest는 최신으로 발행된 값만 처리하고
  // 아직 처리되지 않은 이전 값들을 무시합니다.
    viewModel.stateFlow.collectLatest {
          binding.stateFlowButton.text = it
    }
  }
}

// Compose에서 StateFlow 수집
@Compose
fun HomeScreen() {
 // Compose는 flow에서 값을 수집하고 사용할 최신 값을 제공하는
 // collectAsStateWithLifecycle 함수를 제공합니다.
 // 새로운 flow 값이 발행되면 업데이트된 값을 얻고,
 // 재구성을 통해 값의 상태를 업데이트합니다.
 // LifeCycle.State.Started를 기본값으로 사용하여 수집을 시작합니다.
 // 지정된 상태의 라이프사이클에 있을 때 값 수집을 시작하고,
 // 해당 상태 아래로 떨어질 때 멈춥니다.
  val someFlow by viewModel.flow.collectAsStateWithLifecycle()

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

- SharedFlow — SharedFlow는 여러 개의 수집기(collector)를 가질 수 있는 HotFlow입니다. 수집기들과 독립적으로 값을 방출할 수 있으며, 다수의 수집기들이 동일한 flow에서 값을 수집할 수 있습니다. 하나의 값을 여러 수집기에 브로드캐스팅하거나 동일한 데이터 스트림에 대해 여러 구독자를 가질 때 유용합니다. 초기값이 없으며, 새롭게 추가된 수집기들을 위해 이전에 방출된 값을 일정 수만큼 저장할 replay 캐시를 구성할 수 있습니다.

```js
class HomeViewModel : ViewModel() {
    private val _events = MutableSharedFlow<Event>() // 비공개 mutable shared flow
    val events = _events.asSharedFlow() // 외부에 노출된 읽기 전용 shared flow

    suspend fun produceEvent(event: Event) {
        _events.emit(event) // 모든 구독자가 받을 때까지 실행이 중단됩니다
    }
}

// Activity/Fragment에서 StateFlow 수집
class HomeFragment : Fragment() {
    private val viewModel: HomeViewModel by viewModels()

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        lifecycleScope.launchWhenStarted {

            // 플로우를 트리거하고 값을 수신하기 시작합니다

            // collectLatest()는 Kotlin의 Flow API에서 제공되는 고차 함수로,
            // Flow에서 방출된 값을 수집하고 최신 값에 대해 변환을 수행할 수 있습니다.
            // collect()와 유사하게 모든 방출된 값을 수집하는데 사용되지만,
            // collectLatest는 최신 값만 처리하고 아직 처리되지 않은 이전 값들을 무시합니다.
            viewModel.events.collectLatest {
                binding.eventFlowButton.text = it
            }
        }
    }
}

// Compose에서 StateFlow 수집
@Compose
fun HomeScreen() {
    // Compose는 collectAsStateWithLifecycle 함수를 제공하며,
    // 플로우에서 값을 수집하고 필요한 최신 값을 제공합니다.
    // 새로운 플로우 값이 방출되면 업데이트된 값을 얻고,
    // 값의 상태를 업데이트하기 위해 다시 조합(composition)이 발생합니다.
    // 기본적으로 LifeCycle.State.Started를 사용하여 수집을 시작하고,
    // 지정된 상태에 있을 때 값을 수집하며, 해당 상태보다 낮아지면 중지합니다.
    val someFlow by viewModel.events.collectAsStateWithLifecycle()
}
```

# 플로우에서 예외 처리

Kotlin Flow는 예외와 오류를 처리하기 위한 여러 메커니즘을 제공합니다.

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

- try-catch 블록 — 예외를 처리하는 기본적인 방법 중 하나는 흐름 내에서 try-catch 블록을 사용하는 것입니다.

```js
flow {
    try {
        emit(productsService.fetchProducts())
    } catch (e: Exception) {
        emitError(e)
    }
}
```

- catch 연산자 — Flow의 catch 연산자를 사용하면 예외를 한 곳에서 오류 처리 논리를 캡슐화하여 처리할 수 있습니다.

```js
flow {
    emit(productsService.fetchProducts())
}.catch { e ->
    emitError(e)
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

- onCompletion Operator — Flow가 성공적으로 또는 예외로 완료되고 난 후 코드를 실행할 때 사용됩니다.

```js
flow {
    emit(productsService.fetchProducts())
}.onCompletion { cause ->
    if (cause != null) {
        emitError(cause)
    }
}
```

- 사용자 정의 오류 처리 — Android의 복잡한 시나리오에서는 애플리케이션에 적합한 방식으로 오류를 처리하기 위해 사용자 정의 연산자나 확장 함수를 만들 수 있습니다.

```js
fun <T> Flow<T>.sampleErrorHandler(): Flow<Result<T>> = transform { value ->
    try {
        emit(Result.Success(value))
    } catch (e: Exception) {
        emit(Result.Error(e))
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

# Flows vs LiveData

- LiveData는 라이프사이클을 인식하므로 옵저버의 라이프사이클을 자동으로 관리하여 옵저버가 활성 상태인 경우에만 업데이트가 전달됩니다. 반면에 Flow는 기본적으로 라이프사이클을 인식하지 않습니다. Compose에서는 collectLatest() 또는 collectAsStateWithLifecycle() 함수를 사용하여 Flow로부터 결과를 수집할 수 있습니다.
- Flow는 더 많은 유연성을 제공하며 더 복잡한 비동기 데이터 작업에 적합하며, LiveData는 일반적으로 간단한 UI 업데이트에 사용됩니다.
- Flow는 백프레셔(backpressure)를 내장 지원하여 데이터 방출 및 처리 속도를 제어할 수 있지만, LiveData는 백프레셔 처리를 지원하지 않습니다.
- Flow는 순차적 및 구조화된 처리를위한 다양한 연산자를 제공하며, LiveData는 옵저버에게 최신 데이터를 제공하는 데 중점을 둡니다.

읽어 주셔서 감사합니다! 이 기사가 유익하고 즐거우셨기를 바랍니다. 의견은 댓글 섹션에 남겨 주세요.

좋은 코딩 되세요!
