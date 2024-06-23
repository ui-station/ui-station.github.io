---
title: "Swift 동시성 메모리 관리에서 Task Cancellation과 Weak Self의 비교"
description: ""
coverImage: "/assets/img/2024-06-23-SwiftConcurrencyTaskCancellationvsWeakSelfforMemoryManagement_0.png"
date: 2024-06-23 21:25
ogImage: 
  url: /assets/img/2024-06-23-SwiftConcurrencyTaskCancellationvsWeakSelfforMemoryManagement_0.png
tag: Tech
originalTitle: "Swift Concurrency: Task Cancellation vs. Weak Self for Memory Management"
link: "https://medium.com/@noob-programmer/swift-concurrency-task-cancellation-vs-weak-self-for-memory-management-a2fca44eb90f"
---


![Image](/assets/img/2024-06-23-SwiftConcurrencyTaskCancellationvsWeakSelfforMemoryManagement_0.png)

Swift 개발에 관련해서, 메모리 관리를 이해하는 것은 복잡한 퍼즐을 해결하는 것과 같습니다. 이러한 퍼즐의 핵심에는 클로저와 비동기 작업을 처리하는 동안 메모리 누수에 빠지지 않는 것이라는 도전이 있습니다. 많은 개발자들이 리테인 싸이클을 피하는 데 사용해온 약한 참조(weak self) 개념은 클로저에서 탁월한 해결책이었습니다. 하지만 Swift의 동시성 모델, 특히 Task에서 이 실천이 원활하게 이어지는지 궁금할 것입니다. 실용적인 코드 예제와 통찰을 통해, 효율적인 Swift 프로그래밍을 위한 길을 밝혀보겠습니다.

# 다루는 주제

- 클로저에서 왜 weak self가 필요하며, 사용하지 않으면 어떻게 되는지?
- Task 블록에서도 실제로 weak self가 필요한가?
- Swift 동시성을 사용할 때 리테인 싸이클을 피하기 위해 어떤 조치를 취할 수 있는가?

<div class="content-ad"></div>

# 주요 포인트

- 클로저에서의 순환 참조와 약한 self 사용의 중요성을 이해하세요.
- 비동기 작업이 self를 강하게 캡처하면 작업 블록도 순환 참조를 생성할 수 있습니다.
- 컴파일러는 클로저와 달리 작업 블록에서 명시적 self 사용을 요구하지 않습니다.
- 약한 self에만 의존하지 않고 메모리를 효율적으로 관리할 대안으로 작업 취소를 사용할 수 있습니다.
- 비동기 작업을 다룰 때 ViewModel에서 순환 참조를 방지하기 위해 작업 취소를 구현하세요.

# 클로저에서의 'Weak Self' 딜레마

약한 self의 필요성을 이해하려면 먼저 Swift에서 클로저의 세계로 들어가 보겠습니다. 클로저는 참조 타입이며 강하게 인스턴스(예: self)를 캡처하고 유지할 수 있습니다. 이로 인해 잠재적인 순환 참조가 발생할 수 있습니다. 이는 객체가 self를 참조하는 클로저를 소유하고, 그 클로저가 객체 자체에 의해 소유되는 경우 발생하여 서로 해제될 수 없는 루프를 만듭니다.

<div class="content-ad"></div>

전통적인 시나리오:

```swift
class Repository {
    func remoteAPICallWithClosure(onComplete: @escaping (String) -> Void) {
        DispatchQueue.main.asyncAfter(deadline: .now() + 4) {
            onComplete("완료")
        }
    }
}

class SomeBigViewModel {
    private let repository = Repository()
    private var result = ""
    
    func doSomethingWithClosure() {
        repository.remoteAPICallWithClosure { apiResult in
            self.result = apiResult
        }
    }
}
```

이제 SomeBigViewModel의 인스턴스를 생성하고 doSomethingWithClosure을 호출한 후에 해당 인스턴스를 해제하려고 시도합니다:

```swift
var vm: SomeBigViewModel? = SomeBigViewModel()
vm?.doSomethingWithClosure()
vm = nil
```

<div class="content-ad"></div>

작업이 시작됩니다. 그러나 deint print 문은 즉시 나타나지 않을 것입니다. 이는 유지 주기(순환 참조)를 나타냅니다:

```js
작업이 시작됨
```

## 주기(순환 참조) 파괴하기

