---
title: "코틀린 멀티플랫폼 KMP"
description: ""
coverImage: "/assets/img/2024-06-19-KotlinMultiPlatformKMP_0.png"
date: 2024-06-19 14:12
ogImage: 
  url: /assets/img/2024-06-19-KotlinMultiPlatformKMP_0.png
tag: Tech
originalTitle: "Kotlin MultiPlatform (KMP)"
link: "https://medium.com/@ZahraHeydari/kotlin-multiplatform-kmp-4799fdfb69f1"
---


이것은 안드로이드 및 iOS 플랫폼에서 작동하는 첫 번째 크로스 플랫폼 응용 프로그램을 만드는 단계별 안내서입니다.

![이미지](/assets/img/2024-06-19-KotlinMultiPlatformKMP_0.png)

Kotlin Multiplatform 기술은 크로스 플랫폼 프로젝트의 개발을 간소화하며, 그 중요한 사용 사례 중 하나는 플랫폼 간에 응용 프로그램 로직 코드를 공유하는 것입니다.

https://github.com/ZahraHeydari/Kotlin-MultiPlatform-Mobile

<div class="content-ad"></div>

# 이 기사에서 배울 내용:

- 환경 설정
- 기본적인 KMM 프로젝트 생성
- KMM 프로젝트 구조의 기초 이해
- ViewModel
- 네트워킹
- iOS에서의 코루틴
- 의존성 주입

# 1. 환경 설정

필요한 도구를 설치하세요:

<div class="content-ad"></div>

- Android Studio
- Xcode
- JDK
- Kotlin Multiplatform Mobile plugin
- Kotlin plugin

모든 것이 예상대로 작동되도록하려면 KDoctor 도구를 설치하고 실행하십시오.

```js
brew install kdoctor
```

# 2. 기본 KMM 프로젝트 생성

<div class="content-ad"></div>

안녕하세요! 안드로이드 스튜디오를 열고 새 프로젝트 템플릿에서 Kotlin Multiplatform App을 선택하세요. 그리고 '다음' 버튼을 클릭하세요.

![이미지](/assets/img/2024-06-19-KotlinMultiPlatformKMP_1.png)

다음 화면에서 애플리케이션의 이름과 프로젝트를 저장할 위치 등을 선택하세요. 그리고 다시 '다음' 버튼을 클릭하세요.

![이미지](/assets/img/2024-06-19-KotlinMultiPlatformKMP_2.png)

<div class="content-ad"></div>

마침내 iOS 앱에 대한 종속성 관리자를 선택하십시오. 기본적으로 Regular framework이 선택되어 있고, 그런 다음 '완료'를 눌러주세요.

![이미지](/assets/img/2024-06-19-KotlinMultiPlatformKMP_3.png)

Gradle 동기화가 완료되면 툴바에서 실행 버튼을 사용하여 iOS 및 Android 앱을 모두 실행할 수 있습니다.

![이미지](/assets/img/2024-06-19-KotlinMultiPlatformKMP_4.png)

<div class="content-ad"></div>

안녕하세요! 안드로이드 에뮬레이터나 iOS 시뮬레이터에서 앱을 실행할 거예요.

![이미지](/assets/img/2024-06-19-KotlinMultiPlatformKMP_5.png)

# 3. KMM 프로젝트 구조 기초 이해

각 Kotlin Multiplatform 프로젝트는 shared, androidApp, iosApp 세 가지 모듈을 포함하고 있어요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-KotlinMultiPlatformKMP_6.png" />

- shared은 Android 및 iOS 애플리케이션 모두에 공통으로 포함된 로직을 포함하는 Kotlin 모듈입니다. 이것은 플랫폼 간에 공유하는 코드입니다. 빌드 프로세스를 자동화하기 위해 Gradle을 빌드 시스템으로 사용합니다.
- androidApp은 Android 애플리케이션으로 빌드되는 Kotlin 모듈입니다. 빌드 시스템으로 Gradle을 사용합니다. androidApp 모듈은 공통 Android 라이브러리로 사용하기 위해 shared 모듈에 의존하고 사용합니다.
- iosApp은 iOS 애플리케이션으로 빌드되는 Xcode 프로젝트입니다. 이는 shared 모듈에 의존하며 iOS 프레임워크로 사용합니다. 공유 모듈은 일반 프레임워크로 사용하거나 CocoaPods 종속성으로 사용할 수 있습니다.

