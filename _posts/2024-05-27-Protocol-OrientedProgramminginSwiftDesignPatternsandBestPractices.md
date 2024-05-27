---
title: "스위프트에서의 프로토콜 지향 프로그래밍 디자인 패턴과 최선의 광고"
description: ""
coverImage: "/assets/img/2024-05-27-Protocol-OrientedProgramminginSwiftDesignPatternsandBestPractices_0.png"
date: 2024-05-27 16:27
ogImage: 
  url: /assets/img/2024-05-27-Protocol-OrientedProgramminginSwiftDesignPatternsandBestPractices_0.png
tag: Tech
originalTitle: "Protocol-Oriented Programming in Swift: Design Patterns and Best Practices"
link: "https://medium.com/@priyans05/protocol-oriented-programming-in-swift-design-patterns-and-best-practices-70b2ee030471"
---


<img src="/assets/img/2024-05-27-Protocol-OrientedProgramminginSwiftDesignPatternsandBestPractices_0.png" />

소개

프로토콜 지향 프로그래밍(POP)은 스위프트 개발에서 핵심 패러다임 중 하나로 자리 잡았으며, 전통적인 객체지향 프로그래밍에 강력한 대안을 제공합니다. 프로토콜에 주목함으로써, 스위프트는 개발자들이 유연하고 재사용 가능하며 테스트 가능한 코드를 작성할 수 있게 해줍니다. 이 글에서는 프로토콜 지향 프로그래밍의 원칙을 탐구하고, 일반적인 디자인 패턴을 살펴보며, 스위프트에서 프로토콜을 통해 견고한 애플리케이션을 구축하는 데 가장 좋은 방법을 논의할 것입니다.

왜 프로토콜 지향 프로그래밍을 해야 할까요?

<div class="content-ad"></div>

POP은 작업이나 기능에 대한 청사진을 정의하는 데 프로토콜 사용을 장려합니다. 이 접근 방식은 여러 가지 이점을 제공합니다:

- 유연성: 프로토콜을 사용하면 타입이 여러 프로토콜을 준수하도록 하여 보다 유연한 아키텍처를 구축할 수 있습니다.
- 재사용성: 공통 기능은 프로토콜에 정의하고 다양한 타입 간에 재사용할 수 있습니다.
- 테스트 용이성: 프로토콜은 의존성 주입을 용이하게 하여 단위 테스트에서 의존성을 가짜로 대체하기 쉽습니다.
- 결합도 낮추기: 프로토콜은 인터페이스를 정의함으로써 코드를 결합도 낮추며 구체적인 구현 대신 인터페이스를 정의합니다. 역할 분리를 촉진합니다.

프로토콜을 활용하기
기본 프로토콜 정의

<div class="content-ad"></div>

```js
프로토콜 Drivable {
    var speed: Double { get }
    func drive()
}

구조체 Car: Drivable {
    var speed: Double

    func drive() {
        print("속도: \(speed) km/h로 주행 중")
    }
}

let car = Car(speed: 120)
car.drive()  // 출력: 속도: 120.0 km/h로 주행 중
```

이 예제에서는 speed 속성과 drive 메서드가 있는 Drivable 프로토콜을 정의합니다. Car 구조체는 Drivable 프로토콜을 준수하며 필요한 속성과 메서드를 구현합니다.

프로토콜 익스텐션

프로토콜 익스텐션을 사용하여 프로토콜 메서드에 대한 기본 구현을 제공할 수 있으며 주어진 유형의 기능을 확장할 수 있습니다.
```

<div class="content-ad"></div>

프로토콜 확장 예제

```swift
protocol Drivable {
    var speed: Double { get }
    func drive()
}

extension Drivable {
    func drive() {
        print("Driving at \(speed) km/h")
    }

    func stop() {
        print("Stopped.")
    }
}

struct Car: Drivable {
    var speed: Double
}

let car = Car(speed: 120)
car.drive()  // 출력: 120.0 km/h로 주행 중
car.stop()   // 출력: 정지됨
```

여기에는 프로토콜 확장 내에서 drive 메서드에 대한 기본 구현이 있습니다. 또한 모든 준수하는 타입이 추가 구현없이 사용할 수 있는 새로운 stop 메서드가 도입되었습니다.

프로토콜 조합

<div class="content-ad"></div>

스위프트를 사용하면 여러 프로토콜을 조합하여 타입이 조합된 프로토콜을 준수할 수 있습니다.

프로토콜 조합 예시

```js
protocol Drivable {
    var speed: Double { get }
    func drive()
}

protocol Flyable {
    var altitude: Double { get }
    func fly()
}

struct FlyingCar: Drivable, Flyable {
    var speed: Double
    var altitude: Double

    func drive() {
        print("Driving at \(speed) km/h")
    }

    func fly() {
        print("Flying at \(altitude) meters")
    }
}

