---
title: "어이쿠, 내 앱이 성공했는데 접근성에 대해 생각하지 않았어요"
description: ""
coverImage: "/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_0.png"
date: 2024-05-17 18:44
ogImage:
  url: /assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_0.png
tag: Tech
originalTitle: "Oh Sh*t, My App is Successful and I Didn’t Think About Accessibility"
link: "https://medium.com/gitconnected/oh-sh-t-my-app-is-successful-and-i-didnt-think-about-accessibility-3523dc691820"
---

본인이 천재적인 앱 아이디어, 새로운 일자리, 또는 벤처 자본을 받았을 때, 새로운 기능을 배포하는 데만 시선을 집중하는 유혹이 감도는 건 이해합니다.

올바른 방식으로 집중한다면, 이러한 집중은 앱을 다운로드 차트의 정상으로 랭킹시키며 성과를 거둘 수 있습니다. 그렇지만 그것으로 충분하지 않습니다. 모두가 본인의 앱을 사용하기 전까지 멈출 수 없을 겁니다.

이런 믿을 수 없는 목표를 달성한 후, 현실적인 진실을 알아차립니다: 6명 중 1명이 본인의 앱이 고장나있다고 생각합니다.

그런 다음, 깨닫게 됩니다.

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

16%의 사람들이 어떤 형태의 접근성(a11y - 멋진 친구 중 하나라면) 요구사항을 가지고 있습니다. 그러나 자유롭게 움직이면서 일할 때, 쉽게 a11y를 뒷전에 두는 경우가 많습니다. 특히 기한에 쫓기고 리더십으로부터 제한된 참여를 받을 때 말이죠.

더 성공하게 되면, 사용자들의 접근성 요구를 인정하지 못한 것으로 비판받을 확률이 커집니다. 불행히도 이를 우선적으로 다루기가 어렵습니다. 영향력 있는 누군가가 접근성을 옹호하지 않는 한 말이죠.

오늘은 도와드리겠습니다. 앱을 접근성에 맞춰 빠르게 개선하는 방법을 보여드릴게요:

- SwiftUI 앱에서 a11y를 점검합니다.
- 모든 텍스트 크기에서 앱을 잘 보이도록 만듭니다.
- 화면 낭독기를 사용할 수 있게 만듭니다.
- 이해관계자들을 설득하여 a11y를 우선시하도록 합니다.

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

이 기사를 위해 특별히 만든 캣 테마 동반 앱을 살펴볼 것입니다. 표면 상으로는 괜찮아 보이지만, a11y(웹 접근성) 관점에서 평가를 시작하자마자 완전히 망가진 상태입니다.

기술을 완전히 이해하고 싶다면, 코드를 함께 작성해보세요! 우리는 이 기사에서 자세히 다룬 모든 기술을 구현하기 위해 Before/ 폴더에서 시작합니다 (최종 및 개선된 앱은 After/ 폴더를 참조하세요).

# 앱의 접근성(A11y) 감사

a11y(웹 접근성)를 제대로 이해하려면 실제 장치에서 접근성을 테스트해야 합니다. 이것이 모든 사용자가 앱과 상호 작용하는 방식입니다.

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

접근성(a11y) 작업을 진행할 때는, 먼저 컨트롤 센터를 최적화해야 합니다. 정말 대단한 시간을 절약할 수 있어요.

아이폰 설정으로 이동하여, 컨트롤 센터에 "텍스트 크기" 컨트롤과 "접근성 바로 가기"를 추가해보세요. "텍스트 크기"를 추가하면, 어떤 어플에서든 어디서든 밑으로 스와이프하여 텍스트 크기를 즉시 선택할 수 있어요. 심지어 어플당 크기 조절도 가능해요.

