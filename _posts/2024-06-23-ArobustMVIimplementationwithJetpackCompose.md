---
title: "Jetpack Compose에서 MVI 패턴을 강력하게 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-ArobustMVIimplementationwithJetpackCompose_0.png"
date: 2024-06-23 01:12
ogImage:
  url: /assets/img/2024-06-23-ArobustMVIimplementationwithJetpackCompose_0.png
tag: Tech
originalTitle: "A robust MVI implementation with Jetpack Compose"
link: "https://medium.com/proandroiddev/a-robust-mvi-with-jetpack-compose-e08882d2c4ff"
---

# 왜 이렇게 하고 있는 걸까요?

저는 학습 곡선이 낮고 개발자들이 인식할 수 있는 견고한 아키텍처를 개발하는 데 매진해 왔습니다.

목표는 프로젝트와 무관한 것을 갖는 것이었습니다. 간단히 말해서, 우리는 모든 개발자가 아키텍처에 익숙해져 다른 프로젝트에 보다 쉽게 기여할 수 있도록 하고 싶었습니다. 필요할 때 다른 프로젝트에 쉽게 기여할 수 있어야 한다는 것을 명심하며, 이를 통해 성취도를 더욱 높일 수 있도록 하려 했습니다.

아키텍처를 구축하는 데에는 시간이 걸립니다. 저는 대부분의 시간을 안드로이드 생태계 및 그의 모범 사례에 대해 배우고, 그리고 이전 권장 사항에서 새로운 것으로의 전환하는 것과 같은 작업에 투자했습니다.

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

- DataBinding 대신 ViewBinding 권장
- XML에서 Jetpack Compose로 전환
- MVI 모습 기능
- KMP의 안정적인 릴리스

# 어떻게 해볼까요?

코드 한 줄을 쓰기 전에, 너무 많은 방향으로 헤딩하지 않도록 몇 가지 작업을 해야 합니다:

- 좋은 컵 자신이 좋아하는 음료를 듭니다.
- MVI가 실제로 무엇인지 조사합니다.
- 목표 동작을 정의합니다.
- 실제 구현을 코딩합니다.
- 실제 예제에서 결과를 테스트합니다.

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

# 연구 단계

MVI가 무엇을 의미하는지 알고 있는 것으로 가정하지만(아니면 지금까지 그것을 피해왔다면), 간단히 모든 사람들을 최신 상태로 알려드리겠습니다.

MVI는 Model-View-Intent의 약어입니다. 이 아키텍처는 MVVM 및 기타 모델들과 함께 MV\* 패밀리의 일부입니다. 이 아키텍처의 핵심 원칙은 입력 Intents를 받아들이는 상태 머신이며, 기저 UI를 나타내는 View State를 생성합니다. 이 모든 것은 MVI가 예전의 형제 MVVM과 달리 SSoT(Single Source of Truth) 원칙을 준수한다는 것을 의미합니다.

![이미지](/assets/img/2024-06-23-ArobustMVIimplementationwithJetpackCompose_0.png)

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

# 대상 동작

먼저, 구현의 기초 역할을 할 화면이 필요합니다. 이번에 사용할 앱은 Now In Android 애플리케이션입니다. (여기에서 찾을 수 있습니다.) 다음과 같이 생겼습니다:

간편함을 위해, 첫 번째 스크린샷인 ForYouScreen에만 집중하겠습니다. 이 화면에는 표시하고 상호작용할 수 있는 다양한 데이터가 있습니다.

이제 MVI의 핵심 개념인 Reducer를 설명해야 합니다! Reducer는 어떤 의미에서 UI와 ViewModel 간의 계약입니다. 이는 두 부분으로 나누어집니다:

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

- 객체 인터페이스 : State, Event 및 Effect
- reduce 함수 (수학적 reduce 연산자와 혼동하지 말 것)

## ViewState

이것은 UI의 표현입니다. 이론적으로, Compose 화면이 표시하는 데 필요한 모든 것을 포함해야 합니다. 이것은 화면의 다른 상태를 나타내는 여러 미리 보기를 쉽게 만들 수 있는 장점이 있습니다.

## ViewEvent

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

MVI의 핵심입니다. 모든 사용자 상호작용(그리고 조금 더)을 보유합니다. 이 것들은 ViewModel에 의해 상태 변경을 일으키는 데 사용됩니다.

## ViewEffect

