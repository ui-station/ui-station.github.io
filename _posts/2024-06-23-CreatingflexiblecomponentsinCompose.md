---
title: "Compose를 통해 유연한 컴포넌트 만드는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_0.png"
date: 2024-06-23 01:16
ogImage:
  url: /assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_0.png
tag: Tech
originalTitle: "Creating flexible components in Compose"
link: "https://medium.com/proandroiddev/creating-flexible-components-in-compose-01c57f2502ba"
---

어떠한 개발에서도, 디자이너들이 또 다른 변형이 필요하다며 당신이 막 완성한 컴포넌트에 또 다른 변형을 요구하는 경우가 종종 있습니다. 이 새로운 컴포넌트가 이전 것들을 모두 망가뜨릴지도 확실하지 않은 상황이죠. 오늘은 Compose를 사용하여 유연한 컴포넌트를 만드는 원칙에 대해 이야기해보고, 앞으로를 생각하며 아름답게 구현해보려 합니다.

# 입력 데이터

가장 기초적인 시작부터 시작해봅시다. 여러분의 프로젝트에는 세 가지 다른 컴포넌트가 있다고 상상해보세요. 하지만 이 모든 것들은 한 가지 엔티티인 - 특정 타입의 연락처 사람을 대표합니다. 이번 예제에서, 우리는 셋의 변형을 가지게 될 것입니다:
![그림](/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_0.png)

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

![image1](/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_1.png)

![image2](/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_2.png)

# ContactFullNameView

우선 주어진 레이아웃의 구조를 이해해야 합니다.

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

<img src="/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_3.png" />

```js
Row {
  Image
  Text
}
```

가장 간단한 형태이며 내부에 컴포넌트가 있는 행(Row)일 뿐이라 특별한 점은 없습니다.

코드를 정리한 후 결과물은 다음과 같습니다:

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

@Preview 주석으로 이를 커버해보겠습니다. 결과를 확인하고 비교할 수 있습니다.

```js
@Preview
@Composable
private fun ContactFullNameV1ViewPreview() = ReusableComponentsTheme {
    ContactFullNameV1View(
        imageUrl = "https://images.stockcake.com/public/1/b/e/1be26278-b679-47a6-b17a-f3a66bc3db92_large/elegant-senior-portrait-stockcake.jpg",
        fullName = "Eleanor Pena",
        modifier = Modifier
            .fillMaxWidth()
            .background(MaterialTheme.colorScheme.surface),
    )
}
```

그러나 첫 미리보기 이후에 문제가 발생합니다. - 폰이나 에뮬레이터에서 미리보기를 실행하지 않고 결과를 보는 방법이 필요합니다. 또한 코드에 링크를 유지하면 심지어 @Preview에도 크게 멋지지 않습니다. 여기서 @Composable 함수를 만드는 접근 방식을 변경해야 합니다.

![이미지](/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_4.png)

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

원칙적으로 유연한 구성 요소를 만들려고 하면 작업 중인 모델에서 어느 정도 추상화해야 합니다.

```js
@Composable
fun ContactFullNameV2View(
    imagePainter: Painter,
    fullName: String,
    modifier: Modifier = Modifier,
)
```

Painter 클래스 유형에 대한 참조를 변경함으로써 세 가지 작업을 동시에 해결할 수 있습니다. @Preview에서 결과를 보는 것, 특정 링크를 통해 기기에서 이미지를 볼 수 있는 것(원하는 경우), 실제 @Composable에서 이미지를 로드하는 것에 대한 완전한 제어를 갖는 것입니다.

```js
@Preview
@Composable
fun ContactFullNameV2ViewPreview() = ReusableComponentsTheme {
    ContactFullNameV2View(
        imagePainter = when {
            LocalInspectionMode.current -> painterResource(id = R.drawable.eleanor_pena)
            else -> rememberAsyncImagePainter(model = "https://images.stockcake.com/public/1/b/e/1be26278-b679-47a6-b17a-f3a66bc3db92_large/elegant-senior-portrait-stockcake.jpg")
        },
        fullName = "Eleanor Pena",
        modifier = Modifier
            .fillMaxWidth()
            .background(MaterialTheme.colorScheme.surface),
    )
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

<img src="/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_5.png" />

# ContactActionsView

<img src="/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_6.png" />

우리가 새로 만들 컴포넌트의 대략적인 구조를 한 번 더 정의해 봅시다.

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
Row {
  Image
  Column {
    Text
    Text
  }
  IconButton
  IconButton
  IconButton
}
```

