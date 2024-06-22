---
title: "Jetpack Glance - 알아두어야 할 주요 기능 및 장점"
description: ""
coverImage: "/assets/img/2024-06-23-JetpackGlance_0.png"
date: 2024-06-23 01:28
ogImage: 
  url: /assets/img/2024-06-23-JetpackGlance_0.png
tag: Tech
originalTitle: "Jetpack Glance"
link: "https://medium.com/proandroiddev/jetpack-glance-86a2fc30dd5d"
---


## 안드로이드에서 아름다운 위젯을 만드는 Compose 방법

![image](/assets/img/2024-06-23-JetpackGlance_0.png)

여러 해 동안 위젯은 안드로이드 경험의 중요한 부분이었습니다. 그러나 이를 만들고 유지하는 것은 종종 괴로운 과정이었습니다. 전통적인 방법은 복잡할 수 있으며 명확한 설명 없이 예상치 못한 동작이 발생할 수 있습니다.

여기서 Jetpack Glance가 혁신적인 새로운 방식으로 등장하여 문제를 해결합니다. Jetpack Compose의 선언적 UI의 강점을 활용하여 Glance는 위젯 개발을 간소화하고 더 쉽고 즐겁게 안드로이드 애플리케이션용 아름다운 기능적 위젯을 만드는데 도움을 줍니다.

<div class="content-ad"></div>

최근에 출시된 Jetpack Glance 1.1.0에 대해 얘기해볼까요? 이번 릴리즈는 위젯 개발을 간편하게 해줄 수 있는 잠재력에 대해 특별한 관심을 불러일으키는군요. 그래서 저는 Glance의 기능을 직접 경험해보기로 결심했습니다. 이 기능들을 시험해보기 위해, 저는 내 책 앱을 위한 위젯을 만들기로 했습니다. 이 위젯은 사용자 데이터에 접근하고 마지막으로 읽은 책을 계속할 수 있는 편리한 단축키를 제공하기 위해 Glance의 기능을 활용할 것입니다.

개발 프로세스에 대해 자세히 살펴보고 전체적인 실습 경험을 위해 직접 위젯을 만들어보겠습니다.

# 구조 정의

어떠한 프로젝트라도 시작하기 전에 필요한 종속성을 설정하는 것부터 시작해야 합니다. 이 경우, 두 가지 주요 Jetpack Glance 라이브러리를 활용할 것입니다.

<div class="content-ad"></div>

```js
[버전]

glance = "1.1.0"
...

[라이브러리]

glance-appwidget = { group = "androidx.glance", name = "glance-appwidget", version.ref = "glance" }
glance-material = { group = "androidx.glance", name = "glance-material3", version.ref = "glance" }
...
```

의존성이 설정된 상태에서 이제 코드 자체에 집중할 수 있습니다.

코드 구성 및 재사용성을 증진하기 위해 위젯 전용 패키지를 별도로 생성하기로 결정했습니다. 이 접근 방식을 통해 위젯 관련 코드를 단일하고 명확한 위치에 그룹화할 수 있습니다.

저희 Glance 위젯 개발은 MyBooksAppWidget과 MyBooksAppWidgetReceiver 두 가지 핵심 클래스를 설정하는 데서 시작됩니다.

<div class="content-ad"></div>

- MyBooksAppWidget: 이 클래스는 GlanceAppWidget을 상속받습니다 (나의 경우에는 의존성 주입을 위해 Koin을 사용합니다. 선호하는 것을 선택할 수 있습니다). 이 클래스 안에서 위젯의 레이아웃을 정의할 것입니다.

```js
class MyBooksAppWidget : GlanceAppWidget(), KoinComponent {
...
}
```

- MyBooksAppWidgetReceiver: 이 클래스는 GlanceAppWidgetReceiver를 상속받으며 위젯 인스턴스 (MyBooksAppWidget)의 초기화를 담당합니다.

```js
class MyBooksAppWidgetReceiver : GlanceAppWidgetReceiver() {
    override val glanceAppWidget: GlanceAppWidget = MyBooksAppWidget()
}
```

<div class="content-ad"></div>

xml 디렉토리에 새 리소스 파일을 만들어야 합니다 (저의 경우 my_book_widget_info로 이름 짓겠습니다). 이 파일은 위젯의 구성을 나타내며 홈 화면에서의 동작과 외관을 정의하는 다양한 속성을 제공합니다.

