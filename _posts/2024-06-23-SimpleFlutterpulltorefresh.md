---
title: "Flutter에서 간단하게 Pull to Refresh 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-SimpleFlutterpulltorefresh_0.png"
date: 2024-06-23 21:38
ogImage:
  url: /assets/img/2024-06-23-SimpleFlutterpulltorefresh_0.png
tag: Tech
originalTitle: "Simple Flutter: pull to refresh"
link: "https://medium.com/@cloderaldo/simple-flutter-pull-to-refresh-fd1994224809"
---

<img src="/assets/img/2024-06-23-SimpleFlutterpulltorefresh_0.png" />

모바일 애플리케이션에서 사용자가 콘텐츠를 새로 고칠 수 있는 방법을 제공하는 것은 일반적인 필요사항입니다. 가장 직관적인 방법 중 하나는 pull-to-refresh 제스처입니다. 이 기능을 통해 사용자는 추가 버튼이나 수동 입력이 필요하지 않고 새로운 데이터를 쉽게 가져올 수 있어 사용자 경험을 향상시킵니다.

# Flutter에서 Pull-to-Refresh 구현하기

Flutter를 사용하면 RefreshIndicator 위젯을 사용하여 pull-to-refresh 기능을 구현하기가 쉽습니다. 아래에는 이 기능을 Flutter 애플리케이션에 추가하는 방법을 보여주는 간단한 예제가 있습니다.

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

샘플 코드: main.dart

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Pull to Refresh Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: HomePage(),
    );
  }
}

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  List<String> items = List.generate(20, (index) => "Item ${index + 1}");

  Future<void> _refresh() async {
    await Future.delayed(Duration(seconds: 2));
    setState(() {
      items = List.generate(20, (index) => "New Item ${index + 1}");
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Pull to Refresh Demo'),
      ),
      body: RefreshIndicator(
        onRefresh: _refresh,
        child: ListView.builder(
          itemCount: items.length,
          itemBuilder: (context, index) {
            return ListTile(
              title: Text(items[index]),
            );
          },
        ),
      ),
    );
  }
}
```

# 설명

- MyApp 위젯: 이것은 애플리케이션의 루트 위젯입니다.
- HomePage 위젯: 이것은 풀-투-리프레시 기능이 구현된 메인 화면입니다.
- RefreshIndicator 위젯: 이 위젯은 ListView를 감싸고 풀-투-리프레시 기능을 제공합니다. 사용자가 목록을 새로 고치기 위해 아래로 당기면 onRefresh 메서드가 호출됩니다.
- \_refresh 메서드: 이 메서드는 2초 동안 지연을 통해 네트워크 호출을 시뮬레이션합니다. 지연 후에 새로운 데이터를 보여주기 위해 항목 목록을 업데이트합니다.

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

# 결론

Pull-to-refresh는 현대 모바일 애플리케이션에서 필수적인 기능으로, 사용자 상호작용을 향상시켜 콘텐츠 업데이트를 쉽게 제공합니다. Flutter를 사용하면 RefreshIndicator 위젯을 활용하여 이 기능을 간편하고 효율적으로 구현할 수 있습니다.

이 튜토리얼이 도움이 되었다면 박수를 눌러주시고, 플러터 튜토리얼과 팁을 더 받아보려면 팔로우해주세요. 여러분의 지원은 우리가 여러분의 개발 여정을 돕기 위해 더 많은 콘텐츠를 만들도록 격려합니다!

즐거운 코딩하세요! 🚀
