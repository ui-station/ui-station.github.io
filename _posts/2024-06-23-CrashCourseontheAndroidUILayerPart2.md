---
title: "안드로이드 UI 레이어 속성 완전 정복  Part 2"
description: ""
coverImage: "/assets/img/2024-06-23-CrashCourseontheAndroidUILayerPart2_0.png"
date: 2024-06-23 23:20
ogImage:
  url: /assets/img/2024-06-23-CrashCourseontheAndroidUILayerPart2_0.png
tag: Tech
originalTitle: "Crash Course on the Android UI Layer | Part 2"
link: "https://medium.com/bumble-tech/crash-course-on-the-android-ui-layer-part-2-2335171467e0"
---

## 상태 보유자 및 상태 저장

이 블로그 포스트 시리즈는 Android 개발자 안내를 UI 레이어에 대해 요약하는 것을 목표로 합니다. 우리는 이에 관련된 모든 엔티티들을 탐색하고, 각 부분이 하는 역할을 이해하며, 최선의 실천법을 논의할 것입니다.

첫 번째 부분에서는 UI와 UI 상태에 대해 다루었습니다. 이미 UI 레이어에 존재하는 다양한 엔티티와 UI 및 UI 상태를 효과적으로 생각하는 방법을 알고 있어야합니다.

이제 Part 2로 넘어가 보겠습니다! 상태 보유자 및 Android에서 UI 상태를 어디에 저장하고 상태를 어디에 끌어올려야하는지와 같은 기타 UI 레이어 관련 주제들을 다룰 것입니다.

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

만약 비디오 형식으로 이 콘텐츠를 보시는 것을 선호하신다면, 2023년 Droidcon 런던에서 제가 진행한 강연을 확인해보세요:

## 상태 보유자

상태 보유자는 로직을 처리하거나 UI 상태를 노출함으로써 UI를 간소화합니다. 이 섹션에서는 어떻게 상태 보유자를 구현하는지와 고려해야 할 구현 세부 사항을 살펴볼 것입니다.

구현 세부 사항을 결정하기 위해 먼저 안드로이드 앱에서 일반적으로 발견되는 로직 유형을 식별해야 합니다.

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

## 논리 유형

이미 비즈니스 논리가 애플리케이션 데이터가 생성, 저장 및 수정되는 방법을 지정하는 제품 요구 사항을 구현하는 것을 의미한다는 것을 이야기했습니다. UI 레이어에 비즈니스 논리가 있는 경우, 이 논리를 화면 수준에서 관리하는 것이 좋습니다. 이후에 더 자세히 살펴보겠습니다.

또 다른 유형의 논리는 UI 논리입니다. UI 논리는 화면 상태 변경을 어떻게 표시할지를 결정합니다. 비즈니스 논리가 데이터를 처리하는 방법을 지시하는 반면, UI 논리는 시각적으로 어떻게 표시할지를 결정합니다. UI 논리는 UI 구성에 의존합니다.

예를 들어, 전형적인 앱에서 세부 화면을 표시하는 것은 휴대폰에서 실행 중일 때 탐색을 포함할 수 있습니다. 그러나 태블릿에서 실행 중일 때는 다른 요소를 옆에 표시하는 것을 의미할 수도 있습니다.

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

<img src="/assets/img/2024-06-23-CrashCourseontheAndroidUILayerPart2_0.png" />

다른 유형의 로직은 구성 변경에 다르게 반응합니다:

- UI 로직은 구성 변경에 영향을 받는 경우 다시 실행해야 합니다.
- 비즈니스 로직은 일반적으로 구성 변경 후에 계속되어야 합니다.

예를 들어, 화면 크기 구성 변경 후에 하단 바 또는 네비게이션 레일을 표시할지 여부를 결정하는 UI 로직은 다시 실행하거나 재평가되어야 합니다. 반면, 특정 관심사를 따르거나 업데이트를 새로 고칠 때 사용자가 기기를 회전하거나 펼친다고 해서 비즈니스 로직을 취소하거나 다시 시작해서는 안 됩니다. 그러한 중단은 사용자 경험을 제공하지 않을 것입니다.

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

## 어떤 로직을 처리해야 할까요?

UI 레이어에서 비즈니스 로직은 화면 수준에 가깝게 처리해야 합니다. 대부분의 비즈니스 로직은 데이터 레이어에서 처리됩니다. 따라서, 화면에 가까이 유지함으로써 로직을 올바르게 범위 지정하는 것이 쉬워지고, 저수준 UI 컴포넌트가 비즈니스 로직과 긴밀하게 결합되는 것을 방지할 수 있습니다.

