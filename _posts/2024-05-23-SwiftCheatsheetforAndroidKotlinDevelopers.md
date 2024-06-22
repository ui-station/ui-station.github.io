---
title: "Android Kotlin 개발자를 위한 Swift 참고 자료"
description: ""
coverImage: "/assets/img/2024-05-23-SwiftCheatsheetforAndroidKotlinDevelopers_0.png"
date: 2024-05-23 14:45
ogImage:
  url: /assets/img/2024-05-23-SwiftCheatsheetforAndroidKotlinDevelopers_0.png
tag: Tech
originalTitle: "Swift Cheatsheet for Android Kotlin Developers"
link: "https://medium.com/@domen.lanisnik/swift-cheatsheet-for-android-kotlin-developers-19cce41e54c6"
---

안녕하세요! 안드로이드 개발자들은 대부분 Kotlin으로 작업합니다. 그러나 iOS에서 어떤 기능이 구현되는 방법을 참조할 때 Swift를 읽고 이해하는 것도 도움이 될 수 있습니다. 또한 Kotlin Multiplatform을 탐색하는 경우에도 유용합니다.

iOS 개발자도 Android 코드를 살펴볼 때 이와 같은 원리가 적용됩니다. Kotlin을 읽고 이해하는 방법을 알면 보다 쉬워집니다.

우리는 iOS 코드를 살펴볼 때 볼 수 있는 몇 가지 일반적인 Swift 패턴에 대해 다루고, 이해하려고 노력하고 Kotlin에서의 구현과 비교해 볼 것입니다.

<img src="/assets/img/2024-05-23-SwiftCheatsheetforAndroidKotlinDevelopers_0.png" />

<div class="content-ad"></div>

# 1. 기본 사항

## 변수

Swift에서 변수와 상수는 각각 var 및 let 키워드를 사용하여 정의됩니다. 세미콜론을 사용하여 유형 주석을 제공할 수 있지만 필수는 아닙니다.

```js
// Swift
let animDurationMillis: Int = 500
var clickCount = 0

// Kotlin
val animDurationMillis: Int = 500
var clickCount = 0
```

<div class="content-ad"></div>

현재 시점에서 두 언어의 유일한 차이점은 읽기 전용 변수를 정의하는 let 대 val 키워드입니다.

## 옵셔널 / 널 가능성

옵셔널이나 널 가능한 유형의 경우, 두 언어 모두 같은 ? 문자를 사용하지만, 값이 없는 상태에 대해서는 nil 대 null이 유일한 차이점입니다.

```swift
// Swift
var foundItem: String? = nil

// Kotlin
var foundItem: String? = null
```

<div class="content-ad"></div>

nil 상태를 처리하는 여러 가지 방법이 있습니다:

- 값 확인을 위해 if 표현식을 사용하는 방법 (이 내용은 뒤에서 다룹니다),
- 옵셔널 바인딩을 사용하는 방법 (이 내용은 뒤에서 다룹니다),
- 대체/기본값을 제공하는 방법,
- 강제 언래핑을 하는 방법.

마지막 두 가지 접근법의 예시가 여기 있습니다. 코틀린이 거의 동일한 방식을 제공한다는 것을 알 수 있습니다. 문법에 약간의 차이가 있습니다 (?? vs ?: 및 ! vs !!).

```js
// Swift
//  - 대체/기본값
let actualFoundItem = foundItem ?? "empty"
//  - 강제 언래핑
let actualFoundItem = foundItem!

// Kotlin
//  - 대체/기본값
val actualFoundItem = foundItem ?: "empty"
//  - 강제 언래핑
val actualFoundItem2 = foundItem!!
```

<div class="content-ad"></div>

## 제어 흐름

if 문은 Kotlin과 거의 동일한데, Swift에서는 괄호를 생략할 수 있다는 작은 차이가 있습니다.

```js
// Swift
if foundItem != nil {
  // 무언가를 수행
}

// Kotlin
if (foundItem != null) {
  // 무언가를 수행
}
```

두 언어 모두 else if/else 분기에 대해 동일한 구문을 사용하며, 두 언어 모두 표현식으로 사용할 수 있습니다.

