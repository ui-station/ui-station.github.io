---
title: "안드로이드 프로젝트에 Kotlin Multiplatform Mobile KMM을 원활하게 통합하기"
description: ""
coverImage: "/assets/img/2024-05-20-SeamlesslyIntegrateKotlinMultiplatformMobileKMMintoYourAndroidProject_0.png"
date: 2024-05-20 15:55
ogImage:
  url: /assets/img/2024-05-20-SeamlesslyIntegrateKotlinMultiplatformMobileKMMintoYourAndroidProject_0.png
tag: Tech
originalTitle: "Seamlessly Integrate Kotlin Multiplatform Mobile (KMM) into Your Android Project"
link: "https://medium.com/@jyotibhambhu/seamlessly-integrate-kotlin-multiplatform-mobile-kmm-into-your-android-project-efa76004ec46"
---

<img src="/assets/img/2024-05-20-SeamlesslyIntegrateKotlinMultiplatformMobileKMMintoYourAndroidProject_0.png" />

Kotlin Multiplatform Mobile (KMM)은 Android 및 iOS 애플리케이션 간에 코드를 공유할 수 있게 해주어 개발을 더 신속하고 효율적으로 만듭니다.

본 안내서는 KMM을 기존의 Android 프로젝트에 통합하는 단계를 안내합니다. Android 앱의 고유한 기능을 희생하지 않고 공유 코드를 활용할 수 있도록 보장합니다.

필수 준비물:

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

- 기존 Android 앱.

## 단계 1: KMM 모듈 생성하기:

- Android Studio에서 File -` New -` New Module로 이동합니다.
- KMM Shared Module을 선택합니다.

<img src="/assets/img/2024-05-20-SeamlesslyIntegrateKotlinMultiplatformMobileKMMintoYourAndroidProject_1.png" />

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

새 Android Studio에서 KMM 공유 모듈을 찾을 수 없다면 걱정하지 마세요. 설정에 숨겨져 있습니다.

## 단계 2: 앱 모듈에 공유 모듈 포함하기:

- settings.gradle 파일을 열고 KMM 모듈 종속성이 추가되었는지 확인하세요 (이 작업은 자동으로 수행됩니다).
- app/build.gradle 파일에서 다음 종속성을 추가하세요:

```js
implementation project(":shared")
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

## 단계 3: Kotlin DSL용 빌드 스크립트 업데이트(필요한 경우):

만약 Android 프로젝트가 빌드에 실패한다면, KMM 모듈이 Kotlin DSL을 사용하고 있을 가능성이 높습니다. 다음과 같이 shared/build.gradle.kts에 의존성 설정을 변경하세요:

```js
plugins {
    id("com.android.library")
    id("org.jetbrains.kotlin.multiplatform")
}

kotlin {
    android()
    sourceSets {
        val androidMain by getting {
            dependencies {
                implementation("org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version")
            }
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

```kotlin
의존성 {
    구현("org.jetbrains.kotlin:kotlin-test:$kotlin_version")
}
```

## 단계3: 빌드 및 확인:

- 프로젝트를 Gradle 파일과 동기화하고 빌드가 성공적으로 완료되는지 확인합니다.
- 빌드 과정 중 발생하는 어떤 문제든 해결합니다.

## 단계4: 앱에서 공유 코드 사용:

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

- 이제 KMM 모듈의 공유 코드를 Android 프로젝트에서 사용할 수 있습니다. 예를 들어 Android 액티비티나 프래그먼트에서 필요한 대로 공유 클래스 및 함수를 가져올 수 있습니다.

## 단계5: 빌드 및 확인

- Android 프로젝트를 실행하여 모든 것이 올바르게 통합되었고 공유 코드가 기대대로 작동하는지 확인하십시오.

이러한 단계를 따라서 기존 Android 프로젝트에 KMM을 성공적으로 통합할 수 있으며, Android와 기타 플랫폼 간에 코드를 원할하게 공유하여 개발 프로세스를 강화하고 노력의 중복을 줄일 수 있습니다.

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

다음 시리즈의 내용을 기대해주세요. KMM을 기존 iOS 프로젝트에 통합하는 방법을 살펴볼 예정입니다.
