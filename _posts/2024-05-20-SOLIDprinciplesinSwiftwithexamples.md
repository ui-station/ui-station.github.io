---
title: "Swift에서 SOLID 원칙에 대한 예제"
description: ""
coverImage: "/assets/img/2024-05-20-SOLIDprinciplesinSwiftwithexamples_0.png"
date: 2024-05-20 17:46
ogImage:
  url: /assets/img/2024-05-20-SOLIDprinciplesinSwiftwithexamples_0.png
tag: Tech
originalTitle: "SOLID principles in Swift with examples"
link: "https://medium.com/@bakshioye/solid-principles-in-swift-with-examples-d62011361798"
---

위 문서의 향상된 버전을 확인하세요. 향상된 구문 강조와 최신 업데이트가 모두 포함된 개인 블로그에서 확인할 수 있습니다! 피드백과 참여를 환영하며 앞으로의 콘텐츠 제작을 지원해주세요:

# (S) 단일 책임 원칙

단일 책임 원칙(SRP)은 각 엔티티가 단 하나의 작업이나 문제 해결만을 담당해야 한다는 원칙을 말합니다.

일반적으로 SRP를 따르지 않고 코드를 작성하는 예시를 살펴보겠습니다:

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
import Foundation

구조체 처리기 {

    개인 처리기

    함수 처리() {

        // 사람 데이터 가져오기
        let personAsData = 가져오기()

        // 데이터를 필요한 객체로 파싱
        let person = 파싱(personAsData)

        // 개인 객체 지역적으로 저장
        저장하기(person)
    }

    사적 함수 가져오기() -> 데이터 {
        """
        {
            "name": "Shubham Bakshi",
            "age": 28,
            "canTakeANapAnywhere": true

        }
        """.data(using: .utf8)!
    }

    사적 함수 파싱(_ data: Data) -> 개인 {
        try! JSONDecoder().decode(개인.self, from: data)
    }

    사적 함수 저장하기(_ person: 개인) {
        UserDefaults.standard.set(person, forKey: "person")
    }

}

구조체 개인: Codable {
    let name: String
    let age: Int
    let canTakeANapAnywhere: Bool
}
```

우리의 SRP가 깨진 부분은 Handler 엔티티 자체 내에서 모든 작업을 수행하고 있기 때문에 이를 각각의 엔티티로 분리하고 따로 처리해야 합니다. 이제 SRP를 따르는 코드를 살펴보겠습니다:

```js
import Foundation

구조체 처리기 {

    개인 가져오기: 가져오기 핸들러
    개인 파싱: 변환 핸들러
    개인 저장: 저장 핸들러

    이달함수(init: FetchHandler, ParseHandler, SaveHandler) {
        self.fetchHandler = fetchHandler
        self.parseHandler = parseHandler
        self.saveHandler = saveHandler
    }

    함수 처리() {

        // 사람 데이터 가져오기
        let personAsData = fetchHandler.fetch()

        // 데이터를 필요한 객체로 파싱
        let person = parseHandler.parse(personAsData)

        // 개인 객체 지역적으로 저장
        saveHandler.save(person)
    }

}

구조체 가져오기 핸들러 {

    함수 가져오기() -> 데이터 {
        """
        {
            "name": "Shubham Bakshi",
            "age": 28,
            "canTakeANapAnywhere": true

        }
        """.data(using: .utf8)!
    }

}

구조체 변환 핸들러 {

    함수 파싱(_ data: Data) -> 개인 {
        try! JSONDecoder().decode(개인.self, from: data)
    }

}

구조체 저장 핸들러 {

    함수 저장(_ person: 개인) {
        UserDefaults.standard.set(person, forKey: "person")
    }

}

