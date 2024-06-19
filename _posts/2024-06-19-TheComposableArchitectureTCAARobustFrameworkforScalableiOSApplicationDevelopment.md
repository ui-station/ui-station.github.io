---
title: "구성 가능한 아키텍처TCA 확장 가능한 iOS 애플리케이션 개발을 위한 견고한 프레임워크"
description: ""
coverImage: "/assets/img/2024-06-19-TheComposableArchitectureTCAARobustFrameworkforScalableiOSApplicationDevelopment_0.png"
date: 2024-06-19 11:06
ogImage: 
  url: /assets/img/2024-06-19-TheComposableArchitectureTCAARobustFrameworkforScalableiOSApplicationDevelopment_0.png
tag: Tech
originalTitle: "The Composable Architecture (TCA): A Robust Framework for Scalable iOS Application Development"
link: "https://medium.com/@vinodh_36508/the-composable-architecture-tca-a-robust-framework-for-scalable-ios-application-development-6f39ae32e050"
---


![이미지](/assets/img/2024-06-19-TheComposableArchitectureTCAARobustFrameworkforScalableiOSApplicationDevelopment_0.png)

컴포저블 아키텍처(TCA)는 Point-Free 팀이 디자인한 첨단 소프트웨어 아키텍처 패턴으로, 확장 가능하고 유지보수가 용이하며 테스트 가능한 iOS 애플리케이션을 구축하기 위해 고안되었습니다. Swift 프로그래밍 언어를 활용하는 TCA는 iOS 애플리케이션을 설계하고 구현하는 구조화된 조합 방법론을 제공하여 간결함, 모듈성 및 관심사의 분리에 중점을 둡니다.

# TCA의 주요 개념과 원칙

## 1. 상태(State):

<div class="content-ad"></div>

TCA에서 애플리케이션 상태는 애플리케이션의 현재 데이터 및 UI 상태를 나타내며, 변경할 수 없는 구조체로 정의됩니다. 각 상태 요소를 명시적으로 정의하면 이해하고 관리하기 쉽고 더 명확하고 유지보수가 쉬운 코드를 작성할 수 있습니다.

```js
struct AppState {
 var count: Int
 var isLoggedIn: Bool
}
```

## 2. 액션:

TCA에서 액션은 애플리케이션 상태에 변경을 일으킬 수 있는 이벤트 또는 사용자 의도입니다. 각 액션은 애플리케이션 내에서 특정 이벤트에 해당하는 열거형으로 표시됩니다. 이 구조화된 접근 방식은 상태 업데이트가 예측 가능하고 제어 가능하다는 것을 보장합니다.

<div class="content-ad"></div>

```js
enum AppAction {
 case increment
 case decrement
 case login
 case logout
}
```

## 3. 리듀서:

리듀서는 TCA에서 현재 상태와 액션을 입력으로 받아 새로운 상태를 반환하는 순수 함수입니다. 비즈니스 로직을 캡슐화하여 주어진 액션에 기반한 일관된 상태 변경을 보장합니다. 이를 통해 응용 프로그램의 동작이 더 예측 가능해집니다.

```js
var body: some Reducer {
  Reducer { state, action, environment in
    switch action {
      case .increment:
        state.count += 1
        return .none
      case .decrement:
        state.count -= 1
        return .none
      case .login:
        state.isLoggedIn = true
        return .none
      case .logout:
        state.isLoggedIn = false
        return .none
    }
  }
}
```

<div class="content-ad"></div>

## 4. 환경:

TCA의 환경은 서비스, API 또는 데이터 소스와 같은 외부 종속성에 대한 참조를 보유하는 종속성 컨테이너 역할을 합니다. 이를 통해 리듀서와 이펙트가 이러한 종속성과 제어 가능하고 테스트 가능하게 상호 작용할 수 있으며, 코드의 모듈성과 유지 보수성을 향상시킵니다.

```js
@Dependency(\.apiClient) var apiClient
```

## 5. 이펙트:

<div class="content-ad"></div>

TCA에서의 Effects는 네트워크 요청이나 데이터베이스 작업과 같은 부작용을 나타냅니다. Reducer에서 반환할 수 있는 값으로 모델링되며, Effects는 비동기적으로 실행되어 상태를 업데이트하는 새로운 액션을 생성할 수 있어 반응적이고 동적인 애플리케이션 흐름을 용이하게 합니다.

```js
case .login:
 state.isLoggedIn = true
 return .run { send in
      await send(.userDataResponse(Result {
       try await apiClient.fetchUserData()
       }))
 }
```

## 6. Stores:

Stores는 TCA의 중심 구성 요소로, 애플리케이션 상태를 관리하고 액션을 처리합니다. Store는 현재 상태, 리듀서 및 환경을 포함하며, 액션을 디스패치하고 상태 변경을 관찰할 수 있는 메서드를 제공하여 애플리케이션의 상태 관리를 중앙 집중화합니다.

<div class="content-ad"></div>

## 7. 조회:

TCA에서 뷰는 상태를 갖지 않고 선언적인 UI 구성 요소로, 애플리케이션의 현재 상태를 렌더링하고 사용자 상호작용에 따라 액션을 디스패치합니다. 이 설계는 UI와 비즈니스 로직 사이의 명확한 분리를 장려하여 코드 가독성과 유지보수성을 향상시킵니다.

```js
struct ContentView: View {
    @Bindable var store: StoreOf<LoginCore>

    init(store: StoreOf<LoginCore>) {
        self.store = store
    }

    var body: some View {
         VStack {
             Text("\(store.count)")
             Button("증가") {
                store.send(.increment)
                 }
             Button("감소") {
                store.send(.decrement)
                 }
             if store.isLoggedIn {
                 Text("환영합니다!")
                 }
            else {
                 Button("로그인") {
                    store.send(.login)
                     }
            }
        }
    }
}
```

