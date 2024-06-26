---
title: "iOS 모바일 개발에서 Feature Flags 사용 방법"
description: ""
coverImage: "/assets/img/2024-06-23-FeatureFlagsiniOSMobileDevelopment_0.png"
date: 2024-06-23 21:41
ogImage:
  url: /assets/img/2024-06-23-FeatureFlagsiniOSMobileDevelopment_0.png
tag: Tech
originalTitle: "Feature Flags in iOS Mobile Development"
link: "https://medium.com/@robert.evansii/feature-flags-in-ios-mobile-development-104a68b50e67"
---

앱 개발의 동적인 환경에서는 새로운 기능을 도입하거나 기존 기능을 대체해야 하는 필요성이 항상 존재합니다. 전통적으로, 개발자들은 과거 코드를 제거하거나 주석 처리하고 새 코드를 통합한 후 업데이트를 앱 스토어에 제출한 다음 변경 사항을 공개하기 전에 검토 프로세스를 기다리는 경우가 많습니다. 그러나 이 방식은 앱 스토어의 검토 일정을 기다려야 하는 큰 문제점을 안고 있습니다.

대안으로, 더 나은 방법이 있습니다. 바로 피처 플래그(Feature flags)를 사용하는 것입니다. 이러한 플래그를 사용하면 특정 기능의 가시성과 동작을 손쉽게 제어할 수 있으며 복잡한 코드 변경이나 앱 스토어 제출 없이 기능을 관리할 수 있습니다. 예를 들어, 특정 기능을 특정 시간 동안만 활성화하고 싶다면, 피처 플래그를 사용하여 기능이 나타나는 조건을 제어할 수 있습니다. 앱 스토어에 새로운 빌드를 제출할 필요가 없습니다. 매끄럽고 효율적인 프로세스가 됩니다.

개인적인 개발 경험을 바탕으로 말하자면, 특정 시간대에만 특정 뷰를 제공하고 싶은 경우가 있었습니다. 피처 플래그는 이러한 시간에 민감한 기능 구현을 효율적으로 처리할 수 있는 강력한 도구로 작용했습니다.

![이미지](/assets/img/2024-06-23-FeatureFlagsiniOSMobileDevelopment_0.png)

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

# 기능 플래그란 무엇인가요?

본질적으로, 기능 플래그 또는 기능 토글이라고도 알려진 것은 일반적으로 서버에 저장된 값입니다. Firebase, SupaBase 또는 서버에 호스팅된 JSON과 같은 곳에 저장됩니다. 이 값은 특정 기능 이름과 부울 값과 연결됩니다. 모바일 애플리케이션이 이 객체를 가져오면 해당 기능이 활성화되었는지 비활성화되었는지 확인합니다. 활성화되어 있으면 앱은 일련의 기능을 실행하거나 특정 기능을 제공합니다. 이러한 동적 제어는 개발자가 기능을 선택적으로 관리하고 릴리스할 수 있도록 하여 애플리케이션의 동작을 유연하게 제어할 수 있게 합니다.

# 서버 측

프로세스를 가동하기 위해 앱에 가져올 기능을 저장하기 위한 리포지토리를 설정하는 것이 중요합니다. 제 경우에는 내 앱 내에서 이미 통합되어 있는 SupaBase를 선택하여 이를 활용했습니다. 이 선택은 추가적인 SDK를 통합하거나 새로운 구현을 도입할 필요성을 제거하고, 만들어 둔 테이블을 호출하는 방법으로 단순화된 과정을 제공합니다. 새로운 feature_flag라는 테이블을 만든 다음 다음 필드를 포함했습니다.

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

- created_at
- is_enabled
- feature_name
- number_users_seen

<img src="/assets/img/2024-06-23-FeatureFlagsiniOSMobileDevelopment_1.png" />

'feature_name'을 이해하기 쉬운 내용으로 설정하는 것이 좋지만 원한다면 설명을 위한 다른 필드도 추가할 수 있습니다.

'number_users_seen'은 내가 사용하는 사용 사례로 특정 기능을 본 사용자 수를 추적하는 것이지만 창의력과 요구 사항에 따라 추가 필드도 포함할 수 있습니다.

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

# 클라이언트 측

클라이언트 측 모델을 구축할 때는 데이터 구조와 완벽하게 일치하도록 설계하는 것이 중요합니다. 잘 구성된 모델은 응용 프로그램 전체에서 명확성과 일관성을 보장합니다.

거기에 더해, 특히 기능 이름을 위한 enum을 포함하는 것을 고려해보세요. 열거형은 서로 다른 값 집합을 나타내는 구조화된 안전한 방법을 제공하며, 이 경우에는 각기 다른 기능 이름을 나타냅니다.

여기서 enum의 장점에 대해 자세히 다루지는 않겠지만, docs.swift.org에서 더 많은 정보를 찾아볼 수 있습니다.

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

