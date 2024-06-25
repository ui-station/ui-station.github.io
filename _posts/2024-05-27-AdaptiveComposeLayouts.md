---
title: "적응형 컴포즈 레이아웃"
description: ""
coverImage: "/assets/img/2024-05-27-AdaptiveComposeLayouts_0.png"
date: 2024-05-27 17:51
ogImage:
  url: /assets/img/2024-05-27-AdaptiveComposeLayouts_0.png
tag: Tech
originalTitle: "Adaptive Compose Layouts"
link: "https://medium.com/@stefanoq21/adaptive-compose-layouts-86b7f1e51338"
---

## 모든 창 크기에 대한 흥미로운 소식

![이미지](/assets/img/2024-05-27-AdaptiveComposeLayouts_0.png)

올해 구글 I/O는 흥미로운 발표들로 가득 찼는데, AI 분야뿐만 아니라 (물론 그것도 하이라이트였지만) 적응형 레이아웃을 구축하기 위한 Jetpack Compose의 발전에 중점을 둔 것이 나에게는 핵심적인 교훈이었습니다. 안드로이드는 스마트폰뿐만 아니라 태블릿, 폴더블폰, 대형 화면 등으로 확장되고 있어서 다양한 형태 요소에 적응하는 앱을 개발하는 것이 더 중요해지고 있습니다.

이전에 내가 이전 게시물에서 창 크기 클래스를 사용하여 반응형 레이아웃을 탐구했습니다. 그러나 Jetpack Compose의 흥미로운 새로운 발전으로 인해 해당 주제를 다시 살펴보게 되었습니다. WindowSizeClass의 새로운 구현뿐만 아니라 사용법을 간단하게 하는 새로운 Composable 함수들도 새롭게 나왔습니다. 또한 일반적인 레이아웃 동작을 단순화하는 새로운 Composable 함수들도 나왔는데, 사용자 정의 함수가 필요 없어졌습니다.

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

많은 내용을 다뤄야 하니까, 첫 번째 주요 측면으로 넘어가 볼까요? WindowSizeClass의 새로운 구현을 살펴봅시다. 이해를 돕기 위해, 제 이전 글에서의 구현을 이전해 보겠습니다. 원하신다면 확인해보세요.

## WindowSizeClass의 이전

Gradle 파일을 업데이트하는 것부터 시작해봅시다. 이전 종속성을 제거하고 새롭게 개선된 구현을 위한 새로운 것을 추가할 겁니다.

```js
[versions]
adaptive = "1.0.0-beta01"
...
[libraries]

-- androidx-material3-windowSizeClass = { group = "androidx.compose.material3", name = "material3-window-size-class" }
androidx-adaptive = { module = "androidx.compose.material3.adaptive:adaptive", version.ref = "adaptive" }
...
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

Gradle 종속성을 업데이트한 후 이제 이전 WindowSizeClass 구현에 의존했던 코드 섹션을 이주해 보겠습니다. 예를 들어, 창 크기에 따라 동적으로 열의 수를 결정하는 코드가 있다면 다음과 같이 업데이트할 수 있습니다:

```js
val windowWidthSize = currentWindowAdaptiveInfo().windowSizeClass.windowWidthSizeClass

