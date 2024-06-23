---
title: "Swift에서 클로저 사용하는 방법 - 완벽 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-AGuideToClosuresInSwift_0.png"
date: 2024-06-23 21:23
ogImage: 
  url: /assets/img/2024-06-23-AGuideToClosuresInSwift_0.png
tag: Tech
originalTitle: "A Guide To Closures In Swift"
link: "https://medium.com/swiftable/a-guide-to-closures-in-swift-368e6aca6d71"
---


![Closure](/assets/img/2024-06-23-AGuideToClosuresInSwift_0.png)

스위프트에서 클로저는 코드에서 전달하고 사용할 수 있는 자체 포함 블록 기능입니다. 클로저는 함수와 유사하지만 구문 최적화가 있으며 주변 컨텍스트에서 변수 및 상수에 대한 참조를 캡처하고 저장할 수 있습니다. 이 동작은 클로저가 동작을 캡슐화하고 값을 전달하는 데 강력하게 만듭니다.

클로저는 다양한 시나리오에서 사용할 수 있습니다. 이 글에서는 다음과 같은 내용을 배우게 됩니다:

- 인수 및 반환 값으로서
- 정렬 및 필터링을 위해
- 비동기 작업
- 값 캡처

<div class="content-ad"></div>

# 인수 및 반환 값으로서의 클로저:

클로저는 함수 및 메소드에 인수로 전달될 수 있으며, 함수에서 반환하거나 변수 또는 속성에 저장할 수도 있습니다.

예시:

```js
// 클로저를 인수로 전달하는 함수
func performOperation(on a: Int, and b: Int, operation: (Int, Int) -> Int) -> Int {
    return operation(a, b)
}

// 다양한 연산을 위한 클로저 표현 정의
let addClosure: (Int, Int) -> Int = { $0 + $1 }
let subtractClosure: (Int, Int) -> Int = { $0 - $1 }
let multiplyClosure: (Int, Int) -> Int = { $0 * $1 }

let resultAdd = performOperation(on: 5, and: 3, operation: addClosure) // 결과: 8
let resultSubtract = performOperation(on: 10, and: 4, operation: subtractClosure) // 결과: 6
let resultMultiply = performOperation(on: 6, and: 2, operation: multiplyClosure) // 결과: 12

print(resultAdd, resultSubtract, resultMultiply)

// 클로저를 반환하는 함수
func makeMultiplier(factor: Int) -> (Int) -> Int {
    return { number in
        return number * factor
    }
}

let double = makeMultiplier(factor: 2)
let triple = makeMultiplier(factor: 3)

let resultDouble = double(5) // 결과: 10
let resultTriple = triple(4) // 결과: 12

print(resultDouble, resultTriple)
```

<div class="content-ad"></div>

이 예제에서는 performOperation이라는 함수를 정의합니다. 이 함수는 두 정수와 산술 연산을 나타내는 클로저를 받습니다. 이 함수는 제공된 클로저를 두 정수에 적용합니다.

또한 makeMultiplier라는 함수를 정의합니다. 이 함수는 클로저를 반환합니다. 반환된 클로저는 인수를 지정된 인수로 곱합니다.

그런 다음, 덧셈, 뺄셈 및 곱셈을 위한 다른 클로저 표현식을 만듭니다. 이러한 클로저를 performOperation의 인수로 전달하고, 반환된 makeMultiplier의 클로저를 사용하여 숫자를 2배와 3배로 만듭니다.

# Sorting and Filtering:

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경해 주세요.

<div class="content-ad"></div>

```swift
struct Person {
    var name: String
    var age: Int
}

let people = [
    Person(name: "Alice", age: 28),
    Person(name: "Bob", age: 22),
    Person(name: "Charlie", age: 35)
]

// 나이 기준으로 정렬하는 클로저 사용
let sortedByAge = people.sorted { (person1, person2) in
    return person1.age < person2.age
}

print(sortedByAge)
// 결과: [Person(name: "Bob", age: 22), Person(name: "Alice", age: 28), Person(name: "Charlie", age: 35)]

```

이 예제에서는 sorted(by:) 메서드가 각 Person 인스턴스의 age 속성을 기준으로 people 배열을 정렬하는 클로저를 사용합니다.

2. 클로저로 필터링:

클로저를 사용하여 특정 조건에 따라 배열에서 요소를 필터링할 수도 있습니다. filter(_:) 메서드를 사용하여, 필터링된 결과에 포함되어야 하는지 여부를 나타내는 Boolean 값을 반환하는 클로저를 제공할 수 있습니다.

<div class="content-ad"></div>

예시:

```js
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// 클로저를 사용하여 짝수만 필터링하는 예시
let evenNumbers = numbers.filter { number in
    return number % 2 == 0
}

print(evenNumbers) // 출력: [2, 4, 6, 8, 10]
```

본 예시에서는 filter(_:) 메서드가 클로저를 사용하여 numbers 배열에서 홀수를 필터링하는 방법을 보여줍니다.

# 비동기 작업:

<div class="content-ad"></div>

클로저는 네트워크 요청이나 애니메이션과 같은 비동기 작업에서 완료 핸들러로 자주 사용됩니다.

비동기 예시: 네트워크 요청

```swift
func fetchData(completion: @escaping (Result<Data, Error>) -> Void) {
    // 네트워크 요청 지연 시간 시뮬레이션
    DispatchQueue.global().asyncAfter(deadline: .now() + 2) {
        if Bool.random() { // 성공 또는 실패를 랜덤하게 시뮬레이션
            let data = Data("샘플 데이터".utf8)
            completion(.success(data))
        } else {
            let error = NSError(domain: "com.example", code: 1, userInfo: nil)
            completion(.failure(error))
        }
    }
}

// 클로저를 사용하여 fetchData 함수 호출
fetchData { result in
    switch result {
    case .success(let data):
        print("받은 데이터:", data)
    case .failure(let error):
        print("에러:", error)
    }
}
```

