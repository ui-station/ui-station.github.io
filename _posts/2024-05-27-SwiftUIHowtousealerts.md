---
title: "SwiftUI 얼럿alerts 사용 방법"
description: ""
coverImage: "/assets/img/2024-05-27-SwiftUIHowtousealerts_0.png"
date: 2024-05-27 16:25
ogImage:
  url: /assets/img/2024-05-27-SwiftUIHowtousealerts_0.png
tag: Tech
originalTitle: "[SwiftUI] How to use alerts"
link: "https://medium.com/@ganeshrajugalla/swiftui-how-to-use-alerts-29a5ceef24d9"
---

<img src="/assets/img/2024-05-27-SwiftUIHowtousealerts_0.png" />

이 기사는 .alert()를 사용하여 사용자에게 뭔가를 확인하라는 팝업을 표시하는 방법을 보여줍니다.
이것을 사용하는 방법은 iOS 15 이후 많이 변경되었습니다.
iOS 14 및 이전 버전의 알림을 사용하는 방법에 대한 자세한 정보는 아래를 참조하세요.

- Environment
- Basic Usage
- Example of Use
- Install Multiple Buttons
- Specifying Button Display Style
- Example of Use
- Use Multiple Alerts

# 환경

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

이 기사는 다음 버전을 사용합니다:

- **Xcode** 13.0
- **Swift** 5.5
- **iOS** 15.0
- **macOS** Big Sur 버전 11.6

## 기초 사용법

SwiftUI에서 코딩은 선언적이며 절차적이 아닙니다. 경고(alerts)도 이 규칙을 따릅니다. "알림을 표시"하는 단계별 프로세스를 코딩하는 대신, 경고를 정의하고 언제 나타날지를 정의합니다. 경고를 정의하려면 .alert() 수정자(modifier)를 사용하세요.

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
.alert("제목", isPresented: Binding<bool>, actions: { 액션 버튼 목록 }, message: { 메시지 })
```

또는

```js
.alert("제목", isPresented: Binding<bool>) {
     // 액션 버튼 목록
} message: {
     // 메시지
}
```

“제목”
제목에 표시할 텍스트를 지정합니다.

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

isPresented
알림이 표시될지를 결정하는 표시 플래그 변수입니다. 사용자가 알림 액션을 탭하면 표시 플래그가 자동으로 거짓으로 변경됩니다.

Actions
Button() 목록을 사용하여 알림에 표시할 작업 버튼을 나열합니다. 특정 작업이 없는 경우 "OK" 버튼이 기본으로 표시됩니다. 알림의 모든 버튼은 닫히며, 표시 플래그를 거짓으로 설정합니다.

messages (선택 사항)
Text를 사용하여 제목 아래에 자세한 메시지를 설정할 수 있습니다. 그러나 스타일을 적용하거나 여러 Text를 표시할 수는 없습니다.

# 사용 예제

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

이 예시에서는 Button에 대해 alert 수정자가 설정되어 있지만, 직접적으로 해당 Button에 연결되어 있지는 않습니다. 경고는 Button 자체가 아닌 표시 플래그에 의해 제어됩니다.

```js
import SwiftUI

struct AlertsBootCamp: View {

    // MARK: - 속성
    @State private var isShowAlert: Bool = false

