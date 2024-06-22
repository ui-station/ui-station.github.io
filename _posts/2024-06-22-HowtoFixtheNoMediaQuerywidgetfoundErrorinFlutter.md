---
title: "Flutter에서 No MediaQuery widget found 오류 해결 방법"
description: ""
coverImage: "/assets/img/2024-06-22-HowtoFixtheNoMediaQuerywidgetfoundErrorinFlutter_0.png"
date: 2024-06-22 23:27
ogImage: 
  url: /assets/img/2024-06-22-HowtoFixtheNoMediaQuerywidgetfoundErrorinFlutter_0.png
tag: Tech
originalTitle: "How to Fix the ‘No MediaQuery widget found’ Error in Flutter"
link: "https://medium.com/@kasata/how-to-fix-the-no-mediaquery-widget-found-error-in-flutter-705858adddd2"
---


플러터 개발자들은 개발 과정 중에 종종 'No MediaQuery widget found' 메시지를 마주하게 됩니다. 특히 초보자들에게는 이 오류가 혼란스러울 수 있습니다. 이 글에서는 이 오류의 원인을 살펴보고, 쉽게 이해할 수 있는 단계와 설명으로 해결하는 방법에 대해 알아보겠습니다.

## MediaQuery 위젯 이해하기

플러터의 MediaQuery 위젯은 화면의 크기, 디바이스의 픽셀 비율 및 기타 화면 관련 요소에 대한 정보를 제공합니다. 위젯은 이 정보를 활용하여 레이아웃 및 스타일링에 대한 결정을 내릴 수 있습니다. 그러나 MediaQuery.of(context)를 호출하여 크기 정보에 의존하는 위젯이 MediaQuery 내부에 배치되지 않았을 경우 'No MediaQuery widget found' 오류가 발생합니다.

## 오류를 유발하는 전형적인 시나리오들

<div class="content-ad"></div>

일반적으로 다음과 같은 시나리오 중 하나에서 오류가 발생합니다:

- MediaQuery에 의존하는 위젯을 사용하지만 MediaQuery가 제공되지 않는 컨텍스트에 배치하는 경우, 예를 들어 Padding, SafeArea 또는 AspectRatio와 같은 경우
- MediaQuery.of(context)에 의존하는 사용자 정의 위젯을 만들지만 Material App이나 WidgetsApp으로 감싸놓지 않은 경우

## 오류 수정 방법

## 1. MaterialApp으로 감싸기

<div class="content-ad"></div>

위젯 트리를 MaterialApp로 래핑하는 것이 가장 일반적인 해결책입니다. 예를 들어:

```js
void main() {
  runApp(MaterialApp(
    home: MyHomePage(),
  ));
}
```

이렇게 하면 모든 하위 위젯에서 MediaQuery를 사용할 수 있습니다.

## 2. Using WidgetsApp

<div class="content-ad"></div>

만약 Material 컴포넌트를 사용하지 않는다면 대신 WidgetsApp을 사용할 수도 있어요:

```js
void main() {
  runApp(WidgetsApp(
    home: MyHomePage(),
    color: Colors.blue,
  ));
}
```

WidgetsApp은 후손에 MediaQuery를 동일하게 제공해줍니다.

### 3. BuildContext 무결성 보장하기

<div class="content-ad"></div>

항상 MediaQuery.of(context)에서 사용하는 BuildContext가 MaterialApp 또는 WidgetsApp 아래에 있는 위젯에서 왔는지 확인해주세요.

```js
class MyHomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final mediaQueryData = MediaQuery.of(context);
    return Scaffold(
      appBar: AppBar(
        title: Text("My Home Page"),
      ),
      body: Center(
        child: Text("Screen Width: ${mediaQueryData.size.width}"),
      ),
    );
  }
}
```

## 결론

Flutter에서 'No MediaQuery widget found' 오류는 위젯 트리가 적절하게 MaterialApp 또는 WidgetsApp으로 래핑되어 있는지 확인함으로써 쉽게 해결할 수 있습니다. MediaQuery를 사용하는 맥락을 이해하여 이 흔한 함정을 피하는 것이 중요합니다. 위에서 설명한 단계를 따르면 이 오류를 만남 없이 Flutter 애플리케이션에서 MediaQuery를 효과적으로 사용할 수 있습니다.

<div class="content-ad"></div>

즐거운 코딩하세요!