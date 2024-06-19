---
title: "SwiftUI에서 여러 개의 경고를 표시하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtoshowMultipleAlertsinSwiftUI_0.png"
date: 2024-06-19 14:14
ogImage: 
  url: /assets/img/2024-06-19-HowtoshowMultipleAlertsinSwiftUI_0.png
tag: Tech
originalTitle: "How to show Multiple Alerts in SwiftUI"
link: "https://medium.com/gitconnected/how-to-show-multiple-alerts-in-swiftui-252f4528ad90"
---


## SwiftUI에서 여러 경고를 표시하는 것은 조금 까다로울 수 있어요. 이 두 가지 방법을 소개해 드릴게요.

![이미지](/assets/img/2024-06-19-HowtoshowMultipleAlertsinSwiftUI_0.png)

SwiftUI에서 여러 경고를 표시하는 것은 귀찮을 수 있어요. 그러나, 하나의 경고를 표시하는 것은 꽤 간단해요. 먼저 하나의 경고를 표시하는 방법을 알아보죠.

# 단일 경고

<div class="content-ad"></div>

어플리케이션이 있어요. 사용자가 10진수를 입력하면, 해당 입력이 짝수일 경우 경고 메시지가 표시됩니다.

```js
struct ContentView: View {
    @State private var showEvenAlert: Bool = false
    @State private var num = 2
    
    var body: some View {
        VStack {
             TextField("숫자를 입력하세요: ", value: $num, format: .number)
                .textFieldStyle(.roundedBorder)
                .frame(maxWidth: 150)
            Button("숫자가 짝수일 때 경고 표시") {
                showEvenAlert = num % 2 == 0
            }
            .alert(isPresented: $showEvenAlert, content: {
                Alert(title: Text("짝수"), message: Text("\(num)은(는) 짝수입니다"), dismissButton: .cancel() )
            })
        }
    }
}
```

정상적으로 작동합니다. 사용자가 짝수를 입력하면 경고 메시지가 표시됩니다. 

<img src="https://miro.medium.com/v2/resize:fit:1200/1*uA2Shsj_0WoDUVHLylsHvw.gif" />

<div class="content-ad"></div>

# 다중 알림

우리는 숫자가 홀수일 때도 알림을 표시하고 싶어요. 홀수는 외롭게 남겨두고 싶지 않아요. 그러니까 첫 번째 알림 아래에 추가 알림을 넣으면 작동할까요?

```js
struct ContentView: View {
    @State private var showEvenAlert:Bool = false
    @State private var showOddAlert:Bool = false
    @State private var num = 2
    
    var body: some View {
        VStack {
            TextField("숫자를 입력하세요: ", value: $num, format:.number)
                .textFieldStyle(.roundedBorder)
                .frame(maxWidth: 150)
            Button("숫자가 홀수인지 짝수인지 확인") {
                showEvenAlert = num % 2 == 0
                showOddAlert = num % 2 == 1
            }
            .alert(isPresented: $showEvenAlert, content: {
                Alert(title: Text("짝수"), message: Text("\(num)은(는) 짝수입니다"), dismissButton: .cancel() )
            })
            .alert(isPresented: $showOddAlert, content: {
                Alert(title: Text("홀수"), message: Text("\(num)은(는) 홀수입니다"), dismissButton: .cancel() )
            })
            
        }
    }
}
```

안타깝게도 그 답은 아니에요. 위의 코드는 작동하지 않을 거예요. 홀수에 대한 알림은 표시되지만 짝수에 대한 알림은 나타나지 않을 거에요.

<div class="content-ad"></div>

```markdown
![image](https://miro.medium.com/v2/resize:fit:1200/1*JtKMxr1kTj-vWjPfBfa0bw.gif)

이 무엇이 일어나고 있는지 이해해 봅시다. 뷰의 가장 바깥 쪽 알람만 작동합니다. 그래서 홀수 번째 알람은 작동하지만 짝수 번째는 작동하지 않습니다. 의왕, 컴파일러는 심지어 경고도 표시하지 않습니다. 그렇다면 해결책은 무엇일까요?

# 해결책 1:

한 알람을 여러 뷰에 할당할 수 있습니다. 그래서 단일 뷰에 한 개 이상의 알람이 없습니다.
```

