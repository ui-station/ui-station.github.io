---
title: "앱 테마 변경하기  접근성 맞춤 설정하기"
description: ""
coverImage: "/assets/img/2024-05-27-ChangeAppThemePersonalizingAccessibility_0.png"
date: 2024-05-27 17:50
ogImage:
  url: /assets/img/2024-05-27-ChangeAppThemePersonalizingAccessibility_0.png
tag: Tech
originalTitle: "Change App Theme — Personalizing Accessibility"
link: "https://medium.com/proandroiddev/change-app-theme-personalizing-accessibility-3ceecf919d25"
---

<img src="/assets/img/2024-05-27-ChangeAppThemePersonalizingAccessibility_0.png" />

이전 두 개의 게시물로 시작한 개인화 접근성 주제를 이어가고 있어요. 아래 링크에서 확인할 수 있어요:

- 설정으로 개인화 접근성
- 아이콘과 레이블 전환 - 개인화 접근성

이전 게시물에서 설명한 것처럼, 일반적으로 개인화는 접근성을 개선하는 열쇠가 될 수 있다는 것과 아이콘과 레이블을 숨기거나 표시하는 설정을 추가하는 구체적인 예시를 제시했어요.

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

이 블로그 포스트에서는 구체적인 예시를 계속 다루겠습니다. 앱의 테마를 변경할 수 있는 설정을 추가할 것입니다. 사용자는 시스템 설정을 따르는 것(기본값), 밝은 테마, 어두운 테마, 고대비 색상 테마 중에서 선택할 수 있습니다. 함께 알아보겠습니다.

# 왜?

가끔 사용자들은 핸드폰의 테마와 다른 테마로 앱을 사용하고 싶어합니다. 저는 개인적으로 모든 것을 어두운 모드로 사용하지만, 제게는 사용하기 어려운 색상이 들어간 어두운 테마를 가진 몇몇 앱을 만난 적이 있습니다. 너무 많은 대비가 있는 경우도 있고, 때로는 너무 적은 경우도 있습니다. 그런 경우에는 밝은 테마로 변경하고 싶었습니다. 일반적으로 그런 옵션이 없어서 그 앱을 포기했거나, 필요할 때 최소한 사용했습니다.

그래서 사용자에게 제어력을 주는 것이 중요합니다. 이런 종류의 설정을 위한 최소 옵션은 시스템 기본값, 밝은 테마, 어두운 테마입니다. 하지만 고대비 테마는 어떨까요? 왜 추가해야 하며 누가 필요로 할까요?

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

## 고대비 테마

고대비 테마는 더 높은 대비를 가진 색상으로 이루어진 색상 팔레트를 가지고 있습니다. 구체적인 예로는 Windows 7 이후에 개발된 Windows 고대비 모드가 있습니다. 안드로이드는 고대비 텍스트를 설정할 수 있는 기능을 제공하지만, 그것은 텍스트에만 적용됩니다.

WebAIM은 2018년에 저시력 사용자들을 대상으로 설문조사를 실시해, 응답자 중 51.4% (n=248)가 고대비 모드를 사용했다고 나타냈습니다. 저시력 사용자는 고대비 테마를 필요로 하는 큰 그룹 중 하나지만, 다른 사람들도 필요할 수 있습니다. 예를 들어, 편두통을 앓는 사람이나 아일린 증후군을 가진 사람, 또는 일부 언어 장애를 가진 사람들도 고대비 테마를 통해 혜택을 받을 수 있습니다. 또한 고대비 모드는 눈에 좋은 햇빛 속에서 모두에게 유용할 수 있습니다.

# 어떻게?

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

앱 테마를 변경할 수 있는 기능을 추가하는 흐름은 이전 블로그 게시물의 레이블 및 아이콘과 유사합니다. 설정 화면에 설정을 추가하고 설정 값을 데이터 저장소에 저장한 다음 앱에서 해당 값을 사용하여 어떤 테마를 표시할지 결정합니다.

