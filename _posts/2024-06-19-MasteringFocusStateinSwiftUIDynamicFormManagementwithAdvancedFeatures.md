---
title: "SwiftUI에서 FocusState를 마스터하기 고급 기능을 활용한 동적 폼 관리"
description: ""
coverImage: "/assets/img/2024-06-19-MasteringFocusStateinSwiftUIDynamicFormManagementwithAdvancedFeatures_0.png"
date: 2024-06-19 10:59
ogImage:
  url: /assets/img/2024-06-19-MasteringFocusStateinSwiftUIDynamicFormManagementwithAdvancedFeatures_0.png
tag: Tech
originalTitle: "Mastering FocusState in SwiftUI: Dynamic Form Management with Advanced Features"
link: "https://medium.com/@wesleymatlock/mastering-focusstate-in-swiftui-dynamic-form-management-with-advanced-features-fbb083281cf7"
---

<img src="/assets/img/2024-06-19-MasteringFocusStateinSwiftUIDynamicFormManagementwithAdvancedFeatures_0.png" />

사용자의 포커스를 효과적으로 관리하는 것은 모든 애플리케이션에서 매끈하고 직관적인 사용자 경험을 만들기 위해 중요합니다. SwiftUI는 FocusState라는 강력한 기능을 제공하는데, 이를 사용하면 개발자가 폼 입력 및 기타 대화형 요소의 포커스 상태를 동적으로 관리할 수 있습니다. 이 블로그 포스트에서는 FocusState를 사용하여 사용자 상호작용에 따라 포커스를 조정하는 사용자 지정 입력 폼을 만드는 방법을 살펴보겠습니다. 또한 조건부 포커스, 키보드 관리 및 사용자 정의 포커스 처리와 같은 고급 기능을 포함하고 있습니다.

## FocusState 이해하기

FocusState는 SwiftUI에서 도입된 프로퍼티 래퍼로, 텍스트 필드와 같은 뷰의 포커스 상태를 관리하는 데 도움을 주는 역할을 합니다. FocusState를 사용하면 어떤 입력 필드에 포커스를 맞출지를 프로그래밍적으로 제어할 수 있어 사용자 경험을 더 부드럽게 만들 수 있습니다, 특히 폼에서의 경우에 유용합니다.

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

## 커스텀 입력 폼 만들기

사용자 입력에 따라 동적으로 포커스가 변경되는 여러 텍스트 필드가 있는 포괄적인 양식을 만들어 봅시다. 조건부 포커스, 키보드 해제 및 사용자 정의 포커스 로직과 같은 고급 기능을 통합할 것입니다.

## 구현 단계별

1. 양식 필드와 포커스 상태 정의하기

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

먼저, 다양한 폼 필드를 나타내는 열거형을 정의합니다. 그런 다음, 폼 상태와 포커스 상태를 나타내는 구조체를 생성합니다.

```js
import SwiftUI

enum FormField: Hashable {
    case firstName
    case lastName
    case email
    case password
    case confirmPassword
}

struct FormState {
    var firstName: String = ""
    var lastName: String = ""
    var email: String = ""
    var password: String = ""
    var confirmPassword: String = ""
}
```

2. 폼 뷰 생성하기:

이제, 폼 필드를 포함하는 뷰를 생성해보겠습니다. @FocusState 프로퍼티 래퍼를 사용하여 포커스 상태를 관리하고 더 많은 고급 기능을 추가할 수 있습니다.

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
struct CustomFormView: View {
    @State private var formState = FormState()
    @FocusState private var focusedField: FormField?

