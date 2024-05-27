---
title: "제트팩 컴포즈에서 스낵바를 어떻게 보여줄 수 있을까요"
description: ""
coverImage: "/assets/img/2024-05-27-HowtoshowSnackbarinJetpackCompose_0.png"
date: 2024-05-27 16:36
ogImage: 
  url: /assets/img/2024-05-27-HowtoshowSnackbarinJetpackCompose_0.png
tag: Tech
originalTitle: "How to show Snackbar in Jetpack Compose?"
link: "https://medium.com/@jurajkunier/how-to-show-snackbar-in-jetpack-compose-3f2d81891f87"
---


안녕하세요! 아래는 Markdown 형식으로 변경된 테이블 태그입니다.

```markdown
![Jetpack Compose Snackbar](/assets/img/2024-05-27-HowtoshowSnackbarinJetpackCompose_0.png)

저는 Snackbars에 대해 쓸 예정이며, Jetpack Compose에서 가장 쉬운 방법을 소개해 드릴 예정입니다.

따라서, 이어서 용어를 명확히 정리하면서 시작해 보겠습니다.

# Snackbars란 무엇인가요?
```

<div class="content-ad"></div>

스낵바는 앱 내부에서 어떤 것에 대한 경량화된 피드백을 제공하는 UI 구성 요소입니다. 기본적으로는 앱이 수행했거나 수행할 작업에 대해 사용자에게 간단히 알려주는 메시지입니다.

![스낵바 이미지](/assets/img/2024-05-27-HowtoshowSnackbarinJetpackCompose_1.png)

이들은 임시로 표시되며 시간이 지나거나 사용자 상호 작용 후에 자동으로 사라집니다. 스낵바에는 텍스트 버튼을 통해 액세스할 수 있는 단일 작업이 포함될 수 있습니다. 일반적으로 "다시 시도" 또는 "실행 취소"와 같은 내용입니다. 그리고 사용자 경험을 중단시키지 않아야 합니다.

# 구현

<div class="content-ad"></div>

젯팩 콤포즈에서 스낵바가 구현되는 방법을 살펴봅시다. 기본적으로, 주요 구성 요소는 3가지입니다.

- 스낵바 코포저블은 보여주거나 숨기는 옵션이나 애니메이션 없이 머터리얼 디자인 가이드라인에 정의된 스낵바의 시각적 표현에 불과합니다.
- 스낵바 호스트는 스낵바의 표시 및 숨김, 그리고 애니메이션을 담당하는 구성 요소입니다. 이는 이전에 언급한 스낵바를 감싸는 UI 래퍼입니다.
- 스낵바 호스트 상태는 스낵바 호스트 내에 표시되는 현재 스낵바 및 나중에 표시할 스낵바 대기열을 제어합니다. 한 번에 최대 하나의 스낵바만 표시할 수 있도록 보장합니다. 
showSnackbar()라는 중단 메서드가 있어 새로운 스낵바를 표시할 수 있습니다.

자, 이제 스낵바가 무엇이며 어떻게 구현되는지 명확해졌습니다. 처음에 말한대로 최선의 방법으로 표시하는 방법을 보여드리도록 하겠습니다. 하지만 먼저, 스카폴드를 간단히 소개하겠습니다.

# 스카폴드

<div class="content-ad"></div>

Scaffold는 기본적인 머티리얼 디자인 레이아웃 구조를 구현하는 레이아웃입니다. TopBar, BottomBar, Floating Action Button (FAB) 또는 Drawer와 같은 요소를 추가할 수 있습니다.

![image](/assets/img/2024-05-27-HowtoshowSnackbarinJetpackCompose_2.png)

Scaffold는 모든 것이 머티리얼 디자인 가이드에 따라 올바른 위치에 함께 표시되도록 합니다.

위에서 보듯이, Scaffold composable에는 SnackbarHostState와 Snackbar Host와 같은 다양한 매개변수가 포함되어 있습니다. 이러한 지식을 바탕으로 코드를 작성할 준비가 되었습니다.

<div class="content-ad"></div>

# 코드

Scaffold와 버튼이 있는 Composable을 사용하여 시작해봅시다. 사용자가 버튼을 누르면 스낵바가 표시되어야 합니다.

```js
@Composable
fun SnackbarDemo() {
    Scaffold() {
        Button(onClick = {})
        {
            Text(text = "Click me!")
        }
    }
}
```

우리는 "remember" 메서드 내에 기본 ScaffoldState를 생성하여 재구성 후에도 동일한 상태를 사용할 수 있도록합니다. 앞서 언급했듯이, ScaffoldState에는 새로운 스낵바를 표시하는 데 사용할 수있는 SnackbarHostState가 포함되어 있습니다. 메시지 또는 작업 레이블과 같은 필요한 매개변수를 제공해야합니다. 그러나 snowSnackbar은 일시 중단된 함수이므로 직접 호출할 수는 없습니다.

<div class="content-ad"></div>

```kotlin
@Composable
fun SnackbarDemo() {
    val scaffoldState: ScaffoldState = rememberScaffoldState()
    val coroutineScope: CoroutineScope = rememberCoroutineScope()

    Scaffold(scaffoldState = scaffoldState) {
        Button(onClick = {
            coroutineScope.launch {
                scaffoldState.snackbarHostState.showSnackbar(
                    message = "This is your message",
                    actionLabel = "Do something"
                )
            }
        }) {
            Text(text = "Click me!")
        }
    }
}
```

버튼을 누를 때마다 스낵바가 화면에 나타날 것입니다. 그러나 스낵바가 사라졌는지, 사용자가 스낵바의 액션을 클릭했는지 알 수 없습니다.

이 문제를 해결하기 위해 현재 스레드를 차단하지 않고 새로운 코루틴을 시작하는 코루틴 범위를 사용합니다. 이제 버튼을 누르면 스낵바가 화면에 나타나야 합니다.

표시된 스낵바가 해제되었는지, 사용자가 스낵바의 액션을 클릭했는지 확인하려면 중단된 함수에 의해 반환된 SnackbarResult를 확인하면 됩니다. SnackbarResult는 Dimissined 또는 ActionPerformed 값을 갖는 enum이며 업무 로직을 이에 따라 구현할 수 있습니다.
```

<div class="content-ad"></div>

```kotlin
@Composable
fun SnackbarDemo() {
    val scaffoldState: ScaffoldState = rememberScaffoldState()
    val coroutineScope: CoroutineScope = rememberCoroutineScope()

    Scaffold(scaffoldState = scaffoldState) {
        Button(onClick = {
            coroutineScope.launch {
                val snackbarResult = scaffoldState.snackbarHostState.showSnackbar(
                    message = "This is your message",
                    actionLabel = "Do something"
                )
                when (snackbarResult) {
                    SnackbarResult.Dismissed -> TODO()
                    SnackbarResult.ActionPerformed -> TODO()
                }
            }
        }) {
            Text(text = "Click me!")
        }
    }
}
```

구현 내용을 확인하려면 YouTube 비디오를 참조하십시오. 전체 튜토리얼과 최종 결과를 녹화한 영상이 있습니다.

이 기사가 마음에 드시면 좋아요를 눌러 주시고 피드백을 남기시고 친구들과 공유해 주세요. 즐거운 코딩되세요!
```