val columns = when (windowWidthSize) {
       WindowWidthSizeClass.COMPACT -> 1
       WindowWidthSizeClass.MEDIUM -> 2
       else -> 3
}
```

업데이트가 함수 변경을 넘어 이동했다는 것을 알 수 있습니다! 주요 개선 사항 중 하나는 composable 함수 내에서 windowSizeClass를 직접 가져올 수 있는 능력입니다. 이제 activity를 통해 액세스할 필요 없이 이를 가져올 수 있습니다. 즉, 앱 전반에 걸쳐 창 크기 클래스를 매개변수로 전달할 필요가 없어졌습니다! 이것은 더 깨끗한 코드를 위한 중요한 발전입니다.

## NavigationSuiteScaffold

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

윈도우 크기 클래스 이주를 다루었으니, 이제는 새로운 구성 가능 함수를 살펴봅시다. 먼저, NavigationSuiteScaffold가 등장합니다. 이 중요한 구성 가능 함수는 창 크기에 따라 하단 탐색 표시줄, 탐색 레일 및 서랍 사이를 전환할 때 사용자 정의 논리가 필요 없도록 해줍니다.

이전 글에서는 탐색 요소 전환을 위한 사용자 정의 솔루션 구축을 탐구했습니다. 이제 NavigationSuiteScaffold가 이 프로세스를 간단하게 하는 방법을 살펴보겠습니다. 이 새로운 함수를 사용하여 동일한 결과를 얻는 방법은 다음과 같습니다:

```js
...
NavigationSuiteScaffold(
        modifier = Modifier,
        navigationSuiteItems = {

            bottomNavigationItems.forEach { bottomBarElement ->

                val selected =
                    currentScreen.instanceOf(bottomBarElement.screen::class)

                item(
                    icon = bottomBarElement.icon,
                    selected = selected,
                    alwaysShowLabel = true,
                    label = {
                        Text(
                            text = stringResource(id = bottomBarElement.title),
                            style = MaterialTheme.typography.labelMedium.copy(
                                textAlign = TextAlign.Center,
                                fontWeight = FontWeight.Normal
                            ),
                            maxLines = 1
                        )
                    },
                    onClick = {
                        if (!selected) {
                                NavigationEvent.OnNavigateBottomBar(
                                    bottomBarElement.screen
                                )
                        }
                    }
                )
            }
        }

    ) {
        Scaffold() { innerPadding ->
            MainNavHost(
                modifier = Modifier.padding(innerPadding),
            )
        }
    }
```

이 단일 구현은 현재 창 크기에 따라 적절한 탐색 경험을 제공하도록 자동으로 동작합니다. 이는 작은 화면의 경우 하단 표시줄과 큰 화면의 경우 탐색 레일과 같은 요소 간에 전환하는 적절한 탐색 경험을 제공합니다.

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

그러나 더 맞춤화된 접근을 선호하는 분들을 위해 NavigationSuiteScaffold는 NavigationSuiteType을 통해 유연성을 제공합니다. 여러분은 해당 스캐폴드 내에서 사용자 정의 동작을 매끄럽게 통합하여 스마트폰의 가로 모드에서도 네비게이션 레일과 같은 요소를 사용할 수 있습니다.

```js
...
val adaptiveInfo = currentWindowAdaptiveInfo()
val customNavSuiteType = with(adaptiveInfo) {
            if (windowSizeClass.windowWidthSizeClass == WindowWidthSizeClass.EXPANDED) {
                NavigationSuiteType.NavigationRail
            } else {
                NavigationSuiteScaffoldDefaults.calculateFromAdaptiveInfo(adaptiveInfo)
            }
}

