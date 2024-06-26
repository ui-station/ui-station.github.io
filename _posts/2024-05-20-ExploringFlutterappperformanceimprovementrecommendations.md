---
title: "플러터 앱 성능 향상을 위한 권장 사항 탐색"
description: ""
coverImage: "/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_0.png"
date: 2024-05-20 16:16
ogImage:
  url: /assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_0.png
tag: Tech
originalTitle: "Exploring Flutter app performance improvement recommendations"
link: "https://medium.com/@alex_gl/exploring-flutter-app-performance-improvement-recommendations-5bae61586c71"
---

앱 성능은 소프트웨어 제품을 개발할 때 항상 염두에 두어야 할 중요한 요소입니다. 앱 성능에 신경을 쓰면 앱을 믿을 수 있게 만들어주고 유용한 기능을 제공하고 멋진 사용자 인터페이스를 생성함으로써 기존 사용자를 유지하고 새 사용자를 유치하는 데 도움이 됩니다.

Flutter 앱 성능을 향상시키는 팁을 찾을 수 있는 많은 훌륭한 기사와 비디오가 있습니다. 여기에서는 일반적인 개요를 제시하고, DevTools를 사용하여 몇 가지를 자세히 살펴볼 예정입니다.

Flutter 앱은 기본적으로 성능이 우수합니다. 단순히 권장 사항을 따르기만 하면 됩니다. 이미 앱이 잘 작동하면 성능을 향상시키는 방법을 찾을 필요가 없습니다. 노력이 들어가지 않을 것입니다. 다음과 같은 주요 문제가 앱에 없는지 확인해야 합니다:

- UI 지연 — 렌더링에 허용된 시간을 초과한다는 것을 의미하는 프레임을 건너뛰는 경우 (애니메이션이 끊기는 것을 관찰할 수 있음)
- 배터리 소모가 빠른 경우
- 기기가 과도하게 발열하는 경우

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

일부가 관찰되면 문제가 발생한 곳을 찾아야 합니다. 앱 성능은 릴리스 모드와 비슷하지만 DevTools를 사용할 수 있는 프로파일 모드에서 실제 기기에서 조사되어야 합니다:

```js
flutter run --profile
```

성능 조사 목적으로 Profiling timeline 도구를 사용할 수 있습니다. 이 도구는 기기 화면에 렌더링된 프레임의 순서를 나타내는 차트입니다. UI 스레드와 래스터 스레드에서 데이터를 사용하며, 특정 시점에 실행된 프로그램 코드를 실제로 반영합니다. 코드가 효율적이지 않으면 (프레임 당 16.66밀리초 이상이 걸리면 — 초당 60프레임이 생성되어야 함) 막대는 빨간색으로 나타납니다. 그렇지 않으면 파란색으로 표시됩니다. Performance overlay 옵션을 활성화하면 차트를 기기 화면에 바로 추가할 수 있습니다.

앱 성능에 대해 이야기할 때 리소스 소비를 의미한다는 것을 언급해야 합니다:

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

- 시간 — 프로그램이나 코드의 실행에 필요한 시간량입니다. 초당 프레임 수(FPS), 앱 시작 시간, CPU 사용량 등으로 측정할 수 있습니다.
- 공간 — 프로그램 실행에 필요한 데이터 저장 공간의 양입니다. 메모리(RAM) 사용량, 앱 크기 등으로 측정할 수 있습니다.

소프트웨어 개발자들은 종종 앱 성능을 유지하기 위해 시간과 공간 소비 사이의 균형을 찾아야 합니다.