```js
<?xml version="1.0" encoding="utf-8"?>
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:resizeMode="horizontal|vertical"
    android:updatePeriodMillis="3600000"
    android:minHeight="50dp"
    android:minWidth="100dp"
    android:minResizeHeight="50dp"
    android:minResizeWidth="50dp"
    android:widgetCategory="home_screen"
    android:configure="com.stefanoq21.mybooks.MainActivity"
    android:widgetFeatures="configuration_optional|reconfigurable"
    android:previewImage="@drawable/widget_preview"
    android:maxResizeHeight="512dp"
    android:maxResizeWidth="512dp"
    android:targetCellWidth="2"
    android:targetCellHeight="1"
    android:initialLayout="@layout/glance_default_loading_layout"
    android:description="@string/widget_picker_description"
    >
</appwidget-provider>
```

이제 주요 속성들을 살펴보고 사용자 경험에 어떻게 기여하는지 알아보겠습니다.

- 최소/최대/조정 가능 폭 및 높이는 위젯의 경계를 정의하고 홈 페이지에서 어떻게 조절될 수 있는지를 나타냅니다.
- initialLayout은 위젯이 데이터를 앱 로직이나 데이터베이스에서 검색하는 동안 표시될 레이아웃을 정의합니다. 이 임시 레이아웃은 사용자가 데이터 검색 과정 중에 빈 공간을 보지 않도록 플레이스홀더 역할을 합니다. glance_default_loading_layout를 사용하면 Glance 자체에서 제공하는 표준 로딩 화면을 활용할 수 있습니다.
- previewImage 및 description은 위젯이 픽커 내에서 어떻게 보여질지를 정의합니다.

<div class="content-ad"></div>


<img src="/assets/img/2024-06-23-JetpackGlance_1.png" />

Glance 위젯의 기본 구조를 설정하는 마지막 단계는 AndroidManifest.xml 파일 내에서 수신기 클래스를 등록하는 것입니다. 이 등록 프로세스는 Android 시스템에 위젯 수신기의 존재를 알리고, 해당 브로드캐스트를 수신하여 위젯 업데이트를 트리거할 수 있도록합니다.

```js
 <receiver
            android:name=".widget.MyBooksAppWidgetReceiver"
            android:exported="true"
            android:label="@string/widget_receiver_label">

            <intent-filter>
                <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
            </intent-filter>

            <meta-data
                android:name="android.appwidget.provider"
                android:resource="@xml/my_book_widget_info" />
        </receiver>
```

핵심 구조를 확립했으므로 이제 위젯의 레이아웃 생성에 집중해 보겠습니다. Glance를 사용하면 다양한 시나리오에 대응하는 적응형 레이아웃을 디자인할 수 있습니다. 이러한 적응성은 데이터 수신 및 홈 화면에서 사용자가 선택한 크기에 따라 위젯이 표시를 조정함을 보장합니다.


<div class="content-ad"></div>

# 적응형 레이아웃

MyBooksAppWidget 클래스는 위젯의 시각적 모습을 결정하는 중요한 역할을 합니다.

여기에서는 레이아웃을 사용자 정의해야 하는 두 가지 중요한 오버라이드가 있습니다:

sizeMode: 이것은 위젯이 홈 화면의 사용 가능한 공간에 따라 크기를 어떻게 관리하는지 제어합니다. Glance는 세 가지 옵션을 제공합니다:

<div class="content-ad"></div>

- SizeMode.Single: 위젯은 크기가 고정되어 있으며 크기 변경에 반응하지 않습니다.
- SizeMode.Exact: 위젯은 시스템에서 모든 크기 업데이트를 받아들여 완전히 동적 레이아웃 조정이 가능합니다.
- SizeMode.Responsive: 이 모드는 위젯이 적응해야 할 특정 크기를 정의하도록 허용하여 더 많은 제어를 제공합니다.

내 예시에서는 SizeMode.Responsive를 선택하고 ICON_SQUARE, SMALL_SQUARE, MEDIUM_SQUARE라는 크기를 지정했습니다. 이를 통해 위젯이 사용 가능한 공간에 따라 레이아웃을 조정하고 서로 다른 크기에 대해 다른 내용이나 레이아웃을 표시할 수 있습니다.

```js
class MyBooksAppWidget : GlanceAppWidget(), KoinComponent {

    companion object {
        internal val ICON_SQUARE = DpSize(50.dp, 50.dp)
        internal val SMALL_SQUARE = DpSize(100.dp, 100.dp)
        internal val MEDIUM_SQUARE = DpSize(150.dp, 150.dp)
    }

    override val sizeMode = SizeMode.Responsive(
        setOf(
            ICON_SQUARE,
            SMALL_SQUARE,
            MEDIUM_SQUARE,
        )
    )
...
```

provideGlance 함수는 위젯의 시각적 표현의 핵심을 형성합니다. 여기에서 Jetpack Compose 컴포저블을 사용하여 실제 레이아웃을 정의할 수 있습니다.

