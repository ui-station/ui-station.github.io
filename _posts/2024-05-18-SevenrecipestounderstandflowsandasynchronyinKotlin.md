---
title: "Kotlin에서 플로우와 비동기성 이해를 위한 7가지 레시피"
description: ""
coverImage: "/assets/img/2024-05-18-SevenrecipestounderstandflowsandasynchronyinKotlin_0.png"
date: 2024-05-18 17:11
ogImage: 
  url: /assets/img/2024-05-18-SevenrecipestounderstandflowsandasynchronyinKotlin_0.png
tag: Tech
originalTitle: "Seven recipes to understand flows and asynchrony in Kotlin"
link: "https://medium.com/proandroiddev/seven-recipes-to-understand-flows-and-asynchrony-in-kotlin-1bd7fe041480"
---


코틀린 코루틴의 깨끗한 세계에서는 다양한 시간에 실행되는 많은 작업들이 놀랍게도 적은 스레드로 쌓이게 됩니다. 이러한 코루틴들은 종종 서로 통신을 해야합니다. 예를 들어 비동기 작업이 완료되어 결과를 보고해야 하거나 진행 중인 작업이 결과를 전달해야 할 때입니다.

코틀린에는 이러한 통신을 관리하는 데 도움이 되는 여러 구조가 있습니다. 서스펜드 함수는 비동기 작업을 기다리는 데 도움이 되며, 여러 결과를 기대하는 경우 플로우가 필요합니다.

이 기사에서는 일반적으로 비동기 처리에 대해 예시를 들고, 플로우를 소개합니다. 플로우가 왜 필요한지, 핫 플로우와 콜드 플로우의 차이, 그리고 콜백을 서스펜드 함수와 플로우로 변환하는 방법에 대해 알아보겠습니다.

# 레시피 1: 자바 콜백, 어린날로의 돌아가기

<div class="content-ad"></div>

우선, 여기에는 일시 중단 함수나 플로우가 아닌 것이 있습니다. Firestore에서 문서를 가져오는 방법은 다음과 같습니다:

```js
Firebase.firestore.collection("users").document("me")
  .get()   // "me" 문서의 비동기 다운로드 시작
  .addOnSuccessListener { result ->
    // 다운로드가 완료될 때이 함수가 호출됩니다
  }
```

이는 비동기 콜백 패턴입니다. 요청을 하면 작업이 비동기적으로 수행되고 나중에 결과가 돌아옵니다.

Java와 하위 호환성을 유지하며 작성된 API는 이와 같은 비동기 콜백을 가득 가지고 있습니다. 이 패턴은 오래된 것으로 간주되므로 Kotlin 스타일이 아닙니다.

<div class="content-ad"></div>

# 레시피 2: Kotlin을 사용하여 콜백 처리하기: suspendCoroutine

요청한 내용을 받고 나줌에 결과를 받는 상황을 처리하는 더 나은 방법은 코틀린 코루틴의 강력한 기능을 활용하는 대기 함수를 사용하는 것입니다. 제 최근 블로그 포스트에서 대기 함수에 대해 자세히 설명했었는데, 요약하자면: 대기 함수는 현재 코루틴을 일시 중단하지만 실행 중인 스레드를 차단하지 않습니다.

좋은 소식은 콜백을 대기 함수로 변환할 수 있다는 것입니다*. 필요한 함수는 suspendCoroutine입니다:

```js
suspend fun getDocument() = suspendCoroutine { continuation ->
  Firebase.firestore.collection("users").document("me")
    .get()
    .addOnSuccessListener { result ->
      // 받은 문서를 사용하여 일시 중단된 코루틴을 다시 실행합니다.
      continuation.resume(result)
    }
}
```

<div class="content-ad"></div>

좋아요. 이제는 getDocument()를 호출하는 방법이 훨씬 간단해졌어요. 예를 들어 ViewModel에서:

```js
viewModelScope.launch(Dispatchers.IO) {
    val location = getDocument()
}
```