더 구체적인 성능 지표는 다음 링크에서 확인할 수 있습니다: [https://docs.flutter.dev/perf/metrics](https://docs.flutter.dev/perf/metrics).

# 성능 향상 권고사항 탐색

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

성능 향상을 위한 모든 권장 사항은 다음과 같이 요약할 수 있습니다:

- 시간 관련:
- 반복적인 작업을 수행하지 말고, 가능한 경우 캐싱을 사용하세요.
- 비용이 많이 드는 작업은 필요할 때까지 지연시키세요. 이는 다소 모순적일 수 있습니다. 작업이 시간이 오래 걸리고 사용자가 결과가 필요한 높은 확률이 있다면, 사용자가 작업이 완료될 때까지 기다리지 않도록 미리 백그라운드에서 수행하는 것이 더 나은 솔루션이 됩니다.
- 공간 관련:
- 더 이상 필요하지 않고 나중에 필요하지도 않을 객체를 저장하지 마세요.
- 미디어 파일 사용을 최적화하세요 (그리고 앱 크기를 일반적으로 최적화하세요).

# 반복적인 작업을 수행하지 마세요

반복 작업을 수행하지 말아야 한다는 권장 사항은 당연한 것처럼 들릴 수 있지만, 서두르지 마세요. 물론 필요 없이 동일한 작업을 다시 수행할 의향은 아무도 없을 것입니다. 그러나 프로젝트가 커질수록 논리도 복잡해지므로 누락될 수 있는 부분이 있을 수 있습니다. Flutter 프레임워크가 그러한 프로젝트로 간주될 수 있기 때문에 처음부터 주의 깊게 작업해야 합니다.

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

여기서 가장 인기있는 권장 사항은 위젯의 빌드 메서드에서 실행 비용이 많이 드는 메서드를 호출하지 않는 것입니다. 빌드 메서드는 위젯을 초기화 한 후에 한 번만 호출되는 것이 아닙니다. 예를 들어 위젯이 다시 빌드될 때마다 서버 요청을 하는 것은 효율적이지 않습니다.

그 외에 다시 빌드되는 횟수를 줄이는 것이 중요합니다. 다음 권장 사항을 따르면 이를 달성할 수 있습니다:

- 컴파일 시간에 초기 상태를 결정할 수 있고 이후에 변경되지 않을 때만 사용할 수 있는 const 위젯을 사용하는 것. Flutter는 const 위젯의 단일 인스턴스를 만들어 위젯 트리에서 재사용하며 계속 만들고 다시 빌드하지 않습니다.
- 위젯을 반환하는 메서드 대신 위젯을 선호하는 것. setState 메서드를 사용하여 자식 위젯이 나타내는 하위 트리만 업데이트할 때 전체 부모 위젯이 다시 빌드되는 것을 방지합니다. 위젯을 반환하는 메서드를 사용하면 전체 부모 위젯이 다시 빌드됩니다.
- 위젯 트리의 중첩 수준(구조)과 유형을 변경하지 않는 것. Flutter는 이전 빌드에서 해당 위젯을 찾지 못하면 다시 빌드 중에 새 위젯을 만듭니다. 예를 들어 숨기려는 경우 위젯을 트리에서 제거하는 대신 IgnorePointer를 사용하여 무시 매개변수의 다른 값을 사용하는 것이 좋습니다. 중첩 수준을 변경해야 하는 경우 해당 위젯에 GlobalKey를 사용하는 것이 좋습니다.
- 위젯에 가능한 캐싱을 사용하는 것. 예를 들어, AnimatedBuilder는 애니메이션 반복마다 다시 빌드되지 않을 위젯 서브트리를 저장하는 child 매개변수를 제공합니다. 그렇지 않으면 child 매개변수가 무시되고 해당 서브트리가 직접 builder 콜백에 지정된 경우 매번 다시 빌드될 것입니다.
- RepaintBoundary 위젯을 사용하여 자식 위젯을 별도의 레이어로 분리하는 것. 더 많은 정보는 여기에서 확인할 수 있습니다 - https://www.youtube.com/watch?v=Nuni5VQXARo

위젯이 다시 빌드되었을 때 확인하려면 안드로이드 스튜디오의 Flutter Inspector 탭에서 Widget rebuild stats를 사용하십시오. debugRepaintRainbowEnabled = true로 전역 변수를 설정하면 위젯 주변에 색상 경계가 표시되며 다시 빌드될 때 색상이 바뀝니다.

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

네! 테이블 태그를 마크다운 형식으로 변경해 주세요.

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
class WidgetOptimizationPage extends StatefulWidget {
  const WidgetOptimizationPage({super.key});

  @override
  State<StatefulWidget> createState() => _WidgetOptimizationPageState();
}

class _WidgetOptimizationPageState extends State<WidgetOptimizationPage> {
  @override
  void didChangeDependencies() {
    debugPrint('didChangeDependencies');
    super.didChangeDependencies();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: Text(
          'Widget Optimization',
        ),
      ),
      body: Column(
        children: [
          Container(
            margin: const EdgeInsets.symmetric(vertical: 8),
            height: 44,
            width: 0.75 * MediaQuery.of(context).size.width,
            child: const TextField(
              decoration: InputDecoration(
                border: OutlineInputBorder(),
                hintText: 'Search',
              ),
            ),
          ),
          Expanded(
            child: ListView.separated(
              itemCount: 10,
              itemBuilder: (BuildContext context, int index) {
                return ListTile(
                  visualDensity: VisualDensity.compact,
                  title: Text('$index'),
                );
              },
              separatorBuilder: (BuildContext context, int index) => const Divider(),
            ),
          ),
        ],
      ),
    );
  }
}
```

텍스트 필드에 포커스를 설정하여 키보드를 표시하고 Done 버튼을 클릭하여 숨기기를 시도하는 경우:

<img src="https://miro.medium.com/v2/resize:fit:704/1*u9fTFwbb5q-HzdNF6a9yoA.gif" />

콘솔에서 didChangeDependencies 메소드가 여러 번 실행된 것을 볼 수 있을 것입니다. 여기서 요청이 호출된 곳이었습니다!

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

![image 0](/assets/img/2024-05-20-Flutter성능개선권장사항탐색_0.png)

위젯이 여러 번 다시 빌드되었음을 의미합니다. TextField는 총 3번 다시 빌드되었고 (화면이 열릴 때, 키보드가 나타났을 때, 키보드가 사라질 때), WidgetOptimizationPage는 12번 다시 빌드되었습니다:

![image 1](/assets/img/2024-05-20-Flutter성능개선권장사항탐색_1.png)

didChangeDependencies 메서드가 InheritedWidget에서 알림에 대한 응답으로 작업을 수행하기에 적절한 위치임을 알려져 있습니다. 위젯이 어떤 변화들을 구독하는지 찾기로 결정했습니다. 위젯이 다소 복잡했기 때문에 몇 가지 가정을 하였지만, 위의 코드 예제에서는 명확하게 인식할 수 있습니다. 이유는 MediaQuery.of(context).size.width를 사용했기 때문입니다. MediaQuery는 기본적으로 다양한 매개변수를 가진 InheritedWidget으로, viewInsets와 padding을 포함하여 여러 매개변수가 있습니다. 이들은 키보드 애니메이션 중에 변경됩니다.

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

문제의 해결 방법은 MediaQuery.sizeOf(context).width를 사용하는 것이었습니다. sizeOf는 해당 속성이 변경될 때마다 컨텍스트를 다시 빌드하며, MediaQueryData의 of 메서드에서 직접 속성을 가져 오는 것보다 우선해야 합니다.

수정 후에 만든 아래 스크린샷에서 볼 수 있듯이 WidgetOptimizationPage가 한 번 다시 빌드되었습니다.

![이미지](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_2.png)

# 비용이 많이 드는 작업을 필요할 때까지 지연시키세요

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

성능 향상의 다음 원칙은 불필요한 작업을 하지 말아야 한다고 말합니다. 프로젝트의 구조, 상태 관리 방식, 사용하는 타사 라이브러리의 특성, 특정 화면의 내부 논리 등에 많이 따라 달라집니다. 이곳에서는 Dart에서 제공하는 몇 가지 사항을 언급하고 싶습니다.

다트가 다른 프로그래밍 언어와 마찬가지로 복잡한 조건의 각 부분을 계산하지 않는다는 것을 모두 알고 있을 것입니다. 일부 조건이 일찍 결정될 수 있다면 다른 조건은 심지어 계산할 필요가 없게 됩니다. 예를 들어, 여러 조건이 OR 연산자로 연결된 조건인 경우 첫 번째 조건이 true를 반환하면 다른 조건들은 계산되지 않을 것입니다. 왜냐하면 그들은 전체 조건의 결과에 영향을 미치지 않기 때문이죠.

```js
void testLateInitialization() async {
  late final first = performCalculations();
  late final second = performComplexCalculations();

  if (first || second) {
    debugPrint('Hello world');
  }
}

