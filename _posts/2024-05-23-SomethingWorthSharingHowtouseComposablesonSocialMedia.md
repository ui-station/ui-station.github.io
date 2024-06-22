---
title: "유용한 정보 공유 - 소셜 미디어에서 Composables 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-05-23-SomethingWorthSharingHowtouseComposablesonSocialMedia_0.png"
date: 2024-05-23 12:51
ogImage: 
  url: /assets/img/2024-05-23-SomethingWorthSharingHowtouseComposablesonSocialMedia_0.png
tag: Tech
originalTitle: "Something Worth Sharing — How to use Composables on Social Media"
link: "https://medium.com/@christopher.mederos/something-worth-sharing-cf3e3f5083cf"
---


![Screenshot](/assets/img/2024-05-23-SomethingWorthSharingHowtouseComposablesonSocialMedia_0.png)

# 사진이 없으면 일어나지 않았어요

인스타그램 게시물은 황홀한 모험의 마무리 또는 무기화된 FOMO의 조각일 수 있어요. 어쨌든 사람들은 자신의 하이라이트와 취미를 소셜 미디어에 기억하기를 좋아해요.

최근에 Viz 앱에 공유 기능을 추가했어요. 이제 다이버와 스노클러도 이 의식에 참여할 수 있어요. 당시에는 Compose에서 생성된 아름다운 뷰를 Instagram, TikTok, Twitter 등에서 공유 가능한 이미지로 어떻게 변환하는지에 대해 온라인에 많이 쓰여 있지 않았어요.

<div class="content-ad"></div>

이제 다른 쪽으로 나와서 스스로 구현하는 방법을 안내해 드릴게요! 순서는 대략 다음과 같아요:

- 공유하고 싶은 모든 콘텐츠를 포함하는 컴포저블 만들기
- 컴포저블을 이미지로 변환하기
- 안드로이드의 Sharesheet를 사용하여 이미지를 공유하기

제가 공유된 백 엔드 코드를 작성할 때 KMP를 사용하고 있지만, UI는 Android 및 iOS에 대해 별도로 네이티브로 작성했어요. Android 버전은 아래에 작성되어 있고 SwiftUI 버전은 다음 주 별도의 기사에서 공유될 예정이에요!

# 공유 가능한 형식 디자인하기

<div class="content-ad"></div>

삼격하고 상대적으로 높은 이동성을 가지고 있는 정사각형은 모든 소셜 미디어 플랫폼에서 최적의 화면 비율이에요. 인스타그램을 예로 들어보면—이 모양은 스크롤 피드에서 "게시물"로 공유되거나 전체 화면 "스토리"에 쉽게 가운데 정렬될 수 있어요.

이 이상적인 크기는 두 가지 요소에 따라 다릅니다:

- 이미지로 공유될 때 선명하고 뚜렷하게 보이기 위한 충분한 세부 정보가 있는 크기
- 디자인 시스템에서 폰트 스타일 토큰을 수용할 수 있는 크기 (원시 작성, Material3 또는 다른 것)

저는 안드로이드에서 400.dp부터 500.dp 크기가 이 두 목표를 모두 잘 이루어낼 수 있다고 생각해요. 대부분의 휴대폰에서 전체 화면 크기로 변환될 거예요. 그리고 이 크기에서 '제목', '헤드라인', '본문'과 같은 폰트 스타일을 사용하면 본인만의 원시 폰트 크기를 계산할 필요 없이 손쉽게 사용할 수 있어요.

<div class="content-ad"></div>

```kotlin
@Composable
fun MyShareContent() {
    val headline = MaterialTheme.typography.headlineLarge
    val title = MaterialTheme.typography.titleLarge

    Box(
        modifier = Modifier
            .size(400.dp)
            .background(color = MaterialTheme.colorScheme.surface)
    ) {
        Column(
            verticalArrangement = Arrangement.SpaceBetween,
            horizontalAlignment = Alignment.CenterHorizontally,
            modifier = Modifier
                .padding(12.dp)
                .fillMaxSize(),
        ) {
            Icon(
                painter = painterResource(id = R.drawable.v3_logo_with_text),
                tint = Color.Unspecified, // 원본 SVG 색상 유지
                contentDescription = null,
                modifier = Modifier.height(200.dp)
            )
            Text(text = "Moon Bay Marine Reserve", style = headline)

            Row(
                horizontalArrangement = Arrangement.SpaceBetween,
                modifier = Modifier.fillMaxWidth()
            ) {
                Text(text = "Date", style = title)
                Text(text = "Thursday 10 April 2024", style = title)
            }

            Row(
                horizontalArrangement = Arrangement.SpaceBetween,
                modifier = Modifier.fillMaxWidth()
            ) {
                Text(text = "Time", style = title)
                Text(text = "10:20 - 11:45", style = title)
            }
        }
    }
}
```

![Image](/assets/img/2024-05-23-SomethingWorthSharingHowtouseComposablesonSocialMedia_1.png)

# 그래픽 레이어에 Composable 기록하기

Compose의 최신 버전(1.7)에서는 훌륭한 GraphicsLayer API가 소개되었습니다. 이는 Composable의 그리기를 캡처하고 다른 위치에서 재생하는 방법을 제공합니다. 결국 이를 사용하여 Composable을 이미지 파일로 기록할 것입니다.


<div class="content-ad"></div>

```kotlin
var graphicsLayer = rememberGraphicsLayer()

Box(modifier = Modifier
    .size(400.dp)
    .drawWithCache {
        // draw to graphics layer
        graphicsLayer = obtainGraphicsLayer().apply {
            record { drawContent() }
        }
        // draw to actual UI
        onDrawWithContent { drawContent() }
    }) {
    // The content being recorded
    Surface(modifier = Modifier.fillMaxSize()) {
        MyShareContent()
    }
}
```

이 코드 스니펫의 주요 기능:

- Box를 부모 컨테이너로 사용
- 공유하려는 컴포저블을 Box의 콘텐츠 매개변수에 배치
- drawWithCache 수정자를 사용하여 콘텐츠의 그리기를 캡처
- record 메서드를 사용하여 그림을 저장된 graphicsLayer 변수로 리디렉션

# 컴포저블 그리기를 완전히 건너뛰기
  

<div class="content-ad"></div>

컴포저블을 화면에서 숨기고 전혀 표시하지 않을 수도 있습니다.

Viz 앱에서 사용자가 게시물을 저장한 후에는 전체 상세 버전을 보여줍니다. 그러나 소셜 미디어에 공유하기 위한 간단한 요약 버전을 제공합니다. 이 경우 화면에 전체 버전을 표시하고 공유 가능한 버전은 오프스크린에서 생성합니다.

이를 달성하려면 코드에 몇 가지 변경 사항이 필요합니다 —

```js
var graphicsLayer = rememberGraphicsLayer()

Box(modifier = Modifier
    .size(0.dp) // UI에 공간을 사용하지 않도록 크기를 0으로
    .drawWithCache {
        // 그래픽 레이어에 그리기
        graphicsLayer = obtainGraphicsLayer().apply {
            record(
                size = IntSize(
                    width = 400.dp.toPx().toInt(),
                    height = 400.dp.toPx().toInt()
                )
            ) {
                drawContent()
            }
        }

        // 화면에 그리기를 건너뛰기 위해 비워 두기
        onDrawWithContent { }
    }) {
    Box(
        // 녹화의 원하는 크기로 부모 크기를 재정의
        modifier = Modifier
            .wrapContentHeight(unbounded = true, align = Alignment.Top)
            .wrapContentWidth(unbounded = true, align = Alignment.Start)
            .requiredSize(400.dp)
    ) {
        // 녹화되는 내용
        Surface(modifier = Modifier.fillMaxSize()) {
            MyShareContent()
        }
    }
}
```

<div class="content-ad"></div>

이 코드 스니펫의 주요 기능은 다음과 같습니다:

- 부모 Box 콤포저블은 크기를 0.dp로 설정합니다.
- onDrawWithContent ' '은 비워두었는데요 — 이것은 화면에 그리는 것을 건너뛸 때 사용됩니다.
- 자식 콤포저블에는 wrapContentHeight 및 wrapContentWidth 수정자를 사용하여 unbound = true로 설정합니다.
- 자식 콤포저블에는 desired size를 지정하기 위해 requiredSize 수정자를 사용하여 크기를 설정합니다(400.dp).

# 그래픽 레이어를 이미지 파일에 작성하세요

Android 플랫폼에 파일을 작성할 때, 성능, 권한, API의 가용성 등 여러 가지 고려할 사항이 있습니다.

<div class="content-ad"></div>

간결함과 집중을 위해 여기에 기본적인 해결책을 제시할게요. 안드로이드 14에서 잘 작동하는데요, 미디어 저장소 API로의 이동이라는 소문에도 불구하고요. 파일을 공유 Pictures 디렉토리에 쓰고, 미디어 스캐너를 사용하여 공유 가능한 URI를 생성함으로써 대부분의 권한 고려 사항을 회피할 수 있어요.

