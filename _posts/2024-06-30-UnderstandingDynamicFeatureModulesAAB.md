---
title: "동적 기능 모듈 이해하기 AAB Android App Bundle 효과적으로 사용하기"
description: ""
coverImage: "/assets/img/2024-06-30-UnderstandingDynamicFeatureModulesAAB_0.png"
date: 2024-06-30 19:16
ogImage: 
  url: /assets/img/2024-06-30-UnderstandingDynamicFeatureModulesAAB_0.png
tag: Tech
originalTitle: "Understanding Dynamic Feature Modules , AAB"
link: "https://medium.com/@shashankkumar45556/understanding-dynamic-feature-modules-aab-06d7dcdfea80"
---


플레이 피처 딜리버리는 앱 번들의 고급 기능을 활용하여 앱의 특정 기능을 조건적으로 제공하거나 필요 시 다운로드할 수 있게 합니다. 먼저 기본 앱에서 이러한 기능을 분리하여 피처 모듈로 만들어야 합니다.

Google Play의 앱 제공 모델은 Android 앱 번들을 활용하여 각 사용자의 기기 구성에 맞게 최적화된 APK를 생성하고 제공하므로 사용자는 앱을 실행하는 데 필요한 코드 및 리소스만 다운로드합니다.

## Android 앱 번들 분석

"Android 앱 번들은 모든 앱의 컴파일된 코드와 리소스를 포함하며 APK 생성과 서명을 Google Play에게 위임하는 게시 형식입니다."

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

어플 번들과 APK는 다르다는 것을 아시나요? 어플 번들은 기기에 직접 배포할 수 없는 반면, 모든 앱의 컴파일된 코드와 리소스를 포함한 출판 포맷입니다. 따라서, 서명된 앱 번들을 업로드하면 Google Play가 APK를 빌드하고 서명하여 사용자에 제공하는 데 필요한 모든 것을 보유하게 됩니다.

![image](/assets/img/2024-06-30-UnderstandingDynamicFeatureModulesAAB_0.png)

여러 모듈을 포함하는 Android App Bundle을 분석해보겠습니다. 구체적으로 dynamicFeature, native, java, kotlin, assets 모듈들이 있는데, 이들은 이 프로젝트를 위해 만든 동적 기능 모듈입니다.

위 내용은 Android 스튜디오의 앱 번들 분석 기능에서 나온 어플 번들의 내용을 나타낸 것입니다. 일반적으로, 어플 번들의 모듈은 다음과 같은 것으로 구성됩니다.

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

- Dex 파일 - DEX 파일의 주요 목적은 컴파일된 코드를 Dalvik Virtual Machine (DVM) 또는 Android Runtime (ART)이 실행할 수 있는 형식으로 패키징하는 것입니다.
- Manifest — AndroidManifest.xml 파일은 모든 Android 애플리케이션에 필요한 XML 파일로, 앱에 관한 중요한 정보를 설명합니다. 각 동적 기능 모듈은 고유의 AndroidManifest.xml 파일을 갖습니다. 일반적으로 해당 모듈에 특정한 구성 요소가 포함되어 있고 권한 및 기능을 지정할 수 있습니다.
- Resources — 앱이 사용하는 다양한 리소스를 구성하고 관리하는 데 중요합니다. 이러한 리소스에는 레이아웃, 이미지, 문자열, 애니메이션, 스타일 등이 포함됩니다.
- root — 이 디렉토리에는 나중에 해당 디렉토리가 위치한 모듈을 포함하는 APK의 루트로 재배치되는 파일이 저장됩니다. 예를 들어, 앱 번들의 base/root/ 디렉토리에는 앱이 Class.getResource()를 사용하여 로드하는 자바 기반 리소스가 포함될 수 있습니다.

우리가 볼 수 있듯이, 기본 모듈을 위한 별도의 모듈이 생성됩니다. — 안드로이드 앱 번들 내의 기본 모듈은 앱의 핵심 기능을 포함합니다.