bool performCalculations() {
  // 여기에 일부 계산이 있다고 가정해봅시다.
  return true;
}

bool performComplexCalculations() {
  // 여기에 일부 복잡한 계산이 있다고 가정해봅시다.
  return false;
}
```

그리고 우리에게 중요한 한 가지 — late 키워드입니다. 이는 변수가 액세스하려고 시도할 때만 값을 가져온다는 것을 의미합니다. 이를 통해 조건을 의미있는 부분으로 분할하고 가독성을 향상시킬 수 있습니다.

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

DevTools의 Debugger 섹션에서는 아래 사진에 나와 있는 목록 아이콘 버튼을 선택하여 확인할 수 있습니다:

![List Icon](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_3.png)

함수 이름 앞에 초록색 선은 디버그 세션 중 호출된 것을 나타내고 빨간 선은 호출되지 않은 것을 나타냅니다.

그리고 'late' 키워드에 대한 추가 정보 - 우리의 경우 비동기 함수에 사용할 수 없지만, 비동기 함수는 조건식에서 직접 호출할 수 있습니다.

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
void testLateInitialization() async {
  late final first = performCalculations();

  if (first || await performComplexCalculations()) {
    debugPrint('Hello world');
  }
}

bool performCalculations() {
  // Let's suppose here are some calculations
  return true;
}

Future<bool> performComplexCalculations() async {
  // Let's suppose here are some complex calculations
  return false;
}
```