구조체 개인: Codable {
    let name: String
    let age: Int
    let canTakeANapAnywhere: Bool
}
```

SRP는 다음과 같은 이점을 제공합니다:

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

- 더 높은 응집력
- 코드 중복 가능성 감소
- 부풀어 오른 클래스 감소
- 테스트 및 유지 관리가 용이
- 여러 책임이 분리되어 코드가 깔끔해짐

# (O) 개방-폐쇄 원칙

개방-폐쇄 원칙(OCP)은 엔티티가 확장에 대해 열려 있지만 수정에 대해서는 닫혀 있어야 한다는 것을 말합니다. 이는 클래스가 기존 코드를 수정하지 않고 쉽게 확장 가능해야 함을 의미합니다.

처음에는 정의가 모순적으로 들릴 수 있지만 규모에 맞게 이 원칙을 따르기 시작하면 더 많은 의미를 갖게 됩니다. 이 원칙을 정말로 좁혀보면: 클래스를 만들고 확장할 수 있지만 원본 클래스를 수정할 수 없습니다. 이렇게하는 이유는:

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

- 다른 사람이 클래스를 작성했고 여기에 기능을 추가하거나 변경을 해야 할 때 원래 작성자에게 계속 변경을 요청해야 하거나 직접 변경해야 할 수 있었습니다. 이제 원 작성자는 기능이 작동하도록 작성한 모든 코드에 대해 다른 생각 과정을 가질 수 있습니다.
- 게다가 클래스는 더 많은 관심사(또는 간단히 기능)를 통합(또는 단순히 추가)할 수 있는데, 이는 SRP를 깨뜨릴 수 있습니다.

위에서 SRP가 활성화된 예에서 고려해보겠습니다. 여기에서는 사용자 개체를 UserDefaults에 저장했지만 이제 CoreData에도 저장하려고 합니다. 우리의 go-to 접근법은 다음과 같습니다:

```js
import Foundation

struct Handler {

    private let fetchHandler: FetchHandler
    private let parseHandler: ParseHandler
    private let saveHandler: SaveHandler

    init(fetchHandler: FetchHandler, parseHandler: ParseHandler, saveHandler: SaveHandler) {
        self.fetchHandler = fetchHandler
        self.parseHandler = parseHandler
        self.saveHandler = saveHandler
    }

    func handle() {

        // 사용자 데이터 가져오기
        let personAsData = fetchHandler.fetch()

        // 데이터를 필요한 개체로 구문 분석
        let person = parseHandler.parse(personAsData)

        // 사용자 개체를 UserDefaults에 로컬로 저장
        saveHandler.saveToUserDefaults(person)

        // 사용자 개체를 CoreData에 로컬로 저장
        saveHandler.saveToCoreData(person)
    }

}

struct FetchHandler {

    func fetch() -> Data {
        """
        {
            "name": "Shubham Bakshi",
            "age": 28,
            "canTakeANapAnywhere": true

        }
        """.data(using: .utf8)!
    }

}

struct ParseHandler {

    func parse(_ data: Data) -> Person {
        try! JSONDecoder().decode(Person.self, from: data)
    }

}

struct SaveHandler {

    func saveToUserDefaults(_ person: Person) {
        UserDefaults.standard.set(person, forKey: "person")
    }

    func saveToCoreData(_ person: Person) {
        // CoreData에 데이터를 저장하는 매우 복잡한 코드
    }

}

