---
title: "Jetpack Compose 팝업  마스터하기"
description: ""
coverImage: "/assets/img/2024-05-18-JetpackComposePopupMasterIt_0.png"
date: 2024-05-18 15:27
ogImage: 
  url: /assets/img/2024-05-18-JetpackComposePopupMasterIt_0.png
tag: Tech
originalTitle: "Jetpack Compose Popup — Master It!"
link: "https://medium.com/mobile-app-development-publication/jetpack-compose-popup-master-it-98accb23da36"
---


## 안드로이드 개발 배우기

![이미지](/assets/img/2024-05-18-JetpackComposePopupMasterIt_0.png)

저희가 Jetpack Compose 뷰를 프로그래밍할 때, 단순한 Jetpack Compose 뷰 프로그래밍으로 어떤 제약이 있다는 것을 깨닫지 못할 수 있습니다.

해당 제약은 다음과 같습니다. 어떤 Compose 뷰에서도, 부모 뷰에 비해 큰 뷰나 뷰 외부에서 다른 뷰를 작성하는 것이 불가능합니다 (아래 다이어그램에 표시됨).

<div class="content-ad"></div>

아래는 새로운 코드 블록을 Markdown 형식으로 바꾼 예시입니다.

```markdown
<img src="/assets/img/2024-05-18-JetpackComposePopupMasterIt_1.png" />

자, 다행히도 Jetpack Compose는 이 목적으로 활용할 수 있는 두 개의 Compose 구성 요소를 제공해줍니다.

- Dialog
- Popup

이 글의 주제는 Popup에 대해 모든 것을 공유하고, 그림을 통해 사용자가 쉽게 익힐 수 있도록 합니다. 또 기타 재미있는 세부사항이나 화면 보안, 제스처 사용 방지 등에 대해 알 수도 있습니다.
```

<div class="content-ad"></div>

한번 확인해 보세요.

# 팝업 기본

## 팝업 표시

기본적으로, 다음과 같이 팝업을 표시할 수 있습니다.

<div class="content-ad"></div>

```js
Popup { 
    // 팝업에서 표시할 합성 콘텐츠
}
```

그러나 우리가 그냥 호출하면 팝업이 항상 표시됩니다.

버튼 클릭시에만 표시되도록하려면 몇 가지 제어 로직을 둘러싸야합니다.

```js
var popupControl by remember { mutableStateOf(false) }
TextButton(onClick = { popupControl = true }) {
    Text("일반 팝업 열기")
}

if (popupControl) {
    Popup {
      // 팝업에서 표시할 합성 콘텐츠
    }
}
```

<div class="content-ad"></div>

기본적으로 팝업은 아래와 같이 부모 컨테이너의 상단-왼쪽에 맞춰 표시됩니다.

![Popup Default Alignment](https://miro.medium.com/v2/resize:fit:470/1*jpGDSCT0yj7bk1I2Bf-tPA.gif)

## 팝업 정렬

팝업을 재정렬하려면 제공할 수 있는 정렬 매개변수가 있습니다.

<div class="content-ad"></div>

```js
Popup(
    정렬 = Alignment.Center,
)
```

설정 가능한 값으로는 Center, CenterStart, CenterEnd, TopCenter, TopStart (기본값), TopEnd, BottomCenter, BottomStart, BottomEnd이 있습니다.

물론 항상 이상적인 상황은 아닐 수 있습니다. 따라서 사용자가 제공할 수 있는 Offset 값도 몇 가지 있습니다.

```js
Popup(
    정렬 = Alignment.CenterStart,
    offset = IntOffset(0, 700),
)
```

<div class="content-ad"></div>

## Popup onDismissRequest

기본적으로 팝업 콘텐츠 외부를 누르면 팝업이 닫힙니다. 그러나 다음을 따르면 결코 닫히지 않습니다.

```js
var popupControl by remember { mutableStateOf(false) }
TextButton(onClick = { popupControl = true }) {
    Text("Open normal popup")
}

if (popupControl) {
    Popup(
        alignment = Alignment.CenterStart,
        offset = IntOffset(0, 700),
    ) {
      // 팝업에 표시할 컴포저블 콘텐츠
    }
}
```

팝업이 닫히지 않는 것은 팝업이 사라지지 않았기 때문이 아닙니다. 실제로 닫혔지만, 팝업 컨트롤이 여전히 true 상태이며, recomposition되면 Popup이 다시 나타납니다 (닫혔다고 느끼지 않게 합니다).

<div class="content-ad"></div>

해당 문제를 해결하기 위해 팝업이 닫힐 때 popupControl을 false로 설정하도록 팝업에 알려주어야 합니다. 아래와 같이 onDismissRequest 매개변수를 사용하여 그 작업을 수행할 수 있습니다.

```js
var popupControl by remember { mutableStateOf(false) }
TextButton(onClick = { popupControl = true }) {
    Text("일반 팝업 열기")
}

if (popupControl) {
    Popup(
        alignment = Alignment.CenterStart,
        offset = IntOffset(0, 700),
        onDismissRequest = { popupControl = false },
    ) {
      // 팝업에서 표시할 컴포저블 내용
    }
}
```

# 팝업 속성

팝업의 기본을 배웠으니, 좀 더 고급 기능 중 하나인 PopupProerties 매개변수에 대해 살펴보겠습니다.

<div class="content-ad"></div>

```js
class PopupProperties(
    focusable: Boolean = false,
    dismissOnBackPress: Boolean = true,
    dismissOnClickOutside: Boolean = true,
    securePolicy: SecureFlagPolicy = SecureFlagPolicy.Inherit,
    excludeFromSystemGesture: Boolean = true,
    clippingEnabled: Boolean = true,
    @get:ExperimentalComposeUiApi //현재시점에서
    val usePlatformDefaultWidth: Boolean = false
)
```

상기 내용이 기본값으로 제공되지만 우리는 이를 재정의할 수 있습니다. 아래에서 각각 설명하겠습니다.

## Focusable 및 DismissOnBackPress

focusable이 true일 때 팝업은 IME 이벤트와 키 입력을 수신하고, 뒤로 가기 버튼이 눌렸을 때와 같이 역할을 합니다. 그러나 팝업 뒤에 있는 것에 대한 터치가 비활성화됩니다.```

<div class="content-ad"></div>

```markdown
![Popup](/assets/img/2024-05-18-JetpackComposePopupMasterIt_2.png)

If we couple `focusable = true` with `dismissOnBackPress` is true, then the Popup will be dismissed if the user clicks the back button (the older Android phone).

## DismissOnClickOutside

As mentioned previously, by default, when the user clicks outside the Popup area, it will be dismissed
``` 

<div class="content-ad"></div>

'테이블' 태그를 Markdown 형식으로 변경해주세요.

<div class="content-ad"></div>

```js
enum class SecureFlagPolicy {
    Inherit, // 부모를 따름, 기본값입니다
    SecureOn,
    SecureOff
}
```

다음 그림을 보면 이제 모두 명확해졌습니다.

![Jetpack Compose Popup Master It](/assets/img/2024-05-18-JetpackComposePopupMasterIt_3.png)

## 시스템 제스처에서 제외하기
```

<div class="content-ad"></div>

안드로이드 Q부터는 Android 하드웨어 백 버튼 대신에 사용자가 왼쪽으로 스와이프하여 뒤로 이동할 수 있습니다.

하지만, 이것은 사이드바가 넓은 경우와 같이 앱 제스처와 겹칠 수 있습니다. 좋은 설명은 여기서 (가장자리에서 가장자리 밝기 슬라이더 섹션에서) 공유되어 있습니다.

상황을 모방하기 위해, 제가 큰 팝업을 만들고 이를 켜고 끕니다.

기본값으로 ON 상태일 때, 아래 설정을 한다면, 스와이프를 하려고 할 것입니다.

<div class="content-ad"></div>

```markdown
PopupProperties(
    focusable = true,
    dismissOnBackPress = false,
    dismissOnClickOutside = false,
    excludeFromSystemGesture = true,
)
```

아래 GIF에서 왼쪽에서 스와이프해도 아무 일도 일어나지 않습니다.

<img src="https://miro.medium.com/v2/resize:fit:470/1*8keelwDUflGe_U2LT9_7-g.gif" />

하지만, OFF로 변경해보면
```

<div class="content-ad"></div>

```javascript
PopupProperties(
    focusable = true,
    dismissOnBackPress = false,
    dismissOnClickOutside = false,
    excludeFromSystemGesture = false,
)
```

이제 왼쪽에서 스와이프를 할 때 “뒤로 가기”가 트리거되었습니다 (‘`’ 기호로 표시됨)

