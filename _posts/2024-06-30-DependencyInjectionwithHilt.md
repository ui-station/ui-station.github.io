---
title: "Hilt로 알아보는 Dependency Injection 방법"
description: ""
coverImage: "/assets/img/2024-06-30-DependencyInjectionwithHilt_0.png"
date: 2024-06-30 19:19
ogImage: 
  url: /assets/img/2024-06-30-DependencyInjectionwithHilt_0.png
tag: Tech
originalTitle: "Dependency Injection with Hilt"
link: "https://medium.com/@gaffaryucel/dependency-injection-with-hilt-020a2605d71d"
---


<img src="/assets/img/2024-06-30-DependencyInjectionwithHilt_0.png" />

# 코틀린을 활용한 모던 안드로이드 개발 시리즈 #4

의존성 주입(DI)은 현대 안드로이드 애플리케이션 개발에서 중요한 기술입니다. Hilt는 안드로이드 애플리케이션에서 DI를 간단하게 만들어 주는 라이브러리로, Dagger를 기반으로 하고 있습니다. 이 안내서에서는 Hilt의 기본 개념과 Hilt를 사용한 DI의 구현 방법을 다룰 것입니다.

# 의존성 주입이란?

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

의존성 주입은 클래스가 생성자를 직접 만드는 대신 외부 소스에서 의존성을 받을 수 있도록 하는 디자인 패턴입니다. 이는 코드를 더 유연하고 테스트 가능하며 관리하기 쉽게 만듭니다.

# Hilt란 무엇이며 왜 사용해야 하나요?

Hilt는 안드로이드를 위한 Dagger 기반 의존성 주입 라이브러리로, DI를 간단하게 만들고 다음과 같은 이점을 제공합니다:

- 보일러플레이트 코드를 줄입니다.
- Android 컴포넌트에 대한 내장 지원을 제공합니다.
- 테스트 작성을 쉽게 만듭니다.

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

# 프로젝트에 Hilt 추가하기

프로젝트에 Hilt를 추가하려면 build.gradle 파일에 필요한 종속성을 추가해야 합니다.

## 프로젝트 수준 build.gradle

```js
buildscript {
    ext.hilt_version = '2.38.1'
    dependencies {
        classpath "com.google.dagger:hilt-android-gradle-plugin:$hilt_version"
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

## Module-level build.gradle

```js
plugins {
    id 'com.android.application'
    id 'kotlin-android'
    id 'kotlin-kapt'
    id 'dagger.hilt.android.plugin'
}

dependencies {
    implementation "com.google.dagger:hilt-android:$hilt_version"
    kapt "com.google.dagger:hilt-android-compiler:$hilt_version"
}
```

# Basic Hilt Annotations

## @HiltAndroidApp

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

@HiltAndroidApp 주석을 사용하여 Hilt의 DI 컨테이너를 초기화하는 애플리케이션 클래스에 주석을 달아주세요.

```kotlin
@HiltAndroidApp
class MyApp : Application() {
}
```

## @Inject

@Inject 주석은 Hilt에 클래스 또는 필드의 인스턴스를 제공하는 방법을 알려줍니다.

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
class UserRepository @Inject constructor(
    private val apiService: ApiService
) {
    // UserRepository content
}
```

## @Module and @InstallIn

@Module 어노테이션은 Hilt에게 특정 타입의 인스턴스를 생성하는 방법을 알려줍니다. @InstallIn 어노테이션은 모듈이 설치되어야 하는 Hilt 컨테이너를 지정합니다.

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object AppModule {

    @Provides
    @Singleton
    fun provideApiService(): ApiService {
        return Retrofit.Builder()
            .baseUrl("https://api.example.com")
            .build()
            .create(ApiService::class.java)
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

## @Singleton

@Singleton 어노테이션은 Hilt에게 애플리케이션 전체를 통틀어 의존성의 단일 인스턴스를 제공하도록 지시합니다.

# ViewModel에서 Hilt 사용하기

ViewModel에서 Hilt를 사용하려면 @HiltViewModel과 @Inject 어노테이션을 사용하면 됩니다.

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
@HiltViewModel
class MyViewModel @Inject constructor(
    private val userRepository: UserRepository
) : ViewModel() {
    // ViewModel content
}
```

Activity나 Fragment에서 Hilt를 사용하여 ViewModel을 가져오세요.

```kotlin
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {

    private val viewModel: MyViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Use ViewModel
    }
}
```

# Hilt를 사용하여 다른 컴포넌트에서 DI하기

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

힐트는 액티비티, 프래그먼트, 뷰, 서비스 및 브로드캐스트 수신자와 같은 다양한 Android 구성 요소에서 DI를 제공합니다. @AndroidEntryPoint 주석을 사용하여 힐트 DI를 이러한 구성 요소에서 활성화할 수 있습니다.

```js
@AndroidEntryPoint
class MyFragment : Fragment() {

    private val viewModel: MyViewModel by viewModels()

    // 프래그먼트 내용
}
```

# Hilt와 테스트

힐트는 또한 테스트 작성을 간소화합니다. 테스트에서 @HiltAndroidTest 및 @UninstallModules 주석을 사용하세요.

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
@HiltAndroidTest
@RunWith(AndroidJUnit4::class)
class MyTest {

    @get:Rule
    var hiltRule = HiltAndroidRule(this)

    @Inject
    lateinit var userRepository: UserRepository

    @Before
    fun init() {
        hiltRule.inject()
    }

    @Test
    fun testUserRepository() {
        // Test content
    }
}
```

# 결론

Hilt는 안드로이드 애플리케이션에서 의존성 주입을 간단하게 만들어주는 강력한 도구입니다. 이 안내서에서 Hilt의 기본 개념과 사용 방법을 배웠습니다. Hilt를 사용하면 코드를 더 깔끔하게 만들고 유연하게 유지하며 쉽게 테스트할 수 있습니다.

본 안내서를 따라가면 Hilt를 활용한 의존성 주입을 마스터하는 중요한 한 걸음을 내디딘 것입니다. 즐거운 코딩되세요!
