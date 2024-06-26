---
title: "마법같은 멀티플랫폼 하나의 코드베이스로 세 플랫폼 이용하기"
description: ""
coverImage: "/assets/img/2024-06-19-MultiplatformMagicOneCodebaseThreePlatforms_0.png"
date: 2024-06-19 14:10
ogImage:
  url: /assets/img/2024-06-19-MultiplatformMagicOneCodebaseThreePlatforms_0.png
tag: Tech
originalTitle: "Multiplatform Magic: One Codebase, Three Platforms"
link: "https://medium.com/proandroiddev/exploring-firebase-authentication-in-compose-multiplatform-8a662a30ec8e"
---

![이미지](/assets/img/2024-06-19-MultiplatformMagicOneCodebaseThreePlatforms_0.png)

Compose Multiplatform은 개발자에게 뛰어난 가능성의 세계를 열어주어 안드로이드와 iOS용 네이티브 모습을 하나의 코드베이스로 구축할 수 있게 합니다. 이러한 앱에 인증을 통합하는 것은 어렵게 느껴질 수 있지만, 이 기사에서는 Firebase REST API 인증을 Compose Multiplatform과 통합하는 단계와 혜택을 탐색할 것입니다.

# 왜 Firebase REST API를 사용하는가?

우리는 Android, iOS 및 Web 플랫폼용 다양한 Firebase SDK가 있음을 알고 있지만 Compose Multiplatform용 안정적인 SDK는 없습니다. 또한, 다양한 Compose Multiplatform 예제에서 사용 사례를 보여주기 위해 REST API를 사용하는 것을 보았습니다. 따라서 하나의 코드베이스를 사용하고 여러 플랫폼을 대상으로 하기 위해 Firebase REST API를 인증에 사용하는 것을 선호했습니다.

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

# 준비물:

- Compose Multiplatform 플러그인이 설치된 Android Studio 또는 IntelliJ IDEA.
- iOS 앱을 실행하기 위한 Xcode.
- Firebase 프로젝트.
- 프로젝트에서 Firebase Authentication이 활성화되어 있어야 합니다.

# 단계 1: Compose Multiplatform 프로젝트 생성

Compose Multiplatform 프로젝트를 생성하려면 Kotlin Multiplatform Wizard를 사용할 수 있습니다. 이는 우리가 타깃팅하는 플랫폼을 선택할 수 있게 해줍니다. 이 프로젝트에서는 Android, iOS 및 데스크톱 플랫폼을 선택할 것입니다. 플랫폼을 선택하고 나면 프로젝트를 다운로드하여 선호하는 IDE에서 열 수 있습니다.

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

![image](/assets/img/2024-06-19-MultiplatformMagicOneCodebaseThreePlatforms_1.png)

# 단계 2: 인증을 위한 UI 생성

이 프로젝트에서는 Email/Password 인증만 대상으로 하고 시작하므로 composeApp/src/commonMain/kotlin/authentication/AuthenticationView.kt 파일에서는 로그인 및 회원가입 뷰를 포함한 UI가 제공됩니다.

![image](/assets/img/2024-06-19-MultiplatformMagicOneCodebaseThreePlatforms_2.png)

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

# 단계 3: Firebase 프로젝트 구성하기.

Firebase 프로젝트를 구성하면 프로젝트 설정을 방문하여 Firebase REST API와 함께 사용할 API Key를 얻을 수 있습니다. 이를 사용하여 사용자를 인증할 수 있습니다.

![이미지](/assets/img/2024-06-19-MultiplatformMagicOneCodebaseThreePlatforms_3.png)

# 단계 4: 프로젝트에서 Ktor 구성하기.

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

Kotlin의 Ktor 프레임워크는 강력하고 유연한 HTTP 클라이언트를 제공하여 외부 API와의 상호 작용을 원활하게 만들어줍니다. 구현에 앞서 필요한 종속성이 포함된 Kotlin 프로젝트를 설정했는지 확인해보세요. Ktor의 클라이언트 라이브러리를 포함하여 프로젝트에 Ktor 클라이언트를 추가할 수 있습니다. 이를 위해 libs.versions.toml 파일 내 gradle 폴더에 다음 종속성을 포함하면 됩니다.

