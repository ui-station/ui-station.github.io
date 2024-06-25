---
title: "처음으로 Kotlin Multiplatform KMM 프로젝트를 Android Studio에서 빌드하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_0.png"
date: 2024-06-23 23:47
ogImage:
  url: /assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_0.png
tag: Tech
originalTitle: "First Kotlin Multiplatform (KMM) project to build in Android Studio"
link: "https://medium.com/@paritasampa95/first-kotlin-multiplatform-kmm-project-to-build-in-android-studio-14eba6ac5c6e"
---

![이미지](/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_0.png)

코틀린 멀티플랫폼(KMP)은 코틀린 프로그래밍 언어의 기능으로, 개발자가 단일 코드베이스를 사용하여 Android, iOS, JavaScript 및 JVM과 같은 여러 플랫폼에서 실행할 수 있는 코드를 작성할 수 있게 해줍니다.

이 방식은 코드 재사용을 극대화하고 중복을 줄이기 위한 것으로, 공통 로직을 다른 플랫폼 간에 공유하면서 필요한 경우에 플랫폼별 코드를 사용할 수 있도록 합니다.

# Kotlin 멀티플랫폼의 장점

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

- 코드 재사용성: 공통 로직을 한 번 작성하고 여러 플랫폼에서 재사용하여 코드 중복 및 유지 보수 노력을 줄입니다.
- 일관성: 동일한 코드베이스를 공유함으로써 다양한 플랫폼에서 일관된 동작을 보장합니다.
- 생산성: 비즈니스 로직 및 기타 UI 이외의 구성 요소를 공유하여 개발 속도를 높입니다.
- 유연성: 공유 코드베이스를 유지한 채 필요에 따라 플랫폼별 API 및 라이브러리를 사용합니다.

이제 코드 작성을 시작하고 Android Studio에서 KMM 프로젝트가 어떻게 작동하는지 확인해보겠습니다.

- Android Studio에 KMM 플러그인을 추가하고 플러그인이 추가되도록 IDE를 재시작합니다.

![KMM project in Android Studio](/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_1.png)

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

2. 안녕하세요! 안드로이드 스튜디오에서 새로운 KMM 앱을 만들고 있습니다. 프로젝트 구조가 아래와 같이 보일 것입니다.

![프로젝트 구조](/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_2.png)

3. KMM 프로젝트의 shared 폴더는 코드 재사용성, 일관성, 효율적인 개발 목표를 달성하는 데 근본적인 역할을 합니다. 비즈니스 로직을 중앙 집중화하여 중복을 줄이고 유지보수를 간소화함으로써 더 나은 협업을 도모하고 플랫폼 간 일관된 동작을 보장합니다. 공유 코드를 활용함으로써 개발자들은 더 효율적이고 적은 리소스로 견고하고 기능이 풍부한 애플리케이션을 개발할 수 있습니다.

![공유 폴더](/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_3.png)

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

4. 리소스 및 이미지를 추가하기 위해 The IceRock Moko 라이브러리(Mobile Kotlin)를 사용할 예정입니다. 이 라이브러리는 Kotlin Multiplatform Mobile (KMM) 애플리케이션 개발을 간편하게 하는 데 사용되는 라이브러리 및 도구 세트입니다. 이러한 라이브러리는 iOS 및 Android를 위한 공유 코드 작성을 지원하여 개발자가 효율적으로 크로스 플랫폼 모바일 애플리케이션을 생성할 수 있도록 합니다. IceRock Moko는 네트워크 요청, 리소스, MVVM 아키텍처, 권한 처리 등과 같은 모바일 개발의 다양한 측면을 다루는 다양한 라이브러리를 제공합니다.

5. build.gradle.kts (:shared) 업데이트