struct Person: Codable {
    let name: String
    let age: Int
    let canTakeANapAnywhere: Bool
}
```

이는 우리가 철저히 테스트하고 나중에 배포한 save-to-User-Defaults의 원래 구현을 변경하고 있어 OCP를 깨뜨립니다. OCP를 수용하기 위해 클래스를 통해 상속을 사용하거나 우리 스위프트 개발자가 사용하기를 사랑하는 단순히 프로토콜 준수를 사용할 수 있습니다!

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
import Foundation

struct Handler {

    private let fetchHandler: FetchHandler
    private let parseHandler: ParseHandler
    private let saveHandler: PersistenceHandler

    init(fetchHandler: FetchHandler, parseHandler: ParseHandler, saveHandler: PersistenceHandler) {
        self.fetchHandler = fetchHandler
        self.parseHandler = parseHandler
        self.saveHandler = saveHandler
    }

    func handle() {

        // 사람 데이터 가져오기
        let personAsData = fetchHandler.fetch()

        // 데이터 구문 분석하여 필요한 객체로 변환
        let person = parseHandler.parse(personAsData)

        // 사람 객체를 로컬에 저장
        saveHandler.save(person)
    }

}

struct FetchHandler {

    func fetch() -> Data {
        """
        {
            "name": "Shubham Bakshi",
            "age": 28,
            "canTakeANapAnywhere": true

        }
        """.data(using: .utf8)!
    }

}

struct ParseHandler {

    func parse(_ data: Data) -> Person {
        try! JSONDecoder().decode(Person.self, from: data)
    }
}

protocol PersistenceType {
    func save(_ person: Person)
}

struct UserDefaultsPersistenceHandler: PersistenceType {

    func save(_ person: Person) {
        UserDefaults.standard.set(person, forKey: "person")
    }
}

struct CoreDataPersistenceHandler: PersistenceType {

    func save(_ person: Person) {
        // Core data에 데이터를 저장하기 위한 매우 포괄적인 코드
    }
}

struct PersistenceHandler {

    let userDefaultsPersistenceHandler: UserDefaultsPersistenceHandler
    let coreDataPersistenceHandler: CoreDataPersistenceHandler

    init(userDefaultsPersistenceHandler: UserDefaultsPersistenceHandler, coreDataPersistenceHandler: CoreDataPersistenceHandler) {
        self.userDefaultsPersistenceHandler = userDefaultsPersistenceHandler
        self.coreDataPersistenceHandler = coreDataPersistenceHandler
    }

    func save(_ person: Person) {
        userDefaultsPersistenceHandler.save(person)
        coreDataPersistenceHandler.save(person)
    }
}

struct Person: Codable {
    let name: String
    let age: Int
    let canTakeANapAnywhere: Bool
}
```

만약 프로토콜 준수를 사용하고 싶지 않다면, 클래스를 통해 상속을 사용하여 동일한 결과를 달성할 수 있습니다:

```kotlin
import Foundation

struct Handler {

    private let fetchHandler: FetchHandler
    private let parseHandler: ParseHandler
    private let saveHandler: ModifiedSaveHandler

    init(fetchHandler: FetchHandler, parseHandler: ParseHandler, saveHandler: ModifiedSaveHandler) {
        self.fetchHandler = fetchHandler
        self.parseHandler = parseHandler
        self.saveHandler = saveHandler
    }

    func handle() {

        // 사람 데이터 가져오기
        let personAsData = fetchHandler.fetch()

        // 데이터 구문 분석하여 필요한 객체로 변환
        let person = parseHandler.parse(personAsData)

        // 사람 객체를 로컬에 저장
        saveHandler.save(person)
    }

}

struct FetchHandler {

    func fetch() -> Data {
        """
        {
            "name": "Shubham Bakshi",
            "age": 28,
            "canTakeANapAnywhere": true

        }
        """.data(using: .utf8)!
    }

}

struct ParseHandler {

    func parse(_ data: Data) -> Person {
        try! JSONDecoder().decode(Person.self, from: data)
    }

}

class SaveHandler {

    func save(_ person: Person) {
        UserDefaults.standard.set(person, forKey: "person")
    }

}

final class ModifiedSaveHandler: SaveHandler {

    override func save(_ person: Person) {
        super.save(person)

        // Core data에 데이터를 저장하기 위한 매우 포괄적인 코드
    }

}

struct Person: Codable {
    let name: String
    let age: Int
    let canTakeANapAnywhere: Bool
}
```

OCP의 이점은 다음과 같을 수 있습니다:

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

- 코드를 유지 및 검토하는 것이 쉽습니다.
- 추상화 및 다형성의 사용을 촉진합니다.
- 이미 테스트되고 배포 준비가 된 기존 코드를 수정할 수 없도록 하므로 잠재적 버그를 줄이는 데 도움이 됩니다.

# (L) 리스코프 치환 원칙

솔직히 말해서, 리스코프 치환 원칙(LSK)은 처음에는 아마도 가장 혼란스러운 원칙 중 하나일지도 모릅니다!