<div class="content-ad"></div>

```js
// 스위프트
let description = if delta <= 10 {
  "low"
} else if delta >= 50 {
  "high"
} else {
  "medium"
}

// 코틀린
val description = if (delta <= 10) {
    "low"
} else if (delta >= 50) {
    "high"
} else {
    "medium"
}
```

## Functions

Swift에서 함수는 func 키워드로 선언되며, 함수 이름, 입력 매개변수 및 반환 유형이 이어집니다.

```js
func addTwoNumbers(a: Int, b: Int) -> Int {
    return a + b
}
```

<div class="content-ad"></div>

코틀린에서는 fun 키워드와 :을 사용하여 반환 유형을 정의합니다. -` 대신에 Markdown 형식으로 표를 변경해주세요.

```kotlin
fun addTwoNumbers(a: Int, b: Int): Int {
    return a + b
}
```

# 2. 구조체 및 클래스

Swift는 데이터 모델링 관점에서 비슷한 구조체와 클래스를 지원합니다. 둘 다 속성과 함수를 정의할 수 있지만, 클래스는 참조에 의해 전달되고 구조체는 값에 의해 전달됩니다.

<div class="content-ad"></div>

기본적으로 구조체를 사용하는 것이 권장됩니다. 상속, Objective-C 호환성 등 추가 기능이 필요할 때는 클래스를 사용하세요.

```js
struct VehicleStructure {
    var maxSpeed = 0

    func printInfo() {
        print("최대 속도 \(maxSpeed)")
    }
}

class VehicleClass {
    var maxSpeed = 0

    func printInfo() {
        print("최대 속도 \(maxSpeed)")
    }
}
```

인스턴스를 생성하려면 구조체나 클래스 이름 뒤에 빈 괄호를 사용합니다.

구조체는 기본적으로 변경 불가능하므로 속성의 값 변경이 필요한 경우 let 대신 var로 선언해야 합니다. 멤버 속성의 값을 설정하기 위해 자동으로 생성된 이니셜라이저를 사용할 수 있습니다.

<div class="content-ad"></div>

```js
// 변경 가능한 구조체 인스턴스 생성
var car = VehicleStructure()
car.maxSpeed = 250
car.printInfo()

// 변경 불가능한 구조체 인스턴스 생성
let carSimple = VehicleStructure(maxSpeed: 200)
carSimple.printInfo()

// 클래스 인스턴스 생성
let bike = VehicleClass()
bike.maxSpeed = 50
bike.printInfo()
```

## Kotlin

Kotlin에서의 주요 구성 요소는 클래스입니다. 그 선언과 사용법은 Swift와 거의 동일합니다.

```js
class Vehicle {
    var maxSpeed = 0

    fun printInfo(){
        println("최대 속도는 $maxSpeed입니다.")
    }
}

// 클래스 인스턴스 생성
val vehicle = Vehicle()
vehicle.maxSpeed = 250
vehicle.printInfo()
```

<div class="content-ad"></div>

Kotlin은 클래스 생성 시 모든 클래스 속성에 값을 제공해야하는 클래스 생성자도 지원합니다.

```kotlin
class Vehicle(var maxSpeed: Int) {

    fun printInfo(){
        println("최대 속도는 $maxSpeed 입니다.")
    }
}

val vehicle = Vehicle(250)
```

Kotlin은 추상 클래스, 데이터 클래스, 인터페이스 및 실드 클래스 및 인터페이스와 같은 다른 관련 구조를 지원합니다. 더 많은 내용은 https://kotlinlang.org/docs/classes.html에서 확인할 수 있습니다.

# 3. Optional Binding


<div class="content-ad"></div>

## if let 사용법

Swift 코드베이스에서 자주 볼 수 있는 패턴 중 하나는 다음과 같습니다:

```js
let fetchedUserId: String? = "Optional id of the fetched user"
if let userId = fetchedUserId {
    // userId를 선택적인(non-optional) 상수로 사용할 수 있습니다.
    print(userId)
} else {
    // fetchedUserId가 nil/null인 경우
    throw Error("Missing user id")
}

