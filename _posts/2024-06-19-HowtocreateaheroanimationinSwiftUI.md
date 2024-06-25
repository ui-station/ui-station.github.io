---
title: "SwiftUI에서 히어로 애니메이션을 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtocreateaheroanimationinSwiftUI_0.png"
date: 2024-06-19 11:15
ogImage:
  url: /assets/img/2024-06-19-HowtocreateaheroanimationinSwiftUI_0.png
tag: Tech
originalTitle: "How to create a hero animation in SwiftUI?"
link: "https://medium.com/mobile-app-circular/how-to-create-a-hero-animation-in-swiftui-154c6c6980ef"
---

## matchedGeometryEffect를 사용하여 최초 인상을 남기세요

자신이 가장 좋아하는 앱의 디자인을 상상해보세요. 이 앱의 디자인이 여러분에게 특별한 이유는 무엇인가요? 저희에게는 종종 개별적이고 흥미로운 애니메이션이 그 앱의 디자인을 특별하게 만든다고 생각합니다. 이러한 애니메이션은 사용자의 주의를 이끄는 데 도움이 되며 사용자 흐름을 개선하고 사용자 여정을 더 생동감 있게 만들어 줍니다.

저희 눈길을 사로잡은 애니메이션 중 하나는 히어로 애니메이션인데, 아마도 iOS 앱 스토어에서 본 적이 있을 것입니다. 사용자가 카드를 클릭하면 카드가 전체 화면 크기로 확대되어 더 많은 세부 정보가 표시됩니다.