# 기대 및 실제 키워드

기대 및 실제 선언을 통해 Kotlin Multiplatform 모듈에서 플랫폼별 API에 액세스할 수 있습니다. 공통 코드에서 플랫폼에 중립적인 API를 제공할 수 있습니다.

<div class="content-ad"></div>

```markdown
![Image](/assets/img/2024-06-19-KotlinMultiPlatformKMP_7.png)

다음 규칙을 따라 예상 및 실제 선언을 정의합니다:

- 공통 소스 세트에서 표준 코틀린 구조를 선언합니다. 이는 함수, 속성, 클래스, 인터페이스, 열거형 또는 주석일 수 있습니다.

2. 이 구조물에 expect 키워드를 표시합니다. 이것이 예상 선언입니다. 이러한 선언은 공통 코드에서 사용할 수 있지만 구현은 포함하면 안 됩니다. 대신, 플랫폼별 코드가이 구현을 제공합니다.
```

<div class="content-ad"></div>

3. 각 플랫폼별 소스 세트에서 동일한 구조물을 동일한 패키지에 선언하고 actual 키워드로 표시하세요. 이것이 여러분의 실제 선언입니다. 일반적으로 이 선언에는 플랫폼별 라이브러리를 사용하여 구현이 포함됩니다.

![Kotlin Multiplatform](/assets/img/2024-06-19-KotlinMultiPlatformKMP_8.png)

## 공통 코드

공통 코드는 서로 다른 플랫폼 간에 공유되는 Kotlin 코드입니다.

<div class="content-ad"></div>

간단한 예제를 살펴보겠습니다:

```kotlin
class Greeting {
    private val platform: Platform = getPlatform()

    fun greet(): String {
        return "안녕, ${platform.name}!"
    }
}
```

```kotlin
interface Platform {
    val name: String
}

expect fun getPlatform(): Platform
```

## 모든 플랫폼에서 코드 공유하기

<div class="content-ad"></div>

만약 모든 플랫폼에 대해 공통인 비즈니스 로직이 있다면, 각 플랫폼에 동일한 코드를 작성할 필요가 없습니다. 그저 공통 소스 집합에서 공유하면 됩니다.

![Image](/assets/img/2024-06-19-KotlinMultiPlatformKMP_9.png)

보통 여러 네이티브 타겟을 생성해야 할 때가 많으며, 이러한 타겟들은 공통 로직과 써드파티 API를 재사용할 수 있습니다.

이제 KMM 프로젝트에 viewModel을 추가할 시간입니다!

<div class="content-ad"></div>

# 4. KMP 프로젝트의 ViewModel

ViewModel은 Activity 또는 Fragment를 위한 데이터를 준비하고 관리하는 클래스입니다.

이 샘플 프로젝트에서 EmojiHubViewModel은 이모지 데이터를 준비하고 관리하며 UI에 노출하기 위해 사용되며, SharedViewModel은 여러 뷰모델의 기본 클래스입니다.

SharedViewModel과 EmojiHubViewModel은 commonMain 모듈에 있습니다.

<div class="content-ad"></div>

```kotlin
// shared/commonMain/.../viewModel/SharedViewModel
expect open class SharedViewModel() {

    val sharedViewModelScope: CoroutineScope

    protected open fun onCleared()
}
```

androidMain 및 iosMain 소스 세트에서 actual 키워드를 사용하여 동일한 구조물을 표시하십시오. 일반적으로 이 actual 선언에는 플랫폼별 라이브러리를 사용한 구현이 포함됩니다.

EmojiHubViewModel 선언이 공통 코드에 있고 iOS 및 Android 플랫폼 모두에서 사용할 수 있다는 점에 유의하십시오.

```kotlin
class EmojiHubViewModel(private val repository: EmojiHubRepository) : SharedViewModel() {

    ...

    init {
        sharedViewModelScope.launch {
            ...
        }
    }
}
```