동기 함수에 대한 영향과 동일합니다 - 함수를 호출할 가치가 없으면 호출되지 않습니다.

하나의 변수에 대한 늦은 초기화가 어떻게 작동하는지를 보여드렸습니다. 이제 게으른 컬렉션에 대해 이야기해 봅시다. Dart에서 List 객체에서 호출되는 많은 메서드는 Iterable을 반환합니다. Iterable은 자신의 항목을 반복하는 방법을 제공하는 추상 mixin입니다. 사실 List나 Set과 같은 컬렉션은 Iterable mixin을 구현합니다. Iterable의 요소는 요청될 때 계산되는 특징이 있습니다. 다음 코드를 살펴보겠습니다:

```js
class ListObject {
  int value;

  ListObject(this.value);
}

class IterableObject {
  String value;

  IterableObject(this.value);
}

void testListVsIterable() {
  final List<int> sourceList = List.generate(10, (index) => index);

  final List<ListObject> intObjectList = sourceList.map((e) => ListObject(e)).toList();
  for (var element in intObjectList.take(5)) {
    debugPrint('$element');
  }

  final stringObjectList = sourceList.map((e) => IterableObject('$e'));
  for (var element in stringObjectList.take(5)) {
    debugPrint('$element');
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

두 개의 클래스인 ListObject와 IterableObject을 만들었어요. 두 클래스는 값만을 가지고 있어서 거의 동일하며, 나중에 DevTools에서 구분하기 위해 만들었어요. testListVsIterable 함수에서 sourceList는 초기 데이터의 리스트이며, ListObject와 IterableObject의 컬렉션이 생성됩니다. map 함수는 Iterable을 반환하기 때문에 List`ListObject`를 얻기 위해 toList를 호출해야 합니다. 첫 5개 요소를 반복하고 DevTools에서 어떻게 작동하는지 살펴보세요. 어떤 클래스의 인스턴스가 할당되었는지 확인할 수 있어요. 메모리 - Trace Instances를 선택하고 검사하려는 클래스를 선택해주세요.

![image](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_4.png)

우리의 실험 결과, 10개의 ListObject 객체 인스턴스와 5개의 IterableObject 인스턴스가 할당되었어요. 사실, 나는 Iterable과 List 사이의 차이를 보여주기 위해 컬렉션의 첫 5개 요소만 반복하기로 결정했어요. 앞서 언급했듯이, Iterable은 객체가 생성되는 방법을 결정할 수 있지만 요청될 때만 생성됩니다. 반면 List는 즉시 컬렉션을 생성합니다. 따라서 전체 컬렉션을 반복한다면 차이가 없을 것입니다.

특히 Iterable의 장점에도 불구하고, 이 경우에는 단점이 있어요 - 접근할 때마다 새 인스턴스가 생성됩니다.

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

우리가 두 번 반복해야 하는 경우 ListObject와 IterableObject 사이에 차이가 없어질 것입니다.

```js
void testListVsIterable() {
  final List<int> sourceList = List.generate(10, (index) => index);

  final List<ListObject> intObjectList = sourceList.map((e) => ListObject(e)).toList();
  for (var element in intObjectList.take(5)) {
    debugPrint('$element');
  }
  for (var element in intObjectList.take(5)) {
    debugPrint('$element');
  }

  final stringObjectList = sourceList.map((e) => IterableObject('$e'));
  for (var element in stringObjectList.take(5)) {
    debugPrint('$element');
  }
  for (var element in stringObjectList.take(5)) {
    debugPrint('$element');
  }
}
```

![이미지](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_5.png)

그리고 더 많은 횟수로 반복해야 하는 경우 List가 Iterable보다 효율적일 것입니다.

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

그러나 Iterable은 제가 만든 그런 인위적인 시나리오 뿐만 아니라 다른 상황에서도 유용할 수 있습니다. 앱 내에서 클래스 또는 레이어 간에 데이터를 전송해야 하는 경우, 매번 List로 변환하는 대신 Iterable을 사용하는 것을 고려해보세요. toList 메서드는 실제로 컬렉션을 반복합니다.

요약하면, Iterable은 유용할 수 있지만 이로부터 이득을 얻으려고 할 때 조심해야 합니다.

# 필요 없는 객체를 저장하지 마세요. 이후에도 필요하지 않을 예정이기 때문입니다.

다음 권장 사항을 살펴보겠습니다:

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

이 문장을 확인하기 위해 몇 개의 화면을 만들었습니다. 첫 번째 화면인 AddingCounterPage는 기본적으로 설정된 것과 매우 유사한 카운터를 구현합니다. 새로운 Flutter 프로젝트를 설정할 때 기본적으로 제공되는 것과 매우 유사합니다. 이 페이지는 값에 2를 곱하는 MultiplyingCounterPage로 전파될 BehaviorSubject를 인스턴스화합니다.

이러한 화면들을 앞뒤로 표시하고 메모리 뷰를 통해 메모리에 있는 개체를 확인해보겠습니다. 스트림 구독이 취소되지 않았을 때와 두 화면의 스트림 구독이 모두 취소된 경우에 대해 각각 메모리 스냅샷이 촬영되었습니다. 각 단계별로 (흐름이 나타나 있는 아래 그림의 녹색 직사각형에 이름이 표시된) 메모리 스냅샷을 비교하여 작성된 객체와 파괴된 객체가 무엇인지 이해했습니다.

다음은 구독 취소 없이 진행되는 첫 번째 시나리오의 코드 일부입니다.

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
class _AddingCounterPageState extends State<AddingCounterPage> {
  final BehaviorSubject<int> counter = BehaviorSubject<int>.seeded(1);

  @override
  void initState() {
    counter.listen((event) {
      setState(() {});
    });
    super.initState();
  }
  //...
}

class _MultiplyingCounterPageState extends State<MultiplyingCounterPage> {
  @override
  void initState() {
    widget.counter.listen((event) {
      setState(() {});
    });
    super.initState();
  }
}
```

