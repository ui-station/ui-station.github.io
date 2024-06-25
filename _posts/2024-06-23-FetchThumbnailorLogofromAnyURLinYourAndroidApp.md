---
title: "안드로이드 앱에서 URL로 썸네일 또는 로고 가져오는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-FetchThumbnailorLogofromAnyURLinYourAndroidApp_0.png"
date: 2024-06-23 20:56
ogImage:
  url: /assets/img/2024-06-23-FetchThumbnailorLogofromAnyURLinYourAndroidApp_0.png
tag: Tech
originalTitle: "Fetch Thumbnail or Logo from Any URL in Your Android App"
link: "https://medium.com/@walidkhan345/fetch-thumbnail-or-logo-from-any-url-in-your-android-app-b91d5e4ad604"
---

이 기사에서는 안드로이드 애플리케이션에서 주어진 URL에서 섬네일이나 로고를 가져와서 표시하는 방법을 안내하겠습니다. 네트워크 요청에는 Retrofit, HTML 파싱에는 Jsoup, 이미지 로딩에는 Glide를 사용할 예정입니다.

# 특징

- 주어진 URL에서 HTML 콘텐츠를 가져옵니다.
- HTML을 파싱하여 섬네일이나 로고 URL을 추출합니다.
- Glide를 사용하여 가져온 이미지를 표시합니다.
- 섬네일이나 로고를 찾을 수 없는 경우 기본 로고로 대체합니다.

# 사용 사례

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

이 앱은 다음과 같은 여러 시나리오에 유용할 수 있어요:

- 메시지나 소셜 미디어 앱에서 공유된 URL에 미리보기 이미지 표시하기.
- 콘텐츠 집계 앱에서 블로그 글이나 기사의 썸네일 표시하기.
- 북마크 관리자에서 링크와 관련된 로고나 이미지를 가져와 표시하기.

# 시작하기

# 요구 사항

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

- 안드로이드 스튜디오
- 코틀린
- 인터넷 연결

# 종속성

다음 종속성을 build.gradle.kts 파일에 추가해주세요:

```js
implementation("com.squareup.retrofit2:retrofit:2.9.0");
implementation("com.squareup.retrofit2:converter-scalars:2.9.0");
implementation("org.jsoup:jsoup:1.13.1");
implementation("com.github.bumptech.glide:glide:4.11.0");
kapt("com.github.bumptech.glide:compiler:4.11.0");
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

# 사용 방법

- 저장소를 복제합니다:

  bash
  git clone https://github.com/WalidAhmed90/Fetch-Thumbnail-or-Logo-in-Android-App.git
  cd Fetch-Thumbnail-or-Logo-in-Android-App

2. Android Studio에서 프로젝트를 엽니다.

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

3. 안드로이드 기기 또는 에뮬레이터에서 프로젝트를 빌드하고 실행하세요.

4. URL을 입력하고 "썸네일 생성" 버튼을 클릭하여 썸네일이나 로고를 가져와 표시하세요.

# 코드 개요

## API 서비스

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

ApiService 인터페이스는 URL에서 HTML 콘텐츠를 가져오는 메서드를 정의합니다:

```js
interface ApiService {
    @GET
    suspend fun fetchHtml(@Url url: String): String
}
```

## 썸네일 가져오기

fetchThumbnail 함수는 HTML 콘텐츠를 가져와 썸네일이나 로고를 찾아 Glide를 사용하여 보여줍니다.

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
fun fetchThumbnail(url: String) {
        mBinding.progress.visibility = View.VISIBLE
        val retrofit = Retrofit.Builder()
            .baseUrl("https://example.com/")  // 가짜 기본 URL, 덮어쓰기될 예정
            .client(OkHttpClient())
            .addConverterFactory(ScalarsConverterFactory.create())
            .build()

        val apiService = retrofit.create(ApiService::class.java)

        CoroutineScope(Dispatchers.IO).launch {
            try {
                val html = apiService.fetchHtml(url)
                Log.d("HTML_CONTENT", html) // 가져온 HTML 내용 로깅

                val document = Jsoup.parse(html)
                val metaTags = document.select("meta[property=og:image], meta[name=twitter:image]")

                var thumbnailUrl: String? = null
                for (metaTag in metaTags) {
                    val content = metaTag.attr("content")
                    if (content.isNotEmpty()) {
                        thumbnailUrl = content
                        break
                    }
                }

                if (thumbnailUrl == null) {
                    val mainImage: Element? = document.select("table.infobox img").first()
                    mainImage?.let {
                        thumbnailUrl = "https:${it.attr("src")}"
                    }
                }

                // 썸네일 이미지를 찾을 수 없는 경우 도메인 로고로 대체
                if (thumbnailUrl == null) {
                    val domain = Uri.parse(url).host ?: ""
                    thumbnailUrl = "https://logo.clearbit.com/$domain"
                }

                if (thumbnailUrl != null) {
                    launch(Dispatchers.Main) {
                        mBinding.progress.visibility = View.GONE
                        Glide.with(this@MainActivity)
                            .load(thumbnailUrl)
                            .into(mBinding.thumbnailImage)
                    }
                } else {
                    mBinding.progress.visibility = View.GONE
                    Log.d("THUMBNAIL", "이미지를 찾을 수 없습니다.")
                }
            } catch (e: Exception) {
                e.printStackTrace()
                mBinding.progress.visibility = View.GONE
            }
        }
    }
```

## Layout

activity_main.xml 레이아웃 파일은 다음과 같이 UI 구성요소를 정의합니다:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
  >

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"
            android:padding="16dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            tools:context=".MainActivity">

            <EditText
                android:id="@+id/urlInput"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textColor="@color/grey"
                android:layout_marginTop="20dp"
                android:hint="URL 입력" />

            <Button
                android:id="@+id/generateButton"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginTop="10dp"
                android:text="썸네일 생성" />

            <ImageView
                android:id="@+id/thumbnailImage"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:adjustViewBounds="true"
                android:contentDescription="썸네일"
                android:layout_marginTop="10dp"
                android:scaleType="fitCenter"
                android:visibility="visible" />

            <com.google.android.material.progressindicator.CircularProgressIndicator
                android:id="@+id/progress"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                app:indicatorColor="@color/green"
                android:indeterminate="true"
                android:layout_gravity="center_horizontal"
                android:visibility="gone"
                />

        </LinearLayout>


    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
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

# 기여하기

문제를 발견하거나 개선 제안이 있는 경우 문제를 열거나 풀 요청을 제출해주세요.

# 결과:

![Output](https://miro.medium.com/v2/resize:fit:1200/1*606E5BSXDVXR6xuwV6sFSg.gif)