ViewEvent의 특별한 종류입니다. ViewModel에 의해 UI로 전달됩니다. 네비게이션이나 스낵바/토스트 표시와 같은 작업을 포함합니다.

## reduce 함수

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

reduce 함수는 ViewState와 ViewEvent를 가져와서 새로운 ViewState와 주어진 이벤트에 연결된 필요에 따라 ViewEffect를 생성합니다.

이제 Reducer가 준비되었으니, 모든 ViewModel에 의해 구현될 BaseViewModel을 정의해야 합니다:

## 구현

이제 기본 구조가 설정되었으니, 이제 화면을 위해 실제로 이를 구현할 시간입니다!

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

## Reducer

먼저, ViewState를 정의하여 몇 가지 이벤트를 쉽게 식별할 수 있습니다.

이제 ViewState가 준비되었으므로 사용자 상호작용을 처리하고 상태를 업데이트하는 ViewEvent를 정의할 수 있습니다.

위 내용은 ViewState와 함께 명확할 것으로 생각됩니다. 그러나 마지막 3개의 이벤트에 대해서는 설명이 필요할 수 있습니다.

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

![이미지](/assets/img/2024-06-23-ArobustMVIimplementationwithJetpackCompose_1.png)

화면의 클릭 가능한 요소에 다음과 같이 연결되어 있습니다:

- UpdateTopicIsFollowed은 초록 테두리의 요소와 관련이 있습니다.
- UpdateNewsIsSaved은 주황색 테두리의 요소와 관련이 있습니다.
- UpdateNewsIsViewed는 보라색 테두리의 요소와 관련이 있습니다.

이제 MVI 아키텍처의 마지막 부분, ViewEffect를 정의해야 합니다! 다행히도, 이 화면에서는 가능한 효과가 두 가지뿐이므로 비교적 간단합니다.

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

마지막으로 구현할 reduce 함수가 있습니다. 대부분의 경우, reduce 함수는 매우 간단하여 입력 ViewEvent 데이터를 수정된 ViewState로 매핑합니다.

여기서 중요한 점(특히 마지막 3개의 ViewEvent 경우)은 실제 프로덕션 애플리케이션에서 Reducer에서 직접 표시되는 데이터를 거의 건드리지 않는다는 것입니다.

## ViewModel

이제 우리가 Reducer를 가졌으니, 다음과 같은 ViewModel에서 사용해야 합니다:

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

저희 ViewModel을 보시면, 매우 간단한 코드만 포함되어 있습니다. 이는 우리가 사용할 주요 기능이 포함된 BaseViewModel을 사용하기 때문입니다.

## 화면

마지막으로, 이 모든 것을 화면에 연결하여 UI에서 발생할 수 있는 다양한 경우를 처리하는 것이 얼마나 간단한지 확인할 수 있습니다.

# 그게 다입니다!

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

지금까지 우리가 한 모든 작업으로, SSoT 원칙을 준수하고 UI를 직접 나타내는 ViewState를 활용하는 완전히 정의된 MVI 아키텍처가 생겼습니다!

아마도 여러분 중에는 이미 시도하고 테스트되었으며 작동이 증명된 기존 아키텍처가 있음에도 불구하고 왜 이 모든 R&D를 한 것인지 궁금해할 수도 있습니다. 간단히 말해서, 필요했기 때문입니다. 저는 여러 프로젝트에서 다양한 프로젝트 매니저 및 다른 개발자들과 협업하고 있습니다.

이러한 프로젝트에서 리드 개발자로 있는 것은 종종 프로젝트에 시간을 들여 PRs(또는 GitLab 사용자를 위한 MRs)를 리뷰하는 일을 요구받기 때문입니다. 종종 작은 아키텍처적 오류에 대한 주석을 지적해야 하는데, 해당 아키텍처에 대한 보다 깊은 지식으로 방지할 수 있는 경우가 많습니다.

그래서 제가 일하는 모든 프로젝트와 호환되며 동료들이 알고 인정할 수 있는 아키텍처를 상상하면, 모든 관련 당사자들에게 전면 리뷰 프로세스를 더 빠르고 쉽게 만들어주며 전체적인 개발 속도를 높일 수 있습니다!

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

아래 링크에서 전체 프로젝트를 찾아볼 수 있어요:

MVI에 관한 다양한 훌륭한 기사가 많이 있어요. 관심이 있다면 몇 가지를 소개해 드릴게요:
