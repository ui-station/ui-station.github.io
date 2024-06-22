---
title: "제트팩 코파 서체에서 Window Insets 이해하기"
description: ""
coverImage: "/assets/img/2024-05-18-UnderstandingWindowInsetsinJetpackCompose_0.png"
date: 2024-05-18 17:07
ogImage: 
  url: /assets/img/2024-05-18-UnderstandingWindowInsetsinJetpackCompose_0.png
tag: Tech
originalTitle: "Understanding Window Insets in Jetpack Compose"
link: "https://medium.com/proandroiddev/understanding-window-insets-in-jetpack-compose-46245b9ceffa"
---



![Image](/assets/img/2024-05-18-UnderstandingWindowInsetsinJetpackCompose_0.png)

# 인셋이란 무엇인가요?

인셋은 상태 바, 네비게이션 바, 디스플레이 컷아웃(노치 또는 핀홀로 자주 불림), IME 키보드와 같은 시스템 UI 요소로 인해 화면에서 완전히 사용할 수 없는 영역을 가리킵니다.

기본적으로, 우리 앱의 UI는 상태 바와 네비게이션 바와 같은 시스템 UI 내에 레이아웃되도록 제한됩니다. 이는 시스템 UI 요소가 앱의 콘텐츠를 가리지 않도록 보장합니다.


<div class="content-ad"></div>

그렇다면 왜 우리가 모습들에 대해 걱정해야 할까요?

현대 스마트폰이 엣지 투 엣지 화면과 다양한 화면 비율을 맞이하면서, 인셋(insets) 관리가 중요도를 더욱 높이고 있습니다.

본질적으로, 우리는 시스템 제어를 받는 인셋 패러다임에서 개발자들이 엣지 투 엣지 디스플레이를 활성화하거나 시스템 UI 요소 뒤로 그림을 그리며 인셋 관리를 직접 제어하는 방식으로 전환 중입니다!

# 시작하는 방법은?

<div class="content-ad"></div>

## 초기 설정

우리 앱이 콘텐츠를 그릴 영역을 완전히 제어하도록 설정해야 합니다. 이 설정을 하지 않으면 앱이 시스템 UI 뒤에 검정색이나 단색을 그리거나 소프트웨어 키보드와 동기화되지 않을 수 있습니다.

- Activity onCreate에서 enableEdgeToEdge 함수를 호출합니다.

이 호출은 우리 앱에 시스템 UI 뒤에 표시하도록 요청합니다. 그런 다음 앱은 해당 간격이 UI를 조정하는 데 어떻게 사용되는지를 제어합니다.

<div class="content-ad"></div>

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    enableEdgeToEdge()

    setContent {
        // 앱 내용을 여기에 추가합니다
    }
}
```

- Activity AndroidManifest.xml에서 android:windowSoftInputMode="adjustResize"를 설정하세요.

이 설정을 추가하면 앱이 소프트웨어 IME의 크기를 받아들일 수 있으며, 이를 사용하여 IME가 앱에서 나타나고 사라질 때 내용을 적절하게 패딩 및 정렬할 수 있습니다.

```xml
<activity
  android:name=".ui.MainActivity"
  android:label="@string/app_name"
  android:windowSoftInputMode="adjustResize"
  android:theme="@style/Theme.MyApplication"
  android:exported="true">
