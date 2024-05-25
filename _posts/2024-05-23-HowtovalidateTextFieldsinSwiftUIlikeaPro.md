---
title: "SwiftUI에서 TextField를 전문가처럼 유효성 검사하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-HowtovalidateTextFieldsinSwiftUIlikeaPro_0.png"
date: 2024-05-23 13:19
ogImage: 
  url: /assets/img/2024-05-23-HowtovalidateTextFieldsinSwiftUIlikeaPro_0.png
tag: Tech
originalTitle: "How to validate TextFields in SwiftUI like a Pro"
link: "https://medium.com/@mhmtkrnlk/how-to-validate-textfields-in-swiftui-like-a-pro-3dbe368d1570"
---


<img src="/assets/img/2024-05-23-HowtovalidateTextFieldsinSwiftUIlikeaPro_0.png" />

안녕하세요! 오늘은 여러분께, 널리 확장된 로직을 사용하지 않고 모든 텍스트 필드를 한꺼번에 유효성을 검사하는 방법을 보여드리려고 합니다. 이번에는 텍스트 필드의 유효성을 검사하는 나쁜 방법과 좋은 방법을 구분하는 것을 목표로 하고 있어요.

먼저, 예제를 아주 간단하게 유지할 거에요.

<div class="content-ad"></div>

세 개의 텍스트 필드와 사용자 입력을 저장하는 비밀번호를 위한 한 개의 보안 필드가 있는 화면이 있다고 가정해봅시다. 이 숫자는 간단히 유지하기 위한 것이며, 실제 상황에서는 10개가 될 수도 있습니다.

```js
struct ContentView: View {
   @State var email : String = ""
   @State var password : String = ""
   @State var name : String = ""
   @State var surname : String = ""
    var body: some View {
          VStack {
             TextField("이메일", text: $email)
             TextField("이름", text: $name)
             TextField("성", text: $surname)
             SecureField("비밀번호", text: $password)
             Spacer().frame(height: 300)
             Button {


             } label: {
                Text("여기를 눌러보세요!")
                   .padding(.all)
                   .background(.red)
                   .cornerRadius(16)
             }
          }
          .padding()
       }
} 
```

이제 나쁜 방식과 좋은 방식을 구분하여 느껴보겠습니다.

#나쁜 방식

<div class="content-ad"></div>

```js
Button {
    // 먼저 원하는 방식으로 유효성을 확인합니다.
    if email.count > 6 && password.count > 12
            && !name.isEmpty && !surname.isEmpty {
        // 네트워킹 호출 수행!
        // 이것은 영원히 계속됩니다.
        // 읽기가 어렵고 확장 가능하지 않으며 재사용 가능하지 않습니다.
    } else {
        // 얼럿 팝업 등을 표시합니다.
    }

} label: {
    Text("여기를 탭하여 시도해보세요!")
        .padding(.all)
        .background(.red)
        .cornerRadius(16)
}
```

# 좋은 방법

이제 진짜 작업이 시작되었지만, 먼저 이 솔루션을 사용하기 위해 SwiftUI의 일부 주요 기능을 알아야 합니다.
```

<div class="content-ad"></div>

**PreferenceKey**

PreferenceKey 유형은 자식 뷰에서 부모 뷰로 뷰 트리를 업데이트하는 데이터 흐름을 처리하는 솔루션인데, 어떤 종류의 위임이나 생성자 핸들러를 사용하지 않고도 작업할 수 있습니다. 제가 본 바로는 널리 사용되지 않지만, 정말 복잡하지는 않습니다. 어느 정도로 보면, @EnvironmentObject의 역이라고 할 수 있습니다.

PreferenceKey는 이미 NavigationView의 타이틀이나 TabViews 선택 및 자식의 id 등에 사용되고 있습니다.

우리가 어떻게 사용자 정의 preference key를 정의할 수 있는지 살펴봅시다.

<div class="content-ad"></div>

```js
구조체 ValidationPreferenceKey : PreferenceKey {
   static var defaultValue: [Bool] = []

   static func reduce(value: inout [Bool], nextValue: () -> [Bool]) {
      value += nextValue()
   }
}
```

이것은 사용자 지정 PreferenceKey를 정의하는 구문입니다. 이 특정 프로토콜을 준수하기 위해 두 가지를 구현해야 합니다.

```js
static var defaultValue: [Bool] = []
```

이 기본 값은 Equatable을 준수하는 한 어떤 것이든 될 수 있습니다.

<div class="content-ad"></div>

```markdown
```js
static func reduce(value: inout [Bool], nextValue: () -> [Bool]) {
      value += nextValue()
   }
```

우리는 이 줄이 키가 등록된 키의 여러 반복에서 무엇을 해야 하는지를 정의하는 리덕션 함수를 구현해야 합니다.

예를 들어, TabView가 있고 그 안에 여러 개의 하위 뷰가 있고 모두 .id() 수정자를 가지고 있다고 하면, 이 reduce 함수는 모든 id 수정자 값에 사용됩니다.

TextField에서 이를 사용하려면 두 단계가 필요합니다
```

<div class="content-ad"></div>

1- 뷰 수정자

2- 익스텐션

이제 이를 위해 ViewModifier를 어떻게 구현하는지 보여드릴게요

```js
struct ValidationModifier : ViewModifier  {
   let validation : () -> Bool
   func body(content: Content) -> some View {
         content
            .preference(
               key: ValidationPreferenceKey.self,
               value: [validation()]
            )
      }
   }
