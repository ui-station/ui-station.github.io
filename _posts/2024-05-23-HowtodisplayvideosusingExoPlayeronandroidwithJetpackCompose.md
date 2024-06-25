---
title: "안녕하세요 오늘은 안드로이드에서 ExoPlayer를 사용하여 Jetpack Compose로 비디오를 표시하는 방법에 대해 알아보겠습니다"
description: ""
coverImage: "/assets/img/2024-05-23-HowtodisplayvideosusingExoPlayeronandroidwithJetpackCompose_0.png"
date: 2024-05-23 12:55
ogImage:
  url: /assets/img/2024-05-23-HowtodisplayvideosusingExoPlayeronandroidwithJetpackCompose_0.png
tag: Tech
originalTitle: "How to display videos using ExoPlayer on android with Jetpack Compose"
link: "https://medium.com/@munbonecci/how-to-display-videos-using-exoplayer-on-android-with-jetpack-compose-1fb4d57778f4"
---

이 튜토리얼에서는 Jetpack Compose와 함께 ExoPlayer를 사용하는 간단한 방법을 보여드리기로 했어요.

먼저 아래 종속성을 build.gradle(Module: app) 파일에 추가해주세요.

```js
// in .kts
implementation("androidx.media3:media3-exoplayer:1.2.0");
implementation("androidx.media3:media3-ui:1.2.0");
```

현재 컨텍스트를 LocalContext.current로 가져오세요.

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
    // 현재 컨텍스트 가져오기
    val context = LocalContext.current
```

Composable이나 ViewModel에서 ExoPlayer의 인스턴스를 만듭니다.

```kotlin
val exoPlayer = ExoPLayer.Builder(context).build()
```

ExoPlayer의 라이프사이클을 관리하여 필요하지 않을 때 리소스를 해제해야 합니다. DisposableEffect와 LaunchedEffect를 사용하여 라이프사이클 이벤트를 처리할 수 있습니다.

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
// MediaSource를 ExoPlayer에 설정합니다.
LaunchedEffect(mediaSource) {
    exoPlayer.setMediaItem(mediaSource)
    exoPlayer.prepare()
}

// 라이프사이클 이벤트 관리
DisposableEffect(Unit) {
    onDispose {
        exoPlayer.release()
    }
}
```

ExoPlayer 및 해당 컨트롤을 표시하는 Android view입니다.

```js
AndroidView(
    factory = { ctx ->
        PlayerView(ctx).apply {
            player = exoPlayer
        }
    },
    modifier = Modifier
        .fillMaxWidth()
        .height(200.dp) // 원하는 높이로 설정하세요
)
```

상수 EXAMPLE_VIDEO_URI를 만들어 샘플 비디오의 URL을 정의합니다.

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
const val EXAMPLE_VIDEO_URI = "https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4"
```

manifest.file에 인터넷 권한을 추가해주세요.

```js
<uses-permission android:name="android.permission.INTERNET" />
```

이전에 만든 완성된 코드입니다.

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
/**
 * Composable 함수로 ExoPlayer를 사용하여 비디오를 재생하는 기능을 표시합니다.
 *
 * @OptIn 어노테이션인 UnstableApi는 해당 API가 여전히 실험적이며 향후 변경될 수 있다는 것을 나타냅니다.
 *
 * @see EXAMPLE_VIDEO_URI 실제 재생할 비디오의 URI로 대체하세요.
 */
@OptIn(UnstableApi::class)
@Composable
fun ExoPlayerView() {
    // 현재 컨텍스트 가져오기
    val context = LocalContext.current

    // ExoPlayer 초기화
    val exoPlayer = ExoPlayer.Builder(context).build()

    // MediaSource 생성
    val mediaSource = remember(EXAMPLE_VIDEO_URI) {
        MediaItem.fromUri(EXAMPLE_VIDEO_URI)
    }

    // MediaSource를 ExoPlayer에 설정
    LaunchedEffect(mediaSource) {
        exoPlayer.setMediaItem(mediaSource)
        exoPlayer.prepare()
    }

    // 라이프사이클 이벤트 관리
    DisposableEffect(Unit) {
        onDispose {
            exoPlayer.release()
        }
    }

    // AndroidView를 사용하여 Android View(PlayerView)를 Compose에 임베드
    AndroidView(
        factory = { ctx ->
            PlayerView(ctx).apply {
                player = exoPlayer
            }
        },
        modifier = Modifier
            .fillMaxWidth()
            .height(200.dp) // 원하는 높이로 설정
    )
}
```

