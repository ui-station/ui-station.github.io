---
title: "SwiftUI에서 다양한 그리드 뷰를 만드는 방법 종합 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_0.png"
date: 2024-06-23 23:53
ogImage:
  url: /assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_0.png
tag: Tech
originalTitle: "Various Kinds of Grid Views in SwiftUI: A Comprehensive Guide"
link: "https://medium.com/@jakir/various-kind-of-grid-views-in-swiftui-a-comprehensive-guide-5650511937a0"
---

# LazyVGrid 그리고 LazyHGrid

SwiftUI에서는 LazyVGrid 또는 LazyHGrid 뷰를 사용하여 반응형 그리드 뷰를 만들 수 있습니다. 만약 세로 그리드를 원한다면 LazyVGrid 뷰를 사용하고, 가로 그리드를 원한다면 LazyHGrid 뷰를 사용할 수 있습니다. 이러한 뷰들을 사용하면 다양한 화면 크기와 방향에 적응하는 항목 그리드를 생성할 수 있습니다.

LazyVGrid

그리드를 표시하는 가장 좋은 방법 중 하나는 적응형 열을 사용하는 것입니다. 화면 크기에 따라 자동으로 자식 요소를 적응시킵니다. SwiftUI를 사용하여 반응형 그리드 뷰를 생성하는 예시는 다음과 같습니다:

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
import SwiftUI

struct ContentView: View {

    private var data  = Array(1...20)
    private let adaptiveColumn = [
        GridItem(.adaptive(minimum: 150))
    ]

    var body: some View {

        ScrollView{
            LazyVGrid(columns: adaptiveColumn, spacing: 20) {
                ForEach(data, id: \.self) { item in
                    Text(String(item))
                        .frame(width: 150, height: 150, alignment: .center)
                        .background(Color.blue)
                        .cornerRadius(10)
                        .foregroundColor(Color.white)
                        .font(.title)
                }
            }

        } .padding()
    }
}

#Preview {
    ContentView()
}
```

출력:

<img src="/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_0.png" />

.adaptive(minimum: 150) 으로 설정하면 가능한 한 많은 항목을 행당 씩, 각각 150포인트의 최소 크기를 사용하여 그리드에 맞게 설정하고 있다는 뜻입니다. 다양한 화면 사이즈에서 각 열에 표시되는 항목 수가 다르게 표시됩니다.

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

LazyVGrid에서 adaptive() 크기 수정자 외에도 fixed()이나 flexible() 크기 수정자와 같은 다른 수정자들을 사용할 수 있습니다.

flexible() 크기 수정자

각 열의 항목 수를 제어하려면 flexible() 크기 수정자를 사용할 수 있습니다.

```js
import SwiftUI

struct ContentView: View {

    private var data = Array(1...20)
    private let flexibleColumn = [

        GridItem(.flexible(minimum: 100, maximum: 200)),
        GridItem(.flexible(minimum: 100, maximum: 200)),
        GridItem(.flexible(minimum: 100, maximum: 200))
    ]

    var body: some View {

        ScrollView {
            LazyVGrid(columns: flexibleColumn, spacing: 20) {
                ForEach(data, id: \.self) { item in
                    Text(String(item))
                        .frame(width: 100, height: 100, alignment: .center)
                        .background(Color.blue)
                        .cornerRadius(10)
                        .foregroundColor(.white)
                        .font(.title)
                }
            }
        }
        .padding()
    }
}

# 미리보기 {
    ContentView()
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

아래와 같이 사용 가능합니다:

<img src="/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_1.png" />

flexible() modifier를 사용하면 각 열의 항목 수를 제어할 수 있습니다. 그러나 뷰의 최소 크기를 수용할만큼 충분한 공간이 없는 경우 항목이 겹칠 수 있습니다.

fixed() 크기 수정자

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

만약 고정 크기의 열을 가진 LazyVGrid를 만들고 싶다면, 각 열에 .fixedSize() 수정자를 사용할 수 있습니다. 아래는 고정된 열을 가진 LazyVGrid를 생성하는 예시입니다:

```swift
import SwiftUI

struct ContentView: View {

    private var data = Array(1...20)
    private let fixedColumn = [
        GridItem(.fixed(100)),
        GridItem(.fixed(100)),
        GridItem(.fixed(100))
    ]

    var body: some View {

        ScrollView {
            LazyVGrid(columns: fixedColumn, spacing: 20) {
                ForEach(data, id: \.self) { item in
                    Text(String(item))
                        .frame(width: 100, height: 100, alignment: .center)
                        .background(Color.blue)
                        .cornerRadius(10)
                        .foregroundColor(.white)
                        .font(.title)
                }
            }
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

출력:

<img src="/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_2.png" />

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

여기에서는 각 그리드 항목이 고정된 크기를 가지는 LazyVGrid를 만들었습니다. 열의 크기와 외관을 사용자 지정하여 특정 요구 사항에 맞출 수 있습니다.

LazyHGrid

그리드 항목을 수평으로 표시하려면 LazyHGrid를 사용할 수 있습니다. 이는 LazyVGrid와 정확히 같이 작동하지만 화면에 먼저 행을 맞추려고 합니다. 다음은 반응형 LazyHGrid 뷰를 사용하는 예시입니다.

```js
import SwiftUI

구조체 ContentView: View {

    private var data = Array(1...20)
    private let gridRows = [
        GridItem(.adaptive(minimum: 150))

    ]
    var body: some View {

        ScrollView(.horizontal) {
            LazyHGrid(rows: gridRows, spacing: 20) {
                ForEach(data, id: \.self) { item in
                    Text(String(item))
                        .frame(width: 100, height: 100, alignment: .center)
                        .background(.blue)
                        .cornerRadius(10)
                        .foregroundColor(.white)
                        .font(.title)
                }
            }
        }
    }
}

#Preview {
    ContentView()
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

아래 출력 내용을 참고해주세요:

<img src="/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_3.png" />

앱을 실행하면, 컨텐츠를 처음에는 위에서 아래로 채우려고 하다가 모든 항목이 들어갈 수 없으면 수평 스크롤이 가능해집니다. ScrollView에서는 수평 스크롤을 가능하게 하려면 .horizontal modifier를 포함해야 합니다.

LazyVGrid와 같이 .fixed() 및 .flexible() modifier를 사용할 수 있습니다.

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

게으른 그리드 뷰는 SwiftUI가 그들을 표시해야 할 때에만 자식 뷰를 생성합니다. 따라서, 메모리 최적화에 좋습니다.

# 그리드 뷰

iOS 13에서 소개된 LazyVGrid 및 LazyHGrid입니다. iOS 16에서 Apple은 SwiftUI에서 Grid 뷰를 소개했습니다. 이제, 이미 Lazy Grid가 있기 때문에 그것이 불필요하거나 중복된 것으로 생각할 수도 있습니다. 그러나 Grid 뷰에 익숙해지면, 여러분의 인식이 바뀔 것입니다.

Grid 뷰는 자식 뷰를 2차원 레이아웃으로 정렬합니다. 이를 통해 뷰를 테이블과 유사한 구조로 정리할 수 있습니다. HTML 테이블이나 어떤 테이블에 익숙하다면, SwiftUI를 사용하여 동일한 결과를 얻을 수 있습니다. 셀이나 열을 병합하거나, 빈 셀을 만들거나, 셀 간격을 설정하거나, 정렬을 제어하는 등의 작업을 할 수 있습니다. Grid 뷰는 복잡한 UI 개발에 강력한 도구를 제공합니다. 배울 것이 많습니다. 시작해보죠!

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

다음은 GridView의 매우 기본적인 예제입니다:

```js
import SwiftUI

struct ContentView: View {
    var body: some View {

        Grid ( horizontalSpacing: 20, verticalSpacing: 20 ) {
            GridRow {
                Text("R1, C1")
                Text("R1, C2")
            }
            GridRow {
                Text("R2, C1")
                Text("R2, C2")
            }
        }
    }
}

#Preview {
    ContentView()
}
```

결과:

<img src="/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_4.png" />

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

가로 간격: 20, 세로 간격: 20으로 셀 간격을 사용했습니다.

그리드 열

