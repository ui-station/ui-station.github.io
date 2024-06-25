---
title: "Swift에서 부분 적용 함수 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-PartiallyAppliedFunctionsinSwift_0.png"
date: 2024-06-23 01:50
ogImage:
  url: /assets/img/2024-06-23-PartiallyAppliedFunctionsinSwift_0.png
tag: Tech
originalTitle: "Partially Applied Functions in Swift"
link: "https://medium.com/@xiangyu-sun/partially-applied-functions-in-swift-890a8d720763"
---

Swift에서 부분 적용 함수는 일부 매개변수로 호출된 함수를 의미합니다. 이는 남은 매개변수를 입력으로 받는 새로운 함수를 만듭니다.

# 부분 적용 함수 예시:

Swift에서 간단한 함수를 고려해보겠습니다:

```swift
func multiply(_ a: Int, _ b: Int) -> Int {
 return a * b
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

새로운 기능을 만들고 싶다면 항상 2배를 곱하는 함수를 부분적용할 수 있습니다.

```js
let multiplyByTwo = multiply(2, _);
```

이제 `multiplyByTwo`는 하나의 매개변수를 받는 함수입니다.

```js
let result = multiplyByTwo(5); // 결과는 10
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

# Swift 컴파일러가 부분 적용 함수를 사용하는 이유는 무엇인가요?

부분 적용 함수는 여러 이유로 유용합니다:

1. 코드 재사용성: 일반적인 함수에서 더 구체적인 함수를 다시 작성하지 않고 만들 수 있어 더 모듈식이고 재사용 가능한 코드를 작성할 수 있습니다.

2. 함수형 프로그래밍: 부분 적용 함수는 함수형 프로그래밍의 핵심이며 함수를 일급 시민으로 사용하는 함수형 프로그래밍 패러다임을 강조합니다. Swift는 함수형 프로그래밍 패러다임을 지원하며, 부분 적용 함수는 고차 함수를 만드는 데 도움이 됩니다.

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

3. 클로저 간소화: 클로저 생성을 간단하게 해줍니다. 전체 클로저를 작성하는 대신 부분 적용을 사용하여 더 간결한 효과를 얻을 수 있습니다.

4. 가독성 향상: 중복을 줄임으로써 코드를 더 읽기 쉽게 만들어줍니다. 공통 매개변수로 반복적으로 동일한 함수 호출을 작성하는 대신, 부분 적용된 함수를 만들 수 있습니다.

# 예시 문맥 안에서

다음은 더 포괄적인 예시입니다:

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

여러분이 수를 담은 리스트가 있고 짝수를 필터링하고 싶다면 부분적용 함수를 사용할 수 있습니다.

```js
let numbers = [1, 2, 3, 4, 5, 6]
func isDivisibleBy(_ divisor: Int, _ number: Int) -> Bool {
 return number % divisor == 0
}
let isEven = isDivisibleBy(2, _:)
let evenNumbers = numbers.filter(isEven)
// evenNumbers will be [2, 4, 6]
```

이 예제에서 `isDivisibleBy`가 부분적용으로 `isEven`을 만들어 배열을 필터링하는 데 사용되는 것을 볼 수 있습니다.

# 컴파일러가 부분적용 함수를 사용하는 방법

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

The Swift 컴파일러는 여러 가지 이유로 부분 적용 함수를 사용할 수 있습니다:

1. 최적화: 컴파일러는 부분 적용 함수를 최적화하여 성능을 향상시킬 수 있으며, 특히 고계 함수와 클로저가 포함된 시나리오에서 유용합니다.

2. 유형 추론: Swift의 유형 추론 시스템은 부분 적용 함수를 효과적으로 처리하여 명시적 유형 주석의 필요를 줄이고 코드를 더 깔끔하게 만들 수 있습니다.

3. 중간 표현: 컴파일 중에 부분 적용 함수는 코드의 변환과 최적화를 단순화하는 중간 표현으로 사용될 수 있습니다.

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

