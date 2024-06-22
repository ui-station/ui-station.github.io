---
title: "제트팩 콤포즈에서 멋진 3D 테두리를 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtoCreateaStunning3DBorderinJetpackCompose_0.png"
date: 2024-06-19 10:31
ogImage: 
  url: /assets/img/2024-06-19-HowtoCreateaStunning3DBorderinJetpackCompose_0.png
tag: Tech
originalTitle: "How to Create a Stunning 3D Border in Jetpack Compose"
link: "https://medium.com/@kappdev/how-to-create-a-stunning-3d-border-in-jetpack-compose-e040fbb6b8de"
---


환영합니다! 👋

이 기사에서는 Jetpack Compose용 놀라운 3D 테두리 수정자를 만들어보겠습니다. 이 수정자는 어떤 모양의 뷰에도 적용할 수 있습니다. 게다가, 이 수정자를 사용하여 아름다운 검색 바를 만들어볼 것입니다.

자세히 알아보시죠! 🚀

![image](/assets/img/2024-06-19-HowtoCreateaStunning3DBorderinJetpackCompose_0.png)

<div class="content-ad"></div>

# Convex Border

Modifier를 위한 주요 convexBorder 확장 기능을 정의하는 것부터 시작해봅시다. 이 기능은 최종적으로 윤곽선을 그립니다.

## ConvexStyle

이전에 윤곽 효과의 스타일을 나타내는 ConvexStyle 데이터 클래스를 만들어서 명확성을 높입니다.

<div class="content-ad"></div>

```kotlin
data class ConvexStyle(
    val blur: Dp = 3.dp,
    val offset: Dp = 2.dp,
    val glareColor: Color = Color.White.copy(0.64f),
    val shadowColor: Color = Color.Black.copy(0.64f)
)
```

## 함수

이제 함수를 정의하기 위해 모든 준비가 되었습니다:

```kotlin
fun Modifier.convexBorder(
    color: Color,
    shape: Shape,
    strokeWidth: Dp = 8.dp,
    convexStyle: ConvexStyle = ConvexStyle()
)
```

<div class="content-ad"></div>

⭐ color ➜ 테두리의 색상입니다.

⭐ shape ➜ 테두리의 모양입니다.

⭐ strokeWidth ➜ 테두리 선의 너비입니다.

⭐ convexStyle ➜ 테두리에 적용된 볼록 효과의 스타일입니다.

<div class="content-ad"></div>

# 구현

자, 이제 우리는 구현을 진행할 수 있어요.

## 그림자와 반짝임 그리기

convexBorder 함수를 구현하기 전에, 볼록한 효과를 만들기 위해 그림자를 그리는 지원 함수 drawConvexBorderShadow를 정의해야 해요.

<div class="content-ad"></div>

```kotlin
fun DrawScope.drawConvexBorderShadow(
    outline: Outline,
    strokeWidth: Dp,
    blur: Dp,
    offsetX: Dp,
    offsetY: Dp,
    shadowColor: Color
) = drawIntoCanvas { canvas ->
    // Paint 객체를 생성하고 설정합니다
    val shadowPaint = Paint().apply {
        this.style = PaintingStyle.Stroke
        this.color = shadowColor
        this.strokeWidth = strokeWidth.toPx()
    }

    // 변환 전 현재 레이어 저장
    canvas.saveLayer(size.toRect(), shadowPaint)

    val halfStrokeWidth = strokeWidth.toPx() / 2
    // 테두리가 경계 내에 맞도록 캔버스 이동
    canvas.translate(halfStrokeWidth, halfStrokeWidth)
    // 그림자 외곽선 그리기
    canvas.drawOutline(outline, shadowPaint)

    // 그림자에 대한 혼합 모드 및 흐림 효과 적용
    shadowPaint.asFrameworkPaint().apply {
        xfermode = PorterDuffXfermode(PorterDuff.Mode.DST_OUT)
        maskFilter = BlurMaskFilter(blur.toPx(), BlurMaskFilter.Blur.NORMAL)
    }
    // 클리핑용 색상 설정
    shadowPaint.color = Color.Black

    // 캔버스 이동 및 그림자 클리핑 외곽선 그리기
    canvas.translate(offsetX.toPx(), offsetY.toPx())
    canvas.drawOutline(outline, shadowPaint)
    // 캔버스를 원래 상태로 복원
    canvas.restore()
}
```

이 함수가 어떻게 작동하는지 더 잘 이해하려면 아래 이미지를 확인해보세요 👇

<img src="/assets/img/2024-06-19-HowtoCreateaStunning3DBorderinJetpackCompose_1.png" />

