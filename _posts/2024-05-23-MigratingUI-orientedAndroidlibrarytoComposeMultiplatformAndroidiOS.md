---
title: "컴포즈 멀티플랫폼Android iOS으로 UI 중심 안드로이드 라이브러리 이주하기"
description: ""
coverImage: "/assets/img/2024-05-23-MigratingUI-orientedAndroidlibrarytoComposeMultiplatformAndroidiOS_0.png"
date: 2024-05-23 12:52
ogImage: 
  url: /assets/img/2024-05-23-MigratingUI-orientedAndroidlibrarytoComposeMultiplatformAndroidiOS_0.png
tag: Tech
originalTitle: "Migrating UI-oriented Android library to Compose Multiplatform (Android iOS)"
link: "https://medium.com/proandroiddev/migrating-ui-oriented-android-library-to-compose-multiplatform-android-ios-862129f498a9"
---


연혁을 거듭하면서, Kotlin Multiplatform이 드디어 여기 머물 것 같습니다. 그러나 비즈니스 로직만 공유하는 것이 특히 React Native, Flutter 및 기타 기술이 더 많은 일을 할 수 있는 만큼 편리하고 경쟁력이 있다고 느끼는 어색한 느낌이 아직 남아 있었습니다. 그래서 매우 기대되었던 Compose Multiplatform for iOS의 첫 번째 알파 버전을 작년에 보게 되어 정말 기뻤습니다.

퍼즐이 마침내 맞추어졌습니다. 이제 안드로이드 개발자들은 최소한의 추가 노력으로 iOS 앱을 코틀린으로 만들 수 있습니다. 그러나 이게 정말 그럴까요? 알아봅시다. 여기서는 라이브러리 이전에 대해 설명하겠습니다. 전체 애플리케이션을 이전할 때 몇 가지 점이 다를 수 있습니다.

<img src="/assets/img/2024-05-23-MigratingUI-orientedAndroidlibrarytoComposeMultiplatformAndroidiOS_0.png" />

## 내 경우에 관하여

<div class="content-ad"></div>

안녕하세요! 안드로이드/Kotlin과 iOS/Swift 클라이언트가 있습니다. 두 클라이언트 모두 다음 UI 기능을 갖춘 자체 제작 라이브러리를 사용합니다:

- 정적으로 로드할 수 있는 이미지
- 로드 가능한 GIF
- 비디오 (CDN으로부터 스트리밍 로딩)
- 모든 항목은 수직 목록에 수평 목록이 내장됩니다

추가로:

- 네트워크 통신
- 디스크 캐싱

언제든지 물어보세요!

<div class="content-ad"></div>

```markdown
![image](https://miro.medium.com/v2/resize:fit:472/1*4Eb5MmEXdZwZC8WkYBB6yA.gif)

우리에게는 Compose Multiplatform 기능과 성능을 테스트하는 좋은 시작점이었습니다. 무엇이 문제가 될 수 있는지 알고 싶다면, 여기에서 확인해보세요.

## 왜 Compose Multiplatform 인가요?

일부 소스에서 Jetpack Compose를 선언적 API로 설명합니다. 저에게 있어 Jetpack Compose의 주요 장점은 전통적인 XML 레이아웃과 비교했을 때의 유연성입니다. 그리고 Jetpack Compose에서 레이아웃을 작성한 후에는 Compose Multiplatform로 이전하는 것이 정말 어렵지 않습니다.
```

<div class="content-ad"></div>

비즈니스적인 관점에서 주요 방해 요인이 없이 코드베이스를 점진적으로 통합하는 것이 더 쉽습니다. 추가 개발자가 필요하지 않습니다. 그것이 Compose Multiplatform이 허용하는 것입니다.

Jetbrains에 따르면, 이것이 어떻게 작동하는지 간단히 상기시켜 드리겠습니다:

![Jetbrains Image](/assets/img/2024-05-23-MigratingUI-orientedAndroidlibrarytoComposeMultiplatformAndroidiOS_1.png)

## 코드베이스에서의 장애물

<div class="content-ad"></div>

가끔 우리는 모두 KMP 코드가 아닌 코드를 작성하거나 사용합니다. 때로는 많을 때도 있죠.

전형적인 예시는 다음과 같습니다:

- Java 코드
- Dagger 2, RxJava, Retrofit 등과 같은 호환되지 않는 라이브러리 종속성
- XML 레이아웃, 뷰, 프래그먼트
- 안드로이드 서비스, 푸시 알림, 인앱 구매
- 그 밖에도...

여기에는 주로 2가지 해결책이 있습니다:

- 코드를 다시 작성하거나 KMP 호환 라이브러리로 이전하여 commonMain 폴더에 넣습니다.
- 호환되지 않는 코드를 플랫폼별 서브모듈인 androidMain 및 iosMain에 넣습니다.

<div class="content-ad"></div>

사실 platform-specific 하위 모듈에 항상 코드를 넣을 수 있습니다. 그러나 이 경우에는 iOS용으로 코드를 작성하거나 iOS에서도 구현해야 합니다.

## iOS에 특정한 코드를 작성하는 방법은?

iOS 플랫폼별 코드를 정의하거나 사용하는 데는 2가지 주요 옵션이 있습니다:

옵션 1.
iosMain 폴더에 구현된 Expect/actual 함수.
다음은 구조가 어떻게 보이는지 예시입니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-MigratingUI-orientedAndroidlibrarytoComposeMultiplatformAndroidiOS_2.png" />

옵션 2.
iOS 측에서 호출할 수 있는 몇 가지 커넥터를 제공합니다.
다음은 commonMain에서 빈 로거를 정의하는 예입니다:

```kotlin
object Bridge {
    var logger: (String) -> Unit = {}
    ...
}
```

iOS/Swift 앱에서 로거에 대한 구현을 할당합니다:

<div class="content-ad"></div>

```js
import ...

Bridge.shared.logger = {
    print("ios log: " + $0)
}
```

그냥 간단한 경우일 뿐입니다. 사실 거의 모든 것을 이 방법으로 연결할 수 있습니다. commonMain에서 인터페이스를 정의하고 전체 구현을 별도로 Swift로 작성하는 것도 가능합니다.

물론 작성해야 하는 플랫폼별 코드가 많아질수록 더 나쁠 수 있습니다. 제 상황에서는 iosMain에 전체 라이브러리 코드의 약 9%가 있고, androidMain에는 약 13%가 있습니다. 이 비율은 여러분의 상황에서는 낮을 수도 있습니다.

## 어떻게 마이그레이션을 시작할까요?

<div class="content-ad"></div>

우리는 Android/Kotlin 및 iOS/Swift 클라이언트를 보유하고 있습니다. 통합을 시작하는 방법은 무엇일까요?

Android의 경우, 기존 앱 또는 라이브러리에 KMP(코틀린 멀티플랫폼)과 호환되는 코드를 단계별로 다시 작성하여 통합할 수 있습니다. 언어는 그대로 유지되지만 의존하는 접근 방식과 라이브러리는 변경될 수 있습니다.

간단한 경우에는 다음과 같습니다:

- 코드베이스에 KMP 모듈을 만듭니다.
- 코드를 공통 모듈(commonMain)로 순차적으로 이동시킵니다.
- 플랫폼에서 이동할 수 없는 부분에 대한 커넥터를 만듭니다.

<div class="content-ad"></div>

우리는 우리의 경우에 iOS와 안드로이드 클라이언트가 의존하는 UI 중심 라이브러리를 마이그레이션하기로 결정했습니다. 그 라이브러리를 완전히 다시 작성해야 했던 이유는 RxJava와 XML 레이아웃을 기반으로 했기 때문이었습니다.

## 우리의 주요 기술 변화

RxJava → Coroutines / Flow
만약 Rx를 사용 중이라면 Flow로 마이그레이션하거나 Reaktive와 같은 KMP 대안을 사용해야 합니다.

Retrofit → Ktor
Ktor는 매우 편리한 네트워크 라이브러리입니다. 큰 문제는 없었습니다. 여러 번 검색해서 익숙했던 것을 어떻게 쓰는지 찾아보면 됩니다.

<div class="content-ad"></div>

Room → Room?
제 경우에는 일반 디스크 캐싱을 Okio로 대체하는 것이 허용되었습니다. 하지만 사실 Room은 KMP도 지원합니다.

Glide → Coil 3 + iOS용 자체 GIF 구현.
Coil 3는 아직 알파 버전이지만 동작합니다. 제 경우 문제는 iOS에서 GIF를 재생할 수 없었던 것이었습니다. 안타깝게도 몇 일 동안 문제를 해결하고 디스크 캐싱을 통해 해결책을 구현하는 데 시간이 걸렸습니다.

제 경우 이미지에 대한 기본 계약:

```js
@Composable
expect fun LoadableImage(
    modifier: Modifier,
    url: String,
    imageColorFilter: ColorFilter? = null,
    size: Size? = null,
)
```

<div class="content-ad"></div>

