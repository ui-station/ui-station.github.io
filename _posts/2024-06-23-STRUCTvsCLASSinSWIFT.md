---
title: "SWIFT에서 STRUCT와 CLASS 비교 분석"
description: ""
coverImage: "/assets/img/2024-06-23-STRUCTvsCLASSinSWIFT_0.png"
date: 2024-06-23 01:38
ogImage: 
  url: /assets/img/2024-06-23-STRUCTvsCLASSinSWIFT_0.png
tag: Tech
originalTitle: "STRUCT vs CLASS in SWIFT"
link: "https://medium.com/@pragati91pandey/struct-vs-class-in-swift-ef9dd1771462"
---


스위프트에서는 Struct와 Class를 모두 사용하지만 언제 어떤 것을 사용해야 하는지와 그 차이점이 무엇인지 혼란스러울 수 있습니다. 이 둘 사이의 주요 차이점은 다음과 같습니다:

![Struct vs Class in Swift](/assets/img/2024-06-23-STRUCTvsCLASSinSWIFT_0.png)

구조체: 구조체는 값 타입으로, 각 인스턴스가 데이터의 고유한 복사본을 유지합니다. 한 인스턴스를 수정해도 다른 복사본에는 영향을 미치지 않습니다. Struct 인스턴스는 스택에 할당됩니다.

더 나은 이해를 위해 예시를 살펴봅시다.

<div class="content-ad"></div>

```swift
struct Person {
    var name: String
}

var person1 = Person(name: "Joy")
var person2 = person1

print(person1.name) //prints "Joy"
print(person2.name) //prints "Joy"

person2.name = "Mary"

print(person1.name)//prints "Joy"
print(person2.name)//prints "Mary"
```

위 예제에서는 Person의 인스턴스인 person1을 만들었습니다. 이제 person1의 데이터를 복사하여 새 인스턴스 person2가 생성됩니다. 이 시점에서 person1과 person2는 초기값이 같은 두 개의 별도 인스턴스입니다. 따라서 person2를 수정해도 person1에는 영향을 미치지 않습니다.

클래스: 클래스는 참조 유형이며, 클래스 인스턴스를 할당할 때 메모리 내의 동일한 인스턴스에 대한 참조를 전달합니다. 어떤 인스턴스에서 변경을 가하면 동일한 메모리를 가리키고 있는 다른 모든 인스턴스에 영향을 줍니다. 클래스 인스턴스는 힙에 할당됩니다.

더 나은 이해를 위해 위 예제를 클래스 관점에서 살펴보겠습니다.


<div class="content-ad"></div>

```js
class Person {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

var person1 = Person(name: "Joy")
var person2 = person1

print(person1.name)//prints "Joy"
print(person2.name)//prints "Joy"

person2.name = "Mary"

print(person1.name)//prints "Mary"
print(person2.name)//prints "Mary"
```

위 예제에서 우리는 Person의 인스턴스를 person1로 만들었습니다. 이제 새로운 인스턴스 person2가 person1에서 생성되었습니다. 이 시점에서 person1과 person2는 참조 유형이므로 동일한 메모리 주소를 가리킵니다. 따라서 person2를 수정하면 person1에 영향을 미칠 것입니다.

구조체: 구조체는 상속을 지원하지 않습니다. 다른 구조체로부터 속성, 메서드 및 함수를 상속받는 기능이 없습니다.

```js
struct Person {
    var name: String
}

struct Employee: Person { // 컴파일 시간 오류
    // 프로토콜을 통해 이에 접근 가능
}
```

<div class="content-ad"></div>

클래스: 클래스는 상속을 지원합니다. 다른 클래스로부터 속성, 메서드 및 기능을 상속할 수 있습니다.

```js
class Employee: Person {   

    var employeeId: Int
    
    init(employeeId: Int, name: String) {
        self.employeeId = employeeId
        super.init(name: name)
    }    
}

var obj = Employee(employeeId: 20, name: "Abhay")
```

구조체: 구조체 인스턴스가 var로 선언되면 가변이 될 수 있지만, 인스턴스가 let으로 정의되면 수정할 수 없습니다. 즉, 불변입니다.

```js
struct Person {
    var name: String
}

let person1 = Person(name: "Joy")
person1.name = "Mary"// 오류가 발생합니다

var person1 = Person(name: "Joy")
person1.name = "Mary"// 올바르게 작동하여 수정됩니다
```

<div class="content-ad"></div>

클래스(Class): let 또는 var로 선언된 클래스는 변경 가능(mutable)합니다.

```js
class Person {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

let person1 = Person(name: "Joy") || var person1 = Person(name: "Joy")
person1.name = "Mary" // 둘 다 동일하게 작동합니다
```

구조체(Struct): 사용자 정의 이니셜라이저를 제공하지 않으면 구조체는 기본 멤버 지정 이니셜라이저를 갖습니다. 사용자 정의 이니셜라이저를 정의할 수도 있습니다.

```js
struct Person {
    var name: String
}

또는

struct Person {
    var name: String
    init(name: String) {
        self.name = name
    }
}

// 둘 다 작동합니다
```

<div class="content-ad"></div>

클래스: 클래스에서는 모든 프로퍼티가 초기화되어 있어야 오류가 발생하지 않도록 해야 합니다. 클래스에는 지정 이니셜라이저와 편의 이니셜라이저가 있습니다.

```js
class Person {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}
```

불변하고 작고 간단한 데이터를 다룰 때는 구조체를 사용해야 합니다. 구조체는 더 안전하며 ARC(Automatic Reference Counting)와 힙 할당이 일어나지 않습니다.

상속, 가변 또는 참조 프로퍼티가 필요한 경우 클래스를 사용합니다.