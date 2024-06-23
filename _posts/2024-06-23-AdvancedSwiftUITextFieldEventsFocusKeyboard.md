---
title: "SwiftUI TextField 심화  이벤트, 포커스, 키보드 제어 방법"
description: ""
coverImage: "/assets/img/2024-06-23-AdvancedSwiftUITextFieldEventsFocusKeyboard_0.png"
date: 2024-06-23 21:35
ogImage: 
  url: /assets/img/2024-06-23-AdvancedSwiftUITextFieldEventsFocusKeyboard_0.png
tag: Tech
originalTitle: "Advanced SwiftUI TextField — Events, Focus, Keyboard"
link: "https://medium.com/@fatbobman/advanced-swiftui-textfield-events-focus-keyboard-c99bc9f57c91"
---


이 글은 SwiftUI TextField 이벤트, 포커스 전환, 키보드 설정 및 기타 관련 경험, 기술 및 고려 사항을 탐색할 것입니다.

## 이벤트

### onEditingChanged

TextField가 포커스를 얻을 때(편집 가능한 상태로 전환될 때), onEditingChanged는 주어진 메서드를 호출하고 값으로 true를 전달합니다. TextField가 포커스를 잃을 때, 다시 메서드를 호출하고 값을 false로 전달합니다.

<div class="content-ad"></div>

```swift
struct OnEditingChangedDemo: View {
    @State var name = ""
    
    var body: some View {
        List {
            TextField("이름:", text: $name, onEditingChanged: getFocus)
        }
    }

    func getFocus(focused: Bool) {
        print("포커스 받음: \(focused ? "참" : "거짓")")
    }
}
```

이 매개변수의 이름은 사용자에게 모호함을 초래하기 쉽습니다. 사용자가 콘텐츠를 입력했는지 확인하는 데 onEditingChanged를 사용하지 마십시오.

iOS 15에서는 ParseableFormatStyle을 지원하는 새로 추가된 생성자에서 이 매개변수를 제공하지 않습니다. 따라서 새로운 Formatter를 사용하는 TextFields의 경우, 포커스를 얻었는지 또는 상실했는지를 확인하기 위해 다른 수단을 사용해야 합니다.

# onCommit


<div class="content-ad"></div>

onCommit은 사용자가 입력하는 중에 리턴 키를 누를 때 (또는 클릭할 때) 트리거됩니다 (코드 시뮬레이션으로는 트리거할 수 없음). 사용자가 리턴 키를 클릭하지 않으면(예: 직접 다른 TextField로 전환하는 경우) onCommit은 트리거되지 않습니다. onCommit이 트리거되는 동시에 TextField는 포커스를 잃게 됩니다.

```js
struct OnCommitDemo: View {
    @State var name = ""
    var body: some View {
        List {
            TextField("이름:", text: $name, onCommit: { print("커밋") })
        }
    }
}
```

사용자가 입력한 내용을 확인해야 하는 경우, onCommit과 onEditingChanged를 함께 사용하는 것이 좋습니다. 사용자의 입력 데이터를 실시간으로 처리하려면 SwiftUI TextField 고급 — 형식 지정 및 유효성 검사를 참고해주세요.

onCommit은 SecureField에도 적용됩니다.

<div class="content-ad"></div>

iOS 15에서 추가된 ParseableFormatStyle을 지원하는 새 생성자는 해당 매개변수를 제공하지 않습니다. 동일한 효과를 얻으려면 새롭게 추가된 onSubmit을 사용할 수 있습니다.

# onSubmit

onSubmit은 SwiftUI 3.0에서 새로 추가된 기능입니다. onCommit 및 onEditingChanged는 각각의 TextField의 상태를 설명하지만 onSubmit은 한 뷰 내에서 여러 TextField의 통합 관리 및 일정 관리를 가능하게 합니다.

```js
// onSubmit의 정의
extension View {
    public func onSubmit(of triggers: SubmitTriggers = .text, _ action: @escaping (() -> Void)) -> some View
}
```

<div class="content-ad"></div>

아래 코드는 onCommit과 동일한 동작을 구현할 것입니다:

```js
struct OnSubmitDemo:View{
    @State var name = ""
    var body: some View{
        List{
            TextField("name:",text: $name)
                .onSubmit {
                    print("commit")
                }
        }
    }
}
```