main-3 스냅샷에는 그들의 상태를 가진 AddingCounterPage와 MultiplyingCounterPage가 있었습니다 - 해당 시점에 메모리에 있었습니다. 구독과 관련된 여러 쌍의 객체도 있습니다. StartWithStreamTransformer, \_StartWithStreamSink, \_MultiControllerSink가 있습니다. 하나는 AddingCounterPage를 위한 것이고 다른 하나는 MultiplyingCounterPage를 위한 것입니다:

![image](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_7.png)

main-3와 main-4 스냅샷 사이에는 차이가 없습니다. MultiplyingCounterPage가 사라졌지만 모든 인스턴스가 메모리에 남아 있습니다:

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

![Exploring Flutter App Performance Improvement Recommendations](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_8.png)

And only after closing `AddingCounterPage`, the subscription objects were released with `AddingCounterPage` and `MultiplyingCounterPage`:

![Exploring Flutter App Performance Improvement Recommendations](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_9.png)

Subscription canceling should help get rid of this memory leak. We just save a `StreamSubscription` when adding the listener and cancel it when the widget is disposed:

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
class _AddingCounterPageState extends State<AddingCounterPage> {
  final BehaviorSubject<int> counter = BehaviorSubject<int>.seeded(1);
  late final StreamSubscription subscription;

  @override
  void initState() {
    subscription = counter.listen((event) {
      setState(() {});
    });
    super.initState();
  }
  //...

  @override
  void dispose() {
    subscription.cancel();
    super.dispose();
  }
}

class _MultiplyingCounterPageState extends State<MultiplyingCounterPage> {
  late final StreamSubscription subscription;

  @override
  void initState() {
    subscription = widget.counter.listen((event) {
      setState(() {});
    });
    super.initState();
  }