// fetchedUserId와 userId를 if 문 바깥에서 사용할 수 있습니다.
// 그러나 둘 다 여전히 옵셔널이므로 unwrapping이 필요합니다.
```

이를 선택적 바인딩(optional binding)이라고 합니다.

<div class="content-ad"></div>

- 선택적으로 검색된 fetchedUserId 변수가 nil이 아닌 값으로 존재하는지 확인합니다.
- true이면 새로운 선택 불가능한 상수인 userId에 값을 할당합니다.
- 새로운 상수 userId는 코드 블록 내에서 참조할 수 있습니다.
- fetchedUserId가 nil이라면 else 블록을 실행합니다.

기존 변수 이름을 사용하여 더욱 간단하게 코드를 작성할 수 있습니다:

```js
let fetchedUserId: String? = "검색된 사용자의 선택 사항 ID"
if let fetchedUserId {
    // fetchedUserId를 선택 불가능한 상수로 사용할 수 있습니다.
    print(fetchedUserId)
}
```

두 경우 모두 fetchedUserId와 userId 상수를 if 문 바깥에서 사용할 수 있지만, 두 상수 모두 여전히 선택적으로 간주되기 때문에 추가로 언래핑이 필요합니다.

<div class="content-ad"></div>

코틀린
코틀린에는 이에 해당하는 특수한 패턴이 없습니다. 하나의 옵션은 if/else 문을 사용하는 것입니다. 그러나 이는 로컬 변수에 대해서만 작동하며 전역 변수에는 작동하지 않습니다. 전역 변수를 지원하려면 먼저 값을 새로운 로컬 변수/상수에 할당해야 합니다.

```js
// 글로벌 클래스 속성
var fetchedUserId: String? = "피치된 사용자의 선택 가능한 ID"

val userId = fetchedUserId
if (userId != null) {
  // userId는 선택 사항 없이 사용할 수 있습니다
} else {
  throw Exception("사용자 ID가 누락되었습니다")
}

// userId는 선택 사항 없이 어디서든 사용할 수 있습니다
```

위의 경우에는 if 문 이후에도 userId 상수를 선택 사항 없이 참조할 수 있으며, 이는 옵셔널 바인딩 패턴을 사용한 스위프트에서는 지원되지 않습니다.

대안적인 해결책은 `.let`과 같은 스코프 함수를 사용하는 것입니다. 함수 내부의 코드는 fetchedUserId가 null이 아닌 경우에만 실행됩니다. 이 코드 블록 이후에 fetchedUserId에 대한 참조는 여전히 변수가 선택 사항으로 간주되므로 널 안전성이 필요합니다.

<div class="content-ad"></div>

```kotlin
fetchedUserId?.let { userId ->
  // userId가 옵셔널이 아닌 것으로 사용할 수 있습니다
} ?: throw Exception("사용자 ID가 누락되었습니다")
```

## 보호자

또 다른 일반적인 패턴은 가드문입니다. 이는 if let 패턴과 유사합니다. 주로 함수 내에서 조기에 종료할 때 사용됩니다. 다른 점은 else 블록이 필수적이라는 것입니다.

```swift
func checkUsernameValid(username: String?) -> Bool {
    guard let username else {
        // username이 nil이므로 평가할 수 없습니다
        return false
    }
    // username을 옵셔널이 아닌 것으로 사용할 수 있습니다
    return username.count > 3
}
```

<div class="content-ad"></div>

위의 코드에서 함수는 선택적으로 변수 username을 받습니다. 그런 다음 guard문을 사용하여 username이 값이 있는지 확인합니다. 값이 없는 경우 함수를 종료합니다. 값이 있는 경우 나머지 함수에서는 username을 선택사항이 아닌 것처럼 사용할 수 있습니다.

Kotlin
Kotlin에서는 몇 가지 다른 방법으로 이를 작성할 수 있습니다. 두 가지 제안을 보여드리겠습니다:

```js
fun checkUsernameValid(username: String?): Boolean {
    if (username.isNullOrEmpty()){
        return false
    }
    return username.length > 3
}

// 또는

