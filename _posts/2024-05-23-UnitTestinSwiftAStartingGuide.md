---
title: "Swift에서의 유니트 테스트 시작 가이드"
description: ""
coverImage: "/assets/img/2024-05-23-UnitTestinSwiftAStartingGuide_0.png"
date: 2024-05-23 13:12
ogImage:
  url: /assets/img/2024-05-23-UnitTestinSwiftAStartingGuide_0.png
tag: Tech
originalTitle: "Unit Test in Swift: A Starting Guide"
link: "https://medium.com/@blorenzop/swift-unit-tets-530a8d271f4d"
---

# iOS 프로젝트에 단위 테스트 추가하는 방법과 테스트에 관한 주요 개념 배우기

![image](/assets/img/2024-05-23-UnitTestinSwiftAStartingGuide_0.png)

버그를 고치는 걸 좋아하시나요?

확실히 싫어하시죠.

<div class="content-ad"></div>

안녕하세요! 적절한 유닛 테스트 구조를 갖는 것은 미래에 머리 아픈 문제를 피하는 데 도움이 됩니다.

단위 테스트는 당신이 만든 모든 변경 사항 후에 코드가 여전히 예상대로 작동하는지 확인하고, 새 코드를 프로덕션 환경에 배포할 때 더 확신을 갖게 해주며, 전체 코드 품질을 향상시키고, 코드베이스 복잡성을 줄이는 등의 암묵적인 이점이 있습니다.

이제 테스트의 주요 개념과 iOS 프로젝트에 어떻게 추가하는지 알아봅시다.

<div class="content-ad"></div>

## 단위 테스트란 무엇인가요?

우리가 함께 이야기할 때, 단위 테스트는 우리 애플리케이션 코드의 작은, 독립적이며 명확한 블록(유닛이라고도 함)이 예상대로 작동하는 지를 확인하는 코드 조각들이라고 말할 수 있어요.

### 테스트 구조

코딩 부분으로 바로 들어가기 전에 이해해야 할 여러 구성 요소가 있어요.

<div class="content-ad"></div>

![UnitTestinSwiftAStartingGuide_1.png](/assets/img/2024-05-23-UnitTestinSwiftAStartingGuide_1.png)

- 테스트 메소드는 코드 일부를 유효성 검사하는 메소드입니다. 여기서 우리는 테스트를 생성하고 개발합니다. 이러한 메소드는 테스트 결과를 생성하며, 통과하거나 실패할 수 있습니다.
- 테스트 클래스는 테스트 메소드의 집합입니다. 일반적으로 특정 로직을 테스트하는 데 사용되며, 예를 들어 인증(Authentication)을 그룹화합니다.
- 테스트 번들에는 테스트 클래스의 집합이 포함되며, 두 가지 테스트 유형 중 하나인 Unit 또는 UI를 나타냅니다.
- 테스트 계획은 테스트 번들의 집합이며, 여기에는 Unit 및 UI 테스트를 모두 포함할 수 있습니다. 테스트 계획에서는 테스트 실행 시 고려해야 할 구성 목록을 설정합니다.

# 테스트 번들 생성으로 시작

`파일` -> `새로 만들기` -> `타겟`으로 이동하여 Unit Testing Bundle을 검색합니다. 대상에 적절한 이름을 지정하고 일반적으로 앱 이름 뒤에 Tests로 끝납니다.

<div class="content-ad"></div>

테스트 번들을 만들면 기본 구성으로 테스트 계획이 자동으로 생성됩니다.

![Unit Testing in Swift: A Starting Guide](/assets/img/2024-05-23-UnitTestinSwiftAStartingGuide_2.png)

# 단위 테스트 설정하기

모든 프로그래밍 언어에는 단위 테스트를 실행하기 위한 프레임워크나 라이브러리가 있습니다. iOS 개발 환경에서는 XCTest 프레임워크를 사용합니다.

<div class="content-ad"></div>

이제 테스트 번들이 준비되었으니 새로운 테스트 클래스를 추가할 수 있습니다. 네비게이션 패널이나 메뉴(File -> File -> Unit Test Case Class)를 통해 새로 만들어주세요.