<div class="content-ad"></div>

## 안드로이드 플랫폼의 ViewModel

![image](/assets/img/2024-06-19-KotlinMultiPlatformKMP_10.png)

```kotlin
// shared/androidMain/.../viewModel/SharedViewModel
actual open class SharedViewModel: ViewModel() {

    actual val sharedViewModelScope: CoroutineScope = this.viewModelScope

    actual override fun onCleared() {
        super.onCleared()
    }
}
```

아래 코드 스니펫은 EmojiHubScreen에서 emojiHubViewModel을 사용하는 방법을 보여줍니다.

<div class="content-ad"></div>

```kotlin
@Composable
fun EmojiHubScreen() {

    val emojiHubViewModel: EmojiHubViewModel = koinViewModel()
    val items by emojiHubViewModel.items.collectAsState()

    Scaffold(topBar = {
        TopAppBar(title = {
            Text(text = "EmojiHub")
        })
    }, content = { paddingValues ->
        LazyColumn(modifier = Modifier.padding(paddingValues)) {
            items(items.size) { index ->
                Item(items[index])
            }
        }
    })
}
```

저는 이 글에서 Ktor를 사용하여 데이터를 가져오고 Koin을 사용하여 종속성 주입하는 방법에 대해 설명하겠습니다! 그러니 지금은 건너뛰세요!

## iOS 플랫폼의 ViewModel

<img src="/assets/img/2024-06-19-KotlinMultiPlatformKMP_11.png" />
```

<div class="content-ad"></div>

```kotlin
// shared/iosMain/.../viewModel/SharedViewModel
실제 열린 클래스 SharedViewModel {

    실제 val sharedViewModelScope = MainScope()

    보호된 실제 기능 onCleared() {}

    fun clear() {
        onCleared()
    }
}
```

그리고 ContentView에서 emojiHubViewModel을 사용하는 방법입니다.

```swift
struct ContentView: View {

 @StateObject
 var emojiHubViewModel: EmojiHubViewModel()

 var body: some View {
     Text(String("EmojiHub"))
     List {
          ForEach(iOSEmojiHubViewModel.items, id: \.self) { item in
              Item(emojiItem: item)
          }
     }
  }
}
```

# 5. 네트워킹
```

<div class="content-ad"></div>

Ktor은 마이크로서비스부터 멀티플랫폼 HTTP 클라이언트 앱까지 다양한 용도로 사용됩니다. 이를 사용하려면 build.gradle.kts 파일에 아래와 같이 Ktor 종속성을 추가하면 됩니다.

```kotlin
val ktorVersion = "3.4.3"

// shared/build.gradle.kts
sourceSets {

    val commonMain by getting {
        dependencies {
            implementation("io.ktor:ktor-client-core:$ktorVersion")
            implementation("io.ktor:ktor-client-logging:$ktorVersion")
            implementation("io.ktor:ktor-client-content-negotiation:$ktorVersion")
        }
    }

    val androidMain by getting {
        dependsOn(commonMain)
        dependencies {
            // 다른 의존성 추가
            implementation("io.ktor:ktor-client-okhttp:$ktorVersion")
        }
    }

    val iosMain by getting {
        dependsOn(commonMain)
        dependencies {
            implementation("io.ktor:ktor-client-darwin:$ktorVersion")
        }
    }
}
```

공통 모듈인 commonMain에 HttpClient 파일을 아래와 같이 추가하세요:

```kotlin
expect fun httpClient(config: HttpClientConfig<*>.() -> Unit): HttpClient
```

<div class="content-ad"></div>

안드로이드 메인에서 실제 키워드를 사용하여 httpClient를 선언하세요:

```kotlin
actual fun httpClient(config: HttpClientConfig<*>.() -> Unit): HttpClient = HttpClient(OkHttp) {
    config()
}
```

또한 iOS 메인에서도 실제 키워드를 사용하여 httpClient를 선언하세요:

```kotlin
actual fun httpClient(config: HttpClientConfig<*>.() -> Unit): HttpClient = HttpClient(Darwin) {
    config()
}
```

<div class="content-ad"></div>

