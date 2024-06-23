---
title: "Jetpack Compose로 박스 안팎에서 애니메이션 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-AnimatingInsideandOutsidetheBoxwithJetpackCompose_0.png"
date: 2024-06-23 23:29
ogImage: 
  url: /assets/img/2024-06-23-AnimatingInsideandOutsidetheBoxwithJetpackCompose_0.png
tag: Tech
originalTitle: "Animating Inside and Outside the Box with Jetpack Compose"
link: "https://medium.com/proandroiddev/animating-inside-and-outside-the-box-with-jetpack-compose-a56eba1b6af6"
---


## 안드로이드에서 조합을 활용한 창의적인 애니메이션 구축

![이미지](/assets/img/2024-06-23-AnimatingInsideandOutsidetheBoxwithJetpackCompose_0.png)

# 소개

애니메이션은 사용자 인터페이스를 생생하고 매력적으로 만드는 힘을 지니고 있습니다. 안드로이드에서는 Jetpack Compose를 활용하여 이 힘을 직접 경험할 수 있습니다. 이러한 고급 도구를 제공함으로써 동적인 UI를 만들 수 있는 Jetpack Compose의 애니메이션에 대해 이 글에서는 기본을 넘어 더 깊게 알아보겠습니다.

<div class="content-ad"></div>

우리는 플루이드하고 물리학적인 움직임을 만드는 것부터 복잡한 코레오그래피 시퀀스를 만들어 인터페이스에 서사적인 품질을 더하는 기술 범위를 다룰 것입니다. 여러분이 기술을 미세하게 조정하거나 가능한 영역에 대해 궁금해하는 경우, 이 여정은 앱을 매끄럽게 작동하는 것뿐만 아니라 모든 상호작용에서 사용자를 즐겁게 만드는 실용적인 통찰력을 제공할 것입니다.

자세히 알아보고 이러한 애니메이션들이 UI 디자인 접근 방식을 변화시키고 사용자에게 더 직관적이고 반응성 있으며 즐거운 경험을 제공하는 방법을 발견해 봅시다.

# 섹션 1 — Jetpack Compose에서 사용자 정의 애니메이션 핸들러

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*C1_mzDHNHOfSZkIiULgRzw.gif)

<div class="content-ad"></div>

## 사용자 지정 애니메이션으로 동적 상호작용 수용하기

이 섹션에서는 Jetpack Compose에서 고급 사용자 지정 애니메이션 핸들러의 사용에 대해 탐구하여 동적이고 대화식 UI 요소를 만드는 방법을 살펴봅니다. 우리의 초점은 사용자 상호작용이 의미 있는 방식으로 애니메이션에 영향을 미치는 실제 예제에 있습니다.

## 예제 - 대화식 게임 캐릭터 이동

게임 캐릭터(얼굴 아이콘으로 표시되는)가 사용자가 드래그할 수 있는 제어 지점에 의해 결정된 경로를 따라가는 예제로 이 개념을 설명하겠습니다.

<div class="content-ad"></div>

```kotlin
@Composable
fun GameCharacterMovement() {
    val startPosition = Offset(100f, 100f)
    val endPosition = Offset(250f, 400f)
    val controlPoint = remember { mutableStateOf(Offset(200f, 300f)) }
    val position = remember { Animatable(startPosition, Offset.VectorConverter) }

    LaunchedEffect(controlPoint.value) {
        position.animateTo(
            targetValue = endPosition,
            animationSpec = keyframes {
                durationMillis = 5000
                controlPoint.value at 2500 // 가운데 지점은 드래그 가능한 컨트롤포인트로 제어됩니다
            }
        )
    }

    val onControlPointChange: (offset: Offset) -> Unit = {
        controlPoint.value = it
    }

    Box(modifier = Modifier.fillMaxSize()) {

        Icon(
            Icons.Filled.Face, contentDescription = "로컬라이즈된 설명", modifier = Modifier
                .size(50.dp)
                .offset(x = position.value.x.dp, y = position.value.y.dp)
        )

        DraggableControlPoint(controlPoint.value, onControlPointChange)
    }
}
```

## 설명

