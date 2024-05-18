---
title: "Swift Actors  깊이 있는 이해"
description: ""
coverImage: "/assets/img/2024-05-18-SwiftActorsindepth_0.png"
date: 2024-05-18 17:26
ogImage: 
  url: /assets/img/2024-05-18-SwiftActorsindepth_0.png
tag: Tech
originalTitle: "Swift Actors — in depth"
link: "https://medium.com/@valentinjahanmanesh/swift-actors-in-depth-19c8b3dbd85a"
---


<img src="/assets/img/2024-05-18-SwiftActorsindepth_0.png" />

# 배우세요, 외우지 마세요.

저는 개념의 내부 작동 원리를 이해하는 것을 좋아하는 사람입니다. 내재하는 메커니즘을 파악하지 못하면 개념에 대해 모든 것이 불분명하게 보이고 진정한 이해가 아닌 단순 외우기처럼 느껴집니다. 그래서 저는 여러 주요한 스위프트 개념인 액터(actors), 비동기/대기(async/await), 구조화된 동시성(structured concurrency), 그리고 AsyncSequence에 대해 탐구해왔습니다. 이러한 개념을 이해하기 쉽게 만들기 위해 각각의 개념을 실제적이고 현실적인 예를 사용하여 설명할 계획입니다. 먼저 액터에 대한 토론을 시작해보겠습니다.

# 액터(actor)란 무엇인가요?

<div class="content-ad"></div>

Swift에서 Actor는 5.5 버전에서 도입된 참조 유형으로, 고급 동시성 모델의 일환으로 소개되었습니다. 주요 역할은 데이터 경쟁을 방지하고 동시 프로그래밍 환경에서 공유된 가변 상태에 안전하게 액세스하는 것입니다. 이를 더 잘 이해하기 위해 간단한 예시를 들어보겠습니다: 사무실의 프린터.

