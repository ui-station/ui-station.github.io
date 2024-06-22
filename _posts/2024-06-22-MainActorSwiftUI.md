---
title: "SwiftUI에서 MainActor를 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-MainActorSwiftUI_0.png"
date: 2024-06-22 23:05
ogImage: 
  url: /assets/img/2024-06-22-MainActorSwiftUI_0.png
tag: Tech
originalTitle: "MainActor — SwiftUI"
link: "https://medium.com/@hasanalidev/mainactor-swiftui-4d7b2545927c"
---


안녕하세요 여러분, 이 기사에서는 @MainActor를 사용하여 Swift 및 SwiftUI에서 주 스레드에서 작업을 수행하는 방법에 대해 논의할 것입니다.

![이미지](/assets/img/2024-06-22-MainActorSwiftUI_0.png)

Swift에서 @MainActor를 소개하면 동시성 관리에서 중요한 단계가 이루어집니다. 이는 사용자 인터페이스 작업을 안전하고 원활하며 이해하기 쉽게 보장하는 강력하고 직관적인 메커니즘을 제공합니다.

Swift 5.5 이상에서 @MainActor 속성은 async/await 동시성 모델에서 중요한 역할을 합니다. 이 기능은 코드의 특정 부분을 주 스레드에서 실행하도록 자동으로 조정하여 동시성 프로그래밍을 더 접근 가능하고 안전하며 오류 발생 가능성이 적도록 만듭니다. UIKit 및 SwiftUI는 데이터 문제를 예방하고 좋은 사용자 경험을 보장하기 위해 이러한 작업이 주 스레드에서 실행되어야 합니다. UI 관련 작업에 대해 매우 중요하므로 주요하게 고려해야 합니다.

<div class="content-ad"></div>

글로벌 액터의 주요 목표는 주 스레드에 초점을 맞추고 사용자 인터페이스를 업데이트하는 작업을 관리하는 것입니다. MainActor라는 글로벌 액터는 주 스레드를 나타내며 사용자 인터페이스에 빠른 변경이 필요한 상황에서 매우 유용합니다.

주 액터는 Dispatch 라이브러리를 활용하는 시스템에서 기본 동시성 메커니즘으로 작동하며 주 디스패치 대기열(DispatchQueue.main)을 특수 executor로 래핑합니다.

## 주 스레드에서 작업하기

주 스레드는 UI 관련 변경이 반드시 발생해야 하는 특별한 스레드로 간주됩니다. 애플리케이션에서 API 응답을 기다리거나 파일을 압축하거나 파일을 다운로드하는 것과 같은 시간이 많이 소요되는 작업이 있는 경우 이 작업을 별도의 백그라운드 스레드에서 실행한 다음 작업이 완료되면 사용자 인터페이스를 업데이트하기 위해 다시 주 스레드로 돌아가는 것이 좋습니다.

<div class="content-ad"></div>

Xcode에서 UI를 업데이트할 때 변수의 값을 변경하는지 확인할 수 없는 경우 오류가 발생합니다. 따라서 주 스레드에서 작업을 관리하는 것이 매우 중요합니다.

![Main Actor in SwiftUI](/assets/img/2024-06-22-MainActorSwiftUI_1.png)

주 스레드에서 작업을 수행하기 위한 전통적인 방법은 DispatchQueue.main.async를 사용하는 것입니다.

```js
DispatchQueue.main.async {
  // 여기서 UI 업데이트를 수행할 수 있습니다.
}
```

<div class="content-ad"></div>

그러나 Swift 동시성을 사용할 경우, 다른 @MainActor 클로저를 동기화하거나 MainActor.run() 메서드 대신 사용할 수 있습니다. 이를 위해 선호하는 세 가지 다른 방법이 있습니다.

```js
// (1)
Task.detached { @MainActor in
  // 여기서 UI 업데이트를 수행할 수 있습니다.
}

// (2)
Task { @MainActor [weak self] in
  // 여기서 UI 업데이트를 수행할 수 있습니다.
}

// (3)
Task {
  await MainActor.run { [weak self] in
    // 여기서 UI 업데이트를 수행할 수 있습니다.
  }
}
```

자동으로 주 스레드에서 작업하기