![hero animation](https://miro.medium.com/v2/resize:fit:1400/1*aSTfSWLDnjQlp7QIOzyaDQ.gif)

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

저희 앱 중 하나에서는 우리 자신의 디자인을 위해 이 애니메이션을 재현하고 싶었습니다. 이를 위해 modifier matchedGeometryEffect을 사용했습니다. 이 흥미로운 modifier을 사용하는 방법에 대해 약간의 통찰력을 제공하고자 합니다.

# 작은 코드 예제를 살펴봅시다

우리는 탭하면 확대되는 작은 직사각형이 있습니다. 이를 달성하기 위해 if-else 블록에 두 가지 다른 직사각형을 배치하고 그들 사이의 전환을 생성합니다.

![애니메이션](https://miro.medium.com/v2/resize:fit:1400/1*99ZmB-w_SJZVhhiPTLqFTQ.gif)

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

state 변수인 shouldAnimate에 따라 두 가지 모양 중 하나를 보여주는 뷰가 표시됩니다. withAnimation(_:_:) 함수에서는 뷰를 탭하는 제스처에 응답하여 이 상태를 전환합니다. 이렇게 함으로써 두 직사각형 간 전환할 때 전환 효과를 제공합니다.
핵심은 단순히 애니메이션만 사용하는 대신 matchedGeomeryEffect를 활용하여 두 모양 간 부드러운 전환을 이루는 것입니다.

```js
@Namespace var animation
@State private var shouldAnimate = false

var body: some View {
    VStack {
        if shouldAnimate {
            Rectangle()
              .matchedGeometryEffect(id: "shape", in: animation)
        } else {
            Rectangle()
              .matchedGeometryEffect(id: "shape", in: animation)
              .frame(width: 200, height: 200)
        }
    }
    .onTapGesture {
       withAnimation(.spring()) {
           shouldAnimate.toggle()
       }
    }
}
```

# matchedGeometryEffect — 뭐에요?

애플은 해당 수정자를 다음과 같이 정의합니다:

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

이것은, 수정자가 두 뷰의 크기와 위치를 동기화하여 하나가 사라지고 다른 하나가 나타날 때 두 뷰를 보간하고 두 뷰가 동일한 것처럼 보이게 합니다. 한 위치와 크기에서 다른 위치와 크기로 이동하는 것처럼 보입니다.

이 수정자를 사용하려면, 먼저 애니메이션을 그룹화할 네임스페이스를 정의해야 합니다:

```js
@Namespace var animation
```

다음으로, matchedGeometryEffect를 두 사각형에 적용하겠습니다. 여기서, 두 사각형에 동일한 id를 설정하여 서로 일치시킵니다. 그리고 이제 우리의 네임스페이스를 할당합니다. 추가로, 크기, 위치 또는 둘 다(기본값)에 대한 일치 여부를 정의하기 위해 매개변수 속성을 정의할 수 있습니다:

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

```js
.matchedGeometryEffect(id: "shape", in: animation)
```

두 개의 사각형에 modifier를 첨부한 후에, SwiftUI가 두 개의 사각형을 보이기 위해 보간(interpolate)합니다.

더 나아가기 전에, 이 modifier를 활용하여 원하는 효과를 얻기 위해 취해야 할 조치들을 개요로 살펴봅시다:

- 네임스페이스 정의
- 상태 변수 정의
- 정의된 상태 변수에 따라 두 개의 뷰를 구별할 수 있는 if-else 블록 구현
- 같은 id를 사용하여 matched 되어야 하는 뷰에 modifier를 첨부
- animation 블록 내에서 상태 변경하기

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

# 인상적인 히어로 애니메이션 구현

다음으로, 우리는 좀 더 복잡한 예제를 보여드릴 겁니다. 여기서는 카드 뷰가 세부 보기로 확장되는 것을 원합니다.

![image](https://miro.medium.com/v2/resize:fit:1400/1*6InR3hNEzNQFI4rJN-OqIQ.gif)

이를 달성하기 위해, 우리는 먼저 Card 및 CardDetail 뷰를 구현하고, 상태 변수인 isShowingDetail을 조건으로 하는 if-else 블록 내에 삽입했습니다.

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

다음으로, 우리는 네임스페이스를 만들고 Card와 CardDetail 뷰로 변수를 전달했습니다. Card 뷰 내에서 우리는 카드를 탭했을 때 바인딩을 true로 설정하기 위해 onTapGesture를 추가했는데, 이는 암시적 애니메이션으로 수행됩니다. CardDetail 뷰에서는 닫기 버튼을 탭했을 때 바인딩을 false로 설정했습니다.

```js
struct ContentView: View {
    @State private var isShowingDetail = false
    @Namespace var animation

    var body: some View {
        ZStack {
            if isShowingDetail {
                CardDetail(
                    isShowingDetail: $isShowingDetail,
                    animation: animation
                )
            } else {
                Card(
                    isShowingDetail: $isShowingDetail,
                    animation: animation
                )
            }
        }
}
```

디자인이 구현되고 matchedGeometryEffect에 필요한 모든 준비가 마쳐진 후에, 다음 단계는 해당 뷰에 이를 적용하는 것입니다. 하지만 먼저, 어떤 뷰들을 일치시키고 싶은지 생각해보는 것이 좋습니다:

- 이미지
- 배경
- 코너 반경과 모양
- 텍스트

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

개발 중에 우리는 Apple 문서에서 설명하지 않는 modifier의 독특한 특성을 발견했습니다. 이러한 특성에 대해 토론하고 우리의 경험을 공유하고 싶습니다.

## 1. Modifiers의 순서

SwiftUI에서 modifiers의 순서를 올바르게 설정하는 것이 매우 중요합니다. 이것은 matchedGeometryEffect에도 적용됩니다. 이것은 예를 들어 이미지를 일치시키는 동안 발생했습니다. 이미지에는 높이를 설정하기 위해 .frame() modifier가 연결되어 있습니다. 여기서 우리는 matchedGeometryEffect를 이 modifier 이전에 연결하는 것이 중요하다는 것을 배웠습니다. 그렇지 않으면 애니메이션이 엉망으로 보일 것입니다. 이것은 패딩이나 배경의 코너 반경과 같은 다른 modifiers에도 적용됩니다. 우리의 권장 사항은 항상 modifier를 첫 번째 위치에 연결하는 것입니다.

```swift
Image("header")
    .resizable()
    .matchedGeometryEffect(id: "image", in: animation, anchor: .top)
    .frame(height: 240)
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

## 2. 애니메이션을 자세히 정의하기

스택에 수정자를 추가하려고 했을 때 다음 문제를 마주쳤습니다. 요소들이 충분히 정의되지 않아 어색한 애니메이션이 발생할 수 있습니다. 이를 알아두면, 특정 요소에 대해 수정자를 설정하는 것이 정말 중요하다는 것을 알 수 있습니다. 예시로, 우리는 배경색을 수정자 '배경'으로 만들어 색에 직접 수정자를 적용했습니다.

```js
.background(
  Color.black
   .cornerRadius(0)
   .matchedGeometryEffect(id: AnimationId.imageBackgroundId, in: animation)
)
```

## 3. 텍스트 크기는 기본적으로 애니메이션화되지 않습니다.

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

위의 예시에서는 카드가 열릴 때 텍스트 크기가 더 큰 글꼴 크기로 변경됩니다. 이러한 애니메이션은 크기가 변경될 때 정확히 무엇을 해야 하는지 알아야 해서 더 많은 제어가 필요합니다. 그래서 우리는 matchedGeometryEffect를 animatable text와 결합해야 했습니다. 이를 위해 Hacking with Swift에서 AnimatableSystemFontModifier를 사용하여 전환 중 폰트 크기를 애니메이트했습니다.

게다가, 애니메이션 동안 자르기를 피하기 위해 폰트에 fixedSize()를 설정했습니다. 우리는 애니메이션을 할 때 AnimatableTitle을 사용하기 위해 구현했습니다. 더욱 중요한 것은 뷰가 나타난 후 텍스트 애니메이션을 시작하는 것입니다. isAppeared 변수는 isShowingDetail 상태를 반영하지만 view가 나타난 후에만 트리거됩니다. 이를 통해 더 부드러운 애니메이션을 만들 수 있습니다.

```swift
struct AnimatableTitle: View {
    let isAppeared: Bool

    var body: some View {
        Text("Learning: Do a\nSwiftUI tutorial")
            .animatableSystemFont(size: isAppeared ? 32 : 16, weight: .bold)
            .fixedSize(horizontal: false, vertical: true)
            .frame(maxWidth: .infinity, alignment: .leading)
            .lineLimit(2, reservesSpace: true)
    }
}

AnimatableTitle(isAppeared: isAppeared)
      .matchedGeometryEffect(id: AnimationId.titleId, in: animation)

.onAppear {
     withAnimation(.linear) {
          isAppeared = isShowingDetail
     }
}
```

## 4. 적절한 전환 스타일

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

모든 일치하는 뷰에 matchedGeometryEffect를 적용한 후, 전환 스타일을 .scale()로 설정합니다. SwiftUI에게 애니메이션을 혼합하는 것이 아니라 크기를 조절하도록 하기 위한 조치입니다. 이로써 큰 차이를 만들어냅니다!

```js
Card(animation: animation)
     .transition(.scale(scale: 1))
```

# 결론

matchedGeometryEffect는 한 뷰에서 다른 뷰로의 전환을 애니메이션화하는 강력한 수정자입니다. 이 수정자를 사용하면 멋진 히어로 애니메이션을 만들 수 있습니다. 그러나 이 수정자에는 Apple 문서에서 정의되지 않은 일부 특정 특성이 있습니다. 우리가 제공한 내용이 여러분의 고유한 히어로 애니메이션 구현에 도움이 되었으면 좋겠습니다.

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

우리 코드의 세부 사항에 관심이 더 있다면, GitHub를 확인해보세요:

matchedGeometryEffect를 사용하는 재미를 느껴보세요. 여러분의 히어로 애니메이션 버전이 어떻게 보이는지 기대됩니다! (Laura Siewert 작)