그리드 뷰에 표시하려는 행 수를 제어하는 GridRow입니다. GridRow 내의 각 항목은 열 항목을 나타냅니다. 서로 다른 열 수로 행을 생성할 수 있습니다. 그렇게 하면 그리드가 행에 더 적은 열이있는 경우 자동으로 빈 셀을 행 끝에 추가합니다. 다음은 예시입니다:

```js
import SwiftUI

struct ContentView: View {
    var body: some View {

        Grid {
            GridRow {
                Text("행 1")
                ForEach(0..<3) { _ in Circle().foregroundColor(.red) }
            }
            GridRow {
                Text("행 2")
                ForEach(0..<5) { _ in Circle().foregroundColor(.green) }
            }
            GridRow {
                Text("행 3")
                ForEach(0..<4) { _ in Circle().foregroundColor(.mint) }
            }
        }
    }
}

#Preview {
    ContentView()
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

아래는 표 태그를 Markdown 형식으로 변경한 내용입니다.

<img src="/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_5.png" />

각 행이 다른 열의 수를 가질 때, 최대 열 수와 동일하게 맞추기 위해 빈 셀이 추가된 것을 관찰할 수 있습니다.

Grid 너비와 높이:

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

그리드의 너비와 높이는 해당 자식 뷰에 따라 증가합니다.

- 그리드는 열의 모든 셀의 너비를 가장 넓은 셀에 맞춥니다.
- 그리드는 특정 행의 가장 키가 큰 셀에 맞춰 전체 행의 높이를 설정합니다.

그리드 내부의 뷰 확장

GridRow 없이 그리드 뷰 내에 항목을 추가하면 전체 열을 확장합니다.

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
import SwiftUI

struct ContentView: View {
    var body: some View {

        Grid(horizontalSpacing: 20, verticalSpacing: 20) {
            GridRow {
                Text("R1, C1")
                Text("R1, C2")
            }
            Text("This will expand into full column").font(.title)
            GridRow {
                Text("R2, C1")
                Text("R2, C2")
            }
        }

    }
}

# Preview {
    ContentView()
}
```

결과:

<img src="/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_6.png" />

빈 셀

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

만약 셀을 건너뛰고 어떤 뷰도 보여주고 싶지 않다면, Color.clear.gridCellUnsizedAxes([.horizontal, .vertical])를 사용하여 빈 셀을 추가할 수 있어요.

여기 예시가 있어요:

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {

        Grid {
            GridRow {
                ForEach(0..<3) { _ in Circle().foregroundColor(.red) }
            }
            GridRow {
                Circle().foregroundColor(.green)
                Color.clear.gridCellUnsizedAxes([.horizontal, .vertical])
                Circle().foregroundColor(.green)
            }
            GridRow {
                ForEach(0..<3) { _ in Circle().foregroundColor(.mint) }
            }
        }
    }
}

#Preview {
    ContentView()
}
```

결과:

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

![Various kinds of GridView in SwiftUI - A Comprehensive Guide](/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_7.png)

병합된 셀

하나의 뷰가 두 개 이상의 열을 차지하도록 하려면 gridCellColumns(\_:) 수정자를 사용하여 지정할 수 있습니다.

```swift
import SwiftUI

struct ContentView: View {
    var body: some View {

        Grid {
            GridRow {
                ForEach(0..<3) { _ in Circle().foregroundColor(.red) }
            }
            GridRow {
                Circle().foregroundColor(.green)
                Text("2개의 셀 공간을 차지합니다")
                    .gridCellColumns(2)
                    .font(.title)
            }
            GridRow {
                ForEach(0..<3) { _ in Circle().foregroundColor(.mint) }
            }
        }

    }
}

#Preview {
    ContentView()
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

아래는 표 태그를 Markdown 형식으로 변경한 것입니다.

<img src="/assets/img/2024-06-23-VariousKindsofGridViewsinSwiftUIAComprehensiveGuide_8.png" />

그리드 뷰는 모든 자식 뷰를 한꺼번에 렌더링합니다. 앱의 성능을 높이려면 LazyHGrid나 LazyVGrid를 사용할 수 있습니다.