비즈니스 로직은 일반적으로 androidX.ViewModel에서 확장된 화면 수준 상태 홀더가 처리해야 합니다.

UI 로직에 관련된 것은 비교적 간단한 경우, UI 자체에서 관리하는 것이 가능합니다. 그러나 UI가 더 복잡해지면 해당 UI 로직의 복잡성을 일반 클래스 상태 홀더로 위임하는 것이 좋은 아이디어입니다. 이 경우, 상태 홀더는 androidX.ViewModel에서 확장되지 않을 것입니다.

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

다음 섹션에서 이것에 대해 더 자세히 살펴볼거에요! 이제 상태의 다양한 유형과 로직이 서로 어떻게 관련되는지 살펴봅시다:

![image](/assets/img/2024-06-23-CrashCourseontheAndroidUILayerPart2_1.png)

전형적인 화면에서 발생하는 일에 대한 요약으로, 데이터 레이어는 애플리케이션 데이터를 전체 계층에 노출시킵니다. 그런 다음 ViewModel은 그 데이터에 비즈니스 로직을 적용하여 화면 UI 상태를 생성합니다. UI 자체 또는 일반 상태 홀더 클래스는 화면 UI 상태를 관찰하여 UI 요소나 해당 상태를 수정합니다.

# 비즈니스 로직 처리하기 — androidX.ViewModel

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

우리는 androidX.ViewModel 또는 Architecture Components ViewModel 클래스를 화면 수준 상태 보유자로의 구현 세부사항으로 상세하게 논의해 왔습니다.

아래 코드 조각에서 우리는 주요 기능을 관찰할 수 있습니다: 1) 화면 UI 상태 노출 및 2) 비즈니스 로직 처리.

```js
@HiltViewModel
class InterestsViewModel @Inject constructor(
  private val userDataRepository: UserDataRepository,
  authorsRepository: AuthorsRepository,
  topicsRepository: TopicsRepository
) : ViewModel() {

  val uiState: StateFlow<InterestsUiState> = ...

  fun followTopic(followedTopicId: String, followed: Boolean) {
    viewModelScope.launch {
      userDataRepository.toggleFollowedTopicId(followedTopicId, followed)
    }
  }

  ...
}
```

그런데 왜 이러한 기능이 ViewModel에 적합한 것일까요?

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

## androidX.ViewModel의 장점

가장 큰 장점은 ViewModel이 구성 변경을 견뎌내며 화면 자체보다 더 오랜 수명을 제공한다는 것입니다. ViewModel은 Activity, Fragment, Navigation 그래프 또는 Navigation 그래프의 대상에 대해 범위를 설정할 수 있습니다. 구성 변경이 발생할 때 시스템은 ViewModel의 동일한 인스턴스를 제공합니다.

구성 변경을 견뎌내는 것은 androidX.ViewModel을 화면 UI 상태를 노출하고 비즈니스 로직을 처리하기에 완벽한 위치로 만듭니다. 화면 UI 상태는 구성 변경 전후에도 캐시되어 즉시 사용할 수 있습니다. 그리고 비즈니스 로직은 ViewModel-범위 CoroutineScope(예: viewModelScope로 시작된 경우)로 시작된 경우 계속 실행됩니다.

또 다른 이점은 다른 Jetpack 라이브러리와의 완벽한 통합에 있습니다. 특히 Jetpack Navigation과의 통합이 원활합니다. Navigation은 대상이 백 스택의 일부인 경우 메모리에 ViewModel의 동일한 인스턴스를 유지합니다. 이를 통해 백 스택의 대상 간에 왕복할 수 있으며 데이터가 즉시 화면에 사용 가능하게 하여 해당 대상으로 다시 이동할 때마다 데이터를 다시로드할 필요가 없습니다.

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

젯팩 네비게이션은 목적지가 백 스택의 일부가 아닐 때 ViewModel의 인스턴스를 자동으로 파괴합니다. 이로 인해 이전 사용자 데이터가 화면에 표시되지 않고 이전 목적지로 안전하게 이동할 수 있습니다.

다른 젯팩 통합에는 Hilt도 포함됩니다. @HiltViewModel 주석을 사용하면 도메인이나 데이터 레이어의 종속성이 있는 ViewModel을 손쉽게 얻을 수 있습니다.