onSubmit의 트리거 조건은 onCommit과 동일하며, 사용자가 “return”을 활성화하도록 요구합니다.

onSubmit은 SecureField에도 적용됩니다.

<div class="content-ad"></div>

# 범위와 중첩

onSubmit의 구현은 환경 값 TriggerSubmitAction을 설정하는 것에 기반하며 (아직 개발자에게 제공되지 않음) onSubmit은 범위를 가지고 있으며 (뷰 트리로 전달될 수 있음) 중첩될 수 있습니다.

```js
struct OnSubmitDemo: View {
    @State var text1 = ""
    @State var text2 = ""
    @State var text3 = ""
    var body: some View {
        Form {
            Group {
                TextField("text1", text: $text1)
                    .onSubmit { print("text1 commit") }
                TextField("text2", text: $text2)
                    .onSubmit { print("text2 commit") }
            }
            .onSubmit { print("textfield in group commit") }
            TextField("text3", text: $text3)
                .onSubmit { print("text3 commit") }
        }
        .onSubmit { print("textfield in form commit1") }
        .onSubmit { print("textfield in form commit2") }
    }
}
```

TextField (text1)이 commit되면, 콘솔 출력은 다음과 같습니다:

<div class="content-ad"></div>

```js
양식에서의 텍스트 필드 커밋2
양식에서의 텍스트 필드 커밋1
그룹에서의 텍스트 필드 커밋
텍스트1 커밋
```

외부에서 내부로 호출 순서임을 참고하십시오.

# 제한된 범위

submitScope를 사용하여 범위를 차단하고 (뷰 트리 상에서의 추가 전파를 제한합니다). 예를 들어, 위 코드에서는 그룹 뒤에 submitScope를 추가하십시오.

<div class="content-ad"></div>

```js
그룹 {
    TextField("text1", text: $text1)
        .onSubmit { print("text1 commit") }
    TextField("text2", text: $text2)
        .onSubmit { print("text2 commit") }
}
.submitScope()  // scope blocking
.onSubmit { print("textfield in group commit") }
```

TextField1이 커밋되면 콘솔에 다음이 표시됩니다:

```js
text1 commit
```

이 시점에서 onSubmit의 범위는 그룹 내로 제한됩니다.

<div class="content-ad"></div>

뷰에 여러 개의 TextFields가 있는 경우, onSubmit과 FocusState를 결합하여 훌륭한 사용자 경험을 얻을 수 있습니다.

# searchable 지원

iOS 15에서는 새로운 검색 상자도 "return"을 클릭할 때 onSubmit을 트리거하지만, 트리거를 검색으로 설정해야 합니다:

```js
struct OnSubmitForSearchableDemo:View{
    @State var name = ""
    @State var searchText = ""
    var body: some View{
        NavigationView{
            Form{
                TextField("이름:",text:$name)
                    .onSubmit {print("textField 커밋")}
            }
            .searchable(text: $searchText)
            .onSubmit(of: .search) { //
                print("searchField 커밋")
            }
        }
    }
}
```

<div class="content-ad"></div>

SubmitTriggers의 타입은 OptionSet입니다. onSubmit 메서드는 환경 내에서 뷰 트리에서 SubmitTriggers에 포함된 값을 계속 전달합니다. onSubmit에서 받는 SubmitTriggers 값이 onSubmit에서 지정된 SubmitTriggers 세트에 포함되지 않으면 전달이 종료됩니다. 간단히 말해, onSubmit(of:.search)은 TextField에서 생성된 커밋 상태를 차단하고 그 반대도 마찬가지입니다.

예를 들어, 위 코드에서 searchable 다음에 onSubmt(of:.text)를 추가하면 TextField의 커밋 이벤트에 응답할 수 없게 됩니다.

```js
.searchable(text: $searchText)
            .onSubmit(of: .search) {
                print("searchField commit1")
            }
            .onSubmit {print("textField commit")} //cannot be triggered, blocked by search
```

따라서 TextField와 검색 상자를 모두 처리할 때 호출 순서에 특별히 주의해야 합니다.

<div class="content-ad"></div>

다음 코드는 하나의 onSubmit에서 TextField와 검색 상자를 모두 지원할 수 있습니다:

```js
.onSubmit(of: [.text, .search]) {
  print("무언가가 제출되었습니다")
}
```

