---
title: "네이티브 iOS 느낌을 Compose Multiplatform 앱에서 가져오기"
description: ""
coverImage: "/assets/img/2024-05-18-GettingthenativeiOSlookfeelinyourComposeMultiplatformapp_0.png"
date: 2024-05-18 17:18
ogImage: 
  url: /assets/img/2024-05-18-GettingthenativeiOSlookfeelinyourComposeMultiplatformapp_0.png
tag: Tech
originalTitle: "Getting the native iOS look , feel in your Compose Multiplatform app"
link: "https://medium.com/@jacobras/getting-the-native-ios-look-feel-in-your-compose-multiplatform-app-33371e6ad362"
---


Compose의 기본 모양과 느낌은 Material Design입니다. Compose Multiplatform에서는 일부 요소를 iOS에서 더 네이티브하게 느끼도록 조정했습니다. 예를 들어, 버전 1.5부터 iOS의 스크롤 효과를 해당 플랫폼의 것과 유사하도록 만들었습니다. 그러나 대부분의 UI 요소는 여전히 Material처럼 보입니다. 앱에서 iOS 네이티브 룩 앤 필을 더 얻는 쉬운 방법을 살펴보겠습니다.

![image](/assets/img/2024-05-18-GettingthenativeiOSlookfeelinyourComposeMultiplatformapp_0.png)

우리는 Compose Cupertino라는 라이브러리를 사용할 것입니다. 이 라이브러리는 여러 가지 버전으로 제공됩니다:

- cupertino: Compose를 사용하여 iOS와 유사한 위젯을 구축했습니다;
- cupertino-native: 네이티브 UIKit 구성 요소 주변의 래퍼;
- cupertino-adaptive: 안드로이드에서 Material Design을 사용하는 적응형 테마/래퍼 및 iOS의 cupertino와 cupertino-native의 일부 위젯을 사용합니다 (이 글의 주된 내용);
- cupertino-icons-extended: 가장 많이 사용되는 Apple SF Symbols 800개 이상(참고: 이들은 저작권이 있으며 라이센스 계약을 준수해야 합니다);
- cupertino-decompose: 화면 전환 및 스와이프 동작의 네이티브 느낌.

<div class="content-ad"></div>

이 글에서는 얼마나 쉽게 Adaptive 플레이버를 사용하여 앱의 시스템 바(상단바 + 내비게이션/탭바) 및 주요 구성 요소(버튼 + 로딩 인디케이터 + 대화상자)를 개선할 수 있는지 살펴볼 것입니다. 라이브러리에는 더 많은 기능이 있으므로 이 글은 그에 대한 소개이고 완벽한 안내서는 아닙니다. 또한 Android에서 iOS 모습을 테스트하는 방법도 알아볼 것입니다!

# 작동 원리는 무엇인가요?

Cupertino의 네이티브 룩을 가진 위젯은 Compose를 사용하여 완전히 재구성된 iOS 구성 요소입니다. 이것은 실제 네이티브 컴포넌트가 아니라 그 모습을 그대로 보이도록 그려진 것이라는 것을 의미합니다. 그 점에 대해 우리는 걱정할 필요가 있을까요? 나는 그렇지 않다고 생각합니다. 왜냐하면 이것은 Compose 자체가 안드로이드 컴포넌트를 재구성하는 방식과 비슷하기 때문입니다. 이들은 레거시 android.view 컴포넌트에 의존하는 대신 캔버스에 그려집니다.

Cupertino Adaptive는 Material 컴포넌트뿐만 아니라 그들의 API를 염두에 두고 만들어졌습니다. 즉, 현재 사용 중인 많은 Material 컴포넌트를 Adaptive 대체품으로 사용 가능합니다. 단 몇 초 내에 교체할 수 있습니다. Android에서는 여전히 동일한 기본 코드를 호출하지만, iOS에서는 네이티브 컴포넌트처럼 보이게 그려집니다. 예외는 AdaptiveAlertDialogNative와 같이 *Native로 끝나는 적응형 위젯입니다. 그것은 대화 상자를 위한 실제 UIKit 컴포넌트를 호출하는 Cupertino Native 래퍼를 호출합니다.

<div class="content-ad"></div>

함께 해보겠습니다! 모든 코드는 https://github.com/jacobras/ComposeCupertinoSample 에서 확인할 수 있습니다.

# 튜토리얼: Material을 Cupertino으로 적응하게 만들기

이번에 사용할 샘플 프로젝트는 Kotlin Multiplatform Wizard로 생성되었습니다. 툴바, 두 개의 탭, 로딩 표시기, 그리고 대화상자를 포함한 Material3 구성 요소가 구현되어 있습니다. 시작 지점 코드베이스는 여기에서 확인할 수 있습니다: ComposeCupertinoSample/tree/starting-point.

## 1: 의존성 추가하기

<div class="content-ad"></div>

우리는 버전 카탈로그에 의존성을 추가하고 앱에서 구현했어요:

```js
// gradle/libs.versions.toml 파일:
cupertino = { module = "io.github.alexzhirkevich:cupertino-adaptive", version = "0.1.0-alpha03" }

// composeApp/build.gradle.kts 파일의 common.dependencies 안에:
implementation(libs.cupertino)
```