이제 EmojiHubRepository에서 httpClient를 사용하여 원격 서버에서 이모지를 가져오세요.

```kotlin
class EmojiHubRepository(private val httpClient: HttpClient) {

    suspend fun getEmojis(): List<EmojiItem> {
        return try {
            httpClient.get(urlString = "/api/all").body()
        } catch (e: Exception) {
            e.printStackTrace()
            emptyList()
        }
    }
}
```

그리고 EmojiHubViewModel에서 EmojiHubRepository를 사용하여 원격 또는 로컬 소스에서 데이터를 가져오세요.

```kotlin
class EmojiHubViewModel(private val repository: EmojiHubRepository) : SharedViewModel() {

    private val _items = MutableStateFlow<List<EmojiItem>>(listOf())

    @NativeCoroutinesState
    val items = _items.asStateFlow()

    init {
        sharedViewModelScope.launch {
            _items.update {
                repository.getEmojis() // 저장소를 통해 데이터 가져오기
            }
        }
    }
}
```

<div class="content-ad"></div>

# 6. 아이폰에서의 코루틴

프로젝트에 Kotlinx.Serialization을 추가하세요.

Kotlinx.Serialization은 사용자 정의 타입의 객체를 직렬화하고 역직렬화하는 데 사용됩니다.

```js
val kotlinxSerializationVersion = "1.5.1"

// shared/build.gradle.kts
sourceSets {

    val commonMain by getting {
        dependencies {
            implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:$kotlinxSerializationVersion")
            ...
        }
    }

    val androidMain by getting {
        dependsOn(commonMain)
        dependencies {
            ...
        }
    }

    val iosMain by getting {
        dependsOn(commonMain)
        dependencies {
            ...
        }
    }
}
```

<div class="content-ad"></div>

## httpClient 재구성하기

```kotlin
// shared/commonMain/.../EmojiHubRepository
    class EmojiHubRepository {
        private val httpClient = httpClient {
            ...
            install(ContentNegotiation) {
                json(
                    Json {
                        ignoreUnknownKeys = true
                    }
                )
            }
        }
    }
```

서버에서 데이터를 가져와 Android 앱을 실행합니다.

```kotlin
suspend fun getEmojis(): List<EmojiItem> {
        return try {
            httpClient.get(urlString = "/api/all").body()
        } catch (e: Exception) {
            e.printStackTrace()
            emptyList()
        }
    }
```

<div class="content-ad"></div>

```kotlin
class EmojiHubViewModel(private val repository: EmojiHubRepository) : SharedViewModel() {

    private val _items = MutableStateFlow<List<EmojiItem>>(listOf())

    @NativeCoroutinesState
    val items = _items.asStateFlow()

    init {
        sharedViewModelScope.launch {
            _items.update {
                repository.getEmojis()
            }
        }
    }
}
```

![Image](/assets/img/2024-06-19-KotlinMultiPlatformKMP_12.png)

## KMP-NativeCoroutines

```kotlin
// commonMain/viewModel/EmojiHubViewModel
class EmojiHubViewModel(private val repository: EmojiHubRepository) : SharedViewModel() {

    private val _items = MutableStateFlow<List<EmojiItem>>(listOf())

    @NativeCoroutinesState
    val items = _items.asStateFlow()

    ...
}
```

<div class="content-ad"></div>

IOSEmojiHubViewModel은 iosApp 모듈에 있어요.

<img src="/assets/img/2024-06-19-KotlinMultiPlatformKMP_13.png" />

IOSEmojiHubViewModel에서 KMPNativeCoroutinesAsync을 사용하여 데이터를 가져옵니다.

```swift
@MainActor
class IOSEmojiHubViewModel: ObservableObject {

    private let githubViewModel = GithubViewModel()

    @Published
    var items = Array<EmojiItem>()

    var task: Task<(), Never>? = nil

    init() {
        task = Task {
            do {
                let asyncItems = asyncSequence(for: emojiHubViewModel.itemsFlow)
                for try await asyncItem in asyncItems {
                    items = asyncItem
                }
            } catch {
                print("Failed with error: \(error)")
            }
        }
    }

    func clear() {
        task?.cancel()
    }
}
```

