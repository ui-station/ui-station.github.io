---
title: "친구야, Kotlin 20 - Android 프로젝트 이전 안내"
description: ""
coverImage: "/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_0.png"
date: 2024-05-20 17:39
ogImage:
  url: /assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_0.png
tag: Tech
originalTitle: "Kotlin 2.0 — Android project migration guide"
link: "https://medium.com/@kacper.wojciechowski/kotlin-2-0-android-project-migration-guide-b1234fbbff65"
---

![Kotlin 2.0 Release](/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_0.png)

코틀린은 처음 출시된 이후로 많은 여정을 거쳐왔습니다. 이제 새로운 이정표에 접어들고 있는데요—2.0 릴리스입니다. 이 글을 작성하는 시점에서 2.0.0-RC3 버전이 릴리스되었는데, 이는 거의 최종 버전에 가까운 버전입니다. 저는 코틀린 EAP의 구성원이었고, 내 프라이빗 안드로이드 프로젝트의 설정을 변경하여 코틀린 2.0에 적응하는 과정을 간단히 알려드리고 싶습니다.

# K2 컴파일러

이것은 새로운 코틀린 버전의 가장 큰 기능일 것입니다. JetBrains 팀은 2배 빠른 컴파일 시간을 약속하고 있습니다. 현실은 이 "2배"는 아마도 컴파일 작업의 합에만 해당할 것입니다. 실제 세계에서, Gradle은 몇 가지 작업을 병렬로 실행하여 이득 중 일부를 상쇄시키게 됩니다. 제 경험상 15–30% 정도의 향상이 되었다고 할 수 있습니다.

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

JetBrains 팀은 IntelliJ IDEA 기반 IDE의 경험을 크게 향상시킬 것이라고 말합니다. K2 모드로 이클립스는 프로젝트를 작성하면서 컴파일하며 맞춤법 검사, 코드 자동 완성 등을 제공합니다. 새로운 K2 모드로 작성 경험이 더욱 원활해질 것이라고 약속하지만, 현재 Alpha 단계이며 버그가 많습니다.

## 이전 방법

재미있는 사실: K2 컴파일러는 이전 코틀린 버전에서 gradle.properties 플래그와 함께 실험적 버전으로 사용할 수 있었습니다! 코틀린 2.0부터 K2 컴파일러가 기본 컴파일러로 설정되어 더 이상 아무것도 할 필요가 없습니다. 코틀린 버전을 2.0으로 업데이트하면 자동으로 활성화됨을 기억하세요.

# .kotlin — 새로운 빌드 디렉토리

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

코틀린 2.0에서는 새로운 빌드 출력 디렉토리인 .kotlin이 소개되었습니다. 코틀린 1.8.20부터 코틀린 컴파일러는 일부 컴파일 데이터를 Gradle 디렉토리에 저장하기 시작했지만, 해당 디렉토리는 Gradle 데이터를 위해 예약되어 있습니다. 코틘린 2.0에서 팀은 Kotlin 컴파일 데이터를 위해 예약된 새로운 디렉토리인 .kotlin으로 마이그레이션하기로 결정했습니다.

## 마이그레이션 방법

이것이 새로운 빌드 디렉토리이므로 그 내용이 커밋에 나타나지 않도록하기 위해 .gitignore 파일에 추가해야 합니다:

```js
# Kotlin 2.0
.kotlin/
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

만약 이 디렉토리가 프로젝트에 적합하지 않다면, kotlin.project.persistent.dir gradle 속성을 사용하여 변경할 수 있습니다.

# kotlinOptions 호출이 deprected되었습니다

![이미지](/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_1.png)

이전에는 JDK 버전을 설정하거나 일부 컴파일러 인수를 추가하기 위해 모든 KotlinCompile 작업을 설정해야 했고, 그 작업을 위해 kotlinOptions 개체에 액세스해야 했습니다. 그러나 Kotlin 2.0부터 이 접근 방식은 deprected되었으며, 이러한 설정을 수행할 수 있는 새 API가 있습니다.

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

## 이전 방법

compilerOptions를 소개해 드리겠습니다:

![이미지](/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_2.png)

Gradle 규칙 플러그인용 버전은 여기 있습니다:

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

<img src="/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_3.png" />

# Compose 컴파일러 버전

2.0 버전 이전에는 Kotlin Compose 컴파일러 버전을 수동으로 제공해야 했습니다:

<img src="/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_4.png" />

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

현재 코틀린 버전과 호환되어야 하므로 유지보수가 다소 귀찮았습니다. Compose 버전과 다르며 IDE에서 버전 업그레이드를 권장하지 않았습니다. 코틀린 버전을 업그레이드할 때마다 호환되는 Compose 컴파일러 버전을 구글에서 검색해야 했죠. 코틀린 2.0 버전에서 이 문제가 해결되었습니다.

## 이주하는 방법

Compose 컴파일러 버전이 코틀린 버전과 강하게 결합되어 있기 때문에, 코틀린 버전과 함께 Compose 컴파일러 버전도 받아서 더는 걱정하지 않을까요? 구글과 JetBrains가 이 결론에 도달했고, Kotlin 2.0 작업 중에 이를 해결하고 JetBrains가 Compose 컴파일러를 쉽게 제공할 수 있도록 JetBrains 리포지토리로 이동하였습니다. 이제 여러분을 위해 모든 것을 설정해 줄 새로운 Gradle 플러그인을 소개할게요:

```js
[plugins]
kotlin-compose-compiler = { id = "org.jetbrains.kotlin.plugin.compose", version.ref = "kotlin" }
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

Compose 모듈에 플러그인을 적용하세요:

![image](/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_5.png)

그리고 끝입니다! 이 플러그인은 현재 사용 중인 Kotlin 버전을 기반으로 Compose 컴파일러 버전을 설정해 줍니다. 또한 Compose 설정을 위한 새로운 설정 블록을 도입했습니다:

![image](/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_6.png)

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

그리고 Gradle 관례 플러그인용 버전도 있습니다:

```js
[라이브러리]
# ComposeCompilerGradlePluginExtension 클래스에 액세스하려면 gradle 관례 모듈에 구현합니다
plgn-kotlin-compose-compiler = { module = "org.jetbrains.kotlin:compose-compiler-gradle-plugin", version.ref = "kotlin" }
```

<img src="/assets/img/2024-05-20-Kotlin20Androidprojectmigrationguide_7.png" />

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

지금까지 프로젝트 설정에 변경된 내용이 이것뿐입니다. 많이 변경된 것 같지는 않지만 대부분의 변경 사항은 백그라운드에서 일어났습니다. 앞으로 코틀린 언어 개발자들이 제공할 기능이 어떤 것인지 기대해 봅시다 (명시적 백킹 필드는 이미 실험적입니다🤞).

만약 새로운 산출물을 확인하고 Android 프로젝트 설정에서 더 많은 변경 사항을 발견했다면 자유롭게 코멘트해 주세요. 새로운 내용이 이곳에 언급할 가치가 있는 것으로 생각된다면 기사를 업데이트하겠습니다.