그것이 실제로 말하는 것은 슈퍼클래스의 객체는 해당 서브클래스의 객체로 교체할 수 있어야 하는데 프로그램의 정확성에 영향을 미치지 않아야한다는 것입니다. LSK는 단순히 추가 단계를 통한 상속이라고 볼 수 있습니다.

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

상기한 예시를 사용하여 LSK(Liskov Substitution Principle)를 설명하는 것이 어렵고 혼란스러울 수 있으므로 다른 간단한 예시를 사용하겠습니다. 또한, 이미 해당 내용이 인터넷에 널리 퍼져 있고 때로는 혼란스러울 수 있기 때문에 사각형과 정사각형 예시를 사용하지 않겠습니다.

```js
import Foundation

class Bird {
    func fly() {
        print("I can fly")
    }
}

final class Duck: Bird {
    /// ✅ Duck은 날 수 있는 새이므로 LSP를 따릅니다.
}

final class Ostrich: Bird {
    /// ❌ 사실 타조는 새이지만 날지 못하므로 LSP를 위반합니다.
}
```

그렇다면 이 문제를 어떻게 해결할까요? 우리는 날 수 있는 새를 위한 별도의 클래스를 만듭니다:

```js
import Foundation

class Bird {

}

class FlyingBird: Bird {
    func fly() {
        print("I can fly")
    }
}

final class Duck: FlyingBird {
    /// ✅ Duck은 날 수 있는 새이므로 LSP를 따릅니다.
}

final class Ostrich: Bird {
    /// ✅ 타조는 이제 날 필요가 없으므로 LSP를 따릅니다.
    /// (그리고 이제는 다른 일에 집중할 수 있게 되었죠,
    /// 예를 들어 사람 뒤를 더 빠르게 쫓아가서 겁을 줄 수 있는 방법에 대해)
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

LSP의 이점은 다음과 같습니다:

- 코드는 유연하고 모듈식으로 유지됩니다.
- 코드 중복을 줄입니다.

# (I) 인터페이스 분리 원칙

인터페이스 분리 원칙(ISP)은 클라이언트가 사용하지 않는 인터페이스를 강제로 구현하거나 클라이언트가 사용하지 않는 메서드에 의존하도록 강요해서는 안된다는 것을 명시합니다. 간단히 말하면, 하나의 뚱뚱한 인터페이스(또는 프로토콜) 대신에 많은 작은 인터페이스를 사용하여 제공하는 메서드에 기반을 둔다는 것입니다.

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

ISP(Interface Segregation Principle)은 SRP(Single Responsibility Principle)와 비슷하지만, SRP는 일반적으로 구조체/클래스에 적용되고, ISP는 프로토콜에 적용됩니다.

위의 샘플에서 Bird 예시를 사용하여 ISP를 설명할 수 있지만, 프로토콜(또는 다른 언어에서의 인터페이스)을 사용하여 우리의 entity가 하나 이상의 프로토콜을 준수할 수 있도록 할 것입니다:

```js
import Foundation

protocol Bird {

    func fly()

    func swim()

    func mimic()
}

final class Duck: Bird {
    /// ❌ Duck는 날 수 있고 헤엄칠 수 있지만 모방할 수는 없으므로 ISP를 위반함
}

final class Parrot: Bird {
    /// ❌ Parrot은 날 수 있고 모방할 수 있지만 헤엄쳐갈 수는 없으므로 ISP를 위반함
}

final class Penguin: Bird {
    /// ❌ Penguin은 헤엄칠 수 있지만 날 수 없으며 모방할 수도 없으므로 ISP를 위반함
}
```

이 문제를 해결하기 위해 Bird 프로토콜을 하위 프로토콜로 분리하여 각 메서드를 수용할 수 있도록 합니다:

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
import Foundation

protocol FlyingBird {
    func fly()
}

protocol SwimmingBird {
    func swim()
}

protocol MimickingBird {
    func mimic()
}

final class Duck: FlyingBird, SwimmingBird {
    /// ✅ Follows ISP since duck can fly and swim without needing to mimic
}

final class Parrot: FlyingBird, MimickingBird {
    /// ✅ Follows ISP since parrot can fly and mimic without needing to swim
}

final class Penguin: SwimmingBird {
    /// ✅ Follows ISP since penguin can swim without needing to fly or mimic
}
```