fun checkUsernameValid(username: String?): Boolean {
    val actualUsername = username ?: return false
    return actualUsername.length > 3
}
```

# 4. Enums

<div class="content-ad"></div>

Swift에서는 enum 키워드를 사용하여 열거형을 정의합니다. 값은 case 키워드를 사용하고 그 뒤에 열거형 케이스의 이름을 작성합니다. 열거형 케이스의 이름은 소문자로 작성하는 것이 권장되며 단수형으로 작성하는 것이 좋습니다. 각 케이스를 새로운 줄에 작성해야 합니다. 여러 케이스를 한 줄에 작성할 때는 쉼표로 구분합니다.

```js
enum Direction {
    case left
    case up
    case right
    case down
}

// 또는

enum Direction {
    case left, up, right, down
}
```

enum 케이스를 사용하려면 유형(Direction)과 사용하려는 케이스를 참조합니다. 나중에 유형을 생략하고 더 짧은 점 구문을 사용하여 케이스를 직접 참조할 수 있습니다.

```js
var selectedDirection = Direction.up
selectedDirection = .right
```

<div class="content-ad"></div>

열거형 값 확인을 위해 switch 문을 사용할 수 있습니다. Xcode는 switch 문의 모든 분기를 자동으로 작성합니다. 열거형에 대한 switch 문이 전체적이어야 하기 때문입니다.

```js
switch(selectedDirection){
case .left:
    goLeft()
case .up:
    goForward()
case .right:
    goRight()
case .down:
    goBackward()
}
```

Swift 열거형은 연관 값도 지원합니다. 즉, 각 열거형 케이스마다 다른 수의 값 유형을 가질 수 있습니다. 이는 Kotlin의 sealed class와 유사한 강력한 도메인 모델링 도구 역할을 합니다.

더 알아보기: https://docs.swift.org/swift-book/documentation/the-swift-programming-language/enumerations

<div class="content-ad"></div>

## 코틀린

코틀린에서는 enum 클래스 키워드를 사용하여 Enum을 정의합니다. 값은 쉼표로 구분하여 정의합니다. Enum 값 이름은 일반적으로 대문자로 작성하는 것이 관례이지만, 프로젝트의 스타일에 따라 다를 수 있습니다.

```js
enum class Direction {
    LEFT, UP, RIGHT, DOWN
}
```

Enum을 사용할 때는 클래스 이름과 사용하려는 값으로 참조합니다.

<div class="content-ad"></div>

```js
var selectedDirection = Direction.UP;
```

우리는 값 확인을 위해 when 문을 사용할 수 있습니다. 가능한 모든 열거형 값을 포함해야합니다.

```js
when(selectedDirection){
    Direction.LEFT -> goLeft()
    Direction.UP -> goForward()
    Direction.RIGHT -> goRight()
    Direction.DOWN -> goBackward()
}
```

Kotlin 열거형 클래스는 각 열거형 값이 값을 제공해야하는 추가 속성을 정의하는 것을 지원합니다. 그러나 Swift의 관련 값과 달리 속성은 클래스 수준에 있으며 값 레벨이 아니므로 각 값에 대해 동일한 형식이어야 합니다.

<div class="content-ad"></div>

아래는 Kotlin 공식 문서 링크입니다: https://kotlinlang.org/docs/enum-classes.html

# 5. 사전 / 맵

Swift의 딕셔너리와 Kotlin의 맵 사이의 구문은 매우 다르지만 비슷한 기본 개념을 사용합니다.

## Kotlin

<div class="content-ad"></div>

딕셔너리는 키와 값을 동일한 유형으로 연결해주는 Swift의 데이터 구조입니다. 키-값 쌍은 순서가 없이 저장됩니다. 각 키는 연결된 값을 액세스하기 위한 기준값을 나타냅니다.

딕셔너리를 선언하려면 키-값 쌍을 [Key: Value] 형식으로 대괄호 안에 쉼표로 구분하여 정의합니다. 최소 하나의 키-값 쌍을 정의하면 형식 선언을 생략할 수 있어 컴파일러가 유형을 결정할 수 있습니다.

```js
var httpErrorCodes: [Int: String] = [404: "Not found", 401: "Unauthorized"]
```

키를 사용하여 딕셔너리에서 값을 읽기 위해 서브스크립트 구문(dictionary[key])을 사용할 수 있습니다. 딕셔너리에 키가 없으면 nil을 반환합니다. ?? 연산자를 사용하여 기본값을 제공할 수도 있습니다.

<div class="content-ad"></div>

```js
func getHttpErrorCodeMessage(code: Int) -> String {
    let errorCodeMessage = httpErrorCodes[code] ?? "Unknown"
    return "Http error code \(errorCodeMessage)"
}
```

딕셔너리에 새로운 값을 쓰려면 키에 값을 할당합니다. 키가 존재하지 않으면 새로운 키-값 쌍을 컬렉션에 추가합니다. 키가 이미 존재하면 값을 업데이트합니다.

```js
// 새로운 키:값 쌍 추가
httpErrorCodes[500] = "Internal Server Error";

