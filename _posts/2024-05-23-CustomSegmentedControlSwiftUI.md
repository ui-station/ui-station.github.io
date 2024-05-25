---
title: "사용자 정의 Segmented Control  SwiftUI"
description: ""
coverImage: "/assets/img/2024-05-23-CustomSegmentedControlSwiftUI_0.png"
date: 2024-05-23 13:08
ogImage: 
  url: /assets/img/2024-05-23-CustomSegmentedControlSwiftUI_0.png
tag: Tech
originalTitle: "Custom Segmented Control — SwiftUI"
link: "https://medium.com/kocsistem/custom-segmented-control-swiftui-3d785d1b530f"
---


이 기사에서는 SwiftUI 프레임워크 내에서 사용자 정의 디자인을 적용한 Segmented Control 예제를 찾을 수 있습니다. 이 방법은 애플리케이션에 특별히 디자인된 Segmented Control을 사용자화합니다.

![Custom Segmented Control Example](/assets/img/2024-05-23-CustomSegmentedControlSwiftUI_0.png)

# Segmented Controls

애플 사전적 정의에 따르면 Segmented Control은 두 개 이상의 세그먼트로 구성된 일련의 요소로, 각각 버튼의 역할을 합니다. Segmented Control 내에서 모든 세그먼트는 일반적으로 동일한 너비를 갖고 있습니다. 버튼과 같이 세그먼트에는 텍스트나 이미지가 포함될 수 있습니다. 세그먼트 아래에 텍스트 레이블을 가질 수도 있습니다(또는 제어 전체 아래에). Segmented Control은 단일 선택 또는 다중 선택을 제공할 수 있습니다. (1).

<div class="content-ad"></div>

SwiftUI 라이브러리에서는 Segmented, UIKit 프레임워크에서는 UISegmentedControl라고 해요.

# 사용자 정의 세그먼트 컨트롤

앱의 테마에 맞게 디자인하려면 아래 방법을 따라주세요.

이번에는 두 개의 요소로 구성된 세그먼트 컨트롤을 디자인하겠습니다. 이를 위해 0과 1로 구성된 enum 타입으로 관리할 거에요.

<div class="content-ad"></div>

```js
// 0 : 카메라
// 1 : 사진 라이브러리
```

아래는 디자인 출력입니다. 그에 따라 개발되었습니다. ⬇️

<img src="/assets/img/2024-05-23-CustomSegmentedControlSwiftUI_1.png" />

```js
import Foundation
import SwiftUI

struct CustomSegmentedControl: View {
    @Binding var preselectedIndex: Int
    var options: [String]
    // 이 색상은 테마 라이브러리에서 가져온 것입니다
    let color = ThemeManager.shared.currentTheme.currentPallet.secondary

    var body: some View {
        HStack(spacing: 0) {
            ForEach(options.indices, id:\.self) { index in
                ZStack {
                    Rectangle()
                        .fill(color.opacity(0.2))

                    Rectangle()
                        .fill(color)
                        .cornerRadius(20)
                        .padding(2)
                        .opacity(preselectedIndex == index ? 1 : 0.01)
                        .onTapGesture {
                            withAnimation(.interactiveSpring()) {
                                preselectedIndex = index
                            }
                        }
                }
                .overlay(
                    Text(options[index])
                )
            }
        }
        .frame(height: 50)
        .cornerRadius(20)
    }
}
```

<div class="content-ad"></div>

Custom Segmented Control 클래스는 두 개의 매개변수를 사용합니다. 첫 번째 매개변수는 현재 선택된 아이템을 나타내고, 두 번째 매개변수는 아이템 배열 문자열로 사용됩니다. 이 배열은 상수로 정의할 수 있습니다.

중요한 점은 첫 번째 매개변수가 @Binding으로 표시되어야 한다는 것입니다.

이 매개변수는 @state로 유지하고 sourcetype과 함께 뷰로 전송됩니다.

제스처와 함께 작동하는 작은 애니메이션이 있는 코드가 있습니다.

<div class="content-ad"></div>

```markdown
VStack{
     CustomSegmentedControl(preselectedIndex: $selectedSegmentSourceType, 
        options: [Localization.value("photos.title"), Localization.value("camera.title")])
}
```

또한, 컨트롤에서 변경한 값은 뷰에서 sourcetype 변수와 함께 만들어집니다.

이 변수를 통해 선택 상태를 읽을 수 있습니다.

다음 글에서 이 세그먼트 컨트롤과 피커를 함께 사용할 예정이에요. ❤️‍🔥
```

<div class="content-ad"></div>

# 자료들