    // MARK: - 본문
    var body: some View {
        Button(action: {
           isShowAlert = true
        }, label: {
            Text("알림 표시")
                .font(.headline)
                .fontWeight(.bold)
                .foregroundStyle(.white)
                .padding()
                .background(content: {
                    RoundedRectangle(cornerRadius: 25.0, style: .continuous)
                        .foregroundStyle(Color.black)
                })
        })
        .alert("제목", isPresented: $isShowAlert) {
            Button("눌러주세요") {
                // 버튼이 눌릴 때 수행되는 작업
            }
        } message: {
            Text("메시지")
        }
    }
}
```

![이미지](/assets/img/2024-05-27-SwiftUIHowtousealerts_1.png)

# 여러 버튼 설치

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
import SwiftUI

struct AlertsBootCamp: View {

    // MARK: - Properties
    @State private var isShowAlert: Bool = false

    // MARK: - Body
    var body: some View {
        Button(action: {
           isShowAlert = true
        }, label: {
            Text("Display Alert")
                .font(.headline)
                .fontWeight(.bold)
                .foregroundStyle(.white)
                .padding()
                .background(content: {
                    RoundedRectangle(cornerRadius: 25.0, style: .continuous)
                        .foregroundStyle(Color.black)
                })
        })
        .alert("Title", isPresented: $isShowAlert) {
            Button("Button 1") {
                // button 1 action will come here
            }
            Button("Button 2") {
                // button 2 action will come here
            }
        } message: {
            Text("Message")
        }
    }
}
```

두 개의 버튼이 있습니다.

<img src="/assets/img/2024-05-27-SwiftUIHowtousealerts_2.png" />

세 개 이상 설치하시면 아래와 같이 수직으로 배열됩니다.

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

3개의 버튼이 있는 테이블을 만들려면
다음 Markdown 형식을 사용하실 수 있습니다.

| Header1 | Header2 | Header3 |
| ------- | ------- | ------- |
| Data1   | Data2   | Data3   |
| Data4   | Data5   | Data6   |

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
Button("Title", role: ButtonRole?, action: { action button })
```

3가지 유형을 지정할 수 있습니다:

1. 취소

2. 파괴성

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

3. 아무 값도 지정하지 않으면 버튼은 기본 스타일인 파란색으로 나타납니다.

.cancel
취소 버튼은 진한 파란색으로 스타일이 지정됩니다. 버튼이 두 개인 경우 항상 왼쪽에 위치하며, 세 개 이상인 경우에는 맨 아래에 위치합니다.

.destructive
이 스타일은 삭제와 같은 파괴적인 작업을 나타내며 빨간색으로 표시됩니다. 적어도 하나의 이 유형 버튼이 있는 경우 취소 버튼이 자동으로 추가됩니다.

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

# 사용 예시

버튼에 .destructive 역할을 부여하면 자동으로 취소 버튼이 추가됩니다.

```js
import SwiftUI

struct AlertsBootCamp: View {

    // MARK: - Properties
    @State private var isShowAlert: Bool = false

    // MARK: - Body
    var body: some View {
        Button(action: {
            isShowAlert = true
        }, label: {
            Text("알림 표시")
                .font(.headline)
                .fontWeight(.bold)
                .foregroundStyle(.white)
                .padding()
                .background(content: {
                    RoundedRectangle(cornerRadius: 25.0, style: .continuous)
                        .foregroundStyle(Color.black)
                })
        })
        .alert("제목", isPresented: $isShowAlert) {

            Button("삭제", role: .destructive) {
                // 동작이 여기에 들어갑니다.
            }
        } message: {
            Text("메시지")
        }
    }
}
```

<img src="/assets/img/2024-05-27-SwiftUIHowtousealerts_4.png" />

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

# 여러 경고 사용하기

한 화면에 여러 경고를 사용하려면 여러 디스플레이 플래그 변수를 사용하세요.
iOS14 이전에는 특별한 조치가 필요했습니다.

```js
struct ContentView: View {

    // MARK: - Properties
    @State private var isShowAlert1: Bool = false
    @State private var isShowAlert2: Bool = false

    // MARK: - Body
    var body: some View {
        VStack {
            Button("경고 1 표시") { isShowAlert1 = true }
            Button("경고 2 표시") { isShowAlert2 = true }
        }
        .alert("경고 1", isPresented: $isShowAlert1) { }
        .alert("경고 2", isPresented: $isShowAlert2) { }
    }
}
```

<img src="https://miro.medium.com/v2/resize:fit:590/1*y3FoT4bWzWZcph2TnxqMzg.gif" />

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

감사합니다!
