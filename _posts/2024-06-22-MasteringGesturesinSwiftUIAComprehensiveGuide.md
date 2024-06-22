---
title: "SwiftUI에서 제스처 완벽하게 활용하는 방법 종합 가이드"
description: ""
coverImage: "/assets/img/2024-06-22-MasteringGesturesinSwiftUIAComprehensiveGuide_0.png"
date: 2024-06-22 23:09
ogImage: 
  url: /assets/img/2024-06-22-MasteringGesturesinSwiftUIAComprehensiveGuide_0.png
tag: Tech
originalTitle: "Mastering Gestures in SwiftUI: A Comprehensive Guide"
link: "https://medium.com/@elamir/mastering-gestures-in-swiftui-a-comprehensive-guide-08f58114ed38"
---


앱이라는 힘을 상상해보세요. 각 탭, 스와이프 및 핀치로 UI를 생동감 있게 만들어보세요.

![이미지](/assets/img/2024-06-22-MasteringGesturesinSwiftUIAComprehensiveGuide_0.png)

직관적인 제스처의 마법은 정적인 인터페이스를 동적이고 매력적인 경험으로 변화시킬 수 있습니다. 사용자들이 사랑하는 경험으로 만들어 주죠. 사용자가 쉽게 줌 인/아웃 및 패닝을 할 수 있는 사진 편집기, 창의성이 모든 드래그로부터 흘러나오는 그림 앱, 반응형 스와이프 제어가 있는 게임을 만드는 중이라면 SwiftUI에서 제스처를 마스터하는 것이 이런 가능성을 해제하는 열쇠입니다.

이 포괄적인 가이드에서 기초부터 고급 기술까지 안내해 드릴 것이며, SwiftUI의 제스처 인식기를 활용하여 매끄럽고 상호작용적인 사용자 경험을 만드는 방법을 공개할 것입니다. 실용적인 예제에 대해 깊이 파헤치고, 현실 세계의 사용 사례를 소개하며, 제스처 충돌을 처리하고 접근성을 보장하는 전문 팁을 제공할 것입니다. 이 여정을 마치면 SwiftUI 프로젝트에서 제스처의 모든 잠재력을 활용할 수 있는 준비가 되어 있을 것입니다.

<div class="content-ad"></div>

SwiftUI의 제스처에 대한 소개

SwiftUI에서 제스처는 탭, 스와이프 및 드래그와 같은 사용자 상호 작용을 감지하고 응답하는 데 사용됩니다. SwiftUI에는 기본 제스처 인식기가 제공되어 앱에 이러한 상호 작용을 손쉽게 통합할 수 있습니다.

기본 제스처 인식기

SwiftUI에는 여러 내장 제스처가 포함되어 있습니다:

<div class="content-ad"></div>

- 탭 제스처: 탭 상호작용을 감지합니다.
- 롱 프레스 제스처: 롱 프레스 상호작용을 감지합니다.
- 드래그 제스처: 드래깅 상호작용을 감지합니다.

탭 제스처 예제

<div class="content-ad"></div>

```swift
import SwiftUI

struct TapGestureView: View {
    @State private var message = "어디서나 탭하세요"

    var body: some View {
        Text(message)
            .padding()
            .background(Color.yellow)
            .onTapGesture {
                message = "탭되었습니다!"
            }
    }
}
```

이 예제에서 텍스트를 탭하면 메시지가 "탭되었습니다!"로 변경됩니다.

LongPressGesture 예제

```swift
import SwiftUI

struct LongPressGestureView: View {
    @State private var message = "길게 눌러 변경하세요"

    var body: some View {
        Text(message)
            .padding()
            .background(Color.green)
            .onLongPressGesture {
                message = "길게 눌렀습니다!"
            }
    }
}
```

<div class="content-ad"></div>

여기서 메시지를 "길게 눌렀습니다!"로 변경합니다.

드래그 제스처 예제

```swift
import SwiftUI

struct DragGestureView: View {
    @State private var offset = CGSize.zero

    var body: some View {
        Text("드래그해 보세요")
            .padding()
            .background(Color.blue)
            .offset(offset)
            .gesture(
                DragGesture()
                    .onChanged { gesture in
                        offset = gesture.translation
                    }
                    .onEnded { _ in
                        offset = .zero
                    }
            )
    }
}
```

텍스트를 드래그하여 화면 상에서 이동하고 놓을 때 위치가 재설정됩니다.

<div class="content-ad"></div>

2. 고급 제스처 기술

동시 제스처 인식

동시에 여러 제스처를 처리하면 더 다이내믹한 상호작용을 만들 수 있어요.

확대/축소(Pinch-to-Zoom) 및 이동(Pan) 예제

<div class="content-ad"></div>

```swift
import SwiftUI

struct PinchAndPanView: View {
    @State private var scale: CGFloat = 1.0
    @State private var offset = CGSize.zero

    var body: some View {
        Image(systemName: "photo")
            .resizable()
            .scaledToFit()
            .scaleEffect(scale)
            .offset(offset)
            .gesture(
                SimultaneousGesture(
                    MagnificationGesture()
                        .onChanged { value in
                            scale = value
                        },
                    DragGesture()
                        .onChanged { value in
                            offset = value.translation
                        }
                )
            )
    }
}
```

이 예제는 사용자가 이미지를 확대 및 축소하고 동시에 이동할 수 있도록 합니다.