## androidX.ViewModel 모범 사례

ViewModel의 스코프는 화면 수준 상태 보유자의 구현 세부 정보로 이 유형을 적합하게 만듭니다. 그러나 이 권한을 남용해서는 안 됩니다. 이 클래스를 사용할 때 명심해야 할 몇 가지 모범 사례가 있습니다.

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

- 화면 레벨에서 사용하세요. 재사용 가능한 UI 요소의 복잡성을 처리하기 위해 ViewModel을 사용하지 마세요. 동일한 ViewModel 스코프 내에서 동일한 UI 요소는 동일한 ViewModel의 인스턴스를 받게 되는데, 대부분의 경우 이는 바람직하지 않습니다.
- ViewModel을 충분히 일반화하여 모든 UI 형식을 수용할 수 있도록 해주세요. ViewModel은 자신을 사용하는 UI가 무엇인지 인식하면 안 됩니다. ViewModel의 API 표면(공개된 Screen UI 상태 및 노출된 함수)은 UI별 세부 사항을 포함하는 대신 처리하는 응용 프로그램 데이터를 나타내야 합니다. 예를 들어, 데이터를 로딩 중임을 나타낼 때, 화면 UI 상태에는 showLoadingSpinner 대신 isLoading이라는 필드가 포함될 수 있습니다. UI가 데이터 로딩을 사용자에게 통지하는 방법은 UI에만 관련이 있습니다.
- Lifecycle 관련 API에 대한 참조를 유지하지 마세요. ViewModel은 UI보다 오랜 수명을 가지고 있으며 Context나 Resources 객체에 대한 참조를 유지하면 메모리 누수로 이어질 수 있습니다.
- ViewModel을 전달하지 마세요. 언급된 모든 점을 고려하면 ViewModel 클래스를 화면 수준에 가능한 가깝게 유지하세요. 그렇지 않으면 저수준 구성 요소에 실제로 필요한 상태와 로직보다 더 많은 액세스 권한을 암묵적으로 부여할 수 있습니다.

## androidX.ViewModel 주의사항

ViewModel 영역은 모든 면에서 완벽하지 않습니다. 특히 ViewModel의 viewModelScope에 대한 몇 가지 고려할 사항이 있습니다:

- viewModelScope를 사용하여 시작된 작업은 ViewModel이 메모리에 있는 동안 계속 실행됩니다. 이는 좋은 점이지만 작업이 긴 시간 동안 실행될 경우 문제가 발생할 수도 있습니다. 10초 이상 소요될 수 있는 장기 실행 작업의 경우 WorkManager와 같은 다른 대안을 고려해야 합니다. 배경 작업에 대한 자세한 내용은 문서를 참조하세요.
- viewModelScope에 의해 트리거된 작업을 단위 테스트하기 위해서는 테스트 환경에서 추가 설정이 필요합니다. 테스트에서 MainDispatcher를 교체해야 합니다.

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

## AndroidX의 ViewModel 사용

이 섹션은 항상 ViewModel을 사용해야 한다는 것을 의미합니까? 네, 화면 수준의 상태 보유자로의 구현을 말하면 그렇습니다. 하지만 이점이 앱에 적용된다면 사용해야 합니다.

구성 변경에 관심이 있다면 (그래야 합니다!) 그리고/또는 다른 Jetpack 라이브러리를 사용 중이라면 이를 사용하는 것이 좋을 수 있습니다. 그러나 사용하지 않기로 결정하더라도, 화면 수준에서 비즈니스 로직 복잡성을 다루는 간단한 화면 수준 상태 보유자 클래스를 도입하는 것을 고려해보세요.

# UI 로직 처리 — 간단한 상태 보유자 클래스

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

UI가 점점 복잡해지면 상태 보유 클래스를 도입해야 합니다. 기준은 당신과 팀에 달렸습니다. UI를 간소화해야 할 필요성을 느끼는 때입니다.

다가오는 코드 스니펫에서 UI에 대한 상태 보유자를 생성할 필요가 없습니다. 사용자가 UI와 상호 작용할 때 변경되는 확장된 부울 값만 포함되어 있습니다.

```js
@Composable
fun <T> NiaDropdownMenuButton(items: List<T>, ...) {
  var expanded by remember { mutableStateOf(false) }

  Box(modifier = modifier) {
    NiaOutlinedButton(
      onClick = { expanded = true },
      ...
    )
    NiaDropdownMenu(
      expanded = expanded,
      onDismissRequest = { expanded = false },
      ...
    )
}
```