@MainActor의 가장 인상적인 기능은 주로 업무를 수행하기 위해 일반적으로 우리가 추가적인 작업을 수행할 필요 없이 함수나 모든 변수를 주 스레드에서 자동으로 실행할 수 있는 능력입니다. 대부분의 경우 컴파일러가 자동으로 작업을 처리하기 때문에 수동으로 주 스레드에 입력하고 작업을 수행할 필요가 없어졌습니다.

<div class="content-ad"></div>

사실 클래스, 구조체, 함수 또는 변수에 @MainActor 선언을 추가하면 Swift 컴파일러에게 "이 코드가 메인 스레드에서 실행되도록 해주세요!" 라고 말하는 것입니다. 이 기능은 특히 UI 업데이트, 이벤트 처리 및 메인 스레드에 의존하는 API와 상호 작용할 때 유용합니다.

MainActor는 특별한 속성으로 볼 수 있으며 프로퍼티 래퍼 유형이나 결과 빌더 유형과 유사합니다. @MainActor를 사용하여 선언이 메인 액터에서 격리되도록 지정할 수 있습니다. 이 경우 일반적인 액터 격리 제약이 적용됩니다: 메인 액터로 정의된 구조체에 대한 액세스는 다른 메인 액터에서 동기화될 수 있으며 그렇지 않으면 비동기적으로 액세스해야 합니다.

```js
@Observable
class LoginViewModel {
    @MainActor var showButton = false

    @MainActor func showLoginButton() {
        showButton = true
    }

    func hideLoginButton() {
        Task {
            await MainActor.run {
                showButton = false
            }
        }
    }

    func printButtonValue() async {
        print(showButton) // 에러 없음, 'await'으로 표시되지 않음
        print(await showButton) // 에러 없음
    }
}
```

또는 클래스를 직접 표시할 수도 있습니다.

<div class="content-ad"></div>

```js
@MainActor
class NewWeatherViewModel: ObservableObject {
    @Published var currentTemperature: Double = -5.2
    @Published var city: String = "시바스"
    @Published var isSnowing: Bool = false
    @Published var isRaining = false
}
```

`@MainActor`으로 선언을 표시하면 컴파일러에게 이 작업이 메인 스레드에서 호출되어야 하며, 다른 코드에서 동시성을 인식하도록 이 작업을 수행하라고 지시합니다. 그러나 많은 코드가 동시성에 대한 인식이 부족하며, @MainActor는 현재 이러한 경우에 아무것도 수행하지 않습니다. 왜냐하면 그렇게 한다면 과도한 경고가 발생할 수 있기 때문입니다. 실제로 안전한 경우에도 심지어 상황적으로 안전하더라도.

오래된 스타일의 완료 처리기 내에서 @MainActor 코드를 호출하고 있다면, 현재 메인 스레드로 명시적으로 반환해야 합니다. 가능하면 완료 처리기를 비동기 함수로 변환하거나 완료 처리기에 @Sendable 또는 @MainActor 주석을 추가하십시오.

## 이 모든 것을 요약하면

<div class="content-ad"></div>

주로 사용자 인터페이스를 업데이트하는 작업과 관련이 있지만, @MainActor의 영향은 iOS 애플리케이션의 기본 아키텍처에도 미치는 것이죠. 이 기능은 더 예측 가능하고 안전한 동시성 모델을 제공하여 보다 견고하고 지속 가능한 코드 작성을 촉진합니다. MainActor를 사용할 때 다음 사항에 주의하는 것이 중요합니다:

- 선택적 사용: 사용자 인터페이스 또는 주 스레드를 활용하는 다른 API와 상호 작용이 필요한 작업에 MainActor를 사용하세요.
- 성능: 주 스레드 관리를 간소화하고 불필요한 주 스레드 작업을 피하면서 응용 프로그램 성능을 고려하세요.
- 다른 동시성 도구와 통합: @MainActor를 Swift의 다양한 동시성 기능과 함께 사용하여 포괄적이고 안전한 애플리케이션을 구축하세요. MainActor가 iOS 개발 프로세스에 가져다주는 혁신은 개발자들에게 더 안전하고 깨끗하며 효율적인 코드를 작성할 수 있는 기회를 제공합니다. 이 기능은 동시성이 관리 가능하고 견고한 애플리케이션 아키텍처에 기본적인 Swift 개발의 새 시대를 시작합니다.

이상으로 Main actor에 대해 말씀드렸습니다. 다음 기사가 나올 때까지 즐거운 코딩 되세요!