"접근성 바로 가기"를 추가하면, 어시스티브터치(AssistiveTouch), 보이스오버(VoiceOver), 색상 필터, 모션 감소 등 다양한 접근성 기능을 쉽게 적용할 수 있어요. 하지만 잠금 버튼을 세 번 누르면 동일한 메뉴가 나오기 때문에 더 쉽게 접근할 수 있어요.

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

## 텍스트 크기 감사

iOS에서는 extraSmall부터 accessibilityExtraExtraExtraLarge(즉, AX5)까지 12가지의 텍스트 크기 옵션이 있습니다. 최대 크기인 accessibilityExtraExtraExtraLarge는 기본(큰) 크기보다 약 310%\* 더 큽니다.

텍스트 크기가 정상적으로 작동하는지 확인하기 위해 두 가지 감사를 선호합니다:

- 앱을 가장 큰 일반 폰트인 extraExtraExtraLarge(즉, xxxl)로 테스트하고, 가급적 볼드 텍스트 접근성 설정도 활성화하세요. 이는 상대적으로 일반적인 가장 큰 폰트로, 제 어머니도 이 폰트를 사용합니다. 앱은 이 크기에서 잘 보여야 합니다.
- 앱을 가장 큰 접근성 폰트인 AX5로 테스트하세요. 이 크기는 익숙하지 않다면 우스꽝스러울 수 있지만, 이 크기밖에는 앱과 상호작용할 수 있는 방법이 없는 사람들이 있습니다. UI를 이 크기로 완벽하게 만드는 것은 어려울지라도, 접근성 텍스트 크기 설정에서 앱이 작동하는지조차 확인하지 않은 회사는 명백히 부족합니다.

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

저희 앱을 한 번 살펴보겠습니다.

![앱 스크린샷](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_1.png)

바로 앱을 실행해보면, 정성껏 디자인한 로그인 UI가 텍스트 크기 조절 시 문제가 발생합니다. 저희는 대용량의 텍스트 크기를 예상하지 못했기 때문에 내용을 스크롤할 필요가 있다는 생각조차 하지 못했습니다.

더 나아가서, 색맹일 경우 로그인 버튼의 대비가 미미하여 버튼을 읽을 수 없을 것입니다.

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

우리 앱의 메인 목록 화면도 확인해 봅시다.

![Main List Screen](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_2.png)

이 화면은 덜 '부서지는' 느낌이 나요. 모든 것이 보이지만 액세스 가능한 크기의 폰트를 사용하는 사람에게는 이 크기에서 앱을 테스트하지 않았다는 것이 분명합니다. 공간을 다투는 텍스트 레이블이나 순창방에서 아이콘만큼 큰 캡션을 보세요.

## 스크린 리더 오디트

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

일부 사용자는 텍스트 크기 조정에 영향을 받지 않습니다 — 그들은 앱과 상호 작용하기 위해 스크린 리더기가 필요할 수 있기 때문입니다.

iOS VoiceOver는 여기서의 중요한 작업을 훌륭하게 처리하므로 여러분 팀의 약간의 배려가 큰 차이를 만들어냅니다.

텍스트 크기 감사와 마찬가지로 스크린 리더기 감사도 실제 기기에서 하는 것이 가장 좋습니다 (잠금 버튼을 3번 눌러보세요, 기억하세요!). 또한 Xcode에 내장된 개발자 도구인 접근성 검사 도구가 VoiceOver 작업에 도움이 됩니다.

![2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_3.png](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_3.png)

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

앱을 통해 걸어가면, 텍스트 크기 조정과 비교하면 적어도 깨진 부분이 적은 편이에요. 하지만 이미지와 같은 많은 UI 영역이 전혀 보이지 않아 사용자에게 직접적인 지원이 되지 않아요. 그 이유는 접근성 레이블을 추가하는 데 신경을 쓰지 않았기 때문이죠.

![이미지1](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_4.png)

특히 VoiceOver를 사용하지 않은 경우, 화면 리더기가 모든 UI 요소를 반복하며 읽어주지만 아이콘에는 라벨이 붙어있지 않아 명확합니다.