다음 코드는 searchable 보다 onSubmit(of:.search)가 먼저 배치되어 있기 때문에 어느 것도 트리거하지 않습니다.

```js
NavigationView{
    Form{
        TextField("이름:", text: $name)
            .onSubmit {print("textField 제출")}
    }
    .onSubmit(of: .search) { // 트리거 안 됨
        print("searchField 제출1")
    }
    .searchable(text: $searchText)
}
```

<div class="content-ad"></div>

# 초점

iOS 15(몬테레이) 이전에는 SwiftUI에서 TextField(예 : becomeFirstResponder)에 초점을 맞추는 방법을 제공하지 않았습니다. 오랫동안 개발자들은 비슈위 방법에 의존하여 비슷한 기능을 구현해야 했습니다.

SwiftUI 3.0에서 Apple은 개발자들에게 onSubmit과 유사한, 높은 수준의 뷰 계층 구조에서 텍스트 필드의 초점을 일관되게 결정하고 관리할 수 있는 예상보다 훨씬 더 나은 솔루션을 제공했습니다.

# 기본 사용법

<div class="content-ad"></div>

SwiftUI에서는 새 FocusState 프로퍼티 래퍼를 제공하여이 뷰 내의 TextField에 포커스가 있는지 여부를 결정하는 데 도움을 줍니다. focused를 사용하여 FocusState를 특정 TextField와 연결할 수 있습니다.

```swift
struct OnFocusDemo: View {
    @FocusState var isNameFocused: Bool
    @State var name = ""
    
    var body: some View {
        List {
            TextField("이름:", text: $name)
                .focused($isNameFocused)
        }
        .onChange(of: isNameFocused) { value in
            print(value)
        }
    }
}
```

위 코드는 TextField가 포커스를 얻을 때 isNameFocused를 true로 설정하고 포커스를 잃을 때 false로 설정합니다.

동일한 뷰 내에 여러 개의 TextField가있는 경우 각 해당 TextField와 연결할 여러 FocusStates를 생성할 수 있습니다. 예를 들어:

<div class="content-ad"></div>

위의 방법은 뷰에 더 많은 TextField가 있는 경우 귀찮아지고 통일된 관리에 도움이 되지 않습니다. 다행히 FocusState는 Boolean 값뿐만 아니라 모든 해시 타입도 지원합니다. Hashable 프로토콜을 준수하는 enum을 사용하여 뷰 내 여러 TextField의 포커스를 관리할 수 있습니다. 다음 코드는 위의 코드와 동일한 기능을 수행합니다:

```js
struct OnFocusDemo:View{
    @FocusState var focus:FocusedField?
    @State var name = ""
    @State var password = ""
    var body: some View{
        List{
            TextField("name:",text:$name)
                .focused($focus, equals: .name)
            SecureField("password:",text:$password)
                .focused($focus,equals: .password)
        }
        .onChange(of: focus, perform: {print($0)})
    }

    enum FocusedField:Hashable{
        case name,password
    }
}
```

# 특정 TextField가 뷰를 표시한 직후에 즉시 포커스를 받도록 만들기

<div class="content-ad"></div>

FocusState를 사용하면 지정된 TextField가 포커스를 얻고 뷰를 표시한 후 즉시 키보드가 팝업되게 할 수 있어요:

```js
struct OnFocusDemo: View {
    @FocusState var focus: FocusedField?
    @State var name = ""
    @State var password = ""
    
    var body: some View {
        List {
            TextField("이름:", text: $name)
                .focused($focus, equals: .name)
            SecureField("비밀번호:", text: $password)
                .focused($focus, equals: .password)
        }
        .onAppear {
            DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
                focus = .name
            }
        }
    }

    enum FocusedField: Hashable {
        case name, password
    }
}
```

뷰 초기화 중에 값을 할당하는 것은 잘못된 방법이에요. onAppear에서도 TextField가 포커스를 얻을 시간을 지연시켜야 해요 (iOS 16에서는 지연이 필요하지 않아요).

# 여러 개의 TextField 사이에서 포커스 전환하기

<div class="content-ad"></div>

주의력과 onSubmit을 결합하여 한 TextField에서 입력을 완료하고(리턴을 클릭할 때) 자동으로 다음 TextField로 포커스를 전환하는 효과를 얻을 수 있어요.

