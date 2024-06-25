---
title: "코틀린 고급 주제 필수"
description: ""
coverImage: "/assets/img/2024-06-19-KotlinAdvancedTopicsEssential_0.png"
date: 2024-06-19 13:48
ogImage:
  url: /assets/img/2024-06-19-KotlinAdvancedTopicsEssential_0.png
tag: Tech
originalTitle: "Kotlin Advanced Topics (Essential)"
link: "https://medium.com/@navinprasad/kotlin-advanced-topics-b0786f63a814"
---

Kotlin Multiplatform (KMP)의 공식 릴리스와 안드로이드와의 통합으로 Kotlin은 엄청난 인기를 얻고 있습니다. Kotlin의 매력은 다양성에 있습니다. 개발자들이 단일 언어를 습득하고 백엔드, 프론트엔드(Android, iOS 및 웹)를 포함한 여러 플랫폼에서 활용할 수 있습니다. Kotlin이 계속 성장함에 따라 개발자들은 필수적이고 고급 주제를 탐색하여 기술을 향상시킬 수 있어야 합니다. 이를 통해 예외적인 코딩 표준을 유지하고 산업에서 앞서 나갈 수 있습니다.

![img](/assets/img/2024-06-19-KotlinAdvancedTopicsEssential_0.png)

### 위임

Kotlin에서의 위임은 객체가 일부 책임을 다른 객체에 위임할 수 있도록 하는 디자인 패턴입니다. Kotlin은 by 키워드를 사용하여 위임에 대한 내장 지원을 제공합니다. Kotlin에서는 클래스 위임과 속성 위임 두 가지 주요 위임 유형이 있습니다.

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

## 속성 위임

lazy 위임은 속성이 처음 액세스 될 때만 초기화됩니다. 이는 비용이 많이 드는 객체 초기화나 프로그램 실행 중에만 필요한 속성에 유용합니다. 이를 통해 리소스 사용을 최적화하고 응용 프로그램 성능을 향상시킬 수 있습니다. 기본적으로 lazy 초기화는 스레드 안전합니다.

```js
val myName: String by lazy {
    println("계산됨")
    "내 이름"
}
```

## 클래스 위임

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

클래스 위임은 한 클래스가 다른 클래스로 메서드 구현을 위임할 수 있게 합니다. 이는 합성을 위해 유용하며 상속 없이 다른 클래스의 동작을 통합할 수 있도록 합니다. 인터페이스 구현 또는 기능을 다른 클래스로 위임함으로써 코드 중복을 피할 수 있습니다.

```kotlin
    interface Weather {
        fun currentWeather()
    }

    class Summer : Weather {
        override fun currentWeather() {
            println("Current weather is ${javaClass.simpleName}")
        }
    }

    class HolidayPlans(weather: Weather) : Weather by weather {
    }
```

# 확장 함수

Kotlin의 확장 함수를 사용하면 코드의 재사용성을 높이기 위해 우리만의 유틸리티 함수를 작성할 수 있습니다. 내부적으로 확장 함수는 컴파일 시점에 클래스에 대해 정적으로 해결되며 해당 클래스의 공개 멤버에 액세스할 수 있도록 합니다(소스 코드를 수정하지 않고).

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
val EMAIL_ADDRESS_PATTERN = Pattern.compile(
        "[a-zA-Z0-9\\+\\.\\_\\%\\-\\+]{1,256}\\@[a-zA-Z0-9][a-zA-Z0-9\\-]{0,64}(\\.[a-zA-Z0-9][a-zA-Z0-9\\-]{0,25})+"
    )

    private fun String.isEmail() =
        EMAIL_ADDRESS_PATTERN.matcher(this).matches()

    fun verifyCredential(emailId: String) {
        println(emailId.isEmail())
    }
```

# 고차 함수

고차 함수를 사용하면 함수를 인수로 사용하거나 함수를 반환하거나 둘 다를 할 수 있습니다. 이를 통해 추상 코드를 생성하고 특정 상황에서 함수 구현을 선언하고 다른 상황에서 실행할 수 있습니다. 이를 통해 강력한 추상화와 더 깨끗하고 모듈화된 코드를 작성할 수 있습니다.

## 기본 고차 함수

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
fun calculate(a: Int, b: Int, operator: (Int, Int) -> Int) {
  operator(a, b)
}

fun main() {
    val sum = calculate(5, 3) { a, b -> a + b }
    println("합계: $sum") // 합계: 8
    val product = calculate(5, 3) { a, b -> a * b }
    println("곱셈: $product") // 곱셈: 15
}
```

## 함수 반환

```kotlin
fun operation(op: String): (Int, Int) -> Int {
    return when (op) {
        "add" -> { a, b -> a + b }
        "multiply" -> { a, b -> a * b }
        else -> { _, _ -> 0 }
    }
}

fun main() {
    val addOperation = operation("add")
    println("덧셈: ${addOperation(2, 3)}") // 덧셈: 5
    val multiplyOperation = operation("multiply")
    println("곱셈: ${multiplyOperation(2, 3)}") // 곱셈: 6
}
```

- inline: 컴파일러에게 함수의 바이트코드를 호출 지점에 직접 넣도록 요청합니다. 함수 호출 및 람다 생성의 오버헤드를 줄여 성능을 향상시킬 수 있습니다.
- noinline: 인라인 함수 내 람다 매개변수를 인라인화하지 못하게 합니다. 람다를 저장하거나 전달해야 할 때 유용합니다.
- crossinline: 인라인 함수 내 람다의 비지역 반환을 방지합니다. 람다가 둘러싸는 함수로부터 반환하지 못하도록하여 람다의 예측 가능한 동작을 보장합니다.

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