```

<div class="content-ad"></div>

우리 앱이 현재 어떻게 보이는지 아래 코드를 통해 살펴봅시다:

```js
setContent {
    Box(
        modifier = Modifier
             .fillMaxSize()
             .background(color = Color.DarkGray)
       )
}
```

![이미지](/assets/img/2024-05-18-UnderstandingWindowInsetsinJetpackCompose_1.png)

컬러로 채워진 Box가 화면 전체를 채우고 시스템 바(상단 상태 바 및 하단 네비게이션 바) 뒤에 그려짐을 확인할 수 있습니다. 이것은 우리의 코드가 이제 시스템 UI 뒤에 그리는 능력을 갖추고 있고 이러한 영역을 스스로 제어할 수 있다는 것을 의미합니다!

<div class="content-ad"></div>

## 여백 제어하기

시스템 UI 뒤에 표시되고 모든 여백을 수동으로 처리하는 상태인 경우, Compose API를 사용하여 앱의 상호 작용 가능한 콘텐츠가 시스템 UI와 겹치지 않도록 할 수 있습니다.

이러한 API는 또한 앱의 레이아웃을 여백 변경과 동기화시킵니다.

조정할 수 있는 Composable 레이아웃을 조정하기 위해 Inset 유형을 사용하는 주요 방법은 패딩 수정자와 여백 크기 수정자가 있습니다.

<div class="content-ad"></div>

패딩 수정자

창 인셋을 패딩으로 적용하는 방법은 Modifier.windowInsetsPadding(windowInsets: WindowInsets)를 사용할 수 있습니다. 이는 Modifier.padding와 매우 유사하게 작동합니다.

WindowInsets.systemBars, WindowInsets.statusBars, WindowInsets.navigationBars 등과 같은 몇 가지 내장된 창 인셋이 있습니다. 이를 사용하여 원하는 패딩을 제공할 수 있습니다.

예를 들어 이전 코드에서 상태 바와 네비게이션 바를 제외하고 회색 상자를 그리고 싶다면, 다음과 같이 할 수 있습니다:

<div class="content-ad"></div>

```kotlin
setContent {
    Box(
        modifier = Modifier
            .fillMaxSize()
            .background(color = Color.LightGray)
            .windowInsetsPadding(WindowInsets.systemBars)
    ) {
        Box(
            modifier = Modifier
                .fillMaxSize()
                .background(color = Color.DarkGray)
        )
    }
}
```

Modifier windowInsetsPadding(WindowInsets.systemBars)은 상단 상태 표시줄과 하단 네비게이션 바에 패딩을 추가하며, 이들은 이해를 돕기 위해 LightGray로 칠해졌습니다. 이로써 우리 앱은 다음과 같이 보일 것입니다:

![이미지](/assets/img/2024-05-18-UnderstandingWindowInsetsinJetpackCompose_2.png)

우리는 또한 windowInsetsPadding(WindowInsets.statusBars) 또는 windowInsetsPadding(WindowInsets.navigationBars)를 사용하여 이러한 Insets를 별도로 제어할 수 있습니다.

<div class="content-ad"></div>

가장 일반적인 종류의 Insets에 대한 많은 내장 메소드도 있습니다. 예를 들어:

- safeDrawingPadding(), windowInsetsPadding(WindowInsets.safeDrawing)에 해당하는 메소드
- safeContentPadding(), windowInsetsPadding(WindowInsets.safeContent)에 해당하는 메소드
- safeGesturesPadding(), windowInsetsPadding(WindowInsets.safeGestures)에 해당하는 메소드

Inset 크기 조정기

이러한 조정기는 컴포넌트의 크기를 insets의 정확한 크기로 설정하는 데 도움을 줍니다. Spacer 크기를 설정하는 데 유용합니다.Inset 크기를 차지하면서 화면을 만들 때 유용합니다.

<div class="content-ad"></div>

예를 들어, Inset 크기 수정자를 사용하여 상태 표시줄과 내비게이션 바에 패딩을 제공하도록 마지막 코드를 다음과 같이 작성할 수 있습니다. 이렇게 하면 동일한 결과가 생성됩니다.

 js
setContent {
    Column {
        Spacer(
            modifier = Modifier
                .fillMaxWidth()
                .background(color = Color.LightGray)
                .windowInsetsTopHeight(WindowInsets.statusBars)
        )
        Box(
            modifier = Modifier
                .fillMaxSize()
                .background(color = Color.DarkGray)
                .weight(1f)
        )
        Spacer(
            modifier = Modifier
                .fillMaxWidth()
                .background(color = Color.LightGray)
                .windowInsetsBottomHeight(WindowInsets.navigationBars)
        )
    }
}


이전과 같이 DarkGrey Box에 Insets 패딩을 추가하는 대신, 상태 표시줄과 내비게이션 바의 정확한 크기를 차지하는 LightGray Spacer를 추가했습니다.

## 키보드 IME와 함께 구성 요소 패딩 크기 조정

<div class="content-ad"></div>

가끔 키보드 IME가 열려 있는지 닫혀 있는지에 따라 UI 컴포넌트에 동적 패딩을 적용하고 싶을 때가 있습니다. 좋은 사용 사례는 목록 맨 아래에 입력 필드를 추가하는 경우입니다.

다음과 같은 코드를 고려해보세요:

```js
setContent {
    Column(
        modifier = Modifier.fillMaxSize().systemBarsPadding()
    ) {
        LazyColumn(
            modifier = Modifier.weight(1f),
            reverseLayout = true
        ) {
            items(100) { index ->
                Text(text = "Item $index", modifier = Modifier.padding(16.dp).fillMaxWidth())
            }
        }

        var textFieldValue by remember { mutableStateOf(TextFieldValue()) }

        TextField(
            modifier = Modifier.fillMaxWidth(),
            value = textFieldValue,
            onValueChange = { textFieldValue = it },
            placeholder = {
                Text(text = "Type something here")
            }
        )
    }
}
```

만약 TextField의 패딩을 처리하지 않는다면:

<div class="content-ad"></div>


![Keyboard Opening](https://miro.medium.com/v2/resize:fit:600/1*8HIx9O4S3Lk3mpUJRqI21g.gif)

키보드가 열릴 때 텍스트 필드가 화면 하단에 고정되어 있어서 사용자 경험이 그리 좋지 않음을 볼 수 있습니다.

텍스트 필드 패딩을 조정한 후:

위의 텍스트 필드에는 단순히 `imePadding()` 수정자를 추가하면 됩니다.


<div class="content-ad"></div>

이제 코드는 다음과 같이 보입니다:

```js
// 이전 코드와 동일

