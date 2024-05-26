---
title: "SwiftUI 네비게이션 - 코디네이터 패턴 - 파트 3 시트, FullscreenCover"
description: ""
coverImage: "/assets/img/2024-05-23-SwiftUINavigationThecoordinatorpatternpart3SheetFullscreenCover_0.png"
date: 2024-05-23 13:10
ogImage:
  url: /assets/img/2024-05-23-SwiftUINavigationThecoordinatorpatternpart3SheetFullscreenCover_0.png
tag: Tech
originalTitle: "SwiftUI Navigation — The coordinator pattern — part 3 Sheet , FullscreenCover"
link: "https://medium.com/@ales.dieo/swiftui-navigation-the-coordinator-pattern-part-3-sheet-fullscreencover-05eb1412f562"
---


![이미지](/assets/img/2024-05-23-SwiftUINavigationThecoordinatorpatternpart3SheetFullscreenCover_0.png)

이 글은 이전 글들을 이어서 작성되었으며, 그 위에 쌓아 올릴 것입니다.
SwiftUI 네비게이션 파트 1 — 푸시 네비게이션
SwiftUI 네비게이션 파트 2 — 알림

이 글에서는 Sheets 및 FullscreenCovers을 동시에 다룰 것이며, 이들은 동일한 방식으로 구현됩니다.

시작해 봅시다! 이전 글들에서는 결정적 목적지로 조정하기 위해 Routes를 사용했습니다. 그러나 SwiftUI는 어떻게 이루어지는 걸까요?
뷰 계층 구조의 루트에 있는 NavigationStack에 대한 네비게이션 경로에 대한 바인딩이 있습니다. Apple의 문서 예제를 살펴보겠습니다:


<div class="content-ad"></div>

```js
NavigationStack(path: $presentedParks) {
    List(parks) { park in
        NavigationLink(park.name, value: park)
    }
    .navigationDestination(for: Park.self) { park in
        ParkDetails(park: park)
    }
}
```

우리는 NavigationStack 라는 뷰가 있습니다. 이 뷰를 통해 일부 데이터 유형의 바인딩을 통해 .navigationDestination이라는 뷰 수정자에서 제공된 뷰로 이동할 수 있습니다 🤔 어떻게 보면 흥미로운 것 같네요.

그럼 이 뷰 수정자가 무엇인지 궁금하죠? 프레임워크 문서에서 더 자세히 살펴봐 봅시다.

```js
/// 스택에 한 개 이상의 네비게이션 대상 수정자를 추가할 수 있습니다.
/// 여러 데이터 유형을 표시해야 하는 경우 스택에 네비게이션 대상 수정자를 여러 개 추가할 수 있습니다.
///
/// "List" 또는 "LazyVStack"과 같은 "lazy" 컨테이너 내부에 네비게이션 대상 수정자를 넣지 마세요.
/// 이러한 컨테이너는 화면에 렌더링할 때 필요한 경우에만 자식 뷰를 생성합니다.
/// 항상 네비게이션 대상 수정자를 이러한 컨테이너 외부에 추가하여 네비게이션 스택이 대상을 항상 볼 수 있도록 하세요.
///
/// - Parameters:
///   - data: 이 대상이 일치하는 데이터의 유형입니다.
///   - destination: 스택의 네비게이션 상태에 유형 'data'의 값을 포함하고 있을 때 표시할 뷰를 정의하는 뷰 빌더입니다.
///     클로저는 데이터 값을 나타내는 하나의 인수를 사용합니다.
public func navigationDestination<D, C>(for data: D.Type, @ViewBuilder destination: @escaping (D) -> C) -> some View where D : Hashable, C : View
```

<div class="content-ad"></div>

뭐 이건 Data.Type을 받아들이는 ViewModifier인데, 그런 다음에 destination이라고 불리는 클로저가 주입되는데, 이 클로저는 hashable해야 할 that Data.Type의 인스턴스를 주입하고 구체적인 View를 반환해야 해요...... 그렇죠. 😵

또 다른 것은 .navigationDestination(\_:)이 NavigationStack 내부의 View에 적용되어야 하며, NavigationStack의 범위 외부에 적용할 수 없다는 것을 주의해야 해요. 🤔

뷰 계층 구조를 통해 데이터를 상위 뷰로 전파하는 유사한 시그니처를 가진 다른 것이 있어요. 이것이 바로 데이터를 뷰의 부모에게 전달하는 데 사용되는 PreferenceKeys입니다!

PreferenceKeys를 사용하여 .navigationDestination(\_:)이 NavigationStack에 하는 것처럼 내비게이션 목적지를 전달할 수 있을까요?
우리는 곧 알게 될 거예요!

<div class="content-ad"></div>

시트 및 풀스크린 커버의 ViewModifier를 검사해 봅시다.

