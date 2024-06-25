---
title: "3DraftCode  Retrofit"
description: ""
coverImage: "/assets/img/2024-05-17-3DraftCodeRetrofit_0.png"
date: 2024-05-17 17:47
ogImage:
  url: /assets/img/2024-05-17-3DraftCodeRetrofit_0.png
tag: Tech
originalTitle: "#3DraftCode — Retrofit"
link: "https://medium.com/@rakapermanaputraa/3draftcode-retrofit-d409c4dc060e"
---

<img src="/assets/img/2024-05-17-3DraftCodeRetrofit_0.png" />

# Retrofit이란 무엇인가요?

Retrofit은 안드로이드 개발에서 네트워킹을 위해 사용되는 인기 있는 라이브러리 또는 종속성 중 하나입니다. 이는 RESTful 서비스와 상호 작용하는 데 사용하는 고수준 인터페이스를 제공하여 웹 서비스와 API로 HTTP 요청을 보내는 과정을 단순화합니다. Retrofit을 사용하면 API 엔드포인트와 JSON 응답을 나타내는 데이터 모델을 간단하고 선언적인 방식으로 정의할 수 있습니다. 또한 네트워크 요청, JSON 데이터의 직렬화 및 역직렬화와 같은 작업을 처리하여 개발자가 안드로이드 애플리케이션에서 웹 서버와 RESTful API를 소비하는 것이 더 쉽도록합니다. 전반적으로 Retrofit은 안드로이드 앱에서 네트워크 작업을 처리하는 간편함, 효율성 및 유연성으로 널리 사용됩니다.

# 왜 Retrofit을 사용해야 할까요?

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

Retrofit은 Android 개발에서 네트워크 작업을 처리하는 우선적인 선택으로 여겨지는 몇 가지 이점을 제공합니다:

- 간편함: Retrofit은 API 엔드포인트를 정의하고 상호 작용하기 위한 깨끗하고 직관적인 인터페이스를 제공하여 네트워크 요청을 만드는 과정을 간소화합니다.
- 유형 안정성: Retrofit은 API 인터페이스 정의를 기반으로 유형 안전한 HTTP 클라이언트를 생성합니다. 이는 개발자로서 HTTP 요청 및 응답에 대한 컴파일 시간 유효성 검사를 제공하여 데이터 유형이 잘못된 경우나 필드가 누락된 경우의 런타임 오류 가능성을 줄입니다.
- 쉬운 통합: Retrofit은 Android 생태계의 다른 인기 있는 라이브러리들과 원활하게 통합됩니다. 예를 들어 JSON 직렬화/역직렬화를 위한 Gson이나 Moshi, 그리고 비동기 작업 처리를 위한 RxJava나 Kotlin 코루틴 등을 사용할 수 있습니다.
- 효율성: Retrofit은 성능을 최적화하여 네트워크 리소스를 효율적으로 사용합니다. 요청/응답 캐싱, 연결 풀링, 비동기 요청 실행 등의 기능을 지원하여 빠르고 반응성이 좋은 애플리케이션을 만드는 데 도움을 줍니다.
- 유연성: Retrofit은 다양한 사용 사례와 요구 사항에 적응하기 위한 다양한 사용자 정의 옵션을 제공합니다. HTTP 헤더, 요청 타임아웃, 오류 처리 메커니즘, 로깅 수준 등을 필요에 맞춰 구성할 수 있습니다.

# Retrofit을 사용해야 하는 경우?

Retrofit이 제공하는 다양한 이점을 고려하면, 네트워크 작업 및 RESTful API와 작업할 때마다 Retrofit을 사용해야 한다고 생각합니다.

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

# Retrofit 예제?

여기서 Retrofit을 사용하는 방법을 공유할게요.

1. Android 프로젝트에 Retrofit 추가하기 (build.gradle)

```js
dependencies {
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.9.0' // JSON 직렬화/역직렬화를 위해
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

2. 데이터 클래스 정의

JSON 응답 API를 기반으로 데이터 클래스를 정의하세요.

```kotlin
data class User(
    val id: Int,
    val name: String,
    val username: String,
    val email: String
)
```

3. API 인터페이스 정의

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
인터페이스 ApiService {

    @GET("users/{id}")
    fun getUserById(@Path("id") userId: Int): Call<User>

}
```

4. Retrofit 인스턴스 생성

기본 URL을 구성하고 JSON 파싱을 위한 컨버터 팩토리를 추가하려면 Retrofit 인스턴스를 설정하십시오.

```kotlin
object ApiClient {
    private const val BASE_URL = "https://jsonplaceholder.typicode.com/"

    val retrofit: Retrofit = Retrofit.Builder()
        .baseUrl(BASE_URL)
        .addConverterFactory(GsonConverterFactory.create())
        .build()
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

5. 네트워크 요청 만들기

여기에서 Retrofit을 사용하여 Activity에서 네트워크 요청을 할 수 있습니다.

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var apiService: ApiService

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        apiService = ApiClient.retrofit.create(ApiService::class.java)

        getUserById(userId: 1)
    }

    private fun getUserById(userId: Int) {
        val call = apiService.getUserById(userId)
        call.enqueue(object : Callback<User> {
            override fun onResponse(call: Call<User>, response: Response<User>) {
                if (response.isSuccessful) {
                    val user = response.body()
                    Log.d("MainActivity", "User: $user")
                } else {
                    Log.e("MainActivity", "Request failed: ${response.errorBody()}")
                }
            }

            override fun onFailure(call: Call<User>, t: Throwable) {
                Log.e("MainActivity", "Network request failed", t)
            }
        })
    }
}
```

6. Manifest에 권한을 추가하는 것을 잊지 마세요.

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
<uses-permission android:name="android.permission.INTERNET" />
```

자세한 내용은 Retrofit 공식 페이지를 확인해보세요.