- GameCharacterMovement 함수는 게임 캐릭터를 나타내는 아이콘을 애니메이션화합니다. 애니메이션 경로는 사용자 상호 작용에 의해 설정 및 업데이트되는 controlPoint으로 제어됩니다.
- Animatable은 startPosition에서 endPosition으로 아이콘의 위치를 부드럽게 전환하기 위해 사용됩니다.
- LaunchedEffect는 controlPoint 값의 변경 사항을 감지하고, 제어 지점이 이동할 때마다 애니메이션을 다시 트리거합니다.
- animationSpec은 애니메이션의 지속 시간, 지연 및 이징을 정의하는 구성이며, 애니메이션된 값이 시간에 따라 어떻게 변하는지 결정합니다.
- keyframes를 통해 애니메이션의 중간 지점을 제어 지점에서 최종 위치까지 정의할 수 있습니다. 중요한 역할을 하는 복잡한, 조정된 애니메이션을 만드는 데 특히 유용합니다.
- keyframes 블록은 키프레임의 시퀀스로 애니메이션을 정의합니다. 2500 밀리초(절반 지점)에서 캐릭터가 제어 지점에 도달한 후 최종 위치로 이동합니다.

```kotlin
@Composable
fun DraggableControlPoint(controlPoint: Offset, onControlPointChange: (Offset) -> Unit) {
    var localPosition by remember { mutableStateOf(controlPoint) }
    Box(
        modifier = Modifier
            .offset {
                IntOffset(
                    x = localPosition.x.roundToInt() - 15,
                    y = localPosition.y.roundToInt() - 15
                )
            }
            .size(30.dp)
            .background(Color.Red, shape = CircleShape)
            .pointerInput(Unit) {
                detectDragGestures(onDragEnd = {
                    onControlPointChange(localPosition)
                }) { _, dragAmount ->
                    // 화면 범위에 맞춰 조정
                    val newX = (localPosition.x + dragAmount.x).coerceIn(0f, 600f)
                    val newY = (localPosition.y + dragAmount.y).coerceIn(0f, 600f)
                    localPosition = Offset(newX, newY)
                }
            }
    )
}
```

<div class="content-ad"></div>

## 설명

- DraggableControlPoint는 사용자가 제어 지점의 위치를 인터랙티브하게 변경할 수 있게 하는 컴포저블입니다.
- 제어 지점을 드래그하면 localPosition이 업데이트되고, 이후 드래그 제스처가 완료될 때 (onDragEnd) 이를 GameCharacterMovement에 반영합니다. 이 상호 작용은 애니메이션된 아이콘의 경로를 변경합니다.

## 실제 사용 사례

- 상호 작용적인 교육 앱: 교육 앱에서는 애니메이션을 통해 학습을 더 매료적으로 만들 수 있습니다. 예를 들어, 천문학 앱에서 행성을 따라 원거리를 끌어서 다양한 별자리를 볼 수 있습니다.
- 상호 작용적인 이야기 전달과 게임: 디지털 이야기 전달이나 게임 앱에서 사용자가 드래그 가능한 요소를 통해 이야기나 게임 환경에 영향을 미칠 수 있도록 하는 것은 더 몰입적인 경험을 만들어냅니다.

<div class="content-ad"></div>

# 섹션 2 — Jetpack Compose에서 복잡한 애니메이션 조정하기

## 조화로운 효과를 위한 여러 요소의 동기화

이 섹션에서는 Jetpack Compose에서 복잡한 애니메이션을 조율하는 기술에 대해 다룹니다. 여러 요소가 매끄럽게 상호작용하는 동기화된 애니메이션을 만드는 데 초점을 맞춰 전체 사용자 경험을 향상시킵니다.

## A) 연쇄 반응 애니메이션 — 도미노 효과

<div class="content-ad"></div>


