---
title: "Jetpack Compose에서 사용자 정의 3D 대화 상자 애니메이션 만들기"
description: ""
coverImage: "/assets/img/2024-06-19-CraftingCustom3DDialogAnimationinJetpackCompose_0.png"
date: 2024-06-19 22:26
ogImage:
  url: /assets/img/2024-06-19-CraftingCustom3DDialogAnimationinJetpackCompose_0.png
tag: Tech
originalTitle: "Crafting Custom 3D Dialog Animation in Jetpack Compose."
link: "https://medium.com/@kappdev/crafting-custom-3d-dialog-animation-in-jetpack-compose-b4038f7888d5"
---

환영합니다 👋

젯팩 컴포즈의 기본 대화 상자 모양 애니메이션에 지루한가요? 그럼 다행히도 올바른 곳에 오신 것을 환영합니다.

이 기사에서는 사용자를 기쁘게 할 멋진 3D 애니메이션을 젯팩 컴포즈의 대화 상자에 5분 안에 만들어 보겠습니다.

다행히도, 젯팩 컴포즈를 사용하면 이를 쉽게 할 수 있습니다 🤗

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

![이미지](/assets/img/2024-06-19-CraftingCustom3DDialogAnimationinJetpackCompose_0.png)

# AnimatedDialog 함수

다이얼로그를 재사용 가능하고 어떤 시나리오에도 적용할 수 있게 하기 위해, 애니메이션을 포함하고 다이얼로그의 내용과 속성을 지정할 수 있는 함수를 만들어 봅시다.

```js
@Composable
fun AnimatedDialog(
    onDismiss: () -> Unit,
    inAnimDuration: Int = 720,
    outAnimDuration: Int = 450,
    properties: DialogProperties = DialogProperties(),
    content: @Composable (triggerDismiss: () -> Unit) -> Unit,
)
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

이 함수는 여러 개의 매개변수를 사용합니다. 함께 살펴보겠습니다:

✨ onDismiss ➜ 대화 상자가 닫힐 때 트리거되는 콜백 함수입니다.

✨ inAnimDuration ➜ 대화 상자의 표시 애니메이션 지속 시간입니다.

✨ outAnimDuration ➜ 대화 상자의 사라짐 애니메이션 지속 시간입니다.

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

✨ properties ➜ 대화 상자의 설정 속성입니다.

✨ content ➜ 대화 상자 내용을 정의하는 composable 람다입니다. 이 람다는 대화 상자를 종료 애니메이션과 함께 프로그래밍 방식으로 닫을 수 있는 함수 (triggerDismiss)를 받습니다.

# 구현

자, 이제 애니메이션 구현으로 넘어갈까요?

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

## 변수 정의

먼저, 몇 가지 변수를 정의해야 합니다:

```js
// 종료 애니메이션 처리를 위한 코루틴 범위
val scope = rememberCoroutineScope()
// 애니메이션을 관리하는 상태
var isDialogVisible by remember { mutableStateOf(false) }
// 다양한 애니메이션에서 사용될 공통 애니메이션 스펙
val animationSpec = tween<Float>(
    if (isDialogVisible) inAnimDuration else outAnimDuration
)
```

## 애니메이션 상태

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

그 다음으로, alpha, rotationX 및 scale에 대한 세 가지 애니메이션 상태를 정의해야합니다:

```js
val dialogAlpha by animateFloatAsState(
    targetValue = if (isDialogVisible) 1f else 0f,
    animationSpec = animationSpec
)

val dialogRotationX by animateFloatAsState(
    targetValue = if (isDialogVisible) 0f else -90f,
    animationSpec = animationSpec
)

val dialogScale by animateFloatAsState(
    targetValue = if (isDialogVisible) 1f else 0f,
    animationSpec = animationSpec
)
```

## 애니메이션 람다로 닫기

또한 다이얼로그를 종료 애니메이션으로 닫을 람다 함수를 정의해야하며, 이것은 컨텐츠에 전달할 것입니다:

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
val dismissWithAnimation: () -> Unit = {
    scope.launch {
        // 종료 애니메이션을 트리거합니다
        isDialogVisible = false
        // 완료될 때까지 기다립니다
        delay(outAnimDuration.toLong())
        // 대화 상자를 닫습니다
        onDismiss()
    }
}
```