UI에 더 많은 상태가 필요하고 관련 로직이 더 복잡해지면 상태 보유자를 도입하세요. Compose 라이브러리가 일부 구성 요소에 대해 수행하는 것과 정확히 동일합니다. 다음 코드 스니펫은 다양한 Drawer 구성 구성 요소의 상태 보유자에 속합니다:

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

```kotlin {.line-numbers}
@Stable
class DrawerState(
  initialValue: DrawerValue,
  confirmStateChange: (DrawerValue) -> Boolean = { true }
) {
  internal val swipeableState = SwipeableState(...)

  val currentValue: DrawerValue
    get() { return swipeableState.currentValue }

  val isOpen: Boolean
    get() = currentValue == DrawerValue.Open

  suspend fun open() = animateTo(DrawerValue.Open, AnimationSpec)

  suspend fun animateTo(targetValue: DrawerValue, anim: AnimationSpec<Float>) {
    swipeableState.animateTo(targetValue, anim)
  }
}
```

여기 몇 가지 주요 포인트가 있어요:

- 이 클래스는 Drawer의 현재 값을 나타내는 것처럼 상태를 유지합니다.
- 상태 홀더들은 합성 가능합니다. DrawerState는 내부적으로 다른 상태 홀더인 SwipeableState에 의존합니다.
- UI 로직을 관리하며, 서랍을 여는 동작 및 특정 값으로 애니메이션 하는 등의 작업을 포함합니다.

Compose가 이러한 상태 홀더를 제공하는 것처럼, 당신의 프로젝트에서도 UI를 단순화하기 위해 비슷한 패턴을 구현할 수 있어요. 다음 코드 스니펫은 NiaApp 구성 요소 함수의 상태 홀더인 NiaAppState에 속합니다.

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

```kotlin
@Stable
class NiaAppState(
  val navController: NavHostController,
  val windowSizeClass: WindowSizeClass
) {
  val currentDestination: NavDestination?
    @Composable get() = navController
      .currentBackStackEntryAsState().value?.destination

  val shouldShowBottomBar: Boolean
    get() = windowSizeClass.widthSizeClass == WindowWidthSizeClass.Compact ||
      windowSizeClass.heightSizeClass == WindowHeightSizeClass.Compact

  fun navigate(...) { ... }

  fun onBackClick() { ... }
}
```

비슷한 방식으로,이 클래스는 currentDestination 및 하단 바를 표시해야 하는지 여부와 같은 UI 상태를 노출하고, 네비게이션 및 백 버튼 클릭 이벤트 처리와 같은 UI 논리를 관리합니다.

## 일반적인 상태 보유 클래스의 최상의 관행

재사용 가능한 UI 구성 요소를 위한 상태 보유자를 만드는 것이 좋습니다. 이는 UI의 재사용성을 향상시키고 외부 제어를 제공합니다.

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

일반 상태 보유 클래스는 수명주기 관련 API의 참조를 보유할 수 있습니다. 이 인스턴스들은 UI 수명주기를 따릅니다. UI가 구성 변경을 겪을 때마다 상태 보유 클래스의 새 인스턴스가 생성됩니다. 따라서 Context나 Resources에 대한 참조를 보유해도 메모리 누수가 발생하지 않습니다. Jetpack Compose에서 이러한 상태 보유 클래스는 Composition에도 스코프가 지정됩니다.

일반 클래스가 비즈니스 로직이 필요한 경우, 해당 기능을 클래스에 주입하는 것이 좋은 실천 방법입니다. 이 기능을 주입하는 쪽은 UI 범위를 벗어나도록 보장할 수 있습니다.

## 대규모 ViewModels 다루기

ViewModel이 여러 큰 UI 요소들의 비즈니스 로직 복잡성을 처리하고 있는 경우, 이는 크고 관리하기 어렵고 추론하기 어려울 수 있습니다. ViewModel을 어떻게 간소화할 수 있을까요?

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