```js
plugins {
    kotlin("multiplatform")
    id("com.android.library")
    id("dev.icerock.mobile.multiplatform-resources")
}

kotlin {
    android {
        compilations.all {
            kotlinOptions {
                jvmTarget = "1.8"
            }
        }
    }
    task("testClasses")

    listOf(
        iosX64(),
        iosArm64(),
        iosSimulatorArm64()
    ).forEach {
        it.binaries.framework {
            baseName = "shared"
            isStatic = true
            export("dev.icerock.moko:resources:0.22.3")
            export("dev.icerock.moko:graphics:0.9.0")
        }
    }

    sourceSets {
        val commonMain by getting {
            dependencies {
                api("dev.icerock.moko:resources:0.22.3")
            }
        }

       val  commonTest by getting {
           dependencies{
               implementation(libs.kotlin.test)
           }
        }
        val androidMain by getting
        val iosX64Main by getting
        val iosArm64Main by getting
        val iosSimulatorArm64Main by getting
        val iosMain by creating {
            dependsOn(commonMain)
            iosX64Main.dependsOn(this)
            iosArm64Main.dependsOn(this)
            iosSimulatorArm64Main.dependsOn(this)
        }
        val iosX64Test by getting
        val iosArm64Test by getting
        val iosSimulatorArm64Test by getting
    }
}

android {
    namespace = "com.tokai.mobile.coffeeworld"
    compileSdk = 34
    defaultConfig {
        minSdk = 24
    }
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
}
multiplatformResources {
    multiplatformResourcesPackage = "com.tokai.mobile.coffeeworld"
    multiplatformResourcesClassName = "SharedRes"
}
```

6. build.gradle.kts(:androidApp) 업데이트

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
플러그인 {
    id("com.android.application")
    kotlin("android")
}

android {
    namespace = "com.tokai.mobile.coffeeworld.android"
    compileSdk = 34
    defaultConfig {
        applicationId = "com.tokai.mobile.coffeeworld.android"
        minSdk = 24
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"
    }
    buildFeatures {
        compose = true
    }
    composeOptions {
        kotlinCompilerExtensionVersion ="1.4.4"
    }
    packaging {
        resources {
            excludes += "/META-INF/{AL2.0,LGPL2.1}"
        }
    }
    buildTypes {
        getByName("release") {
            isMinifyEnabled = false
        }
    }
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_1_8
        targetCompatibility = JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = "1.8"
    }
}

dependencies {
    implementation(projects.shared)
    implementation("androidx.compose.ui:ui-tooling:1.6.8")
    implementation("androidx.compose.ui:ui-tooling-preview:1.6.8")
    implementation("androidx.compose.foundation:foundation:1.4.4")
    implementation("androidx.compose.material:material:1.4.0")
    implementation("androidx.activity:activity-compose:1.7.0")
    implementation("org.jetbrains.kotlin:kotlin-test:1.8.10")
    implementation("androidx.compose.material3:material3:1.1.2")
}
```

7. build.gradle.kts(CoffeeWorld)를 업데이트 합니다

```js
플러그인 {
    // 동일한 플러그인 버전을 모든 서브 모듈에서 사용하기 위한 트릭
    id("com.android.application").version("8.0.1").apply(false)
    id("com.android.library").version("8.0.1").apply(false)
    kotlin("android").version("1.8.10").apply(false)
    kotlin("multiplatform").version("1.8.10").apply(false)
}
buildscript {
    dependencies {
        classpath("dev.icerock.moko:resources-generator:0.22.3")
    }
}

tasks.register("clean", Delete::class) {
    delete(rootProject.buildDir)
}
```

Android Studio에서 프로젝트를 다시 빌드하세요

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

8. 안드로이드 스튜디오에서 다음 버전들이 언급되었습니다.

```js
agp = "8.0.1";
kotlin = "1.8.10";
compose = "1.4.4";
```

```js
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-8.0-bin.zip
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
```

9. 이제 공유 폴더인 commonMain 내 KMM 프로젝트에서 문자열 파일을 만들어 보겠습니다. resources라는 새 디렉토리를 만들었고, MR/base 및 MR/de를 만들었습니다. 영어 및 독일어 언어용 두 개의 문자열 파일을 생성했습니다.

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

<img src="/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_4.png" />

MR/base/strings.xml

```js
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <string name="hello_world">안녕 세계</string>
    <string name="hello_x">안녕 %s</string>
