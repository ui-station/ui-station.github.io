---
title: "SwiftUI의 NavigationStack 사용 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowtousetheSwiftUIsNavigationStack_0.png"
date: 2024-06-23 23:37
ogImage: 
  url: /assets/img/2024-06-23-HowtousetheSwiftUIsNavigationStack_0.png
tag: Tech
originalTitle: "How to use the SwiftUI’s NavigationStack"
link: "https://medium.com/@fsamuelsmartins/how-to-use-the-swiftuis-navigationstack-79f32ada7c69"
---



![Navigation in SwiftUI](/assets/img/2024-06-23-HowtousetheSwiftUIsNavigationStack_0.png)

SwiftUI에서의 네비게이션은 복잡하고 동적인 사용자 인터페이스를 생성하는 강력한 도구입니다. 직관적인 구문과 강력한 기능 세트를 갖춘 SwiftUI의 네비게이션 기능을 사용하면 원활하고 매력적인 앱 경험을 구축하기 쉽습니다. 간단한 프로토 타입을 만들거나 완전한 애플리케이션을 구축하더라도 SwiftUI의 네비게이션 도구를 사용하면 완벽한 사용자 경험을 만들 수 있습니다.

네비게이션은 NavigationView를 사용하여 수행되었으나, iOS 16에서 사용 중단되었으며 새로운 두 개의 컨테이너인 NavigationStack와 NavigationSplitView로 분할되었으며, 각각의 컨테이너에는 새로운 기능이 추가되었습니다.

NavigationStack은 다음 네비게이션에 뷰를 설정하기 위해 사용되며, 새로운 뷰를 이전 뷰 위에 쌓아 올리고 항상 상위에 뷰를 하나 유지합니다.


<div class="content-ad"></div>

NavigationSplitView은 컬럼 기반 네비게이션을 만들어야 할 때 사용됩니다. 화면이 컬럼으로 나누어지며, 각 컬럼은 NavigationSplitView의 하위 뷰 중 하나입니다.

이 기사에서는 NavigationStack의 기본에 중점을 둘 것입니다.

## NavigationView를 NavigationStack으로 이관하기

이전에 언급한 대로, NavigationView는 두 개로 분리되었으므로 모든 구현이 이관될 수 있는 것은 아닙니다. NavigationView가 스택 네비게이션 스타일을 사용할 때에만 직접적인 이관이 가능합니다.

<div class="content-ad"></div>

위의 코드에서 볼 수 있듯이 .stack 함수를 사용하여 NavigationView를 사용했습니다.

``` js
// IOS 16 이전 (사용 중단).
struct ContentView: View {
 var body: some View {

  NavigationView { 
   VStack {
    NavigationLink("스크린 1입니다") {
     Text("스크린 1로 이동")
    }
    Spacer().frame(height: 10)
    NavigationLink("스크린 2입니다") {
     Text("스크린 2로 이동")
    }
   }
  }
  .navigationViewStyle(.stack)
 }
}
```

그래서 이 마이그레이션을 수행하려면 내비게이션 스타일 함수를 제거하고 NavigationView를 NavigationStack으로 변경하면 됩니다.

``` js
// IOS 16.
struct ContentView: View {
 var body: some View {

  NavigationStack { 
   VStack {
    NavigationLink("스크린 1입니다") {
     Text("스크린 1로 이동")
    }
    Spacer().frame(height: 10)
    NavigationLink("스크린 2입니다") {
     Text("스크린 2로 이동")
    }
   }
  }
 }
}
```

<div class="content-ad"></div>

# NavigationStack 새로운 기능

## 값 타입을 위한 NavigationDestination

새로운 네비게이션 시스템으로 코드가 더 깔끔해졌습니다. 이제는 NavigationLink 생성과 관계없이 목적지를 정의할 수 있습니다.

목적지를 정의하려면 NavigationLink의 상위 뷰에 navigationDestination() 함수를 추가해야 합니다. 이 함수에는 다음과 같은 매개변수가 있습니다:

<div class="content-ad"></div>

파라미터:

- Data: 이 대상과 일치하는 데이터의 유형입니다.
- Destination: 스택의 탐색 상태에 `data` 유형의 값이 포함된 경우 표시할 뷰를 정의하는 뷰 빌더입니다. 이 클로저는 하나의 인수를 사용하며, 그것은 표시할 데이터의 값입니다.

이전에 보았듯이, 파라미터 중 하나는 대상 데이터입니다. 이 정보를 navigationDestination()에 보내기 위해 NavigationLink에 값을 추가해야 합니다. 이 매개변수는 Hashable 프로토콜을 구현한 값만 수용합니다.

아래 예제에서는 각 링크마다 스크린 번호가 포함된 문자열이 NavigationLink의 값 매개변수로 전송됩니다. 그런 다음 링크 컨테이너는 navigationDestination을 구현했습니다. 이것은 문자열 유형의 어떤 값이라도 전달될 때 호출될 것입니다.

<div class="content-ad"></div>

```swift
struct ContentView: View {

 var body: some View {
  NavigationStack {
   VStack {
    NavigationLink("화면 1으로 이동", value: "1")
    Spacer().frame(height: 10)
    NavigationLink("화면 2로 이동", value: "2")
   }.navigationDestination(for: String.self) { value in
    Text("이것은 화면 번호 \(value)입니다")
   }
  }
 }
}
```