<div class="content-ad"></div>

```markdown
```swift
struct ContentView: View {
    @State private var showEvenAlert: Bool = false
    @State private var showOddAlert: Bool = false
    @State private var num = 2
    
    var body: some View {
        VStack {
            TextField("Enter number: ", value: $num, format: .number)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .frame(maxWidth: 150)
                .alert(isPresented: $showOddAlert) {
                    Alert(title: Text("Odd"), message: Text("\(num) is odd"), dismissButton: .cancel())
                }
            
            Button("Show Alert if number is odd or even") {
                showEvenAlert = num % 2 == 0
                showOddAlert = num % 2 == 1
            }
            .alert(isPresented: $showEvenAlert) {
                Alert(title: Text("Even"), message: Text("\(num) is even"), dismissButton: .cancel())
            }
        }
    }
}
```

이렇게 수정하면 의도한 대로 작동할 것입니다. 하지만, 이 코드가 조금 어수선해 보입니다. 더 많은 알림이 있다면 어떻게 될까요? 코드가 더 어지러워질 것입니다.

<img src="https://miro.medium.com/v2/resize:fit:1188/1*QoWoOOrtm-jHFuuIjTYEag.gif" />

# 해결책 2:
```

<div class="content-ad"></div>

문제를 해결하는 한 가지 방법은 열거형(enum)을 사용하는 것이에요. 모든 종류의 알림에 대해 하나의 case를 만들고 열거형(enum)의 연관 값(associated value)을 사용하여 알림의 제목과 메시지를 나타낼 수 있어요.

```js
enum AlertContent {
    case even(num:Int), odd(num:Int)
    
    var title:String {
        switch self {
        case .even:
            "짝수"
        case .odd:
            "홀수"
        }
    }
    
    var message:String {
        switch self {
        case .even(let num):
            "\(num)은(는) 짝수에요"
        case .odd(let num):
            "\(num)은(는) 홀수에요"
        }
    }
}
```

이후에 뷰 코드도 훨씬 간단해질 거예요. 더 이상 여러 알림과 바인딩 변수가 필요하지 않아요. 하나의 알림, 하나의 바인딩 변수, 그리고 AlertContent 변수만 있으면 돼요. 알림 콘텐츠의 형식을 변경하고 알림을 표시할 수 있어요.

```js
struct ContentView: View {
    @State private var showAlert:Bool = false
    @State private var num = 2
    @State private var alertContent:AlertContent = .even(num: 2)
    
    var body: some View {
        VStack {
            TextField("숫자를 입력하세요: ", value: $num, format:.number)
                .textFieldStyle(.roundedBorder)
                .frame(maxWidth: 150)
            Button("숫자가 홀수이거나 짝수이면 알림 표시") {
                alertContent = (num % 2 == 0) ? .even(num: num) : .odd(num: num)
                showAlert = true
            }
            .alert(isPresented: $showAlert, content: {
                Alert(title: Text(alertContent.title), message: Text(alertContent.message), dismissButton: .cancel() )
            })
        }
    }
}
```

<div class="content-ad"></div>

한 화면에 필요한 경고를 여러 개 보여야 할 때도 하나의 경고창으로 처리할 수 있습니다. 더 많은 경고를 위해 enum 케이스를 더 추가해야 할 뿐입니다. 이상적으로는 버튼 누름 로직은 다른 곳으로 이동하여, 필요한 경고의 수에 관계없이 뷰가 변하지 않습니다. GitHub에서 해당 프로젝트를 확인할 수 있습니다.

# 함께 소통해요!

- LinkedIn
- Twitter
- GitHub