[versions]
...
kotlin = "1.9.21"
kotlinx-coroutines = "1.7.3"
ktor = "2.3.6"

[libraries]
....
ktor-serialization-kotlinx-json = { module = "io.ktor:ktor-serialization-kotlinx-json", version.ref = "ktor" }
ktor-client-core = { module = "io.ktor:ktor-client-core", version.ref = "ktor" }
ktor-client-darwin = { module = "io.ktor:ktor-client-darwin", version.ref = "ktor" }
ktor-client-okhttp = { module = "io.ktor:ktor-client-okhttp", version.ref = "ktor" }
ktor-client-content-negotiation = { module = "io.ktor:ktor-client-content-negotiation", version.ref = "ktor" }

[plugins]
androidApplication = { id = "com.android.application", version.ref = "agp" }
androidLibrary = { id = "com.android.library", version.ref = "agp" }
jetbrainsCompose = { id = "org.jetbrains.compose", version.ref = "compose-plugin" }
kotlinMultiplatform = { id = "org.jetbrains.kotlin.multiplatform", version.ref = "kotlin" }
kotlinxSerialization = { id = "org.jetbrains.kotlin.plugin.serialization", version.ref = "kotlin" }

libs.versions.toml 파일 내의 라이브러리를 추가했으면, build.gradle.kts 파일의 composeApp 내부에 종속성을 추가하고 프로젝트를 동기화해주면 됩니다:

androidMain.dependencies {
...
implementation(libs.ktor.client.okhttp)
}
commonMain.dependencies {
...
implementation(libs.ktor.client.core)
implementation(libs.ktor.client.content.negotiation)
implementation(libs.ktor.serialization.kotlinx.json)
}
desktopMain.dependencies {
..
implementation(libs.ktor.client.okhttp)
}

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

# 단계 5: Firebase REST API 호출하기

Ktor 라이브러리가 추가되고 프로젝트가 동기화된 후, Ktor 함수를 사용하여 Firebase REST API를 호출할 수 있습니다. 그 전에 HttpClient를 초기화하여 API 요청을 만들 수 있어야 합니다. 그래서, AuthenticationViewModel.kt 파일을 생성하여 모든 백엔드 작업을 처리하고 아래 코드를 추가합니다.

```js
private val httpClient = HttpClient() {
    install(ContentNegotiation) {
        json()
    }
}
```

## 사용자 생성 정복하기:

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

이제 새로운 사용자를 생성해 봅시다! https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=$'API_KEY' 엔드포인트로 Ktor 클라이언트 호출을 만들어 보겠습니다. 사용자 데이터(이메일 및 비밀번호)를 코틀린 직렬화를 사용하여 JSON 형식으로 직렬화해야 합니다. 데이터와 함께 POST 요청을 보내고 Firebase 응답을 기다리세요. 사용자가 성공적으로 생성되면 응답을 구문 분석하여 사용자 ID 및 기타 관련 정보를 추출하세요.
AuthenticationViewModel.kt에 추가된 signUp() 메서드의 아래 코드 스니펫을 확인해보세요.

```js
fun signUp(
        email: String,
        password: String,
        confirmPassword: String,
        onCompletion: onCompletion
    ) {
        if (password == confirmPassword) {
            viewModelScope.launch {
                val responseBody = httpClient
                    .post("https://identitytoolkit.googleapis.com/v1/accounts:signUp?key=${API_KEY}") {
                        header("Content-Type", "application/json")
                        parameter("email", email)
                        parameter("password", password)
                        parameter("returnSecureToken", true)
                    }
                if (responseBody.status.value in 200..299) {
                    val response = Json { ignoreUnknownKeys = true }
                        .decodeFromString<AuthResponse>(responseBody.bodyAsText())
                    onCompletion.onSuccess(response.idToken)
                } else {
                    onCompletion.onError(Exception(responseBody.bodyAsText()))
                }
            }
        } else {
            onCompletion.onError(Exception("Password doesn't match"))
        }
    }
```