먼저 설정 화면부터 시작해보겠습니다. 테마를 선택할 수 있는 섹션과 선택할 수 있는 옵션을 추가하고 싶습니다. 리스트에는 시스템 기본, 다크 테마, 라이트 테마 및 고대비 테마 네 가지 옵션이 포함되어 있습니다. 예시에서 색상이 표시됩니다:

![테마 선택 화면](/assets/img/2024-05-27-ChangeAppThemePersonalizingAccessibility_1.png)

## 데이터 저장하기

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

먼저, 이전 블로그 게시물에서 설정 저장에 사용한 설정 데이터 저장소에 키-값 쌍을 추가하려고 합니다. 이전 블로그 게시물에서 언급했듯이, 블로그 게시물을 단순하게 유지하기 위해 데이터 저장소는 SettingsRepository에서 정의되고 상호 작용됩니다.

네 가지 옵션이 있기 때문에 이 경우에는 간단한 부울 값이 작동하지 않습니다. 코드에서는 테마에 대해 미리 정의된 값들을 사용하고자 하므로, 테마에 대한 옵션을 모두 포함하는 enum 클래스를 만들고 시스템 기본값으로 null을 사용합니다.

```js
// ThemeExt.kt

enum class Theme {
    Dark,
    Light,
    HighContrast;
}
```

데이터 저장소는 enum 값들을 저장할 수 없으므로, enum을 문자열로 변환하고 다시 변환할 방법이 필요합니다. 문자열로 변환하는 부분은 쉽습니다 - toString() 함수를 사용할 수 있습니다. 다른 변환에 대해서는 도우미 함수를 정의해야 합니다. enum 클래스에 추가해보겠습니다:

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
enum class Theme {
    ...

    companion object {
        fun from(value: String?): Theme? {
            return when (value) {
                Dark.name -> Dark
                Light.name -> Light
                HighContrast.name -> HighContrast
                else -> null
            }
        }
    }
}
```

이 메서드에서는 주어진 값과 일치하는 테마 이름이 무엇인지 확인합니다. 또한, 일치하는 것이 없을 때의 기본 케이스는 null입니다 - 일치하는 것이 없으면 시스템 기본값으로 가정하고 값을 null로 설정합니다.

이제 데이터 스토어에 테마를 저장하기 위한 모든 준비가 되었습니다. 먼저 값을 읽기 위한 flow를 추가해 보겠습니다:

```kotlin
// SettingsRepository.kt

private object PreferencesKeys {
    ...
    val colorTheme = stringPreferencesKey("color_theme")
}