<div class="content-ad"></div>

```kotlin
...
 재정의 suspend fun provideGlance(context: Context, id: GlanceId) {
        val repository: DatabaseRepository by inject()
        provideContent {
            val book by repository.getLastOpenedLibraryItemFlow().collectAsState(null)
            GlanceTheme {
                if (book != null) {
                    BookState(
                        book!!, onClick =
                        actionStartActivity(
                            Intent(context.applicationContext, MainActivity::class.java)
                                .setAction(Intent.ACTION_VIEW)
                                .setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP)
                                .setData("https://com.stefanoq21.mybooks/ReaderEpub/${book!!.bookId}".toUri())
                        )
                    )
                } else {
                    ZeroState(
                       onClick =
                        actionStartActivity(
                            Intent(context.applicationContext, MainActivity::class.java)
                                .setAction(Intent.ACTION_VIEW)
                                .setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP)
                        )
                    )
                }
            }
        }
    }
```

제공된 코드 조각은 귀하의 Glance 위젯 기능의 두 가지 주요 측면을 보여줍니다:

- 조건부 레이아웃: 데이터가 있는 경우 (BookState)와 데이터가 없는 경우 (ZeroState)를 위한 두 가지 다른 레이아웃을 구현했습니다. 이를 통해 위젯이 다른 시나리오에서 사용자에게 적절한 피드백을 제공합니다.
- 액션: onClick 매개변수로 전달된 액션들. 하나의 액션은 MainActivity를 실행하고, 다른 하나는 Jetpack Compose Navigation의 딥 링킹 기능을 활용하여 앱 내의 특정 화면으로 이동합니다. 이를 통해 위젯에서 앱의 관련 부분으로 원활하게 전환할 수 있습니다.

ZeroState 컴포저블은 표시할 관련 데이터가 없을 때 위젯에 표시되는 플레이스홀더 레이아웃으로 작동합니다. 이 시나리오는 사용자가 책을 읽기 전에 위젯을 추가한 경우에 발생합니다.


<div class="content-ad"></div>

```kotlin
import androidx.glance.Button
import androidx.glance.GlanceModifier
import androidx.glance.GlanceTheme
import androidx.glance.ImageProvider
import androidx.glance.LocalSize
import androidx.glance.action.Action
import androidx.glance.action.clickable
import androidx.glance.appwidget.components.Scaffold
import androidx.glance.appwidget.components.TitleBar
import androidx.glance.layout.Alignment
import androidx.glance.layout.Box
import androidx.glance.layout.fillMaxSize

@Composable
fun ZeroState(onClick: Action) {
    val size = LocalSize.current
    if (size.width <= ICON_SQUARE.width) {
        IconDimension(onClick)
    } else {
        Scaffold(
            titleBar = {
                TitleBar(
                    startIcon = ImageProvider(R.drawable.ic_launcher_foreground),
                    textColor = GlanceTheme.colors.onSurface,
                    title = LocalContext.current.getString(R.string.widget_title),
                )
            },
            backgroundColor = GlanceTheme.colors.widgetBackground,
            modifier = GlanceModifier.fillMaxSize().clickable(onClick),
        ) {
            Box(modifier = GlanceModifier.fillMaxSize(), contentAlignment = Alignment.Center) {
                Button(
                    text = LocalContext.current.getString(R.string.start_reading),
                    onClick = onClick
                )
            }
        }
    }
}
```

Glance는 위젯에 대해 특별히 설계된 별도의 조합 기능을 제공한다는 점을 강조하기 위해 이 코드 조각에 import 문이 포함되어 있습니다.

ZeroState 조합은 Glance의 적응형 레이아웃을 만들어 사용 가능한 공간에 따라 프레젠테이션을 조정하는 능력을 보여줍니다. 이를 통해 사용자가 선택한 크기와 관계없이 위젯이 유용하고 정보 전달이 되도록합니다. 너비를 기준으로하면, 더 작은 크기에는 IconDimension 구성요소를 조건부로 렌더링하고, 보다 복잡한 레이아웃에서는 제목 표시줄과 버튼과 같은 구성요소를 조건부로 렌더링합니다.

<img src="https://miro.medium.com/v2/resize:fit:1400/1*ukJec6XVolL-uwNf9VbUhQ.gif" />


<div class="content-ad"></div>

BookState 코움파서블은 ZeroState의 기반을 두고 있습니다. 사용자가 현재 읽고 있는 책 또는 최근 상호 작용한 책에 대한 정보를 표시하는 데 사용됩니다. ZeroState와 유사하게 버튼은 충분한 수직 공간이 있을 때에만 표시됩니다. 이는 레이아웃이 균형을 유지하고 시각적 혼란을 피하기 위해 보장합니다.