Jetpack ExoPlayer → ExoPlayer + AVPlayer
우리는 각 플랫폼마다 플레이어를 대체하기 위해 expect/actual을 사용합니다.
ExoPlayer는 디스크 캐싱 및 스트리밍 재생 기능을 갖춘 안드로이드용 강력한 솔루션입니다.
AvPlayer는 iOS의 기본 솔루션입니다.

플레이어의 기본 계약:

```js
@Composable
expect fun VideoPlayer(
    modifier: Modifier, 
    url: String, 
    volumeEnabled: State<Boolean>,
)
```

## 안드로이드용 빌드 방법

<div class="content-ad"></div>

안드로이드의 경우 일반적인 안드로이드 라이브러리와 유사합니다. 이 라이브러리는 애플리케이션의 모듈로 사용할 수 있습니다. 현재는 이것이 우리의 선택입니다.

다른 옵션으로 그레이들 복합 빌드를 사용할 수도 있습니다. 자세한 내용은 다음 기사를 참조하세요:

마지막으로, 그레이들 작업 bundleReleaseAar을 사용하여 라이브러리를 .aar 파일로 조립할 수 있습니다.

## iOS용 빌드 방법

<div class="content-ad"></div>

로컬 개발 중에는 XCFramework을 빌드하고 iOS 프로젝트에 넣습니다. 프로세스는 여기에 설명되어 있습니다:

간단히 말하면 다음과 같습니다:

- Gradle 작업을 호출합니다.
로컬 빌드(빠름)의 경우 iosX64Binaries 또는 iosArm64Binaries를 사용하거나 최종 이진 파일의 경우 assembleReleaseXCFramework를 사용합니다(느림).
- build/bin/iosArm64/releaseFramework (또는 유사한 경로)에서 결과물을 iOS 프로젝트로 복사합니다.
- Xcode에 의해 동기화될 때까지 잠시 기다립니다.
- 완료. iOS 프로젝트에서 Kotlin 코드를 사용하세요.

자동화된 CI/CD 파이프라인에서는 프로세스가 약간 다르지만 이는 다른 이야기입니다.

<div class="content-ad"></div>

## 나의 경우의 결과

현재 저희 이주 작업은 사전 제작 단계에 있지만, 이미 몇 가지 결론을 도출할 수 있습니다.

🟢 기능성.
저희 라이브러리의 모든 주요 기능이 이주되었습니다.
일부 비중요한 예외를 제외하고 기능 요구 사항이 충족되었습니다.

🟢 코드베이스.
이와 같이 재작성하는 것은 많은 레거시 코드를 제거할 수 있는 기회입니다.
숫자:
이주 이전: 10,000 줄 코드, 1,200 줄 XML
이후: 4,000 줄 코드
결과: 기존 버전과 비교했을 때 코드 양이 약 2.5배 감소했습니다.

<div class="content-ad"></div>

🟢 성능.
iOS에서는 iPhone SE에서도 부드럽게 스크롤됩니다.
저사양 안드로이드 기기에서는 XML 버전과 비교했을 때 그렇게 부드럽지 않지만, 심각한 문제는 아닙니다.

🟡 개발 생산성.
일반적으로, 현대적인 안드로이드 개발과 Jetpack Compose에 익숙하다면 주요 문제가 없을 것입니다. 다만 제 경험상 한 가지 예외가 있습니다: Android Studio 및 IDEA에는 미리보기가 없습니다. Fleet에서는 작동하지만 AS/IDEA에서는 Composable 함수를 블라인드로 작성해야 합니다.

🟡 이진 크기.
Android APK: + 0.5 MB
iOS IPA: + 18 MB

🟡 라이브러리 크기.
거대한 XCFrameworks 크기. 300MB 이상입니다.

<div class="content-ad"></div>

🔴 빌드 시간.
iOS의 빌드 시간이 길어요. 기계에 따라 다르지만 라이브러리 4k 줄의 코드를 가지고 17분 같은 숫자를 볼 수 있어요.

## 결론

2024년에는 Kotlin으로 크로스 플랫폼 UI를 작성하는 것이 전혀 가능합니다.

KMP와 Compose Multiplatform의 멋진 점은 Kotlin으로 코드를 작성해야 한다면, 왜 KMP 호환 방식으로 작성하지 않을까요?

<div class="content-ad"></div>

우리의 경우에는 결과가 수용 가능하며, 스트레스 테스트로 간주할 수 있습니다. 또한 iOS용 Compose Multiplatform이 알파 단계에 있고, Kotlin 2가 미래에 있음을 염두에 두어야 합니다.

읽어 주셔서 감사합니다!