![Screenshot 1](/assets/img/2024-06-23-FeatureFlagsiniOSMobileDevelopment_2.png)

구체적인 상황으로 돌아와서, 특정 시간 프레임 내에서 뷰를 표시하고 한 번만 나타나도록하는 것을 목표로 한 내 상황에서는 아래에 설명된 방식과 유사한 접근 방식을 채택했습니다. 사용자가 프롬프트를 보았는지 여부를 추적하기 위해 UserDefaults (또는 SwiftUI에서의 @AppStorage)를 사용했습니다. 또한 featureFlags 데이터에 대한 Published 변수와 관련 시트를 표시해야 하는지를 결정하는 데 사용된 다른 Published 변수를 사용했습니다.

- 기능 플래그 가져오기
- shouldSowGiveAwaySheet 호출
- updateHasSeenGiveAwayPrompt 호출

![Screenshot 2](/assets/img/2024-06-23-FeatureFlagsiniOSMobileDevelopment_3.png)

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

내 수업 내에서는 FeatureFlagManager가 초기화될 때 getFeatureFlags를 호출하고 SupaBase에서 기능 플래그를 가져올 것입니다. 이 작업은 초기화 시에 수행하지 않을 수도 있으며 필요한 진입점에서 getFeatureFlags를 호출할 수도 있습니다.

내가 기능 플래그가 있는 시트를 표시해야 하는지 결정해야 하는 경우 shouldShowGiveAwaySheet를 호출할 수 있습니다.

```swift
guard let featureIsEnabled = featureFlags.first(where:
  { $0.featureName == .giveAwayPrompt })?.isEnabled else
    { self.showGiveAwayFeature = false }
```

위의 guard 문은 giveAwayPrompt의 기능 플래그가 featureFlags 배열에 있는지 확인합니다. 해당 플래그를 찾지 못하면 showGiveAwayFeature를 false로 설정하고 함수를 일찍 종료합니다.

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
if featureIsEnabled && !hasSeenGiveAwayPrompt {
  self.showGiveAwayFeature = true
  self.updateHasSeenGiveAwayPrompt(featureName: .giveAwayPrompt)
}
```

이 블록은 특징이 활성화되어 있고 사용자가 이전에 선물 증정 프롬프트를 보지 않은 경우에 실행됩니다.

giveaway 시트를 표시해야 한다는 것을 나타내기 위해 showGiveAwayFeature를 true로 설정합니다.

또한, updateHasSeenGiveAwayPrompt 메소드를 호출하여 사용자가 선물 증정 프롬프트를 이제 보았다는 것을 나타내는 @AppStorage showGiveAwayFeature 표식을 업데이트합니다.

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
self.showGiveAwayFeature = false;
```

이 라인은 조건 블록 밖에 있으므로 해당 기능이 활성화되었는지와 사용자가 give-away 프롬프트를 보았는지 여부에 관계없이 실행됩니다. showGiveAwayFeature를 false로 설정하여 기본적으로 표시되지 않도록 합니다.

# 열거형(enum)의 우아함

열거형(enum)의 우아함은 updateHasSeenGiveAwayPrompt와 같은 함수에서 빛을 발합니다. 필수는 아니지만, 저는 switch 문을 사용하여 사용법을 설명하는 것이 중요하다고 생각합니다. 이 접근 방식은 새로운 기능이 도입되었을 때 개발자가 해당 경우를 명시적으로 처리하도록 요청하므로 코드 완성도를 높이고 놓치는 것을 방지합니다.

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

앱 개발의 동적 영역에서 특징 플래그와 열거형의 신중한 사용은 개발자에게 유연하고 조직적인 도구 상자를 제공해줍니다. 우리는 특징 플래그가 동적 변경을 가능하게 하며, 지속적인 코드 수정이나 긴 앱 스토어 리뷰 없이 기능 릴리스를 원할하게 제어할 수 있도록 하는 방법을 살펴보았습니다. updateHasSeenGiveAwayPrompt와 같은 함수에서 열거형을 활용하면 코드의 가독성을 향상시킬 뿐만 아니라 새로운 기능 사례를 신중하게 처리하도록 개발자에게 강요하는 최선의 실천법을 갖추게 됩니다.

우리가 계속 발전하는 응용 프로그램 개발 영역을 탐험하는 동안, 이러한 실천법은 귀중하다는 것을 입증했습니다. 이것은 효율적인 프로세스뿐만 아니라 탄력적이고 적응 가능한 코드 기반을 생성하는 데 기여합니다. 특징 플래그와 열거형을 활용하여, 개발자는 변화하는 요구 사항에 우아하게 대응하는 애플리케이션을 구축할 수 있으며, 이는 보다 효율적이고 유지보수가 쉽며 미래를 대비한 개발 경험을 제공합니다.