// 기존 키의 값 업데이트
httpErrorCodes[401] = "Requires authentication";
```

가변 또는 불변(읽기 전용) 딕셔너리를 사용하는지는 할당에 달려있습니다. let을 사용하면 선언한 후에만 읽을 수 있는 딕셔너리를 정의합니다. 쓰기를 지원하려면 var로 선언해야 합니다.

<div class="content-ad"></div>

자세히 읽기: https://docs.swift.org/swift-book/documentation/the-swift-programming-language/collectiontypes#Dictionaries

## Kotlin

맵은 고유한 키와 값의 쌍을 보유하고 각 키에 해당하는 값을 효율적으로 가져오는 컬렉션입니다.

Kotlin에서 불변(읽기 전용) 맵을 선언하려면 Map`KeyType, ValueType` 유형을 사용하고 mapOf(varargs pairs: Pair`KeyType, ValueType`) 표준 라이브러리 함수를 사용하여 초기화합니다. 적어도 하나의 키-값 쌍을 제공하면 명시적인 변수 유형 선언을 생략할 수 있습니다. 컴파일러가 유형을 결정할 수 있기 때문입니다.

<div class="content-ad"></div>

값을 선언할 때 Pair(key, value) 클래스를 직접 사용하거나 객체를 만들어주는 to 인픽스 함수를 사용할 수 있습니다.

```kotlin
val httpErrorCodes: Map<Int, String> = mapOf(
    404 to "찾을 수 없음",
    Pair(401, "권한이 없음"),
)
```

키를 사용하여 맵에서 값을 읽을 때 대괄호 표기법(map[key])을 사용할 수 있습니다. 키가 맵에 존재하지 않으면 null이 반환됩니다. ?: 연산자를 사용하여 기본값을 제공할 수 있습니다.

```kotlin
fun getHttpErrorCodeMessage(code: Int): String {
    val errorCodeMessage = httpErrorCodes[code] ?: "알 수 없음"
    return "Http 오류 코드: $errorCodeMessage"
}
```

<div class="content-ad"></div>

맵에 새로운 값을 작성하려면 MutableMap KeyType, ValueType 형식과 mutableMapOf() 팩토리 함수를 사용하여 가변 맵을 선언해야 합니다. 그런 다음 키에 값을 할당합니다. 키가 존재하지 않으면 새로운 키-값 쌍이 컬렉션에 추가됩니다. 키가 이미 존재하면 해당 값을 업데이트합니다.

```js
// 새 키:값 쌍 추가
httpErrorCodes[500] = "내부 서버 오류";

