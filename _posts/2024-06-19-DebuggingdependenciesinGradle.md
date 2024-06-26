---
title: "Gradle에서 종속성을 디버깅하기"
description: ""
coverImage: "/assets/img/2024-06-19-DebuggingdependenciesinGradle_0.png"
date: 2024-06-19 10:29
ogImage:
  url: /assets/img/2024-06-19-DebuggingdependenciesinGradle_0.png
tag: Tech
originalTitle: "Debugging dependencies in Gradle"
link: "https://medium.com/proandroiddev/debugging-dependencies-in-gradle-54c8be444849"
---

## Android 앱에서 dependencyInsight 사용 방법 및 transient dependencies를 특정 버전으로 고치는 방법

![이미지](/assets/img/2024-06-19-DebuggingdependenciesinGradle_0.png)

간략히 말하자면; 빠르게 알고 싶다면 아래의 제목 섹션으로 이동하여 dependencyInsight를 사용하는 방법에 대한 지침을 따르세요.

최근에 안드로이드 앱에서 종속성을 베타 버전(androidx.navigation:navigation-compose, 버전 2.8.0-beta02 정확히)으로 업그레이드해야 했습니다. 일반적으로 베타 버전의 종속성은 다른 Jetpack Compose 종속성(일시적 종속성)을 필요로 합니다. 그 중에는 알파 또는 베타 버전으로 지정된 것들도 있습니다. 일반적으로 이는 괜찮습니다. 알파 또는 베타 버전의 종속성에 문제가 있을 수 있고, 안정된 버전을 기다릴 수 있고 필요하다면 버그를 제기할 수 있다는 것을 받아들입니다.

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

하지만 내 경우에는 기다릴 수 없었습니다. 내 앱에 빨리 배포해야 하는 쿨한 새로운 기능을 새로운 네비게이션 라이브러리(Type-Safe Navigation yay!)에서 원했어요. 문제는, 일시적 종속성의 새로운 알파 버전이 앱에서 발생시키지 않으려고 했던 크래시를 일으켰다는 것이었어요(내 경우 HorizontalPager에서 NoSuchMethodError가 발생했어요)!

조사를 해보니, HorizontalPager 생성자가 androidx.compose.foundation 종속성의 1.7.0-beta02 버전에서 시그니처가 변경되었다는 것을 발견했어요(HorizontalPager는 실험적 API로 표시되어 있어 이것이 전혀 놀라운 일이 아니었어요). 이것은 androidx.navigation:navigation의 일시적 종속성으로 포함되었던 것이었어요. 네비게이션 라이브러리는 1.7.0-beta02 버전을 명시했지만, 이전 안정 버전인 1.6.7에서 내 HorizontalPager 구현이 작동하는 것을 알고 있었어요.

그럼 어떡해야 할까요? androidx.navigation:navigation 또는 androidx.compose.foundation:foundation이 안정화되어 내 앱에 통합되기를 기다릴까요(필요한 새로운 기능을 배포해야 하지만)? 새로운 종속성을 가지고 무언가를 갖다 붙이려고 노력할까요, 아니면 안정화된 종속성 버전이 사용되도록 확인할 수 있을까요?

이 시점에서 난 심지어 어떤 종속성이 일시적 종속성 버전 업그레이드를 유발하고 있는지도 몰랐어요 — 똑같은 작업에서 다른 종속성을 여러 개 업데이트했기 때문에 무엇이든지 가능합니다...

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

# Gradle 의존성 트리 보기

가장 먼저 확인해야 할 것은 전체 의존성 트리입니다.

가장 간단한 방법은 gradle wrapper를 사용하여 의존성 명령을 실행하는 것입니다:

```js
./gradlew :app:dependencies
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

위에서도 언급했듯이 모듈을 지정하면 흥미로운 모듈 전체의 출력을 얻을 수 있습니다. 모듈을 제외하면 보통 원하는 결과가 아닌 상위 수준의 세부 정보만을 얻게 됩니다.

대신 모듈을 지정하면 해당 모듈이 필요로 하는 종속성 목록 및 이를 통해 암시적으로 필요한 종속성들을 얻을 수 있습니다.

더 신기하게 하고 싶다면 종속성 명령에 --scan을 추가하여 검색 가능한 웹 기반 보고서를 생성할 수도 있습니다. 이를 위해서는 gradle으로 이메일을 확인해야 하며, 종종 텍스트 버전을 볼 때 더 빠르다고 생각합니다. 일반적으로 명령 결과를 파일로 출력하여 큰 프로젝트에서도 변경 전과 변경 후 결과를 비교할 수 있습니다 (결과가 너무 길어질 수 있습니다!).

./gradlew :app:dependencies `dependencyTree.txt"

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

## 의존성 트리 출력 이해하기

만약 결과 파일을 살펴보면 엄청나게 크다는 것을 알 수 있습니다 (간단한 테스트 Hello World 앱으로는 7435줄이었어요). 원하는 설정을 지정하여 이를 줄일 수 있습니다. 대부분의 경우에는 compileClasspath, runtimeClasspath, testCompileClasspath, 그리고 testRuntimeClasspath를 확인할 수 있습니다. 저는 runtimeClasspath를 필요로 했고, 빌드 유형을 debug로 추가했어요:

```js
./gradlew :app:dependencies --configuration debugRuntimeClasspath
> dependencyTree.txt
```

이제 제 파일은 단지 692줄이에요. 훨씬 사용하기 편리하죠!

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

이제 우리는 모든 종속성과 포함된 순환 종속성을 확인할 수 있어요. 예를 들어:

```js
+--- androidx.navigation:navigation-compose:2.8.0-beta02
|    +--- androidx.activity:activity-compose:1.8.0 -> 1.9.0 (*)
|    +--- androidx.compose.animation:animation:1.7.0-beta02 (*)
|    +--- androidx.compose.foundation:foundation-layout:1.7.0-beta02 (*)
|    +--- androidx.compose.runtime:runtime:1.7.0-beta02 (*)
|    +--- androidx.compose.runtime:runtime-saveable:1.7.0-beta02 (*)
|    +--- androidx.compose.ui:ui:1.7.0-beta02 (*)
|    +--- androidx.lifecycle:lifecycle-viewmodel-compose:2.6.2 -> 2.8.1
|    |    \--- androidx.lifecycle:lifecycle-viewmodel-compose-android:2.8.1
|    |         +--- androidx.annotation:annotation:1.8.0 (*)
|    |         +--- androidx.compose.runtime:runtime:1.6.0 -> 1.7.0-beta02 (*)
|    |         +--- androidx.compose.ui:ui:1.6.0 -> 1.7.0-beta02 (*)
|    |         +--- androidx.lifecycle:lifecycle-common:2.8.1 (*)
|    |         +--- androidx.lifecycle:lifecycle-viewmodel:2.8.1 (*)
...
```

여기서 우리는 androidx.navigation:navigation-compose가 androidx.compose.foundation:foundation-layout:1.7.0-beta02를 포함한다는 것을 볼 수 있어요.

Gradle 문서에서는 각 종속성마다 나열된 주석 기호의 의미를 이해하는 데 도움이 되는 설명이 명확하게 제공되어 있어요.

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

하지만 여기서 androidx.navigation:navigation-compose:2.8.0-beta02가 androidx.compose.foundation:foundation-layout을 버전 1.7.0-beta02를 사용하도록 강제하는지 알 수 없어요. 큰 프로젝트에서는 문제가 되는 라이브러리의 각 언급을 모두 확인하고 비교하는 것도 매우 수고스러울 수 있어요.

# dependencyInsight로 의존성 대상 지정하기

대신, dependencyInsight를 사용하여 의존성에 대한 특정 해결 방법 정보를 얻을 수 있어요.

./gradlew :app:dependencyInsight — configuration debugRuntimeClasspath — dependency androidx.compose.foundation ` dependencyInsight.txt`

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

./gradlew :app:dependencyInsight --configuration debugRuntimeClasspath
--dependency androidx.compose.foundation > dependencyInsight.txt

