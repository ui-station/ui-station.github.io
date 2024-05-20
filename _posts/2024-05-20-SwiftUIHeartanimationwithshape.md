---
title: "SwiftUI 모양으로 하는 심장 애니메이션"
description: ""
coverImage: "/assets/img/2024-05-20-SwiftUIHeartanimationwithshape_0.png"
date: 2024-05-20 16:14
ogImage: 
  url: /assets/img/2024-05-20-SwiftUIHeartanimationwithshape_0.png
tag: Tech
originalTitle: "[SwiftUI] Heart animation with shape"
link: "https://medium.com/@ganeshrajugalla/swiftui-heart-animation-with-shape-db2b2b5a5861"
---


```markdown
![Heart Shape](/assets/img/2024-05-20-SwiftUIHeartanimationwithshape_0.png)

사각형, 둥근 사각형, 원 및 캡슐은 SwiftUI에서 사용할 수 있지만 하트 모양은 제공되지 않으므로 저는 Shape Protocol을 사용하여 직접 만들었습니다.

# 하트 만들기

```swift
struct HeartShape: Shape {
    let minX = 10
    let centerX = 55
    let maxX = 100
    let minY = 10
    let maxY = 100
    
    func path(in rect: CGRect) -> Path {
        Path { path in
            path.move(to: CGPoint(x: centerX, y: maxY))
            path.addQuadCurve(to: CGPoint(x: minX, y: 50), control: CGPoint(x: minX, y: 70))
            path.addQuadCurve(to: CGPoint(x: centerX, y: 30), control: CGPoint(x: minX, y: minY))
            path.addQuadCurve(to: CGPoint(x: maxX, y: 50), control: CGPoint(x: maxX, y: minY))
            path.addQuadCurve(to: CGPoint(x: centerX, y: maxY), control: CGPoint(x: maxX, y: 70))
            path.closeSubpath()
        }
    }
}
```
```

<div class="content-ad"></div>

이곳에서 도형에 대해 더 많이 배워보세요

# 애니메이션 추가

```js
import SwiftUI

struct HeartAnimation: View {

    // MARK: - Properties
    @State private var to:CGFloat = 0

    // MARK: - Body
    var body: some View {
        HeartShape()
            .trim(from: 0, to: to)
            .stroke(Color.pink, style: StrokeStyle(lineWidth: 5, lineCap: .round))
            .frame(width: 110, height: 110)
            .onAppear {
                withAnimation(
                    Animation
                        .easeInOut(duration: 0.7)
                        .repeatForever(autoreverses: false)) {
                            to = 1
                        }
            }
    }
}
```

![이동](https://miro.medium.com/v2/resize:fit:590/1*_RXEvVkvqCafU2ke7Fe17Q.gif)

<div class="content-ad"></div>

감사합니다!