## 로그인 퀘스트:

이제 로그인에 도전해 봅시다! 사용자 생성과 유사하게, https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=$'API_KEY' 엔드포인트로의 Ktor 클라이언트 호출을 구성해보세요. 다시 한번 사용자 자격 증명(이메일 및 비밀번호)을 직렬화하고 POST 요청을 보내세요. 응답을 구문 분석하여 ID 토큰을 얻어 사용자 신원을 확인하고 보호된 리소스에 액세스하는 데 필수적인 요소를 확보하세요.
AuthenticationViewModel.kt에 추가된 login() 메서드의 아래 코드 스니펫을 확인해보세요.

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
fun login(
    email: String,
    password: String,
    onCompletion: onCompletion
) {
    viewModelScope.launch {
        val responseBody = httpClient
            .post("https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=${API_KEY}") {
                header("Content-Type", "application/json")
                parameter("email", email)
                parameter("password", password)
                parameter("returnSecureToken", true)
            }
        if (responseBody.status.value in 200..299) {
            val response = Json { ignoreUnknownKeys = true }
                .decodeFromString(responseBody.bodyAsText())
            storeUserDetails(response)
            onCompletion.onSuccess(response.idToken)
        } else {
            onCompletion.onError(Exception(responseBody.bodyAsText()))
        }
    }
}
```

# 단계 6: 캐시 지원을 위해 SQLDelight 추가.

SQLDelight는 플랫폼에 중립적인 SQL 쿼리를 작성하기 위한 강력한 코틀린 라이브러리로, 캐싱과 결합하여 앱의 효율성을 높이는 동적 이중체를 형성합니다. 여기서는 login 및 signUp API 호출의 응답에서 얻은 refreshToken을 저장할 것입니다. 따라서 사용자가 앱을 다시 열 때 인증을 요청하지 않을 것입니다.

## SQLDelight 종속성 설정

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

먼저 gradle 폴더 내 libs.versions.toml 파일에 필요한 SQLDelight 종속성 및 플러그인을 추가해야 합니다.

```js
[versions]
...
...
sqlDelight = "2.0.1"

[libraries]
...
...
sqldelight-androidDriver = { module = "app.cash.sqldelight:android-driver", version.ref = "sqlDelight" }
sqldelight-jvmDriver = { module = "app.cash.sqldelight:sqlite-driver", version.ref = "sqlDelight" }
sqldelight-nativeDriver = { module = "app.cash.sqldelight:native-driver", version.ref = "sqlDelight" }
sqldelight-coroutines = { module = "app.cash.sqldelight:coroutines-extensions", version.ref = "sqlDelight" }
sqldelight-primitiveAdapters = { module = "app.cash.sqldelight:coroutines-extensions", version.ref = "sqlDelight" }

