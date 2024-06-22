---
title: "Swift 비동기 처리에서 경합 상태 버그 해결 방법 "
description: ""
coverImage: "/assets/img/2024-06-22-ResolvingaRaceConditionBuginSwiftConcurrency_0.png"
date: 2024-06-22 23:11
ogImage: 
  url: /assets/img/2024-06-22-ResolvingaRaceConditionBuginSwiftConcurrency_0.png
tag: Tech
originalTitle: "Resolving a Race Condition Bug in Swift Concurrency 💡"
link: "https://medium.com/@dalgudot/resolving-a-bug-caused-by-a-race-condition-in-faulty-swift-concurrency-code-bda1f3e9cbd8"
---


<img src="/assets/img/2024-06-22-ResolvingaRaceConditionBuginSwiftConcurrency_0.png" />

iOS 캘린더 스케줄러에는 '기본 캘린더' 기능이 있어요. Apple 캘린더와 같은 외부 캘린더와 통합한 후에, 이 설정은 이벤트를 추가할 때 가장 자주 사용하는 캘린더가 기본적으로 선택되도록 합니다. 이 기능은 사용자가 정의한 '기본 캘린더'를 연결된 캘린더에서 이벤트 스케줄링 시에 에디터의 '캘린더 선택' 섹션에서 자동으로 선택되도록 설정합니다.

<img src="/assets/img/2024-06-22-ResolvingaRaceConditionBuginSwiftConcurrency_1.png" />

# 💡 경합 조건으로 인한 문제 해결하기

<div class="content-ad"></div>

그러나 때로는 다른 닠린더가 선택된 경우도 있었습니다. 항상 그런 것은 아니었고, 대부분의 경우 정상적으로 작동했습니다. 제가 이 문제를 디버그하는 방법을 잘 모르겠었는데, 가끔 발생하는 문제라서 더욱 더 어려웠습니다. 가끔씩 발생하더라도, 이 기능이 제대로 작동하지 않으면 사용자가 잘못된 링크된 달력에 이벤트를 알지 못하게 추가할 수 있어 나중에 불필요한 수정을 유발할 수 있습니다.

최근에 이 문제를 해결할 실마리를 찾았습니다. Xcode 16 베타 버전의 새 기능에 대한 호기심으로 설치한 후 보게 된 경고 메시지 덕분이었습니다. 이 경고는 이전 버전의 Xcode에서는 나타나지 않았고, 스위프트 6의 보다 엄격한 Actor Isolation 규칙으로 인해 발생했습니다. 이 규칙은 동시성 모델을 더 안전하게 만들기 위한 것입니다.

![Image](/assets/img/2024-06-22-ResolvingaRaceConditionBuginSwiftConcurrency_2.png)

스위프트에서 Actor는 동시성 모델로, 내부 함수와 속성을 동시 액세스로부터 보호합니다. Actors는 수행하는 작업을 직렬화하여 각 작업이 순차적으로 실행되도록 합니다. 이를 통해 경합 조건 및 데이터 경주와 같은 동시성 문제를 방지할 수 있습니다.

<div class="content-ad"></div>

경주 조건은 시스템의 동작이 실행 순서에 따라 달라질 수 있는 다중 스레드 프로그래밍에서 중요한 개념입니다. 다시 말해, 경주 조건은 두 개 이상의 작업이 병렬로 실행되고 결과가 실행 순서나 시간에 따라 변경될 수 있는 경우 발생합니다. 경주 조건이 발생하면 프로그램이 예측할 수 없이 동작하거나 예상치 못한 결과를 내놓을 수 있습니다.

데이터 경주는 두 개 이상의 스레드가 동시에 동일한 메모리 위치에 액세스하고, 그 중 적어도 하나가 쓰기 작업을 수행하는 경우 발생합니다. 이는 메모리 일관성을 파괴하고 예측할 수 없는 프로그램 동작으로 이어질 수 있습니다. Swift의 Actor는 이러한 문제를 방지하기 위해 상태를 안전하게 격리합니다.