사용자 지정 UI 컨트롤이 필요하면 play, pause, seek 등을 위한 버튼으로 Composables를 생성하고 exoPlayer를 업데이트하면 됩니다.

이 기능을 테스트하려면 Compose UI에서 ExoPlayeView()를 포함하면 됩니다:

```kotlin
ExoPlayerView()
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

코드를 실행하고 결과를 확인해보세요.

![이미지](/assets/img/2024-05-23-HowtodisplayvideosusingExoPlayeronandroidwithJetpackCompose_0.png)

아래에는 해당 예제의 저장소 URL이 있습니다.

## 그러나, 더 복잡한 설정과 동영상 컨트롤을 숨기는 기능이 포함된 다음 두 가지 화면으로 구성된 샘플 프로젝트가 있습니다. 첫 번째는 동영상 목록이고 두 번째는 선택된 동영상의 상세 정보입니다.

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
/**
 * Jetpack Compose를 사용하여 ExoPlayer를 이용한 비디오 플레이어를 표시하는 조합 가능한 함수입니다.
 *
 * @param video 비디오 재생 대상인 [VideoResultEntity]를 나타내는 매개변수입니다.
 * @param playingIndex 현재 재생 중인 인덱스를 나타내는 State입니다.
 * @param onVideoChange 비디오가 변경될 때 호출되는 콜백 함수입니다.
 * @param isVideoEnded 비디오가 종료되었는지를 결정하는 콜백 함수입니다.
 * @param modifier 스타일링 및 위치 지정을 위한 Modifier입니다.
 *
 * UnstableApi에 대한 OptIn 주석은 API가 여전히 실험적이며 미래에 변경될 수 있음을 나타냅니다.
 *
 * OpaqueUnitKey 사용에 대한 경고를 억제하기 위해 이용되는 SuppressLint 주석입니다.
 *
 * 실험적인 Animation API 사용을 위해 ExperimentalAnimationApi 주석이 적용되었습니다.
 */
@OptIn(UnstableApi::class)
@SuppressLint("OpaqueUnitKey")
@ExperimentalAnimationApi
@Composable
fun VideoPlayer(
    video: VideoResultEntity,
    playingIndex: State<Int>,
    onVideoChange: (Int) -> Unit,
    isVideoEnded: (Boolean) -> Unit,
    modifier: Modifier = Modifier
) {
    // 현재 context 가져오기
    val context = LocalContext.current

    // 비디오 제목의 가시성을 제어하는 Mutable state
    val visible = remember { mutableStateOf(true) }

    // 비디오 제목을 보유하는 Mutable state
    val videoTitle = remember { mutableStateOf(video.name) }

    // ExoPlayer를 위한 MediaItems 목록 생성
    val mediaItems = arrayListOf<MediaItem>()
    mediaItems.add(
        MediaItem.Builder()
            .setUri(video.video)
            .setMediaId(video.id.toString())
            .setTag(video)
            .setMediaMetadata(MediaMetadata.Builder().setDisplayTitle(video.name).build())
            .build()
    )

    // ExoPlayer 초기화
    val exoPlayer = remember {
        ExoPlayer.Builder(context).build().apply {
            this.setMediaItems(mediaItems)
            this.prepare()
            this.addListener(object : Player.Listener {
                override fun onEvents(player: Player, events: Player.Events) {
                    super.onEvents(player, events)
                    // 200밀리초 후에 비디오 제목 숨김
                    if (player.contentPosition >= 200) visible.value = false
                }

                override fun onMediaItemTransition(mediaItem: MediaItem?, reason: Int) {
                    super.onMediaItemTransition(mediaItem, reason)
                    // 비디오 변경 시 콜백
                    onVideoChange(this@apply.currentPeriodIndex)
                    visible.value = true
                    videoTitle.value = mediaItem?.mediaMetadata?.displayTitle.toString()
                }

                override fun onPlaybackStateChanged(playbackState: Int) {
                    super.onPlaybackStateChanged(playbackState)
                    // 비디오 재생 상태가 STATE_ENDED로 변경될 때 콜백
                    if (playbackState == ExoPlayer.STATE_ENDED) {
                        isVideoEnded.invoke(true)
                    }
                }
            })
        }
    }

    // 지정된 인덱스로 이동하고 재생 시작
    exoPlayer.seekTo(playingIndex.value, C.TIME_UNSET)
    exoPlayer.playWhenReady = true

    // 생명주기 이벤트에 기반한 플레이어 상태 관리를 위한 생명주기 관찰자 추가
    LocalLifecycleOwner.current.lifecycle.addObserver(object : LifecycleEventObserver {
        override fun onStateChanged(source: LifecycleOwner, event: Lifecycle.Event) {
            when (event) {
                Lifecycle.Event.ON_START -> {
                    // Composable이 화면에 있을 때 재생 시작
                    if (exoPlayer.isPlaying.not()) {
                        exoPlayer.play()
                    }
                }

                Lifecycle.Event.ON_STOP -> {
                    // Composable이 화면에서 벗어날 때 플레이어 일시정지
                    exoPlayer.pause()
                }

                else -> {
                    // Nothing
                }
            }
        }
    })

    // 비디오 플레이어를 포함하는 Column Composable
    Column(modifier = modifier.background(Color.Black)) {
        // Composable이 소멸될 때 ExoPlayer 해제를 위한 DisposableEffect
        DisposableEffect(
            AndroidView(
                modifier = modifier
                    .testTag(VIDEO_PLAYER_TAG),
                factory = {
                    // Compose에 PlayerView를 포함시키는 AndroidView
                    PlayerView(context).apply {
                        player = exoPlayer
                        layoutParams = FrameLayout.LayoutParams(
                            ViewGroup.LayoutParams.MATCH_PARENT,
                            ViewGroup.LayoutParams.MATCH_PARENT
                        )
                        // 사용 가능한 공간을 채우는 리사이즈 모드 설정
                        resizeMode = AspectRatioFrameLayout.RESIZE_MODE_FILL
                        // 불필요한 플레이어 컨트롤 숨김
                        setShowNextButton(false)
                        setShowPreviousButton(false)
                        setShowFastForwardButton(false)
                        setShowRewindButton(false)
                    }
                })
        ) {
            // Composable이 소멸될 때 ExoPlayer 해제
            onDispose {
                exoPlayer.release()
            }
        }
    }
}
```