# 다른 아키텍처 패턴 대비 TCA의 장점

<div class="content-ad"></div>

## 1. 모듈화와 구성:

TCA는 상태, 액션, 리듀서 및 이펙트와 같은 구성 요소가 자기 포함적이고 재사용 가능한 모듈화 접근 방식을 촉진합니다. 이 모듈화는 복잡한 애플리케이션을 처리 가능한 조각으로 분해하여 코드 구성과 재사용을 개선합니다.

## 2. 예측 가능한 상태 관리:

TCA는 상태 변경을 액션과 리듀서를 통해 처리하여 상태 전이를 결정론적이고 추론하기 쉽도록 합니다. 이 예측 가능성은 디버깅을 간단하게 하고 예상치 못한 동작이 발생할 가능성을 줄입니다.

<div class="content-ad"></div>

## 3. 단방향 데이터 흐름:

단방향 데이터 흐름을 준수함으로써 TCA는 관심사의 명확한 분리를 강제하고 제어되지 않은 데이터 변이를 방지합니다. 이 패턴은 데이터 흐름을 추적하고 상태 변경이 애플리케이션을 통해 어떻게 전파되는지 이해하는 데 도움이 됩니다.

## 4. 테스트 용이성:

TCA는 비즈니스 로직을 UI 구성 요소와 외부 종속성으로부터 분리함으로써 테스트 가능성을 향상시킵니다. 리듀서와 효과는 격리된 상태에서 단위 테스트할 수 있어 다양한 시나리오에서 애플리케이션 동작을 확인하는 포괄적인 테스트 스위트를 가능하게 합니다. 의존성 주입과 순수 함수는 쉬운 모킹과 테스트를 지원합니다.

<div class="content-ad"></div>

```js
let store = TestStore(
 initialState: AppState(count: 0, isLoggedIn: false),
 reducer: appReducer,
 environment: AppEnvironment(apiClient: MockAPIClient(), mainQueue: .main)
)
let store = TestStore(
              initialState: AppState(count: 0, isLoggedIn: false)
            ) {
              LoginCore()
            } withDependencies: {
              $0.apiClient = .mock
            }
store.send(.increment) {
 $0.count = 1
}
```

## 5. Scalability and Maintainability:

TCA의 구조화된 접근 방식은 확장성과 유지 관리성을 향상시킵니다. 모듈화된 디자인과 관심사 분리로 인해 기능 추가, 코드 리팩터링 및 다른 개발자들과 협업하는 것이 더 쉬워집니다. TCA의 선언적인 특성 또한 코드 가독성을 향상시키며, 개발자들에게 인지 부하를 줄여줍니다.

## 6. Error Handling and Resilience:

<div class="content-ad"></div>

TCA는 효율적인 오류 처리 및 부작용 관리 메커니즘을 제공합니다. 이는 효과 취소 및 재시도 전략과 같은 부작용을 다루는 데 도움이 됩니다. 리듀서와 이펙트 내에 오류 처리 로직을 캡슐화함으로써 TCA는 응용 프로그램 전반에서 일관된 오류 관리를 보장하여 복원력과 사용자 경험을 향상시킵니다.

# 다른 아키텍처와 비교

MVC(Model-View-Controller) 또는 MVVM(Model-View-ViewModel)과 같은 전통적인 패턴과 비교하여 TCA는 보다 구조화되고 예측 가능한 접근 방식을 제공합니다.

응용 프로그램이 커짐에 따라 MVC 및 MVVM은 코드가 얽히고 복잡해질 수 있지만, TCA는 모듈화와 단방향 데이터 흐름을 강조하여 관심사의 깔끔한 분리를 유지하는 데 도움이 됩니다.

<div class="content-ad"></div>

TCA(TemporaryChange-of-Address)는 VIPER(View-Interactor-Presenter-Entity-Router)에 비해 테스트 가능성과 간단함에서 뛰어납니다. VIPER와 자주 관련된 복잡성과 보일러플레이트를 피할 수 있습니다.

# 잠재적인 단점 대응

TCA는 많은 혜택을 제공하지만 잠재적인 도전이 있음을 인지하는 것이 중요합니다:

- 학습 곡선: TCA는 새로운 개념을 소개하며 함수형 프로그래밍 패러다임을 이해해야 하기 때문에, 보다 전통적인 패턴에 익숙한 개발자에게는 도전이 될 수 있습니다.
- 초기 설정: 새 프로젝트에 TCA를 설정하는 것은 처음에는 보일러플레이트 코드가 더 많이 필요하지만, 장기적인 유지 관리성과 확장성 측면에서 보상을 받을 수 있습니다.
- 성능 부담: TCA의 추가 추상화 레이어는 약간의 성능 부담을 줄 수 있지만, 대부분의 애플리케이션에는 무시해도 괜찮을 정도로 미세합니다.

<div class="content-ad"></div>

# 결론

Composable Architecture는 iOS 애플리케이션을 구축하기 위한 실용적이고 효과적인 프레임워크를 제공하며, 간결함, 확장성 및 유지보수성을 강조합니다. 이 모듈식, 예측 가능하고 테스트 가능한 디자인은 변화하는 요구사항과 함께 발전할 수 있는 견고하고 고품질의 소프트웨어를 개발하는 데 이상적인 선택지입니다.

TCA를 활용함으로써, 개발자들은 잘 구조화되고 견고한 iOS 애플리케이션을 만들어 스위프트 생태계에서 애플리케이션 아키텍처의 새로운 표준을 설정할 수 있습니다.