![domino effect](https://miro.medium.com/v2/resize:fit:1400/1*iNeJJU3ixcdcZnQFHHSWYw.gif)

UI에서 도미노 효과를 만드는 방법은 하나의 애니메이션이 완료되면 다음 애니메이션을 시작하도록 설정하는 일련의 애니메이션을 만들어내면 됩니다.

```kotlin
@Composable
fun DominoEffect() {
    val animatedValues = List(6) { remember { Animatable(0f) } }

    LaunchedEffect(Unit) {
        animatedValues.forEachIndexed { index, animate ->
            animate.animateTo(
                targetValue = 1f,
                animationSpec = tween(durationMillis = 1000, delayMillis = index * 100)
            )
        }
    }

    Box (modifier = Modifier.fillMaxSize()){
      animatedValues.forEachIndexed { index, value ->
        Box(
            modifier = Modifier
                .size(50.dp)
                .offset(x = ((index+1) * 50).dp, y = ((index+1) * 30).dp)
                .background(getRandomColor(index).copy(alpha = value.value))
        )
      }
    }
}

fun getRandomColor(seed: Int): Color {
    val random = Random(seed = seed).nextInt(256)
    return Color(random, random, random)
}
```

## 설명


<div class="content-ad"></div>

- animatedValues는 상자의 불투명도를 제어하는 Animatable 값 목록입니다.
- LaunchedEffect는 이러한 값들에 대한 일련의 애니메이션을 트리거하여 각 상자가 이전 상자 뒤에 나타나는 비슷한 도미노가 넘어지는 효과를 만듭니다.
- getRandomColor 함수는 각 상자에 대해 무작위 회색을 생성하여 시퀀스의 각 구성 요소에 고유한 시각적 요소를 추가합니다.
- 상자들은 화면 대각선상에 배치되어 도미노 효과를 더욱 부각시킵니다.

## B) 대화식 스크롤 가능한 타임라인

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*Kk-V0g5pEqy83NajRy_6lA.gif)

이 타임라인에서 사용자가 타임라인을 스크롤하면 각 요소가 서서히 나타나고 위치로 이동합니다. 스크롤 가능한 목록에는 LazyColumn을 사용하고 애니메이션에는 Animatable을 사용할 것입니다.

<div class="content-ad"></div>

```kotlin
@Composable
fun InteractiveTimeline(timelineItems: List<String>) {
    val scrollState = rememberLazyListState()

    LazyColumn(state = scrollState) {
        itemsIndexed(timelineItems) { index, item ->
            val animatableAlpha = remember { Animatable(0f) }
            val isVisible = remember {
                derivedStateOf {
                    scrollState.firstVisibleItemIndex <= index
                }
            }

            LaunchedEffect(isVisible.value) {
                if (isVisible.value) {
                    animatableAlpha.animateTo(
                        1f, animationSpec = tween(durationMillis = 1000)
                    )
                }
            }

            TimelineItem(
                text = item,
                alpha = animatableAlpha.value,
            )
        }
    }
}

@Composable
fun TimelineItem(text: String, alpha: Float) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .background(Color.DarkGray.copy(alpha = alpha))
            .padding(16.dp),
        verticalAlignment = Alignment.CenterVertically
    ) {
        Text(
            text = text,
            color = Color.White,
            modifier = Modifier.fillMaxWidth(),
            textAlign = TextAlign.Center,
            fontSize = 18.sp,
            fontWeight = FontWeight.SemiBold
        )
    }
}
```

## 설명

- animatableAlpha은 각 타임라인 아이템의 투명도 (투명도)를 제어하며, 초기에 0 (완전히 투명)로 설정됩니다.
- isVisible 상태는 현재 스크롤 위치에서 파생되어 항목이 표시될지 여부를 결정합니다.
- 사용자가 스크롤하면 LaunchedEffect가 뷰포트에 들어오는 항목에 대한 페이드인 애니메이션을 트리거합니다.

## 사용 사례


<div class="content-ad"></div>

이 상호작용형 타임라인은 사용자에게 시각적으로 매력적인 방식으로 일련의 이벤트나 단계를 제시하고 싶은 응용 프로그램에 이상적입니다. 애니메이션은 항목이 나타날 때 주목을 끌어 사용자 참여도를 높입니다.

# 섹션 3 — 젯팩 콤포즈에서 현실감 있는 물리 기반 애니메이션