</resources>
```

MR/de/strings.xml

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
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <string name="hello_world">Hallo Welt</string>
    <string name="hello_x">Hallo %s</string>
</resources>
```

10. shared 폴더 아래 iosMain과 androidMain에 String.kt 파일을 추가합니다.

![image](/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_5.png)

iosMain/Strings.kt

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
패키지 com.tokai.mobile.coffeeworld

import dev.icerock.moko.resources.StringResource
import dev.icerock.moko.resources.desc.Resource
import dev.icerock.moko.resources.desc.StringDesc
import dev.icerock.moko.resources.format

실제 클래스 Strings() {
    실제로 fun get(id: StringResource, args: List<Any>): String {
        return if (args.isEmpty()) {
            StringDesc.Resource(id).localized()
        } else {
            id.format(*args.toTypedArray()).localized()
        }
    }
}
```

androidMain/Strings.kt

```js
패키지 com.tokai.mobile.coffeeworld

import android.content.Context
import dev.icerock.moko.resources.StringResource
import dev.icerock.moko.resources.desc.Resource
import dev.icerock.moko.resources.desc.StringDesc
import dev.icerock.moko.resources.format

실제 클래스 Strings(private val context: Context) {
    실제로 fun get(id: StringResource, args: List<Any>): String {
        return if (args.isEmpty()) {
            StringDesc.Resource(id).toString(context = context)
        } else {
            id.format(*args.toTypedArray()).toString(context)
        }
    }
}
```

11. 이제 프로젝트를 다시 빌드하고 KMM 프로젝트의 공용 폴더인 commonMain/resources/MR/images 경로에 이미지를 추가합니다. 이미지를 추가한 후 프로젝트를 다시 빌드합니다.

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

![이미지](/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_6.png)

12. 이제 androidApp 내의 MainActivity에서 코드를 업데이트하고 프로젝트를 실행합니다.

```js
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.Image
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.tooling.preview.Preview
import com.tokai.mobile.coffeeworld.Greeting
import com.tokai.mobile.coffeeworld.SharedRes
import com.tokai.mobile.coffeeworld.Strings
import dev.icerock.moko.resources.StringResource

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyApplicationTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    Column(
                        modifier = Modifier.fillMaxSize(),
                        verticalArrangement = Arrangement.Center,
                        horizontalAlignment = Alignment.CenterHorizontally
                    ) {
                        Image(
                            painter = painterResource(
                                id = com.tokai.mobile.coffeeworld.R.drawable.coffee
                            ),
                            contentDescription = null
                        )
                        Text(
                            text = stringResource(
                                id = SharedRes.strings.hello_world
                            )
                        )
                        Text(
                            text = stringResource(
                                id = SharedRes.strings.hello_x,
                                "Parita"
                            )
                        )
                    }
                }
            }
        }
    }
}

@Composable
fun stringResource(id: StringResource, vararg args: Any): String {
    return Strings(LocalContext.current).get(id, args.toList())
}

@Composable
fun GreetingView(text: String) {
    Text(text = text)
}

@Preview
@Composable
fun DefaultPreview() {
    MyApplicationTheme {
        GreetingView("Hello, Android!")
    }
}
```

13. 결과물은 다음과 같이 보일 것입니다.

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

![image](/assets/img/2024-06-23-FirstKotlinMultiplatformKMMprojecttobuildinAndroidStudio_7.png)

# 이 프로젝트의 Github 링크: https://github.com/paritadey/CoffeeWorld

KMM에서의 다음 코딩 세트로 돌아오겠습니다. 그 동안 즐거운 코딩하세요!!
