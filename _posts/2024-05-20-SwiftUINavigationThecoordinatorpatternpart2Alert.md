---
title: "SwfitUI 네비게이션 - 코디네이터 패턴 2부 -  경고 "
description: ""
coverImage: "/assets/img/2024-05-20-SwiftUINavigationThecoordinatorpatternpart2Alert_0.png"
date: 2024-05-20 16:08
ogImage: 
  url: /assets/img/2024-05-20-SwiftUINavigationThecoordinatorpatternpart2Alert_0.png
tag: Tech
originalTitle: "SwiftUI Navigation — The coordinator pattern part 2 — ⚠️ Alert ⚠️"
link: "https://medium.com/@ales.dieo/swiftui-navigation-the-coordinator-pattern-part-2-%EF%B8%8F-alert-%EF%B8%8F-534f75c68916"
---


<img src="/assets/img/2024-05-20-SwiftUINavigationThecoordinatorpatternpart2Alert_0.png" />

만약 Part 1을 아직 읽지 않았다면 여기에서 찾아볼 수 있어요. 이 글은 이전 글의 연장선입니다.

Part 1에서는 네비게이션 라우팅 로직을 coordinators로 격리시켰습니다. 이를 통해 앱을 위한 확장 가능하고 모듈식 네비게이션 설정을 만들었습니다. 하지만 이것은 기본에 불과해요. SwiftUI에서는 navigationPath로 push하는 것 이외에도 더 많은 종류의 네비게이션이 있어요. 이 중 하나는 "네비게이션"으로 팝업 알람을 표시하는 것이죠.

오늘은 이 주제에 대해 이야기할 거에요.

<div class="content-ad"></div>

SwiftUI에서는 다양한 수정자(modifier)를 제공하여 Alert를 표시할 수 있습니다. 일반적으로 이러한 Alert 수정자는 뷰(View)에 설정되며, ViewModel/View에는 Alert를 표시할지 여부를 결정하는 데 사용되는 Binding`Bool`이 있습니다. 이 논리는 저에게는 좀 더 화면에 적합하게 표현되도록 조정되어야 한다고 생각합니다. 그렇다면 그에 대비할 방법을 찾아봅시다.

Alert에는 다양한 기능이 있을 수 있습니다. 타이틀, 메시지, 한 개 또는 두 개 이상의 버튼(primaryAction, cancelAction)이 포함될 수 있으며, 이러한 버튼은 .destructive 역할을 갖는 등 다양한 역할을 할 수 있습니다. 이는 시스템이 설계된 방식의 일부이며 그것이 예상대로 작동할 것으로 예상됩니다. 그래서 모든 이러한 경우의 Alert를 표시하는 데 필요한 정보를 제공하기 위한 프로토콜을 생성해 봅시다.

```js
import SwiftUI

protocol AlertDisplayable { 
    var title: String { get }
    var message: String? { get }
    var buttons: [AlertButton] { get }
}

struct AlertButton {
    let title: String
    let role: ButtonRole?
    let action: () -> Void

    private init(title: String, role: ButtonRole?, action: @escaping () -> Void) {
        self.title = title
        self.role = role
        self.action = action
    }
    
    static func actionButton(title: String, action: @escaping () -> Void) -> Self {
        AlertButton(title: title, role: nil, action: action)
    }

    static func cancelButton(title: String, action: @escaping () -> Void) -> Self {
        AlertButton(title: title, role: .cancel, action: action)
    }

    static func destructiveButton(title: String, action: @escaping () -> Void) -> Self {
        AlertButton(title: title, role: .destructive, action: action)
    }
}
```

이제 part 1에서 생성한 NavigationController에 다음을 추가할 수 있습니다.

<div class="content-ad"></div>

```swift
@Observable final class NavigationController {
    var navigationPath = NavigationPath()
    var alertPath = AlertPath()

    func presentAlert<T>(_ alert: T) where T: AlertDisplayable {
        alertPath.setAlert(alert)
    }
}
```

일관성을 유지하기 위해 ****Path 규칙을 따르고 있지만 원하는 대로 이름을 지정할 수 있습니다.

```swift
import SwiftUI

struct AlertPath {

    var alert: AlertDisplayable?

    mutating func setAlert<T>(_ alert: T) where T: AlertDisplayable {
        self.alert = alert
    }
}
```

또한 설정한 후 경고를 처리하는 CoordinatedView를 업데이트해야 합니다.```

<div class="content-ad"></div>

```swift
struct CoordinatedView<C: Coordinator>: View {
    private let coordinator: C

    init(_ coordinator: C) {
        self.coordinator = coordinator
    }

    var body: some View {
        @Bindable var navigationController = coordinator.navigationController
        NavigationStack(path: $navigationController.navigationPath) {
            coordinator.rootView
        }
        .alert(for: $navigationController.alertPath) // <-- Here we add a new viewModifier
    }
}
```

이 alert modifier는 alertPath의 바인딩을 받아 설정된 알림이 있을 경우 알림을 표시하는 사용자 정의 ViewModifier입니다.

```swift
extension View {
    func alert(for alertPath: Binding<AlertPath>) -> some View {
        self.modifier(AlertModifier(alertPath: alertPath))
    }
}

struct AlertModifier: ViewModifier {

    @Binding var alertPath: AlertPath

    init(alertPath: Binding<AlertPath>) {
        self._alertPath = alertPath
    }