## VideoPlayer() 조합 가능한 함수 내용:

MediaItems 및 ExoPlayer 설정:

- 비디오 정보를 담을 MediaItems 목록 생성.
- ExoPlayer를 해당 MediaItems로 구성하고 준비하며, 비디오 변경 및 재생 상태 변경과 같은 이벤트를 처리할 수 있도록 리스너가 추가됨.

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

라이프사이클 관리:

- 리사이클러브 옵서버가 추가되어 Composable 라이프사이클 이벤트를 기반으로 플레이어 상태를 관리합니다. 화면이 포그라운드에 있을 때 플레이어가 재생되고, 백그라운드에 있을 때는 일시정지됩니다.

AndroidView 및 PlayerView 통합:

- AndroidView Composable은 안드로이드 PlayerView를 Jetpack Compose에 삽입하는 데 사용됩니다.
- PlayerView는 ExoPlayer 인스턴스, 레이아웃 매개변수 및 리사이즈 모드와 플레이어 컨트롤의 가시성과 같은 속성과 함께 구성됩니다.

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

휴지통 효과를 위한 정리:

- DisposableEffect는 Composable이 dispose될 때 ExoPlayer 자원을 해제하기 위해 사용됩니다.

총적으로, VideoPlayer Composable은 Jetpack Compose UI 내에서 비디오 재생을 위해 ExoPlayer를 초기화하고 관리하는 로직을 캡슐화합니다.

## 아래 저장소에서 코드를 더 주의 깊게 테스트하고 검토해 주셨으면 좋겠습니다.