TextField(
    modifier = Modifier.fillMaxWidth().imePadding(), // IME 패딩 추가
    value = textFieldValue,
    onValueChange = { textFieldValue = it },
    placeholder = {
        Text(text = "여기에 무언가를 입력하세요")
    }
)
```

이제 TextField 패딩이 변경되며 또한 IME의 상태 변화와 함께 애니메이션되어 입력 필드가 키보드와 함께 이동하는 효과를 만듭니다:

<img src="https://miro.medium.com/v2/resize:fit:600/1*IQIdBM5ovhwMcHmJ0qCOZQ.gif" />

<div class="content-ad"></div>

## 스크롤할 때 키보드 IME를 애니메이션화하자:

스크롤 컨테이너에 추가하는 실험적 API 수정자 imeNestedScroll()가 있습니다. 이 수정자를 스크롤 컨테이너에 추가하면 컨테이너의 맨 아랫부분으로 스크롤할 때 키보드가 애니메이션으로 열립니다.

위 코드를 수정해서 LazyColumn에 이 수정자를 추가하면 다음과 같습니다:

```js
// 위 코드와 같음

LazyColumn(
    modifier = Modifier.weight(1f).imeNestedScroll(), // 수정자 추가
    reverseLayout = true
) {

// 이전과 같은 코드
```

<div class="content-ad"></div>

아래와 같이 경험을 제공할 예정입니다:

![image](https://miro.medium.com/v2/resize:fit:600/1*dciSCS0k29yCh1Z4lAM85A.gif)

# 패딩 소비

이제 이 시점에서 우리 마음 속에 몇 가지 질문이 떠오를 수 있습니다.

<div class="content-ad"></div>

어떤 내부 삽입 패딩 수정자(예: safeDrawingPadding())를 고려할 때, Composable 계층 구조에서 이를 한 번만 적용해야 할까요? 한 번 이상 적용하면 어떻게 되나요? 부모에 적용한 다음에 하위 자식에 다시 적용하면 패딩이 두 번 추가될까요?

내장된 삽입 패딩 수정자는 자동으로 적용된 인셋의 일부를 패딩으로 사용합니다. 구성 트리를 깊게 들어가면, 중첩된 삽입 패딩 수정자와 자식 Composable에 적용된 인셋 크기 조절 수정자는 외부 수정자에 의해 이미 소비(또는 적용 또는 고려)된 인셋의 일부를 알고 있어 해당 인셋을 다시 적용하지 않고 건너뛰어 중복 공간을 피합니다.

이를 이해하기 위해 예시를 살펴봅시다:

```js
setContent {
    var textFieldValue by remember { mutableStateOf(TextFieldValue()) }
    LazyColumn(
        Modifier.windowInsetsPadding(WindowInsets.statusBars).imePadding()
    ) {
        items(count = 30) {
            Text(
                modifier = Modifier.fillMaxWidth().padding(16.dp),
                text = "Item $it"
            )
        }
        item {
            TextField(
                modifier = Modifier.fillMaxWidth().height(56.dp),
                value = textFieldValue,
                onValueChange = { textFieldValue = it },
                placeholder = { Text(text = "Type something here") }
            )
        }
        item {
            Spacer(
                Modifier.windowInsetsBottomHeight(
                    WindowInsets.systemBars
                )
            )
        }
    }
}
```

<div class="content-ad"></div>

이 코드는 LazyColumn에 긴 항목 목록을 표시합니다. 목록의 맨 아래에는 사용자 입력을 위한 TextField가 있고, 끝에는 창 간격 크기 수정자를 사용하여 하단 시스템 내비게이션 바에 공간을 제공하는 Spacer가 있습니다. 또한 LazyColumn에 imePadding이 적용되어 있습니다.

여기서 키보드가 닫힌 경우 IME의 높이가 없어 imePadding() 수정자가 패딩을 적용하지 않습니다. 따라서 인셋이 사용되지 않고, 이 때 Spacer의 높이는 하단 시스템 바의 크기가 됩니다. 키보드가 열리면 IME 인셋이 IME의 크기에 맞도록 애니메이션화되며 imePadding() 수정자가 LazyColumn에 하단 패딩을 적용하기 시작합니다. 결과적으로 해당 인셋량도 "소비"하기 시작합니다.

이제 이 시점에서 imePadding() 수정자에 의해 하단 시스템 바의 일부 여백이 이미 적용되어 Spacer의 높이가 감소하기 시작합니다. 어느 시점에서는 IME 패딩 크기가 하단 시스템 바의 크기를 초과하여 Spacer의 높이가 제로가 됩니다. 키보드가 닫히면 동일한 메커니즘이 반대로 발생합니다.

이 동작은 모든 windowInsetsPadding 수정자 간의 통신을 통해 달성됩니다.

<div class="content-ad"></div>

위의 코드가 작동하는 방식을 확인해보세요:

![Code](https://miro.medium.com/v2/resize:fit:600/1*8ZLXFWRuKcZXvPtRmjxPJQ.gif)

또 다른 예시를 살펴보겠습니다:

이 예시에서는 Modifier.consumedWindowInsets(insets: WindowInsets)를 살펴보겠습니다.

<div class="content-ad"></div>

이 수정자는 Modifier.windowInsetsPadding과 같은 방식으로 패딩을 소비하기 위해 사용됩니다. 그러나 소비된 인셋을 패딩으로 적용하지 않습니다.

다른 수정자인 Modifier.consumedWindowInsets(paddingValues: PaddingValues)은 임의의 PaddingValues를 소비합니다.

인셋 패딩 수정자가 아닌 일반 Modifier.padding 또는 고정 높이 간격과 같은 다른 메커니즘으로 패딩 또는 간격을 제공할 때 자식에게 알리는 데 유용합니다.

다음 코드를 참고하세요:

<div class="content-ad"></div>

```kotlin
setContent {
    Scaffold { innerPadding ->
        // innerPadding에는 사용하고 적용할 인셋 정보가 포함되어 있습니다
        Box(
            modifier = Modifier
                .fillMaxSize()
                .background(color = Color.LightGray)
                .padding(innerPadding)
        ) {
            Box(
                modifier = Modifier
                    .fillMaxSize()
                    .background(color = Color.Red)
                    .windowInsetsPadding(WindowInsets.safeDrawing)
            ) {
                Box(
                    modifier = Modifier
                        .fillMaxSize()
                        .background(color = Color.DarkGray)
                )
            }
        }
    }
}
```

이 코드의 결과는 다음과 같이 보일 것입니다:

<img src="/assets/img/2024-05-18-UnderstandingWindowInsetsinJetpackCompose_3.png" />

우리는 이 결과물에 문제가 있는 것을 볼 수 있습니다. Scaffold 람다에서 얻은 innerPadding을 바깥 Box에 패딩으로 적용했지만, 내부 Box의 windowInsetsPadding(WindowInsets.safeDrawing)은 중복 패딩(빨간색으로 나타남)을 생성합니다. 즉, 어떤 이유로 인해 여기에서 인셋 소비가 발생하지 않았다는 의미입니다.

<div class="content-ad"></div>

기본적으로 Scaffold는 우리가 사용하고 활용할 수 있는 insets를 매개변수 paddingValues로 제공합니다. Scaffold는 콘텐츠에 insets를 적용하지 않으며, 이 책임은 우리에게 있습니다.

따라서 우리가 이중 패딩을 피하고 싶다면, consumeWindowInsets(innerPadding) 수정자를 사용하여 패딩을 직접 소비해야 합니다.

다음은 업데이트된 코드를 고려해보세요:

```js
setContent {
    Scaffold { innerPadding ->
        // innerPadding은 사용하고 적용할 인셋 정보를 포함합니다
        Box(
            modifier = Modifier
                .fillMaxSize()
                .background(color = Color.LightGray)
                .padding(innerPadding)
                // 이 인셋을 소비하여, 아래 계층에서 safeDrawing을 사용할 때 다시 적용되지 않도록 합니다
                .consumeWindowInsets(innerPadding)
        ) {
              // 나머지 코드
          }
        }
    }
}
```

<div class="content-ad"></div>

위의 내용을 한국어로 다음과 같이 번역하면 됩니다:

이렇게 작성하면 됩니다:

<img src="/assets/img/2024-05-18-UnderstandingWindowInsetsinJetpackCompose_4.png" />

그러므로 innerPadding이 outer Box에 의해 소비되면 inner Box의 windowInsetsPadding(WindowInsets.safeDrawing)에 중복 패딩이 적용되지 않습니다.

이로써 이 기사는 마칩니다!

<div class="content-ad"></div>

이 글을 통해 많은 개발자들이 Insets의 필요성에 대해 이해하고, 효과적으로 활용하는 방법을 배울 것이라 확신합니다.

모든 예시는 다음 저장소에서 확인할 수 있습니다: [https://github.com/pushpalroy/ComposeInsetsPlayground](https://github.com/pushpalroy/ComposeInsetsPlayground)

팔로우 하기: @pushpalroy