# Sealed Class / Interface

Sealed 클래스는 자바 세계의 enum의 고급 버전으로 볼 수 있습니다. 코틀린에서 sealed class와 sealed interface를 모두 선언할 수 있습니다. Sealed 클래스와 sealed interface는 Kotlin에서 제한된 계층 구조를 모델링하는 유용한 도구입니다. 두 가지 중에서 선택하는 것은 상태와 동작을 공유해야 하는지(Sealed class 사용) 또는 동작에 대한 계약을 정의해야 하는지에 따라 다릅니다(Sealed interface 사용).

```js
sealed interface Polygon {
    data class Circle(val radius: Double) : Polygon
    data class Square(val side: Double) : Polygon
    data object NotAShape : Polygon
}
```

```js
sealed class Shape(area: Double) {
    data class Circle(val radius: Double) : Shape(3.14* radius* radius)
    data class Square(val side: Double) : Shape(side * side)
    data object NotAShape : Shape(0.0)
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

# 제네릭

제네릭은 Kotlin에서 선언된 클래스, 인터페이스 및 함수에 타입 매개변수를 사용할 수 있는 강력한 도구입니다. 이를 통해 다양한 데이터 유형을 허용하고 타입 안전성을 유지하면서 유연하고 재사용 가능한 코드를 작성할 수 있습니다.

## 제네릭 클래스

```js
class Machine<T>(val type: T)

fun main() {
    val machine1 = Machine(12)
    val machine2 = Machine("optimus")
    println(machine1.type) // 12
    println(machine2.type) // optimus
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

## 일반 함수들

일반 함수들은 타입 매개변수를 가진 함수들입니다. 이를 통해 우리는 타입 안전성을 희생하지 않고 다른 타입들에 대해 동작하는 함수를 작성할 수 있습니다.

```kotlin
fun <T> singletonList(item: T): List<T> {
    return listOf(item)
}

fun main() {
    val intList = singletonList(5)
    println(intList) // [5]
}
```

## Variance

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

분산이란 일반적인 유형 간의 서브타이핑 관계를 정의합니다.

- 불변성은 요소를 소비하고 생성할 수 있습니다. `T` 타입의 `Invariant` 클래스입니다.

```kotlin
class Invariant<T>(var value: T)
val intInvariant = Invariant<Int>(12)
// var anyInvariant : Invariant<Any> = intInvariant // 컴파일 오류
```

- 공산성은 요소를 생성만 할 수 있습니다. `T`의 슈퍼 클래스는 `T`를 대체할 수 있지만 서브타입은 아닙니다. `out T`입니다.

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
class Contravariant<out T>(private val value: T) {
    fun get(): T {
        return value
    }
}

fun main() {
    val intContravariant = Contravariant<Int>(12)
    val anyContravariant: Contravariant<Any> = intContravariant
    // val doubleContravariant : Contravariant<Double> = intContravariant // Compilation error
}
```

- Contravariance can only consume elements. The subclasses of T can replace it but not the superclass. `in T`

```kotlin
class Contravariant<in T> {
    fun put(item: T) { println(item) }
}

fun main() {
    val numberContravariant = Contravariant<Number>()
    val doubleContravariant: Contravariant<Double> = numberContravariant
    // val anyContravariant: Contravariant<Any> = numberContravariant  // Compilation error
}
```

# 코루틴

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

코루틴은 가볍고(쓰레드보다 훨씬 가볍습니다). 블로킹하지 않고, 어느 스레드도 차단하지 않지만 코드 실행을 일시 중단하고 다시 시작합니다. 디스패처(dispatchers)와 스코프(scopes)에 따라 코루틴이 널리 분류됩니다.

## 코루틴 스코프

- GlobalScope 어떤 특정 라이프사이클에 바인딩되지 않은 최상위 코루틴을 시작하는 글로벌 스코프
- lifecycleScope 액티비티나 프래그먼트의 수명주기에 바인딩된 스코프
- viewModelScope ViewModel 수명주기에 바인딩된 스코프

```js
GlobalScope.launch {
    // 오래 실행되는 작업
}
lifecycleScope.launch {
    // 코루틴 코드
}
viewModelScope.launch {
    // 코루틴 코드
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

## Coroutine Dispatchers

- `dispatchers.Main`은 주 (UI) 스레드에서 실행됩니다.
- `dispatchers.IO`는 네트워크 또는 데이터베이스 작업에 사용됩니다.
- `dispatchers.Default`는 CPU 집약적인 작업에 사용됩니다 (비트맵 작업).
- `dispatchers.Unconfined`는 호출자 스레드에서 실행되지만 처음 일시 중지 지점까지만 실행됩니다.

## Builders

- `launch`는 새로운 코루틴을 시작하고 결과를 반환하지 않습니다. Fire-and-forget 방식입니다.
- `async`는 새로운 코루틴을 시작하고 향후 결과를 나타내는 Deferred를 반환합니다. 결과를 얻으려면 `await`를 사용하세요.
- `runBlocking`은 해당 블록이 완료될 때까지 현재 스레드를 차단합니다. 주로 메인 함수 및 테스트에서 사용됩니다.

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
CoroutineScope(Dispatchers.Main).launch {
    // 코루틴 코드
}

val deferred = CoroutineScope(Dispatchers.Default).async {
    // 비동기 작업
    "결과"
}
runBlocking {
    val result = deferred.await()
    println(result)
}

runBlocking {
    // 코루틴이 완료될 때까지 블록됨
}
```