    func body(content: Content) -> some View {
        content
            .alert(
                alertPath.alert?.title ?? "",
                isPresented: Binding(get: { alertPath.alert != nil }, set: { _ in alertPath.alert = nil }),
                actions: {
                    if let buttons = alertPath.alert?.buttons {
                        ForEach(buttons) { button in
                            Button(button.title, role: button.role, action: button.action)
                        }
                    }

                }, message: {
                    if let message = alertPath.alert?.message {
                        Text(message)
                    }
                })
    }
}
```

- 알림의 제목은 title 속성에서 가져옵니다.
- alertPath의 존재 여부에 따라 수동으로 getter 및 setter를 설정한 Binding`Bool`를 전달합니다. 따라서 alert가 존재하는 경우 알림을 표시합니다. 그리고 알림이 닫힐 때 alert를 nil로 설정합니다.
- 사용 가능한 각 버튼에 대해 제공된 title, role 및 action으로 SwiftUI Button을 만듭니다. 버튼을 하나도 보내지 않으면 시스템이 단일 'OK' 버튼 알림으로 기본 설정됩니다.
- 필요에 따라 알림 아래에 제목 아래에 알림에 대한 일부 설명 정보가 포함될 수 있습니다.```

<div class="content-ad"></div>

한 번 시도해 보고 실제로 잘 작동하는지 확인해 봅시다. 알림 유형을 생성해 보겠습니다.

```js
// 비슷한 알림을 그룹화하거나 케이스별 알림을 만들고 싶다면 emum을 생성할 수 있습니다.
enum SomeAlert: AlertDisplayable {
    case alert(buttons: [AlertButton]) // <-- 액션을 주입합니다
    case alertWithTitleAndMessage(buttons: [AlertButton])

    var title: String {
        switch self {
        case .alert:                        return "제목만 있는 알림"
        case .alertWithTitleAndMessage:     return "제목과 메시지가 있는 알림"
        }
    }

    var message: String? {
        switch self {
        case .alert:                    return nil
        case .alertWithTitleAndMessage: return "이것은 메시지입니다"
        }
    }

    var buttons: [AlertButton] {
        switch self {
        case .alert(let buttons),
             .alertWithTitleAndMessage(let buttons):    return buttons
        }
    }
}

// 또는
// 알림을 따로 모델로 사용하고 싶다면 하드코딩된 값들을 가진 구조체를 만들 수 있습니다.
struct SomeSpecificAlertType: AlertDisplayable {
    var title: String = "특정 알림 제목"
    var message: String? = "특정 메시지"
    var buttons: [AlertButton]

    init(onAction: () -> Void, onCancel: () -> Void) { // <- 액션을 주입하고 해당 액션을 처리합니다
      self.buttons = [
          .actionButton(title: "두 번째 작업", action: onAction),
          .cancelButton(title: "취소", action: onCancel)
      ]
    }
}
```

알 수 있듯이, enum 케이스별로 Alert 유형을 생성하고 비슷한 알림 유형을 하나의 개체로 그룹화할 수 있습니다. 예를 들어, 모든 권한 알림을 한 곳에 유지하고 싶다면, 개별 알림 구조체/객체로 만들거나 선택은 여러분의 몫입니다. 중요한 점은 처음에 생성한 AlertDisplayable 프로토콜을 준수하는지입니다. 이제 모바일 앱에서 구현이 어떻게 보일지 살펴보겠습니다.

```js
struct FirstView: View {
    let viewModel: FirstViewModel

    var body: some View {
        VStack {
            Text("첫 번째 화면")
            Button("알림 표시") {
                viewModel.didTapButton()
            }
        }
    }
}

@Observable class FirstViewModel {
    let coordinator: FirstCoordinator

    init(coordinator: FirstCoordinator) {
        self.coordinator = coordinator
    }

    func didTapButton() {
        coordinator.presentAlert(
                SomeAlert.alert(buttons: [
                    .actionButton(title: "주요 작업") {
                        print("작업 버튼 누름") // <- 버튼 클릭 시 호출되는 클로저 
                    },
                    .cancelButton(title: "취소") {
                        print("취소 버튼 누름") // <- 버튼 클릭 시 호출되는 클로저 
                    }
                ]))
    }
}

@Observable final class FirstCoordinator: Coordinator {
    typealias Route = FirstCoordinatorRoute

    let navigationController: NavigationController

    @MainActor @ViewBuilder var rootView: some View {
        FirstView(FirstViewModel(self)).navigationDestination(for: Route.self, destination: coordinate(_:))
    }

    func presentAlert<T>(_ alert: T) where T: AlertDisplayable {
        navigationController.presentAlert(alert)
    }
}
```

<div class="content-ad"></div>

이는 viewModel에서 프로그래밍 방식으로 알림을 표시할 수 있게 해주며, 사용자가 버튼을 클릭할 때 viewModel이 선택적으로 콜백을 처리할 수 있게 합니다. 이 경우 뷰 자체 "FirstView"는 무엇이 일어나는지 전혀 모릅니다. 알고 있는 것은 didTapButton뿐입니다.

![image](/assets/img/2024-05-20-SwiftUINavigationThecoordinatorpatternpart2Alert_1.png)

부분 2에 대한 내용은 여기까지입니다.

이 내용이 유용하다고 생각되면 좋겠습니다. 피드백이나 개선 제안이 있으면 알려주세요. 이 글이 유익했다면 공유해 주세요!

<div class="content-ad"></div>

좋은 하루 보내세요!