  //...
  @override
  void dispose() {
    subscription.cancel();
    super.dispose();
  }
}
```

두 번째 경우에서는 main-3 스냅샷은 이전 시나리오와 비슷한 모습이므로 여기에 추가하지 않았습니다. 그러나 main-3와 main-4 스냅샷 간의 차이점은 MultiplyingCounterPage 및 해당 구독 객체가 해제되었다는 것입니다:

![이미지](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_10.png)

마찬가지로, AddingCounterPage 및 해당 구독 객체는 main-5 스냅샷에서 해제되었습니다.

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

이 실험을 통해 이 권장이 작동한다는 것을 입증했어요. 반드시 해지해야 하는 구독과 기타 해체가 필요한 객체(TextEditingController, AnimationController 등)들을 잊지 마세요. 그렇지 않으면 앱 성능에만 영향을 미칠 뿐만 아니라 프로그램 동작을 망가뜨릴 수도 있어요.

# 미디어 파일 사용 최적화

이미지와 비디오와 같은 미디어 파일을 다루는 것은 앱 성능에 중대한 영향을 미칩니다. 그들의 품질이 좋을수록, 메모리에서 차지하는 공간이 더 많이 필요하고 처리 및 렌더링하는 데 더 많은 시간이 필요합니다. 그들의 품질을 희생할 수 없기 때문에 일반적으로 가장 많은 리소스를 사용하는 객체들입니다. 따라서 미디어 파일 처리 방법을 최적화해야 합니다.

이미지를 처리하는 방법을 최적화하는 방법은:

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

- 대부분에 맞는 이미지 형식을 선택해보세요:
- 사진과 같은 연속음 영상에는 JPEG를 사용합니다. 손실 압축 기능을 제공합니다.
- GIF 및 PNG는 예리한 가장자리를 가진 이미지, 로고, 텍스트, 그래픽 및 투명성이 필요한 이미지에 적합합니다. 비손실 압축을 제공합니다.
- JPEG와 PNG의 이점을 결합한 WebP 및 AVIF는 더 효율적인 압축을 제공합니다.
- 로고, 텍스트 또는 아이콘과 같은 단순한 기하학적 모양에 대한 벡터 기반 그래픽용 SVG. XML 마크업으로 표현되며 품질 하락 없이 모든 해상도에서 렌더링할 수 있습니다.
- 품질 압축은 이미지 크기를 바이트 수로 줄이는 방법입니다.
- 손실 압축은 인치 당 픽셀 수의 이미지 해상도를 줄여 픽셀의 숫자를 줄임으로써 이미지 크기를 줄이는 것이 가능합니다. 소량의 품질 손실이 허용되는 경우 (예: 사진)에 적용됩니다.
- 비손실 압축은 품질에 해를 입히지 않고 원래 이미지를 복원할 수 있는 이미지 인코딩을 제공합니다. 주로 의학 영상, 기술 도면 등의 보존 목적으로 더 적합합니다.
- 높이와 너비처럼 이미지 크기인 차원을 줄입니다. 대규모 이미지를 작은 화면 (휴대전화 등)에 표시하는 것은 정보가 중복되어 있어 효율적이지 않습니다. 대규모 이미지를 작은 화면에 맞게 조정하려면 추가 계산이 필요합니다. 원하는 차원에 정확히 맞게 이미지를 조정함으로써 렌더링 프로세스를 더 효율적으로 처리할 수 있습니다.
- 네트워크로부터 수신한 이미지를 캐싱합니다.

Flutter의 이미지 최적화 기능을 살펴보겠습니다. 먼저 화면에 Image 위젯을 추가해야 합니다:

```js
Image.asset(
  'lib/assets/landscape.jpg',
),
```

하지만 이미지를 최적화해야 하는지 어떻게 알 수 있을까요? DevTools에는 과도한 크기의 이미지를 강조하는 옵션이 있어 항상 식별할 수 있습니다. Flutter Inspector 탭에서 해당 버튼을 클릭하여 이를 활성화할 수 있습니다:

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

다음은 글로벌 매개변수를 프로그래밍 방식으로 설정하는 방법입니다:

```js
debugInvertOversizedImages = true;
```

거대한 이미지는 색상이 반전되고 뒤집힙니다:

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

<img src="/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_12.png" />

로그에서 오류 메시지도 확인할 수 있습니다:

```js
이미지 lib/assets/landscape.jpg의 표시 크기는 640×360이지만 디코딩 크기는 1920×1080으로, 장치의 픽셀 비율을 2.0으로 가정하면 추가로 9600KB를 사용합니다.