```js
    public func sheet<Item, Content>(item: Binding<Item?>, onDismiss: (() -> Void)? = nil, @ViewBuilder content: @escaping (Item) -> Content) -> some View where Item : Identifiable, Content : View

    public func fullScreenCover<Item, Content>(item: Binding<Item?>, onDismiss: (() -> Void)? = nil, @ViewBuilder content: @escaping (Item) -> Content) -> some View where Item : Identifiable, Content : View
```

이들은 서로 거의 동일하며 navigationDestination(\_:)과도 매우 유사합니다. 주요 차이점은 나타낼 아이템에 대한 바인딩이 Hashable 대신 Identifiable을 준수해야 하며 전달할 수 있는 선택적인 onDismiss 클로저가 있는 것입니다.

Routes도 Identifiable을 준수하도록 만들어 보겠습니다.

<div class="content-ad"></div>

```js
enum FirstTabCoordinatorRoute: Codable, Hashable, Identifiable { // <-- Identifiable
    var id: String { String(describing: self) }

    case detailView
    case secondDetailView(String)
}
```

이제 루트를 처리하는 ViewModifier를 만들어야 합니다. navigationDestination(_:)가 어떻게 작동하는지 더 잘 이해하려고 했고, navigationDestination(_:)와 동일하게 목적지를 뷰 계층 구조로 전달할 수 있는 방법을 재현해 볼 수 있을지 생각했습니다.

내가 알기로는 뷰 계층 구조로 데이터를 전송하는 유일한 방법은 PreferenceKeys이니까 그것부터 시작하겠습니다.
그러나 몇 가지 기준이 있습니다. 어떤 경로에 대해서도 작동하고 다른 경로에 대한 모든 목적지를 해결할 수 있어야합니다. 이 기준으로 인해 타입 지워를 사용해야 합니다. 다음과 같이 PreferenceKeys를 생성해 봅시다.

```js
struct SheetFactoryKey: PreferenceKey {
    static var defaultValue: [String: NavigationViewFactory] = [:]
    static func reduce(value: inout [String: NavigationViewFactory], nextValue: () -> [String: NavigationViewFactory]) { value.merge(nextValue()) { $1 } } // reduce all into one value (dictionary)
}

struct CoverFactoryKey: PreferenceKey {
    static var defaultValue: [String: NavigationViewFactory] = [:]
    static func reduce(value: inout [String: NavigationViewFactory], nextValue: () -> [String: NavigationViewFactory]) { value.merge(nextValue()) { $1 } }
}

extension View {
    public func sheetDestination<D,C>(for data: D.Type, @ViewBuilder sheet: @escaping (D) -> C) -> some View where D: Identifiable & Hashable, C : View  {
        preference(key: SheetFactoryKey.self, value: [String(describing: data): NavigationViewFactory(data, sheet)]) // <- Here we set the key to the data type's description! and pass our view factory
    }

    public func coverDestination<D,C>(for data: D.Type, @ViewBuilder cover: @escaping (D) -> C) -> some View where D: Identifiable & Hashable, C : View  {
        preference(key: CoverFactoryKey.self, value: [String(describing: data): NavigationViewFactory(data, cover)])
    }
}
```

<div class="content-ad"></div>

네비게이션 목적지()가 sheetDestination()와 coverDestination() 선호 키 모두 호출자의 모든 값을 하나의 [String: NavigationViewFactory] 사전으로 줄입니다.

기본적으로 여러 Coordinator가 있는 경우 각각이 자체 경로 유형을 위해 sheetDestination() 선호 키를 사용합니다. 이것은 PreferenceKeys에서 보내진 모든 값들을 하나의 사전으로 병합/줄이고, 문자열 키를 사용하여 작업하는 데이터 유형에 대한 식별자로 사용할 수 있습니다. 따라서 뷰 계층 구조를 통해 전달된 대응하는 예상 값에 접근할 수 있습니다. 이 값은 해당 목적지를 반환하는 데 도움이 됩니다.

여기에는 데이터 유형을 설명하는 ID와 AnyView를 반환하는 타입 지워진 NavigationViewFactory가 있습니다.
AnyView는 SwiftUI에서 제공되는 타입 지워진 뷰 래퍼로 성능에 부담이 있으므로 절약해서 사용해야 합니다. 그러나 한 번에 하나의 시트나 커버만 표시할 수 있기 때문에 수용할만한 것으로 보입니다.

<div class="content-ad"></div>

NavigationController로 다시 가서 코드를 추가해 봅시다.