![프린터](https://miro.medium.com/v2/resize:fit:1400/1*IEsYeP_QiG4ZmObPQ-vURA.gif)

사무실에 모든 직원들이 사용할 수 있는 공유 프린터가 있는 상황을 상상해보세요. 어느 날 문서를 인쇄해야 해서 파일을 프린터에 보내고 수령하러 가는데, 프린터에 도착해보니 의외의 일이 벌어졌습니다: 출력된 페이지가 여러분의 파일과 일치하지 않습니다. 혼란스러운 상황에서 올바른 파일을 보냈다는 것을 재확인하더니, 다른 사람이 여러분의 인쇄 작업을 취소하고 자신의 작업을 시작한 것 같습니다. 이런 착각에 직면했을 때, 어떤 조치를 취할 것인지 고민해보실 거에요?

![문제 해결](https://miro.medium.com/v2/resize:fit:960/1*mI-teGZmtwFc4VLeRWFh-Q.gif)

<div class="content-ad"></div>

문서를 출력한 후 확인 없이 그것을 바로 당신의 매니저에게 전달하는 상황을 상상해보십시오. 그것이 의도한 대로인지 확인하지 않은 채에, 문제가 발생할 수 있습니다. 공유 리소스를 다룰 때 프로그래밍에서도 비슷한 문제가 발생할 수 있습니다. Swift의 맥락에서, 클래스에 대해서 특히 중요한데, 이는 참조 유형입니다. 이 특성은 클래스의 인스턴스에 대한 모든 수정사항이 여러분의 애플리케이션에서 해당 클래스를 사용하는 모든 인스턴스에 반영될 것임을 의미합니다. 이는 공유 오피스 프린터에서 한 사람의 행동이 모두의 인쇄 작업에 영향을 미치는 것과 유사합니다. 이제, 특정 코드 예제를 살펴보겠습니다.

```js
class Account {
 var balance: Int = 20//현재 사용자의 잔액은 20
 ...
 func withdraw(amount: Int) {
  guard balance >= amount else {return}
  self.balance = balance - amount
 }
}

var myAccount = Account()
myAccount.withdraw(20$)
```

우리 코드는 계좌에서 돈을 인출하는 간단한 작업을 수행합니다. 이 코드에서는 인출을 진행하기 전에 계정 잔액이 충분한지 확인합니다. 이 시나리오는 사무실에서 프린터를 사용하는 것과 유사합니다. 당신이 사무실에 혼자 있을 때, 프린터(계정 객체와 유사)를 사용하는 것은 문제가 없습니다. 그러나 다수의 사람(또는 우리의 프로그래밍 비유인 경우, 다수의 스레드)이 동시에 프린터를 사용하려고 하면 문제가 발생할 수 있습니다.

이제, 멀티스레딩 환경에서의 Account 예제로 돌아가봅시다. 여러 CPU 코어가 있는 현대 모바일 장치와 같이 멀티스레딩 환경에서, 두 개의 스레드가 동시에 동일한 Account 객체에서 withdraw(20$) 함수를 실행하려고 시도한다고 상상해보십시오. 운영 체제는 이러한 스레드를 조절하고, CPU 코어를 할당하고, 그들의 실행을 관리하지만 우리는 정확한 작업 순서를 예측할 수 없습니다.

<div class="content-ad"></div>

요점은 다음과 같습니다: 첫 번째 스레드가 잔액을 확인하고 충분하다고 판단하면서(잔액 `= 금액) 출금을 하기 전에 문맥 전환으로 인해 두 번째 스레드가 실행을 시작합니다. 이 두 번째 스레드도 첫 번째 스레드가 아직 출금을 완료하지 않았기 때문에 잔액이 충분하다고 판단하여 돈을 인출하고 잔액을 0으로 만듭니다. 그런 다음, 첫 번째 스레드가 다시 실행되면, 두 번째 스레드에 의해 잔액이 이미 소진된 상태에서 돈을 인출하려고 시도합니다.

이 상황은 두 명이 같은 프린터를 사용하려고 하는 것과 유사합니다. 이들이 조율하지 않으면 서로 방해하는 인쇄 명령을 보낼 수 있어 혼합이나 누락된 인쇄물이 발생할 수 있습니다. 계좌 예시에서, 이러한 조율의 부재는 계좌가 동일한 금액에 대해 두 번 차감되는 초과 인출로 이어지며, 프로그래밍에서 동시 액세스의 어려움을 보여줍니다.

실제로 이전 예시에서는 잔액이 부족하여 두 출금 작업 중 하나가 거부되어야 했지만, 동시 액세스로 인해 두 작업이 모두 성공했습니다. 이 상황은 '경쟁 조건(Race Condition)'을 보여줍니다. 경쟁 조건은 여러 스레드가 동시에 공유 리소스인 여기서는 계좌 잔액을 액세스하고 수정하며, 이러한 작업의 최종 결과가 각 스레드의 실행 타이밍에 달렸을 때 발생합니다.

경쟁 조건에서 결과는 프로그램의 제어 밖에 있는 특정 이벤트의 순서와 타이밍에 따라 예측할 수 없습니다. 계좌 예시에서의 문제는 두 스레드 모두 잔액을 확인하고 금액을 차감하기 전에 충분하다고 판단했기 때문에 발생했습니다. 이로 인해 두 스레드가 모두 인출을 진행하고, 잘못된 계좌 잔액이 발생했습니다.

<div class="content-ad"></div>

이러한 레이스 조건은 프로그래밍에서 심각한 문제입니다, 특히 데이터 일관성과 정확성이 중요한 시스템에서는 더욱 중요합니다. 이는 멀티 스레드 환경에서 공유 리소스를 신중하게 관리하여 작업이 안전하고 예측 가능한 방식으로 수행되도록하는 필요성을 강조합니다.

# Actors

Swift의 Actors는 이전에 논의한 동시성 문제에 대한 우아한 해결책을 제공합니다. Actors가 도입되기 전에는 DispatchQueue, Operations 및 Locks를 사용하여 동시 액세스를 관리하는 흔한 방법이 있었습니다. 이러한 방법은 효과적이었지만, 상당량의 수동 코드 및 관리가 필요했습니다.

이제 Swift에서 Actor 유형을 도입함으로써 동시성 관리에 포함된 복잡성의 많은 부분이 숨겨졌습니다. 프린터 예에서 생각해보세요: 어떻게 해서 다른 사람의 인쇄 작업을 덮어 쓰지 않도록 보장할 수 있을까요? 이상적인 해결책은 인쇄 작업이 줄지어 있는 대기열이 있는 것입니다. 새 작업이 도착할 때마다 대기열에 가입하고 차례를 기다리며, 프린터는 각 작업을 순차적으로 처리합니다.

<div class="content-ad"></div>

이것이 정확히 배우는 방법입니다. Actor들은 내부 메일함을 가지고 있으며 이것은 큐처럼 작동합니다. Actor에 보내진 요청은 이 메일함에 저장되어 순차적으로 처리됩니다. 큐잉 메커니즘은 작업이 순서대로 실행되도록 보장해주어 경합 조건을 방지합니다. 게다가, 내부 큐와 메일함은 Actor 자체에서 관리되므로, 모든 복잡성이 개발자로부터 숨겨지게 됩니다. 이로써 Swift에서 동시성 작업을 처리하는 보다 간소화되고 오류 방지된 접근 방식이 가능해집니다.

대박, 맞죠?

![이미지](https://miro.medium.com/v2/resize:fit:400/1*CisZ-Zm-zu0MiLV45EpV9Q.gif)

하지만 이것이 실제로 어떻게 작동하는지 궁금하시죠? Actor들은 '데이터 격리'를 보장합니다.

<div class="content-ad"></div>

데이터 격리를 이해하기 위해 사무실의 프린터 비유를 생각해보세요. 이 사무실 프린터를 별도의 방에 넣고 와이파이에서 분리한다고 상상해봅시다. 이제 누군가 인쇄하려면 물리적으로 이 방에 가서 차례를 기다려야 합니다. 이 설정은 프린터의 겹침이나 병행 사용을 효과적으로 방지하여 한 번에 한 사람만 사용할 수 있도록 합니다.

Swift의 경우, 액터(actors)도 비슷하게 작동합니다. 액터 내부에 데이터를 캡슐화하면 프로그램의 다른 부분에서 직접 액세스할 수 없게 만듭니다. 데이터와 상호 작용하려면 액터를 통과해야 하는 코드가 필요하며, 효과적으로 '정렬'하여 차례를 기다립니다. 이는 동시성 환경에서도 액터가 데이터에 대한 액세스를 직렬화하여 한 번에 코드 한 개만 상호 작용할 수 있도록 합니다.

이제 이 개념이 어떻게 실제로 적용되는지 알아보기 위해 코드를 살펴봅시다...

개정된 Account 예제에서 클래스에서 액터로 전환은 놀랍도록 간단합니다. 정의에서 클래스를 액터로 변경하는 것만으로 Account 오브젝트가 스레드 안전해집니다. 이 변경은 구문적으로는 미미하지만, 동시성 환경에서 객체가 액세스되고 조작되는 방식에 중요한 영향을 미칩니다.

<div class="content-ad"></div>

```swift
actor Account {
 var balance: Int = 20// 현재 사용자의 잔액은 20
 // ...
 func withdraw(amount: Int) {
  guard balance >= amount else {return}
  self.balance = balance - amount
 }
}
```

정말 쉽게 actor로 전환할 수 있습니다. 그러나 이 변경에는 일부 어려움이 딸립니다. 클래스를 actor로 전환한 후 코드를 컴파일하려고 하면 오류가 발생할 수 있습니다. 이는 actor의 특성과 데이터 고립 속성 때문에 발생하는 문제이며, 프린터를 별도의 방에 두고 Wi-Fi와 연결을 끊어버린 것에 비유할 수 있습니다.

우리의 클래스를 actor로 변환하면 이제 해당 클래스의 속성과 메소드에 외부에서 직접 액세스할 수 없게 됩니다. actor는 스레드 안전을 보장하기 위해 엄격한 액세스 제어를 시행합니다. 따라서 이제는 이전에 Account 객체에 액세스했던 코드의 모든 지점을 업데이트해야 합니다. 일반적으로 Actor와 상호 작용하기 위해 async 및 await와 같은 비동기 패턴을 사용하여 메소드와 속성에 안전하게 직렬화된 액세스가 이루어지도록 해야 합니다.

```swift
var myAccount = Account()
myAccount.withdraw(20$)  // 이 줄은 더 이상 유효하지 않습니다
await myAccount.withdraw(20$) // 좋아요
```

<div class="content-ad"></div>

# 배우와 배역 간 참조

Swift에서 배우가 데이터 격리를 보장한다고 말할 때, 이는 배우 내의 모든 가변 속성과 함수가 외부로부터 직접 액세스되는 것과 격리된다는 것을 의미합니다. 이 격리는 배우의 핵심 기능이며, 병렬 프로그래밍에서 스레드 안전성을 보장하기 위한 중요한 요소입니다. 그렇다면 이 격리는 이러한 속성과 함수에 액세스하고 수정하는 데 어떤 영향을 미칠까요?

기본적으로 배우의 속성을 읽거나 값을 변경하거나 함수를 호출하려면 일반 클래스나 구조체처럼 직접 수행할 수 없습니다. 대신, 말그대로 '순서를 기다려야' 합니다. 이는 배우의 메일함에 요청을 보내는 것으로 이루어집니다. 그러면 요청이 대기열에 들어가 순서대로 처리됩니다. 요청이 처리될 차례가 되면 해당 요청을 처리할 수 있을 때만 배우의 속성을 읽거나 수정하거나 함수를 호출할 수 있습니다.

이 과정을 배우 간 참조라고 합니다. 배우 외부에서 배우 내부의 내용을 참조하거나 액세스할 때 배우 간 참조를 하게 됩니다. 실제로는 비동기 패턴을 사용하여 배우와 상호 작용하는 것을 의미합니다. 이러한 구조를 사용하면 코드가 효과적으로 다음과 같이 말합니다. `이 배우 내부의 내용에 액세스하거나 수정해야 합니다. 여기가 제 요청입니다. 안전하고 적절한 시기가 돌아올 때까지 비동기로 기다리겠습니다.`

<div class="content-ad"></div>

그러니까, 배우들에서 데이터 격리는 배우의 내부 상태와 외부 세계 간의 모든 상호작용이 이를 통해 조절되는 비동기 프로세스를 통해 이루어져야 함을 의미합니다. 이를 통해 배우가 동시 액세스 충돌의 위험 없이 안전하게 상태를 관리할 수 있습니다.

```js
actor Account {
 var balance: Int = 20// 현재 사용자 잔액은 20입니다.
 // ...
 func withdraw(amount: Int) {
  guard balance >= amount else {return}
  self.balance = balance - amount
 }
}

actor TransactionManager {
    let account: Account

    init(account: Account) {
        self.account = account
    }

    func performWithdrawal(amount: Int) async {
        await account.withdraw(amount: amount)
    }
}

// 사용법
let account = Account()
let manager = TransactionManager(account: account)

// TransactionManager 배우에서 인출 수행
Task {
    // 배우 간 참조
    await manager.performWithdrawal(amount: 10)
}

// 배우 바깥에서 인출 수행
Task {
    // 배우 간 참조
    await myAccount.withdraw(amount: 5)
}
```

Swift의 동시성 모델에서 await 키워드는 배우와 함께 작업할 때 특히 중요한 구성 요소입니다. 흥미롭게도, 배우 내 withdraw 함수를 명확하게 async로 표시할 필요가 없었음을 알 수 있습니다. 이는 기본적으로 배우의 모든 함수가 배우 자체의 특성으로 인해 잠재적으로 비동기로 간주되기 때문입니다. 외부에서 배우와의 모든 상호작용은 본질적으로 비동기이며, 따라서 어떤 배우 간 참조도 await로 선행되어야 합니다.

await를 사용하는 것은 실행에서 잠재적인 일시 정지를 나타내는 것과 같습니다. 마치 다른 사람이 사무실 프린터를 사용할 때 차례를 기다리는 것과 유사합니다. 이는 Swift 런타임에게 코드에서 이 지점이 배우가 요청을 처리할 준비가 될 때까지 실행을 보류해야 할 수도 있다고 알려주는 것입니다. 이러한 보류는 항상 발생하는 것은 아닙니다. 배우가 다른 작업으로 바쁘지 않다면 코드는 즉시 계속 진행됩니다. 이것이 '가능성 있는' 보류 지점으로 표현하는 이유입니다.

<div class="content-ad"></div>

이제 출금 시나리오에 이를 적용하면, 구현이 훨씬 안전하고 예측 가능해집니다. 두 스레드가 동시에 같은 계좌 엑터에서 출금을 실행하려고 할 때를 상상해보세요. 엑터와 await가 준비되어 있으면, 운영 체제가 첫 번째 스레드에서 두 번째 스레드로 실행을 전환하더라도, 두 번째 스레드가 즉시 출금 함수에 진입하지 않습니다. 실행이 중간에 멈추고 await 지점에서 일시 중지되어, 첫 번째 작업이 완료될 때까지 기다립니다. 이렇게 하면 작업이 직렬화되어 첫 번째 스레드가 출금을 완료한 후에야 엑터가 두 번째 스레드의 요청을 처리합니다. 이 시점에서 두 번째 스레드는 잔액이 다음 출금에는 충분하지 않음을 발견하고, 함수가 계좌 잔액에 더 이상 변경을 가하지 않고 반환됩니다.

이러한 방식으로, 엑터 모델은 비동기 액세스 패턴과 await 키워드로 공유 리소스인 계좌 객체에 대한 작업을 안전하게 처리하여 경쟁 조건을 방지하고 데이터 무결성을 유지합니다.

# 직렬 실행기

Swift의 엑터에 대한 논의에서, 각 엑터는 해당 메일박스 내의 작업(또는 '메일')을 하나씩 처리하는 내부 직렬 큐를 가지고 있다고 언급했습니다. 엑터의 내부 큐는 직렬 실행기(Serial Executor)로 알려져 있으며, 이는 직렬 DispatchQueue와 비슷한 역할을 합니다. 그러나 이들 사이에는 작업 실행 순서와 기본 구현의 처리 방식을 비롯한 중요한 차이점이 있습니다.

<div class="content-ad"></div>

One significant difference is that tasks waiting on an actor’s Serial Executor are not necessarily executed in the order they were awaited. This is a departure from the behavior of a Serial DispatchQueue, which adheres to a strict first-in-first-out (FIFO) policy. With a Serial DispatchQueue, tasks are executed exactly in the order they are received.

On the other hand, Swift’s actor runtime employs a lighter and more optimized queue mechanism compared to Dispatch, tailored to leverage the capabilities of Swift’s async functions. This difference stems from the fundamental nature of executors versus DispatchQueues. An executor is essentially a service that manages the submission and execution of tasks. Unlike DispatchQueues, executors are not bound to execute jobs strictly in the order they were submitted. Instead, executors are designed to prioritize tasks based on various factors, including task priority, rather than solely on submission order.

This nuanced difference in task scheduling and execution between Serial Executors and Serial DispatchQueues underpins the flexibility and efficiency of Swift’s actor model. Executors offer a more dynamic way to manage tasks, especially in a concurrent programming environment. I plan to explore Executors more deeply in a separate discussion to further elucidate their role and advantages in Swift’s concurrency model.

# Rules

<div class="content-ad"></div>

- 액터 내의 읽기 전용 속성에 접근할 때는 await을 사용할 필요가 없습니다. 왜냐하면 이들 값은 불변이기 때문에 변경되지 않기 때문이죠.

```js
actor Account {
 **let accountNumber: String = "IBAN---"**
 var balance: Int = 20 // 현재 사용자 잔액은 20입니다.
 // ...
 func withdraw(amount: Int) {
  guard balance >= amount else {return}
  self.balance = balance - amount
 }
}

**/// 이 부분은 괜찮아요 ✅**
let accountNumber = account.accountNumber
Task {
 **/// 이 부분은 괜찮아요 ✅**
 let balance = await account.balance
 
 **/// 이 부분은 좋지 않아요 ❌**
 let balance = ****account.balance // 에러
}
```

2. 액터 간 참조에서 변경 가능한 변수를 수정하는 것은 금지되어 있습니다. await을 사용해도 안 됩니다.

```js
/// 이 부분은 좋지 않아요 ❌
account.balance = 12 // 에러

/// 이 부분은 좋지 않아요 ❌
await account.balance = 12 // 에러
```

<div class="content-ad"></div>

3. 모든 액터에 고립된 함수는 await 키워드와 함께 호출되어야 합니다.

# 고립되지 않은 함수

Swift의 동시성 모델에서 액터 내의 비고립 멤버 개념이 중요한 역할을 합니다. 비고립 멤버를 통해 액터의 일부를 비동기 호출이나 액터 작업 대기 순번을 기다리지 않고도 액세스할 수 있습니다. 이는 액터 상태를 수정하지 않는 속성이나 메서드에 특히 유용하며, 이로 인해 경쟁 조건이나 다른 동시성 문제를 발생시키지 않습니다.

```js
actor Account {
 let accountNumber: String = "IBAN..." // 상수, 비고립 속성
 var balance: Int = 20 // 현재 사용자 잔고는 20
  // 고립되지 않은 함수
 nonisolated func getMaskedAccountNumber() -> String {
  return String.init(repeating: "*", count: 12) + accountNumber.suffix(4)
 }
 func withdraw(amount: Int) {
  guard balance >= amount else { return }
  self.balance = balance - amount
 }
}
```

<div class="content-ad"></div>

```js
let accountNumber = account.**getAccountNumber()**
```

당신의 예제에서 accountNumber는 Account actor 안에서 상수 속성(let)이며 변경불가능합니다. 이 불변성은 스레드 안전하고 격리가 필요하지 않다. 결과적으로, accountNumber는 actor의 일부임에도 불구하고 await 키워드 없이 동기적으로 액세스할 수 있습니다. 반면에 balance는 변경 가능한 속성이며 actor 내에서 격리가 필요합니다. withdraw 함수와 같은 balance와의 상호작용은 종종 비동기 액세스가 필요한 actor의 격리 프로토콜을 준수해야 합니다.

actor 내부의 격리된 멤버와 격리되지 않은 멤버 사이의 구별은 중요합니다. 이는 엄격한 격리가 필요하지 않은 시나리오에서 성능을 최적화하고 코드를 간소화하는 데 도움이 됩니다. 동시성 관리와 안전성을 유지하면서도 actor 모델에 내재된 특성을 유지합니다.

# 중요 사항

<div class="content-ad"></div>

- Concurrency Management에 대한 배우기: 우리는 Swift Actors를 Swift 5.5의 동시성 모델의 중요한 부분으로 소개했는데, 이는 공유 가능한 가변 상태를 안전하게 처리하고 데이터 레이스를 방지하기 위해 특별히 설계되었습니다.
- 동시성 문제 해결: Race 조건, 데드락, 리블락과 같은 일반적인 동시성 문제에 대해 이야기하고, Actor가 이러한 문제를 완화하는 방법을 보여줬습니다. 이러한 문제들은 전통적으로 DispatchQueue, Operations, 그리고 Locks를 이용해 관리되었습니다.
- 쓰레드 안전성과 직렬 실행: Actor는 Swift 애플리케이션의 쓰레드 안전성을 강화합니다. 내부 큐 안에서 직렬로 작업을 처리하여 큐 관리자처럼 작동함으로써 작업이 한 번에 처리되도록 보장하며 이로써 동시성 충돌을 피합니다.
- Cross-actor Reference 이해: Cross-actor 참조의 개념은 중요합니다. 이는 Actor의 속성이나 메서드에 접근하기 위해 await 키워드를 사용함으로써 효율적인 작업 관리를 위한 중단 지점을 표시하는 것을 필요로 합니다.
- 직렬 Executors 대 DispatchQueues: Actor의 직렬 Executor와 직렬 DispatchQueue의 주요 차이를 강조했습니다. Actor는 엄격한 선입선출 순서에 묶이지 않으며 여러 요인에 기반해 작업을 우선 순위로 처리할 수 있습니다.
- Actor와 상호 작용하는 규칙: Actor와 상호 작용하는 특정 규칙을 개요로 설명했는데, 가변 속성에 대한 비동기 접근과 격리된 함수를 기다리는 필요성을 강조했습니다.
- Actors 내의 비 격리 구성원: Actor 내의 비 격리 구성원 개념을 소개했습니다. 이는 쓰레드 안전성을 저해하지 않고 특정 속성이나 메서드에 동기적으로 액세스하는 데 유용합니다.
- 더 나은 이해를 위한 실제 예시: Account actor 예시를 통해, 우리는 Actor가 어떻게 상태를 안전하게 유지할 수 있는지 효과적으로 활용할 수 있는 방법에 대해 실제로 보여줬습니다.
- Exectors에 대한 미래 전망: 미래의 토론에서 Exectors에 대한 심층적인 탐구를 약속하며, Swift의 동시성 모델 이 측면에 대해 보다 자세히 탐구할 것을 기대하고 있습니다.

# 결론

Swift Actors의 세계를 완전히 탐험한 우리의 여정을 통해, Swift의 동시성 모델에서 이 강력한 기능의 심오함과 복잡성을 발견했습니다. Actor는 Swift 5.5의 기본 부분으로, 우리가 공유 가능한 가변 상태와 동시성 문제에 접근하는 방식을 재정의하여 제공하며, 전통적인 방법인 DispatchQueue, Operations, Locks와 비교해 보다 강력하고 안전한 동시성 프로그래밍 도전을 처리하는 방법을 제공합니다.

Actor가 Race 조건, 데드락, 리블락과 같은 일반적인 동시성 문제에 대한 보호 기능으로 작용함을 보았는데, 기존 방법보다 스트리밍되고 효율적인 접근을 제공합니다. Actor의 도입은 동시성 관리를 단순화하는 중요한 진전을 나타내어 개발자들에게 더 접근성이 높고 오류가 적은 방법으로 제공합니다.

<div class="content-ad"></div>

앞으로 계속 발전해 나가는 동안, 미래 토론에서 Executor에 대해 더 심층적으로 파고들 수 있는 약속은 Swift의 동시성 모델에서 더 발전된 주제들을 다룰 수 있는 문을 열어줍니다. 이 탐험은 단지 프로그래밍 기능을 이해하는 데 그치지 않고, 더 안전하고 신뢰성이 높고 효율적인 Swift 코드를 작성하는 새로운 패러다임을 수용하는 데 관한 것입니다.

맺음말로, Swift Actor는 Swift 개발자들에게 새로운 도구가 아닌, 우리가 동시성에 대해 생각하고 처리하는 방식에 근본적인 변화를 가져다 줍니다. 이는 코드를 더 안전하고 예측 가능하며 논리적으로 이해하기 쉽게 만들어줍니다. Swift Actor에 대한 이 탐험은 계속 발전하여 현대 소프트웨어 개발의 요구를 충족하기 위해 지속적으로 적응하고 개선하는 언어인 Swift의 진화의 증거입니다.