![이미지](https://miro.medium.com/v2/resize:fit:470/1*lIPOkC6ucBMDt0yUOWWAPg.gif)

## ClippingEnabled
```

<div class="content-ad"></div>

기본적으로는 그렇습니다. 이것은 팝업을 앱 장치 화면 바깥으로 실수로 배치할 수 없게 한다는 것을 의미합니다. Offset 값이 너무 크거나 팝업이 너무 커지면 아래에 나와 있는 것처럼 팝업이 축소되어 앱 장치에 고정됩니다.

그러나 화면을 벗어나도 상관없다면 이 값을 false로 설정할 수 있습니다.

<img src="/assets/img/2024-05-18-JetpackComposePopupMasterIt_4.png" />

## UsePlatformDefaultWidth

<div class="content-ad"></div>

작성 시점에는 이것이 여전히 실험 중입니다. 잘라내기와 거의 동일하지만 다음과 같은 차이가 있습니다.

- 너비만 잘라냅니다 (높이는 잘라내지 않음)
- 위치를 재조정하지 않습니다 (X 좌표).

아래는 Clipping과 함께 사용했을 때의 결과입니다.

![Image](/assets/img/2024-05-18-JetpackComposePopupMasterIt_5.png)

<div class="content-ad"></div>

# 위치 조정 사용자 정의

PopupProperty에 대해 배웠습니다. 또한 일반 Popup을 알고 있으며, Parent Composable View와 정렬하여 위치를 조정할 수 있습니다.

그러나 Popup이 Parent Composable View에 의존하지 않도록하려면 어떻게해야 합니까?

좋은 소식은 PopupPositionProvider를 제공할 수 있는 또 다른 Popup API가 있다는 것입니다.

<div class="content-ad"></div>

```kotlin
@Composable
fun Popup(
    popupPositionProvider: PopupPositionProvider,
    onDismissRequest: (() -> Unit)? = null,
    properties: PopupProperties = PopupProperties(),
    content: @Composable () -> Unit
) {
```

## PopupPositionProvider

이 인터페이스는 사용자가 원하는 위치를 계산하는 데 사용됩니다.

```kotlin
@Immutable
interface PopupPositionProvider {
    fun calculatePosition(
        anchorBounds: IntRect,
        windowSize: IntSize,
        layoutDirection: LayoutDirection,
        popupContentSize: IntSize
    ): IntOffset
}
```

<div class="content-ad"></div>

제 경우에는 항상 창의 중앙에 위치한 사용자 정의 팝업 위치를 원합니다.

그래서 아래처럼 Position을 쉽게 만들 수 있어요. 여기에는 창의 중앙 위치에 대한 상대적인 OffSet도 있습니다.

```js
class WindowCenterOffsetPositionProvider(
    private val x: Int = 0,
    private val y: Int = 0
) : PopupPositionProvider {
    override fun calculatePosition(
        anchorBounds: IntRect,
        windowSize: IntSize,
        layoutDirection: LayoutDirection,
        popupContentSize: IntSize
    ): IntOffset {
        return IntOffset(
            (windowSize.width - popupContentSize.width) / 2 + x,
            (windowSize.height - popupContentSize.height) / 2 + y
        )
    }
}
```

이제 아래와 같이 Popup에 할당하기만 하면 됩니다. 그러면 그에 맞게 동작합니다.

<div class="content-ad"></div>

```kotlin
var popupControl by remember { mutableStateOf(false) }
TextButton(onClick = { popupControl = true }) {
    Text("일반 팝업 열기")
}

if (popupControl) {
    Popup(
        popupPositionProvider = 
           WindowCenterOffsetPositionProvider(),
        onDismissRequest = { popupControl = false },
    ) {
      // 팝업에 표시할 콘텐츠
    }
}
```

<img src="https://miro.medium.com/v2/resize:fit:476/1*Gd9kg-HENkrcawasnU1IZw.gif" />

여기서 코드 디자인을 얻을 수 있습니다.

# TL; DR;```

<div class="content-ad"></div>

팝업은 호출자의 Compose 기능이 호출자의 Compose 뷰 경계 밖의 내용을 표시할 수 있게 하는 Jetpack Compose 구성 요소입니다.

삭제되는 방법, 레이아웃, 호출자 Compose 뷰와의 위치 맞춤과 같이 사용자 정의할 수 있는 여러 팝업 속성이 있습니다.

창과 맞춤을 맞출 수 있게 하는 Positioning 같은 위치 지정도 사용자 정의할 수 있습니다.

예를 들어 사용 가능한 여러 제한 사항이 있습니다.

<div class="content-ad"></div>

- 표시 여부를 결정하는 외부 변수 (팝업 외부의 변수)에 의해 외부에서 제어되어야 합니다.
- Composable 함수이므로 onClick 내에서 호출하거나 큐에 넣어 시기적절한 닫기를 할 수 없습니다. (예: 토스트로 만들어 호출), 코루틴 스코프에서.

위의 제한사항을 고려하여 Popup에서 영향을 받은 사용자 정의 AbstractComposeView를 시도해보려고 합니다. 발견한 내용은 아래와 같습니다.