![이미지](/assets/img/2024-06-30-UnderstandingDynamicFeatureModulesAAB_1.png)

Google Play에서 안드로이드 앱 번들을 처리할 때, 특정 장치 구성에 맞게 여러 개의 APK로 분할됩니다. 기본 분할 APK은 기본 모듈에서 생성되며 앱 시작에 필요한 핵심 기능과 리소스를 포함합니다. 이 APK는 필수적이며 모든 장치에 설치되어 앱이 모든 지원되는 장치에서 실행될 수 있도록 합니다.

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

기본 분할 APK에 추가로 Android 앱 번들은 구성 APK라고 하는 여러 다른 APK를 생성합니다. 이러한 APK는 다양한 장치 구성에 최적화되어 있으며 다음을 포함할 수 있습니다:

- 밀도 APK: 다양한 화면 밀도에 대한 것 (예: mdpi, hdpi, xhdpi).
- 언어 APK: 다른 언어 및 로캘을 위한 것.
- ABI APK: 다양한 CPU 아키텍처에 대한 것 (예: armeabi-v7a, arm64-v8a).
- 동적 기능 APK: 필요시 다운로드할 수 있는 선택적 기능에 대한 것.

앱 번들의 각 동적 기능 모듈에 대해 하나의 분할 APK가 생성될 것입니다. 이러한 각 분할 APK는 사용자가 요청하거나 장치에서 필요할 때 다운로드되어 설치됩니다.

이외에도 앱 번들의 다른 구성 요소들이 있습니다 -

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

- BUNDLE-METADATA: 이 디렉토리에는 도구나 앱 스토어에서 유용한 정보를 포함하는 메타데이터 파일이 포함됩니다. 이러한 메타데이터 파일에는 ProGuard 매핑 및 앱의 DEX 파일 완전한 목록 등이 포함될 수 있습니다. 이 디렉토리의 파일은 앱의 APK에 포장되지 않습니다.
- 모듈 프로토콜 버퍼 (*.pb) 파일: 이러한 파일은 Google Play와 같은 앱 스토어에 각 앱 모듈의 내용을 설명하는 데 도움이 되는 메타데이터를 제공합니다.

## 앱 번들이 생성되는 방식

AGP 코드를 더 깊이 파고들어 Android 앱 번들이 생성되는 방식을 이해해 보겠습니다.

PackageBundleTask.kt는 Android 앱 번들 (.aab 파일)을 빌드하기 위한 Gradle 태스크입니다. 이 태스크는 애플리케이션의 다양한 모듈과 구성 요소를 최종 .aab 파일로 패키징합니다.

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

작업은 필요한 모든 입력을 수집합니다. 이는 기능 모듈 zip 파일, 자산 팩 zip 파일, 메인 dex 목록, 난독화 매핑 파일, 무결성 구성 파일 및 네이티브 디버그 메타데이터 파일 등을 포함합니다. 이러한 입력은 Gradle에 의해 처리되어야 하는 방식을 나타내기 위해 속성 및 어노테이션을 사용하여 지정됩니다 (예: 선택적인지, 경로 변경에 민감한지 등).

작업은 ABI 분할, 밀도 분할, 언어 분할, 텍스처 분할 및 기기 티어 분할과 같은 번들 옵션을 구성합니다. 이러한 구성을 캡슐화하기 위해 BundleOptions를 사용하고 이를 BundleTool 명령에 적용합니다.

ABI, 밀도 및 언어에 의한 분할과 같은 구성 옵션은 동적 기능 모듈에도 적용되며, 기기 기능과 사용자 요구에 기반한 맞춤형 APK 생성이 가능하게 합니다.

작업은 메타데이터 파일 및 최적화 설정을 설정합니다. 이에는 압축 설정, 무결성 구성, 네이티브 라이브러리 압축 및 기타 번들별 설정이 포함됩니다.

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