여기서도 구성을 전달하고 관심 있는 종속성을 인수로 추가합니다. 또한 결과를 파일에 보내어 변경 전과 변경 후의 결과를 diff 할 수 있습니다. --scan 플래그를 사용하여 웹 버전도 있습니다.

파일 상단에는 종속성에 대한 메타데이터와 요청된 버전 및 해결된 버전에 대한 정보가 표시됩니다:

> 작업 :app:dependencyInsight
> androidx.compose.foundation:foundation:1.7.0-beta02
> Variant releaseRuntimeElements-published:

    | 속성 이름                                      | 제공     | 요청    |
    |----------------------------------------------|---------|---------|
    | org.gradle.status                             | release |         |
    | org.gradle.category                           | library | library |
    | org.gradle.usage                              | java-runtime | java-runtime |
    | org.jetbrains.kotlin.platform.type            | androidJvm | androidJvm |
    | com.android.build.api.attributes.AgpVersionAttr | | 8.6.0-alpha03 |
    | com.android.build.api.attributes.BuildTypeAttr  | | debug |
    | org.gradle.jvm.environment                     | | android |
    선택 이유:
       - 제약 조건으로 인해 foundation-layout이 atomic 그룹인 androidx.compose.foundation에 있음
       - 제약 조건으로 인해
       - 제약 조건으로 인해 Text의 심각한 버그 방지
       - 버전 1.7.0-beta02, 1.6.7, 1.4.0 및 1.6.0 사이의 충돌 해결로 인해

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

이제 해결된 버전과 그것을 요청한 종속성 목록을 볼 수 있습니다:

```js
androidx.compose.foundation:foundation:1.7.0-beta02
+--- debugRuntimeClasspath
\--- androidx.compose.foundation:foundation-layout-android:1.7.0-beta02
     +--- androidx.compose:compose-bom:2024.05.00 (androidx.compose.foundation:foundation-layout-android:1.6.7를 요청함)
     |    \--- debugRuntimeClasspath
     \--- androidx.compose.foundation:foundation-layout:1.7.0-beta02
          +--- androidx.compose:compose-bom:2024.05.00 (androidx.compose.foundation:foundation-layout:1.6.7를 요청함) (*)
          +--- androidx.navigation:navigation-compose:2.8.0-beta02
...
```

각 요청에 대한 선택 이유도 제공됩니다. 따라서 여기에서 문제 버전이 선택된 이유를 찾을 수 있습니다.

나무가 크고 어떤 라이브러리가 원인일 수 있다는 감이 있다면, 아래 설명된대로 제외를 수행한 후 명령어를 다시 실행하고 출력을 비교하여 변경된 내용을 확인할 수 있습니다.

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

# 특정 종속성 버전 강제 지정

이제 원하지 않는 추이 버전을 포함시키는 종속성 또는 종속성을 알게 되었으므로, **exclude**를 사용하여 제외할 수 있습니다. 다음은 내 예시입니다:

```js
implementation(libs.compose.navigation) {
    exclude(group = "androidx.compose.foundation", module = "foundation")
    exclude(group = "androidx.compose.foundation", module = "foundation-android")
    exclude(group = "androidx.compose.foundation", module = "foundation-layout-android")
}
```

위와 같이 사용하면 됩니다.

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

compose-navigation = { group = "androidx.navigation", name = "navigation-compose", version.ref = "2.8.0-beta02"

그리고 원하는 버전을 포함시키는 것을 잊지 마세요:

implementation(libs.compose.foundation)
implementation(libs.compose.foundation.layout)

where:

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
compose-foundation = { group = "androidx.compose.foundation", name = "foundation", version.ref = "1.6.7"}
compose-foundation-layout = { group = "androidx.compose.foundation", name = "foundation-layout-android", version.ref = "1.6.7"}
```

위와 같이 제 종속성 문제를 해결했어요. 당연히 전이 종속성 버전을 재정의하는 것은 자주 하고 싶지 않은 일이에요 (의외로 빌드 또는 런타임 오류를 발생시키기도 해요) 하지만 필요할 때는 시도해볼 가치가 있는 것이에요.