우리의 클로저에서 [weak self]를 사용하여 유지 주기를 피하고 SomeBigViewModel이 해제되도록 할 수 있습니다:

<div class="content-ad"></div>

```js
func doSomethingWithClosure() {
    print("작업이 시작되었습니다.")
    repository.remoteAPICallWithClosure { [weak self] apiResult in
        guard let self = self else { return }
        self.result = apiResult
        print("API 결과를 받았습니다.")
    }
}
```

코드를 수정하고 다시 실행한 후, 출력 결과는 예상대로 `deinit`이 호출되어 순환 참조가 해제된다는 것을 확인합니다:

```js
작업이 시작되었습니다.
SomeBigViewModel이 해제되고 있습니다.
```

# Swift의 병행성 모델: Task와의 사례

<div class="content-ad"></div>

Swift의 동시성 모델 소개로 Task에 대한 유사한 질문이 제기됩니다: 여전히 순환 참조를 방지하기 위해 weak self를 사용해야 할까요?

다음 비동기 작업을 고려해보세요.

```swift
func doSomething() {
    print("비동기 작업 시작됨")
    Task {
        do {
            let result = try await repository.remoteAPICall()
        } catch {
            print(error)
        }
    }
}
```

여기서 weak self를 사용하지 않으면, `Task`이 `self`을 강하게 캡처하면서 인스턴스가 작업이 완료되기 전에 해제되어야 하는 순환 참조가 발생합니다. 클로저와는 달리, 컴파일러는 여기서 명시적인 `self` 사용을 요구하지 않기 때문에 weak self이 모든 비동기 작업에서 필수적인지에 대한 의문이 생깁니다. 놀랍게도, 대안 전략으로 작업 취소라는 것이 있기 때문에 weak self가 모든 비동기 작업에서 꼭 필요하지는 않습니다.

<div class="content-ad"></div>

# 작업 취소: 전략적 접근

모든 비동기 작업에 일반적으로 `weak self`를 적용하는 대신 개발자들은 작업 취소를 더 적절한 전략으로 생각할 수 있습니다. 작업이 더 이상 필요하지 않을 때 해당 작업을 취소함으로써 `self`를 직접 관리하지 않고도 잠재적인 유지 사이클을 방지할 수 있습니다.

## 작업 취소 단순화:

```swift
public extension Task {
    func store(in set: inout Set<AnyCancellable>) {
        set.insert(AnyCancellable(cancel))
    }
}
```

<div class="content-ad"></div>

이 확장 프로그램은 작업 수명주기 관리를 간소화하여 집단 작업 취소를 용이하게 합니다.

## ViewModel에서 작업 취소 활용:

```js
@MainActor
class SomeBigViewModel {
    private let repository = Repository()
    private var cancellableBag = Set<AnyCancellable>()
    
    deinit {
        print("deinit called")
    }
    
    func send(_ action: Action) {
        switch action {
            case .viewWillDisappear:
                cancellableBag.removeAll()
        }
    }
    
    private func doSomething() {
        print("async operation started")
        Task {
            do {
                let result = try await repository.remoteAPICall()
            } catch {
                print(error)
            }
        }
        .store(in: &cancellableBag)
    }
}
```

작업 취소를 사용하면 ViewModel이 해제될 때 작업도 취소되어 명시적으로 `weak self`를 사용하지 않아도 보존 사이클을 방지합니다.

<div class="content-ad"></div>

```js
비동기 작업이 시작되었습니다
SomeBigViewModel이 해제 중입니다
```

더 섬세한 작업 취소 제어를 원하는 개발자를 위해 다음 패턴을 적용할 수 있습니다:

이 접근 방식을 사용하면 비동기 작업의 ViewModel 관리가 보다 구조적이고 효율적으로 되며, 기본적으로 약한 self에 의존하는 것보다 전략적인 작업 취소로서 보류 중인 사이클을 피할 수 있습니다.

이 기사를 즐겼다면, Medium에 Clap으로 사랑을 보여주시고 의견을 자유롭게 공유해주세요. 이와 유사한 통찰력을 얻고 싶다면, Medium에서 저를 팔로우하고 LinkedIn 및 Twitter에서 저와 연락하십시오. 함께 더 많은 기술 토론에 참여해 봅시다!

<div class="content-ad"></div>


Medium | LinkedIn | Twitter