let flyingCar = FlyingCar(speed: 120, altitude: 1000)
flyingCar.drive()  // 출력: Driving at 120.0 km/h
flyingCar.fly()    // 출력: Flying at 1000.0 meters
```

이 예시에서 FlyingCar 구조체는 Drivable 및 Flyable 프로토콜을 모두 준수하며, 프로토콜 조합이 복잡한 기능을 가능하게 함을 보여줍니다.

<div class="content-ad"></div>

프로토콜 중심의 디자인 패턴

1. 전략 패턴

전략 패턴은 알고리즘들의 집합을 정의하고 각각을 캡슐화하여 교환 가능하게 만듭니다. Swift에서 이 패턴을 구현하는 데는 프로토콜이 이상적입니다.

전략 패턴 예시

<div class="content-ad"></div>

```js
프로토콜 PaymentStrategy {
    func pay(amount: Double)
}

구조체 CreditCardPayment: PaymentStrategy {
    func pay(amount: Double) {
        print("\(amount) 금액으로 신용카드를 사용해 결제했습니다")
    }
}

구조체 PayPalPayment: PaymentStrategy {
    func pay(amount: Double) {
        print("\(amount) 금액으로 PayPal을 사용해 결제했습니다")
    }
}

구조체 ShoppingCart {
    var paymentStrategy: PaymentStrategy

    func checkout(amount: Double) {
        paymentStrategy.pay(amount: amount)
    }
}

let creditCardPayment = CreditCardPayment()
let payPalPayment = PayPalPayment()

var cart = ShoppingCart(paymentStrategy: creditCardPayment)
cart.checkout(amount: 100)  // 결과: 100.0 금액으로 신용카드를 사용해 결제했습니다
cart.paymentStrategy = payPalPayment
cart.checkout(amount: 200)  // 결과: 200.0 금액으로 PayPal을 사용해 결제했습니다
```

여기서 PaymentStrategy 프로토콜은 결제를 위한 메서드를 정의합니다. 다양한 결제 전략(CreditCardPayment과 PayPalPayment)이 이 프로토콜을 준수합니다. ShoppingCart은 전략을 사용하여 결제를 수행하며, 결제 방법을 동적으로 전환할 수 있습니다.

2. 의존성 주입

의존성 주입(Dependency Injection, DI)은 IoC(제어의 역전)를 구현하는 데 사용되는 디자인 패턴으로, 클래스가 내부적으로 생성하는 대신 외부 소스에서 의존성을 받을 수 있도록 합니다.
```

<div class="content-ad"></div>

의존성 주입을 사용한 예제

```js
protocol DataService {
    func fetchData() -> String
}
```

```js
struct APIService: DataService {
    func fetchData() -> String {
        return "API에서 데이터를 가져왔습니다"
    }
}
struct MockService: DataService {
    func fetchData() -> String {
        return "모의 데이터입니다"
    }
}
struct DataManager {
    var service: DataService
    func getData() -> String {
        return service.fetchData()
    }
}
let apiService = APIService()
let mockService = MockService()
let dataManager = DataManager(service: apiService)
print(dataManager.getData())  // 출력: API에서 데이터를 가져왔습니다
let testManager = DataManager(service: mockService)
print(testManager.getData())  // 출력: 모의 데이터입니다
```

이 예제에서는 DataService 프로토콜이 데이터를 가져오는 메서드를 정의합니다. APIService와 MockService 구조체는 이 프로토콜을 준수하여 다른 구현을 제공합니다. DataManager는 데이터를 가져오기 위해 DataService에 의존하며, 실제 서비스와 테스트를 위한 모의 서비스를 쉽게 변경할 수 있습니다.

<div class="content-ad"></div>

프로토콜 지향 프로그래밍의 Best Practices

- 상속 대신 구성 사용: 클래스 상속에 의존하는 대신 프로토콜을 사용하여 함께 조합할 수 있는 재사용 가능한 동작을 정의하세요.
- 작고 집중된 프로토콜 정의: 프로토콜을 단일 책임 원칙(SRP)을 준수하며 집중시켜 작고 집중된 상태로 유지하세요. 이렇게 하면 이해하고 구현하기 쉬워집니다.
- 프로토콜 익스텐션 올바르게 활용: 프로토콜 익스텐션에서 일반적인 동작에 대한 기본 구현을 제공하지만 오버라이드할 필요가 있는 로직을 너무 많이 포함하지 않도록 주의하세요.
- 프로토콜 조합 활용: 여러 프로토콜을 결합하여 유연하고 모듈식 설계를 만드세요.
- 프로토콜 및 익스텐션 문서화: 프로토콜과 그 익스텐션의 의도된 사용 및 요구 사항을 명확히 문서화하여 올바르게 사용되도록 하세요.

결론

Swift에서의 프로토콜 지향 프로그래밍은 유연하고 재사용 가능하며 테스트 가능한 코드를 작성하는 강력한 방법을 제공합니다. 프로토콜 및 그 조합에 집중함으로써 관심사 분리를 촉진하고 유지보수성을 향상시키는 견고한 아키텍처를 만들 수 있습니다. Strategy 및 Dependency Injection과 같은 디자인 패턴을 구현하거나 프로토콜 익스텐션을 사용하여 기본 동작을 활용하는 경우, 프로토콜 지향 프로그래밍을 도입하면 Swift 개발 기술을 크게 향상시킬 수 있습니다. 프로토콜의 힘을 받아 코드를 더 높은 수준으로 발전시키세요. 즐거운 코딩 되세요!