OrderManager 클래스 뒤에 있는 로직을 테스트하기를 원하므로, 테스트 클래스의 좋은 이름은 OrderTests가 될 것 같아요.

```js
// 새 주문 추가
func add(_ order: Order) throws { ... }
// 마지막 주문을 반복하며 주문 목록에 추가
func repeatLastOrder() { ... }
```

테스트 클래스 안에 각 함수마다 테스트 메소드를 만들어봅시다.

<div class="content-ad"></div>

앱의 코드에 접근하지 않고 파일 단위로 공유하지 않고 테스트 대상에서 앱의 코드에 액세스하려면 @testable 지시문을 추가하고 앱을 가져와야 합니다.

```swift
import XCTest
@testable import Coffee_Shop_App

final class OrderTests: XCTestCase {

    override func setUpWithError() throws {}
    override func tearDownWithError() throws {}

    func testAddNewOrder() {}
    func testRepeatLastOrder() {}
}
```

<img src="/assets/img/2024-05-23-UnitTestinSwiftAStartingGuide_3.png" />

각 테스트가 시작하기 전에 XCTest는 setUpWithError() 함수를 실행합니다. 따라서 여기서 테스트의 초기 상태를 설정해야 합니다. 특정 클래스의 인스턴스 생성, 의존성 주입, 변수 값 구성 등이 될 수 있습니다.

<div class="content-ad"></div>

테스트가 끝난 후에는 tearDownWithError()가 호출됩니다. 여기서 우리가 고려하는 모든 것을 정리하기에 좋은 장소입니다.

## 이제 Unit Tests를 작성하기 시작할 준비가 모두 완료되었습니다

먼저, OrdersManager에 액세스해야 합니다.

- 클래스에 속성을 추가합니다.
- setUpWithError() 메서드에서 인스턴스를 만듭니다.
- tearDownWithError() 메서드에서 상태를 정리합니다.

<div class="content-ad"></div>

```kotlin
@testable import Coffee_Shop_App

final class OrderTests: XCTestCase {
  private var ordersManager: OrdersManager!

  override func setUpWithError() throws {
    ordersManager = OrdersManager.shared
  }

  override func tearDownWithError() throws {
    ordersManager.removeAllOrders()
    ordersManager = nil
  }

  func testAddNewOrder() {}
  func testRepeatLastOrder() {}
}
```

만약 단위 테스트를 시작한다면, 'Arrange / Act / Assert' 패턴을 사용하는 것이 좋은 초보자를 위한 초기 단계입니다. 이 패턴을 사용하여 테스트를 다음과 같이 분해하시면 됩니다:

- Arrange → 테스트가 사용할 모든 필요한 객체나 데이터를 생성합니다.
- Act → 테스트하려는 메서드나 함수를 실행합니다.
- Assert → 얻은 결과를 기대한 결과와 비교합니다.

```kotlin
final class OrderTests: XCTestCase {
  private var ordersManager: OrdersManager!

  override func setUpWithError() throws {
    ordersManager = OrdersManager.shared
  }

  override func tearDownWithError() throws {
    ordersManager.removeAllOrders()
    ordersManager = nil
  }

  func testAddNewOrder() {
    // 1 - Arrange
    let orderItems: [OrderItem] = [
      .init(item: AnyMenuItem(Coffee.flatwhite), size: .regular, quantity: 1),
      .init(item: AnyMenuItem(Food.chickenSandwich), size: .regular, quantity: 1),
    ]
    let order = Order(items: orderItems)

    // 2 - Act
    try? ordersManager.add(order)

    // 3 - Assert
    XCTAssertEqual(ordersManager.orders.count, 1)
  }

  func testRepeatLastOrder() {
    // 1 - Arrange
    let orderItems: [OrderItem] = [
      .init(item: AnyMenuItem(Coffee.flatwhite), size: .regular, quantity: 1),
      .init(item: AnyMenuItem(Food.chickenSandwich), size: .regular, quantity: 1),
    ]

    // 2 - Act
    let order = Order(items: orderItems)
    try? ordersManager.add(order))
    ordersManager.repeatLastOrder()

    // 3 - Assert
    XCTAssertEqual(ordersManager.orders.count, 2)
  }
}
```