제 코드에서는 메인 Actor 콘텍스트에서 Actor로 격리된 ekRepository의 eventStore 속성에 액세스하려고 시도했으며, 이로 인해 Swift 6에서 컴파일 오류가 발생했습니다. 코드의 문제가 요약된 부분은 다음과 같습니다:

```swift
import EventKit

actor EKRepository {
    let eventStore: EKEventStore
    
    init() {
        self.eventStore = EKEventStore()
    }
}

@MainActor
final class EKInteractor: ObservableObject {
    private let ekRepository: EKRepository
    
    var eventStore: EKEventStore {
        /// 🔥 Actor-isolated property 'eventStore' can not be referenced from the main actor;
        /// 🔥 this is an error in the Swift 6 language mode.
        ekRepository.eventStore
    }
    
    init(ekRepository: EKRepository) {
        self.ekRepository = ekRepository
    }
}
```

<div class="content-ad"></div>

저는 이 코드를 처음 작성할 때 'eventStore'가 'EKRepository' 내에서만 액세스되도록 보장하는 것이 목표였습니다. 처음에는 EKInteractor가 속성으로 eventStore를 정의하지 않았습니다. 그러나 EKEventEditViewController와 같은 Apple에서 제공하는 UI에 EKEventStore를 매개변수로 전달해야 하는 상황이 많았습니다. 그 결과, EKInteractor는 eventStore을 노출하기 시작했습니다.

하지만, EKInteractor가 Actor Isolation을 무시하고 ekRepository의 eventStore에 액세스하고 있다면, 이는 경합 조건과 예기치 않은 문제를 초래할 수 있습니다.

이를 해결하기 위해 코드를 리팩토링하여 Actor Isolation을 준수하도록 만들어 경합 조건을 피했습니다. 리팩토링된 코드는 다음과 같습니다:

```swift
actor EKRepository {
    /// 💡 'getEventStore()' 메서드를 사용하여 eventStore에 대한 액세스 캡슐화
    private let eventStore: EKEventStore
    
    init() {
        self.eventStore = EKEventStore()
    }
}

extension EKRepository {
    func getEventStore() -> EKEventStore {
        return eventStore
    }
}

@MainActor
final class EKInteractor: ObservableObject {
    private let ekRepository: EKRepository
    
    private var cachedEventStore: EKEventStore? = nil
    
    /// 💡 Actor Isolation을 준수하면서 eventStore에 비동기적으로 액세스하고 캐시합니다.
    /// 우선 캐시된 eventStore이 있는지 확인하고,
    /// 그렇지 않으면 EKRepository의 getEventStore 메서드를 비동기적으로 호출하여 eventStore을 가져와 캐시합니다.
    var eventStore: EKEventStore {
        get async {
            if let eventStore = cachedEventStore {
                return eventStore
            }
            
            let store = await ekRepository.getEventStore()
            cachedEventStore = store
            return store
        }
    }
    
    init(ekRepository: EKRepository) {
        self.ekRepository = ekRepository
        
        /// 💡 시작할 때 eventStore을 비동기적으로 가져와 캐싱하여 이후 액세스 성능을 향상시킵니다.
        initEventStore(ekRepository: ekRepository)
    }
}

extension EKInteractor {
    private func initEventStore(ekRepository: EKRepository) {
        /// 💡 [Task] 이 메서드는 eventStore을 비동기적으로 가져와 캐시하지만,
        /// 이 프로세스가 완료되기 전에 후속 작업이 진행될 수 있습니다.
        Task {
            self.cachedEventStore = await ekRepository.getEventStore()
        }
    }
}
```

<div class="content-ad"></div>

또한, 'Default Calendar'를 설정하는 부분에서 레이스 컨디션에 민감한 코드를 발견했어요. 아래 코드에서는 newEKEvent의 캘린더(타입은 EKCalendar)가 Task 블록 내에서 비동기로 설정되어 있습니다. 이 비동기 작업은 getNewEKEvent 함수가 newEKEvent 객체를 반환한 뒤에 실행될 수 있습니다. 이는 반환된 newEKEvent 객체를 다른 곳에서 사용하려고 할 때 캘린더 속성이 아직 설정되지 않을 수 있다는 의미입니다. 이러한 상황은 '실행 순서(execution order)'에 따라 예측할 수 없는 동작을 유발할 수 있으며, 이는 해결하고자 했던 간헐적 버그와 일치합니다.