```js
@Observable final class NavigationController {
    var navigationPath = NavigationPath()
    var alertPath = AlertPath()
    var sheetPath: SheetPath = .init() // <- SheetPath를 생성해 봅시다
    var coverPath: CoverPath = .init() // <- CoverPath를 생성해 봅시다

    func presentSheet<T>(_ route: T, onDismiss: (() -> Void)? = nil)  where T: Codable & Identifiable & Hashable  {
        sheetPath.setSheet(route, onDismiss: onDismiss)
    }

    func presentCover<T>(_ route: T, onDismiss: (() -> Void)? = nil)  where T: Codable & Identifiable & Hashable  {
        coverPath.setCover(route, onDismiss: onDismiss)
    }
}

// Sheet
struct SheetPath: Identifiable {
    var id: String { sheet?.id ?? UUID().uuidString } // <- 현재 sheet의 id를 사용합니다.
    var sheet: SheetContainer?

    mutating func setSheet<T>(_ sheet: T, onDismiss: (() -> Void)? = nil) where T: Codable & Identifiable & Hashable  {
        self.sheet = SheetContainer(sheet, onDismiss: onDismiss)
    }
}

struct SheetContainer: Identifiable {
    let id: String
    let sheet: Any
    let onDismiss: (() -> Void)?

    init<T>(_ sheet: T, onDismiss: (() -> Void)? = nil) where T: Codable & Identifiable & Hashable  {
        self.id = String(describing: T.self)
        self.sheet = sheet
        self.onDismiss = onDismiss
    }
}

// Fullscreen Cover
struct CoverPath: Identifiable {
    var id: String { cover?.id ?? UUID().uuidString }
    var cover: CoverContainer?

    mutating func setCover<T>(_ cover: T, onDismiss: (() -> Void)? = nil) where T: Codable & Identifiable & Hashable  {
        self.cover = CoverContainer(cover, onDismiss: onDismiss)
    }
}

struct CoverContainer: Identifiable {
    let id: String
    let cover: Any
    let onDismiss: (() -> Void)?

    init<T>(_ cover: T, onDismiss: (() -> Void)? = nil) where T: Codable & Identifiable & Hashable  {
        self.id = String(describing: T.self)
        self.cover = cover
        self.onDismiss = onDismiss
    }
}
```

이 두 가지 구현 사항은 기본적으로 동일하며 같은 컨테이너, 경로, 함수 뷰 수정자 등을 사용하고 열거형 PresentationMode를 추가하여 sheet와 cover를 구분할 수 있는 case를 추가할 수 있습니다. 그러나 일관성을 유지하기 위해 이 예제에서는 이들을 분리해 두었습니다. 또한 SwiftUI가 이후에 여러 개의 sheet/cover를 지원한다면 별도로 처리하고자 할 수도 있습니다. 선택은 당신에게 맡깁니다. 지금까지 어떤 것들을 얻었나요?

- NavigationController에는 이제 sheet 및 fullScreenCover에 대한 경로가 있습니다.
- 우리는 데이터 작업의 구체적인 유형을 지우기 위해 타입 이레이저를 사용하지만, 우리가 수용할 수 있는 유형으로만 컨테이너를 초기화할 수 있도록 하여, 지워진 타입 값은 일종의 Identifiable, Hashable 및 Codable 유형이 되도록 보장합니다.
- 또한, 해당 데이터 유형의 설명을 Identifiable 준수를위한 식별자로 저장하여 우리의 뷰 팩토리 콜백 딕셔너리에서 구분할 수 있도록 합니다.

<div class="content-ad"></div>

여전히 함께 계신다니 너무 기쁘네요! 이제 모든 것을 함께 연결해보려 합니다.

이제 시트를 표시하는 ViewModifier를 만들어야 합니다.

```js
extension View {
    func sheet(for sheetPath: Binding<SheetPath>) -> some View {
        modifier(SheetModifier(sheetPath: sheetPath))
    }

    func cover(for data: Binding<CoverPath>, onDismiss: (() -> Void)? = nil) -> some View {
        modifier(CoverModifier(data: data))
    }
}

struct SheetModifier: ViewModifier {
    @Binding var sheetPath: SheetPath
    @State private var factories: [String: NavigationViewFactory] = [:]
    private let onDismiss: (() -> Void)?

    init(sheetPath: Binding<SheetPath>) {
        self._sheetPath = sheetPath
        self.onDismiss = sheetPath.wrappedValue.sheet?.onDismiss
    }

    func body(content: Content) -> some View {
        content
            .onPreferenceChange(SheetFactoryKey.self) { factories = $0 }
            .sheet(item: _sheetPath.sheet, onDismiss: sheetPath.sheet?.onDismiss, content: { factories[sheetPath.id]?.factory($0.sheet) })
    }
}

struct CoverModifier: ViewModifier {
    @Binding var data: CoverPath
    @State private var factories: [String: NavigationViewFactory] = [:]
    private let onDismiss: (() -> Void)?

    init(data: Binding<CoverPath>) {
        self._data = data
        self.onDismiss = data.wrappedValue.cover?.onDismiss
    }

    func body(content: Content) -> some View {
        content
            .onPreferenceChange(CoverFactoryKey.self) { factories = $0 }
            .fullScreenCover(item: _data.cover, onDismiss: data.cover?.onDismiss, content: { factories[data.id]?.factory($0.cover) })
    }
}
```