- 도메인 레이어를 소개합니다. ViewModel의 비즼스 로직 복잡성을 다양한 리포지토리와 상호 작용을 처리하는 유즈 케이스에 위임합니다. 그러나 이 접근 방식은 여전히 ViewModel이 의존해야 하는 상당한 수의 유즈 케이스 목록을 일으킬 수 있습니다.
- UI의 다양한 요소에 대해 여러 상태 홀더를 만들어 그 이점을 모두 누릴 수 있도록 ViewModel에 이를 넣어두세요. ViewModel은 핵심적으로 구성 변경을 견디는 상태 전달 메커니즘이 됩니다.
- #2 대신, 재사용할 수 없는 UI 요소의 복잡성을 관리하기 위해 여러 개의 ViewModel을 만드는 것을 고려해 볼 수 있습니다. 이러한 방식은 허용되지만, ViewModel은 메모리가 제한되지 않는 상태로 작동하며, 여러 ViewModel을 가지면 그 크기와 메모리 풋프린트를 모니터링하기 어려워질 수 있음을 염두에 두세요.

# 상태를 어디에 둘 것인가

상태를 읽거나 쓰는 가장 낮은 공통 조상에 둘 것을 권장합니다.

요약하면: UI에서 상태가 전혀 없을 수 있고, UI 자체에 상태가 있을 수도 있고, UI를 단순화하기 위해 상태 홀더에 상태가 있을 수도 있고, 다른 구성 요소 호출자나 조상이 상태를 제어할 수 있도록 UI 트리 상단에 상태를 올릴 수도 있고, 비즈니스 로직에서 필요한 경우 ViewModel에 상태를 올릴 수도 있습니다.

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

If the state is required by business logic, whether for reading or writing, it should be hoisted in the screen-level state holder. If not, it should be placed in the appropriate node of the UI tree.

Let’s take a look at the UI hierarchy of a typical Chat app and discuss why certain state is placed where it is:

![UI Hierarchy](/assets/img/2024-06-23-CrashCourseontheAndroidUILayerPart2_2.png)

- The screen UI state should be placed in the ViewModel (#5) because the ViewModel applies business logic to create it.
- The LazyList is part of the ConversationScreen and not the MessagesList because the screen has additional functionality that require that state, such as scrolling to the most recent messages when the user sends a new message in UserInput.

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

이 주제에 대해 더 알아보려면 Alejandra Stamato가 하는 State hoisting in Compose 이야기를 확인해보세요.

# UI 상태 저장하기

이 블로그 포스트에서는 androidx.ViewModel API를 사용하여 구성 변경 사항에 걸쳐 상태를 유지하는 수단으로 탐색했습니다. 그러나 안드로이드는 상태를 더 효과적으로 보호하는 추가 대안을 제공합니다.

SavedState API를 사용하면 구성 변경과 시스템에서 시작된 프로세스 종료를 통해 상태가 지속될 수 있습니다. 시스템은 이 데이터를 Bundle에 저장하며, 저장을 위해 데이터를 parcel화해야 합니다. 전형적으로 사용자 입력이나 탐색에 따라 달라지는 일시적 UI 상태를 저장할 것입니다.

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

그래도, 위에서 언급한 것뿐만 아니라 예상치 못한 앱 종료(예: 사용자가 앱을 강제로 종료)에도 살아남기 위해 지속적인 저장소를 사용할 수 있습니다. 이것은 디스크 공간 제한을 고려해야 하며 일반적으로 응용 프로그램 데이터를 저장하는 데 사용됩니다.

![Crash Course on the Android UI Layer Part 2_3](/assets/img/2024-06-23-CrashCourseontheAndroidUILayerPart2_3.png)

이 주제에 대한 자세한 내용은 Android에서 UI 상태 저장하기 토크를 확인하세요.

# 결론

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

UI 계층에 대한 이 간단한 소개를 읽은 후에는 이 계층 내에서 발생하는 프로세스와 상태 및 로직을 효과적으로 관리하는 데 필요한 도구에 대한 일반적인 이해를 가지고 있을 것입니다.

안드로이드는 앱을 다양한 UI 구성 및 기기에 반응적으로 만들도록 설계되어 있어, 일부 개발자들이 원하는 것보다 약간 복잡한 API 결정 트리들을 만듭니다. 그러나 동시에 예상대로 앱이 동작하도록 하는 도구를 제공해주어 훌륭한 사용자 경험을 전달할 수 있게 해줍니다.

이 내용을 즐기셨기를 바랍니다! 의견을 공유하거나 질문을 하고 싶으시다면 댓글 섹션에서 자유롭게 남겨주세요! 감사합니다 😊

만약 비디오 형식으로 내용을 소화하고 싶다면, 2023년 드로이드콘 런던에서 발표한 제 이야기를 확인해보세요!