```js
private func getNewEKEvent(ekInteractor: EKInteractor, selectedDate: Date) -> EKEvent {
    let newEKEvent = EKEvent(eventStore: ekInteractor.eventStore)
    
    // 🔥 문제: 이 Task 블록은 비동기로 실행되므로, 함수가 newEKEvent를 반환한 후에 실행될 수 있습니다.
    Task {
        // 🔥 문제: 비동기 작업이 newEKEvent 반환 후에 완료된 경우, 캘린더 속성이 아직 설정되지 않을 수 있습니다.
        if let defaultEKCalendarToAdd = await ekInteractor.getDefaultEKCalendarToAdd() {
            newEKEvent.calendar = defaultEKCalendarToAdd
        }
    }
    
    // 🔥 문제: 함수가 비동기 작업이 완료되기 전에 newEKEvent를 반환하므로, 데이터 레이스와 레이스 컨디션이 발생할 수 있습니다.
    return newEKEvent
}
```

요약하면, 위의 코드는 '레이스 컨디션'의 대상이 될 수 있으며, 비동기 작업이 완료되기 전에 객체가 반환됩니다. 이 문제는 아래에 표시된 대로 코드를 재구성하여 해결할 수 있습니다.

```js
/// 💡 비동기 작업을 수행할 수 있도록 getNewEKEvent 함수를 async로 선언하여,
/// 모든 비동기 작업이 완료된 후에만 newEKEvent가 반환되도록 함.
/// ---> 레이스 컨디션과 데이터 레이스 방지
private func getNewEKEvent(ekInteractor: EKInteractor, selectedDate: Date) async -> EKEvent {
    /// 💡 eventStore를 비동기로 가져오기 위해 await 사용
    let newEKEvent = EKEvent(eventStore: await ekInteractor.eventStore)
    
    /// 💡 defaultEKCalendarToAdd를 비동기로 가져와 newEKEvent의 캘린더 속성에 설정
    if let defaultEKCalendarToAdd = await ekInteractor.getDefaultEKCalendarToAdd() {
        newEKEvent.calendar = defaultEKCalendarToAdd
    }
    
    /// 💡 모든 비동기 작업이 완료된 후에 newEKEvent 반환
    return newEKEvent
}
```

<div class="content-ad"></div>

# 결론

이 문제를 해결하면서 Swift 동시성에 대한 이해가 깊어졌습니다. 안전한 동시 코드 작성의 기초를 확립할 수 있어 기쁩니다. 무엇보다도, Scheduler 앱의 다음 업데이트에서 사용자에게 흠잡을 데 없는 '기본 캘린더' 기능을 제공할 수 있어 기쁩니다.

'Reminders'부터 '기본 캘린더'까지 모든 것을 신속하게 통합하세요.
다양한 위젯.
Scheduler: iPhone, iPad 및 Mac용 캘린더 앱.

# 참고문헌

<div class="content-ad"></div>

- Swift 프로그래밍 언어 — 동시성:
https://docs.swift.org/swift-book/documentation/the-swift-programming-language/concurrency/
- Apple 개발자 문서 — Swift 동시성:
https://developer.apple.com/documentation/swift/concurrency
- Apple 개발자 문서 — EKEventStore:
https://developer.apple.com/documentation/eventkit/ekeventstore
- 경합 조건 이해와 해결:
https://en.wikipedia.org/wiki/Race_condition
- Apple 개발자 문서 — Task:
https://developer.apple.com/documentation/swift/task

이러한 자료들은 Swift 동시성, 액터 및 관련 주제에 대해 자세한 정보를 제공하여 경합 조건과 같은 문제를 이해하고 해결하는 데 도움이 됩니다.