## convexBorder 구현


<div class="content-ad"></div>

이제 drawConvexBorderShadow 함수를 사용하여 볼록한 테두리를 그리는 주요 함수를 정의할 수 있습니다.

여기에 코드 예시가 있습니다 👇

```kotlin
fun Modifier.convexBorder(
    /* 매개변수... */
) = this.drawWithContent {
    // 캔버스 경계 내에 맞도록 크기 조정
    val adjustedSize = Size(size.width - strokeWidth.toPx(), size.height - strokeWidth.toPx())
    // 모양과 조정된 크기에 기반한 윤곽 생성
    val outline = shape.createOutline(adjustedSize, layoutDirection, this)

    // 컴포저 내의 원본 콘텐츠 그리기
    drawContent()

    // 테두리를 캔버스에 맞게 이동
    translate(halfStrokeWidth, halfStrokeWidth) {
        // 주요 테두리 윤곽 그리기
        drawOutline(
            outline = outline,
            color = color,
            style = Stroke(width = strokeWidth.toPx())
        )
    }

    with(convexStyle) {
        // 그림자 윤곽 그리기
        drawConvexBorderShadow(outline, strokeWidth, blur, -offset, -offset, shadowColor)
        // 눈부심 윤곽 그리기
        drawConvexBorderShadow(outline, strokeWidth, blur, offset, offset, glareColor)
    }
}
```

이 코드가 어떻게 작동하는지 확인해보세요👇

<img src="/assets/img/2024-06-19-HowtoCreateaStunning3DBorderinJetpackCompose_2.png" />

<div class="content-ad"></div>

축하드려요🥳! 성공적으로 만들었네요👏. 전체 코드 구현은 GitHub Gist에서 확인하실 수 있어요🧑‍💻. 이제 이 기능을 활용해 아름다운 사용자 정의 검색 바를 만드는 방법을 알아보겠어요.

## 광고

외국어를 배우고 새로운 어휘에 어려움을 겪고 계신가요? 그렇다면, 여정을 쉽고 편리하게 만들어줄 이 어플을 확인해보시기를 강력히 추천해요!

<img src="/assets/img/2024-06-19-HowtoCreateaStunning3DBorderinJetpackCompose_3.png" />

<div class="content-ad"></div>

# 실용적인 예제 💁

자, 오늘의 기사에서 실용적인 부분으로 들어가 봅시다.

커스텀 스타일이 적용된 TextField를 작성하기 위해 BasicTextField의 decorationBox 매개변수를 활용할 수 있습니다.

```js
// 텍스트 입력을 보관할 가변 상태
var text by remember { mutableStateOf("") }

BasicTextField(
    value = text,
    onValueChange = { text = it },
    singleLine = true,
    keyboardOptions = KeyboardOptions(
        capitalization = KeyboardCapitalization.Sentences,
        imeAction = ImeAction.Search
    ),
    textStyle = LocalTextStyle.current.copy(
        fontSize = 16.sp,
        fontWeight = FontWeight.Medium
    ),
    decorationBox = { innerTextField ->
        Row(
            modifier = Modifier
                .size(350.dp, 60.dp)
                // 배경 색상과 모양 설정
                .background(Color(0xFF7F2DBF), CircleShape)
                // 동일한 색상과 모양으로 볼록한 테두리 적용
                .convexBorder(Color(0xFF7F2DBF), CircleShape)
                .padding(horizontal = 20.dp),
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            // 검색 아이콘 추가
            Icon(
                imageVector = Icons.Rounded.Search,
                contentDescription = null
            )
            Box {
                // 입력 텍스트가 비어 있을 때 플레이스홀더 텍스트 표시
                if (text.isEmpty()) {
                    Text(
                        text = "검색...",
                        style = LocalTextStyle.current.copy(color = Color(0xFF242424))
                    )
                }
                // 실제 텍스트 필드 표시
                innerTextField()
            }
        }
    }
)
```

<div class="content-ad"></div>

## 결과 😍

![image](https://miro.medium.com/v2/resize:fit:1400/1*d8g4XiPZ6-d3y-3aTig-9Q.gif)

아래 내용도 맘에 드실지도요 👇

이 글을 읽어 주셔서 감사합니다! ❤️ 즐겁고 유익한 시간이 되셨기를 바랍니다. 좋아하신다면 박수 👏를 눌러주세요. Kappdev를 팔로우하시면 더 많은 흥미로운 글을 만나보실 수 있습니다 😊

<div class="content-ad"></div>

행복한 코딩하세요!

![이미지](/assets/img/2024-06-19-HowtoCreateaStunning3DBorderinJetpackCompose_4.png)