```js
struct OnFocusDemo:View{
    @FocusState var focus:FocusedField?
    @State var name = ""
    @State var email = ""
    @State var phoneNumber = ""
    var body: some View{
        List{
            TextField("이름:",text:$name)
                .focused($focus, equals: .name)
                .onSubmit {
                    focus = .email
                }
            TextField("이메일:",text:$email)
                .focused($focus,equals: .email)
                .onSubmit {
                    focus = .phone
                }
            TextField("전화번호:",text:$phoneNumber)
                .focused($focus, equals: .phone)
                .onSubmit {
                    if !name.isEmpty && !email.isEmpty && !phoneNumber.isEmpty {
                        submit()
                    }
                }
        }
    }

    private func submit(){
        // 모든 정보 제출
        print("제출")
    }
    enum FocusedField:Hashable{
        case name,email,phone
    }
}
```

위의 코드는 onSubmit 전달 기능을 활용하여 다음과 같이 변환될 수도 있어요:

```js
List {
    TextField("이름:", text: $name)
        .focused($focus, equals: .name)
    TextField("이메일:", text: $email)
        .focused($focus, equals: .email)
    TextField("전화번호:", text: $phoneNumber)
        .focused($focus, equals: .phone)
}
.onSubmit {
    switch focus {
    case .name:
        focus = .email
    case .email:
        focus = .phone
    case .phone:
        if !name.isEmpty, !email.isEmpty, !phoneNumber.isEmpty {
            submit()
        }
    default:
        break
    }
}
```

<div class="content-ad"></div>

화면 버튼(예: 보조 키보드 보기) 또는 키보드 단축키를 결합하여 포커스를 전환하거나 특정 TextField로 이동할 수도 있습니다.

# 포커스를 얻기 위한 키보드 단축키 사용

보안 필드를 포함한 여러 개의 TextField가 있는 뷰에서는 Tab 키를 사용하여 TextField에서 포커스를 직접 전환할 수 있습니다. 그러나 SwiftUI는 키보드 단축키를 사용하여 특정 TextField에 포커스를 설정하는 기능을 직접 제공하지 않습니다. FocusState와 keyboardShortcut을 결합하여 iPad 및 MacOS에서 이 기능을 얻을 수 있습니다.

키보드 단축키 바인딩을 지원하는 focused를 생성하세요:

<div class="content-ad"></div>

```swift
public extension View {
    func focused(_ condition: FocusState<Bool>.Binding, key: KeyEquivalent, modifiers: EventModifiers = .command) -> some View {
        focused(condition)
            .background(Button("") {
                condition.wrappedValue = true
            }
            .keyboardShortcut(key, modifiers: modifiers)
            .hidden()
            )
    }

    func focused<Value>(_ binding: FocusState<Value>.Binding, equals value: Value, key: KeyEquivalent, modifiers: EventModifiers = .command) -> some View where Value: Hashable {
        focused(binding, equals: value)
            .background(Button("") {
                binding.wrappedValue = value
            }
            .keyboardShortcut(key, modifiers: modifiers)
            .hidden()
            )
    }
}
```

코드 사용법:

```swift
struct ShortcutFocusDemo: View {
    @FocusState var focus: FouceField?
    @State private var email = ""
    @State private var address = ""
    var body: some View {
        Form {
            TextField("이메일", text: $email)
                .focused($focus, equals: .email, key: "t")
            TextField("주소", text: $address)
                .focused($focus, equals: .address, key: "a", modifiers: [.command, .shift, .option])
        }
    }

    enum FouceField: Hashable {
        case email
        case address
    }
}
```

사용자가 ⌘ + T를 입력하면 이메일을 처리하는 TextField에 초점이 맞춰집니다. 사용자가 ⌘ + ⌥ + ⇧ + A를 입력하면 주소를 처리하는 TextField에 초점이 맞춰집니다.


<div class="content-ad"></div>

# 자체 onEditingChanged 만들기

개별 TextField의 포커스 상태를 확인하는 가장 좋은 방법은 여전히 onEditingChanged를 사용하는 것입니다. 그러나 새 포매터와 같이 onEditingChanged를 사용할 수 없는 경우에는 FocusState를 사용하여 비슷한 효과를 얻을 수 있습니다.