파일 작성에 대해 미묘하게 다루고 싶다면, Wan Xiao의 다음 글을 추천드려요 -

지금은 다음과 같은 과정을 따를 거에요:

- 그래픽 레이어를 비트맵으로 변환
- 비트맵을 PNG로 압축
- PNG를 Pictures 디렉토리에 파일로 쓰기
- 새 이미지 파일의 URI 가져오기

<div class="content-ad"></div>

```kotlin
private suspend fun GraphicsLayer.saveAsShareableFile(context: Context): Uri? {
    
    // 비트맵으로 변환
    val bitmap = this.toImageBitmap().asAndroidBitmap()

    // 파일 생성
    val file = File(context.getExternalFilesDir(Environment.DIRECTORY_PICTURES),
        "my-app-post-${System.currentTimeMillis()}.png")

    // PNG로 비트맵을 파일에 쓰기
    file.outputStream().use { out ->
        bitmap.compress(Bitmap.CompressFormat.PNG, 100, out)
        out.flush()
    }

    // 공유를 위한 파일 URI 가져오기
    return file.scanPath(context)
}

suspend fun File.scanPath(context: Context): Uri? {
    // 코루틴으로 자바 콜백 래핑
    return withTimeoutOrNull(timeMillis = 5000) {
        suspendCancellableCoroutine { continuation ->
            MediaScannerConnection.scanFile(
                context,
                arrayOf(path),
                arrayOf("image/png")
            ) { scannedPath, scannedUri ->
                continuation.resume(scannedUri)
            }
        }
    }
}
```

사회적 공유 이미지를 위해 PNG를 사용하는 것을 추천합니다. 텍스트가 포함된 이미지일 경우 선명도를 보존하는 것이 중요합니다. 각 소셜 미디어 플랫폼은 이미지를 원하는 크기와 형식으로 압축하기 때문에 이미지의 품질을 최대로 유지해야 합니다.

# 버튼으로 프로세스 시작

이미지 URI를 얻은 후에는 공유 인텐트를 생성하여 어디든 전송할 수 있습니다. 이렇게 하면 Android 공유 시트가 열리고 나머지 프로세스를 자동으로 처리합니다 — 추가적인 코드는 필요하지 않습니다!


<div class="content-ad"></div>

```kotlin
val context = LocalContext.current
val coroutineScope = rememberCoroutineScope()
var graphicsLayer = rememberGraphicsLayer()

Button(onClick = {
    coroutineScope.launch {
        if (graphicsLayer.size.width > 0 && graphicsLayer.size.height > 0) {
            val uri = graphicsLayer.saveAsShareableFile(context)
            val shareIntent: Intent = Intent().apply {
                action = Intent.ACTION_SEND
                putExtra(Intent.EXTRA_STREAM, uri)
                type = "image/png"
            }
            startActivity(
                context, Intent.createChooser(shareIntent, "share"), null
            )
        }
    }

}) {
    // imo - nicer than IconButton with text
    Row(horizontalArrangement = Arrangement.spacedBy(ButtonDefaults.IconSpacing)) {
        Icon(
            Icons.Default.Share,
            contentDescription = null,
            modifier = Modifier.size(18.dp)
        )
        Text(
            text = "Share", style = MaterialTheme.typography.labelLarge
        )
    }
}

// Shareable 컴포저블을 여기에 포함하세요...
```

이 코드 스니펫에서 중요한 기능:

- 부모 컴포저블에 범위 지정된 코루틴 시작
- 그래픽 레이어가 0보다 큰지 확인하여 실행 보호
- 이전에 생성한 헬퍼 확장 기능을 사용하여 그래픽 레이어 저장
- 공유 Intent 생성 및 시작

이러한 기능은 ViewModel에서 구현하는 것이 좋을까요?

<div class="content-ad"></div>

전체 과정은 다른 상태와 상호 작용할 필요 없이 구성 가능한 내부에 포함될 수 있습니다.

또한 컴포저블 내에 Context를 유지하고 관리하는 것이 더 쉽습니다. 이러한 이유로, 버튼의 onClick 매개변수에서 코루틴을 직접 시작하는 것을 권장합니다.

# 전체 코드 — 모든 것을 함께 넣기

그래 — 이제 복사/붙여넣기 시간입니다!

<div class="content-ad"></div>