4. 기능적 패러다임: Swift는 객체지향 및 함수형 프로그래밍 패러다임을 모두 지원합니다. 부분 함수 적용은 함수형 프로그래밍에 자연스럽게 적합하며 사용하면 더 표현력이 풍부하고 간결한 코드를 작성할 수 있습니다.

# Swift 컴파일러와 부분 함수 적용

Swift 컴파일러는 고수준 추상화 및 효율적인 최적화 처리를 수행할 수 있도록 설계되었습니다. 부분 함수 적용은 컴파일러가 성능을 향상시키고 코드 관리를 용이하게 하는 데 활용할 수 있는 핵심 기능입니다.

## 1. 중간 표현들

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

스위프트 코드를 작성할 때, 스위프트 컴파일러는 중간 표현 (IR)으로 번역합니다. 부분 적용된 함수는 이 중간 형태로 표현될 수 있어, 컴파일러가 쉽게 최적화를 수행할 수 있습니다.

- 함수 커링: 함수형 프로그래밍에서 커링은 여러 인수를 갖는 함수를 하나의 인수로 있는 각각의 함수의 순서로 변환하는 것입니다. 스위프트 컴파일러는 함수 호출을 최적화하기 위해 자동으로 커링을 적용할 수 있습니다.
- 람다 리프팅: 이 기술은 중첩 함수를 추가 매개변수를 갖는 최상위 함수로 변환하는 것을 포함합니다. 부분적으로 적용된 함수를 최상위 엔티티로 표현함으로써, 컴파일러가 더 잘 관리하고 최적화할 수 있습니다.

이제 스위프트에서 함수 커링과 람다 리프팅의 예를 살펴보겠습니다.

함수 커링

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

커링은 여러 인수를 사용하는 함수를 한 인수를 사용하는 함수의 시퀀스로 변환하는 과정입니다. 이 기술은 더 유연하고 재사용 가능한 코드를 작성하는 함수형 프로그래밍에서 유용하게 활용됩니다.

## 스위프트에서 함수 커링 예제

두 정수를 더하는 간단한 함수로 시작해보겠습니다:

```js
func add(_ a: Int, _ b: Int) -> Int {
 return a + b
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

이 함수를 커리한 버전으로 변환할 수 있습니다:

```swift
func curriedAdd(_ a: Int) -> (Int) -> Int {
    return { b in
        return a + b
    }
}
```

이제 `curriedAdd`는 정수 `a`를 받아 정수 `b`를 받고 `a`와 `b`의 합을 반환하는 새 함수를 반환하는 함수입니다.

## 사용법:

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
let addFive = curriedAdd(5); // addFive은 이제 함수(Int) -> Int입니다.
let result = addFive(3); // 결과는 8입니다.
```

직접 사용할 수도 있습니다:

```js
let resultDirect = curriedAdd(5)(3); // resultDirect는 8입니다.
```

## 람다 리프팅

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

람다 끌어올리기는 중첩 함수(람다)를 추가 매개변수를 가진 최상위 함수로 변환하는 프로세스입니다. 이 변환은 함수가 주변 컨텍스트와 독립적이 되도록 만듭니다.

## Swift에서 람다 끌어올리기 예제

중첩 함수(클로저)가 있는 함수로 시작해봅시다:

```js
func outerFunction(_ x: Int) -> Int {
 func innerFunction(_ y: Int) -> Int {
 return x + y
 }
 return innerFunction(5)
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

이 예시에서 `innerFunction`은 `outerFunction` 내에 중첩되어 있으며 변수 `x`에 의존합니다.

## 람다 릴팅 변환

`innerFunction`을 최상위 수준으로 이동시키려면 명시적 매개변수로 `x`를 추가해야 합니다:

```js
func liftedInnerFunction(_ x: Int, _ y: Int) -> Int {
 return x + y
}
func outerFunction(_ x: Int) -> Int {
 return liftedInnerFunction(x, 5)
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

이제 `liftedInnerFunction`은 주변 컨텍스트의 변수에 의존하지 않는 최상위 함수입니다.

## 사용법:

```js
let result = outerFunction(3); // result is 8
```

`innerFunction`을 최상위 수준으로 올리면 더 재사용 가능하고 더 이해하기 쉬워지며, 더 이상 둘러싸는 함수의 상태에 의존하지 않게 됩니다.

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

## 2. 인라인 처리와 특수화

Swift 컴파일러는 부분적으로 적용된 함수를 인라인 처리하여 함수 호출을 함수의 본문으로 대체할 수 있습니다. 이는 함수 호출의 오버헤드를 줄이고 성능을 크게 향상시킬 수 있습니다.

- 함수 인라인 처리: 부분적으로 적용된 함수가 인라인 처리될 때 컴파일러는 중간 클로저를 생성할 필요 없이 함수의 로직을 직접 해당 위치에 삽입할 수 있습니다.
- 특수화: 컴파일러는 특정 유형이나 매개변수 값에 대해 함수의 특수화된 버전을 생성할 수 있습니다. 이는 특히 부분적으로 적용된 함수에 유용하며, 컴파일러가 가장 일반적인 사용 사례에 최적화할 수 있게 합니다.

함수 인라인 처리

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

함수 인라인은 컴파일러가 함수 호출을 해당 함수의 실제 코드로 대체하는 최적화 기술입니다. 이는 레지스터의 저장 및 복원과 같은 함수 호출과 관련된 오버헤드를 제거할 수 있으며, 컴파일러에 의해 추가적인 최적화를 가능하게 할 수 있습니다.

인라인은 작고 빈번히 호출되는 함수에 특히 유용합니다.

예시

다음과 같은 간단한 함수를 고려해보세요:

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
func add(_ a: Int, _ b: Int) -> Int {
 return a + b
}
```

이 함수를 호출하면:

```js
let result = add(2, 3);
```

컴파일러는 이 호출을 함수의 본문으로 대체할 수 있습니다:

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
let result = 2 + 3;
```

이렇게 하면 함수 호출의 오버헤드를 제거할 수 있어요.

## `@inline(__always)`를 사용한 명시적 인라인화

Swift에서는 `@inline(__always)` 속성을 사용하여 항상 함수를 인라인으로 처리하도록 컴파일러에 제안할 수 있어요. 이는 성능에 중요한 코드에 유용할 수 있어요.

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

```swift
@inline(__always)
func multiply(_ a: Int, _ b: Int) -> Int {
 return a * b
}
```

사용 방법:

```swift
let result = multiply(4, 5)
```

여기서 컴파일러는 `multiply`를 인라인으로 권장받아 호출을 효과적으로 다음과 같이 대체합니다:

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
let result = 4 * 5;
```

## 인라인 사용 시기 및 이유

- 성능: 인라인은 함수 호출 오버헤드를 제거하고 추가적인 컴파일러 최적화를 가능하게 함으로써 성능을 향상시킬 수 있습니다.

- 작은 함수: 작고 자주 호출되는 함수는 인라인에 적합한 후보입니다.

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

- 중요한 경로: 매 밀리초가 소중한 성능에 영향을 미치는 코드 경로에서는 인라인화가 유익할 수 있습니다.

그러나 지나치게 많은 인라인화는 이진 크기가 크게 증가하여 캐시 미스와 증가된 메모리 사용량으로 인한 성능 저하를 초래할 수 있습니다. 따라서, 인라인화를 분별하여 사용하는 것이 중요합니다.

## 인라인화에 대한 컴파일러 결정

Swift 컴파일러는 함수를 인라인으로 처리할지 여부를 결정하는데 여러 가지 휴리스틱을 사용합니다:

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

- 기능 크기: 작은 함수는 큰 함수보다 인라인화될 가능성이 높습니다.
- 호출 빈도: 자주 호출되는 함수는 오버헤드를 줄이기 위해 인라인화될 수 있습니다.
- 복잡성: 간단한 논리를 가진 함수는 인라인화하기에 적합합니다.
- 주석: `@inline(__always)` 또는 `@inline(never)`와 같은 속성은 인라인화 결정에 대한 힌트를 컴파일러에 제공합니다.

예시

주어진 함수를 배열의 각 요소에 적용하는 고차 함수를 고려해보세요:

```js
func applyToEach(_ array: [Int], _ transform: (Int) -> Int) -> [Int] {
 return array.map { transform($0) }
}
@inline(__always)
func increment(_ x: Int) -> Int {
 return x + 1
}
let numbers = [1, 2, 3, 4]
let incrementedNumbers = applyToEach(numbers, increment)
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

인라인 없이:

`applyToEach` 함수는 배열의 각 요소마다 `increment`를 호출하여 여러 함수 호출을 발생시킵니다.

인라인 사용:

컴파일러는 `increment`를 인라인으로 처리하여 함수 호출 대신 `map` 클로저 내에서 증가 연산을 바로 수행할 수 있습니다.

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
let incrementedNumbers = numbers.map { $0 + 1 }
```

함수 호출 오버헤드를 제거하고 더 효율적인 코드를 만들 수 있습니다.

## 3. 클로저 최적화

부분적으로 적용된 함수는 클로저와 밀접한 관련이 있습니다. Swift 컴파일러는 부분적으로 적용된 함수에 이로운 여러 전략을 사용하여 클로저를 최적화합니다.

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

- 스택 할당: 클로저(또는 부분 적용 함수)가 정의된 범위에서 벗어나지 않는다면, 컴파일러는 메모리 할당 오버헤드를 줄이기 위해 힙 대신 스택에 할당할 수 있습니다. 이것은 클로저에 `@escaping` 속성을 사용하여 직접 제어할 수 있습니다.
- 컨텍스트 캡처: 컴파일러는 클로저가 캡처하는 변수를 분석하고 이러한 변수의 저장을 최적화하여 캡처 및 컨텍스트 저장의 오버헤드를 최소화합니다.

## 4. 타입 추론 및 제네릭 함수

Swift의 강력한 타입 추론 시스템은 부분 적용 함수와 원활하게 작동하여 명시적인 타입 주석이 필요 없이 코드를 더 간결하게 만듭니다.

- 제네릭 함수: 컴파일러는 부분 적용 함수를 사용하여 제네릭 처리를 더 효과적으로 다룰 수 있으며, 사용되는 맥락에 기반하여 함수의 타입을 추론하고 특수화된 버전을 생성할 수 있습니다.

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

# 애플의 공식 설명

애플의 공식 Swift 문서는 클로저와 부분 적용 함수를 포함한 함수형 프로그래밍 패러다임을 강조합니다. 다음은 애플의 문서와 관련 자료에서 주요한 내용들입니다:

- 클로저: 애플은 클로저를 자체 포함된 기능 블록으로 설명하며, 변수와 상수에 대한 참조를 캡처하고 저장할 수 있는 기능입니다. 부분 적용 함수는 일부 매개변수가 고정된 클로저의 특수한 경우입니다.
- 함수형 프로그래밍: Swift는 함수형 프로그래밍 방식을 장려하며, 부분 적용 함수는 이 방식에 중요한 역할을 합니다. 애플의 문서는 Swift의 함수 유형과 1급 함수가 유연하고 표현력 있는 코드를 가능하게 한다는 점을 강조합니다.
- 최적화 기법: 사용자에게 명시적으로 자세히 설명되지는 않지만, 백그라운드에서 컴파일러 최적화 기능을 통해 언어의 고급 기능을 활용합니다. 이는 인라인 최적화, 제네릭 특수화, 효율적인 클로저 처리 등을 포함합니다.

# 애플 문서 예시

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

애플의 Swift 문서에서 간단한 map 함수 예제를 살펴보겠습니다:

```js
let numbers = [1, 2, 3, 4]
let doubled = numbers.map { $0 * 2 }
```

여기서 ' $0 \* 2 '의 클로저는 부분 적용 함수로 볼 수 있습니다. 컴파일러는 이를 최적화하기 위해 다음을 수행합니다:

- 클로저의 논리를 맵 함수로 직접 인라인 처리합니다.
- 정수 배열에 대한 맵 함수를 특수화하여 일반적인 유형 처리의 오버헤드를 제거합니다.

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

Swift 컴파일러는 이를 통해 함수형 스타일 코드가 가능한 한 효율적임을 보장합니다.