Java의 모든 부가 기능을 제거한 Kotlin이 얼마나 아름다운지 보세요. 함수를 한 줄로 호출할 수 있어요. 아름다워요.

# 안티-레시피 1: 일회성 함수는 한 번만 반환할 수 있어요.

<div class="content-ad"></div>

하지만 만일 우리가 하나 이상의 응답을 예상하고 있다면 어떻게 해야 할까요?

Firestore에게 문서의 새 버전을 얻을 때마다 업데이트를 유지하도록 요청할 수 있습니다. 이를 위해 addOnSnapshotListener를 사용하고 Firestore는 업데이트가 있을 때마다 리스너를 호출합니다:

```js
Firebase.firestore.collection("users").document("me")
  .addOnSnapshotListener { snapshot, error ->
    // 이 함수는 "me" 문서가 업데이트될 때마다 호출됩니다.
  }
```

이것을 대기 함수로 변경할 수 있을까요? 아뇨! 대기 함수는 한 번만 반환합니다. 여러 번 continuation.resume()을 호출하면 IllegalStateException("이미 재개되었습니다")가 발생합니다.

<div class="content-ad"></div>


![Recipe 3](/assets/img/2024-05-18-SevenrecipestounderstandflowsandasynchronyinKotlin_0.png)

So we need something different. What we need is a flow.

# Recipe 3: Using callbackFlow to return multiple things from an async callback

When an object hits the flow’s conveyor belt, we say it’s been emitted by the flow. When it gets taken off the conveyor belt to be handled, we say it’s been collected by a collector.


<div class="content-ad"></div>

우리의 Firestore 예제에서는 문서 데이터가 로드되는 플로우를 생성해야 합니다. 나중에 그것을 수집하는 코드를 작성할 것입니다.

## 멀티 샷 콜백을 플로우로 변환하기: callbackFlow

여기서 우리는 흐름 "컨베이어 벨트"를 생성하고 업데이트된 문서를 올립니다. 업데이트된 문서는 콜백에서 얻은 스냅샷으로 나타납니다:

```js
// callbackFlow를 사용하여 플로우 "컨베이어 벨트"를 만듭니다.
val documentFlow = callbackFlow {

  // Firestore에게 "me" 문서의 변경 사항을 계속 업데이트하라고 요청합니다.
  database.collection("users").document("me")
    .addOnSnapshotListener { snapshot, error ->
      // 업데이트된 문서를 이 플로우의 "컨베이어 벨트"에 올립니다.
      trySend(snapshot)
    }
}
```

<div class="content-ad"></div>

이는 Firestore에서 받은 각 스냅샷을 방출하는 flow 객체를 생성합니다.

# 레시피 4: flow 수집하기

이제 이 컨베이어 벨트를 설정했고, documentFlow라는 변수에 할당했습니다. 결과를 어떻게 얻을까요?

답변: collect 함수를 사용하여 결과를 수집합니다.

<div class="content-ad"></div>

```kotlin
viewModelScope.launch {
  documentFlow.collect { snapshot ->
    // This is the snapshot placed on the conveyor belt earlier
  }
}
```

이 함수는 일시 중단되므로 코루틴 내에서 실행해야 합니다. collect에 전달하는 람다는 새 항목이 컨베이어 벨트에 나타날 때마다 호출됩니다.

이것의 라이프사이클을 좀 고려해보죠. collect 함수는 컨베이어 벨트가 작동을 멈출 때까지 일시 중단됩니다.

플로우 컨베이어 벨트가 작동을 멈출 수 있는 방법은 두 가지뿐입니다:


<div class="content-ad"></div>

- 종료로: 발신자가 더 이상 전달할 것이 없으면 close()를 호출하여 컨베이어 벨트가 멈추도록 요청합니다.
- 오류로: 발신자가 예외를 throw합니다. 이로 인해 자동으로 흐름이 닫힙니다.

