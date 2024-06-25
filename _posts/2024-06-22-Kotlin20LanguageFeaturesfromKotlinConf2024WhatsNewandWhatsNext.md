---
title: "KotlinConf 2024에서 공개된 Kotlin 20 언어 기능 새로운 점과 앞으로 기대할 점"
description: ""
coverImage: "/assets/img/2024-06-22-Kotlin20LanguageFeaturesfromKotlinConf2024WhatsNewandWhatsNext_0.png"
date: 2024-06-22 22:39
ogImage:
  url: /assets/img/2024-06-22-Kotlin20LanguageFeaturesfromKotlinConf2024WhatsNewandWhatsNext_0.png
tag: Tech
originalTitle: "Kotlin 2.0 Language Features from KotlinConf 2024: What’s New and What’s Next"
link: "https://medium.com/@omarsahl/kotlin-language-features-from-kotlinconf-2024-whats-new-and-what-s-next-a4668eae5e9d"
---

<img src="/assets/img/2024-06-22-Kotlin20LanguageFeaturesfromKotlinConf2024WhatsNewandWhatsNext_0.png" />

2024년 KotlinConf에서 JetBrains의 주요 언어 디자이너인 Michail Zarečenskij가 Kotlin의 최신 언어 기능을 소개했습니다. 그의 발표를 기반으로 한 이 기사는 Kotlin 2.0의 주요 업데이트를 강조하고 향후 릴리스에서의 흥미로운 기능을 미리 보여줍니다.

계속 읽어보시면 Kotlin 언어에서 새로운 기능과 다가올 내용을 발견할 수 있습니다.

# Kotlin 2.0 기능 요약

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

## 1. 명시적 백킹 필드

Kotlin 2.0에서 소개된 주목할만한 기능 중 하나는 명시적 백킹 필드입니다. 이 기능은 K2 컴파일러 미리보기의 일부로 버전 1.7.0부터 있었으며, 현재 Kotlin 2.0에서 실험적인 기능입니다.

이 기능을 이해하기 위해, 가장 익숙하고 안드로이드 프로젝트에서 매우 인기 있는 몇 가지 코드를 살펴보겠습니다:

```js
class MyViewModel {
    private val _title = MutableStateFlow<String>("Placeholder")
    val title: StateFlow<String> get() = _title
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

이 예시에서는 title 상태가 캡슐화되어 있어서 ViewModel만 상태를 수정할 수 있고 나머지 애플리케이션에는 읽기 전용 버전이 노출됩니다.

Kotlin 2.0에서는 명시적 백킹 필드를 사용하여 더 간결하고 우아하게 이를 달성하는 방법이 있습니다:

```js
class MyViewModel {
    val title: StateFlow<String>
        field = MutableStateFlow<String>("Placeholder")
}
```

이 새로운 구문은 title 속성에 대한 명시적 백킹 필드를 선언하여 보일러플레이트 코드를 줄이고 가독성을 향상시킵니다.

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

미래의 Kotlin 버전에서는 명명된 명시적 백킹 필드와 같은 이 기능에 대한 업데이트가 포함될 예정이라는 사실을 언급하는 것도 좋습니다. 아래는 이 기능이 어떻게 보일지 예시입니다:

```kotlin
class MyViewModel {
    val title: StateFlow<String>
        field mutableTitle = MutableStateFlow<String>("Placeholder")
}
```

## 2. 연산자 결합 및 숫자 변환

K2 컴파일러는 Frontend Intermediate Representation (FIR)이라고 불리는 것을 도입합니다. 이를 통해 컴파일러 및 IDE 성능이 향상되어 더 빠른 컴파일과 정확한 오류 메시지를 제공합니다.

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

FIR로 인한 사용자에게 보이는 변경 중 하나는 "연산자 및 숫자 변환의 결합"입니다. 이 기능은 숫자 유형을 포함한 작업을 간소화하여 명시적인 유형 변환의 필요성을 줄입니다.

예를 들어:

```js
fun foo(longs: MutableList<Long>) {
    longs[0] += 1 // 이것은 Kotlin 1.x에서 오류가 발생하지만, Kotlin 2.0에서는 정상적으로 작동합니다.
}
```

Kotlin 1.x에서 위 코드는 1이 명시적으로 Long이 아니기 때문에 오류가 발생합니다. 1L을 대신 사용해야 합니다.

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

코틀린 2.0에서는 longs[0] += 1이 longs.set(0, longs.get(0).plus(1))로 분해되기 때문에 정상적으로 작동합니다.

## 3. 널 가능 연산자 호출의 조합

이 기능은 K2 컴파일러에서 소개된 새로운 FIR과 관련이 있습니다.

이를 설명하기 위해 다음 예시를 살펴보겠습니다:

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
class Box(val longs: MutableList<Long>)

fun foo(box: Box?) {
    box?.longs[0] += 1 // Kotlin 1.x에서 오류 발생
    box?.longs[0] += 1L // Kotlin 1.x에서도 오류 발생
}
```

Kotlin 1.x에서는 longs의 첫 번째 요소를 증가시키는 두 줄 모두 오류가 발생합니다.

그러나 Kotlin 2.0에서는 box?.longs[0] += 1을 사용하면 어떤 문제도 발생하지 않습니다. 왜냐하면 이 코드는 box?.run ' longs.set(0, longs.get(0).plus(1)) '으로 변환되기 때문입니다.

## 4. 더 똑똑해진 스마트 캐스트 (좋아요!)

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

K2에서는 새로운 제어 플로 엔진이 소개되어 타입 추론 및 해결에서 상당한 개선이 이루어졌어요. 이 새로운 제어 플로 엔진 덕분에 여러 가지 새로운 기능을 사용할 수 있게 됐어요.

간단한 코드 예시를 살펴보죠:

```js
class Phone {
    fun ring() {
        println("벨이 울립니다...")
    }
}

fun makeNoise(device: Any) {
    if (device is Phone) {
        device.ring()
    }
}
```

이 코드는 간단합니다. device가 Phone인지 확인한 후에 device 객체가 Phone으로 스마트 캐스트되어 ring 메서드를 안전하게 호출할 수 있습니다.

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

그러나 makeNoise 함수를 약간 수정하고 다음과 같이 새로운 isPhone 변수를 도입하면:

```js
fun makeNoise(device: Any) {
    val isPhone = device is Phone
    if (isPhone) {
        device.ring() // Kotlin 1.x에서는 여기서 오류 발생
    }
}
```

Kotlin 1.x에서는 지역 변수가 데이터 흐름 정보를 전달하지 않고 스마트 캐스트 로직에 기여하지 않기 때문에 오류가 발생합니다. 이로 인해 우리는 device 객체의 ring 메서드를 호출할 수 없습니다.

그러나 Kotlin 2.0에서는 이 코드가 예상대로 작동합니다. 이제 로컬 변수가 스마트 캐스트에 대한 정보를 전파하기 때문에 ring 메서드를 호출할 수 있습니다!

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

모든 일반적인 스마트 캐스트 규칙에 해당하는 것을 유의해야 합니다. 계약을 포함하여 예를 들어:

```js
class Card(val holderName: String?)

fun foo(card: Any): String {
    val hasHolderName = card is Card && !card.holderName.isNullOrBlank()
    return when {
        hasHolderName -> card.holderName
        else -> "Unknown"
    }
}
```

여기에는 두 개의 스마트 캐스트가 있습니다. card 객체는 Card로 스마트 캐스트되고, holderName은 String?에서 String으로 스마트 캐스트됩니다.

또한, Kotlin 2.0에서 해결된 스마트 캐스트와 관련된 여러 버그가 있었습니다. 이 글에서는 이러한 개선 사항에 대해 자세히 다루지는 않겠지만, 이곳에서 더 많은 정보를 읽을 수 있습니다.

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

## 5. Smart Casts After ||

Kotlin 2.0의 새로운 데이터 플로 엔진에서 제공되는 개선 사항을 기반으로, 논리 || 연산자 이후에 스마트 캐스트를 도입한 또 다른 기능이 있습니다.

이 기능을 설명하기 위해 예를 살펴보겠습니다:

```js
interface Action {
    fun execute()
}

interface None : Action
interface Send : Action
interface Receive : Action

fun demo(action: Any) {
    if (action is Send || action is Receive) {
        action.execute() // Kotlin 1.x에서는 작동하지 않습니다
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

친구야,

Kotlin 1.x에서는 이 코드가 컴파일되지 않습니다. || 연산자 이후에 스마트 캐스트를 수행하지 않기 때문에 execute 함수를 호출할 수 없기 때문입니다.

그러나 Kotlin 2.0에서는 컴파일러가 공통 상위 유형을 인식하고 Action으로 스마트 캐스트를 수행하여 문제없이 execute를 호출할 수 있게 됩니다.

## 6. 인라인 람다의 클로저 내에서 스마트 캐스트

Kotlin 2.0의 새 데이터 플로 엔진과 관련된 스마트 캐스트와 인라인 람다의 클로저에서 캡처된 지역 변수에 대한 스마트 캐스트를 수행할 수 있는 기능이 있습니다.

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

다음은 정수 배열에서 최댓값의 인덱스를 찾는 코드입니다:

```kotlin
fun indexOfMax(numbers: IntArray): Int? {
    var maxIndex: Int? = null
    numbers.forEachIndexed { i, number ->
        // Kotlin 1.x에서는 여기서 Int?에서 Int로의 스마트 캐스트가 불가능합니다.
        // 왜냐하면 'maxIndex'가 변하는 클로저에 의해 캡처된 로컬 변수이기 때문입니다.
        if (maxIndex == null || numbers[maxIndex!!] <= number) {
            maxIndex = i
        }
    }
    return maxIndex
}
```

Kotlin 1.x에서는 Int?에서 Int로의 스마트 캐스트가 불가능합니다. 이는 'maxIndex'가 변하는 클로저에 의해 캡처된 로컬 변수이기 때문입니다. 이에 대한 설명은 이 Stack Overflow 답변을 참조하십시오.

Kotlin 2.0은 인라인 함수를 암묵적으로 callsInPlace 계약이 있는 것으로 취급하여 이 문제에 대응합니다. 본질적으로 컴파일러는 람다를 보다 일반적인 for 루프처럼 다룰 수 있어 람다 내에서 스마트 캐스트를 활성화합니다. 이로써 느낌표(!!)를 제거할 수 있으며 이제 maxIndex가 Int로 스마트 캐스트되었기 때문에입니다:

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
fun indexOfMax(numbers: IntArray): Int? {
    var maxIndex: Int? = null
    numbers.forEachIndexed { i, number ->
        // 코틀린 2.0에서는 명시적인 !!를 제거할 수 있습니다.
        if (maxIndex == null || numbers[maxIndex] <= number) {
            maxIndex = i
        }
    }
    return maxIndex
}
```

# Kotlin의 미래 기능

Kotlin 2.0의 새로운 기능을 탐색했는데, 앞으로 나올 Kotlin의 릴리스에서 기대되는 흥미로운 기능을 간단히 살펴보겠습니다.

## 1. When 문에서의 가드 조건

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

미래의 Kotlin 릴리스에서 나올 흥미로운 기능 중 하나는 guards 기능입니다. 이 기능을 사용하면 when 문의 분기에 직접 조건을 추가할 수 있어서 코드를 더 간결하게 만들 수 있습니다.

다음 예시를 살펴보세요:

```js
sealed interface SearchResult {
    class Person(val isBlocked: Boolean) : SearchResult
    class Post(/* ... */) : SearchResult
    class Place(/* ... */) : SearchResult
}

@Composable
fun SearchResultListItem(searchResult: SearchResult) {
    when (searchResult) {
        is SearchResult.Person -> when {
            !searchResult.isBlocked -> { /* ... */ }
        }
        is SearchResult.Post -> { /* ... */ }
        is SearchResult.Place -> { /* ... */ }
    }
}
```

이 코드는 꽤 간단하지만 searchResult가 여러 번 반복됩니다. 따라서 더 간결하게 만들기 위해 반복되는 searchResult를 제거하고 subject로 searchResult를 사용하는 when 문을 사용할 수 있습니다:

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
@Composable
fun SearchResultItem(searchResult: SearchResult) {
    when (searchResult) {
        is SearchResult.Person && !searchResult.isBlocked -> { /* ... */ }
        is SearchResult.Post -> { /* ... */ }
        is SearchResult.Place -> { /* ... */ }
    }
}
```

그러나 이 방식은 && 연산자를 사용할 수 없기 때문에 오류가 발생합니다.

가드(guard)를 사용하면 주어진 하위 표현식에 조건을 추가하기 위한 우아한 방법을 제공하여 이 문제를 해결할 수 있습니다:

```kotlin
@Composable
fun SearchResultItem(searchResult: SearchResult) {
    when (searchResult) {
        is SearchResult.Person if !searchResult.isBlocked -> { /* ... */ }
        ...
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

카틴 2.1에서 가드가 베타로 나올 때입니다.

## 2. 컨텍스트 민감한 해결

이전 예제를 기반으로 하여, when 가드로 코드를 보다 간결하게 만들었지만 여전히 SearchResult 클래스 이름을 각 when 분기에서 반복해야 합니다.

컨텍스트 민감한 해결을 통해 반복되는 SearchResult를 제거하여 코드를 더 간결하게 만들 수 있습니다:

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
@Composable
fun SearchResultItem(searchResult: SearchResult) {
    when (searchResult) {
        is Person && !searchResult.isBlocked -> { /* ... */ }
        is Post -> { /* ... */ }
        is Place -> { /* ... */ }
    }
}
```

이 기능은 sealed 타입 및 열거형의 인스턴스와 함께 작동합니다. 이것은 코드를 더 깔끔하고 간결하게 만듭니다.

컨텍스트에 민감한 해결책은 코틀린 2.2에서 실험적 기능으로 제공됩니다.

## 3. 이름 기반 구조 분해

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

현재 Kotlin의 해체 선언 기능은 임의의 변수 이름 사용을 허용합니다.

예를 들어, 다음 코드를 살펴보세요:

```js
data class User(val firstName: String, val lastName: String)

fun handleUser(user: User) {
    val (surname, someName) = user
    // ...
}
```

여기서는 User 데이터 클래스의 속성을 surname과 someName과 같은 임의의 이름으로 해체하고 있습니다. 이러한 방식은 컴파일러가 수용하지만 안전하지 않을 수 있으며, 실제 속성을 정확하게 반영하지 않아 버그를 유발할 수 있습니다.

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

앞으로의 Kotlin 버전에서는 해체 선언에 임의의 이름을 사용하는 것을 경고하고, 마침내 오류로 만들어가며 이에 대응하게 될 것입니다.
또한, 해체된 속성에 새 이름을 할당하는 특별한 구문이 도입될 예정이며, 더 안전하고 예측 가능한 코드를 보장할 것입니다.

## 4. 확장 가능한 데이터 인수

앞으로의 Kotlin 버전에 추가될 또 다른 강력한 기능은 확장 가능한 데이터 인수입니다.

이 기능을 이해하기 위해 Compose의 LazyColumn 함수를 살펴봅시다:

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
@Composable
fun LazyColumn(
    modifier: Modifier = Modifier,
    state: LazyListState = rememberLazyListState(),
    contentPadding: PaddingValues = PaddingValues(0.dp),
    reverseLayout: Boolean = false,
    verticalArrangement: Arrangement.Vertical = if (!reverseLayout) Arrangement.Top else Arrangement.Bottom,
    horizontalAlignment: Alignment.Horizontal = Alignment.Start,
    flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    userScrollEnabled: Boolean = true,
    content: LazyListScope.() -> Unit
)
```

만약 Compose에 익숙하지 않다면, LazyColumn 함수를 사용하면 필요할 때만 로드되는 항목들의 게으른 목록을 표시할 수 있습니다. 많은 수의 항목을 효율적으로 표시하는 데 특히 유용합니다.

대부분의 UI 함수와 마찬가지로, LazyColumn에도 동작을 수정하는 여러 인수가 있습니다. 그러나 함수가 변경되고 새로운 매개변수가 추가되면 문제가 발생합니다. 바이너리 호환성을 유지하기 위해 함수의 다른 오버로드(이 경우 LazyColumn)가 추가되어 코드와 설명이 중복됩니다.

![image](/assets/img/2024-06-22-Kotlin20LanguageFeaturesfromKotlinConf2024WhatsNewandWhatsNext_1.png)

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

확장 가능한 데이터 인수가 등장하는 곳입니다. 이를 통해 기본값을 갖는 인수를 데이터 인수 클래스로 이동하여 함수 서명을 간단하게 만들고 소스와 이진 호환성을 유지할 수 있습니다:

```js
dataarg class LazyColumnSettings(
    val contentPadding: PaddingValues = PaddingValues(0.dp),
    val reverseLayout: Boolean = false,
    val verticalArrangement: Arrangement.Vertical = if (!reverseLayout) Arrangement.Top else Arrangement.Bottom,
    val horizontalAlignment: Alignment.Horizontal = Alignment.Start,
    val flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    val userScrollEnabled: Boolean = true,
)
```

이후 LazyColumn 함수는 다음과 같습니다:

```js
@Composable
fun LazyColumn(
    modifier: Modifier = Modifier,
    state: LazyListState = rememberLazyListState(),
    dataarg settings: LazyColumnSettings,
    content: LazyListScope.() -> Unit
)
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

`LazyColumn`의 호출 위치에서 이제 `LazyColumn(reverseLayout = true)`를 사용할 수 있습니다. 해당 매개변수는 `LazyColumnSettings`의 인스턴스로 변환될 것입니다.

확장 가능한 데이터 매개변수는 실험적인 기능으로 Kotlin 2.2에 추가될 예정입니다.

## 5. 오류를 위한 Union 타입

이는 향후 Kotlin 버전에서 추가될 또 다른 유망한 기능입니다.
이 기능을 설명하기 위해, Sequence의 `last()` 확장 함수를 살펴봅시다. 이 함수는 주어진 조건과 일치하는 Sequence의 마지막 요소를 반환합니다.

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
inline fun <T> Sequence<T>.last(predicate: (T) -> Boolean): T {
    var last: T? = null
    var found = false
    for (element in this) {
        if (predicate(element)) {
            last = element
            found = true
        }
    }
    if (!found) throw NoSuchElementException("Sequence contains no element matching the predicate.")
    @Suppress("UNCHECKED_CAST")
    return last as T
}
```

Michail Zarečenskij씨의 이 발표에서는 이 구현의 두 가지 단점을 지적했습니다. ' it == null '이 되는 경우를 처리하는 found 변수의 사용과 함수 끝 부분의 unchecked 캐스트입니다.
그는 그 후에 private object NotFound를 사용하는 해결책을 제안했습니다:

```kotlin
private object NotFound

inline fun <T> Sequence<T>.last(predicate: (T) -> Boolean): T {
    var result: Any? = NotFound
    for (element in this) {
        if (predicate(element)) result = element
    }
    if (result === NotFound) throw NoSuchElementException("Sequence contains no element matching the predicate.")
    @Suppress("UNCHECKED_CAST")
    return result as T
}
```

이 방법은 더 나아 보이지만 제네릭 유형 Any?을 사용하고 마지막 부분의 unchecked 캐스트를 제거하지 않습니다.

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

이곳은 오류에 대한 연합 유형이 필요한 곳입니다. 이것은 향후 코틀린 기능으로 우리가 이렇게 작성할 수 있게 해줄 것입니다:

```js
private error object NotFound

inline fun <T> Sequence<T>.last(predicate: (T) -> Boolean): T {
    var result: T | NotFound = NotFound // 오류에 대한 연합 유형.
    for (element in this) {
        if (predicate(element)) result = element
    }
    if (result is NotFound) throw NoSuchElementException("Sequence contains no element matching the predicate.")
    return result // T로의 자동 형변환.
}
```

이 코드는 결과 변수에 대한 연합 유형을 사용하여 일반적인 Any? 유형이 필요 없게 합니다. 또한 컴파일러는 우리를 위해 스마트 캐스트를 수행하므로 더 이상 검사되지 않은 형변환이 필요하지 않습니다. 멋지죠!

# 마무리맺음

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

이러한 새로운 기능들로 Kotlin은 계속 발전하며 현대 프로그래밍 언어가 제공할 수 있는 영역을 넓히고 있습니다. Kotlin을 사용하면 개발이 더욱 효율적이고 즐거워집니다.
이외에도 상세 내용이 제공되지 않은 다른 기능들이 언급되었습니다. Kotlin 팀이 이후에 관련 정보를 더 제공할 때까지 기다려야 할 것입니다.

읽어 주셔서 감사합니다! 아래 댓글란에 의견을 자유롭게 남겨 주세요.

이 기사가 마음에 드셨다면 클랩(좋아요)을 눌러 주시고 안드로이드 개발에 관한 더 많은 내용을 제공하는 저를 팔로우해 주세요.
