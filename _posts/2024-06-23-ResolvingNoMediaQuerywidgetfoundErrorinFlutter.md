---
title: "Flutter에서 No MediaQuery widget found 오류 해결하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-ResolvingNoMediaQuerywidgetfoundErrorinFlutter_0.png"
date: 2024-06-23 21:40
ogImage:
  url: /assets/img/2024-06-23-ResolvingNoMediaQuerywidgetfoundErrorinFlutter_0.png
tag: Tech
originalTitle: "Resolving No MediaQuery widget found Error in Flutter"
link: "https://medium.com/@kasata/resolving-no-mediaquery-widget-found-error-in-flutter-0f34fc14f4f6"
---

플러터를 사용해 오셨다면 언젠가 No MediaQuery 위젯을 찾을 수 없다는 오류와 마주쳐 보셨을 겁니다. 이 오류는 답답할 수 있지만, 다행히도 보통 쉽게 해결할 수 있어요. 이 글에서는 이 오류의 원인을 살펴보고 해결하는 방법을 알아볼 거에요.

## 오류 이해하기

No MediaQuery 위젯을 찾을 수 없다는 오류는 주로 MediaQuery 클래스를 사용하여 화면 크기, 방향 또는 기타 미디어 관련 정보를 가져오려고 시도할 때 발생합니다. MediaQuery 클래스는 미디어 쿼리 데이터에 액세스해야 하는 위젯들보다 위젯 트리 계층구조에서 MediaQuery 위젯이 존재해야 합니다.

## 일반적인 원인

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

이 에러의 일반적인 원인 중 하나는 MaterialApp, CupertinoApp 또는 WidgetsApp으로 래핑되지 않은 위젯에서 MediaQuery.of(context)를 사용하려고 시도하는 것입니다. 이러한 위젯들은 위젯 트리 상위에서 MediaQuery 위젯이 사용 가능하도록 보장합니다.

## 예시 시나리오

간단한 예시를 살펴보겠습니다. 다음과 같은 코드가 있다고 가정해봅시다:

```js
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Text(
        '화면 너비: ${MediaQuery.of(context).size.width}',
      ),
    );
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

이 코드를 실행하면 No MediaQuery widget found 오류가 발생합니다. 이는 Text 위젯이 액세스를 시도하는 곳에 MediaQuery 위젯이 존재하지 않기 때문입니다.

## 해결 방법

이 오류를 해결하려면 MaterialApp, CupertinoApp 또는 WidgetsApp과 같은 MediaQuery 객체를 소개하는 위젯이 포함된 위젯 트리를 확인하십시오. 수정된 코드는 다음과 같습니다:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text('MediaQuery Example'),
        ),
        body: Center(
          child: Text(
            'Screen width: ${MediaQuery.of(context).size.width}',
          ),
        ),
      ),
    );
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

이 수정된 버전에서는 MaterialApp 위젯이 MediaQuery 위젯이 위젯 트리 상위에 있는지 확인합니다. 이제 MediaQuery.of(context)를 사용하여 미디어 쿼리 정보에 성공적으로 액세스할 수 있습니다.

## 대안 솔루션

만약 MaterialApp을 사용하고 싶지 않다면 수동으로 위젯을 MediaQuery 위젯으로 래핑할 수 있습니다. 예를 들어:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MediaQuery(
      data: MediaQueryData.fromWindow(WidgetsBinding.instance.window),
      child: Center(
        child: Text(
          '화면 너비: ${MediaQuery.of(context).size.width}',
        ),
      ),
    );
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

이번 버전에서는 MediaQuery 위젯을 수동으로 추가했습니다. 이 위젯에는 MediaQueryData.fromWindow으로 얻은 화면 데이터가 제공되었습니다.

## 결론

플러터(Flutter)에서 발생하는 No MediaQuery widget found 오류는 흔히 마주치는 장애물이지만, 위젯 트리에 MediaQuery 위젯이 포함되어 있는지 확인하여 쉽게 해결할 수 있습니다. MaterialApp, CupertinoApp 또는 WidgetsApp을 사용하거나 위젯을 수동으로 MediaQuery 위젯으로 래핑함으로써 이를 수행할 수 있습니다.

이 오류를 이해하고 해결하는 것은 Flutter 개발 경험을 더욱 원만하고 효율적으로 만들어 줄 것입니다.