ISP의 장점은 다음과 같습니다:

- 더 집중된 및 일관된 인터페이스
- 불필요한 종속성을 피할 수 있음
- 단일 부풀린 인터페이스를 피할 수 있음
- 결합을 촉진함

# (D) 의존성 역전 원칙 (Dependency Inversion principle)

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

의존 역전 원칙(Dependency Inversion Principle, DIP)은 고수준 모듈이 저수준 모듈에 의존하지 않아야 하며, 둘 다 추상화에 의존해야 한다고 설명합니다. 간단하게 말하면: 구성원은 구상체(concretions)가 아닌 추상화(abstractors)에 의존해야 한다는 것이죠.

이렇게 함으로써 코드가 가능한한 작은 표면적 영역에 의존하도록 보장됩니다. 실제로 코드 자체에 의존하지 않고, 단지 그 코드가 어떻게 행동해야 하는지를 정의하는 계약에만 의존하게 됩니다. 이렇게 함으로써 코드의 한 부분에 오류가 발생해도 다른 의존하는 부분에서 코드가 연쇄적으로 망가지는 결과를 막을 수 있습니다.

DIP는 OCP와 꽤 비슷하게 들리지만 예시로서도 OCP의 예시를 사용할 수 있을 정도이기도 합니다. 그러나 여기서는 다른 예시를 사용하겠습니다:

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

위의 예제는 DIP(Dependency Inversion Principle)를 위반합니다. Payment 클래스는 CreditCardPayment과 강하게 결합되어 있으며, 이는 고수준 모듈인 Payment 클래스가 저수준 모듈인 CreditCardPayment에 의존하고 있음을 의미합니다. DIP에 따르면 추상화/프로토콜에 의존해야 합니다.

```js
import Foundation

protocol PaymentMethod {
    func pay(_ amount: Double)
}

struct DebitCardPayment: PaymentMethod {
    func pay(_ amount: Double) { }
}

struct CreditCardPayment: PaymentMethod {
    func pay(_ amount: Double) { }
}

struct ApplePayPayment: PaymentMethod {
    func pay(_ amount: Double) { }
}

struct Payment {

    private let paymentMethod: PaymentMethod

    init(method: PaymentMethod) {
        paymentMethod = method
    }

    func makePayment(of amount: Double) {
        paymentMethod.pay(amount)
    }
}

let creditCardPaymentMethod = CreditCardPayment()

let payment = Payment(method: creditCardPaymentMethod)
payment.makePayment(of: 500)
```

DIP의 장점:

- 결합 감소를 장려함
- 코드를 보다 유연하고 재사용 가능하며, 민첩하게 만듦
- 구성 요소 간의 계약을 정의하기 위해 인터페이스 또는 프로토콜 사용을 장려함

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

모두를 종합하자면, 앱에 SOLID 원칙을 통합하는 것에는 많은 이점이 있습니다:

- 코드를 이해, 유지 및 확장하기 쉽게 만들어줍니다.
- 엔티티의 책임이 적을수록, 엔티티를 변경할 때 코드가 깨지는 가능성이 줄어듭니다.
- 더 확장 가능하고 테스트하기 쉬운 코드를 작성할 수 있습니다.
- 코드를 리팩터링하는데 더 적은 복잡성으로 도와줍니다.
- 딱 맞는 수준의 결합을 사용할 수 있도록 도와줍니다 - 함께 있어야 할 것들은 함께 두고, 분리되어야 하는 것들은 분리합니다.

여기까지, 여러분! 즐거운 코딩하세요!

LinkedIn에서 저와 연결해보세요 👱🏻 또는 다른 채널로 연락할 수 있습니다 📬
