---
title: "Jetpack Compose 안드로이드에서 사용자 지정 PDF 뷰어 만들기"
description: ""
coverImage: "/assets/img/2024-06-19-CreatingCustomPDFViewerinJetpackComposeAndroid_0.png"
date: 2024-06-19 13:43
ogImage: 
  url: /assets/img/2024-06-19-CreatingCustomPDFViewerinJetpackComposeAndroid_0.png
tag: Tech
originalTitle: "Creating Custom PDFViewer in Jetpack Compose Android"
link: "https://medium.com/proandroiddev/creating-custom-pdfviewer-in-jetpack-compose-android-0e962aa22b9f"
---



![PDFViewer](/assets/img/2024-06-19-CreatingCustomPDFViewerinJetpackComposeAndroid_0.png)

PDF는 우리가 매일 사용하는 가장 일반적인 파일 형식 중 하나입니다. 그러나 Jetpack Compose에서 공식 PDF 뷰어가 아직 없습니다. 그렇다면 왜 만들지 않을까요?

## 어떻게 가능한가요?

우리의 계획 개요를 살펴봅시다:


<div class="content-ad"></div>

- PDF 파일이 있는 원격 URL 또는 전화 저장소가 있습니다.
- 하나씩 페이지를 보여줄 수 있습니다.
- 페이지는 확대 및 이동할 수 있어야 합니다.
- 서버 PDF를 로컬 캐시/저장소에 다운로드하고 저장해야 합니다.
- PDF를 '페이지'를 나타내는 '비트맵' 목록으로 변환할 수 있습니다. 
- 그런 다음 간단히, 비트맵을 지원하는 이미지 컴포저블을 사용하여 세로로 페이지를 하나씩 모두 표시할 것입니다.

## 단계 1: PDF 다운로드 및 저장

이 시점에서 PDF의 URL만 있습니다. 이를 다운로드하는 기능을 만들어 보겠습니다.

먼저 AndroidManifest.xml에 다음 권한을 추가하세요.

<div class="content-ad"></div>

```java
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

우리는 HttpURLConnection 객체를 사용하여 연결을 설정하고 InputStream을 가져올 것입니다.

```java
val connection = URL(url).openConnection() as HttpURLConnection
connection.connect()
```

위의 코드들이 작동하여 PDF 파일을 가져올 것입니다. 작업이 완료되었고 필요한 Input stream을 얻었는지 확인해보세요.

<div class="content-ad"></div>

```kotlin
if (connection.responseCode != HttpURLConnection.HTTP_OK) {
    connection.disconnect()
    return@withContext null
}