디자인과 비교한 후에는 약간 변경될 것이지만, 본질은 같을 것입니다. 이전 컴포넌트와 비교하여 새 필드를 추가하고 작업 버튼을 도입했습니다. 우리가 가진 지식을 활용하여 이 기능을 정확하게 설명합니다. 추가 변경 사항에는 여백 및 컨테이너 자체의 최소 크기도 포함됩니다.

```kotlin
@Composable
fun ContactActionsV1View(
    imagePainter: Painter,
    fullName: String,
    modifiedTime: String,
    modifier: Modifier = Modifier,
    onChatClicked: () -> Unit = {},
    onMainClicked: () -> Unit = {},
    onCallClicked: () -> Unit = {},
)
```

![Image](/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_7.png)

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

# ContactDetailsView

![ContactDetailsView](/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_8.png)

우리는 방금 이 버튼들을 추가했는데, 이제 제거해야 해요... 디자이너 분들! 컴포넌트의 구조를 다시 생각해 봐요.

```js
Row {
  Image
  Column {
    Text
    Text
    Row {
      Image
      Text
    }
  }
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

여기에도 특별한 것은 없습니다. 텍스트와 새로운 사진이 초기 버전에 추가된 간단한 레이아웃입니다. 차이점은 여백, 컨테이너의 최소 크기 및 연락처 사진의 정렬에 있습니다.

```kotlin
@Composable
fun ContactDetailsV1View(
    imagePainter: Painter,
    managerPainter: Painter,
    title: String,
    fullName: String,
    modifiedTime: String,
    modifier: Modifier = Modifier,
)
```

![이미지](/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_9.png)

# 프랑켄슈타인의 몬스터 만들기

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

마음에 떠오를 수 있는 첫 번째 아이디어는 우리 컴포넌트에 존재하는 모든 매개변수를 수집하고 모든 것을 할 수 있고 모든 것을 아는 하나의 매우 크고 강력한 @Composable을 만드는 것입니다. 그러나 이런 이리저리 섞인 것을 조립하는 동안 코드에 나타나는 여러 when 조건들을 발견할 수 있습니다. 이런 조건들이 대량으로 존재하는 것은 무언가 잘못되었음을 나타낼 수 있습니다. 여기서 단점은 특정 구성에서 우리 컴포넌트와 논리적으로 관련이 없는 많은 매개변수에 대한 접근이며, 또한 컴포넌트의 불안정성입니다. 새로운 요구사항이 들어오면 기존의 유연성으로는 원하는 기능을 추가하는 데 충분하지 않을 수 있습니다.

```kotlin
@Composable
fun ContactViewV1(
    imagePainter: Painter,
    managerPainter: Painter?,
    title: String?,
    fullName: String,
    modifiedTime: String?,
    modifier: Modifier = Modifier,
    onChatClicked: (() -> Unit)? = null,
    onMainClicked: (() -> Unit)? = null,
    onCallClicked: (() -> Unit)? = null,
) {
    Row(
        modifier = modifier
            .sizeIn(
                minHeight = when {
                    title.isNullOrBlank() && modifiedTime.isNullOrBlank() -> 56.dp
                    !title.isNullOrBlank() -> 72.dp
                    else -> 88.dp
                },
            )
            .padding(
                horizontal = 16.dp,
                vertical = when {
                    title.isNullOrBlank() && modifiedTime.isNullOrBlank() -> 8.dp
                    else -> 16.dp
                },
            ),
        horizontalArrangement = Arrangement.spacedBy(
            space = 16.dp,
        ),
    ) {

    ... other stuff ...
}
```

# 이론

높은 사용자 정의를 목표로 하는 새로운 컴포넌트를 구축할 때에는 정교하게 계획하여 무언가를 추가하거나 완전히 독특한 외관을 만들 때 고통을 최소화하는 것이 중요합니다.

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

컴포넌트 구성의 첫 번째 규칙은 @Composable의 첫 번째 선택적 매개변수로 Modifier를 사용하는 것입니다. 이 가이드라인은 우리가 컴포넌트를 외부에서 제어할 수 있도록 해주기 때문에 중요합니다. 두 번째 Modifier를 추가해야 한다면, 함수 구조를 재검토하는 것이 좋습니다. 각 함수는 하나의 Modifier만 포함해야 한다는 가이드라인이 있어 잠재적인 함수 디자인 문제를 나타냅니다. 따라서 해당 Modifier는 루트 @Composable에만 적용되어야 합니다.

컴포넌트는 UI 및 기능을 표현하는 다양한 메커니즘을 가질 수 있습니다. 필수 매개변수는 함수의 핵심 내용을 정의하고 기본값을 가지면 안 됩니다. 미리 정의된 값이 있는 선택적 매개변수는 컴포넌트에서 사용할 수도, 사용하지 않을 수도 있는 선택적 수정 사항으로 작용합니다.

선택적 값을 선언하는 방법을 고려하는 것도 중요합니다. Nullable은 기능이 사용되거나 완전히 생략될 수 있다는 것을 의미합니다. 빈 구현은 기능이 필수적이지만 빈 값을 또는 사용자 정의 로직을 사용할 수 있다는 것을 의미합니다. 기본값은 비-nullable이어야 하고 명확하게 이해돼야 합니다.

컴포넌트 스타일을 처리할 때, 일부 매개변수는 함수 본문 내에 남겨둘 수 있습니다. 단, 이 매개변수가 간단하고 명확하다면입니다. 그러나 보다 복잡한 컴포넌트의 경우 그룹화하는 것이 좋습니다.

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

여러 논리적 블록으로 구성된 컴포넌트의 경우, 이러한 블록들은 슬롯이라고 명명됩니다. 이 접근 방식은 표준 구현을 사용하거나 우리의 필요에 맞게 확장적으로 사용할 수 있도록 유연성을 제공합니다.

총괄적으로, 필수 항목을 다루었습니다. 나머지는 이후에 계속해서 다룰 것입니다.

# 아름다움을 창조하다

우선, 컴포넌트를 논리적으로 슬롯으로 나눕니다. 여기서 매우 명확합니다. 로고 슬롯, 정보 슬롯 및 액션 슬롯이 있습니다. 우리는 이를 기반으로 구조를 만들겠습니다.

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

```html
<img src="/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_10.png" />

항상 전체 이름과 연락처 사진이 있으므로 이러한 데이터는 @Composable 함수의 필수
매개변수로 사용할 수 있습니다. 사진의 경우 Painter를 전달하여 나중에 외부에서
사진 로드를 제어하고 @Preview에서 결과를 미리 볼 수 있게합니다. 또한 구성 요소의
구조를 대략적으로 스케치하고 모든 슬롯을 비워 둡시다. @Composable private fun
ContactView( leadingPainter: Painter, title: String, modifier: Modifier =
Modifier, leadingView: @Composable RowScope.() -> Unit = {}, content:
@Composable ColumnScope.() -> Unit = {}, trailingView: (@Composable RowScope.()
-> Unit)? = null, ) { Row( modifier = modifier .sizeIn( minHeight =
Dp.Unspecified, ) .padding( horizontal = 16.dp, vertical = Dp.Unspecified, ),
horizontalArrangement = Arrangement.spacedBy( space = 16.dp, ), ) {
leadingView.invoke(this) Column( modifier = Modifier
.align(Alignment.CenterVertically), ) { content.invoke(this) }
trailingView?.invoke(this) } } 첫 번째 슬롯을 살펴보고 있습니다. 여기서 사진은
항상 존재하며 정렬만 변경되므로 이러한 매개변수를 함수에 남겨두기로 하였습니다.
그 외의 모든 것은 일정합니다.
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

```kotlin
@Composable
fun RowScope.ContactLeadingView(
    imagePainter: Painter,
    headerAlignment: Alignment.Vertical,
) {
    Image(
        painter = imagePainter,
        contentDescription = "연락처 이미지",
        modifier = Modifier
            .requiredSize(40.dp)
            .clip(CircleShape)
            .align(
                alignment = headerAlignment,
            ),
    )
}
```

두 번째 슬롯에는 미래 호환성을 보장하기 위해 제목이라는 필수 매개변수가 하나 있습니다. 이렇게 하면 성과 이름 이외의 정보를 전달해도 구조가 변경되지 않습니다.

```kotlin
@Composable
fun ColumnScope.ContactContentView(
    title: String,
    header: String? = null,
    subtitle: String? = null,
    managerPainter: Painter? = null,
) {
    if (!header.isNullOrBlank()) { ... }

    Text(
        text = title,
        color = MaterialTheme.colorScheme.onSurface,
        style = MaterialTheme.typography.bodyMedium,
    )

    Row(
        modifier = Modifier,
        horizontalArrangement = Arrangement.spacedBy(
            space = 8.dp,
        ),
    ) {
        if (managerPainter != null) { ... }
        if (!subtitle.isNullOrBlank()) { ... }
    }
}
```

세 번째 슬롯은 세 가지 다른 콜백을 포함하는 작업을 포함할 것입니다. 여기서는 존재 여부를 제어할 수 있도록 각각을 nullable로 표시했습니다.

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
@Composable
fun RowScope.ContactActionsView(
    onChatClicked: (() -> Unit)? = null,
    onMainClicked: (() -> Unit)? = null,
    onCallClicked: (() -> Unit)? = null,
) {
    Row(
        modifier = Modifier
            .align(Alignment.CenterVertically),
    ) { ... }
}
```

모든 슬롯을 조립하고 이를 함수의 기초로 선언한 후 결과적으로 구조는 다음과 같아야 합니다:

```js
@Composable
private fun ContactView(
    leadingPainter: Painter,
    title: String,
    modifier: Modifier = Modifier,
    config: ContactViewConfig = ContactViewDefaults.config(),
    leadingView: @Composable RowScope.() -> Unit = {
        ContactLeadingView(
            imagePainter = leadingPainter,
            headerAlignment = config.headerAlignment,
        )
    },
    content: @Composable ColumnScope.() -> Unit = {
        ContactContentView(
            title = title,
        )
    },
    trailingView: (@Composable RowScope.() -> Unit)? = null,
)
```

이 구조에서 새로운 추가 요소는 컴포넌트의 다양한 설정을 제어하는 ContactViewConfig입니다.

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

테이블 태그를 마크다운 형식으로 변경해주세요.

```js
// 이렇게 하지 마세요
@Immutable
class ContactViewConfig internal constructor(
    internal val minHeight: Dp,
    internal val verticalPadding: Dp,
    internal val headerAlignment: Alignment.Vertical,
)

object ContactViewDefaults {

    @Composable
    fun config(
        minHeight: Dp = 56.dp,
        verticalPadding: Dp = 8.dp,
        headerAlignment: Alignment.Vertical = Alignment.CenterVertically,
    ): ContactViewConfig = ContactViewConfig(
        minHeight = minHeight,
        verticalPadding = verticalPadding,
        headerAlignment = headerAlignment,
    )
}
```

자, 이제 구성 가능하고 작동하는 @Preview가 있는 컴포넌트가 준비되었습니다.

다음에는 기본 기능을 확장하기 위해 기본 함수 위에 추가 컴포넌트를 작성할 수 있습니다. 선택 사항으로 간단한 타입 대신 도메인 클래스를 사용할 수 있습니다.

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
@Composable
fun FullNameContactView(
    imagePainter: Painter,
    fullName: String,
    modifier: Modifier = Modifier,
) {
    ContactView(
        leadingPainter = imagePainter,
        title = fullName,
        modifier = modifier,
    )
}
```

```kotlin
@Composable
fun ActionsContactView(
    imagePainter: Painter,
    fullName: String,
    modifiedTime: String,
    modifier: Modifier = Modifier,
    onChatClicked: (() -> Unit)? = null,
    onMainClicked: (() -> Unit)? = null,
    onCallClicked: (() -> Unit)? = null,
) {
    ContactView(
        leadingPainter = imagePainter,
        title = fullName,
        modifier = modifier,
        config = ContactViewDefaults.config(
            minHeight = 72.dp,
            verticalPadding = 16.dp,
        ),
        content = {
            ContactContentView(
                title = fullName,
                subtitle = modifiedTime,
            )
        },
        trailingView = {
            ContactActionsView(
                onChatClicked = onChatClicked,
                onMainClicked = onMainClicked,
                onCallClicked = onCallClicked,
            )
        },
    )
}
```

```kotlin
@Composable
fun DetailsContactView(
    imagePainter: Painter,
    managerPainter: Painter,
    title: String,
    fullName: String,
    modifiedTime: String,
    modifier: Modifier = Modifier,
) {
    ContactView(
        leadingPainter = imagePainter,
        title = fullName,
        modifier = modifier,
        config = ContactViewDefaults.config(
            minHeight = 88.dp,
            verticalPadding = 16.dp,
            headerAlignment = Alignment.Top,
        ),
        content = {
            ContactContentView(
                title = fullName,
                header = title,
                subtitle = modifiedTime,
                managerPainter = managerPainter,
            )
        },
    )
}
```

# 일부 결론

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

@Preview를 살펴보면 컴포넌트 사이에는 차이가 거의 없을 것입니다. 코드적으로도 거의 차이가 없을 것입니다. 유연한 기능과 설정을 가진 컴포넌트는 세 가지 다른 독립적인 함수만큼의 코드 라인을 차지할 것입니다. 이 글이 유연한 컴포넌트를 작성하는 방법에 대해 더 잘 이해하는 데 도움이 되었으면 좋겠습니다. 더 많은 개발을 위해 필요시 이 비디오와 코드를 살펴보시기를 권장합니다.

![Creating flexible components in Compose](/assets/img/2024-06-23-CreatingflexiblecomponentsinCompose_11.png)