    var body: some View {
        VStack {
            Form {
                TextField("이름", text: $formState.firstName)
                    .focused($focusedField, equals: .firstName)
                    .onSubmit {
                        focusedField = .lastName
                    }
                    .submitLabel(.next)

                TextField("성", text: $formState.lastName)
                    .focused($focusedField, equals: .lastName)
                    .onSubmit {
                        focusedField = .email
                    }
                    .submitLabel(.next)

                TextField("이메일", text: $formState.email)
                    .focused($focusedField, equals: .email)
                    .keyboardType(.emailAddress)
                    .onSubmit {
                        focusedField = .password
                    }
                    .submitLabel(.next)

                SecureField("비밀번호", text: $formState.password)
                    .focused($focusedField, equals: .password)
                    .onSubmit {
                        focusedField = .confirmPassword
                    }
                    .submitLabel(.next)

                SecureField("비밀번호 확인", text: $formState.confirmPassword)
                    .focused($focusedField, equals: .confirmPassword)
                    .onSubmit {
                        validateForm()
                    }
                    .submitLabel(.done)
            }
            .padding()

            Button("제출") {
                validateForm()
            }
            .padding()
            .buttonStyle(.borderedProminent)
        }
        .onTapGesture {
            // 필드 외부를 탭하면 키보드 닫기
            focusedField = nil
        }
        .toolbar {
            ToolbarItemGroup(placement: .keyboard) {
                Spacer()
                Button("완료") {
                    focusedField = nil
                }
            }
        }
    }

    private func validateForm() {
        // 여기서 폼 유효성 검사 및 제출 로직 수행
        guard !formState.firstName.isEmpty,
              !formState.lastName.isEmpty,
              isValidEmail(formState.email),
              formState.password == formState.confirmPassword else {
            // 오류 메시지 표시 또는 유효성 검사 실패 처리
            return
        }

        // 폼 제출 처리
        print("폼이 제출되었습니다: \(formState)")
        focusedField = nil
    }

    private func isValidEmail(_ email: String) -> Bool {
        // 기본적인 이메일 유효성 검사 로직
        let emailRegEx = "[A-Z0-9a-z._%+-]+@[A-Z0-9a-z.-]+\\.[A-Za-z]{2,64}"
        let emailPred = NSPredicate(format: "SELF MATCHES %@", emailRegEx)
        return emailPred.evaluate(with: email)
    }
}
```

3. Form View 미리보기:

마지막으로 SwiftUI 미리보기에서 Form View를 미리볼 수 있습니다.

```swift
#Preview {
  CustomFormView()
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

## Explanation

- **포커스 관리:** 각 TextField 및 SecureField는 `.focused($focusedField, equals: .formField)` 수정자를 사용하여 특정 포커스 상태와 연결됩니다. 이를 통해 사용자가 하나의 필드를 제출할 때 다음 필드로 포커스가 전환됩니다.

- **동적 포커스 조정:** `onSubmit` 수정자는 반환 키 작업을 처리하는 데 사용됩니다. 사용자가 현재 필드를 제출하면 포커스가 다음 필드로 변경됩니다.

- **사용자 지정 키보드 툴바:** 키보드에 사용자 정의 툴바가 추가되어 "완료" 버튼을 탭함으로써 키보드를 해제할 수 있습니다.

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

• 양식 제출 및 유효성 검사: validateForm 함수는 양식 제출과 유효성 검사를 처리합니다. 비어있는 필드, 유효한 이메일 형식 및 일치하는 비밀번호를 확인한 후에야 양식 제출이 진행됩니다.

• 키보드 해제: onTapGesture 수정자는 사용자가 입력 필드 외부를 탭할 때 키보드를 해제하는 데 사용됩니다.

## 결론

SwiftUI에서 FocusState를 사용하면 입력 필드의 포커스 상태를 관리하여 동적이고 사용자 친화적인 양식을 만들 수 있습니다. 조건부 포커스, 키보드 관리 및 사용자 정의 유효성 검사와 같은 고급 기능을 통합함으로써 SwiftUI 애플리케이션에서 사용자 경험을 향상시키고 더 견고한 양식을 생성할 수 있습니다.

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

원하는 경우 원시 모바일 개발에 대해 더 알아보려면 여기에서 다른 기사들을 확인해보세요: [https://medium.com/@wesleymatlock](https://medium.com/@wesleymatlock)

코딩을 즐기세요! 🚀