```kotlin
@Composable
fun BookState(book: LibraryItem, onClick: Action) {
    val size = LocalSize.current
    if (size.width <= MyBooksAppWidget.ICON_SQUARE.width) {
        IconDimension(onClick)
    } else {
        Scaffold(
            backgroundColor = GlanceTheme.colors.widgetBackground,
            modifier = GlanceModifier.fillMaxSize().clickable(onClick)
        ) {
            Column(
                modifier = GlanceModifier.fillMaxSize()
                    .padding(bottom = 8.dp),
                verticalAlignment = Alignment.Vertical.CenterVertically,
                horizontalAlignment = Alignment.Horizontal.CenterHorizontally,
            ) {
                Text(
                    text = book.title,
                    style = TextStyle(
                        fontWeight = FontWeight.Bold,
                        fontSize = 16.sp,
                        color = (GlanceTheme.colors.onSurface),
                    ),
                )
                if (size.height >= MyBooksAppWidget.MEDIUM_SQUARE.height) {
                    Spacer(GlanceModifier.size(16.dp))
                    Button(
                        text = LocalContext.current.getString(R.string.widget_with_state_button),
                        onClick = onClick
                    )
                }
            }
        }
    }
}
```

<img src="https://miro.medium.com/v2/resize:fit:1400/1*WqumbHh4UZoC8ewJXXcvCw.gif" />

# 위젯 업데이트

<div class="content-ad"></div>

Glance는 위젯 구성(my_book_widget_info)에서 정의된 간격에 따라 위젯 업데이트를 관리하지만 즉시 업데이트를 원하는 경우가 있을 수 있습니다.

Glance는 이 수동 업데이트를 수행하는 편리한 방법을 제공합니다. 앱 로직 내부에서 MyBooksAppWidget().updateAll(context)를 간단히 호출하면 됩니다. 이 방법은 Glance에게 사용자 홈 화면의 위젯 모든 인스턴스를 업데이트하도록 지시하여 최신 정보를 반영합니다.

# 위젯 홍보하기

가치 있는 Glance 위젯을 만드는 것은 첫 번째 단계일 뿐입니다. 사용자가 이를 발견하고 활용할 수 있도록하기 위해서는 효과적인 홍보 전략을 구현하는 것이 중요합니다.
내 앱에서는 현재 책을 다시 읽을 수 있도록 도와주는 위젯을 개발했습니다. 그러나 사용자가 이를 찾아보기 위해 위젯 목록을 활발히 찾아보지 않을 수도 있습니다.

<div class="content-ad"></div>

그래서 앱의 특정 지점에 위젯을 홍보하는 함수를 정의할 수 있습니다.

```js
fun promoteWidget(context: Context) {
    val appWidgetManager = AppWidgetManager.getInstance(context)
    val myProvider = ComponentName(context, MyBooksAppWidgetReceiver::class.java)
    if (appWidgetManager.isRequestPinAppWidgetSupported) {
        appWidgetManager.requestPinAppWidget(myProvider, null, null)
    }
}
```

만약 기기가 requestPinAppWidget 메소드를 지원하고 사용자 시스템이 허용한다면, 코드는 시스템에서 제공하는 하단 시트를 트리거할 것입니다. 해당 하단 시트는 사용자에게 위젯에 대해 안내하고 집 화면에 직접 추가할 수 있는 옵션을 제공할 것입니다. 이 접근 방식은 위젯 발견을 위한 매끄럽고 사용자 친화적인 경험을 제공합니다.

결과물은 다음과 같을 것입니다:

<div class="content-ad"></div>

![JetpackGlance_2](/assets/img/2024-06-23-JetpackGlance_2.png)

# 결론

이 기사는 Glance 위젯을 만드는 과정을 안내했습니다. 우리는 위젯의 기본 구조를 설정하는 과정을 탐구하고, 다양한 시나리오에 대한 레이아웃을 정의하며, 사용자 상호작용을 처리하는 방법을 이해했습니다. 추가로, 위젯을 홍보하고 사용자의 채택을 촉진하는 전략을 탐구했습니다.

이 지식을 활용하여 자신만의 Glance 위젯을 만들어 보세요! 다양한 기능을 실험하고 어떻게 앱과 사용자에게 이점을 줄 수 있는지 탐색해보세요. 기억하세요, 핵심은 가치를 제공하고 훌륭한 사용자 경험을 제공하는 데 있습니다.

<div class="content-ad"></div>

친구야! 댓글을 자유롭게 공유해 주세요. 또는 LinkedIn에서 연락해도 괜찮아요.

좋은 하루 보내세요!