이 예시에서 fetchData 함수는 지연 후 완료되는 네트워크 요청을 시뮬레이션합니다. 네트워크 요청이 완료되면 실행되는 클로저(completion)를 인자로 받습니다. 클로저는 성공(가져온 데이터와 함께) 또는 실패(에러와 함께)를 나타내는 Result 타입을 갖습니다.

<div class="content-ad"></div>

비동기 예제: 애니메이션

```js
import UIKit

UIView.animate(withDuration: 0.5, animations: {
    // 애니메이션할 UI 속성 업데이트
    someView.alpha = 0.0
}) { _ in
    // 이 클로저는 애니메이션이 완료될 때 실행됩니다
    someView.removeFromSuperview()
}
```

이 예제에서는 UIView.animate(withDuration:animations:completion:) 함수를 사용하여 뷰의 알파 속성을 애니메이션화합니다. animations로 전달된 클로저는 애니메이션 변경을 정의하며, completion 클로저는 애니메이션이 완료되면 실행됩니다.

# 값 캡처하기:

<div class="content-ad"></div>

클로저는 주변 컨텍스트에서 변수와 상수에 대한 참조를 캡처하고 저장할 수 있습니다. 이는 클로저 내부에서 상태를 유지하는 데 유용할 수 있습니다.

예시:

```js
func makeIncrementer(incrementAmount: Int) -> () -> Int {
    var total = 0

    let incrementer: () -> Int = {
        total += incrementAmount
        return total
    }

    return incrementer
}

let incrementByTwo = makeIncrementer(incrementAmount: 2)
print(incrementByTwo()) // Prints: 2
print(incrementByTwo()) // Prints: 4

let incrementByFive = makeIncrementer(incrementAmount: 5)
print(incrementByFive()) // Prints: 5
print(incrementByFive()) // Prints: 10

print(incrementByTwo()) // Prints: 6
```

이 예시에서 makeIncrementer 함수는 주변 컨텍스트에서 total 변수를 캡처하는 클로저인 incrementer를 반환합니다. 반환된 클로저는 캡처된 total을 지정된 incrementAmount만큼 증가시킵니다.

<div class="content-ad"></div>

makeIncrementer(incrementAmount: 2)을 호출하면 incrementByTwo 클로저를 얻게 됩니다. incrementByTwo()를 호출할 때마다 총합이 2씩 증가하고 업데이트된 값이 반환됩니다.

비슷하게, makeIncrementer(incrementAmount: 5)를 호출하면 총합을 5씩 증가시키는 incrementByFive 클로저를 얻게 됩니다.

클로저는 생성될 때 값을 캡처합니다. 이는 각 캡처된 값이 클로저 인스턴스에 고유하다는 것을 의미합니다. 이 예시에서 incrementByTwo와 incrementByFive는 각각 고유한 캡처된 총합 값을 갖습니다.

클로저에서 값들을 캡처하는 것은 상태를 유지하고 컨텍스트를 기억하는 동작을 만들 수 있게 해줍니다. 이러한 특성으로 인해 클로저는 다양한 프로그래밍 시나리오에 강력한 도구가 됩니다.

<div class="content-ad"></div>

# 문법:

스위프트에서 클로저의 기본 문법은 다음과 같습니다:

```js
let closureName: (파라미터들) -> 반환타입 = { // 클로저 내용 }
```

두 정수를 더하는 클로저의 예시:

<div class="content-ad"></div>

```swift
let addClosure: (Int, Int) -> Int = { (a, b) in
    return a + b
}

let result = addClosure(5, 3) // 결과는 8이 될 것입니다
```

클로저는 그들의 타입을 유추할 수 있는 경우에 더 간결한 형식으로 작성할 수 있습니다:

```swift
let addClosure = { (a: Int, b: Int) in
    return a + b
}

let result = addClosure(5, 3)
```

더 간결하게도:

<div class="content-ad"></div>

```js
let addClosure: (Int, Int) -> Int = { $0 + $1 }

let result = addClosure(5, 3)
```

클로저는 주변 컨텍스트에서 값들을 캡쳐할 수 있어요.

예를들어:

```js
func makeIncrementer(incrementAmount: Int) -> () -> Int {
    var total = 0
    let incrementer: () -> Int = {
        total += incrementAmount
        return total
    }
    return incrementer
}

let incrementByTwo = makeIncrementer(incrementAmount: 2)
print(incrementByTwo()) // 2 출력
print(incrementByTwo()) // 4 출력
```

<div class="content-ad"></div>

이 예시에서 증가자 클로저는 주변 함수 컨텍스트에서 total 변수와 incrementAmount 매개변수를 캡처합니다.

이 게시물을 즐기셨다면 공유하고 클랩도 부탁드려요👏🏻👏🏻👏🏻👏🏻👏🏻

또한 아래 연락처로 연락하실 수도 있어요📲

LinkedIn

<div class="content-ad"></div>

아래에서 제 코드를 확인할 수 있어요👇🏻

GitHub

잘 읽었나요?

의견, 질문 또는 추천이 있으면 자유롭게 아래 댓글 섹션에 남겨주세요💬

<div class="content-ad"></div>

💁🏻‍♀️ 즐거운 코딩하세요!

감사합니다 😊