// 기존 키에 대한 값 업데이트
httpErrorCodes[401] = "인증이 필요함";
```

더 읽어보기: https://www.baeldung.com/kotlin/maps

# 6. 확장

<div class="content-ad"></div>

확장(extension)은 기존 클래스나 구조체에 새로운 기능을 추가하는 방법입니다. 때로는 코드에 액세스할 수 없는 것들에 대해서도 확장을 적용할 수 있습니다.

Swift에서는 extension 키워드를 사용하여 확장을 정의할 수 있습니다. 확장은 다른 클래스나 구조체 외부의 최상위 레벨에서 선언되어야 합니다.

```js
extension String {
    func doubled() -> String {
        return self + self
    }
}
```

위 예제에서는 String 타입에 doubled() 확장 함수를 정의했습니다. 이제 이 함수를 문자열의 인스턴스에서 호출할 수 있으며 마치 원래 정의에 포함된 것처럼 사용할 수 있습니다.

<div class="content-ad"></div>

```js
let originalStr = "Swift";
let doubledStr = originalStr.doubled();
print(doubledStr); // prints "SwiftSwift"
```

## Kotlin

Kotlin은 동일한 방식으로 작동하는 확장 함수를 사용하며 기존 클래스에 새 기능을 추가할 수 있습니다. 우리는 확장하려는 클래스 이름과 함께 점과 함수 이름이 이어지는 최상위 함수로 정의합니다.

```js
fun String.doubled(): String {
    return this + this
}
```

<div class="content-ad"></div>

이제 이 함수를 사용하여 원래 정의의 일부인 문자열 인스턴스에서 호출할 수 있습니다.

```js
val originalStr = "Kotlin"
val doubledStr = originalStr.doubled()
println(doubledStr) // "KotlinKotlin" 출력
```

# 7. 프로토콜

Swift에서 프로토콜은 클래스, 구조체 또는 열거형이 해당 요구 사항의 실제 구현을 제공함으로써 채택할 수 있는 속성, 메서드 및 기타 요구 사항의 집합입니다.

<div class="content-ad"></div>

프로토콜을 정의할 때는 프로토콜 키워드 뒤에 프로토콜 이름을 붙이면 됩니다. 이는 구조체나 클래스 선언과 유사합니다. 프로토콜 내부에서는 get할 수 있는(' get ') 또는 get과 set이 모두 가능한(' get set ') 속성을 정의할 수 있습니다.

```js
protocol RequestError {
    var errorCode: Int { get }
    var isRecoverable: Bool { get set }
}

protocol PrintableError {
    func buildErrorMessage() -> String
}
```

위 예시에서는 RequestError라는 프로토콜을 정의했는데, 이 프로토콜에는 errorCode라는 get할 수 있는 속성과 isRecoverable이라는 set이 가능한 속성이 포함되어 있습니다. 또한, adopter가 구현해야 하는 buildErrorMessage() 함수를 포함하는 PrintableError라는 프로토콜도 정의했습니다.

프로토콜을 채택하기 위해서는 클래스나 구조체를 정의하고 이름 뒤에 : ProtocolName을 추가해야 합니다. 여러 프로토콜을 선언하려면 쉼표를 사용하면 됩니다. 그런 다음 클래스나 구조체의 본문에서 프로토콜로부터 요구되는 내용을 정의해야 합니다.

<div class="content-ad"></div>

```js
class ServerHttpError: RequestError, PrintableError {
    var errorCode: Int = 500
    var isRecoverable: Bool = false

    func buildErrorMessage() -> String {
        return "Server side http error with error code \(errorCode)"
    }
}

struct ConnectionError: RequestError, PrintableError {
    var errorCode: Int
    var isRecoverable: Bool

    func buildErrorMessage() -> String {
        return "Local connection error"
    }
}
```

우리는 RequestError와 PrintableError 프로토콜을 채택하는 ServerHttpError 클래스를 정의했고, 두 속성의 기본값과 함수의 구현을 정의했습니다. 또한 두 속성을 선언하고 함수의 구현을 제공하는 ConnectionError 구조체를 가지고 있습니다.

이제 ServerHttpError 및 ConnectionError의 인스턴스를 생성하고 이들을 RequestError 또는 PrintableError 유형으로 전달할 수 있습니다. RequestError 유형을 수락하는 onRequestError() 함수에서 error가 PrintableError 프로토콜을 준수하는지 확인하여 오류 메시지를 구성합니다.

```js
func onRequestError(error: RequestError) {
    if let printableError = error as? PrintableError {
        print(printableError.buildErrorMessage())
    }
    print("Is recoverable: \(error.isRecoverable)")
}

let firstError = ServerHttpError()
firstError.errorCode = 503
firstError.isRecoverable = false

let secondError = ConnectionError(errorCode: 404, isRecoverable: true)