val inputStream = connection.inputStream
```

작업이 끝나면 꼭 연결을 끊어 주세요.

```kotlin
connection.disconnect()
```

이제 다운로드 부분이 완료되었으니 사용자의 로컬 스토리지에 저장해주세요.

<div class="content-ad"></div>

```kotlin
file = File.createTempFile(fileName, ".pdf")
val outputStream = FileOutputStream(file)
inputStream.copyTo(outputStream)
outputStream.close()
```

변경 후 전체 함수는 다음과 같습니다: 

```kotlin
suspend fun downloadAndGetFile(url: String, fileName: String): File? {
    if (isFileExist(fileName)) return File(fileName) // 이 줄은 중복 파일 생성을 피하기 위해 중요합니다.
    var connection: HttpURLConnection? = null
    var file: File? = null
    try {
        withContext(Dispatchers.IO) {
            connection = URL(url).openConnection() as HttpURLConnection
            connection!!.connect()

            if (connection!!.responseCode != HttpURLConnection.HTTP_OK) {
                return@withContext null
            }

            val inputStream = connection!!.inputStream
            file = File.createTempFile(fileName, ".pdf")
            val outputStream = FileOutputStream(file)
            inputStream.copyTo(outputStream)
            outputStream.close()
        }
    } catch (e: IOException) {
        // UI에 응답을 전송합니다.
    } finally {
        connection?.disconnect()
    }
    return file
}
```

```kotlin
fun isFileExist(path: String): Boolean {
    val file = File(path)
    return file.exists()
}
```

<div class="content-ad"></div>

## 단계 2: 파일 객체를 List`Bitmap`으로 변환합니다.

이 변환에는 PdfRenderer 클래스를 사용할 것입니다:

```js
PdfRenderer renderer = new PdfRenderer(ParcelFileDescriptor.open(file, ParcelFileDescriptor.MODE_READ_ONLY));
```

하지만 이것을 직접 사용하는 것은 Jetpack Compose에서 좋지 않고 많은 RAM을 소비할 수 있습니다.

<div class="content-ad"></div>

그럼 다음과 같이 사용할 것입니다:

```js
val rendererScope = rememberCoroutineScope()
val mutex = remember { Mutex() }
val renderer by produceState<PdfRenderer?>(null, file) {
    rendererScope.launch(Dispatchers.IO) {
        val input = ParcelFileDescriptor.open(file, ParcelFileDescriptor.MODE_READ_ONLY)
        value = PdfRenderer(input)
    }
    awaitDispose {
        val currentRenderer = value
        rendererScope.launch(Dispatchers.IO) {
            mutex.withLock {
                currentRenderer?.close()
            }
        }
    }
}
```

이제 우리가 만든 "PDFRenderer" 객체인 "renderer"를 사용하여 모든 페이지를 가져와 비트맵 객체에 렌더링할 것입니다.

```js
renderer?.let {
    it.openPage(index).use { page ->
        page.render(destinationBitmap, null, null, PdfRenderer.Page.RENDER_MODE_FOR_DISPLAY)
    }
}
```

<div class="content-ad"></div>

이제 모든 페이지에 대한 비트맵을 가져올 수 있습니다. UI에서 모든 페이지에 표시할 시간입니다.

## 단계 3: UI에 목록`비트맵` 표시 + 줌 및 이동 기능 추가:

PDFViewer Composable의 전체 코드가 필요하면 이를 참조하십시오:

여기서 설명이 시작됩니다:

<div class="content-ad"></div>

- BoxWithConstraints를 사용하는 이유는 페이지의 높이와 너비를 정의하고 줌 및 이동을 위해 화면의 높이와 너비가 필요하기 때문입니다.

```js
            val width = with(LocalDensity.current) { maxWidth.toPx() }.toInt()
            val height = (width * sqrt(2f)).toInt()
            val pageCount by remember(renderer) { derivedStateOf { renderer?.pageCount ?: 0 } }//Used ahead

            var scale by rememberSaveable {
                mutableFloatStateOf(1f)
            }
            var offset by remember {
                mutableStateOf(Offset.Zero)
            }
            val state = //Used for Zoom and Move
                rememberTransformableState { zoomChange, panChange, rotationChange ->
                    scale = (scale * zoomChange).coerceIn(1f, 5f)

                    val extraWidth = (scale - 1) * constraints.maxWidth
                    val extraHeight = (scale - 1) * constraints.maxHeight

                    val maxX = extraWidth / 2
                    val maxY = extraHeight / 2

                    offset = Offset(
                        x = (offset.x + scale * panChange.x).coerceIn(-maxX, maxX),
                        y = (offset.y + scale * panChange.y).coerceIn(-maxY, maxY),
                    )
                }
```

Zoom 구현을 자세히 이해하려면 이 비디오를 시청하세요.

2. 이 상태를 LazyColumn과 함께 간단히 사용하고 있습니다:

<div class="content-ad"></div>

```kotlin
            LazyColumn(
                modifier = Modifier
                    .fillMaxSize()
                    .graphicsLayer {
                        scaleX = scale
                        scaleY = scale
                        translationX = offset.x
                        translationX = offset.y
                    }
                    .transformable(state)
```

3. 호출할 때 이미 PDF 파일을 다운로드하고 저장했습니다.

```kotlin
LaunchedEffect(key1 = Unit) {
        file = async { downloadAndGetFile(url, fileName) }.await()
    }
```

4. "cacheKey"로부터 Bitmap 객체를 만들어야 합니다. 이미지도 캐시에 저장될 것입니다.

<div class="content-ad"></div>

```kotlin
val cacheKey = MemoryCache.Key("${file!!.name}-$index")
val cacheValue: Bitmap? = imageLoader.memoryCache?.get(cacheKey)?.bitmap
var bitmap: Bitmap? by remember { mutableStateOf(cacheValue) }
```

5. 이후에는 각 페이지의 비트맵을 가져와 Coil ImageRequest 객체로 표시합니다.

```kotlin
val request = ImageRequest.Builder(context)
    .size(width, height)
    .memoryCacheKey(cacheKey)
    .data(bitmap)
    .build()

Image(
    modifier = Modifier
        .background(Color.Transparent)
        .border(1.dp, MaterialTheme.colors.background)
//        .aspectRatio(1f / sqrt(2f))
        .fillMaxSize(),
    contentScale = ContentScale.Fit,
    painter = rememberAsyncImagePainter(request),
    contentDescription = "Page ${index + 1} of $pageCount"
)
```

나머지 부분은 상당히 직관적이고 이해하기 쉽습니다.


<div class="content-ad"></div>

여기서 새로운 것을 배웠다면, 좋다면 팔로우 버튼을 눌러 주세요.