전체 커밋 내역: ComposeCupertinoSample/pull/2/commits/d7b05ad809bc03cf87c3c58a6f7765f5c6442b92

## 2: 테마 업데이트

<div class="content-ad"></div>

AppTheme은 현재 MaterialTheme을 사용합니다. 이것을 adaptive theme을 사용하도록 변경해야 합니다. material과 cupertino라는 두 가지 중요한 매개변수가 있습니다. material은 현재 MaterialTheme을 취하고, cupertino은 CupertinoTheme을 취합니다. 후자는 darkColorScheme() 또는 lightColorScheme()에 사용자 정의 색상을 전달하여 iOS 외관을 사용자 정의할 수 있습니다.

```js
// 변경 전
@Composable
fun AppTheme(
    useDarkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit
) {
    MaterialTheme(
        colorScheme = if (useDarkTheme) {
            darkColorScheme()
        } else {
          lightColorScheme()
        },
        content = content
    )
}

// 변경 후
@OptIn(ExperimentalAdaptiveApi::class)
@Composable
fun AppTheme(
    useDarkTheme: Boolean = isSystemInDarkTheme(),
    theme: Theme = determineTheme(),
    content: @Composable () -> Unit
) {
    AdaptiveTheme(
        material = {
            MaterialTheme(
                colorScheme = if (useDarkTheme) {
                    androidx.compose.material3.darkColorScheme()
                } else {
                    androidx.compose.material3.lightColorScheme()
                },
                content = it
            )
        },
        cupertino = {
            CupertinoTheme(
                colorScheme = if (useDarkTheme) {
                    darkColorScheme()
                } else {
                    lightColorScheme()
                },
                content = it
            )

        },
        target = theme,
        content = content
    )
}
```

determineTheme() 메서드는 [androidMain]에서 Theme.Material을 반환하고 [iosMain]에서 Theme.Cupertino를 반환하는 expect/actual 함수입니다. 자세한 내용은 전체 커밋을 참조하세요: ComposeCupertinoSample/pull/2/commits/592b3e2a1d35ff8a9961dbc6739e0e25bf581b95

지금 앱을 실행하면 아직 아무것도 변경되지 않습니다. 모든 것이 이전과 똑같이 보입니다. 왜냐하면 아직 적응형 컴포넌트를 사용하지 않았기 때문입니다. 이는 안드로이드에서는 모든 것이 변하지 않음을 보여줍니다.

<div class="content-ad"></div>

## 3: 적응형 구성요소 사용하기

이제 재미있는 부분이 시작됩니다! 이것은 또한 가장 쉬운 변경입니다. 모든 자료 구성요소를 찾아서 적응형 래퍼로 대체합니다. 예를 들어요:

```js
// Before
Button(onClick = { showContent = !showContent }) {
    Text("Click me!")
}

// After
AdaptiveButton(onClick = { showContent = !showContent }) {
    Text("Click me!")
}
```

다른 구성요소들도 이와 같이 변경할 예정입니다:

<div class="content-ad"></div>

```markdown
![이미지](/assets/img/2024-05-18-GettingthenativeiOSlookfeelinyourComposeMultiplatformapp_1.png)

패턴은 명확해야 합니다: iOS 스타일 컴포넌트에는 Cupertino[ComponentName]을 사용하고, 플랫폼에 따라 변경되는 컴포넌트에는 Adaptive[ComponentName]을 사용해야 합니다. 이 튜토리얼에서는 모두 Adaptive 컴포넌트만 사용할 것입니다.

대부분은 매개변수를 변경하지 않고 이름만 변경하면 됩니다. AlertDialog은 text를 title로, confirmButton을 buttons로 변경해야 하는 예외입니다.

전체 커밋: ComposeCupertinoSample/pull/2/commits/a8da43dd7db1187df15c0fbbca9af3ef705c64bd
```

<div class="content-ad"></div>

앱을 iOS에서 다시 실행하면 변경 사항 이전을 보여주는 좌측 시뮬레이터와 변경 후를 보여주는 우측 시뮬레이터가 표시됩니다:

![이미지1](/assets/img/2024-05-18-GettingthenativeiOSlookfeelinyourComposeMultiplatformapp_2.png)

정말 멋지게 보이네요! 다크 테마도 두 플랫폼에서 작동합니다:

![이미지2](/assets/img/2024-05-18-GettingthenativeiOSlookfeelinyourComposeMultiplatformapp_3.png)

<div class="content-ad"></div>

## 안드로이드에서의 테스트

안드로이드에서 쿠퍼티노 스타일을 테스트하려면 Android 소스 세트의 determineTheme() 메서드만 변경하면 됩니다:

```js
actual fun determineTheme(): Theme = Theme.Material3
```

# 추가 단계 및 독서

<div class="content-ad"></div>

요즘 이렇게 쉽고 영향력이 큰 작업이었는지 알겠죠. 더 할 수 있는 일이 있습니다. 아이폰/아이패드에서 iOS 아이콘을 사용하거나 더 많은 네이티브 모양의 구성 요소를 사용하는 것 등이 가능하지만, 결정은 여러분에게 달려 있습니다. 제가 제공한 것은 라이브러리를 시작하는 짧은 소개였죠.

- GitHub: Compose Cupertino
- GitHub: Compose Cupertino 샘플 앱