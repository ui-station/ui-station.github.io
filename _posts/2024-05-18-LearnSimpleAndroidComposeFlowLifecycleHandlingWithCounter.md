---
title: "간단한 안드로이드 Compose Flow 라이프사이클 처리 및 카운터로 배우기"
description: ""
coverImage: "/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_0.png"
date: 2024-05-18 15:24
ogImage: 
  url: /assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_0.png
tag: Tech
originalTitle: "Learn Simple Android Compose Flow Lifecycle Handling With Counter"
link: "https://medium.com/mobile-app-development-publication/learn-simple-android-compose-flow-lifecycle-handling-with-counter-36d62c88a2cd"
---


## 안드로이드 개발 배우기

![이미지](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_0.png)

가끔 안드로이드 라이프사이클을 이해하려고 머리를 싸매면서, 정말 중요한 시나리오는 무엇인지 궁금해 할 때가 있습니다. 이해하지 못하면 적절하게 활용을 놓치게 될 수 있습니다.

여기, 매우 간단한 디자인인 카운터를 고안해 보았습니다. 이를 통해 각종 간단한 라이프사이클 시나리오를 살펴볼 수 있습니다. 도움이 되길 바랍니다.

<div class="content-ad"></div>

# 간단한 흐름

간단한 카운터를 만들기 위해, 제 ViewModel에 아래와 같은 흐름이 있습니다. 매 초마다 1씩 증가하는 흐름입니다.

```js
val counter = flow {
    var value = 0
    while (true) {
       emit(value++)
       delay(1000)
    }
}
```

MainActivity에서 트리거된 Composable 함수에서는, 상태 변수로 수집하고 표시합니다.

<div class="content-ad"></div>

```kotlin
val stateVariable = viewModel.counter.collectAsState(0)
Text("${stateVariable.value}")
```

작동은 됩니다. 하지만 한 가지 문제가 있어요.

## 화면을 회전하면 초기화돼요!

화면을 회전할 때마다(세로에서 가로로 변경할 때) 숫자가 다시 시작돼요!

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_1.png" />

처음에는 카운터가 재설정되어 깜짝 놀랐습니다. ViewModel이 화면 회전(구성 변경)을 통해 유지되는 것에 대해 생각해 보았더니, 화면이 회전될 때마다 MainActivity가 파괴되고 카운터가 다시 수집된다는 것을 깨달았습니다.

```js
val stateVariable = viewModel.counter.collectAsState(0)
```

<div class="content-ad"></div>

각 컬렉션은 새로운 플로우 이벤트를 시작합니다. 따라서 화면을 회전할 때 카운터가 재설정됩니다.

![이미지](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_2.png)

# 외부 변수를 플로우로 변경

이 문제를 해결하기 위해 제가 고안한 해결책은 다음과 같습니다. 카운터 값을 플로우에 저장하는 대신 외부에서 정의하겠습니다.

<div class="content-ad"></div>

```kotlin
var value = 0
val counter = flow {
    while (true) {
       emit(value++)
       delay(1000)
    }
}
```

제 MainActivity에서 트리거된 Composable Function에서는 상태 변수로만 수집하고 표시합니다.

```kotlin
val stateVariable = viewModel.counter.collectAsState(0)
Text("${stateVariable.value}")
```

<img src="/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_3.png" />

<div class="content-ad"></div>

이 방법은 작동합니다. 그리고 꾸준히 계속 작동합니다.

![이미지](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_4.png)

## 백그라운드에서 멈추지 않음

하지만 한 가지 문제가 있습니다. 앱을 백그라운드로 이동시키면 작동이 멈추지 않고 계속 실행됩니다 (활동이 종료되지 않고 계속 활성 상태로 남아 있음).

<div class="content-ad"></div>

<img src="/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_5.png" />

이게 이상적이지 않아요. 우리가 백그라운드로 가면(즉, 액티비티가 onPause되면), 일시 중지되기를 원했고, 다시 포어그라운드로 돌아오면 계속되기를 원했어요.

그 이유는 collectAsState는 라이프사이클 변경을 인식하지 못하기 때문에, 흐름을 멈추거나 일시 중지시키지 못하기 때문이에요.

## CollectAsStateWithLifecycle가 구조안으로 와서 구원을 줍니다

<div class="content-ad"></div>

좋은 소식이 있어요. 구글에서 Manuel Vivo가 공유한 collectAsStateWithLifecycle을 소개했어요.

우리는 이렇게만 하면 돼요:

```kotlin
val stateVariable = viewModel.counter.collectAsStateWithLifecycle(0)
Text("${stateVariable.value}")
```

<img src="/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_6.png" />

<div class="content-ad"></div>

잠시 일시 중단해볼게요

![image](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_7.png)

## 시스템에 의해 종료될 때 상태를 저장하지 않음