물론 위의 조건 중 어느 것도 발생하지 않을 수 있어서 컨베이어가 멈출 수도 없습니다. 따라서 collect 호출은 무한 루프를 나타낼 수 있습니다. 그러나 코루틴의 진정한 매력은 collect 함수가 취소 가능하다는 것입니다. 따라서 위의 예제에서는 viewModelScope가 취소되는 즉시 수집이 중지됩니다. 그렇기 때문에 위의 코드는 사실 완전히 안전합니다. 코루틴 범위, 컨텍스트, 작업에 대한 더 많은 내용은 블로그 글에서 확인하실 수 있습니다.

# 레시피 5: Cold flows와 awaitClose

callbackFlow 람다 내의 코드는 수집기가 수집을 시작할 때 즉시 실행됩니다. 각 새로운 수집기는 다른 수집기와 병렬로 실행 중이더라도 코드가 다시 실행되게 합니다. 이것이 콜드 플로우 동작이라고 알려져 있습니다.

<div class="content-ad"></div>

이에 따라, 수집기가 수집을 멈출 때 Firestore 연결을 종료해야 합니다. 'awaitClose'를 사용하여 이를 자동으로 처리할 수 있습니다:

```js
val documentFlow = callbackFlow {
  val listener = database.collection("users").document("me")
    .addOnSnapshotListener { snapshot, error ->
      ...
    }
  
  // 이 코드 블록은 수집기가 수집을 멈출 때마다 실행됩니다
  awaitClose {
    // Firestore 연결 종료
    listener.remove()
  }
}
```

'awaitClose' 블록은 수집기가 수집을 멈출 때마다 실행됩니다. 이를 사용하여 원격 데이터베이스의 리스너를 등록 해제합니다.

우리의 더 높은 수준의 코드를 수정할 필요는 없습니다: 우리는 여전히 레시피 4의 문서 플로우 수집을 위해 documentFlow.collect를 호출합니다. 모든 것이 요구될 때 필요한대로 모두 종료되도록 보장하는 데 필요한 다른 작업은 없습니다.

<div class="content-ad"></div>

# 레시피 6: SharedFlow 및 여러 수집기

알고 계세요. callbackFlow 람다의 코드는 수집기가 수집을 시작하는 즉시 실행됩니다. 즉, 동시에 100개의 수집기가 작동하는 경우 Firestore로부터 100개의 연결이 열리게 됩니다. 한 개로도 충분히 해결할 수 있는 상황에서 매우 비효율적입니다.

실제로 우리가 원하는 것은 여러 수집기를 사용할 수 있는 flow 입니다. 이 flow는 첫 번째 수집기가 데이터 수집을 시작할 때 Firestore 연결을 시작하고, 마지막 수집기가 멈출 때 해당 연결을 종료해야 합니다. 해당 시간 사이에 도착하는 다른 수집기는 새로운 Firestore 연결을 열지 않고 데이터의 사본을 받아야 합니다.

다시 말해, flow의 지속적인 이벤트와 개별 수집기의 도착/사라짐 간의 연계를 어느 정도 분리하고자 합니다. 더 이상 냉각된 flow를 사용하고 싶지 않으며, hot flow를 원합니다. hot flow의 수집기를 구독자(subscriber)라고 합니다 — 이러한 언어의 약간의 변경은 flow가 수집자와 독립적으로 계속되는 사실을 강조하기 위한 것입니다. 구독자는 단지 "확인"하는 역할을 하며, flow를 운전하는 것이 아닙니다.

<div class="content-ad"></div>

여기서 필요한 특정 종류의 핫 플로우는 SharedFlow입니다. SharedFlow는 데이터를 여러 구독자에게 방송하는 데 사용됩니다. 예를 들어 데이터를 SharedFlow 컨베이어 벨트에로드하는 코루틴이 여기에 있습니다.

해당 코루틴이 실행중인 한 데이터는 컨베이어 벨트를 따라 이동합니다. 누군가 데이터를 수집하고 있든 상관없습니다.

