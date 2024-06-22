---
title: "젯팩 콤포즈로 귀여운 3D 라디오 버튼을 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtoCreateaCute3DRadioButtoninJetpackCompose_0.png"
date: 2024-06-19 10:35
ogImage: 
  url: /assets/img/2024-06-19-HowtoCreateaCute3DRadioButtoninJetpackCompose_0.png
tag: Tech
originalTitle: "How to Create a Cute 3D Radio Button in Jetpack Compose"
link: "https://medium.com/@kappdev/how-to-create-a-cute-3d-radio-button-in-jetpack-compose-9068deb2a71c"
---


환영합니다 👋

이 글에서는 젯팩 컴포즈로 귀여운 3D 라디오 버튼을 만들어, 5분만에 앱의 외관을 개선해볼 거에요.

코드를 살펴봐요! 🔎

![이미지](/assets/img/2024-06-19-HowtoCreateaCute3DRadioButtoninJetpackCompose_0.png)

<div class="content-ad"></div>

# 상수 정의

나중에 사용할 상수를 정의하여 여정을 시작해보겠습니다.

```js
// 라디오 버튼 애니메이션 지속 시간
private const val RadioAnimationDuration = 100

// 그림자 효과에 대한 오프셋 및 흐림 반경
private val RadioShadowOffset = 1.dp
private val RadioShadowBlur = 2.dp

// 그림자 및 빛효과에 대한 색상
private val RadioShadowColor = Color.Black.copy(0.54f)
private val RadioGlareColor = Color.White.copy(0.64f)

// 라디오 버튼 및 그의 점에 대한 크기 상수
private val RadioButtonDotSize = 12.dp
private val RadioStrokeWidth = 3.dp
private val RadioButtonSize = 22.dp
```

# 다양한 상태를 위한 색상 정의

<div class="content-ad"></div>

다음 단계는 라디오 버튼이 다양한 상태(선택됨, 선택되지 않음, 활성화됨, 비활성화됨)에서 사용하는 색상을 정의하는 것입니다. 이러한 색상을 관리하는 ConvexRadioButtonColors 클래스를 만들 것입니다.

```kotlin
class ConvexRadioButtonColors(
    private val selectedColor: Color,
    private val unselectedColor: Color,
    private val disabledSelectedColor: Color,
    private val disabledUnselectedColor: Color
) {
    @Composable
    fun radioColorAsState(enabled: Boolean, selected: Boolean): State<Color> {
        // 현재 상태를 기반으로 대상 색상을 결정합니다
        val target = when {
            enabled && selected -> selectedColor
            enabled && !selected -> unselectedColor
            !enabled && selected -> disabledSelectedColor
            else -> disabledUnselectedColor
        }
        // 활성화됐을 때 색상을 애니메이션화하고, 그렇지 않으면 업데이트된 상태를 직접 반환합니다
        return if (enabled) {
            animateColorAsState(target, tween(durationMillis = RadioAnimationDuration))
        } else {
            rememberUpdatedState(target)
        }
    }
}
```

이 클래스는 라디오 버튼의 다양한 상태를 나타내는 네 가지 색상을 매개변수로 사용하며, 현재 라디오 버튼 상태에 기반하여 적절한 색상을 반환하는 radioColorAsState 컴포저블 함수를 제공합니다.

# 기본 색상 제공하기

<div class="content-ad"></div>

다음으로, 라디오 버튼의 기본 색상 값을 제공하는 ConvexRadioButtonDefaults 객체를 생성할 것입니다.

```js
object ConvexRadioButtonDefaults {
    @Composable
    fun colors(
        selectedColor: Color = MaterialTheme.colorScheme.primary,
        unselectedColor: Color = MaterialTheme.colorScheme.onSurfaceVariant,
        disabledSelectedColor: Color = MaterialTheme.colorScheme.onSurfaceVariant.copy(alpha = DisabledAlpha),
        disabledUnselectedColor: Color = MaterialTheme.colorScheme.onSurfaceVariant.copy(alpha = DisabledAlpha)
    ): ConvexRadioButtonColors = ConvexRadioButtonColors(selectedColor, unselectedColor, disabledSelectedColor, disabledUnselectedColor)
}
```

이 객체는 기본 또는 지정된 색상으로 ConvexRadioButtonColors 인스턴스를 반환하는 Composable 함수 colors를 제공합니다. 이러한 기본값은 현재 material theme의 색상 체계를 기반으로 합니다.

# Convex Radio Button 정의하기

<div class="content-ad"></div>

이제 주요 ConvexRadioButton composable 함수를 선언하고 그 매개변수들을 살펴볼 수 있습니다.

```js
@Composable
fun ConvexRadioButton(
    selected: Boolean,
    modifier: Modifier = Modifier,
    enabled: Boolean = true,
    colors: ConvexRadioButtonColors = ConvexRadioButtonDefaults.colors(),
    interactionSource: MutableInteractionSource = remember { MutableInteractionSource() },
    onClick: () -> Unit
)
```

💎 selected ➜ 라디오 버튼이 선택되었는지 여부를 나타냅니다.

💎 modifier ➜ 이 라디오 버튼에 적용할 Modifier입니다.

<div class="content-ad"></div>

💎 enabled ➜ 라디오 버튼이 사용자 상호 작용을 위해 활성화되는지 여부를 나타냅니다.

💎 colors ➜ 라디오 버튼의 다른 상태에 대한 색상을 정의하는 ConvexRadioButtonColors의 인스턴스입니다.