ViewModel은 회전되더라도 앱이 계속 실행되도록 할 수 있어 좋습니다. 그러나 시스템에 의해 앱이 종료될 경우 계속 실행되지 않을 수 있습니다. 기기 메모리가 부족한 경우에는 OS가 백그라운드에서 실행 중인 앱을 종료할 수 있습니다.

<div class="content-ad"></div>

이렇게 되면 우리 카운터에 무슨 일이 벌어집니다.

![LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_8](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_8.png)

우리는 savedStateHandle을 사용하여 외부 값 저장 및 복원할 수 있습니다.

```js
var value = savedStateHandle[KEY] ?: 0
val counter = flow {
    while (true) {
       emit(value++)
       savedStateHandle[KEY] = value
       delay(1000)
    }
}
```

<div class="content-ad"></div>

# StateFlow With Lifecycle Aware

하지만 flow를 사용하고 있기 때문에, savedStateHandle에서 stateFlow를 직접 얻을 수 있다면 stateFlow를 사용해보는 것은 어떨까요?

```kotlin
val stateFlow = savedStateHandle.getStateFlow(KEY, 0)
```

flow와는 달리, stateFlow는 hot flow입니다. 이는 stateFlow 자체에서 값이 발행되지 않고, 외부에서 값을 받는다는 것을 의미합니다.

<div class="content-ad"></div>

그래서, ViewModel에서 아래의 간단한 코드를 가지고 있어요.

```js
val stateFlowCounter = savedStateHandle.getStateFlow(KEY, 0)
init {
    viewModelScope.launch {
        while (true) {
           delay(1000)
           savedStateHandle[KEY] = stateFlowCounter.value + 1
        }
    }
}
```

그리고 Activity에서는 아래와 같이 수집도 해요.

```js
val stateVariable 
    = viewModel.stateFlowCounter.collectAsStateWithLifecycle()
Text("${stateVariable.value}")
```

<div class="content-ad"></div>

이 방법은 상태Flow 변수를 저장하고 복원하므로 좋습니다.

![이미지](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_9.png)

그러나 이 방식에는 문제가 있습니다. collectAsStateWithLifecycle를 사용하더라도 수집 부분이 MainActivity에 있습니다.

stateFlow는 핫 플로우이며, 발행 프로세스도 ViewModel에 있기 때문에 발행 프로세스는 라이프사이클을 인식하지 못한 채 계속 실행됩니다.

<div class="content-ad"></div>

```js
init {
    viewModelScope.launch {
        while (true) {
           delay(1000)
           savedStateHandle[KEY] = stateFlowCounter.value + 1
        }
    }
}
```

따라서 결과는 아래와 같습니다.

<img src="/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_10.png" />

회전 문제와 저장 및 복원 문제를 해결했습니다. 그러나 백그라운드에서 일시 중지되지 않는 문제가 되돌아왔습니다.

<div class="content-ad"></div>

아래 이미지를 참고해 주세요.

## WhileSubcribed가 도와 주었어요

답을 찾기 위해 검색한 후, 답을 찾도록 도와준
Manuel Vivo님에게 감사드립니다.

<div class="content-ad"></div>

간략하게 말하자면, stateFlow에 대해 WhileSubscribed를 사용하여 구독자(수집 중인)가 있는 경우에만 stateFlow가 활성화되도록 해야합니다.

이를 위해 아래와 같이 stateIn을 추가해야 합니다.

```js
val stateFlowCounter = savedStateHandle
    .getStateFlow(KEY, 0)
    .stateIn(
        viewModelScope,
        SharingStarted.WhileSubscribed(0),
        0
    )
init {
    viewModelScope.launch {
        while (true) {
           delay(1000)
           savedStateHandle[KEY] = stateFlowCounter.value + 1
        }
    }
}
```

이렇게 해야합니다.

<div class="content-ad"></div>

```kotlin
val stateVariable
    = viewModel.stateFlowCounter.collectAsStateWithLifecycle()
Text("${stateVariable.value}")
```

아래에 최신 코드가 설명되어 있습니다.

<img src="/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_12.png" />

원하는 동작을 모두 갖게 될 것입니다.

<div class="content-ad"></div>

```markdown
![이미지1](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_13.png)

![이미지2](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_14.png)

![이미지3](/assets/img/2024-05-18-LearnSimpleAndroidComposeFlowLifecycleHandlingWithCounter_15.png)

# TL;DR
```

<div class="content-ad"></div>

만약 Google에서 권장하는 Lifecycle Aware한 Flow를 원한다면,

- 보통 플로우나 핫 플로우와 관계없이 collectAsStateWithLifecycle을 사용하세요.
- 모든 핫 플로우 (예: StateFlow)에는 WhileSubscribed를 적용하세요.

이 내용이 유용하고 상태로 수집된 Flow 및 해당 라이프사이클 처리를 설명했기를 바랍니다.

여기에서 코드와 디자인을 가져와 직접 실험해 볼 수 있습니다.