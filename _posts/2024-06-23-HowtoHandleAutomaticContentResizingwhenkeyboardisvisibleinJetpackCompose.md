---
title: "Jetpack Compose에서 키보드가 보일 때 자동 콘텐츠 크기 조정하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-HowtoHandleAutomaticContentResizingwhenkeyboardisvisibleinJetpackCompose_0.png"
date: 2024-06-23 21:06
ogImage:
  url: /assets/img/2024-06-23-HowtoHandleAutomaticContentResizingwhenkeyboardisvisibleinJetpackCompose_0.png
tag: Tech
originalTitle: "How to Handle Automatic Content Resizing when keyboard is visible in Jetpack Compose"
link: "https://medium.com/@mark.frelih_9464/how-to-handle-automatic-content-resizing-when-keyboard-is-visible-in-jetpack-compose-1c76e0e17c57"
---

안녕하세요! 안드로이드 개발자로서 저희는 앱의 내용이 키보드와 겹치는 문제에 모두 부딪혀본 적이 있을 것입니다. 이는 사용자들에게 귀찮은 일이 될 수 있습니다.

아래는 해당 내용에 대한 이미지입니다.

[![이미지](/assets/img/2024-06-23-HowtoHandleAutomaticContentResizingwhenkeyboardisvisibleinJetpackCompose_0.png)](https://miro.medium.com/v2/resize:fit:856/1*5uRM2cdOb4ROhfT5N3NpLw.gif)

본 문서에서는 Jetpack Compose에서 키보드가 나타날 때 내용을 자동으로 조절하는 방법에 대해 알아보겠습니다. 시작하기 전에, 이게 무엇인지 살펴보겠습니다.

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

과거에는 AndroidManifest.xml 파일에 다음 라인을 추가하여 이 문제를 해결했었습니다:

```js
android: windowSoftInputMode = "adjustResize";
```

그러나 Compose만 사용하는 앱에서는 이 방법이 작동하지 않기 때문에 대안적인 해결책을 찾아야 합니다.

키보드가 열릴 때 자동으로 패딩을 추가하는 .imePadding 수정자를 사용하는 재사용 가능한 컴포넌트를 만들었습니다:

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

이 간단한 컴포넌트는 콘텐츠를 수용하고 키보드가 열릴 때 자동으로 크기를 조절합니다. 이 컴포넌트의 사용법은 간단하며 아래에서 확인할 수 있습니다:

```js
 KeyboardAware {
    SearchSongScreen(...)
 }
```

결과는 다음과 같습니다:

![이미지](https://miro.medium.com/v2/resize:fit:856/1*Xp9vSTgY1d2eFegiv_9t1Q.gif)

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

그러나 우리는 곧 하나의 화면에 여러 개의 TextField가 필요하다는 것을 깨달았는데, 그렇게 되면 내용이 다시 겹칠 것임을 깨달을 수 있었습니다.

![이미지](https://miro.medium.com/v2/resize:fit:856/1*PXj9YJxEyse91vYwgfcTNQ.gif)

이 경우 내용을 스크롤할 수 있게 해야 합니다. 이를 위해 몇 가지 더 필요합니다:

화면에 전달될 scrollState를 추가해야 합니다. 이를 .verticalScroll 수정자에 전달할 것입니다.

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

우리는 rememberCoroutineScope로 coroutineScope를 생성해야 합니다. 이는 코루틴을 컴포저블 외부에서 시작하는 데 사용할 수 있는 composition-aware scope를 생성합니다.

마지막으로 물론 키보드의 높이를 얻어야 합니다. 이는 WindowInsets.ime.getBottom(LocalDensity.current)로 수행할 수 있습니다.

모든 것을 연결하고 작동하는 예제를 얻기 위해 keyboardHeight를 키로 사용하여 LaunchedEffect를 사용합니다. 이는 우리가 LaunchedEffect에 전달하는 블록이 키보드의 높이가 변경될 때마다 (다시) 시작될 것을 의미합니다. 우리가 필요한 것과 정확히 일치합니다!

마지막으로 scrollState.scrollBy(keyboardHeight.toFloat())를 호출하여 내용을 키보드의 높이만큼 스크롤합니다. 이때 이전에 생성한 coroutineScope를 사용해야 합니다. scrollBy가 중단 함수이기 때문입니다.

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

최종 솔루션이 이렇게 생겼습니다:

![image](https://miro.medium.com/v2/resize:fit:856/1*E2MdREPidgMZFSoTn_pHpg.gif)

그런데요! 🥳 이제 거의 모든 화면에서 작동하는 최종 솔루션이 완성되었습니다. 이 간단한 튜토리얼이 유용하길 바라요. 👨‍💻

전체 코드는 여기에서 확인할 수 있어요: https://github.com/Kuglll/KeyboarAwareSample/tree/main

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

제 Github 또는 LinkedIn을 통해 언제든지 연락하셔도 괜찮아요.
