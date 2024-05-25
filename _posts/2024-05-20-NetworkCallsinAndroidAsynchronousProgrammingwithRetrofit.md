---
title: "안드로이드에서 네트워크 호출 Retrofit을 이용한 비동기 프로그래밍"
description: ""
coverImage: "/assets/img/2024-05-20-NetworkCallsinAndroidAsynchronousProgrammingwithRetrofit_0.png"
date: 2024-05-20 15:58
ogImage: 
  url: /assets/img/2024-05-20-NetworkCallsinAndroidAsynchronousProgrammingwithRetrofit_0.png
tag: Tech
originalTitle: "Network Calls in Android: Asynchronous Programming with Retrofit"
link: "https://medium.com/@paramjeet.singh0199/network-calls-in-android-asynchronous-programming-with-retrofit-b1312ff664df"
---


<img src="/assets/img/2024-05-20-NetworkCallsinAndroidAsynchronousProgrammingwithRetrofit_0.png" />

안녕하세요! 안드로이드 개발 세계에서 네트워크 호출을 효율적으로 처리하는 것이 매우 중요해요. 원격 서버에서 데이터를 가져오거나 클라우드에 데이터를 업로드한다 하더라도, 네트워크 작업은 시간이 오래 걸릴 수 있고 제대로 처리되지 않으면 사용자 경험에 상당한 영향을 줄 수 있어요. 여기서 Retrofit이 등장하는데, 이는 Square에서 개발한 안드로이드와 자바용 타입 안전한 HTTP 클라이언트에요. 이 글에서는 Retrofit의 내부 동작과 안드로이드에서 네트워크 호출을 수행하는 데 어떻게 비동기 프로그래밍으로 사용할 수 있는지 알아볼 거에요.

동기 호출의 문제점을 상상해보세요. 만약 앱이 서버에서 사용자 데이터를 다운로드해야 한다면, 개발자는 전통적인 동기 방식을 사용할 수 있어요:

<div class="content-ad"></div>

```js
문자열 userData = downloadUserDataSync();

// 이제 userData를 앱 로직에서 사용하세요...
```

이 방법은 직관적으로 보이지만 문제점이 있습니다: downloadUserDataSync()가 실행 중일 때 전체 앱이 차단됩니다. 사용자가 화면과 상호 작용할 수 없게 되며, 버튼이 응답하지 않는 것처럼 보이고 데이터 다운로드가 완료될 때까지 앱이 멈춰 있는 것처럼 보입니다. 이는 사용자 경험을 저하시킵니다.

비동기 프로그래밍 등장

비동기 프로그래밍을 통해 앱이 네트워크 응답을 기다리는 동안에도 계속 실행할 수 있습니다. 핵심 아이디어는 백그라운드에서 네트워크 호출을 시작하고 응답이 준비되었을 때 알림을 받는 것입니다. 이렇게 하면 주 스레드가 사용 가능해져 사용자의 상호작용에 민첩하게 대응할 수 있습니다.```

<div class="content-ad"></div>

레트로핏을 위한 구조체

레트로핏은 안드로이드에서 네트워크 호출을 간소화하고 비동기 프로그래밍과 완벽하게 통합되는 인기 있는 HTTP 클라이언트 라이브러리입니다. 인터페이스를 사용하여 API 엔드포인트를 정의하고 요청을 생성하고 응답을 처리하는 방법을 제공합니다.

다음은 레트로핏의 주요 기능 중 일부입니다:

- 쉬운 사용: 네트워크 요청을 만들고 응답을 구문 분석하는 복잡성을 추상화합니다.
- 타입 안전성: 작업 중인 데이터의 유형이 예상대로인지 확인합니다.
- 비동기 요청: 원활한 사용자 경험을 위해 필수적인 비동기 네트워크 호출을 지원합니다.
- 통합: OkHttp, RxJava, Gson과 같은 다른 라이브러리와 원활하게 작동합니다.

<div class="content-ad"></div>

# Retrofit 설정하기

```js
의존성 {
    구현체 'com.squareup.retrofit2:retrofit:2.9.0'
    구현체 'com.squareup.retrofit2:converter-gson:2.9.0'
}
```

이러한 종속성에는 Retrofit과 JSON 직렬화 및 역직렬화를 처리하기 위한 Gson 변환기가 포함되어 있습니다.

# API 인터페이스 정의하기

<div class="content-ad"></div>

Retrofit을 사용하려면 API의 엔드포인트를 Java 인터페이스로 정의해야 합니다. 사용자 목록을 제공하는 간단한 API와 작업 중이라고 가정해봅시다.

```js
public interface ApiService {
    @GET("/users")
    Call<List<User>> getUsers();
}
```

```bash
여기서 @GET("users")는 /users 엔드포인트로 GET 요청을 보낸다는 것을 나타내고, Call<List<User>>는 이 요청이 User 객체의 목록을 반환할 것임을 나타냅니다.
```

# Retrofit 인스턴스 생성하기

<div class="content-ad"></div>

다음으로 Retrofit의 인스턴스를 만들어보겠습니다. 보통은 애플리케이션에서 한 번만 이 작업을 수행하며 대부분 싱글톤 패턴으로 처리됩니다.

```java
public class ApiClient {
    private static final String BASE_URL = "https://api.example.com/";
    private static Retrofit retrofit = null;

