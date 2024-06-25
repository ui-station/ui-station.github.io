---
title: "스위프트UI MVVM  라우터"
description: ""
coverImage: "/assets/img/2024-05-18-SwiftUIMVVMRouter_0.png"
date: 2024-05-18 15:41
ogImage:
  url: /assets/img/2024-05-18-SwiftUIMVVMRouter_0.png
tag: Tech
originalTitle: "SwiftUI : MVVM + Router"
link: "https://medium.com/@akash.patel2520/swiftui-mvvm-router-265103a62a37"
---

<img src="/assets/img/2024-05-18-SwiftUIMVVMRouter_0.png" />

# MVVM 및 Router 이해하기

MVVM 아키텍처는 코드베이스를 세 가지 주요 구성 요소로 나눕니다.

- Model: 데이터 및 비즈니스 로직을 나타냅니다.
- View: 프레젠테이션 및 사용자 상호 작용을 처리합니다.
- ViewModel: Model과 View 사이의 중간 역할을 수행하여 데이터 로직과 상태를 관리합니다.

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

# 라우터

라우터는 응용 프로그램 내에서 다른 뷰나 화면 간의 탐색을 관리하는 역할을 합니다. 네비게이션 로직을 캡슐화하여 전환을 관리하고 관심사의 깔끔한 분리를 유지하기 쉽게 만듭니다.

# SwiftUI에서 MVVM + 라우터 구현

간단한 SwiftUI 앱을 만들어서 항목 목록을 가져와 표시하는 방법을 알아봅시다. 항목을 선택하면 해당 항목의 상세보기로 이동하는 기능을 구현해보겠습니다.

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

## 모델

아이템 모델은 id와 이름을 가진 기본 데이터 구조를 나타냅니다.

```swift
struct Item: Identifiable {
    let id: UUID
    let name: String
}
```

아이템 구조체는 간단합니다. id와 이름을 가진 아이템을 정의합니다. 이 모델은 앱 전반에서 사용되는 핵심 데이터 구조를 나타냅니다.

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

## ViewModel

ItemViewModel은 항목을 가져오고 뷰에 노출하는 역할을 합니다.

```swift
class ItemViewModel: ObservableObject {
    @Published var items: [Item] = []

    func fetchItems() {
        // 원격 서버에서 항목을 가져오는 것을 모방
        self.items = [
            Item(id: UUID(), name: "항목 1"),
            Item(id: UUID(), name: "항목 2"),
            Item(id: UUID(), name: "항목 3")
        ]
    }
}
```

ItemViewModel 클래스는 데이터를 가져오는 로직을 처리합니다. @Published 프로퍼티 래퍼를 사용하여 SwiftUI 뷰가 항목 배열이 변경될 때 반응적으로 업데이트되도록합니다. 실제 애플리케이션에서는 네트워크 요청이나 데이터베이스에서 데이터를 가져올 수 있지만 여기서는 간단함을 위해 더미 데이터를 채웁니다.

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

## 라우터

라우터 클래스는 현재 네비게이션 상태를 관리합니다.

```js
enum AppRoute {
    case itemList
    case itemDetail(Item)
}

class Router: ObservableObject {
    @Published var currentRoute: AppRoute?

    func navigateToItemDetail(_ item: Item) {
        currentRoute = .itemDetail(item)
    }

    func navigateToItemList() {
        currentRoute = .itemList
    }
}
```

라우터 클래스는 앱의 네비게이션 상태를 관리합니다. @Published 속성을 사용하여 뷰가 네비게이션 변경에 반응할 수 있습니다. navigateToItemDetail 및 navigateToItemList 메서드는 현재 경로를 업데이트하고 적절한 뷰가 표시됩니다.

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

## 화면

아이템 목록 뷰
아이템 목록 뷰는 아이템 목록을 표시하고, 아이템을 선택하면 상세 뷰로 이동합니다.

```swift
struct ItemListView: View {
    @ObservedObject var viewModel: ItemViewModel
    @EnvironmentObject var router: Router

    var body: some View {
        List(viewModel.items) { item in
            Button(action: {
                router.navigateToItemDetail(item)
            }) {
                Text(item.name)
            }
        }
        .onAppear {
            viewModel.fetchItems()
        }
        .navigationTitle("Items")
    }
}
```

아이템 목록 뷰는 ItemViewModel이 가져온 아이템 목록을 표시합니다. 각각의 아이템은 탭되면 상세 뷰로 이동하는 Button으로 둘러싸여 있습니다. 화면이 나타날 때도 onAppear 수정자를 사용하여 아이템을 가져옵니다.

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

아이템 상세보기
아이템 상세보기는 선택된 항목의 세부 정보를 표시하고 목록 보기로 돌아가는 버튼을 제공합니다.

```js
struct ItemDetailView: View {
    let item: Item
    @EnvironmentObject var router: Router

    var body: some View {
        VStack {
            Text(item.name)
                .font(.largeTitle)
            Spacer()
            Button("목록으로") {
                router.navigateToItemList()
            }
            .padding()
        }
        .navigationTitle("아이템 상세")
    }
}
```

아이템 상세보기 화면은 선택된 항목의 세부 정보를 보여주며, 아이템 목록으로 이동할 수 있는 버튼이 포함되어 있습니다. 이 화면은 @EnvironmentObject를 사용하여 라우터에 액세스하여 탐색 작업을 수행합니다.

컨텐츠뷰
컨텐츠뷰는 현재 경로에 따라 아이템 목록 뷰와 아이템 상세보기 뷰 간의 탐색을 관리하는 주요 진입점 역할을 합니다.

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
struct ContentView: View {
    @StateObject var itemViewModel = ItemViewModel()
    @StateObject var router = Router()

    var body: some View {
        NavigationView {
            VStack {
                switch router.currentRoute {
                case .none, .some(.itemList):
                    ItemListView(viewModel: itemViewModel)
                        .environmentObject(router)
                case .some(.itemDetail(let item)):
                    ItemDetailView(item: item)
                        .environmentObject(router)
                }
            }
        }
        .onAppear {
            router.navigateToItemList()
        }
    }
}
```

ContentView은 현재 라우터가 관리하는 현재 경로에 따라 탐색을 조율하는 주요 뷰입니다. 뷰 모델과 라우터를 초기화하고, switch 문을 사용하여 표시할 뷰를 결정합니다.

MVVM 및 라우터를 SwiftUI에서 구현함으로써, 잘 구조화되고 유지보수 가능한 코드베이스를 달성할 수 있습니다. 역할 분리를 통해 각 구성 요소가 명확한 책임을 갖도록하여 코드를 이해하기 쉽고 확장하기 쉽게 만듭니다. 이 아키텍처는 상태 및 탐색을 관리하기 어려운 복잡한 애플리케이션에 특히 유용합니다.

이 예시는 더 복잡한 시나리오에 맞게 확장할 수 있는 기초적인 접근 방식을 제공하여 SwiftUI 프로젝트가 확장 가능하고 견고하게 유지될 수 있도록 보장합니다.