- 개별 TextField의 포커스 상태 확인

```swift
public extension View {
    func focused(_ condition: FocusState<Bool>.Binding, onFocus: @escaping (Bool) -> Void) -> some View {
        focused(condition)
            .onChange(of: condition.wrappedValue) { value in
                onFocus(value == true)
            }
    }
}
```

<div class="content-ad"></div>

사용 방법:

```js
struct onEditingChangedFocusVersion:View{
    @FocusState var focus:Bool
    @State var price = 0
    var body: some View{
        Form{
            TextField("가격:",value:$price,format: .number)
                .focused($focus){ focused in
                    print(focused)
                }
        }
    }
}
```

- 여러 TextFields 확인

TextField가 포커스를 잃을 때 여러 번 호출을 피하기 위해 뷰 계층 구조에서 마지막 포커스된 TextField의 FocusState 값을 저장해야 합니다.

<div class="content-ad"></div>

```js
public extension View {
    func storeLastFocus<Value: Hashable>(current: FocusState<Value?>.Binding, last: Binding<Value?>) -> some View {
        onChange(of: current.wrappedValue) { _ in
            if current.wrappedValue != last.wrappedValue {
                last.wrappedValue = current.wrappedValue
            }
        }
    }

    func focused<Value>(_ binding: FocusState<Value>.Binding, equals value: Value, last: Value?, onFocus: @escaping (Bool) -> Void) -> some View where Value: Hashable {
        return focused(binding, equals: value)
            .onChange(of: binding.wrappedValue) { focusValue in
                if focusValue == value {
                    onFocus(true)
                } else if last == value { // only call once
                    onFocus(false)
                }
            }
    }
}
```

호출:

```js
struct OnFocusView: View {
    @FocusState private var focused: Focus?
    @State private var lastFocused: Focus?
    @State private var name = ""
    @State private var email = ""
    @State private var address = ""
    var body: some View {
        List {
            TextField("이름:", text: $name)
                .focused($focused, equals: .name, last: lastFocused) {
                    print("이름:", $0)
                }
            TextField("이메일:", text: $email)
                .focused($focused, equals: .email, last: lastFocused) {
                    print("이메일:", $0)
                }
            TextField("주소:", text: $address)
                .focused($focused, equals: .address, last: lastFocused) {
                    print("주소:", $0)
                }
        }
        .storeLastFocus(current: $focused, last: $lastFocused) // 최신 포커스 값을 저장합니다.
    }

    enum Focus {
        case name, email, address
    }
}
```

# 키보드


<div class="content-ad"></div>

TextField를 사용하다보면 소프트웨어 키보드와 함께 작업해야 합니다. 이 섹션에서는 키보드와 관련된 몇 가지 예제를 소개할 것입니다.

## 키보드 유형

iPhone에서 keyboardType를 통해 소프트웨어 키보드 유형을 설정하여 사용자 입력을 용이하게 하거나 입력 문자 범위를 제한할 수 있습니다.

예시:

<div class="content-ad"></div>

```swift
struct KeyboardTypeDemo: View {
    @State var price: Double = 0
    var body: some View {
        Form {
            TextField("Price:", value: $price, format: .number.precision(.fractionLength(2)))
                .keyboardType(.decimalPad) // 소수점 숫자 키보드 지원
        }
    }
}
```

위 이미지는 "/assets/img/2024-06-23-AdvancedSwiftUITextFieldEventsFocusKeyboard_0.png" 입니다.

현재, 지원되는 키보드 유형은 총 11가지이며, 다음과 같습니다:

- asciiCapable


<div class="content-ad"></div>

아래는 번호 및 구두점 테이블입니다.

| numbersAndPunctuation |
| --------------------- |
| Numbers and punctuation |

URL 테이블로 아래 내용을 변환해주세요.

<div class="content-ad"></div>

URL 주소를 입력하는 데 유용하며, 문자 및 '.', '/', '.com'을 포함할 수 있습니다.

- numberPad

해당 지역의 숫자 세트를 사용합니다(0-9, ۰-۹, ०-९ 등). 양의 정수나 PIN에 적합합니다.

- phonePad

<div class="content-ad"></div>

전화기에서 사용되는 숫자 및 다른 기호, 예를 들면 ‘*#+’

- namePhonePad

