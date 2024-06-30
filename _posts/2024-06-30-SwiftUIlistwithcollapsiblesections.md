---
title: "SwiftUI에서 접을 수 있는 섹션을 사용한 리스트 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-SwiftUIlistwithcollapsiblesections_0.png"
date: 2024-06-30 19:21
ogImage: 
  url: /assets/img/2024-06-30-SwiftUIlistwithcollapsiblesections_0.png
tag: Tech
originalTitle: "SwiftUI list with collapsible sections"
link: "https://medium.com/@switch2mac/swiftui-list-with-collapsible-sections-beb58760ef2c"
---


리스트는 많은 데이터를 보여주는 좋은 방법입니다. 섹션은 이를 그룹화하는 데 유용합니다. 변수, 프로그래밍, 그리고 접을 수 있는 섹션을 어떻게 만들 수 있을까요?

리스트를 그룹화하는 한 가지 방법은 사이드바를 가지고 다양한 섹션을 숨기고 보여주는 것입니다. 이는 앱에서 유용한 리스트만 보여주기 위해 당신과 사용자가 도움이 됩니다.

# 간단한 방법

iOS14에서는 섹션 상태 변수를 위한 optional binding parameter를 도입했습니다. 이것은 .liststyle sidebar와 함께 사용되어 자동으로 섹션을 숨기고 보여줍니다.

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
@State private var isExpandedAllSeas: Bool = true
....

VStack {
    List {
       Section(
           isExpanded: $isExpandedAllSeas,
           content: {
              ForEach(allSea) { sea in
                  HStack {
                     Image(systemName: "water.waves")
                     Text(sea.name)
               }
       }
       .onDelete(perform: allSeaDelete)
       .onMove(perform: allSeaMove)
       },
       header: {
           Text("All seas")
       })
                 
    }
    .listStyle(.sidebar)
```

상태 변수의 초기 설정을 통해 접히거나 펼쳐진 섹션으로 시작할 수 있습니다.

# 더 어려운 방법

만약 이차원 데이터 배열을 가지고 있고 섹션의 수가 다른 경우에는 어떻게 하면 될까요?


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

부울(bool) 타입의 상태 변수를 사용하는 것으로는 문제를 해결할 수 없어요. 이렇게 하면 모든 섹션이 닫혀 있거나 열려 있게 됩니다.

`isExpanded`가 예상하는 타입인 `Binding<Bool>`이 필요합니다. 따라서 값에 대한 getter 및 setter 속성을 주의 깊게 처리해야 합니다.

예를 들어 몇 개의 해양을 나타내는 이차원 데이터로 시작해볼까요? 대양 지역(OceanRegion) 예시입니다.

```js
@State private var oceanRegions: [OceanRegion] = [
        OceanRegion(name: "태평양",
                    seas: [Sea(name: "오스트랄라시안 지중해"),
                           Sea(name: "필리핀 해"),
                           Sea(name: "코랄 해"),
                           Sea(name: "남중국해")]),
        OceanRegion(name: "대서양",
                    seas: [Sea(name: "미국 지중해"),
                           Sea(name: "사르가소 해"),
                           Sea(name: "카리브 해")]),
        OceanRegion(name: "인도양",
                    seas: [Sea(name: "벵갈만")])
 ]
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

그러면 약간 다른 상태 변수가 필요합니다.

```js
@State private var isExpanded: Set<String> = []
```

확장된 섹션의 모든 해양 지역 이름이 포함된 문자열 집합을 사용하고 있습니다. 이제 OceanRegions에서 섹션과 각 섹션마다 일부 Seas가 있는 List를 구성할 수 있습니다.

```js
VStack {
    List {
        ForEach(oceanRegions) { region in
            Section(
                isExpanded: Binding<Bool> (
                    get: {
                        return isExpanded.contains(region.name)
                    },
                    set: { isExpanding in
                        if isExpanding {
                            isExpanded.insert(region.name)
                        } else {
                            isExpanded.remove(region.name)
                        }
                    }
                ),
                content: {
                    ForEach(region.seas) { sea in
                        Text(sea.name)
                    }.onDelete(perform: { indexSet in
                        delete(indexSet: indexSet, region: region.id)
                    })
                },
                header: {
                    Text(region.name)
                }
            )
        }
    }
    .listStyle(.sidebar)
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

저희는 섹션의 축소 상태가 변경될 때 지역 이름을 삽입하고 제거해야 하기 때문에 State 변수인 isExpanded를 직접 사용할 수 없습니다.
그래서 이를 위해 isExpanded가 기대하는 Bool 유형의 제네릭 Binding을 사용하고 있습니다.

```js
isExpanded: Binding<Bool> (...)
```

이제 getter와 setter 로직이 필요합니다. 이겁니다. 섹션의 region.name을 사용합니다. 이유는 그겁니다. region.name이 고유하기 때문입니다. 물론 더 복잡한 시나리오에서는 uuid를 사용할 수도 있습니다.
getter는 boolean을 반환하며, 이 boolean은 State 변수에서 나왔기 때문에 Binding`Bool`입니다.
반면에 setter는 섹션이 확장되면 region.name을 String으로 Set에 추가하고, 섹션이 축소되면 region.name을 제거합니다.

```js
isExpanded: Binding<Bool> (
               get: {
                   return isExpanded.contains(region.name)
               },
               set: { isExpanding in
                   if isExpanding {
                       isExpanded.insert(region.name)
                   } else {
                       isExpanded.remove(region.name)
                   }
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

이 설정으로 모든 섹션이 처음에 접혀 있는 상태인 하나의 공개된 포인트가 있습니다. 초기 상태 isExpanded가 빈 Set이기 때문입니다.

```js
@State private var isExpanded: Set<String> = []
```

필요한 모든 데이터가 준비되어 있고, 우리는 이것을 고유한 init 함수와 연결하기만 하면 됩니다.

```js
// @State private var isExpanded: Set<String> = []
@State private var isExpanded: Set<String>
init() {
        _isExpanded = State(initialValue: Set(oceanRegions.map { $0.name }))
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

초기 isExpanded 상태를 채우려면 oceanRegions 배열에서 데이터를 매핑해야 합니다.

그리고 이렇게 하면 oceanRegions 배열의 모든 섹션이 확장된 상태로 표시됩니다.

<img src="/assets/img/2024-06-30-SwiftUIlistwithcollapsiblesections_0.png" />