사용자 정의 제스처 인식기

사용자 지정 제스처를 만들면 앱에 특정한 독특한 상호작용을 추가할 수 있습니다.


<div class="content-ad"></div>

```swift
import SwiftUI

struct CustomGestureView: View {
    @State private var message = "오른쪽으로 스와이프하여 나타납니다"

    var body: some View {
        Text(message)
            .padding()
            .background(Color.orange)
            .gesture(
                DragGesture(minimumDistance: 50)
                    .onEnded { value in
                        if value.translation.width > 50 {
                            message = "나타났습니다!"
                        }
                    }
            )
    }
}
```

이 사용자 정의 제스처에서 오른쪽으로 스와이핑하면 메시지가 나타납니다.

제스처 수정자

여러 제스처 인식기를 연결하면 복잡한 상호 작용을 만들 수 있습니다.


<div class="content-ad"></div>

길게 누른 후 스와이프

```js
import SwiftUI

struct LongPressAndSwipeView: View {
    @State private var message = "길게 누르고 스와이프하세요"

    var body: some View {
        Text(message)
            .padding()
            .background(Color.purple)
            .gesture(
                LongPressGesture(minimumDuration: 1)
                    .onEnded { _ in
                        message = "이제 스와이프하세요"
                    }
                    .simultaneously(
                        with: DragGesture(minimumDistance: 50)
                            .onEnded { value in
                                if value.translation.width > 50 {
                                    message = "스와이프했어요!"
                                }
                            }
                    )
            )
    }
}
```

이 예제는 메시지를 변경하려면 길게 누른 후에 스와이프해야 합니다.

3. 실용적인 예제 및 사용 사례

<div class="content-ad"></div>

사진 편집 앱: 드래그 제스처로 조정하기

```js
import SwiftUI

struct PhotoEditingView: View {
    @State private var brightness: Double = 0.0

    var body: some View {
        VStack {
            Image(systemName: "photo")
                .resizable()
                .scaledToFit()
                .brightness(brightness)
                .gesture(
                    DragGesture()
                        .onChanged { value in
                            brightness = Double(value.translation.height / 100)
                        }
                )
            Text("드래그하여 밝기 조정")
        }
    }
}
```

위아래로 드래그하여 이미지의 밝기를 조정할 수 있어요.

그림 그리기 앱: 드래그 제스처로 선 그리기

<div class="content-ad"></div>

```js
import SwiftUI

struct DrawingView: View {
    @State private var points: [CGPoint] = []

    var body: some View {
        Canvas { context, size in
            for point in points {
                context.fill(Path(ellipseIn: CGRect(origin: point, size: CGSize(width: 5, height: 5))), with: .color(.black))
            }
        }
        .gesture(
            DragGesture()
                .onChanged { value in
                    points.append(value.location)
                }
                .onEnded { _ in
                    // 선택적으로 제스처 종료 처리
                }
        )
    }
}
```

이 예제는 사용자가 화면을 드래그하여 선을 그릴 수 있도록 합니다.

게임 앱: 스와이프로 이동

```js
import SwiftUI

struct GamingView: View {
    @State private var position = CGSize.zero

    var body: some View {
        Circle()
            .frame(width: 50, height: 50)
            .offset(position)
            .gesture(
                DragGesture(minimumDistance: 50)
                    .onEnded { value in
                        if abs(value.translation.width) > abs(value.translation.height) {
                            if value.translation.width > 0 {
                                position.width += 50
                            } else {
                                position.width -= 50
                            }
                        } else {
                            if value.translation.height > 0 {
                                position.height += 50
                            } else {
                                position.height -= 50
                            }
                        }
                    }
            )
    }
}
```

<div class="content-ad"></div>

스와이핑은 스와이프 방향으로 원을 이동시킵니다.

4. 프로 팁 및 고려 사항

제스처 충돌 처리

여러 제스처가 동시에 인식될 수 있는 경우, 제스처를 우선순위를 정하거나 배타적 제스처 인식을 사용하세요.

<div class="content-ad"></div>

```swift
import SwiftUI

struct GestureConflictView: View {
    @State private var message = "탭 또는 길게 누르기"

    var body: some View {
        Text(message)
            .padding()
            .background(Color.gray)
            .gesture(
                TapGesture()
                    .onEnded {
                        message = "탭!"
                    }
                    .exclusively(
                        before: LongPressGesture()
                            .onEnded { _ in
                                message = "길게 눌렀어요!"
                            }
                    )
            )
    }
}
```

이 예제에서는 탭 제스처가 길게 누르기 제스처보다 우선합니다.

접근성 고려사항

보이스오버와 같은 보조 기술을 사용하는 데 제스처를 사용할 수 있도록 보장하세요. 예를 들어, 버튼이나 다른 UI 요소를 통해 작업을 수행할 수 있는 대안 방법을 제공하세요.

<div class="content-ad"></div>

SwiftUI에서 제스처를 마스터하는 것은 상호작용적이고 매력적인 앱을 만들기 위해 꼭 필요합니다. 다양한 제스처 인식기를 이해하고 구현함으로써 사용자 경험을 향상시키고 동적 상호작용을 만들 수 있습니다. 이러한 예제들을 실험해보고 추가 제스처 인식기와 서드파티 라이브러리를 탐구하여 SwiftUI 프로젝트를 더욱 발전시켜 보세요.