텍스트 및 전화번호 입력에 편리합니다. 문자 상태는 asciiCapable과 유사하며, 숫자 상태는 numberPad와 유사합니다.

- emailAddress

<div class="content-ad"></div>

'@'를 입력하는 데 편리한 아스키 호환 키보드입니다.

- decimalPad

소수점이 포함된 숫자 키패드입니다. 자세한 내용은 위의 이미지를 참조해주세요.

- 트위터

<div class="content-ad"></div>

아스키 지원 키보드에는 '@#'이 포함되어 있습니다.

- 웹 검색

'.'을 포함한 아스키 지원 키보드이며 'return' 키는 'go'로 표시됩니다.

- 아스키 지원 번호 패드

<div class="content-ad"></div>

An asciiCapable 키보드에는 숫자가 포함되어 있습니다.

Apple은 다양한 키보드 모드를 제공하지만, 경우에 따라 사용자의 요구 사항을 충족시키지 못할 수도 있습니다.

예를 들어, numberPad 및 decimalPad에는 "-" 및 "return"이 없습니다. SwiftUI 3.0 이전에는 주요 뷰에 별도로 그리거나 SwiftUI 이외의 방법을 사용하여 문제를 해결해야 했습니다. 그러나 SwiftUI 3.0에서는 네이티브 설정 키보드 보조 뷰(자세한 내용은 아래에 설명되어 있음) 추가로 위 문제를 해결하는 것이 이제 더 어렵지 않아졌습니다.

# TextContentType를 통해 제안을 받아보세요

<div class="content-ad"></div>

특정 iOS 앱을 사용할 때 텍스트를 입력할 때 소프트웨어 키보드가 자동으로 입력해야 할 내용을 제안하는 경우가 있습니다. 이는 전화 번호, 이메일, 인증 코드 등을 텍스트ContentType를 통해 얻는 효과입니다.

TextField의 UITextContentType을 설정함으로써 시스템은 입력 중에 입력하려는 콘텐츠를 지능적으로 추론하고 제안을 표시합니다.

다음 코드는 비밀번호 입력 시 키체인을 사용할 수 있도록 합니다.

```js
struct KeyboardTypeDemo: View {
    @State var password = ""
    var body: some View {
        Form {
            SecureField("", text: $password)
                .textContentType(.password)
        }
    }
}
```

<div class="content-ad"></div>


<img src="/assets/img/2024-06-23-AdvancedSwiftUITextFieldEventsFocusKeyboard_1.png" />

이메일 주소를 입력할 때 연락처 및 이메일에서 비슷한 이메일 주소를 제안하는 코드입니다:

```js
struct KeyboardTypeDemo: View {
    @State var email = ""
    var body: some View {
        Form {
            TextField("", text: $email)
                .textContentType(.emailAddress)
        }
    }
}
```

<img src="/assets/img/2024-06-23-AdvancedSwiftUITextFieldEventsFocusKeyboard_2.png" />


<div class="content-ad"></div>

다음은 설정할 수 있는 여러 종류의 UITextContentType이 있습니다. 흔히 사용되는 것들 중 일부는 다음과 같습니다:

- password
- 이름 옵션, 예를 들어 name, givenName, middleName 등이 있습니다.
- 주소 옵션, 예를 듀 addressCity, fullStreetAddress, postalCode 등이 있습니다.
- telephoneNumber
- emailAddress
- oneTimeCode (인증 코드)

# 키보드 해제

일부 경우에는 사용자가 텍스트 입력을 완료한 후 소프트웨어 키보드를 해제하여 더 많은 디스플레이 공간을 확보해야 할 수 있습니다. 일부 키보드 유형에는 "return" 버튼이 없기 때문에 프로그래밍을 사용하여 키보드를 사라지게 해야 합니다.

<div class="content-ad"></div>

또한 가끔은 사용자가 "return" 버튼을 클릭하지 않고도 화면의 다른 영역을 클릭하거나 목록을 스크롤하여 키보드를 숨길 수 있게 하려고 할 수도 있습니다. 상호 작용 경험을 향상시키기 위해 프로그래밍을 사용하여 키보드를 사라지게 할 필요가 있습니다.

- FocusState를 사용하여 키보드 숨기기

해당 FocusState가 TextField에 설정되면 값을 "false" 또는 "nil"로 설정하여 키보드를 숨길 수 있습니다.

