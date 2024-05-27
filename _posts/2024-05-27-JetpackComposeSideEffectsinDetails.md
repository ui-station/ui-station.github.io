---
title: "젯팩 컴포즈 부가 효과 자세히 알아보기"
description: ""
coverImage: "/assets/img/2024-05-27-JetpackComposeSideEffectsinDetails_0.png"
date: 2024-05-27 16:11
ogImage: 
  url: /assets/img/2024-05-27-JetpackComposeSideEffectsinDetails_0.png
tag: Tech
originalTitle: "Jetpack Compose Side Effects in Details"
link: "https://medium.com/@mortitech/exploring-side-effects-in-compose-f2e8a8da946b"
---


```markdown
![Jetpack Compose](/assets/img/2024-05-27-JetpackComposeSideEffectsinDetails_0.png)

Jetpack Compose는 Android에서 UI 개발을 훨씬 쉽게 만들었지만, UI 효과를 효율적으로 관리하는 방법을 이해하는 것이 성능 향상에 매우 중요합니다. 본 문서에서는 UI 효과를 효과적으로 관리하는 데 도움이 되는 세 가지 중요한 Composable 함수인 SideEffect, LaunchedEffect 및 DisposableEffect을 살펴보겠습니다.

# 왜 부작용(side-effect)이 필요한가요?

Jetpack Compose에서 부작용의 목적은 Composable 함수 외부에서 앱의 상태를 변경하는 UI와 관련 없는 작업을 제어 가능하고 예측 가능한 방식으로 실행할 수 있게 하는 것입니다.
```

<div class="content-ad"></div>

부작용은 데이터베이스 업데이트나 네트워크 호출과 같은 작업을 UI 렌더링 로직과 분리하여 코드의 성능과 유지 보수성을 향상시켜야 합니다.

젯팩 컴포즈는 SideEffect, LaunchedEffect, DisposableEffect와 같은 여러 컴포저블 함수를 제공하여 개발자가 사이드 이펙트를 효과적으로 관리할 수 있도록 해줍니다. 이를 통해 UI 렌더링 로직과 분리하고 별도의 코루틴 범위에서 실행함으로써 사이드 이펙트를 처리할 수 있습니다.

젯팩 컴포즈에서 사이드 이펙트를 사용하는 주요 이점은 다음과 같습니다:

- 성능 향상: 비 UI 관련 작업을 컴포저블 함수 외부에서 실행함으로써 UI 렌더링 로직이 반응성이 있고 성능이 좋아집니다.
- 코드 구성 개선: UI 렌더링 로직에서 비 UI 관련 작업을 분리함으로써 코드베이스가 이해하기 쉬워지고 유지 보수가 용이해집니다.
- 디버깅 개선: 사이드 이펙트는 로깅 및 분석 작업에 사용될 수 있으며, 이를 통해 개발자가 앱의 동작을 더 잘 이해하고 문제점을 식별할 수 있습니다.

<div class="content-ad"></div>

요약하면 Jetpack Compose에서의 부작용의 목적은 UI 렌더링 로직에서 UI와 상관없는 작업을 분리함으로써 코드베이스의 성능, 유지 관리 및 디버깅을 개선하는 것입니다.

## SideEffect

SideEffect는 부모 Composable이 recomposed될 때 부작용을 실행할 수 있게 해주는 Composable 함수입니다. 부작용이란 직접적으로 UI에 영향을 주지 않는 작업으로, 로깅, 분석 또는 외부 상태 갱신과 같은 작업을 의미합니다. 이 함수는 Composable의 상태나 props에 의존하지 않는 작업을 실행하는 데 유용합니다.

Composable이 recomposed되면 Composable 함수 내의 모든 코드가 다시 실행되며, 이 과정에서 모든 부작용도 다시 실행됩니다. 그러나 UI는 Composable의 상태나 props에 대한 변경 사항만 반영하여 업데이트됩니다.

<div class="content-ad"></div>

# SideEffect를 사용하는 방법

SideEffect를 사용하려면 Composable 함수 내부에서 호출하고 실행하려는 사이드 이펙트를 포함한 람다를 전달해야 합니다. 다음은 예시입니다:

```js
@Composable
fun Counter() {
    // Count를 위한 상태 변수 정의
    val count = remember { mutableStateOf(0) }

    // SideEffect를 사용하여 count 변수의 현재 값 로깅
    SideEffect {
        // 재구성할 때마다 호출됨
        log("Count is ${count.value}")
    }

    Column {
        Button(onClick = { count.value++ }) {
            Text("Increase Count")
        }

        // 상태가 업데이트될 때마다 텍스트가 변경되고 재구성이 트리거됩니다
        Text("Counter ${count.value}")
    }
}
```

이 예시에서 SideEffect 함수는 Counter 함수가 재구성될 때마다 count 상태 변수의 현재 값이 로깅됩니다. 이는 디버깅 및 Composable 동작 모니터링에 유용합니다.

<div class="content-ad"></div>

현재 Composable 함수가 recomposed될 때만 부작용이 발생합니다. 중첩된 Composable 함수에는 해당되지 않습니다. 즉, 다른 Composable 함수를 호출하는 Composable 함수가 있는 경우, 내부 Composable 함수가 recomposed될 때 외부 Composable 함수의 SideEffect가 트리거되지 않습니다. 이를 이해하기 위해 코드를 다음과 같이 변경해보겠습니다:

```js
@Composable
fun Counter() {
    // count를 위한 상태 변수 정의
    val count = remember { mutableStateOf(0) }
    
    // count의 현재 값 로그 남기기 위해 SideEffect 사용
    SideEffect {
        // 재구성마다 호출됨
        log("Count is ${count.value}")
    }
    
    Column {
        Button(onClick = { count.value++ }) {
            // 버튼이 탭될 때마다 바깥쪽 부작용이 트리거되지 않음
            // 매번 버튼이 탭될 때마다 Text가 recompose됨
            Text("Increase Count ${count.value}")
        }
    }
}
```

위의 코드에서 앱이 처음 시작될 때, Counter Composable 함수가 compose되고 SideEffect가 count의 초기 값으로 console에 로그를 남깁니다. 버튼을 클릭하면 Text Composable이 count의 새 값으로 recompose되지만, 이는 SideEffect를 다시 트리거하지 않습니다.

이제 이 작동 방식을 보기 위해 inner side effect를 추가해봅시다.

<div class="content-ad"></div>

```kotlin
@Composable
fun Counter() {
    // 카운트를 위한 상태 변수 정의
    val count = remember { mutableStateOf(0) }

    // SideEffect를 사용하여 count의 현재 값 로깅
    SideEffect {
        // 재구성마다 호출
        log("Outer Count is ${count.value}")
    }

    Column {
        Button(onClick = { count.value++ }) {
            // SideEffect를 사용하여 count의 현재 값 로깅
            SideEffect {
                // 재구성마다 호출
                log("Inner Count is ${count.value}")
            }

            // 이 재구성은 버튼을 누를 때마다
            // 외부 SideEffect를 계속해서 트리거하지 않습니다
            Text("Increase Count ${count.value}")
        }
    }
}
```

위의 코드를 사용하여 버튼을 클릭하면 출력 결과가 다음과 같이 나타납니다:

```kotlin
Outer Count is 0
Inner Count is 0
Inner Count is 1
Inner Count is 2
Inner Count is 3
```

지금 이유를 알 것 같아요 :)

<div class="content-ad"></div>

# LaunchedEffect

LaunchedEffect은 별도의 코루틴 스코프에서 부작용을 실행하는 Composable 함수입니다. 이 함수는 UI 스레드를 차단하지 않고 네트워크 호출 또는 애니메이션과 같은 오랜 시간이 걸리는 작업을 실행하는데 유용합니다.

아래는 LaunchedEffect를 사용하는 예시입니다:

```kt
@Composable
fun MyComposable() {
    val isLoading = remember { mutableStateOf(false) }
    val data = remember { mutableStateOf(listOf<String>()) }

    // LaunchedEffect를 정의하여 오랜 시간 동안 비동기적으로 작업을 수행합니다
    // `isLoading.value`가 변경될 때마다 `LaunchedEffect`가 취소되고 다시 시작됩니다
    LaunchedEffect(isLoading.value) {
        if (isLoading.value) {
            // 네트워크에서 데이터를 가져오는 등 오랜 시간 동안 실행해야 할 작업을 수행합니다
            val newData = fetchData()
            // 새 데이터로 상태를 업데이트합니다
            data.value = newData
            isLoading.value = false
        }
    }

    Column {
        Button(onClick = { isLoading.value = true }) {
            Text("데이터 가져오기")
        }
        if (isLoading.value) {
            // 로딩 인디케이터를 표시합니다
            CircularProgressIndicator()
        } else {
            // 데이터를 표시합니다
            LazyColumn {
                items(data.value.size) { index ->
                    Text(text = data.value[index])
                }
            }
        }
    }
}

// 2초 동안 코루틴을 일시 중단하여 네트워크 호출 시뮬레이션
private suspend fun fetchData(): List<String> {
    // 네트워크 지연 시간을 시뮬레이션합니다
    delay(2000)
    return listOf("항목 1", "항목 2", "항목 3", "항목 4", "항목 5",)
}
```