플로우가 흐르기 시작한 정확한 시점에 구독하면 순차적으로 0, 1, 2, 3, 4...가 출력됩니다. 그러나 몇 초 후에 수집을 시작하면 처음 몇 번의 에미션이 놓치고 7, 8, 9, 10, 11...이 출력됩니다. 이것은 플로우가 수집을 시작하기 전에 이미 흐르기 시작했음을 보여줍니다.

<div class="content-ad"></div>

## 레시피 7: 차가운 플로우를 공유해서 뜨겁게 만들기

`shareIn()` 함수는 차가운 플로우를 가져와서 뜨겁게 만드는 데 사용됩니다. 즉, 이 함수는 코루틴을 시작하여 차가운 플로우를 수집하고 받은 모든 것을 구독자에게 다시 방송합니다. 구독자의 수는 0을 포함하여 제한이 없을 수 있습니다.

다음은 `shareIn()` 함수의 정의입니다:

```js
fun <T> Flow<T>.shareIn(
    scope: CoroutineScope, 
    started: SharingStarted, 
    replay: Int = 0
): SharedFlow<T>
```

<div class="content-ad"></div>

또한 Android Activity는 SharingStarted.WhileSubscribed()와 stopTimeoutMillis를 사용하는 예시입니다. 액티비티는 장치가 회전될 때 종료되고 다시 시작되기 때문에 해당 프로세스 중 구독자는 몇 밀리초 동안 사라집니다. 이를 방지하기 위해 코드 플로우를 즉시 종료하고 다시 시작하지 않도록하기 위해 shareIn의 coroutine은 5000밀리초 동안 유지됩니다.

<div class="content-ad"></div>

마침내, replay 매개변수를 사용하여 구독자가 처음 구독할 때 현재 및 이전 값도 받을 수 있습니다.

# 요약하면...

- suspendCoroutine을 사용하여 비동기 단일 호출 콜백을 일시 중단 함수로 변환할 수 있습니다.
- 다중 호출 콜백이 있는 경우 플로우가 필요합니다. callbackFlow를 사용하여 다중 호출 콜백을 플로우로 변환할 수 있습니다.
- callbackFlow에서 생성된 것과 같은 Cold flows는 각 수집기에 대해 별도로 실행됩니다.
- Hot flows는 수집기(구독자라고도 불림)와 독립적으로 실행됩니다.
- SharedFlow와 같은 Hot flows 예는 여러 구독자에게 배출을 전파하는 SharedFlow입니다. cold flow를 sharedIn()를 사용하여 SharedFlow로 변환할 수 있습니다.

도움이 되었기를 바랍니다. 모든 의견을 환영합니다!

<div class="content-ad"></div>

코틀린에서의 비동기 처리는 매우 넓은 주제입니다. 그 중에서도 특히 플로우(Flow)는 다양한 기능이 있습니다. 이에 대해 특정 소주제에 초점을 맞춘 더 많은 기사를 쓸 예정이니, 댓글이나 LinkedIn에서 어떤 내용이 도움이 될지 알려주세요.

* Firestore 팬 여러분을 위해 말하자면, kotlinx-coroutines-play-services 라이브러리의 await()을 사용하여 이 특정 콜백을 중지할 수 있는 작업으로 바꿀 수 있다는 사실을 지적할 것입니다. 맞아요, 당연히 맞습니다. 하지만 이렇게 멋진 예제를 볼 수 없을 테지요? 🙃

** Firebase는 실제로 그것 이상으로 똑똑하며, 여러 연결을 열지 않도록 합니다. 위를 참조하세요.

톰 콜빈(Tom Colvin)은 안드로이드의 Google 개발 전문가로, 20년간 소프트웨어 아키텍처를 설계해왔습니다. 그는 모바일 앱 전문가인 Apptaura의 공동 창업자이자 CTO이며, 컨설팅을 통해 사용 가능합니다.