```js
struct HideKeyboardView: View {
    @State private var name = ""
    @FocusState private var nameIsFocused: Bool

    var body: some View {
        Form {
            TextField("이름을 입력하세요", text: $name)
                .focused($nameIsFocused)
            Button("키보드 숨기기") {
                nameIsFocused = false
            }
        }
    }
}
```

<div class="content-ad"></div>

- 기타 상황

대부분의 경우, 우리는 UIkit에서 제공하는 메서드를 직접 사용하여 키보드를 숨길 수 있습니다.

```js
UIApplication.shared.sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
```

예를 들어, 사용자가 뷰를 드래그할 때 키보드를 숨기는 다음 코드가 있습니다:

<div class="content-ad"></div>

```js
구조 ResignKeyboardOnDragGesture: ViewModifier {
    var gesture = DragGesture().onChanged { _ in
        UIApplication.shared.sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
    }

    func body(content: Content) -> some View {
        content.gesture(gesture)
    }
}
extension View {
    func dismissKeyboard() -> some View {
        return modifier(ResignKeyboardOnDragGesture())
    }
}
구조 HideKeyboardView: View {
    @State private var name = ""
    var body: some View {
        Form {
            TextField("이름을 입력하세요", text: $name)
        }
        .dismissKeyboard()
    }
}
```

# 키보드 어시스턴트 뷰

# 툴바를 통해 생성됨

SwiftUI 3.0에서는 ToolbarItem(placement: .keyboard, content: View)를 사용하여 키보드 어시스턴트 뷰(inputAccessoryView)를 생성할 수 있습니다.

<div class="content-ad"></div>

입력 보조 뷰를 통해 이전에 처리하기 어려웠던 많은 문제들을 해결하고 상호 작용 수단을 더 많이 제공할 수 있습니다.

다음 코드는 소수점 숫자를 입력할 때 양/음수 변환 및 확인 버튼을 추가합니다:

```js
import Introspect
struct ToolbarKeyboardDemo: View {
    @State var price = ""
    var body: some View {
        Form {
            TextField("가격:", text: $price)
                .keyboardType(.decimalPad)
                .toolbar {
                    ToolbarItem(placement: .keyboard) {
                        HStack {
                            Button("-/+") {
                                if price.hasPrefix("-") {
                                    price.removeFirst()
                                } else {
                                    price = "-" + price
                                }
                            }
                            .buttonStyle(.bordered)
                            Spacer()
                            Button("완료") {
                                UIApplication.shared.sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
                                // 해야 할 일을 수행합니다
                            }
                            .buttonStyle(.bordered)
                        }
                        .padding(.horizontal, 30)
                    }
                }
        }
    }
}
```

<img src="/assets/img/2024-06-23-AdvancedSwiftUITextFieldEventsFocusKeyboard_3.png" />

<div class="content-ad"></div>

안타깝지만, ToolbarItem을 통해 입력 보조 뷰를 설정하는 데는 아직 몇 가지 제한 사항이 있습니다:

- 표시 콘텐츠 제한

높이가 고정되어 있어 악세서리 뷰의 전체 디스플레이 영역을 활용할 수 없습니다. 다른 종류의 툴바와 마찬가지로 SwiftUI가 콘텐츠의 레이아웃에 개입합니다.

- 같은 뷰 내에서 여러 텍스트 필드에 대해 별도의 액세서리 뷰를 설정할 수 없음

<div class="content-ad"></div>

보다 복잡한 조건부 구문은 ToolbarItem에서 사용할 수 없습니다. 다른 텍스트 필드에 대해 별도로 설정하더라도 SwiftUI는 모든 내용을 함께 표시하기 위해 병합합니다.

# UIKit을 통해 생성하기

현재 단계에서는 SwiftUI에 대한 키보드 액세서리 뷰를 UIKit을 통해 생성하는 것이 여전히 최적의 솔루션이 됩니다. 화면 표시에 대한 완벽한 제어를 얻을 수 있을 뿐만 아니라 동일한 뷰 내부의 여러 텍스트 필드를 별도로 설정할 수도 있습니다.

