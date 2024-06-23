---
title: "Callback 대신 Coroutine 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowtouseaCoroutineinsteadofCallback_0.png"
date: 2024-06-23 23:28
ogImage: 
  url: /assets/img/2024-06-23-HowtouseaCoroutineinsteadofCallback_0.png
tag: Tech
originalTitle: "How to use a Coroutine instead of Callback?"
link: "https://medium.com/@vishpraveen7/how-to-use-a-coroutine-instead-of-callback-1799284e9e32"
---


콜백을 코루틴으로 대체하는 것은 비동기 작업을 처리하기 위해 콜백을 사용하는 코드를 코루틴 기반 코드로 변환하는 것을 의미합니다. 이렇게하면 코드가 더 읽기 쉽고 유지 관리하기 쉬워집니다. Kotlin에서 코루틴은 복잡한 콜백 체인 없이 비동기 작업을 처리할 수 있습니다.

아래는 콜백 기반 접근 방식을 코루틴으로 대체하는 방법입니다:

콜백 수신을 위한 인터페이스:

```js
interface Callback<T> {
    fun onSuccess(response: T)
    fun onFailure(e: Throwable)
}
```

<div class="content-ad"></div>

추상화 계층 추가를 위한 서비스 인터페이스:

```kotlin
interface FruitService {
    fun fetchFruits(callback: Callback<List<String>>)
}
```

과일 목록을 가져오는 데이터 소스:

```kotlin
class FruitsDataSource : FruitService {
    override fun fetchFruits(callback: Callback<List<String>>) {
        Thread {
            try {
                Thread.sleep(1000) // API 호출을 시뮬레이션하기 위한 지연
                callback.onSuccess(fruits)
            } catch (e: Exception) {
                callback.onFailure(e)
            }
        }.apply {
            start()
            join()
        }
    }

    private companion object {
        val fruits = listOf(
            "사과",
            "망고",
            "체리",
            "바나나",
            "레몬",
            "수박",
            "달콤한 라임",
            "오렌지",
            "키위"
        )
    }
}
```

<div class="content-ad"></div>

API 호출을 하는 저장소:

```js
class FruitsRepository(
    fruitService: FruitService
) {

    private var _service: FruitService = fruitService

    fun fetchFruits(callback: Callback<List<String>>) {
        _service.fetchFruits(callback)
    }
}
```

# 1. Callback 접근 방식 이해

일반적인 콜백 기반 함수부터 시작해봅시다. 비동기 작업을 하는 과일 데이터를 가져오는 작업이 있으며 결과를 반환하기 위해 콜백을 사용한다고 가정해봅시다.

<div class="content-ad"></div>

```kotlin
fun fetchFruitsLegacyWay(repository: FruitsRepository) {
    repository.fetchFruits(object : Callback<List<String>> {
        override fun onSuccess(response: List<String>) {
            println("fetchFruitsLegacyWay: onSuccess: $response ")
        }

        override fun onFailure(e: Throwable) {
            println("fetchFruitsLegacyWay: onFailure: ${e.message} ")
        }
    })
}
```

Callback을 사용하여 API 호출하는 방법:

```kotlin
fun main() {
    val repository = FruitsRepository(LegacyDataSource())
    fetchFruitsLegacyWay(repository)
}
```

# 2. 코루틴으로 변환하기


<div class="content-ad"></div>

이를 코루틴 기반 접근 방식으로 변환하려면 다음 단계를 따르세요:

- 서스펜드 함수 생성: 콜백 함수를 서스펜드 함수로 변경합니다.
- 코루틴 빌더 사용: launch 또는 async를 사용하여 코루틴 스코프 내에서 서스펜드 함수를 호출합니다.
- 예외 처리: 콜백 오류 메서드 대신 코루틴 내에서 try-catch를 사용하여 예외를 처리합니다.

## 단계별 변환

서스펜드 함수 정의: 콜백을 제거하고 함수를 서스펜드로 변경하세요.