```

<div class="content-ad"></div>

이 수정자는 그 특정 뷰에 해당하는 키를 등록합니다.

```js
let validation: () -> Bool
```

이 변수는 우리가 유효성 검사에 사용할 로직을 보유할 것입니다.

그리고 이 확장은 이를 일부 특정 유형의 뷰에 대해서만 적용합니다. TextFields 및 SecureFields와 같은 뷰입니다.

<div class="content-ad"></div>

```swift
extension TextField {
   func validate(_ flag: @escaping () -> Bool) -> some View {
      self
         .modifier(ValidationModifier(validation: flag))
   }
}

extension SecureField {
   func validate(_ flag: @escaping () -> Bool) -> some View {
      self
         .modifier(ValidationModifier(validation: flag))
   }
}
```

여기서 중요한 두 가지 아쉬움이 있습니다. 첫째로, 우리는 TextFields 텍스트를 노출할 수 없습니다. 이것은 이니셜라이저에서만 사용 가능하며 Apple에 의해 노출되지 않습니다.

둘째로, TextField와 SecureField는 View가 아닌 동일한 형식에서 상속되지 않기 때문에 이것을 단일 확장으로 줄일 수 없습니다. 해결 방법이 있다면 알려주세요!

이 modifier를 다른 뷰에서 사용할 수 없도록 유지하려면 동일한 파일에서 modifier와 확장을 정의하고 modifier를 private으로 만들 수 있습니다.
```

<div class="content-ad"></div>

마지막 부분 빼고는 설정이 완료되었어요.

마지막으로, 이 키의 업무를 처리할 뷰를 정의해야 해요.

```js
struct TextFormView<Content : View> : View {
   @State var validationSeeds : [Bool] = []
   @ViewBuilder var content : (( @escaping () -> Bool)) -> Content
   var body: some View {
         content(validate)
         .onPreferenceChange(ValidationPreferenceKey.self) { value in
            validationSeeds = value
         }
   }

   private func validate() -> Bool {
      for seed in validationSeeds {
         if !seed { return false}
      }
      return true
   }
}
```

이 컨테이너 뷰는 이 키 타입에서 로직을 실행하고, Vstack 또는 TabView와 같은 뷰를 내부에 가져옵니다. .onPreferenChange 수정자를 본 적 있나요?

<div class="content-ad"></div>

이 수정자는 이 키에서 자식 뷰 업데이트를 잡습니다.

그래서

```js
.preference(
               key: ValidationPreferenceKey.self,
               value: [validation()]
            )
```

이렇게 업데이트됩니다

<div class="content-ad"></div>

```js
.onPreferenceChange(ValidationPreferenceKey.self) { value in
  validationSeeds = value
}
```

각 자식 뷰의 키 값을 캐치해요. [Bool]가 아니라 Bool로 반환했던 것 기억하시죠? 이건 단지 validationSeeds 변수에 할당하기 위한 것이에요. 이 validationSeeds 변수는 .validate() 확장 ValidationPreferenceKey로 수정된 각 자식 뷰를 보유하고 있어요.

이제 모든 필드의 Key 값이 TextFormView에 있으니 이 데이터를 처리하고 무언가를 구축할 수 있어요.

```js
private func validate() -> Bool {
  for seed in validationSeeds {
    if !seed { return false }
  }
  return true
}
```

<div class="content-ad"></div>

이 함수는 그냥 제가 생각해 낸 것일 뿐이에요. 필수적이지는 않지만 저는 좋아해요.

```js
@ViewBuilder var content : ((@escaping () -> Bool)) -> Content
                        // (validate) -> Content 이게 무슨 뜻인지에요.
```

우리는 그냥 이 TextFormView 바깥으로 이 validate 함수를 노출시키는 것 뿐이에요.

이제 이것을 우리 뷰에서 사용할 수 있고, 다음과 같이 될 거에요.

<div class="content-ad"></div>

간단한 유효성 검사를 만들었지만, 실제 상황에서는 정규식과 다른 로직을 사용하여 유효성을 검사해야 합니다. 어쩌다 그렇게 했어요. 

```swift
struct ContentView: View {
   @State var email : String = ""
   @State var password : String = ""
   @State var name : String = ""
   @State var surname : String = ""
   var body: some View {

       TextFormView  { validate  in // -> 이 부분이 바로 validate 함수에요
          VStack {
             TextField("Email", text: $email)
                .validate {
                   email.count > 6 
                }
             TextField("Name", text: $name)
                .validate {
                   !name.isEmpty
                }
             TextField("Surname", text: $surname)
                .validate {
                   !surname.isEmpty
                }
             SecureField("Password", text: $password)
                .validate {
                   password.count > 10
                }
             Spacer().frame(height: 300)
             Button {
                if !validate() {
                   return
                }
                // 네트워크 호출을 해주세요
             } label: {
                Text("Tap here to try!")
                   .padding(.all)
                   .background(.red)
                   .cornerRadius(16)
             }
          }
       }
          .padding()
    }

}
```
미리 말씀드린 대로 이미지 삽입과 함께, 어떻게 동작하는지 보여주기 위해 validate()을 출력했어요. 모든 필드가 함께 유효성을 검사하는 모습이에요.

<div class="content-ad"></div>

여기까지 읽어 주셔서 감사합니다.

이것은 시작에 불과하다는 것을 잊지 마세요. 이것을 사용하여 오류 메시지를 반환하거나 어떤 필드가 유효하지 않은지 선택할 수 있습니다. 나중에 이를 사용자 정의할 수도 있을 것 같네요.

어쨌든 나중에 뵙겠습니다! 즐거운 시간 보내세요!