```js
extension UIView {
    func constrainEdges(to other: UIView) {
        translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            leadingAnchor.constraint(equalTo: other.leadingAnchor),
            trailingAnchor.constraint(equalTo: other.trailingAnchor),
            topAnchor.constraint(equalTo: other.topAnchor),
            bottomAnchor.constraint(equalTo: other.bottomAnchor),
        ])
    }
}

extension View {
    func inputAccessoryView<Content: View>(@ViewBuilder content: @escaping () -> Content) -> some View {
        introspectTextField { td in
            let viewController = UIHostingController(rootView: content())
            viewController.view.constrainEdges(to: viewController.view)
            td.inputAccessoryView = viewController.view
        }
    }
    func inputAccessoryView<Content: View>(content: Content) -> some View {
        introspectTextField { td in
            let viewController = UIHostingController(rootView: content)
            viewController.view.constrainEdges(to: viewController.view)
            td.inputAccessoryView = viewController.view
        }
    }
}
```

<div class="content-ad"></div>

사용법:

```js
struct OnFocusDemo: View {
    @FocusState var focus: FocusedField?
    @State var name = ""
    @State var email = ""
    @State var phoneNumber = ""
    var body: some View {
        Form {
            TextField("이름:", text: $name)
                .focused($focus, equals: .name)
                .inputAccessoryView(content: accessoryView(focus: .name))

            TextField("이메일:", text: $email)
                .focused($focus, equals: .email)
                .inputAccessoryView(content: accessoryView(focus: .email))
            
            TextField("전화번호:", text: $phoneNumber)
                .focused($focus, equals: .phone)
        }
        .onSubmit {
            switch focus {
            case .name:
                focus = .email
            case .email:
                focus = .phone
            case .phone:
                if !name.isEmpty, !email.isEmpty, !phoneNumber.isEmpty {}
            default:
                break
            }
        }
    }
}

struct accessoryView: View {
    let focus: FocusedField?
    var body: some View {
        switch focus {
        case .name:
            Button("이름") {}.padding(.vertical, 10)
        case .email:
            Button("이메일") {}.padding(.vertical, 10)
        default:
            EmptyView()
        }
    }
}
```

# 사용자 정의 제출 레이블

기본적으로 TextField (SecureField)의 키보드에 대한 제출 동작 버튼은 "return"입니다. SwiftUI 3.0에서 소개된 "submitLabel" 수정자를 사용하면 "return" 버튼을 입력 콘텍스트와 관련된 텍스트로 수정할 수 있습니다.

<div class="content-ad"></div>

```swift
TextField("사용자 이름", text: $username)
            .submitLabel(.next)
```

<img src="/assets/img/2024-06-23-AdvancedSwiftUITextFieldEventsFocusKeyboard_4.png" />

현재 지원되는 유형은 다음과 같습니다:

- continue
- done
- go
- join
- next
- return
- route
- search
- send


<div class="content-ad"></div>

예를 들어, 이전 코드에서는 이름, 이메일 및 전화번호에 대한 서로 다른 표시를 설정할 수 있습니다:

```js
TextField("이름:", text: $name)
                .focused($focus, equals: .name)
                .submitLabel(.next)
TextField("이메일:", text: $email)
                .focused($focus, equals: .email)
                .submitLabel(.next)

TextField("전화번호:", text: $phoneNumber)
                .focused($focus, equals: .phone)
                .submitLabel(.return)
```

# 결론

SwiftUI 1.0부터 Apple은 TextField의 기능을 지속적으로 개선해 왔습니다. 버전 3.0에서 SwiftUI는 더 많은 네이티브 수정자를 제공하는데 그치지 않고 FocusState와 onSubmit과 같은 통합 관리 논리를 제공합니다. 믿음직스러운 2~3년 뒤에는 SwiftUI의 주요 컨트롤의 네이티브 기능이 해당하는 UIKit 컨트롤과 맞먹을 것이라고 믿습니다.

<div class="content-ad"></div>

만약 이 글이 도움이 되었거나 즐겁게 읽으셨다면, 제 글을 지원하기 위해 기부를 고려해 주세요. 여러분의 기부는 저가 계속해서 가치 있는 콘텐츠를 만드는데 도움이 될 것입니다.
Patreon, Buy Me a Coffee 또는 PayPal을 통해 기부할 수 있습니다.

```js
연락하고 싶다면?

Twitter에서 @fatbobman을 팔로우하세요.
```