<div class="content-ad"></div>

# Xcode에서 단위 테스트 직접 실행하기

구성 세트에 포함된 모든 테스트, 테스트 번들에 포함된 모든 테스트, 테스트 클래스에 포함된 모든 테스트, 일부 특정 테스트 메서드의 그룹 또는 한 번에 하나의 테스트 메서드를 실행할 수 있습니다.

테스트를 실행한 후에는 결과가 포함된 테스트 보고서를 받을 수 있습니다. 또한 Test 네비게이터 및 테스트 클래스 내에서 테스트 결과를 직접 확인할 수 있습니다.

![image](/assets/img/2024-05-23-UnitTestinSwiftAStartingGuide_4.png)

<div class="content-ad"></div>

# 테스트 커버리지

테스트를 실행한 후에 테스트 커버리지 정보를 활성화할 수 있습니다. 시험한 코드 안에서 테스트 된 코드 부분을 볼 수 있습니다.

![unit test](/assets/img/2024-05-23-UnitTestinSwiftAStartingGuide_5.png)

녹색으로 표시된 부분은 최근 테스트 실행에서 도달한 코드 부분이고, 빨간색으로 표시된 부분은 도달하지 못한 코드 부분입니다. 데모에서 주문 최소 금액을 확인하는 부분이 누락되었습니다.

<div class="content-ad"></div>

만약 이 시나리오를 발견하면, 새로운 유닛 테스트를 추가해서 이를 확인하고 테스트를 다시 실행할 수 있어요.

# 다른 XCTAssert 사용

테스트 결과를 확인할 때 사용할 수 있는 다양한 함수들이 있어요. 몇 가지 예시를 보여드릴게요.

```js
// 1 - throw 함수가 예외를 발생시키지 않는지 확인
XCTAssertNoThrow(try ordersManager.add(order))

// 2 - throw 함수가 에러를 발생시키는지 확인
XCTAssertThrowsError(try ordersManager.add(order)) { error in
  if let appError = error as? AppError, let errorType = appError.type as? OrderError {
    XCTAssertEqual(errorType.code, 2)
  } else {
    XCTFail("잘못된 에러 유형이 트리거됐어요")
  }
}

// 3 - 옵셔널 값이 nil이 아닌지 확인
let lastCoffeeDescription = ordersManager.getLastCoffee()
XCTAssertNotNil(lastCoffeeDescription)
```

<div class="content-ad"></div>


```js
XCTAssertEqual(ordersManager.orders.count, 1, "주문이 올바르게 추가되지 않았습니다")
```

# 명심해야 할 점

- 버그를 수정하는 것보다 테스트를 작성하는 데 시간을 소비하는 것이 항상 좋습니다.
- 단위 테스트를 추가하는 것을 추가 작업으로 보지 마세요. 개발 과정의 일부로 테스트를 추가하려고 노력하세요.
- FIRST 원칙을 기억하세요. 모든 단위 테스트는 빠르고 독립적이며 반복 가능하며 자가 검증 가능하며 적시성을 가져야 합니다.
- 테스트가 실패하면 조심해야 합니다. 테스트를 적응시키려고 할 수 있습니다. 그러나 이는 일부 경우에는 올바를 수 있지만 (테스트가 잘못 작성된 경우) 대게 테스트하려는 논리가 잘못된 것입니다.
- 100%의 테스트 커버리지를 추구하지 마세요. 100%의 커버리지가 있더라도 10가지 가능한 시나리오 중 1개에 대한 테스트만 있는 경우가 있습니다. 대신 품질 높은 테스트를 작성하는 데 중점을 두세요.
- 테스트를 사용하여 전체적인 코드 품질을 높일 수 있는 작은 코드 리팩터링을 찾을 수 있는 기회로 활용하세요.



<div class="content-ad"></div>

질문이 있으시면 언제든지 메시지 보내주세요! 🙂

- 🤓 iOS 개발 팁 및 통찰력이 담긴 규칙적인 콘텐츠를 보려면 X에 들러주세요.
- 🚀 제 GitHub에서 제 예제 프로젝트를 모두 공유하고 있습니다.