<img src="https://miro.medium.com/v2/resize:fit:784/1*GwLWWM6KAWYGQmaztzbqTw.gif" />

## 불리언 상태 변수에 대한 NavigationDestination

또 다른 navigation 대상을 구현하는 방법은 불리언 상태 변수를 관찰하는 것입니다. 따라서 observable state가 변경될 때마다 대상이 트리거됩니다.


<div class="content-ad"></div>

아래 예제에서는 showDetails 변수를 true로 변경하는 버튼이 있습니다. 이 변수는 navigationDestination에 의해 관찰되며, destination에는 변수를 false로 변경하는 버튼이 포함되어 있습니다. 변수가 true이면 대상 뷰가 나타나고, false이면 사라집니다.

```swift
struct ContentView: View {
 @State private var showDetails = false

 var body: some View {
  NavigationStack {
   VStack {
    Button("세부 정보 업데이트") {
     showDetails = true
    }
   }
   .navigationDestination(isPresented: $showDetails) {
    VStack {
     Text("세부 정보가 업데이트되었습니다")
     Button("닫기") {
      showDetails = false
     }
    }
   }
  }
 }
}
```

<div class="content-ad"></div>


![image](https://miro.medium.com/v2/resize:fit:784/1*mN4EwmCDiECEE6Iw_rKxBg.gif)

## NavigationStack 경로 매개변수

이 새로운 매개변수는 iOS 16에 도입된 최고의 새로운 기능 중 하나로, 딥링크 및 다른 특정 네비게이션 경우의 쉬운 구현을 가능하게 합니다.

기본적으로 이 매개변수는 우리에게 새로운 대상으로의 미리 정의된 경로를 정의하고, 경로에 쌓인 모든 화면을 추적할 수 있도록 해줍니다.


<div class="content-ad"></div>

아래 예시에서는 패스 변수를 가지고 있으며, 해당 변수는 네비게이션 스택의 경로 매개변수로 설정되어 있습니다. 이 목록의 각 항목은 프로그램 시작 시에 열리는 화면과 대응합니다.

```js
struct ContentView: View {

 @State var path: [String] = ["1", "2", "3"]

 var body: some View {
  NavigationStack(path: $path) {
   VStack {
    NavigationLink("Go to screen 4", value: "4")
   }.navigationDestination(for: String.self) { value in
    Text("This is screen number \(value)")
   }
  }
 }
}
```

![이미지](https://miro.medium.com/v2/resize:fit:784/1*UKAZP7Xn4iBP12FsyphWng.gif)

## NavigationPath

<div class="content-ad"></div>

이전 주제에서 NavigationStack의 변수 경로로 사용하기 위해 String 목록이 생성되었습니다. 그런데 새로운 화면이 다른 유형을 매개변수로받아야하는 경우에는 어떻게 해야하며 여전히 모든 탐색을 추적하고 싶다면 어떻게 해야합니까?

Apple은이 문제를 해결하기 위해 데이터의 타입을 지워버리는 NavigationPath라는 목록을 만들었습니다.

NavigationPath를 사용하는 방법을 더 잘 이해하기 위해 인물 모델을 만들겠습니다. NavigationLink의 값 매개변수와 마찬가지로 경로 목록에 추가될 모든 유형은 Hashable 프로토콜을 구현해야 합니다.

```js
struct PersonModel: Hashable {
  let name: String
  let age: Int
}
```

<div class="content-ad"></div>

그런 다음, 우리는 두 가지 시작 경로와 함께 문자열 유형을 가진 NavigationPath를 만듭니다.

그 후에, 새 경로가 NavigationStack의 경로 매개변수로 설정되고, NavigationPath의 유형 유연성을 테스트하기 위해 두 가지 새로운 NavigationsLinks를 만들 것입니다.

첫 번째 링크는 정수를 값 매개변수로 사용하고, 두 번째 링크는 생성된 person 모델을 값으로 사용합니다. 서로 다른 유형의 값을 추가한 후, 이들에 대한 특정 대상을 정의해야 합니다.

```js
struct ContentView: View {
 
 @State var path = NavigationPath(["1", "2"])
 
 var body: some View {
  NavigationStack(path: $path) {
   VStack {
    NavigationLink("정수 화면으로 이동", value: 1)
    Spacer().frame(height: 10)
    NavigationLink("사람 화면으로 이동", value: PersonModel(name: "Mark", age: 32))

   }.navigationDestination(for: String.self) { value in
    Text("이것은 값이 있는 문자열 화면입니다: \(value)")
   }.navigationDestination(for: Int.self) { value in
    Text("이것은 값이 있는 정수 화면입니다: \(value)")
   }.navigationDestination(for: PersonModel.self) { value in
    Text("이것은 값이 있는 사람 화면입니다.\n이름: \(value.name), 나이: \(value.age)")
   }
  }
 }
}
```

<div class="content-ad"></div>

<img src="https://miro.medium.com/v2/resize:fit:788/1*13DNutvSEBznr-r2hmWSlQ.gif" />

# 결론

여기까지 새로운 iOS 16 내비게이션 방식의 강력함을 확인했고, 이제 SwiftUI가 제공하는 네이티브 도구를 사용하여 앱의 자체 내비게이션 시스템을 쉽게 구축할 수 있게 되었습니다.

iOS 개발 관련 질문이 있거나 대화를 나누고 싶으시다면 언제든지 LinkedIn으로 메시지를 남겨주세요! 🙂