미리 크기를 조정하거나 cacheWidth 매개변수로 640, cacheHeight 매개변수로 360을 제공하거나 ResizeImage를 사용하는 것을 고려하십시오.
```

따라서 이미지가 비용이 많이 소비되는 자원으로 간주되는 이유는 큰 차원으로, 이는 렌더링 중에 추가 계산이 필요하다는 것을 의미합니다. 이 오류를 피하려면 cacheHeight 및 cacheWidth 매개변수를 사용해야 합니다.

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
Image.asset(
  'lib/assets/landscape.jpg',
  cacheWidth: MediaQuery.of(context).devicePixelRatio.round() * MediaQuery.of(context).size.width.round(),
),
```

여기서 cacheWidth는 디바이스 픽셀마다 논리적인 픽셀의 수인 devicePixelRatio와 화면 너비의 곱으로 계산됩니다. size.width만 사용하면 이미지가 흐릿해집니다. 이미지 품질에 대해 고려해야 합니다!

![ExploringFlutterappperformanceimprovementrecommendations_13](/assets/img/2024-05-20-ExploringFlutterappperformanceimprovementrecommendations_13.png)

원활한 사용자 경험을 보장하기 위해 preacheImage 메서드를 사용할 수 있습니다. 이 메서드를 사용하면 위젯 초기화 또는 메인 메서드에서 이미지를 캐시에 미리 로드하여 활용할 수 있습니다.

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

이미지 캐싱을 자동으로 처리해주는 Image.network 생성자를 사용하거나, cached_network_image와 같은 서드파티 라이브러리를 사용할 수도 있어요. cached_network_image는 이미지 다운로드 중에 플레이스홀더를 표시하고 요청이 실패한 경우 오류를 표시하는 기능을 제공해요.

이미지 압축에 대해 이야기하자면 flutter_image_compress와 같은 플러그인을 사용할 수 있어요.

전체 애플리케이션 크기에 대해 --analyze-size 명령어를 사용하여 더 명확한 그림을 얻고 최적화할 아이디어를 만들어볼 수 있어요. 코드 크기를 줄이려면 릴리스 버전을 빌드할 때 --split-debug-info를 사용하는 것이 좋아요.

추가 권장 사항으로, 필요 없는 작업을 실행하지 않는 것이 중요해요. 예를 들어, Opacity와 ClipRRect을 필요할 때만 사용하고, 애니메이션에서 클리핑을 피해야 해요. 또한, 비싼 작업을 직접 메인 스레드에서 실행하지 않아야 해요. Isolate를 사용하여 UI에 영향을 주지 않도록 해야 해요.

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

이 글에서는 플러터 앱 개발뿐만 아니라 유용한 앱 성능의 일반 원칙을 제시했습니다. 또한 DevTools를 이용한 플러터 앱 성능 개선에 대해 좀 더 자세히 살펴보았습니다. 도움이 되었으면 좋겠네요. 자세한 정보는 아래 자료 목록에서 찾을 수 있습니다.

자료:

- [https://docs.flutter.dev/perf](https://docs.flutter.dev/perf)
- [https://docs.flutter.dev/tools/devtools/performance](https://docs.flutter.dev/tools/devtools/performance)
- [플러터 성능 향상하는 방법:](https://www.youtube.com/watch?v=KH-3tbD7NoU)
- [DevTools 깊게 들어가기:](https://www.youtube.com/watch?v=_EYk-E29edo)
- [플러터에서 성능 및 최적화 팁 TOP 10:](https://medium.com/@slawomirprzybylski/top-10-performance-optimization-tips-in-flutter-3a4f3f31202b)
- [플러터 성능 개선을 위한 경계 넘기기:](https://medium.com/@parthbhanderi01/raising-the-bar-for-flutter-app-performance-52418f7fa604)
- [RepaintBoundary로 플러터 앱 성능 향상하기:](https://www.youtube.com/watch?v=Nuni5VQXARo)
- [플러터에서 네트워크 이미지 최적화로 메모리 사용량 절약하기:](https://medium.com/make-android/save-your-memory-usage-by-optimizing-network-image-in-flutter-cbc9f8af47cd)
- [MediaQuery와 성능 최적화의 플러터 스킬:](https://medium.com/codex/flutter-skill-of-mediaquery-and-performance-optimization-2fbf9c532fea)