## Entry Animation 트리거하기

컴포저블을 실행했을 때 진입 애니메이션을 트리거하려면 LaunchedEffect와 Unit을 키로 사용할 수 있습니다:

```js
LaunchedEffect(Unit) {
    isDialogVisible = true
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

## 대화창 만들기

최종 부분에서는 모든 것을 함께 결합하고이 매혹적인 애니메이션으로 대화창을 만들 준비가 되었습니다:

```js
Dialog(
    onDismissRequest = dismissWithAnimation,
    properties = properties
) {
    Box(
        modifier = Modifier
            // 알파 전환 적용
            .alpha(dialogAlpha)
            // 스케일 전환 적용
            .scale(dialogScale)
            // x축 회전 전환 적용
            .graphicsLayer { rotationX = dialogRotationX },
        content = {
            content(dismissWithAnimation)
        }
    )
}
```

축하합니다🥳! 성공적으로 만들었어요👏. 전체 코드 구현은 GitHub Gist에서 확인하실 수 있습니다🧑‍💻. 이제 어떻게 활용할 수 있는지 알아봅시다.

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

## 광고

외국어를 배우고 새 어휘에서 고민 중이신가요? 그렇다면, 여행을 쉽고 편리하게 만들어줄 이 어휘 학습 앱을 확인해보시는 것을 강력히 추천드립니다!

![이미지](/assets/img/2024-06-19-CraftingCustom3DDialogAnimationinJetpackCompose_1.png)

# 사용법

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

좋아요, 이제 이 기능을 사용하여 일반적인 샘플을 만들어볼게요.

## 변수 선언하기

먼저, 대화 상태를 저장할 변수를 선언해보세요:

```js
var showDialog by remember { mutableStateOf(false) }
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

## 대화 상자를 트리거하는 버튼

이제 우리는 대화 상자를 열기 위한 버튼이 필요합니다:

```js
Button(
    onClick = { showDialog = true }
) {
    Text("대화 상자 열기")
}
```

## 대화 상자 표시

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

마지막으로, showDialog가 true로 설정되면 다이얼로그가 표시됩니다:

```js
if (showDialog) {
    AnimatedDialog(
        onDismiss = { showDialog = false }
    ) { triggerDismiss ->
        Column(
            modifier = Modifier
                .fillMaxWidth()
                .background(
                    color = MaterialTheme.colorScheme.surface,
                    shape = RoundedCornerShape(24.dp)
                )
                .padding(16.dp)
        ) {
            Text(
                text = "👏 박수 알림 대화 상자",
                color = MaterialTheme.colorScheme.contentColorFor(MaterialTheme.colorScheme.surface),
                style = MaterialTheme.typography.titleLarge
            )

            Spacer(Modifier.height(8.dp))

            Text(
                text = "박수 버튼을 눌러 감사를 표현해 주세요.",
                color = MaterialTheme.colorScheme.contentColorFor(MaterialTheme.colorScheme.surface),
                style = MaterialTheme.typography.bodyLarge
            )

            Spacer(Modifier.height(16.dp))

            Button(
                onClick = triggerDismiss,
                modifier = Modifier.align(Alignment.End)
            ) {
                Text("박수")
            }
        }
    }
}
```

결과를 확인해보세요 😍

![결과 확인](https://miro.medium.com/v2/resize:fit:1200/1*3rjr41FLlYZwKl-SFagv4w.gif)

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

![image](https://miro.medium.com/v2/resize:fit:1200/1*A8bBm5VjKaGTe-jMEoLDUg.gif)

맞춤화할 수 있는 여지가 많지만, 이것이 영감의 출발점이 되기를 바랍니다. 완벽한 설정을 찾는 데 행운을 빕니다! ✨

이것도 마음에 드실지도요 👇

이 기사를 읽어 주셔서 감사합니다! ❤️ 즐겁고 가치 있는 시간이었길 바랍니다. 마음에 드셨다면 박수를 치는 👏 버튼을 눌러 감사를 표현하고, 더 많고 흥미로운 기사를 보시려면 Kappdev를 팔로우해 주세요 😊

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

행복한 코딩!

![이미지](/assets/img/2024-06-19-CraftingCustom3DDialogAnimationinJetpackCompose_2.png)