[plugins]
androidApplication = { id = "com.android.application", version.ref = "agp" }
androidLibrary = { id = "com.android.library", version.ref = "agp" }
jetbrainsCompose = { id = "org.jetbrains.compose", version.ref = "compose-plugin" }
kotlinMultiplatform = { id = "org.jetbrains.kotlin.multiplatform", version.ref = "kotlin" }
kotlinxSerialization = { id = "org.jetbrains.kotlin.plugin.serialization", version.ref = "kotlin" }
sqlDelight = { id = "app.cash.sqldelight", version.ref = "sqlDelight" }
```

다음 단계는 프로젝트 수준의 build.gradle.kts 파일에 플러그인을 추가하는 것입니다.

```js
plugins {
    // this is necessary to avoid the plugins to be loaded multiple times
    // in each subproject's classloader
    alias(libs.plugins.androidApplication) apply false
    alias(libs.plugins.androidLibrary) apply false
    alias(libs.plugins.jetbrainsCompose) apply false
    alias(libs.plugins.kotlinMultiplatform) apply false
    //SQLDelight Plugin
    alias(libs.plugins.sqlDelight) apply false
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

마지막 단계로는 composeApp build.gradle.kts 파일에서 SQLDelight를 구성해야 합니다. 여기서는 필요한 플랫폼에 따라 종속성을 추가하고 SQLDelight 데이터베이스의 이름을 추가할 것입니다. 매개변수가 포함된 데이터베이스 목록을 포함하는 sqlDelight 블록을 맨 끝에 볼 수 있을 것입니다.

```js
import org.jetbrains.compose.ExperimentalComposeLibrary
import org.jetbrains.compose.desktop.application.dsl.TargetFormat

plugins {
    alias(libs.plugins.kotlinMultiplatform)
    alias(libs.plugins.androidApplication)
    alias(libs.plugins.jetbrainsCompose)
    alias(libs.plugins.kotlinxSerialization)
    alias(libs.plugins.sqlDelight)
}

kotlin {

    androidTarget {
        compilations.all {
            kotlinOptions {
                jvmTarget = "1.8"
            }
        }
    }


    jvm("desktop")

    listOf(
        iosX64(),
        iosArm64(),
        iosSimulatorArm64()
    ).forEach { iosTarget ->
        iosTarget.binaries.framework {
            baseName = "ComposeApp"
            // This should be set to false to run on iOS
            isStatic = false
            // Add it to avoid sqllite3 issues in iOS
            linkerOpts.add("-lsqlite3")
        }
    }

    sourceSets {
        val desktopMain by getting

        androidMain.dependencies {
            implementation(libs.compose.ui.tooling.preview)
            implementation(libs.androidx.activity.compose)
            implementation(libs.ktor.client.okhttp)
            //SqlDelight for Android
            implementation(libs.sqldelight.androidDriver)
        }
        commonMain.dependencies {
            implementation(compose.runtime)
            implementation(compose.foundation)
            implementation(compose.material)
            implementation(compose.ui)
            @OptIn(ExperimentalComposeLibrary::class)
            implementation(compose.components.resources)
            //Ktor
            implementation(libs.ktor.client.core)
            implementation(libs.ktor.client.content.negotiation)
            implementation(libs.ktor.serialization.kotlinx.json)
            //Moko MVVM
            implementation(libs.moko.mvvm.core)
            implementation(libs.moko.mvvm.compose)
            //Kamel
            implementation(libs.kamel)
            // Navigator
            implementation(libs.voyager.navigator)
            //SqlDelight for common
            implementation(libs.sqldelight.coroutines)
            implementation(libs.sqldelight.primitiveAdapters)
        }
        desktopMain.dependencies {
            implementation(compose.desktop.currentOs)
            implementation(libs.kotlinx.coroutines.swing)
            implementation(libs.ktor.client.okhttp)
            //SqlDelight for jvm
            implementation(libs.sqldelight.jvmDriver)
        }
        iosMain.dependencies {
            //SqlDelight for iOS
            implementation(libs.sqldelight.nativeDriver)
        }
    }
}

sqldelight {
    databases {
        //Note: Name of your Database and .sq file should be same
        create("Database") {
            packageName.set("com.dwarshb.firebaseauthentication")
        }
    }
    // Add this line to avoid library linking issues
    linkSqlite = true
}

android {
    ...
}

compose.desktop {
    ...
}
```

## 쿼리용 Database.sq 파일 만들기

다음 단계는 모든 필요한 SQL 쿼리를 포함하는 .sq 파일을 작성하는 것입니다. 기본적으로 SQLDelight 플러그인은 sqldelight 폴더 내의 패키지 폴더에서 .sq를 읽습니다. 해당 폴더는 직접 commainMain 폴더 내에 있을 것입니다.
Database.sq 파일이 위치하는 폴더 구조 스크린샷은 아래에서 확인하실 수 있습니다.

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

<img src="/assets/img/2024-06-19-MultiplatformMagicOneCodebaseThreePlatforms_4.png" />

다음 코드를 Database.sq 파일에 추가하세요. 이 파일은 createTable, insertUser, removeAllUsers, getAllUsers 등의 쿼리를 포함합니다.

프로젝트를 컴파일하면 생성된 Kotlin 코드가 composeApp/build/generated/sqldelight 디렉토리에 저장됩니다. 또는 터미널에서 ./gradlew generateSqlDelightInterface 명령어를 사용하여 sqldelight 코틀린 코드를 생성할 수도 있습니다.

## 데이터베이스 드라이버 생성

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

SQLDelight은 SQLite 드라이버의 여러 플랫폼별 구현을 제공하므로 각 플랫폼에 대해 별도로 생성해야 합니다. 이를 기대 선언과 실제 선언을 사용하여 수행할 수 있습니다.

composeApp/src/commonMain/kotlin에서 패키지를 만들고 그 안에 DriverFactory.kt 클래스를 만드세요.

```kotlin
package com.dwarshb.firebaseauthentication

import app.cash.sqldelight.db.SqlDriver

expect class DriverFactory {
    fun createDriver(): SqlDriver
}
```

이제 각 대상 플랫폼에 대해 이를 구현해야 합니다.

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

안드로이드에서는 AndroidSqliteDriver 클래스가 SQLite 드라이버를 구현합니다.
그래서 composeApp/src/androidMain/kotlin에서 패키지를 만들어 그 안에 DriverFactory.kt 클래스를 생성해주세요.

```kotlin
package com.dwarshb.firebaseauthentication

import android.content.Context
import app.cash.sqldelight.db.SqlDriver
import app.cash.sqldelight.driver.android.AndroidSqliteDriver

actual class DriverFactory(var appContext: Context) {

    actual fun createDriver(): SqlDriver {
        return AndroidSqliteDriver(Database.Schema, appContext, "firebase.db")
    }
}
```

이제 Android에서 작동하도록 하기 위해 composeApp/src/androidMain/kotlin에 있는 MainActivity.kt 파일에서 해당 인스턴스를 생성해야 합니다.

```kotlin
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        //Android용 DriverFactory의 인스턴스 생성
        val driverFactory = DriverFactory(this)
        setContent {
            App(driverFactory.createDriver())
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

iOS에서 SQLite 드라이버 구현은 NativeSqliteDriver 클래스입니다.
그래서 composeApp/src/iosMain/kotlin에 패키지를 만들고 내부에 DriverFactory.kt 클래스를 만들어 주세요.

```kotlin
package com.dwarshb.firebaseauthentication

import app.cash.sqldelight.db.SqlDriver
import app.cash.sqldelight.driver.native.NativeSqliteDriver

actual class DriverFactory {
    actual fun createDriver(): SqlDriver {
        return NativeSqliteDriver(Database.Schema, "firebase.db")
    }
}
```

이제 MainViewController.kt 파일을 만들어서 iOS에서 작업할 수 있도록 인스턴스를 만들어주세요. 이 파일은 composeApp/src/iosMain/kotlin에 있습니다.

```kotlin
import androidx.compose.ui.window.ComposeUIViewController
import com.dwarshb.firebaseauthentication.DriverFactory

fun MainViewController() = ComposeUIViewController {
    val driverFactory = DriverFactory()
    App(driverFactory.createDriver())
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

데스크톱 환경에서는 SQLite 드라이버 구현이 JdbcSqliteDriver 클래스입니다.
그래서 composeApp/src/desktopMain/kotlin에 패키지를 만들고 그 안에 DriverFactory.kt 클래스를 생성해주세요.

```js
package com.dwarshb.firebaseauthentication

import app.cash.sqldelight.db.SqlDriver
import app.cash.sqldelight.driver.jdbc.sqlite.JdbcSqliteDriver
import java.io.File

actual class DriverFactory {
    actual fun createDriver(): SqlDriver {
        val databasePath = File(System.getProperty("java.io.tmpdir"), "firebase.db")
        val driver: SqlDriver = JdbcSqliteDriver(url = "jdbc:sqlite:${databasePath.absolutePath}")
        Database.Schema.create(driver)
        return driver
    }
}
```

이제 데스크톱에서 작동하도록 composeApp/src/desktopMain/kotlin의 main.kt 파일에 인스턴스를 생성해야 합니다.

```js
import androidx.compose.ui.window.Window
import androidx.compose.ui.window.application
import com.dwarshb.firebaseauthentication.DriverFactory

fun main() = application {
    Window(onCloseRequest = ::exitApplication, title = "FirebaseAuthentication") {
        val driverFactory = DriverFactory()
        App(driverFactory.createDriver())
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

<img src="/assets/img/2024-06-19-MultiplatformMagicOneCodebaseThreePlatforms_5.png" />

모든 플랫폼에서 DriverFactory를 구성하고 위 단계를 모두 따라왔다면, App() 함수를 수정하고 SqlDriver를 매개변수로 추가해야 합니다. 각 플랫폼에서 SqlDriver의 인스턴스를 가져올 수 있는 유일한 방법이기 때문입니다. 그래서 composeApp/src/commanMain/kotlin/App.kt 안에 다음과 같이 작성하세요.

```js
@Composable
fun App(sqlDriver: SqlDriver) {
    val authenticationViewModel = AuthenticationViewModel(sqlDriver)

    MaterialTheme {
      ...
    }
}
```

# 단계 7: SQLDelight 쿼리 구성하기

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

이 단계에서는 Firebase Authentication API에서 받은 토큰을 저장하여 사용자 세션을 유지하기 위해 insertUser 쿼리를 사용할 예정입니다. 또한 로컬 데이터베이스에 저장된 토큰을 얻기 위해 selectAllUser 쿼리를 사용할 것입니다. AuthenticationViewModel.kt 내에서 먼저 데이터베이스를 초기화할 것입니다.

```js
    var databaseQuery : DatabaseQueries

    val database = Database(sqlDriver)
    databaseQuery = database.databaseQueries
```

이제 API에서 받은 응답을 저장할 storeUserDetails() 메서드를 만들 것입니다. 이 메서드는 로컬 데이터베이스에 정보를 저장하는 데 insertUser 쿼리를 사용합니다. AuthenticationViewModel.kt 파일의 login()이나 signUp() 메서드 내에 이 메서드를 추가할 수 있습니다.

```js
internal fun storeUserDetails(response: AuthResponse) {
        databaseQuery.insertUser(
            response.idToken, response.email, response.refreshToken,
            response.email
        )
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

세션 유지를 위해 시스템에 이미 토큰이 로컬 데이터베이스에 있는지 확인해야 합니다. 그래서 우리는 selectAllUsers 쿼리를 사용하여 로컬 데이터베이스에 저장된 토큰을 확인하는 checkSession()을 생성할 것입니다.

```kotlin
internal fun checkSession(onCompletion: onCompletion) {
    for(user in databaseQuery.selectAllUsers().executeAsList()) {
        if (user != null) {
            onCompletion.onSuccess(user.refreshToken.toString())
        } else {
            onCompletion.onError(Exception("세션을 찾을 수 없습니다"))
        }
    }
}
```

우리는 checkSession() 메서드를 사용하여 결과에 따라 UI를 업데이트할 수 있습니다. 예를 들어, 토큰이 있는 경우 onSuccess 내에서 앱을 MainScreen으로 이동시키고, 그렇지 않은 경우 AuthenticationView 화면이 표시됩니다.

# 데모

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

아래의 Github 링크에서 전체 코드를 확인해보세요.

# 참조 링크:

- https://www.jetbrains.com/help/kotlin-multiplatform-dev/multiplatform-ktor-sqldelight.html#build-an-sdk
- https://firebase.google.com/docs/reference/rest/auth#section-api-usage