`BundleToolWorkAction` 클래스는 Google이 제공하는 도구인 BundleTool을 사용하여 BuildBundleCommand를 구성하고 실행합니다. 이 도구는 AAB 파일을 빌드하는데 사용됩니다. 명령은 수집된 모든 입력, 구성 및 메타데이터로 구성되어 최종 .aab 파일을 생성하도록 설정됩니다.

## 분할 APK가 생성되는 방법

`BuildApksManager` 클래스는 Android Bundle Tool의 일부로서 Android App Bundle (AAB)에서 APK를 생성하는 프로세스를 관리합니다.

`BuildApksManager`의 `execute()` 메서드는 명령 옵션과 번들 구성을 기반으로 생성할 APK의 유형을 결정하며, 특히 분할 APK의 경우 어떤 모듈이 분할되어야 하는지 및 그들의 구성 (예: dimensions)을 식별합니다.

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

`BundleModuleMerger` 클래스의 `mergeNonRemovableInstallTimeModules` 메서드는 특정 조건에 따라 기본 모듈에 병합할 모듈을 식별합니다. 예를 들어, 제거 가능으로 표시된 모듈들을 병합하지 않습니다 (`dist:removable dist:value="true" /`). 이 메서드는 식별된 모듈에서 자산 (Assets), 네이티브 라이브러리 (NativeLibraries), 및 리소스 테이블 (ResourceTable)과 같은 설정을 기본 모듈로 병합하고, 병합된 모듈을 제외한 업데이트된 AppBundle을 구성합니다.

이 병합된 앱 번들은 그런 다음 `SplitApkGenerator` 클래스의 `generateSplits` 메서드를 사용하여 Split APK를 생성하는 데 사용됩니다. `SplitApksGenerator` 클래스는 Android App Bundle (AAB)에서 Split APK를 생성하는 것을 담당합니다.

`generateSplits` 메서드는 제공된 모듈과 구성에 기반한 APK 분할을 생성합니다. 이는 variant targetings를 생성하기 위해 `variantTargetingGenerator`로 위임하고, 각 variant에 대해 분할을 생성하기 위해 `generateSplitApks`를 사용하여 해당 variant targeting에 대해 Split APK를 생성합니다. 이때 `modulesForVariant`를 통해 각 모듈을 주어진 구성 및 variant targetings에 따라 APK로 분할하기 위해 `ModuleSplitter`를 사용합니다.
`SplitApkGenerator`에서 주목해야 할 중요한 점은, 이 클래스가 동적 기능 모듈을 관리하기 위한 특정 구성 (featureModulesCustomConfig)을 허용한다는 것입니다. 이를 통해 동적 기능 모듈의 최적화 및 분할 APK에 포함 여부를 제어할 수 있습니다.

`featureModulesCustomConfig`는 `SplitApksGenerator`에 주입되는 선택적 구성 매개변수입니다. 개발자들은 동적 기능 모듈에 대해 맞춤 규칙 및 구성을 지정할 수 있습니다. 예를 들어, 특정 모듈을 다르게 압축해야 하는지 또는 특정 최적화에서 제외해야 하는지를 지정할 수 있습니다. 동적 기능 모듈은 `featureModulesCustomConfig`에 구성된 내용에 따라 분할 APK에서 선택적으로 포함하거나 제외될 수 있습니다.

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

GeneratedApks.fromModuleSplits() 메서드는 각 변형을 개별적으로 처리하여 split.xml 및 로캘 구성과 같은 필요한 구성을 ModuleSplits에 주입합니다.

모든 APK가 생성된 후, ApkSerializerManager를 사용하여 APK 세트를 지정된 출력 형식으로 직렬화하며, apkSerializerManager.serializeApkSet() 메서드는 tempDir.getPath()에서 생성된 ApkSetWriter를 사용하여 APK를 최종 APK 세트로 직렬화합니다. 이 방법은 동적 기능 및 장치별 구성을 처리하면서 테스트 및 퓨즈된 모듈 필요에 기반한 사용자 정의를 허용하여 APK를 효율적으로 패키징하여 배포합니다.