    public static Retrofit getClient() {
        if (retrofit == null) {
            retrofit = new Retrofit.Builder()
                    .baseUrl(BASE_URL)
                    .addConverterFactory(GsonConverterFactory.create())
                    .build();
        }
        return retrofit;
    }
}
```

여기서 BASE_URL은 API의 루트 URL입니다. addConverterFactory(GsonConverterFactory.create())은 Retrofit에 Gson을 JSON 변환에 사용하도록 지시하는 부분입니다.

# 비동기 네트워크 호출하기

<div class="content-ad"></div>

Retrofit을 설정하면 이제 비동기 네트워크 호출을 할 수 있습니다. 사용자 목록을 가져오고 응답을 처리하는 방법은 다음과 같습니다:

```js
public class MainActivity extends AppCompatActivity {

    private ApiService apiService;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Retrofit retrofit = ApiClient.getClient();
        apiService = retrofit.create(ApiService.class);

        fetchUsers();
    }

    private void fetchUsers() {
        Call<List<User>> call = apiService.getUsers();
        call.enqueue(new Callback<List<User>>() {
            @Override
            public void onResponse(Call<List<User>> call, Response<List<User>> response) {
                if (response.isSuccessful()) {
                    List<User> users = response.body();
                    // 사용자 목록 처리
                } else {
                    // 오류 처리
                }
            }

            @Override
            public void onFailure(Call<List<User>> call, Throwable t) {
                // 실패 처리
            }
        });
    }
}
```

`fetchUsers` 메서드에서 enqueue를 호출하여 네트워크 요청을 비동기적으로 수행합니다. `onResponse` 메서드는 요청이 성공한 경우에 호출되고, `onFailure` 메서드는 요청이 실패한 경우에 호출됩니다.

# 다양한 응답 유형 처리

<div class="content-ad"></div>

레트로핏은 일반 텍스트, JSON 및 사용자 정의 타입을 포함한 다양한 유형의 응답을 지원합니다. 예를 들어, API가 추가 메타데이터를 포함한 JSON 객체를 반환하는 경우 사용자 정의 응답 유형을 정의할 수 있습니다:

```js
public class ApiResponse<T> {
    private T data;
    private String status;
    private String message;

    // Getter 및 Setter 메서드
}
```

그런 다음, API 인터페이스를 이 사용자 정의 타입을 사용하도록 업데이트하십시오:

```js
public interface ApiService {
    @GET("users")
    Call<ApiResponse<List<User>>> getUsers();
}
```

<div class="content-ad"></div>

그리고 액티비티나 프래그먼트에서 응답을 처리해보세요:

```js
call.enqueue(new Callback<ApiResponse<List<User>>>() {
    @Override
    public void onResponse(Call<ApiResponse<List<User>>> call, Response<ApiResponse<List<User>>> response) {
        if (response.isSuccessful()) {
            ApiResponse<List<User>> apiResponse = response.body();
            if (apiResponse != null && "success".equals(apiResponse.getStatus())) {
                List<User> users = apiResponse.getData();
                // 사용자 목록 처리
            } else {
                // API 오류 처리
            }
        } else {
            // 에러 처리
        }
    }

    @Override
    public void onFailure(Call<ApiResponse<List<User>>> call, Throwable t) {
        // 실패 처리
    }
});
```

# 고급 사용법: 인터셉터와 로깅

네트워크 요청에 대한 더 많은 제어를 위해 OkHttp를 사용하여 인터셉터를 추가할 수 있습니다. 인터셉터는 요청 및 응답을 수정하고 세부 정보를 기록하며 인증을 처리할 수 있습니다.

<div class="content-ad"></div>

먼저 OkHttp 종속성을 추가해주세요:

```js
implementation 'com.squareup.okhttp3:logging-interceptor:4.9.0'
```

그런 다음 인터셉터를 사용하여 OkHttp 클라이언트를 설정하세요:

```js
HttpLoggingInterceptor logging = new HttpLoggingInterceptor();
logging.setLevel(HttpLoggingInterceptor.Level.BODY);

OkHttpClient client = new OkHttpClient.Builder()
        .addInterceptor(logging)
        .build();

Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(BASE_URL)
        .client(client)
        .addConverterFactory(GsonConverterFactory.create())
        .build();
```

<div class="content-ad"></div>

이 설정을 통해 모든 네트워크 요청과 응답이 로깅되어 문제를 디버그하기가 더 쉬워집니다.

# 결론

Retrofit은 안드로이드에서 네트워크 호출을 처리하는 강력하고 유연한 라이브러리입니다. 그 간결함과 방대한 사용자 정의 옵션을 결합하여 네트워크 작업을 처리하는 데 탁월한 선택지가 됩니다. Retrofit을 사용함으로써 복잡한 API 및 대규모 데이터셋을 다룰 때에도 앱이 효율적으로 작동하고 부드러운 사용자 경험을 제공할 수 있습니다.

본 문서에서 제공된 예제와 지침을 따라 Retrofit을 안드로이드 프로젝트에 통합하고 비동기 프로그래밍을 위한 기능을 활용할 수 있는 능력을 습득할 수 있을 것입니다. 즐거운 코딩 되세요!