![애니메이션 이미지](https://miro.medium.com/v2/resize:fit:1400/1*lZ_rpGorcFzewpUJN6WPAQ.gif)

## UI 다이내믹스 향상을 위한 물리학 활용하기

<div class="content-ad"></div>

이 섹션에서는 물리학 원리를 Jetpack Compose와 통합하여 UI에 현실성과 상호 작용성을 더하는 방법을 살펴보겠습니다. 탄탄한 드래그 상호 작용 예시에 초점을 맞출 것입니다.

## 드래그 시 탄탄한 효과

이 예시에서는 아이콘에 탄탄한 드래그 상호 작용을 보여줍니다. 수직으로 드래그할 때 아이콘이 늘어나고 탄탄한 효과로 튕기며, 스프링이나 고무줄의 행동을 모방합니다.

```js
@Composable
fun ElasticDraggableBox() {
    var animatableOffset by remember { mutableStateOf(Animatable(0f)) }

    Box(modifier = Modifier.fillMaxSize().background(Color(0xFFFFA732)), contentAlignment = Alignment.Center) {
        Box(
            modifier = Modifier
                .offset(y = animatableOffset.value.dp)
                .draggable(
                    orientation = Orientation.Vertical,
                    state = rememberDraggableState { delta ->
                        animatableOffset = Animatable(animatableOffset.value + delta)
                    },
                    onDragStopped = {
                        animatableOffset.animateTo(0f, animationSpec = spring())
                    }
                )
                .size(350.dp),
            contentAlignment = Alignment.Center
        ) {
            Icon(
                Icons.Filled.Favorite,
                contentDescription = "heart",
                modifier = Modifier.size(animatableOffset.value.dp + 150.dp),
                tint = Color.Red
            )
        }
    }
}
```

<div class="content-ad"></div>

## 설명

- 드래그할 수 있는 modifier를 사용하여 아이콘을 포함하는 Box Composable을 만듭니다.
- animatableOffset은 드래깅으로 인한 아이콘의 수직 오프셋을 추적합니다.
- 드래그하는 동안 아이콘의 크기가 드래그 양에 따라 변경되어 스트레칭 효과를 만듭니다.
- 드래그가 멈추면 (onDragStopped), animatableOffset을 스프링 애니메이션을 사용하여 0f로 다시 애니메이션화하여 아이콘이 원래 크기와 위치로 되돌아가도록 합니다.

# 섹션 4 — 제스처 기반 애니메이션(Jetpack Compose에서)

## 사용자 경험을 향상시키는 반응형 제스처들

<div class="content-ad"></div>

이 섹션에서는 사용자 동작으로 제어되는 애니메이션을 생성하는 데 Jetpack Compose를 사용하는 방법을 살펴봅니다. 두 가지 예제에 중점을 두겠습니다 - 멀티터치로 변환 가능한 이미지 및 제스처로 제어되는 오디오 파형입니다.

## A) 멀티 터치로 변환 가능한 이미지

이 예제에서는 사용자가 핀치, 줌 및 회전과 같은 멀티터치 제스처를 사용하여 상호 작용할 수 있는 이미지 뷰를 생성합니다.

![image](https://miro.medium.com/v2/resize:fit:1400/1*34WxcBivTWhiCY6KVSVelQ.gif)

<div class="content-ad"></div>

```kotlin
@Composable
fun TransformableImage(imageId: Int = R.drawable.android) {
    var scale by remember { mutableStateOf(1f) }
    var rotation by remember { mutableStateOf(0f) }
    var offset by remember { mutableStateOf(Offset.Zero) }

    Box(modifier = Modifier.fillMaxSize().background(Color.DarkGray), contentAlignment = Alignment.Center) {
        Image(
            painter = painterResource(id = imageId),
            contentDescription = "Transformable image",
            contentScale = ContentScale.Crop,
            modifier = Modifier
                .size(300.dp)
                .graphicsLayer(
                    scaleX = scale,
                    scaleY = scale,
                    rotationZ = rotation,
                    translationX = offset.x,
                    translationY = offset.y
                )
                .pointerInput(Unit) {
                    detectTransformGestures { _, pan, zoom, rotate ->
                        scale *= zoom
                        rotation += rotate
                        offset += pan
                    }
                }
        )
    }
}
```

## 설명

- 이미지 컴포저블은 scale, rotation, translation과 같은 변환을 적용하기 위해 graphicsLayer로 수정되었습니다.
- detectTransformGestures가 사용되는 pointerInput은 멀티 터치 제스처를 처리하고, scale, rotation, offset을 해당 값을 따라 업데이트합니다.

## B) 제스처 컨트롤 웨이브폼


<div class="content-ad"></div>

사용자 제스처(스와이프 및 핀치)에 따라 와브폼 시각화가 모양을 바꿉니다. 이를 통해 진폭 및 주파수와 같은 요소를 제어할 수 있습니다.

![waveform visualization](https://miro.medium.com/v2/resize:fit:1400/1*qKzb1XpUrSGKdCL-OxhtLw.gif)

```js
@Composable
fun GestureControlledWaveform() {
    var amplitude by remember { mutableStateOf(100f) }
    var frequency by remember { mutableStateOf(1f) }

    Canvas(modifier = Modifier
        .fillMaxSize()
        .pointerInput(Unit) {
            detectDragGestures { _, dragAmount ->
                amplitude += dragAmount.y
                frequency += dragAmount.x / 500f 
                // 드래그에 기반한 주파수 조정
            }
        }
        .background(
            Brush.verticalGradient(
                colors = listOf(Color(0xFF003366), Color.White, Color(0xFF66B2FF))
            )
        )) {
        val width = size.width
        val height = size.height
        val path = Path()

        val halfHeight = height / 2
        val waveLength = width / frequency

        path.moveTo(0f, halfHeight)

        for (x in 0 until width.toInt()) {
            val theta = (2.0 * Math.PI * x / waveLength).toFloat()
            val y = halfHeight + amplitude * sin(theta.toDouble()).toFloat()
            path.lineTo(x.toFloat(), y)
        }

        val gradient = Brush.horizontalGradient(
            colors = listOf(Color.Blue, Color.Cyan, Color.Magenta)
        )

        drawPath(
            path = path,
            brush = gradient
        )
    }
}
```

## 설명

<div class="content-ad"></div>

- 진폭과 주파수는 각각 웨이브폼의 진폭과 주파수를 제어하는 상태 변수입니다.
- Canvas composable은 웨이브폼을 그리는데 사용됩니다. Canvas 내부의 그리기 로직은 사인 함수에 기반하여 각 X 위치에 대한 Y 위치를 계산하여 파동 효과를 만듭니다.
- detectDragGestures 수정자는 사용자의 드래그 제스처에 기반하여 진폭과 주파수를 업데이트하는데 사용됩니다. 가로 드래그는 주파수를 조절하고, 세로 드래그는 진폭을 조절합니다.
- 사용자가 화면을 가로지르면, 웨이브폼의 형태가 그에 따라 변경되어 상호 작용하는 경험을 제공합니다.

## 참고

- 이것은 기본 구현입니다. 보다 현실적인 오디오 웨이브폼을 만들려면 실제 오디오 데이터를 통합해야 합니다.
- 제스처에 대한 웨이브폼의 반응은 드래그하는 동안 진폭과 주파수가 어떻게 수정되는지 조정하여 미세 조정할 수 있습니다.

이 예제는 Compose에서 기본 대화형 웨이브폼을 만드는 방법을 보여주며, 더 복잡한 사용 사례에 확장하거나 수정하여 더 복잡한 제스처를 처리할 수 있습니다.

<div class="content-ad"></div>

# 섹션 5 — Jetpack Compose에서 상태 주도 애니메이션 패턴

![image](https://miro.medium.com/v2/resize:fit:1400/1*nkfhmC6JjQnshL3y_izhKg.gif)

## 데이터 및 상태 변경에 따른 UI 애니메이션

이 섹션은 데이터 또는 UI 상태 변경으로 구동되는 애니메이션을 생성하는 데 초점을 맞추어 앱의 상호 작용성과 반응성을 향상시킵니다. 우리는 데이터 그래프를 애니메이션화하고 다중 상태 UI에서 상태 전이를 구현하는 두 가지 구체적인 예시를 살펴볼 것입니다.

<div class="content-ad"></div>

## A) 데이터 기반 그래프 애니메이션

이 예시는 데이터 세트의 변화에 반응하여 그래프의 경로가 애니메이션되는 라인 그래프를 보여줍니다.

```js
@Composable
fun AnimatedGraphExample() {
    var dataPoints by remember { mutableStateOf(generateRandomDataPoints(5)) }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .background(Color.DarkGray)
    ) {
        AnimatedLineGraph(dataPoints = dataPoints)

        Spacer(modifier = Modifier.height(16.dp))

        Button(
            onClick = {
                dataPoints = generateRandomDataPoints(5)
            },
            modifier = Modifier.align(Alignment.CenterHorizontally),
            colors = ButtonDefaults.buttonColors(containerColor = Color.Green)
        ) {
            Text(
                "데이터 업데이트",
                fontWeight = FontWeight.Bold,
                color = Color.DarkGray,
                fontSize = 18.sp
            )
        }
    }
}

@Composable
fun AnimatedLineGraph(dataPoints: List<Float>) {
    val animatableDataPoints = remember { dataPoints.map { Animatable(it) } }
    val path = remember { Path() }

    LaunchedEffect(dataPoints) {
        animatableDataPoints.forEachIndexed { index, animatable ->
            animatable.animateTo(dataPoints[index], animationSpec = TweenSpec(durationMillis = 500))
        }
    }

    Canvas(
        modifier = Modifier
            .fillMaxWidth()
            .height(400.dp)
    ) {
        path.reset()
        animatableDataPoints.forEachIndexed { index, animatable ->
            val x = (size.width / (dataPoints.size - 1)) * index
            val y = size.height - (animatable.value * size.height)
            if (index == 0) path.moveTo(x, y) else path.lineTo(x, y)
        }
        drawPath(path, Color.Green, style = Stroke(5f))
    }
}

fun generateRandomDataPoints(size: Int): List<Float> {
    return List(size) { Random.nextFloat() }
}
```

## 설명

<div class="content-ad"></div>

- AnimatedGraphExample composable은 라인 그래프의 데이터 포인트를 업데이트할 수 있는 환경을 생성합니다.
- 그래프는 Canvas 내에서 그려지며 drawPath 메서드는 animatableDataPoints에서 애니메이션 값을 사용합니다.
- 그래프의 각 데이터 포인트에 대해 캔버스 상에서 해당 x (수평) 및 y (수직) 위치를 계산해야 합니다.
- x 계산 — x 위치는 데이터 포인트의 인덱스와 캔버스의 총 너비에 기반하여 계산됩니다. 데이터 포인트를 캔버스 너비를 따라 균일하게 분산합니다.

```js
val x = (size.width / (dataPoints.size - 1)) * index
```

- y 계산 — y 위치는 데이터 포인트의 값 (animatable.value) 및 캔버스의 높이에 기반하여 계산됩니다.

```js
val y = size.height - (animatable.value * size.height)
```

<div class="content-ad"></div>

- 경로는 첫 번째 데이터 포인트에서 시작하여 lineTo를 사용하여 각 후속 포인트로 선을 그려 그래프 선을 생성합니다.
- 경로는 데이터 포인트의 애니메이션 값에 기반하여 그려지며, 데이터가 변경될 때 애니메이션 효과를 만듭니다.

## B) 다중 상태 UI에서 상태 전환

다중 상태 UI에서 상태 전환을 구현하는 방법은 Animatable을 사용하여 다른 UI 상태 간에 애니메이션을 적용하는 것입니다.

```js
enum class UIState { StateA, StateB, StateC }

@Composable
fun StateTransitionUI() {
    var currentState by remember { mutableStateOf(UIState.StateA) }

    Box(
        modifier = Modifier
            .fillMaxSize()
            .background(getBackgroundColorForState(currentState)),
        contentAlignment = Alignment.Center
    ) {
        AnimatedContent(currentState = currentState)

        Button(
            onClick = { currentState = getNextState(currentState) },
            modifier = Modifier.align(Alignment.BottomCenter)
        ) {
            Text("다음 상태")
        }
    }
}

@Composable
fun AnimatedContent(currentState: UIState) {
    AnimatedVisibility(
        visible = currentState == UIState.StateA,
        enter = fadeIn(animationSpec = tween(durationMillis = 2000)) + expandVertically(),
        exit = fadeOut(animationSpec = tween(durationMillis = 2000)) + shrinkVertically()
    ) {
        Text("현재 상태는 ${currentState.name} 입니다", fontSize = 32.sp)
    }

    // B와 C에 대한 유사한 블록

}

fun getBackgroundColorForState(state: UIState): Color {
    return when (state) {
        UIState.StateA -> Color.Red
        UIState.StateB -> Color.Green
        UIState.StateC -> Color.Blue
    }
}

fun getNextState(currentState: UIState): UIState {
    return when (currentState) {
        UIState.StateA -> UIState.StateB
        UIState.StateB -> UIState.StateC
        UIState.StateC -> UIState.StateA
    }
}
```

<div class="content-ad"></div>

## 설명

- 이 예시에서 AnimatedVisibility는 각 상태의 콘텐츠가 나타나고 사라질 때 애니메이션 효과를 적용하는 데 사용됩니다. 이는 상태가 변경될 때 부드러운 전환 효과를 추가합니다.
- 각 상태(StateA, StateB, StateC)마다 해당 콘텐츠의 가시성을 페이드 및 확장/축소 애니메이션으로 제어하는 AnimatedVisibility 블록이 있습니다.
- AnimatedVisibility의 enter 및 exit 매개변수는 콘텐츠가 표시되거나 숨겨질 때의 애니메이션을 정의합니다.

# 섹션 6 — Compose에서 모양 변환

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*q82EIocVzR8XBMuG_14mdg.gif)

<div class="content-ad"></div>

도형 간 변환 애니메이션은 이러한 도형의 속성을 보간하는 것을 포함합니다.

```js
@Composable
fun ShapeMorphingAnimation() {
    val animationProgress = remember { Animatable(0f) }

    LaunchedEffect(Unit) {
        animationProgress.animateTo(
            targetValue = 1f,
            animationSpec = infiniteRepeatable(
                animation = tween(2000, easing = LinearOutSlowInEasing),
                repeatMode = RepeatMode.Reverse
            )
        )
    }

    Canvas(modifier = Modifier.padding(40.dp).fillMaxSize()) {
        val sizeValue = size.width.coerceAtMost(size.height) / 2
        val squareRect = Rect(center = center, sizeValue)

        val morphedPath = interpolateShapes(progress = animationProgress.value, squareRect = squareRect)
        drawPath(morphedPath, color = Color.Blue, style = Fill)
    }
}

fun interpolateShapes(progress: Float, squareRect: Rect): Path {
    val path = Path()

    val cornerRadius = CornerRadius(
        x = lerp(start = squareRect.width / 2, stop = 0f, fraction = progress),
        y = lerp(start = squareRect.height / 2, stop = 0f, fraction = progress)
    )

    path.addRoundRect(
        roundRect = RoundRect(rect = squareRect, cornerRadius = cornerRadius)
    )

    return path
}

fun lerp(start: Float, stop: Float, fraction: Float): Float {
    return (1 - fraction) * start + fraction * stop
}
```

## 설명

- ShapeMorphingAnimation은 animationProgress 값을 0과 1 사이로 토글하는 무한 애니메이션을 설정합니다.
- Canvas 콤포저블을 사용하여 도형을 그립니다. 여기서 캔버스 크기에 기반하여 정사각형의 크기(squareRect)를 정의합니다.
- interpolateShapes는 현재 애니메이션 진행도와 정사각형 사각형을 가져와 원과 정사각형 사이를 보간합니다. 변형되는 모양을 나타내는 둥근 직사각형의 cornerRadius를 서서히 조절하기 위해 lerp(선형 보간)를 사용합니다.
- 진행도가 0일 때 cornerRadius는 직사각형의 반만큼이므로 도형은 원이 됩니다. 진행도가 1일 때 cornerRadius는 0이 되어 도형이 정사각형이 됩니다.

<div class="content-ad"></div>

## 실제 사용 사례

- 로딩 및 진행 상태 표시기 — 모프 형태는 더 매력적인 로딩 또는 진행 상태 표시기를 만드는 데 사용될 수 있습니다. 진행 상태나 로딩 상태를 나타내는 더욱 시각적으로 흥미로운 방법을 제공합니다.
- UI 내 아이콘 전환 — 모프 아이콘은 사용자 작업에 대한 시각적 피드백을 제공하는 데 사용할 수 있습니다. 예를 들어, 클릭할 때 플레이 버튼이 일시 중지 버튼으로 변하는 경우 또는 햄버거 메뉴 아이콘이 뒤로 이동하는 화살표로 변하는 경우 등.
- 데이터 시각화 — 복잡한 데이터 시각화에서 모프는 사용자가 시간에 따른 변경 또는 범주 간의 변화를 따라가고 이해하기 쉽도록 돕는 데 도움이 될 수 있습니다.

# 눈 오는 소리 좀?

간단한 입자 시스템을 사용하여 눈이 내리는 효과를 만들어보겠습니다.

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경합니다.


<img src="https://miro.medium.com/v2/resize:fit:1400/1*E26GhhxDZLGTpE8gMvJoHw.gif" />

```js
data class Snowflake(
    var x: Float,
    var y: Float,
    var radius: Float,
    var speed: Float
)

@Composable
fun SnowfallEffect() {
    val snowflakes = remember { List(100) { generateRandomSnowflake() } }
    val infiniteTransition = rememberInfiniteTransition(label = "")

    val offsetY by infiniteTransition.animateFloat(
        initialValue = 0f,
        targetValue = 1000f,
        animationSpec = infiniteRepeatable(
            animation = tween(durationMillis = 5000, easing = LinearEasing),
            repeatMode = RepeatMode.Restart
        ), label = ""
    )

    Canvas(modifier = Modifier.fillMaxSize().background(Color.Black)) {
        snowflakes.forEach { snowflake ->
            drawSnowflake(snowflake, offsetY % size.height)
        }
    }
}

fun generateRandomSnowflake(): Snowflake {
    return Snowflake(
        x = Random.nextFloat(),
        y = Random.nextFloat() * 1000f,
        radius = Random.nextFloat() * 2f + 2f, // Snowflake size
        speed = Random.nextFloat() * 1.2f + 1f  // Falling speed
    )
}

fun DrawScope.drawSnowflake(snowflake: Snowflake, offsetY: Float) {
    val newY = (snowflake.y + offsetY * snowflake.speed) % size.height
    drawCircle(Color.White, radius = snowflake.radius, center = Offset(snowflake.x * size.width, newY))
}
```

## 설명

- SnowfallEffect는 여러 개의 눈송이(Snowflake 객체)를 가진 입자 시스템을 설정합니다.
- 각 Snowflake는 위치 (x, y), 반지름 (크기), 속도와 같은 속성을 갖습니다.
- rememberInfiniteTransition 및 animateFloat은 눈이 내리는 것을 시뮬레이션하기 위한 연속적인 수직 이동 효과를 생성하는 데 사용됩니다.
- Canvas composable은 각 눈송이를 그리는 데 사용됩니다. drawSnowflake 함수는 속도와 애니메이션된 offsetY에 기반하여 각 눈송이의 새 위치를 계산합니다.
- 눈송이들은 아래로 떨어진 후 다시 위로 나타나며, 반복되는 눈내림 효과를 만듭니다.


<div class="content-ad"></div>

# 결론

제트팩 구성에서 애니메이션을 탐색하면서 마무리하는 시점에서, 애니메이션이 시각적 장식 이상의 중요한 도구라는 것이 분명해졌습니다. 애니메이션은 매력적이고 직관적이며 즐거운 사용자 경험을 만드는 데 중요한 도구입니다.

## 상호 작용 포용

게임 캐릭터의 동적인 움직임부터 인터랙티브 타임라인까지, 우리는 애니메이션이 사용자 상호작용을 더 매력적이고 유익하게 만들 수 있는 방법을 알아보았습니다.

<div class="content-ad"></div>

## 현실적인 경험 구현하기

눈 내리는 효과와 형태 변화는 이 도구상자가 디지털 영역에 현실감과 유동성을 표현하는 능력을 보여줍니다. 이러한 애니메이션들은 사용자와 공감을 형성하는 몰입형 경험을 만들어줍니다.

## 복잡함을 간단하게 만들기

여러 요소를 조정하거나 상태 전환을 애니메이션화하는 경우, 이것이 할 수 있는 간단함이 돋보입니다.

<div class="content-ad"></div>

# 마무리 인사

만약 읽은 것이 마음에 들었다면, 소중한 피드백이나 감사의 말을 자유롭게 남겨주세요. 저는 항상 개발자 친구들과 함께 배우고 협력하며 성장하고자 노력하고 있습니다.

질문이 있으시다면 언제든지 메시지를 보내주세요!

더 많은 기사를 보시려면 저의 Medium 프로필을 팔로우해주세요.

<div class="content-ad"></div>

LinkedIn과 트위터에서 저와 연결해주세요. 협업 기회가 있을지도 몰라요.

애니메이션 즐기세요!