// "Server side http error with error code 503. Is recoverable: false"
onRequestError(error: firstError)
// "Local connection error. Is recoverable: true"
onRequestError(error: secondError)
```

<div class="content-ad"></div>

이것은 프로토콜을 사용하는 간단한 예시입니다. Swift의 프로토콜은 상속, 합성, 연관 타입, 제네릭 등과 같이 더 고급적인 사용 사례를 지원합니다. 더 많은 내용은 https://docs.swift.org/swift-book/documentation/the-swift-programming-language/protocols에서 확인할 수 있어요.

## Kotlin

위의 예시는 인터페이스, 추상 클래스, sealed 클래스 등을 사용하여 Kotlin으로 여러 방법으로 작성할 수 있습니다. 그러나 Swift의 프로토콜을 가장 가깝게 표현하는 것은 아마도 인터페이스일 것입니다. 이는 속성과 함수를 정의하며, 상속, 구성, 그리고 제네릭을 지원합니다.

인터페이스를 정의할 때는 interface 키워드를 사용하고 이름을 이어서 작성합니다. 본문에서는 속성과 함수를 정의합니다. 읽기 전용 속성은 val 키워드를 사용하여 정의하고, 읽기/쓰기 속성은 var 키워드를 사용합니다.

<div class="content-ad"></div>

이 인터페이스를 구현하도록 클래스에 지정하는 방법은, 클래스 이름 뒤에 InterfaceName을 사용합니다. 여러 인터페이스를 구현하려면 쉼표를 사용합니다. 그런 다음 모든 속성과 함수를 override 키워드와 함께 정의해야 합니다.

```js
interface RequestError {
    val errorCode: Int
    var isRecoverable: Boolean
}

interface PrintableError {
    fun buildErrorMessage(): String
}

class ServerHttpError(
    override val errorCode: Int,
    override var isRecoverable: Boolean
) : RequestError, PrintableError {
    override fun buildErrorMessage(): String {
        return "Server side http error with error code $errorCode"
    }
}

class ConnectionError : RequestError, PrintableError {
    override val errorCode: Int
        get() = 404
    override var isRecoverable: Boolean = true

    override fun buildErrorMessage(): String {
        return "Local connection error"
    }
}
```

이제 ServerHttpError 및 ConnectionError의 인스턴스를 생성하고, 함수에 RequestError 유형으로 전달할 수 있습니다.

```js
fun onRequestError(error: RequestError) {
    if (error is PrintableError) {
        println(error.buildErrorMessage())
    }
    println("$errorMessage. Is recoverable: ${error.isRecoverable}")
}

val firstError = ServerHttpError(errorCode = 503, isRecoverable = false)
val secondError = ConnectionError()
// "Server side http error with error code 503. Is recoverable: false"
onRequestError(firstError)
// "Local connection error. Is recoverable: true"
onRequestError(secondError)
```

<div class="content-ad"></div>


Read more at https://kotlinlang.org/docs/interfaces.html

# 결론

일반적인 Swift 패턴을 이해하고 Kotlin에서 어떻게 번역되는지 알면 코드가 하는 일을 더 잘 이해할 수 있습니다. 이웃 플랫폼에서 어떤 기능이 구현되었는지 보거나 코드 검토를 수행하거나 기술 사양/제안서를 검토하거나 작업을 Kotlin Multiplatform으로 수행하는 여러 방법이 있습니다.

Swift 언어의 기본적인 내용과 Kotlin과 비교하는 방법을 살펴보았습니다. 또한, 선택적 바인딩, 딕셔너리, 익스텐션, 구조 및 프로토콜과 같은 전형적인 iOS 프로젝트에서 발견할 수 있는 일반적인 패턴에 대해서도 다루었습니다.


<div class="content-ad"></div>

이 문이 도움이 되었다면 댓글로 알려주세요! Swift 코드를 읽거나 검토하거나 작성하는 경험에 대해 공유하는 것을 장려합니다.

참고 자료:

- https://kotlinlang.org/docs/home.html
- https://docs.swift.org/swift-book/documentation/the-swift-programming-language/thebasics

Andrej Rolih 님의 검토와 피드백에 감사드립니다.