<div class="content-ad"></div>

이 예시에서 LaunchedEffect 함수는 isLoading 상태 변수가 true로 설정될 때 네트워크 호출을 실행하여 API에서 데이터를 가져옵니다. 이 함수는 별도의 코루틴 스코프에서 실행되므로 작업이 수행되는 동안 UI가 반응할 수 있게 유지됩니다.

LaunchedEffect 함수는 두 개의 매개변수를 사용합니다: key는 isLoading.value로 설정되고 block은 실행할 부작용을 정의하는 람다입니다. 이 경우 block 람다는 fetchData() 함수를 호출하며, 이 함수는 코루틴을 2초 동안 일시 중단하여 네트워크 호출을 시뮬레이트합니다. 데이터를 가져오면 데이터 상태 변수를 업데이트하고 isLoading을 false로 설정하여 로딩 지시자를 숨기고 가져온 데이터를 표시합니다.

## key 매개변수 뒤의 논리는 무엇인가요?

LaunchedEffect에서 key 매개변수는 LaunchedEffect 인스턴스를 식별하고 불필요하게 recompose되지 않도록 방지하는 데 사용됩니다.

<div class="content-ad"></div>

컴포저가 recomposed될 때, Jetpack Compose는 다시 그려야 하는지 여부를 결정합니다. 만약 Composable의 상태나 프로퍼티가 변경되었거나 Composable이 invalidate를 호출했다면, Jetpack Compose는 Composable을 다시 그릴 것입니다. Composable을 다시 그리는 것은 비용이 많이 드는 작업일 수 있습니다. 특히 Composable에 장기 실행 작업이 포함되어 있거나 매번 recomposed될 때마다 다시 실행할 필요가 없는 부작용이 있는 경우에도 그렇습니다.

LaunchedEffect에 key 매개변수를 제공함으로써, LaunchedEffect 인스턴스를 고유하게 식별하는 값을 지정할 수 있습니다. key 매개변수의 값이 변경되면, Jetpack Compose는 LaunchedEffect 인스턴스를 새로운 인스턴스로 간주하고 부작용을 다시 실행할 것입니다. key 매개변수의 값이 동일하면, Jetpack Compose는 부작용의 실행을 건너뛰고 이전 결과를 재사용하여 불필요한 recomposition을 방지할 것입니다.

LaunchedEffect에 여러 키를 사용할 수도 있습니다:

```js
// LaunchedEffect에 임의의 UUID를 키로 사용합니다
val key = remember { UUID.randomUUID().toString() }

LaunchedEffect(key, isLoading.value) {
  ....
}
```

<div class="content-ad"></div>

# DisposableEffect

DisposableEffect은 부모 Composable이 처음으로 렌더링될 때 사이드 이펙트를 실행하고, Composable이 UI 계층구조에서 제거될 때 효과를 삭제하는 Composable 함수입니다. 이 함수는 이벤트 리스너나 애니메이션과 같이 Composable이 더 이상 사용되지 않을 때 정리해야 하는 리소스를 관리하는 데 유용합니다.

다음은 DisposableEffect를 사용하는 예시입니다:

```js
@Composable
fun TimerScreen() {
    val elapsedTime = remember { mutableStateOf(0) }

    DisposableEffect(Unit) {
        val scope = CoroutineScope(Dispatchers.Default)
        val job = scope.launch {
            while (true) {
                delay(1000)
                elapsedTime.value += 1
                log("Timer is still working ${elapsedTime.value}")
            }
        }

        onDispose {
            job.cancel()
        }
    }

    Text(
        text = "Elapsed Time: ${elapsedTime.value}",
        modifier = Modifier.padding(16.dp),
        fontSize = 24.sp
    )
}
```

<div class="content-ad"></div>