```kt
// 모든 명확하지 않은 임포트
// 더 짧게 하기 위해 구성 임포트는 제외
import android.content.Context
import android.content.Intent
import android.graphics.Bitmap
import android.media.MediaScannerConnection
import android.net.Uri
import android.os.Environment
import androidx.core.content.ContextCompat.startActivity
import kotlinx.coroutines.launch
import kotlinx.coroutines.suspendCancellableCoroutine
import kotlinx.coroutines.withTimeoutOrNull
import java.io.File
import kotlin.coroutines.resume

@Composable
fun MyShareScreen() {
    val context = LocalContext.current
    val coroutineScope = rememberCoroutineScope()
    var graphicsLayer = rememberGraphicsLayer()

    Column {
        Button(onClick = {
            coroutineScope.launch {
                if (graphicsLayer.size.width > 0 && graphicsLayer.size.height > 0) {
                    val uri = graphicsLayer.saveAsShareableFile(context)
                    val shareIntent: Intent = Intent().apply {
                        action = Intent.ACTION_SEND
                        putExtra(Intent.EXTRA_STREAM, uri)
                        type = "image/png"
                    }
                    startActivity(
                        context, Intent.createChooser(shareIntent, "share"), null
                    )
                }
            }

        }) {
            // IMO - nicer than IconButton with text
            Row(horizontalArrangement = Arrangement.spacedBy(ButtonDefaults.IconSpacing)) {
                Icon(
                    Icons.Default.Share,
                    contentDescription = null,
                    modifier = Modifier.size(18.dp)
                )
                Text(
                    text = "Share", style = MaterialTheme.typography.labelLarge
                )
            }
        }

        Box(modifier = Modifier
            .size(0.dp) // size 0 so that no space is used in the UI
            .drawWithCache {
                // draw to graphics layer
                graphicsLayer = obtainGraphicsLayer().apply {
                    record(
                        size = IntSize(
                            width = 400.dp.toPx().toInt(),
                            height = 400.dp.toPx().toInt()
                        )
                    ) {
                        drawContent()
                    }
                }

                // leave blank to skip drawing on the screen
                onDrawWithContent { }
            }) {
            Box(
                // override the parent size with desired size of the recording
                modifier = Modifier
                    .wrapContentHeight(unbounded = true, align = Alignment.Top)
                    .wrapContentWidth(unbounded = true, align = Alignment.Start)
                    .requiredSize(400.dp)
            ) {
                // The content being recorded
                Surface(modifier = Modifier.fillMaxSize()) {
                    Text("My Share Content Here")
                }
            }
        }
    }
}

suspend fun GraphicsLayer.saveAsShareableFile(context: Context): Uri? {

    // convert to bitmap
    val bitmap = this.toImageBitmap().asAndroidBitmap()

    // create file
    val file = File(context.getExternalFilesDir(Environment.DIRECTORY_PICTURES),
        "my-app-post-${System.currentTimeMillis()}.png")

    // write bitmap to file as PNG
    file.outputStream().use { out ->
        bitmap.compress(Bitmap.CompressFormat.PNG, 100, out)
        out.flush()
    }

    // get file URI for sharing
    return file.scanPath(context)
}

suspend fun File.scanPath(context: Context): Uri? {
    // wrap java callback in coroutine
    return withTimeoutOrNull(timeMillis = 5000) {
        suspendCancellableCoroutine { continuation ->
            MediaScannerConnection.scanFile(
                context,
                arrayOf(path),
                arrayOf("image/png")
            ) { scannedPath, scannedUri ->
                continuation.resume(scannedUri)
            }
        }
    }
}
```

![이미지](https://miro.medium.com/v2/resize:fit:1200/1*wX0_iwYqjzYVOm9Qu4pv1A.gif)

# 정리합니다

저는 Viz 앱을 개발하고 운영하는 독립 개발자입니다. 이 앱은 Kotlin Multiplatform을 활용한 네이티브 iOS 및 Android 앱입니다. 호주에서 다이빙, 스노클링 또는 프리다이빙을 즐기는 분들에게 꼭 한번 확인해보세요! 이 앱은 사람들이 서로 물 조건을 공유하고 해저 사진을 업로드하며 다이빙 로그를 유지할 수 있는 공간입니다. 항상 무료이며 가입이 필요하지 않습니다.

<div class="content-ad"></div>

잘못된 부분이나 버그를 발견하셨나요? 제안이나 대안이 있으시다면 언제든지 편하게 피드백해주세요! 이 기사에 몇 가지 수정사항을 추가하는 것에 항상 열려있습니다!

그렇지 않다면, 여러분들이 이 내용을 유용하게 사용하고 있는지 알기 위해 언제나 의견/좋아요 등을 감사히 받겠습니다 :)