이게 뭐하는 거야? 모든 .sheetDestination()를 사용하는 코디네이터는 경로에 해당하는 코디네이션 함수를 뷰 계층 구조에 전달합니다. 그리고 sheetPath에 값을 설정하면 동일한 데이터 유형에 대한 viewFactory를 추출하고 데이터 인스턴스를 주입하여 메서드를 호출하여 대상을 반환할 것입니다. 모두 어떻게 함께 작동하는지 예제를 살펴봅시다.

```js
enum FirstTabCoordinatorRoute: Codable, Hashable, Identifiable {
    var id: String { String(describing: self) }
    ... 이전 경로들

    case sheet // 선택 사항으로 연결된 값 전달 가능
    case cover
}

@Observable final class FirstTabCoordinator: Coordinator {
    ... 이전 코드

    @ViewBuilder @MainActor var rootView: some View {
        let viewModel = FirstViewModel(self)
        FirstView(viewModel)
            .navigationDestination(for: Route.self, destination: coordinate(_:))
            .sheetDestination(for: Route.self, sheet: coordinate(_:))
            .coverDestination(for: Route.self, cover: coordinate(_:))
    }

    func presentSheet(onDismiss: (() -> Void)? = nil) {
        navigationController.presentSheet(Route.sheet, onDismiss: onDismiss)
    }

    func presentCover(onDismiss: (() -> Void)? = nil) {
        navigationController.presentCover(Route.cover, onDismiss: onDismiss)
    }

    @ViewBuilder @MainActor func coordinate(_ route: Route) -> some View {
        switch route {
            ... 이전 경로들

            case .sheet: SomeBranchedView()
            case .cover: SomeCoverView()
        }
    }
}
```

<div class="content-ad"></div>

와, 정말 깔끔하네요!
rootView 계산 속성 내에서 PreferenceKeys를 설정하여 모든 정보가 코디네이터의 Route.Types를 해결하는 데 필요한 정보를 코디네이트(coordinates) 함수로 보냅니다. 이 함수는 특정 코디네이터가 좌표를 맡은 모든 뷰를 생성합니다. 🤔 꽤 괜찮죠.

```js
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
        .sheet(for: $navigationController.sheetPath)
        .cover(for: $navigationController.coverPath)
        .alert(for: $navigationController.alertPath)
    }
}
```

마지막으로 루트 CoordinatedView에 sheet 또는 cover를 적용하는 방법에 대해 다음과 같이 살펴보겠습니다. 이것도 꽤 깔끔하네요 😉

그러니까 이 경우를 위해 우리의 view와 viewModel을 검사해봅시다.

<div class="content-ad"></div>

```swift
struct FirstView: View {
    let viewModel: FirstViewModel

    var body: some View {
        VStack {
            Text("First View")
            Button("상세 화면으로 이동") {
                viewModel.didTapButton()
            }
        }
    }
}

@Observable class FirstViewModel {

    private let coordinator: FirstTabCoordinator

    init(coordinator: FirstTabCoordinator) {
        self.coordinator = coordinator
    }

    func didTapButton() {
        coordinator.presentSheet {
          // closure called onDismiss
        }
    }
}
```

우리의 뷰는 didTapButton 이외에 무슨 일이 벌어지고 있는지 알 수 없습니다.

ℹ️ 또한 다른 필요한 수식어(modifier)들이 coordinate() 함수에서 반환되기 전에 뷰에 적용될 수 있습니다.

예를 들어, 시트에 presentationDetents를 적용하려면. 그리고 뷰 내부에서 심지어 그것에 대해 알 필요조차 없을 수 있습니다.```

<div class="content-ad"></div>

```js
@Observable final class FirstTabCoordinator: Coordinator {
    ... 이전 코드

    @ViewBuilder @MainActor func coordinate(_ route: Route) -> some View {
        switch route {
         ... 이전 루트

        case .sheet: SomeBranchedView().presentationDetents([.medium, .large]) // 시트를 반으로 크기 조정하고 드래그 액션 등을 허용합니다.
        case .cover: SomeCoverView()
        }
    }
}
```

지금은 여기까지입니다.

이 글에서 어떤 통찰을 얻으셨다면 좋겠네요. 피드백이나 개선 제안이 있다면 알려주세요. 이 글이 가치 있다고 느끼신다면 공유해주세요!

즐거운 코딩하세요!




