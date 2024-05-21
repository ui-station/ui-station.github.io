---
title: "SwiftUI 웃기고 독특한 사용자 정의 얼굴 애니메이션 회전 효과"
description: ""
coverImage: "/assets/img/2024-05-20-SwiftUIFunnyCustomFaceAnimationwithrotationeffect_0.png"
date: 2024-05-20 16:11
ogImage: 
  url: /assets/img/2024-05-20-SwiftUIFunnyCustomFaceAnimationwithrotationeffect_0.png
tag: Tech
originalTitle: "[SwiftUI] Funny Custom Face Animation with rotation effect"
link: "https://medium.com/@ganeshrajugalla/swiftui-funny-custom-face-animation-with-rotation-effect-b8d48d4ac289"
---



![Funny Custom Face Animation](/assets/img/2024-05-20-SwiftUIFunnyCustomFaceAnimationwithrotationeffect_0.png)

저는 원형과 둥근 직사각형 같은 형태를 사용하여 사용자 정의 얼굴을 만들어보았어요. 눈이 회전하는 애니메이션도 추가했답니다.

```swift
struct FunnyFaceView: View {
    
    // MARK: - 속성
    @State private var isRotate: Bool = false
    @State private var rotationAngle: Double = 0
    
    // MARK: - Body
    var body: some View {
        ZStack {
            faceView()
            facePartsView()
        }
        .onAppear {
            withAnimation(Animation.linear(duration: 1).repeatForever(autoreverses: false)) {
                rotationAngle = 360
            }
        }
    }
}

extension FunnyFaceView {
    
    @ViewBuilder
    private func faceView() -> some View {
        Circle()
            .fill(Color.yellow)
            .frame(width: 300, height: 300)
    }
    
    @ViewBuilder
    private func facePartsView() -> some View {
        VStack {
            eyesView()
            mouthView()
        }
    }
    
    @ViewBuilder
    private func eyesView() -> some View {
        HStack {
            eyeView(offset: 10)
            eyeView(offset: -10)
        }
        .offset(y: -50)
    }
}

// MARK: - 도우미 뷰
extension FunnyFaceView {
    @ViewBuilder
    private func eyeView(offset: CGFloat) -> some View {
        ZStack {
            Circle()
                .fill(Color.white)
                .frame(width: 50, height: 50)
            Circle()
                .fill(Color.black)
                .frame(width: 10, height: 10)
                .offset(x: offset)
                .rotationEffect(.degrees(rotationAngle))
        }
        .onAppear {
            isRotate.toggle()
        }
    }
    
    @ViewBuilder
    private func mouthView() -> some View {
        RoundedRectangle(cornerRadius: 20, style: .continuous)
            .fill(Color.white)
            .frame(width: 150, height: 20)
            .offset(y: 50)
    }
}

#if DEBUG
#Preview {
    FunnyFaceView()
}
#endif
```

![Funny Custom Face Animation](https://miro.medium.com/v2/resize:fit:590/1*ZUIvBP0Uf6Me8MkBYtu0Sg.gif)


<div class="content-ad"></div>

감사합니다!