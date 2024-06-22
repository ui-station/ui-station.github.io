---
title: "Jetpack Compose에서 애니메이션 화살표 포인터 만들기"
description: ""
coverImage: "/assets/img/2024-05-27-CreatinganAnimatedArrowPointerinJetpackCompose_0.png"
date: 2024-05-27 16:10
ogImage: 
  url: /assets/img/2024-05-27-CreatinganAnimatedArrowPointerinJetpackCompose_0.png
tag: Tech
originalTitle: "Creating an Animated Arrow Pointer in Jetpack Compose"
link: "https://medium.com/@kappdev/creating-an-animated-arrow-pointer-in-jetpack-compose-83f1ddf432d5"
---


환영합니다 👋

이 기사에서는 Jetpack Compose를 사용하여 멋진 애니메이션 화살표 포인터를 만드는 방법을 탐색하고 앱의 외관을 5분 안에 향상시키는 방법에 대해 알아보겠습니다.

계속 주목하고, 함께 알아보시죠! 🚀

![Animated Arrow Pointer](https://miro.medium.com/v2/resize:fit:1400/1*jP0QAL3blRz6ynJ8IkjCuQ.gif)

<div class="content-ad"></div>

# 함수 정의하기

AnimatedArrowPointer 함수를 선언하고 그 파라미터들을 살펴봅시다.

```js
@Composable
fun AnimatedArrowPointer(
    modifier: Modifier,
    color: Color,
    isVisible: Boolean = true,
    strokeWidth: Dp = 2.dp,
    pointerSize: Dp = 12.dp,
    dashLength: Dp? = 4.dp,
    strokeCap: StrokeCap = StrokeCap.Round,
    pointerShape: Shape = SimpleArrow,
    animationSpec: AnimationSpec<Float> = tween(3000)
)
```

## ⚒️ 파라미터 설명

<div class="content-ad"></div>

⚡ modifier ➜ 포인터 레이아웃에 적용할 수정자입니다.

⚡ color ➜ 화살표 포인터의 색상입니다.

⚡ isVisible ➜ 화살표 포인터가 표시되는지 여부를 결정합니다.

⚡ strokeWidth ➜ 화살표 포인터의 선 두께입니다.

<div class="content-ad"></div>

⚡ pointerSize ➜ 화살표 포인터의 크기입니다.

⚡ dashLength ➜ 테두리 대시의 길이입니다. null은 실선을 의미합니다.

⚡ strokeCap ➜ 화살표 포인터의 선 끝 스타일입니다.

⚡ pointerShape ➜ 화살표 포인터의 모양입니다.

<div class="content-ad"></div>

⚡ animationSpec ➜ 화살표 애니메이션 동작을 지정합니다.

# 경로

좋아요, 오늘 기사의 주요 내용으로 넘어갑시다.

## ArrowPath

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경해보세요.

<div class="content-ad"></div>

## SimplePointer

다음으로 포인터를 제작해야 합니다. Shape 인터페이스를 구현하는 객체를 정의합시다:

```js
object SimpleArrow : Shape {
    override fun createOutline(
        size: Size,
        layoutDirection: LayoutDirection,
        density: Density
    ): Outline {
        val width = size.width
        val height = size.height

        val path = Path().apply {
            moveTo(0f, 0f) // 1
            lineTo(width, height * 0.5f) // 2
            lineTo(0f, height) // 3
            lineTo(width * 0.5f, height * 0.5f) // 4
            close() // line to 1
        }
        return Outline.Generic(path)
    }
}
```

이해를 돕기 위해 다음 이미지를 확인해보세요 👇

<div class="content-ad"></div>


![이미지](/assets/img/2024-05-27-CreatinganAnimatedArrowPointerinJetpackCompose_1.png)

# 그리기

이제 함수 구현에 가까워졌어요. 하지만 그 전에, 경로와 포인터 머리를 그리기 위한 두 개의 지원 함수를 정의해야 해요.

## 화살 경로 그리기


<div class="content-ad"></div>

경로를 그리는 것은 간단합니다. 지정된 속성을 가진 캔버스에 일반적인 경로를 그리기만 하면 됩니다:

```js
fun DrawScope.drawPathSegment(
    path: Path,
    color: Color,
    strokeWidth: Dp,
    strokeCap: StrokeCap,
    dashLength: Dp? = null
) {
    drawPath(
        path = path,
        color = color,
        style = Stroke(
            width = strokeWidth.toPx(),
            cap = strokeCap,
            // dashLength가 지정되어 있다면 대시 스트로크를 그리고,
            // 그렇지 않으면 실선을 사용합니다.
            pathEffect = dashLength?.let { dash ->
                PathEffect.dashPathEffect(
                    floatArrayOf(dash.toPx(), dash.toPx())
                )
            }
        )
    )
}
```

## 포인터 헤드 그리기

현재 경로 세그먼트의 끝에 동적으로 헤드를 그리려면 PathMeasure의 getPosition 및 getTangent를 활용하여 포인터의 위치와 올바른 방향을 가리키는 각도를 얻을 것입니다.

<div class="content-ad"></div>

```js
fun DrawScope.drawPointerHead(
    pathMeasure: PathMeasure,
    stopDistance: Float,
    pointerSize: Dp,
    color: Color,
    pointerShape: Shape
) {
    // 지정된 거리에서의 점과 접선을 계산합니다.
    val headPoint = pathMeasure.getPosition(stopDistance)
    val tangent = pathMeasure.getTangent(stopDistance)

    // 접선을 기반으로 화살표 머리의 회전 각도를 계산합니다.
    val angle = atan2(tangent.y.toDouble(), tangent.x.toDouble()).toFloat() * 180 / Math.PI.toFloat()

    // 화살표 머리의 크기와 윤곽을 정의합니다.
    val headSize = Size(pointerSize.toPx(), pointerSize.toPx())
    val headOutline = pointerShape.createOutline(headSize, layoutDirection, this)

    // 캔버스를 이동하고 회전시켜 화살표 머리의 위치와 방향을 조정합니다.
    translate(headPoint.x - (headSize.width / 2), headPoint.y - (headSize.height / 2)) {
        rotate(angle, pivot = headSize.center) {
            // 화살표 머리 윤곽을 그립니다.
            drawOutline(headOutline, color = color)
        }
    }
}
```  

# 구현

마지막으로 모든 것을 합쳐서 애니메이션을 적용합시다.

```js
@Composable
fun AnimatedArrowPointer(
    /* 매개변수... */
) {
    // 애니메이션 진행 값을 위한 Animatable 정의
    val pathCompletion = remember { Animatable(0f) }

    // 가시성 상태 변경에 기반하여 애니메이션 시작
    LaunchedEffect(isVisible) {
        if (isVisible) {
            // 경로 애니메이션
            pathCompletion.animateTo(1f, animationSpec)
        } else {
            // 경로 즉시 숨기기
            pathCompletion.snapTo(0f)
        }
    }

    // Canvas에서 애니메이션된 화살표 그리기
    Canvas(
        // 경로 왜곡을 방지하기 위한 적절한 비율 보장
        modifier.aspectRatio(0.6f)
    ) {
        // 캔버스 크기에 따라 화살표 경로 생성
        val arrowPath = createArrowPath(size.width, size.height)

        // 길이와 세그먼트 정보를 얻기 위해 경로 측정
        val pathMeasure = PathMeasure().apply {
            setPath(arrowPath, false)
        }

        // 현재 애니메이션 진행에 기반한 경로 세그먼트 생성
        val pathSegment = Path()
        val stopDistance = pathCompletion.value * pathMeasure.length
        pathMeasure.getSegment(0f, stopDistance, pathSegment, true)

        // 지정된 속성으로 현재 화살표 경로 세그먼트 그리기
        drawPathSegment(pathSegment, color, strokeWidth, strokeCap, dashLength)

        // 경로가 일부 그려진 경우, 화살표 머리 그리기
        if (pathCompletion.value > 0) {
            drawPointerHead(pathMeasure, stopDistance, pointerSize, color, pointerShape)
        }
    }
}
```

<div class="content-ad"></div>

축하해요 🥳! 우리 성공적으로 만들었어요 👏. 전체 코드 구현을 확인하려면 GitHub Gist에 액세스할 수 있어요 🧑‍💻. 이제 사용 방법을 알아보아요.

## 광고

외국어를 배우고 새로운 어휘로 고민하고 계신가요? 그렇다면, 여러분의 학습을 쉽고 편리하게 해줄 이 어플을 확인하는 걸 강력히 추천해요!

<img src="/assets/img/2024-05-27-CreatinganAnimatedArrowPointerinJetpackCompose_2.png" />

<div class="content-ad"></div>

# 사용법

배치에 간단한 변형 수정자를 적용하여 화살표 포인터의 위치를 쉽게 사용자 정의할 수 있습니다. 여기에는 일반적인 시나리오가 있습니다:

## 하단-우측 (기본)

하단-우측 위치는 기본 포인팅 방향입니다:

<div class="content-ad"></div>

```kotlin
AnimatedArrowPointer(
    modifier = Modifier.size(140.dp),
    color = Color.Red
)
```

![Arrow Pointer](https://miro.medium.com/v2/resize:fit:576/1*FM6yzByscqrfK0bihhE_6g.gif)

## Top-Right

To point to the top-right, rotate the layout by -90 degrees:

<div class="content-ad"></div>

```kotlin
AnimatedArrowPointer(
    modifier = Modifier
        .size(140.dp)
        .rotate(-90f),
    color = Color.Red
)
```

![Animated Arrow Pointer](https://miro.medium.com/v2/resize:fit:900/1*TDE55uvzhAy-XZNn-d8bGg.gif)

## Bottom-Left

Bottom-Left를 가리키려면 레이아웃을 x축에서 -1f로 확장하여 수평으로 반사합니다:

<div class="content-ad"></div>

```kotlin
AnimatedArrowPointer(
    modifier = Modifier
        .size(140.dp)
        .scale(-1f, 1f),
    color = Color.Red
)
```

![Animated arrow pointer](https://miro.medium.com/v2/resize:fit:570/1*NZ608yVkzKu7IVAwAPa8HQ.gif)

## 왼쪽 위

왼쪽 위 방향을 위해, 먼저 레이아웃을 수평으로 반사한 다음, -90도로 회전시킵니다:

<div class="content-ad"></div>

```kotlin
AnimatedArrowPointer(
    modifier = Modifier
        .size(140.dp)
        .scale(-1f, 1f)
        .rotate(-90f),
    color = Color.Red
)
```

![Animated Arrow Pointer](https://miro.medium.com/v2/resize:fit:900/1*UaIjzfaG0aaaqpSNmN4Qpw.gif)

좋아할 만한 내용이 있을 것 같아요 👇

이 기사를 읽어 주셔서 감사합니다! ❤️ 즐거우시고 가치 있게 보내셨으면 좋겣습니다. 만약 좋았다면 만세 👏를 눌러서 감사를 표현해 주시고 Kappdev를 팔로우하여 더욱 흥미로운 기사를 읽어보세요 😊


<div class="content-ad"></div>

행복한 코딩!

![이미지](/assets/img/2024-05-27-CreatinganAnimatedArrowPointerinJetpackCompose_3.png)