<div class="content-ad"></div>

아래 내용을 추가하여 Service Interface를 업데이트하세요:

```kotlin
suspend fun fetchFruits(): List<String> // 새로운 중단 함수
```

아래 코드를 추가하여 FruitsDataSource를 업데이트하세요:

```kotlin
// 과일 목록을 가져오는 중단 함수
override suspend fun fetchFruits(): List<String> {
    delay(1000) // API 호출을 모방하기 위한 지연
    return fruits
}
```

<div class="content-ad"></div>

아래의 코드를 추가하여 FruitsRepository를 업데이트해주세요:

```kotlin
override suspend fun fetchFruits(): List<String>  {
    return _service.fetchFruits()
}
```

사용하는 Suspend Function 호출하기: launch 또는 async와 같은 코루틴 빌더를 사용하여 suspend function을 호출합니다.

## launch를 사용한 예시''

<div class="content-ad"></div>

```js
private suspend fun fetchFruitsUsingCoroutine(repository: FruitsRepository) {
    try {
        println("fetchFruitsUsingCoroutine 성공:${repository.fetchFruits()}")
    } catch (ex: Exception) {
        println("fetchFruitsUsingCoroutine 실패:${ex.message}")
    }
}
```

```js
fun main() = runBlocking {
    // IO 컨텍스트에서 코루틴 실행
    launch(Dispatchers.IO) {
        try {
            val repository = FruitsRepository(LegacyDataSource())
            fetchFruitsUsingCoroutine(repository)
            println(result) // Output: 서버로부터의 과일 목록
        } catch (e: Exception) {
            e.printStackTrace()
        }
    }
}
```

# 3. Async/Await를 사용한 여러 작업 처리

만약 여러 비동기 태스크를 수행해야 한다면, 코루틴은 동시 실행을 위해 async를, 결과를 기다리기 위해 await를 사용하여 더 우아하게 처리할 수 있습니다.

<div class="content-ad"></div>

## 예시: 코루틴을 이용한 여러 작업

```js
fun main() = runBlocking {
  val repository = FruitsRepository(LegacyDataSource())

  val fruits1 = async { repository.fetchFruits() } //API 호출 1 
  val data1 = fruits1.await()
  println("Data1: $data1") // 출력: 서버에서 과일 목록

  val fruits2 = async { repository.fetchFruits() } // API 호출 2
  val data2 = fruits2.await()
  println("Data2: $data2") // 출력: 서버에서 과일 목록
}
```

# 4. 기존 코드 통합

기존 콜백을 사용하는 레거시 시스템과 통합할 때, suspendCoroutine 또는 suspendCancellableCoroutine을 사용하여 콜백 기반 코드를 코루틴으로 래핑할 수 있습니다.

<div class="content-ad"></div>

## 예시: 코루틴을 이용한 콜백 감싸기

```kotlin
private suspend fun fetchFruitsUsingCoroutines(repository: FruitsRepository) {
  suspendCoroutine { continuation ->
      repository.fetchFruits(object : Callback<List<String>> {
          override fun onSuccess(response: List<String>) {
              continuation.resume(response)
          }

          override fun onFailure(e: Throwable) {
              continuation.resumeWithException(e)
          }
      })
  }.let {
      println("fetchFruitsUsingCoroutines: Response: $it")
  }
}
```

# 5. 코루틴 사용의 장점

- 가독성: 코루틴을 사용하면 동기 코드처럼 보이고 동작하는 비동기 코드를 간편하게 작성할 수 있습니다.
- 확장성: 코루틴은 가벼우며 많은 수의 동시 작업을 효율적으로 처리할 수 있습니다.
- 구조화된 병행성: 비동기 작업의 라이프사이클을 보다 효과적으로 관리할 수 있습니다.

<div class="content-ad"></div>

콜백을 코루틴으로 변환하면 코드를 현대화하여 유지 및 확장하기 쉬워집니다.