<div class="content-ad"></div>

```swift
struct ContentView: View {

    @StateObject
    var iOSEmojiHubViewModel = IOSEmojiHubViewModel()

    var body: some View {
        Text(String("EmojiHub"))
        List {
            ForEach(iOSEmojiHubViewModel.items, id: \.self) { item in
                Item(emojiItem: item)
            }
        }
    }
}
```

# 7. 의존성 주입

의존성 주입 패턴을 구현하기 위해 프로젝트에 Koin을 추가하세요.

```gradle
val koinVersion = "3.4.3"

// shared/build.gradle.kts
sourceSets {

    val commonMain by getting {
        dependencies {
            implementation("io.insert-koin:koin-core:$koinVersion")
            ...
        }
    }

    val androidMain by getting {
        dependsOn(commonMain)
        dependencies {
            implementation("io.insert-koin:koin-android:$koinVersion")
            ...
        }
    }

    val iosMain by getting {
        dependsOn(commonMain)
        dependencies {
            ...
        }
    }
}
```

<div class="content-ad"></div>

EmojiHubRepository에 HttpClient을 주입해주세요.

```kotlin
class EmojiHubRepository(private val httpClient: HttpClient) {

    ...
}
```

그리고 EmojiHubViewModel에서 EmojiHubRepository를 주입해주세요.

```kotlin
// commonMain/viewModel/EmojiHubViewModel
class EmojiHubViewModel(private val repository: EmojiHubRepository) : SharedViewModel() {
    
    ...
}
```

<div class="content-ad"></div>

아래와 같이 의존성을 정의하도록 모듈을 선언해보세요:

```kotlin
val appModule = module {
    single {
        httpClient {
           ...
        }
    }

    single {
        EmojiHubRepository(get())
    }

    sharedViewModel {
        EmojiHubViewModel(get())
    }
}
```

프로젝트 애플리케이션 클래스에 Koin을 시작하는 코드를 추가해보세요.

```kotlin
//androidApp/MainApplication
class MainApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        startKoin {
            androidContext(this@MainApplication)
            modules(appModule) // 앱 모듈
        }
    }
}
```

<div class="content-ad"></div>

iOSMain 에서 Koin을 시작하려면 아래와 같이 시작하세요.

```js
// shared/iosMain/.../KoinStarter.kt
fun startKoin() {
    startKoin { modules(appModule) }
}
```

<img src="/assets/img/2024-06-19-KotlinMultiPlatformKMP_14.png" />

iOSApp에서 startKoin을 호출하세요.

<div class="content-ad"></div>

```markdown
@ main 
 구조체 iOSApp: 앱 {

   init() {
       KoinStarterKt.startKoin()
    }

    var body: some Scene {
       WindowGroup {
           ContentView()
       }
    }
}
```

 그리고 GithubViewModelHelper은 iOS에서 GithubViewModel을 주입하는 데 사용됩니다.

```markdown
class EmojiHubViewModelHelper: KoinComponent {

    private val emojiHubViewModel: EmojiHubViewModel = get()

    @NativeCoroutinesState
    val items = emojiHubViewModel.items
}
```

![이미지](/assets/img/2024-06-19-KotlinMultiPlatformKMP_15.png)
```

<div class="content-ad"></div>

이제 Android 앱과 iOS 앱을 실행해보세요.

![Kotlin Multiplatform](/assets/img/2024-06-19-KotlinMultiPlatformKMP_16.png)

## 라이브러리 및 도구

- KMP 플러그인
- Kdoctor
- Ktor
- kotlinx.serialization
- KMP-NativeCoroutines
- Koin

<div class="content-ad"></div>

간단한 KMP(Kotlin Multiplatform)를 사용한 예제 프로젝트입니다.

[여기](https://github.com/ZahraHeydari/Kotlin-MultiPlatform-Mobile)를 클릭해주세요!

이 이야기를 읽어 주셔서 감사합니다. 도움이 되셨으면 좋겠어요.

의견이 있으시면 언제든지 남겨주세요. 감사합니다!

<div class="content-ad"></div>

코딩을 즐기세요!