![이미지2](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_5.png)

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

큰 목록이 있으면 상황이 더 악화됩니다.

SwiftUI에 내용에 대한 정보를 제공하지 않으면, 스크롤 가능한 내용의 아래쪽 로그아웃 버튼으로 이동하려면 100번 이상 스와이프해야 할 수도 있는 상황에 처하게 될 수 있습니다. 페이징에 대해서 시작도 하지 마세요.

![앱 접근성을 고려하지 않고 앱이 성공적이라고 생각하지 않은 경우 스크린샷](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_6.png)

우리 앱을 접근성이 좋은 앱이라고 부를 수 있기 전에 해결해야 할 문제가 많습니다. 다행히 SwiftUI에는 a11y에 대해 신속하게 파악할 수 있는 도구가 가득합니다.

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

# 스피드런 액세시빌리티

이제 문제가 어디에 있는지 알았으니, 하나씩 빠르게 해결해 나갈 수 있어요.

## 스크롤 가능한 콘텐츠

이것은 가장 불편한 문제입니다. 큰 텍스트 크기에 대한 스크롤 뷰로 확장되지 않아 앱 온보딩이 완전히 망가졌어요.

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

우리의 감사에서는 로그인 화면 버튼이 폰트 크기가 커짐에 따라 화면 밖으로 밀렸습니다. 나머지 텍스트 내용은 서로 밀어붙여져 레이블이 겹치고 확장할 공간이 없어 잘린 것처럼 보였어요.

자연스러운 해결책은 콘텐츠를 스크롤 가능하도록 만드는 것입니다. 그러나 모든 화면이 컨텐츠가 잘 맞는 경우에도 스크롤이 자유롭게 되어서는 안 됩니다. 우리는 Ionic 앱을 만드는 일이 아니에요. _억압된 기억으로 전율을 느끼며_

이 문제는 사용자 지정 뷰 수정자로 해결할 수 있습니다. 제가 a11yScrollView()라고 부르는 이 뷰 수정자의 목표는 화면 내용을 스크롤 뷰로 감싸지만 필요한 경우에만 발생하도록 하는 것입니다. 이것을 작은 라이브러리 A11yUtils에 추가하여 여러분의 코드에서 사용할 수 있도록 했어요.

현재 이것은 iOS 16.4 이상에서만 우리의 꿈이 되는 동작을 실현합니다 — 이미 콘텐츠가 맞는 경우 스크롤을 방지하기 위해 스크롤 뷰에 scrollBounceBehavior를 적용합니다.

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

미래에는 iOS 16에서 ViewThatFits\* 및 iOS 15에서 GeometryReader와 함께 작동하도록하고 싶습니다.

OS 버전을 지정하는 사람이 주위에 없기 때문에 저희 온보딩 뷰의 VStack에 이 수정자를 적용할 수 있습니다.

```swift
// OnboardingView.swift

import A11yUtils
import SwiftUI

struct OnboardingView: View {

    @Binding var isLoggedIn: Bool

    var body: some View {
        NavigationStack {
            VStack(spacing: 20) { /* ... */ }
              .padding(.horizontal)
              .a11yScrollView()
              .navigationTitle("Create account")
        }
    }
}
```

이제 콘텐츠가 멋지게 스크롤되어 xxxl, AX3 및 AX5에서 잘 보입니다.

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

<img src="/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_7.png" />

## 스페이서는 코드 냄새입니다

온보딩 화면을 작업하는 동안, 우리가 할 수 있는 또 다른 간단한 개선 사항이 있습니다:

```js
VStack(spacing: 20) {
    // ...
    OnboardingReasonsText()
    Spacer()
    LoginButtonView()
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

텍스트 크기가 크면 화면 공간이 제한되어 Spacer()를 사용하면 문제가 발생할 수 있습니다. SwiftUI 레이아웃 엔진은 공간을 만들기 위해 열심히 노력합니다. 심지어 그 공간이 더 이상 없어도 텍스트가 절단되는 문제가 발생할 수 있습니다.

이러한 시나리오에서 스페이서를 매우 좁은 높이로 압축해도 VStack에 정의된대로 위아래로 각각 20포인트의 공간이 생길 것입니다.

우리는 Spacer()를 frame() 수정자로 바꿀 수 있습니다:

```js
VStack(spacing: 20) {
    OnboardingReasonsText()
    LoginButtonView()
        .frame(maxHeight: .infinity, alignment: .bottom)
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

프레임은 더 신뢰할 수 있는 방식으로 작동합니다: SwiftUI는 이 버튼 위에 가능한 한 많은 공간을 제공해야 함을 알지만, 여백 자체가 UI의 중요한 부분이 아니기 때문에 공간이 부족하면 불필요한 여백이 적용되지 않습니다.

이 프레임 수정자는 의미가 더 명확합니다. 즉, SwiftUI에게 우리의 의도를 명확하게 전달합니다. 이것은 a11y와 관련된 중요한 개념이므로 나중에 더 자세히 다룰 것입니다.

## 이미지와 아이콘의 확대

다이나믹 타입 덕분에 iOS는 사용자가 선택한 글꼴 크기에 따라 앱 내 글꼴을 자동으로 확대합니다. 이것은 사용자 정의 글꼴과도 쉽게 구현할 수 있습니다.

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

이는 SwiftUI에서 의미있는 글꼴 크기를 사용할 때 텍스트가 자동으로 조절된다는 것을 의미합니다.

```js
Text(cat.quote)
    .font(.body) // 이것도 잘 작동합니다
```

더 좋은 것은 SFSymbols를 사용할 때 아이콘에도 동적 글꼴 크기를 적용할 수 있다는 것입니다!

```js
Image(systemName: "heart.circle.fill")
    .font(.body) // 이것도 SFSymbols와 잘 작동합니다
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

안타깝게도 대부분의 iOS 앱에는 Android 버전이 있으므로, 디자이너가 SFSymbols을 사용하는 대신 맞춤 아이콘을 만든다는 점에 유의하셔야 합니다. 대부분의 앱은 사용자 정의 이미지와 미디어를 포함합니다.

따라서 우리의 예시인 Before/에서는 아이콘을 SwiftUI에 추가하는 일반 이미지처럼 처리하여, 하드코딩된 크기를 사용했습니다.

```js
Image(cat.image)
    .resizable()
    .aspectRatio(contentMode: .fit)
    .frame(width: 72, height: 72) // 하드코딩된 크기 - 확장되지 않을 것입니다!
    .clipShape(Circle())

Image(systemName: cat.icon)
    .resizable()
    .aspectRatio(contentMode: .fit)
    .foregroundStyle(.secondary)
    .frame(width: 24, height: 24) // 하드코딩된 크기 - 확장되지 않을 것입니다!
```

결과는 어떻게 될까요? 가장 큰 글꼴 크기로 조정했을 때, 콘텐츠가 매우 어울리지 않게 보일 수 있습니다.

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

![image](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_8.png)

SwiftUI에는 사용자 정의 이미지 및 뷰에서 동적 타입의 파워를 제공하는 멋진 도구인 @ScaledMetric 프로퍼티 래퍼가 있습니다.

```swift
@ScaledMetric(relativeTo: .largeTitle) private var imageSize: CGFloat = 72
@ScaledMetric(relativeTo: .body) private var iconSize: CGFloat = 24
```

우리는 @ScaledMetric을 자체로 사용할 수 있으며, 이는 기본 body 글꼴 크기 조정을 사용합니다. relativeTo를 사용하면 SwiftUI가 어떻게 조정할 지 알 수 있으므로 더 좋습니다.

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

largeTitle은 이미 상당히 크기 때문에 조금 더 확장됩니다. 캡션 스타일의 글꼴은 더 많이 확장됩니다. 이 경우 아이콘은 본문 크기 조정이 되어 동반하는 인용문 텍스트와 일치합니다.

```js
Image(cat.image)
    .resizable()
    .aspectRatio(contentMode: .fit)
    .frame(width: imageSize, height: imageSize) // 동적 크기 조정
    .clipShape(Circle())

Image(systemName: cat.icon)
    .resizable()
    .aspectRatio(contentMode: .fit)
    .foregroundStyle(.secondary)
    .frame(width: iconSize, height: iconSize) // 동적 크기 조정
```

이제 이미지가 텍스트와 함께 더 합리적으로 보입니다.

<img src="/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_9.png" />

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

## 콘텐츠 정렬하기

이미지는 더 잘 확장되지만, 여전히 텍스트 라벨이 공간을 지배하며 UI가 깨져 보입니다. 레이아웃 엔진과의 협상을 끝내고 나니, 줄바꿈으로 가득 차 있습니다.

만약 사용자의 텍스트 확장에 기반한 컨텐츠의 정렬이 가능하다면 어떨까요?

다행히도, 라이브러리 A11yUtils 덕분에 A11yHStack을 활용할 수 있습니다.

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

새로운 SwiftUI 도구 릴리스 속도가 빨라서 (그리고 Apple의 뒷전포기가 혹독해서), 이것은 최소 지원 OS에 기반을 둔 두 가지 다른 구현을 가지고 있어요:

- iOS 16에서는 ViewThatFits를 사용하여 HStack이 모든 내용을 포함할 수 있는지 확인하고, 그렇지 않으면 내용을 정렬하기 위해 VStack을 사용합니다.
- iOS 15에서는 더 단호한 방식을 적용하여, @Environment(\.sizeCategory)를 확인하고, 접근성 텍스트 크기 범주 (예: AX1, AX2 또는 AX5) 가 사용된 경우에는 VStack으로 전환합니다.

다음은 간단한 접근 방식입니다:

```js
@Environment(\.sizeCategory) private var sizeCategory

var body: some View {
    if sizeCategory.isAccessibilityCategory {
        VStack(alignment: .leading, spacing: spacing) {
            content()
        }
        .frame(maxWidth: .infinity, alignment: .leading)
    } else {
        HStack(alignment: alignment, spacing: spacing) {
            content()
        }
    }
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

@Environment(\.sizeCategory)은 중요한 경우에 사용할 수 있는 유용한 도구입니다. 사용자가 접근성 텍스트 크기 범주를 사용하는지 여부를 알아야 하는 경우 (isAccessibilityCategory와 함께). 때로는 이 속성을 사용하여 대량 콘텐츠 크기에 맞지 않는 이미지와 같은 불필요한 UI 요소를 제거하는 데 활용합니다.

더 현대적인 구현은 비슷하게 동작하지만 ViewThatFits를 활용하여 콘텐츠가 맞지 않는 경우에만 재분배됩니다:

```js
var body: some View {
    ViewThatFits {
        HStack(alignment: alignment, spacing: spacing) {
            content()
        }
        VStack(alignment: .leading, spacing: spacing) {
            content()
        }.frame(maxWidth: .infinity, alignment: .leading)
    }
}
```

생각을 많이 해면 A11yHStack을 몇 군데에 적용할 수 있습니다:

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
import A11yUtils

A11yHStack {
    Image(cat.image)
    VStack {
        A11yHStack {
            Text(cat.name)
            Text("\(cat.age) years old")
        }
        HStack {
            Image(systemName: cat.icon)
            Text(cat.quote)
        }
    }
}
```

이제 컨텐츠가 매우 큰 텍스트 크기에서 더 적절해 보입니다.

<img src="/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_10.png" />

## 스크린 리더 개선사항

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

보이스오버는 퍼즐 조각 중 비교적 간단한 부분입니다.

앱이 스크린 리더와 잘 작동하도록 만드는 것은:

- 시각적 콘텐츠가 충분히 설명되었는지 확인하기.
- 탐색이 직관적으로 작동되도록 만들기.
- 뷰가 의미론적으로 올바른지 확인하기.

먼저 시각적 설명부터 시작해보죠. 이것은 우리 레퍼토리에서 가장 간단한 수정 사항입니다: 그래픽 콘텐츠가 VoiceOver에 의해 설명될 수 있도록 보장하는 것입니다. accessibilityLabel 수정자가 우리의 책과 버터라고 할 수 있죠.

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
Image("catKingdom").accessibilityLabel(
  Text("나의 세 마리 고양이: 코디, 로지, 루나")
);
```

스크린리더가 현재 초점을 맞춘 대상을 설명하는 방법을 알려줍니다.

![Cat Kingdom](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_11.png)

이제 주 뷰의 셀에 동일한 방식을 적용할 수 있습니다. Cat 데이터 모델을 약간 수정하여 각 고양이 사진과 함께 이미지 설명을 제공합니다.

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
struct Cat {
    // ...
    let imageDescription: String
}

Image(cat.image)
    .accessibilityLabel(Text(cat.imageDescription))
```

이건 들릴만큼 쉽고, Apple 플랫폼에서만 작동하는 경향이 있어요.

![image](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_12.png)

우리 앱을 통해 탐색할 때, 좀 더 신중하게 할 수 있는 기회가 있어요. 기본 설정으로 VoiceOver는 SwiftUI 뷰 트리의 모든 잎 노드를 반복하며 모든 이미지, 버튼 및 텍스트를 읽어줄 거예요.

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

가끔 사용자에게 불필요한 탐색을 초래합니다. 저희 앱에서는 각 고양이의 아이콘과 인용구를 건너뛰려면 여러 번 탭해야 합니다. 아이콘의 이름(예: cat.fill)을 읽는 것은 경험에 별다른 도움이 되지 않습니다.

이들을 단일 a11y 요소로 결합하여 screenreader가 탐색 중에 하나의 항목으로 처리할 수 있도록 할 수 있습니다.

```swift
HStack {
    Image(systemName: cat.icon)
    Text(cat.quote)
}
.accessibilityElement(children: .combine)
```

이제 screenreader가 요소를 하나로 이동하는 것을 들을 수 있습니다:

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

![image](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_13.png)

어떤 상황에서는 그래픽적으로 시간이 표시되는 셀렉트한 타이머 요소와 같이 복잡한 UI가 있을 수 있습니다. VoiceOver는 똑똑하지만, 설명되지 않은 뷰들의 번잡한 모음에서 유용한 정보를 유추하지는 못합니다.

여기서 accessibilityRepresentation 수정자가 정말 유용하게 사용됩니다. 이를 통해 뷰의 VoiceOver UI를 완전히 사용자 정의된 접근성 표현으로 대체할 수 있습니다.

최근에 개발한 개인 프로젝트 'Check 'em'에서는 사용자의 2단계 인증 코드가 카운트다운과 함께 표시되어 해당 기능이 특히 유용했습니다:

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
// AccountView.swift

struct AccountView: View {

    var body: some View {
        Section(account.name) {
            // Image, 2FA Code Text, and Countdown UI ...
        }
        .accessibilityRepresentation {
            VStack {
                Text(account.name)
                if let code = account.code {
                    ForEach(Array(code.enumerated()), id: \.0) {
                        Text(String($0.1))
                    }
                }
                if let countdown = account.countdown {
                    Text("Expires in \(countdown)")
                }
            }.accessibilityElement(children: .combine)
        }
    }
```

이 modifier를 사용하여 각 셀 요소를 순서대로 반복하는 것보다 훨씬 유용한 완전히 사용자 정의 된 VoiceOver 인터페이스를 소개할 수 있었습니다. 또한 화면 낭독기가 "676,252"를 "육십 칠만 육천 이백 오십 이"로 읽는 것을 방지했습니다.

![Screenshot](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_14.png)

## Native components

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

이 어플리케이션의 주요 뷰는 저희 콘텐츠를 배치하는 일반적이지만 조잡한 방법을 사용합니다: ForEach 내에 있는 셀을 래핑하는 방식으로 LazyVStack을 래핑하고 ScrollView로 래핑합니다.

```js
ScrollView {
    LazyVStack(spacing: 24) {
        ForEach(cats, id: \.name) {
            CatView(cat: $0)
        }
    }
}
```

최적의 방법은 가능한 한 SwiftUI의 기본 구성 요소를 활용하는 것입니다. 이 경우에는 List가 가장 적합합니다.

Apple은 사용자 정의 UI 대신 iOS-Settings 스타일의 List를 사용하려면 모디파이어를 3개 추가해야 한다는 점을 설명할 때, 이 작업을 좀 더 어렵게 만들어 놓았습니다.

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
목록(고양이) {
    고양이뷰(고양이: $0)
        .목록행배경(Color.clear)
        .목록행구분선(.숨김)
}
.목록스타일(일반목록스타일())
```

이제 보일러플레이트를 작성했으니 수정자들을 자유롭게 사용하여 SwiftUI 목록에서 자체 UI를 사용할 수 있습니다.

<img src="/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_15.png" />

한 가지 질문이 있습니다.

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

왜 네이티브 컴포넌트를 사용하는 것에 이렇게 많은 노력을 기울일까요?

먼저, 비 접근성(a11y) 관련 이유: SwiftUI List는 UICollectionView를 사용하여 구현됩니다. 이는 고성능 스크롤링을 위해 셀 재사용을 활용하며 항목이 많아도 성능을 유지합니다. 그에 반해 LazyVStack은 이전에 렌더링된 모든 셀을 메모리에 유지하므로 많은 항목을 스크롤하는 경우 성능이 뚝뚝 떨어집니다.

네이티브 컴포넌트를 사용하는 a11y 관련 이유는 간단하지만 다양합니다:

List는 의미론적 의미가 있습니다: 화면 판독기에게 비슷한 콘텐츠 컬렉션을 포함하는 컨테이너임을 알려줍니다.

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

이것이 많은 목록 항목이 있는 경우, 순진한 구현이 망가진 이유입니다: VoiceOver로 목록 컨테이너를 건너뛸 수 있게 해주지 않고 모든 항목을 스와이프해야만 로그아웃 버튼에 도달할 수 있었습니다.

List를 사용하면 SwiftUI가 화면 판독기에게 그 내용이 단일 컨테이너임을 알려줄 수 있어, 화면 판독기가 쉽게 이동할 수 있습니다.

아래는 Markdown 형식으로 변경된 이미지 링크입니다:

![이미지](/assets/img/2024-05-17-OhShtMyAppisSuccessfulandIDidntThinkAboutAccessibility_16.png)

또한 List에는 스와이프 삭제, 드래그 앤 드롭, 키보드 기반 탐색 등 많은 내장 상호작용 모드가 있습니다. 네이티브 컴포넌트를 사용할 때 이들은 자동으로 작동합니다. 사용자 지정 구현과 함께 하는 화면 판독기 작업을 어떻게 할 것인지 아시겠습니까?

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

리스트는 심지어 가장 복잡한 컴포넌트도 아닙니다. 사용자 정의 그리드나 레이아웃 프로토콜을 사용하지 않는 뷰의 사용자 정의 분배에 a11y를 어떻게 구현할 것인가요?

# 이해관계자 설득

이제 SwiftUI 앱에서 a11y를 빠르게 실행하는 방법을 이해했으니, 작업에 착수하기 전에 남은 퍼즐 조각은 비즈니스의 나머지 구성원으로부터 승인을 얻는 것입니다. 여기서 당신이 조직적 영향력을 행사하는 소프트 스킬을 펼칠 수 있습니다.

당신이 휘둘 수 있는 큰, 둔한 도구 중 하나는 입법의 망치입니다. 영국, 미국, 그리고 EU와 같은 세계 각국은 디지털 서비스가 접근성의 최소 기준을 충족해야 한다는 법률을 시행했습니다.

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

또한, 웹 접근성(웹 콘텐츠 접근성)은 실제로 비즈니스에서 큰 이점을 제공합니다. 현재 세계 인구의 16%가 중요한 장애를 겪고 있습니다. 즉, 다양한 요구에 맞게 서비스를 제공함으로써 사용자, 긍정적인 리뷰 및 수익을 놓칠 수 있습니다. 대상 시장에 따라 이는 더 또는 덜 중요할 수 있습니다.

작은 규모에서는, 접근성을 제품 부채로 다루는 것보다 앱이 어떻게 오작동하는지 간단히 보여주는 것이 훨씬 효과적이라는 것을 발견했습니다. 접근성 요구사항을 추상적인 개념에서 벗어나서 버그 있는 가입 플로우를 보여주는 것이 어떤 엔지니어링 조직에게도 더 설득력있을 것입니다.

제품에 쉽게 접근할 수 있도록 옹호하는 영향력 있는 리더십 속에 있는 옹호자를 가지는 것이 가장 도움이 됩니다. 물론, 모든 조직이 다르기 때문에 이 조언을 적용할 때 결과가 다를 수 있습니다.

가장 중요한 것은, 이제 웹 접근성을 빠르게 적용하고 제품이 완전히 접근 가능한 상태라면?

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

공을 놓치지 마세요!

기술 부채와 마찬가지로, 이제 좋은 위치에 있으니 a11y를 표준 개발 워크플로에 통합할 수 있습니다. 이미 알고 계시니 빠르고 쉽게 할 수 있어요!

# 결론

오늘 많은 내용을 다뤘습니다.

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

먼저 앱을 감사하여 대화형 텍스트 스케일링 또는 VoiceOver를 사용할 때 발생하는 일반적인 접근성 오류를 확인하는 프로세스를 거쳤습니다.

텍스트 스케일링을 사용할 때 발생하는 문제를 수정하고, a11yScrollView로 스크롤링을 적용하고, @ScaledMetric으로 콘텐츠 스케일링하고, A11yHStack으로 정렬하는 방법을 살펴보았습니다.

화면 판독기에서 앱이 잘 작동하도록 만들기 위해 accessibilityLabels를 구현했고, accessibilityElements를 결합하고, 심지어 완전히 사용자 정의된 accessibilityRepresentations도 구현했습니다.

네이티브 SwiftUI 구성 요소 대신 사용자 지정보기를 사용하는 이유에 대해 논의했습니다. 이유로는 의미론, 상호 작용 모드 및 성능이 있습니다.

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

마침내, 당신이 조직 내에서 접근성에 진지하게 대응하기 위해 소프트 스킬을 적용하는 방법을 살펴보았습니다.

당신이 SwiftUI 앱에서 접근성에 진지하다면, 동반 앱을 통해 이러한 기술들을 직접 구현해보는 것을 적극 권장합니다. 도구를 체득하는 가장 좋은 방법이죠. 지금까지 작업해온 사이드 프로젝트가 있다면, 그것 역시 좋은 시작점일 것입니다.

내 A11yUtils 라이브러리에 기여 (문제 포함)를 기쁘게 받아들이겠습니다. 지금도 SwiftUI에 이미 있는 API를 보완하기 위한 포괄적인 접근성 도구 모음으로 이것을 발전시키기 위해 커뮤니티와 협력하고 싶어합니다.