NavigationSuiteScaffold(
        modifier = Modifier,
        layoutType = customNavSuiteType,
        navigationSuiteItems = {
...
```

동일한 원칙이 특정 시나리오에서 네비게이션을 완전히 숨기고 싶은 경우에도 적용됩니다.

```js
...
val adaptiveInfo = currentWindowAdaptiveInfo()
    val customNavSuiteType = with(adaptiveInfo) {
         when {
            !shouldShowBottomBar -> {
                NavigationSuiteType.None
            }
            windowSizeClass.windowWidthSizeClass == WindowWidthSizeClass.EXPANDED -> {
                NavigationSuiteType.NavigationRail
            }
            else -> {
                NavigationSuiteScaffoldDefaults.calculateFromAdaptiveInfo(adaptiveInfo)
            }
        }
    }
...
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

## ListDetailPaneScaffold

안녕하세요, 또 다른 강력한 Composable인 ListDetailPaneScaffold를 살펴보겠습니다. 이 함수는 화면 크기가 큰 경우 두 개의 화면(또는 패널로 표시)을 옆에 나란히 표시하고 싶을 때 이상적입니다. 주요 장점 중 하나는 단일 패널을 표시하거나 이중 패널 레이아웃을 활용하든 내부 내비게이션을 자동으로 처리한다는 점입니다. 이것은 개발을 간소화할 뿐만 아니라 창 크기에 관계없이 부드러운 사용자 경험을 보장합니다.

```js
[versions]
material3AdaptiveNavigationSuiteAndroid = "1.3.0-beta01"
...
[libraries]

androidx-material3-adaptive-navigation-suite-android = { group = "androidx.compose.material3", name = "material3-adaptive-navigation-suite-android", version.ref = "material3AdaptiveNavigationSuiteAndroid" }
...
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

Gradle 종속성을 사용하여 코드를 업데이트할 수 있습니다.

```js
 val navigator = rememberListDetailPaneScaffoldNavigator<String>()

    BackHandler(navigator.canNavigateBack()) {
        navigator.navigateBack()
    }

    ListDetailPaneScaffold(
        directive = navigator.scaffoldDirective,
        value = navigator.scaffoldValue,
        listPane = {
            AnimatedPane {
                HomeScreen(
                    onClickOnItem = {
                        navigator.navigateTo(
                            ListDetailPaneScaffoldRole.Detail,
                            it
                        )
                    }
                )
            }
        },
        detailPane = {
            AnimatedPane {
                navigator.currentDestination?.content?.let {
                    ZoomBookInitScreen(book = it.id)
                }
            }
        },
    )
```

이름에서 알 수 있듯이 navigator는 패널 내에서의 네비게이션을 관리하는 역할을 합니다. 이는 상세 패널로 이동하거나, 단일 패널 모드에서 백 네비게이션을 처리하는 것을 포함합니다. 또한 상세 패널로 전달된 데이터 객체를 포함합니다. 특히, Parcelable인 경우 사용자 정의 객체도 공유할 수 있습니다.

WindowSizeClass와 함께 ListDetailPaneScaffold를 사용하여 현재 창 크기에 기반한 동작을 맞춤화할 수 있습니다. 예를 들어 (스크린샷에서 볼 수 있듯이) 단일 패널 모드에서만 뒤로 화살표와 같은 요소를 조건부로 표시할 수 있습니다.

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
val windowWidthSize = currentWindowAdaptiveInfo().windowSizeClass.windowWidthSizeClass
val backVisible = when (windowWidthSize) {
    WindowWidthSizeClass.EXPANDED -> false
    else -> true
}
```

이 수준의 제어는 모든 다양한 디바이스 크기에서 정교한 사용자 경험을 보장합니다.

## SupportingPaneScaffold

새로운 적응형 레이아웃 기능을 탐색하기 위해 SupportingPaneScaffold를 간단히 살펴보겠습니다. 이 구성 요소는 핵심 기능인 탐색 관리와 창 내용 표시를 ListDetailPaneScaffold와 유사한 점을 가지고 있습니다. 하지만 SupportingPaneScaffold는 주요 콘텐츠 창과 오른쪽에 있는 더 작은 "보조" 창을 함께 사용하는 경우에 맞게 설계되었습니다. 이는 보조 콘텐츠가 주요 콘텐츠를 보완하거나 부가 정보를 제공하지만 동일한 화면 공간이 필요하지 않은 상황에 이상적입니다.

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

프로젝트에서 SupportingPaneScaffold을 구현하려면 ListDetailPaneScaffold에서 얻은 지식을 기반으로 활용할 수 있습니다. 더 깊은 이해를 위해 젯팩 코믹스의 공식 문서를 여기에 남겨 두겠습니다.

# 결론

본 문서를 통해 Google I/O 2024에서 젯팩 코믹스를 사용한 적응형 레이아웃 구축의 흥미로운 발전을 확인하였습니다. 새로운 WindowSizeClass 구현은 액세스 및 사용을 간단하게 만들어주며, NavigationSuiteScaffold, ListDetailPaneScaffold 및 SupportingPaneScaffold와 같은 강력한 조합 가능 함수들은 다양한 화면 크기와 형태 요인을 통해 탐색 및 콘텐츠 표현을 간소화하는 접근 방식을 제공합니다.

이러한 새로운 기능은 Android 애플리케이션을 위한 정말로 반응적이고 사용자 친화적인 경험을 만들 수 있게 해줍니다. 적응형 레이아웃을 위한 최상의 실천 방법을 준수하고 이러한 도구들을 수용함으로써, 앱이 지속적으로 진화하는 Android 생태계에 매끄럽게 적응하여 모든 기기의 사용자에게 탁월한 경험을 제공할 수 있습니다.

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

의견을 자유롭게 공유해주시거나, 원하신다면 LinkedIn에서 연락 주셔도 좋습니다.

좋은 하루되세요!
