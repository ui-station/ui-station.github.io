---
title: " Jetpack Compose vs 전통적 안드로이드 반응형 UI를 위한 대결 "
description: ""
coverImage: "/assets/img/2024-06-23-JetpackComposevsTraditionalAndroidTheBattleforResponsiveUIs_0.png"
date: 2024-06-23 01:29
ogImage:
  url: /assets/img/2024-06-23-JetpackComposevsTraditionalAndroidTheBattleforResponsiveUIs_0.png
tag: Tech
originalTitle: "🚀 Jetpack Compose vs. Traditional Android: The Battle for Responsive UIs! 📱🤖💡"
link: "https://medium.com/@cohen.n.raphael/responsive-ui-jetpack-compose-vs-18ec617aa216"
---

![이미지](/assets/img/2024-06-23-JetpackComposevsTraditionalAndroidTheBattleforResponsiveUIs_0.png)

# Jetpack Compose:

- 적응형 레이아웃:

  - 의미: Jetpack Compose는 다양한 화면 크기 및 방향에 매끄럽게 적응하는 레이아웃을 만들도록 권장합니다.
  - 방법: fillMaxWidth, fillMaxHeight 및 padding과 같은 수정자를 사용하여 사용 가능한 공간에 따라 레이아웃 속성을 동적으로 조정합니다. 또한 Modifier.weight(f)를 사용하여 공간, 높이, 너비 또는 자식 Composables의 크기를 비례적으로 분배할 수 있습니다.

- 예시:

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
Column(
    modifier = Modifier.fillMaxSize().padding(16.dp)
) {
    Row {
      Text(text = "안녕하세요!", modifier = Modifier.weight(1f))
      Text(text = "컴포즈!", modifier = Modifier.weight(1f))
    }
}
```

- 왜 중요한가요?: 적응형 레이아웃으로 여러 기기(소형 폰부터 대형 태블릿까지)에서 앱이 멋지게 보입니다.

2. Compose의 ConstraintLayout:

- 무엇인가요?: ConstraintLayout은 UI 요소 간의 관계를 정의할 수 있는 강력한 레이아웃 매니저입니다.
- 어떻게 사용하나요?: 아래 예시에서 text1과 text2는 Text Composable에 할당된 레이아웃 ID이며, top.linkTo(text1.bottom)을 사용하여 요소를 text1 아래에 배치할 수 있습니다.
- 예시:

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
ConstraintLayout(Modifier.fillMaxSize()) {
    val (text1, text2) = createRefs()
    Text("Top", modifier = Modifier.constrainAs(text1) {
        top.linkTo(parent.top)
    })
    Text("Bottom", modifier = Modifier.constrainAs(text2) {
        top.linkTo(text1.bottom)
    })
}
```

- 왜 멋진가요: ConstraintLayout은 복잡한 UI 배열을 간소화하고 태블릿 및 휴대전화 모두에서 반응성을 보장합니다.

3. 창 크기 클래스:

- 이것들은 무엇인가요: 창 크기 클래스는 다양한 화면 크기(작은, 중간, 큰 등)를 나타냅니다.
- 어떻게 사용하나요: LocalConfiguration.current.screenLayout를 통해 창 크기 클래스에 액세스할 수 있습니다.
- 예시:

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
val screenSize = LocalConfiguration.current.screenLayout
when (screenSize) {
    Configuration.SCREENLAYOUT_SIZE_SMALL -> // 작은 화면 처리
    Configuration.SCREENLAYOUT_SIZE_NORMAL -> // 일반 화면 처리
    // 기타 경우
}
```

- 이유: 특정 차원을 하드코딩하지 않고 사용 가능한 공간에 기반하여 사용자 인터페이스를 맞춤화할 수 있습니다. 💃🏽🕺🏿

# 전통적인 안드로이드 개발:

- 리소스 한정자:

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

- 무엇인가요: 자원 한정자를 사용하면 다양한 화면 크기와 밀도에 대해 다른 레이아웃을 제공할 수 있습니다.
- 사용 방법: 레이아웃-작은, 레이아웃-큰 등과 같은 자원 폴더를 만들고 레이아웃 파일을 해당 폴더에 배치합니다. OS에서 기기 사양에 따라 어떤 레이아웃 파일을 inflate 할지 결정합니다.
- 예시:

```java
res/
    layout/
        activity_main.xml
    layout-large/
        activity_main.xml (큰 화면에 최적화된 파일)
```

- 왜 중요한가요: 자원 한정자는 다양한 기기들 사이에서 일관된 UI를 보장합니다.

2. 뷰 내 ConstraintLayout:

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

- 무엇인가요: ConstraintLayout은 평평한 뷰 계층 구조로 복잡한 UI 레이아웃을 만들 수 있게 해줍니다 😊
- 어떻게 사용하나요: 아래 XML은 두 개의 TextView 요소를 포함하는 ConstraintLayout을 정의합니다. 첫 번째 TextView는 부모의 시작에 고정되고 위쪽과 정렬됩니다. 두 번째 TextView는 첫 번째 TextView의 오른쪽에 위치하며 위쪽으로 정렬됩니다. 이 레이아웃은 두 개의 TextView가 ConstraintLayout 내에서 수평으로 인접하도록 보장합니다.
- 예시:

```js
<androidx.constraintlayout.widget.ConstraintLayout
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
>
  <TextView
    android:id="@+id/textView1"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="첫 번째 TextView"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent"
  />

  <TextView
    android:id="@+id/textView2"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="두 번째 TextView"
    app:layout_constraintStart_toEndOf="@+id/textView1"
    app:layout_constraintTop_toTopOf="parent"
  />
</androidx.constraintlayout.widget.ConstraintLayout>
```

- 그들이 필수적인 이유: 모든 중첩된 레이아웃은 레이아웃 계산 중 추가적인 처리 시간을 소모합니다. ConstraintLayout은 뷰 간의 유연한 관계를 조성하고 중첩된 뷰 그룹을 최소화하여 전체 성능을 크게 향상시킵니다.

반응형 Android 레이아웃을 개발하는 것은 다양한 화면 크기에 일관된 사용자 경험을 제공하기 위해 중요합니다. 다른 기기에서 레이아웃을 확인하여 예상치 못한 문제를 파악하는 것을 기억해 주세요. 그리고 위 Lonely T-Rex 이미지가 궁금하다면 - 그것은 구글 크롬 오프라인 게임으로, Jetpack Compose를 이용해 만들어진 것입니다 👍🏽 구글 플레이에서 다운로드하고 리뷰를 남겨서 이상한 알고리즘을 돕는 데 도움을 주세요 🚀📱

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

🗣️: 문의해 주세요
LinkedIn: [링크드인 프로필](https://www.linkedin.com/in/raphael-c-8b43612b6/)

감사합니다,
RC