이 코드에서는 DisposableEffect를 사용하여 매 초마다 elapsedTime 상태 값을 증가시키는 코루틴을 실행합니다. 또한 DisposableEffect를 사용하여 Composable이 사용되지 않을 때 코루틴이 취소되고 코루틴에서 사용하는 리소스가 정리되도록 합니다.

DisposableEffect의 정리 함수에서 우리는 job에 저장된 Job 인스턴스의 cancel() 메서드를 사용하여 코루틴을 취소합니다.

onDispose 함수는 Composable이 UI 계층에서 제거될 때 호출되며, Composable에서 사용된 모든 리소스를 정리하는 방법을 제공합니다. 이 경우에는 onDispose를 사용하여 코루틴을 취소하고 코루틴에서 사용하는 모든 리소스가 정리되도록 합니다.

이 DisposableEffect가 작동하는 방법을 확인하려면 다음 코드를 실행하여 결과를 확인해보세요:

<div class="content-ad"></div>

```kotlin
@Composable
fun RunTimerScreen() {
    val isVisible = remember { mutableStateOf(true) }

    Column(
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Bottom
    ) {
        Spacer(modifier = Modifier.height(10.dp))

        if (isVisible.value)
            TimerScreen()

        Button(onClick = { isVisible.value = false }) {
           Text("타이머 숨기기")
        }
    }
}
```

새로운 RunTimerScreen Composable을 추가했습니다. 이를 통해 사용자는 TimerScreen의 가시성을 토글할 수 있습니다. 사용자가 "타이머 숨기기" 버튼을 클릭하면 TimerScreen Composable이 UI 계층에서 제거되고 코루틴이 취소되고 정리됩니다.

onDispose 함수에서 job.cancel() 호출을 제거하면, TimerScreen Composable이 더 이상 사용되지 않더라도 코루틴이 계속 실행되어 메모리 누수 및 다른 성능 문제를 야기할 수 있습니다.

DisposableEffect와 CoroutineScope를 이렇게 함께 사용함으로써, CoroutineScope에서 시작된 코루틴이 TimerScreen Composable이 더 이상 사용되지 않을 때 취소되고 리소스가 정리되도록 합니다. 이를 통해 메모리 누수 및 다른 성능 문제를 방지하고 앱의 성능과 안정성을 향상시킬 수 있습니다.
```

<div class="content-ad"></div>

# 각각 사용하는 시기

DisposableEffect 사용 사례

- 이벤트 리스너 추가 및 제거
- 애니메이션 시작 및 중지
- 카메라, 위치 관리자 등과 같은 센서 자원 바인딩 및 언바인딩
- 데이터베이스 연결 관리

LaunchedEffect 사용 사례

<div class="content-ad"></div>

- 네트워크로부터 데이터 가져오기
- 이미지 처리 수행
- 데이터베이스 업데이트

SideEffect의 사용 사례

- 로깅 및 분석
- 블루투스 장치에 연결 설정, 파일에서 데이터 불러오기 또는 라이브러리 초기화와 같은 일회성 초기화 수행.

한 번 설정할 때 SideEffect를 사용하는 예시:

<div class="content-ad"></div>

```kotlin
@Composable
fun MyComposable() {
    val isInitialized = remember { mutableStateOf(false) }

    SideEffect {
        if (!isInitialized.value) {
            // 여기에 일회성 초기화 작업 실행
            initializeBluetooth()
            loadDataFromFile()
            initializeLibrary()
            
            isInitialized.value = true
        }
    }

    // UI 코드 여기에 작성
}
```

# 요약

SideEffect, DisposableEffect 및 LaunchedEffect 간의 차이 요약:

- SideEffect는 부모 Composable이 recomposed될 때 실행되며, Composable의 상태나 props에 의존하지 않는 작업을 실행하는 데 유용합니다.
- DisposableEffect는 부모 Composable이 처음 렌더링될 때 실행되며, Composable이 더 이상 사용되지 않을 때 정리해야하는 리소스를 관리하는 데 유용합니다. 첫 번째 구성 또는 키 변경시 트리거되며, 종료시 onDispose 메서드를 호출합니다.
- LaunchedEffect는 별도의 코루틴 스코프에서 부작용을 실행하며, UI 스레드를 블로킹하지 않고 긴 작업을 실행하는 데 유용합니다. 첫 번째 구성 또는 키 변경시 트리거됩니다.
```