val colorThemeFlow: Flow<Theme?> = dataStore.data
    .map {
        Theme.from(it[PreferencesKeys.colorTheme])
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

그럼, 먼저 preferences 키 오브젝트에 preferences 키를 추가합니다. 플로우를 위해 데이터 스토어에서 값을 읽은 다음, 앞서 정의한 Theme.from()을 사용하여 문자열을 Theme-enum으로 파싱합니다.

값을 편집하기 위해 다음과 같은 함수를 정의합니다:

```kotlin
// SettingsRepository.kt

suspend fun setColorScheme(theme: Theme?) {
    dataStore.edit { preferences ->
        preferences[PreferencesKeys.colorTheme] = theme.toString()
    }
}
```

이 함수에서는 enum의 문자열 값을 정의한 preference 키를 사용하여 데이터 저장소에 설정합니다.

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

다음으로 할 일은 SettingsViewModel을 업데이트하는 것입니다. 먼저, 테마 값을 저장할 Mutable State Flow를 추가하고 UI에서 사용합니다:

```kotlin
// SettingsViewModel.kt

private var _colorScheme =
    MutableStateFlow<Theme?>(Theme.Dark)
val colorScheme = _colorScheme.asStateFlow()
```

그런 다음, 리포지토리에서 값을 읽고 setColorScheme 함수를 사용할 수 있도록 함수를 정의합니다:

```kotlin
// SettingsViewModel.kt

private fun getColorScheme() {
    viewModelScope.launch {
        settingsRepository.colorThemeFlow.collect {
            _colorScheme.value = it
        }
    }
}

fun setColorScheme(theme: Theme?) {
    viewModelScope.launch {
        settingsRepository.setColorScheme(theme)
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

마지막으로 앱을 열 때 초기값을 얻기 위해 init 블록에서 getColorScheme()을 호출하는 것입니다:

```js
// SettingsViewModel.kt

init {
    ...
    getColorScheme()
}
```

## 설정 화면

지난 게시물과 마찬가지로 SettingsViewModel에서 값을 사용하는 방법을 보여주지는 않겠지만, 접근성 및 의미론적인 관점에서 몇 가지 사항을 언급하고 싶습니다: 각 색상 옵션은 선택 가능한 변경자를 가져야하며, 색상 옵션을 감싸는 구성 요소는 selectableGroup()-변경자를 가져야합니다. 내 코드에서 이러한 것들은 다음과 같습니다:

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

부모 컴포넌트에서 다음을 사용하고 있어요:

```js
// SettingsScreen.kt

Column(
    modifier = Modifier.selectableGroup(),
) {
    colorOptions.map { option ->
        ...
    }
}
```

이 작업이 왜 이루어지는지 더 알고 싶다면, 제 블로그 게시물인 젯팩 콤포즈에서 Modifier를 활용하여 Android 접근성 향상하기를 확인해보세요.

## UI에 대한 테마 변경

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

최종 단계는 테마의 저장된 값 사용하여 해당 값을 업데이트하는 것입니다. 저장된 값에 따라 테마를 설정하는 로직을 추가해야 합니다.

실제 테마 정의는 이 블로그 포스트의 범위를 벗어납니다. 저는 어두운, 밝은 및 고대비 테마에 대한 색상을 정의했고 코드에서 이를 사용할 것입니다.

Theme.kt 파일에서 애플리케이션 테마의 기본 구현에 추가적인 확인을 추가해봅시다:

AppTheme 코파서블에 Theme 유형의 선택적 매개변수를 추가하고 해당 값을 사용하여 MaterialTheme 구성 요소에 설정할 색상을 결정합니다. 테마가 null이 아닌 경우 값을 기준으로 색상을 설정하고, null이면 시스템의 기본 색상을 사용해야 합니다. 이를 위해 isSystemInDarkTheme 값을 확인하여 색상을 설정합니다.

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

마지막으로, 앱의 루트가 정의된 MainActivity에서 우리는 저장된 테마의 값을 읽은 다음 그 값을 AppTheme-composable에 전달합니다:

```js
// MainActivity.kt

val theme = settingsViewModel.colorScheme.collectAsState()

AppTheme(theme = theme.value) { ... }
```

그리고 이러한 변경 사항들로 설정에서 선택했을 때 다음 테마들을 볼 수 있게 될 것입니다:

# 마무리

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

이 블로그 글에서는 앱에서 사용자가 테마를 선택할 수 있도록 설정을 추가하여 사용자가 소프트웨어의 테마를 결정하는 방법을 살펴보았어요. 제공되는 테마는 밝은 테마, 어두운 테마, 고대비 테마 또는 시스템의 기본 테마를 따를 수 있어요.

당신의 앱에 테마 선택기를 구현해 보셨나요? 지금까지 고대비 테마를 경험해 보신 적이 있나요? 앞으로 다룰 수 있는 접근성 설정 유형에 대한 아이디어가 있으신가요?

# 블로그 글 내 링크

- 설정으로 접근성 개인화하기
- 아이콘과 레이블을 토글하기 — 접근성 개인화
- WebAIM
- Jetpack Compose에서 Modifier로 안드로이드 접근성 향상하기