💎 interactionSource ➜ 사용자 상호 작용 이벤트를 처리하는 상호 작용 소스입니다.

💎 onClick ➜ 라디오 버튼이 클릭될 때 호출되는 람다 함수입니다.

<div class="content-ad"></div>

# Convex Radio Button 구현

우리는 가장 흥미로운 부분, Convex 라디오 버튼을 만드는 과정에 진입하고 있어요.

## 준비

이 기능을 구현하기 위해 innerShadow 및 convexBorder 수정자를 활용해야 합니다. 자세한 설명은 아래 제공된 관련 기사를 참조하거나👇 또는 InnerShadow Gist, ConvexBorder Gist에서 코드를 가져오세요.

<div class="content-ad"></div>

## 구현

자, 코드로 들어가 봅시다 👀

```js
@Composable
fun ConvexRadioButton(
    /* 매개변수... */
) {
    // 선택된 상태에 따라 도트의 크기를 애니메이션화합니다
    val dotSize = animateDpAsState(
        targetValue = if (selected) RadioButtonDotSize else 0.dp,
        animationSpec = tween(durationMillis = RadioAnimationDuration)
    )
    // 현재 상태에 맞는 색상을 가져옵니다
    val radioColor = colors.radioColorAsState(enabled, selected)

    // 선택 및 클릭 상호작용을 처리하는 Modifier
    val selectableModifier = Modifier.selectable(
        selected = selected,
        onClick = onClick,
        enabled = enabled,
        role = Role.RadioButton,
        interactionSource = interactionSource,
        indication = rememberRipple(bounded = false, radius = RadioButtonSize)
    )

    // 오목한 테두리를 적용하는 Modifier
    // 이것은 외부 원 또는 선택되지 않은 상태를 나타냅니다
    val convexBorderModifier = Modifier.convexBorder(
        color = radioColor.value,
        shape = CircleShape,
        strokeWidth = RadioStrokeWidth,
        convexStyle = ConvexStyle(RadioShadowBlur, RadioShadowOffset, RadioGlareColor, RadioShadowColor)
    )

    // 라디오 버튼의 주요 컨테이너
    Box(
        modifier
            .minimumInteractiveComponentSize() // 최소 터치 대상 크기를 보장합니다
            .then(selectableModifier) // 선택 가능한 동작 추가
            .size(RadioButtonSize) // 라디오 버튼의 크기를 설정합니다
            .then(convexBorderModifier), // 오목한 테두리 적용
        contentAlignment = Alignment.Center
    ) {
        // 크기가 0 이상인 경우 내부 점을 조건부로 표시합니다
        if (dotSize.value > 0.dp) {
            Box(
                modifier = Modifier
                    .size(dotSize.value)
                    .background(radioColor.value, CircleShape)
                    // 두 개의 내부 그림자를 활용해 오목한 효과를 생성합니다
                    .innerShadow(CircleShape, RadioShadowColor, RadioShadowBlur, -RadioShadowOffset, -RadioShadowOffset)
                    .innerShadow(CircleShape, RadioGlareColor, RadioShadowBlur, RadioShadowOffset, RadioShadowOffset)
            )
        }
    }
}
```

축하해요🥳! 성공적으로 만들어냈어요👏. 전체 코드 구현은 GitHub Gist에서 확인할 수 있습니다🧑‍💻. 이제 어떻게 활용할 수 있는지 살펴보도록 하죠.

<div class="content-ad"></div>

## 광고

외국어를 배우고 있고 새로운 어휘에 어려움을 겪고 계신가요? 그렇다면, 여러분의 학습을 쉽고 편리하게 만들어 줄 이 어플을 꼭 확인해 보시기를 강력히 추천합니다!

<img src="/assets/img/2024-06-19-HowtoCreateaCute3DRadioButtoninJetpackCompose_1.png" />

# 실용적인 예시 💁

<div class="content-ad"></div>

알겠어요, 결제 옵션을 선택하기 위한 간단한 예제를 만들어 봅시다.

먼저, 필요한 옵션들을 나타내기 위해 enum 클래스 PaymentOption을 만들어주세요:

```js
enum class PaymentOption(val displayName: String) {
    CreditCard("신용 카드"),
    PayPal("PayPal"),
    BankTransfer("은행 송금"),
}
```

다음으로, 현재 선택된 옵션을 저장하기 위한 상태 변수가 필요합니다:

<div class="content-ad"></div>

```js
var selectedOption by remember { mutableStateOf(PaymentOption.CreditCard) }
```

마지막으로, 모든 가능한 옵션을 열에 넣어주세요:

```js
Column {
    PaymentOption.entries.forEach { option ->
        Row(verticalAlignment = Alignment.CenterVertically) {
            ConvexRadioButton(
                selected = (option == selectedOption),
                onClick = { selectedOption = option }
            )
            Text(option.displayName)
        }
    }
}
```

## 결과:

<div class="content-ad"></div>


![Image](https://miro.medium.com/v2/resize:fit:826/1*5IdpszpbEX4LHPABjORO-g.gif)

You might also like 👇

Thank you for reading this article! ❤️ I hope you’ve found it enjoyable and valuable. Feel free to show your appreciation by hitting the clap 👏 if you liked it and follow Kappdev for more exciting articles 😊

Happy coding!


<div class="content-ad"></div>


![image](/assets/img/2024-06-19-HowtoCreateaCute3DRadioButtoninJetpackCompose_2.png)
