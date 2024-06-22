---
title: "오픈 소스의 힘 Flutter로 Maestro 성능 최적화 하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_0.png"
date: 2024-06-22 23:21
ogImage: 
  url: /assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_0.png
tag: Tech
originalTitle: "The power of open-source. Making Maestro work better with Flutter"
link: "https://medium.com/mobile-dev-inc/the-power-of-open-source-making-maestro-work-better-with-flutter-d92b386f9a33"
---


마에스트로는 기본 또는 크로스 플랫폼 기술로 구축된 앱에 대한 최종 UI 테스트를 생성하는 가장 빠르고 쉬운 방법입니다. 마에스트로는 "안드로이드 테스팅 프레임워크"나 "플러터 테스팅 프레임워크"가 아니라, 접근성 정보에만 의존하는 "UI" 테스팅 프레임워크입니다.

이 기사에서는 Flutter 앱을 테스트할 때 사용자가 마주한 문제와 우리가 Flutter 오픈 소스 프로젝트에 기여함으로써 그 문제를 어떻게 해결했는지 알아볼 수 있을 거예요!

# Flutter에 대한 간단한 소개

Flutter에 익숙하지 않은 분들을 위해, Flutter는 Dart 언어를 사용하여 앱을 구축하기 위한 크로스 플랫폼 프레임워크입니다. Flutter에서 UI의 거의 모든 부분(버튼, 목록, 화면 전체)을 위젯이라고 부릅니다.

<div class="content-ad"></div>

플러터의 독특한 특징 중 하나는 게임 엔진처럼 작동한다는 점입니다. 위젯은 캔버스 위에 직접 렌더링됩니다. 이에는 많은 장점이 있지만, 네이티브 구성 요소를 사용하지 않기 때문에 안드로이드와 iOS에서 제공하는 기본 UI 프레임워크에서처럼 UI를 설명하는 의미 정보를 자동으로 제공할 수 없습니다. 대신, 플러터 위젯은 전체 의미 정보 트리를 직접 구성합니다.

## 플러터에서의 의미 정보

여기서 플러터의 구성 요소인 접근성 브릿지라는 것이 필요합니다. 이는 플러터 앱 내부에 존재하는 가상 의미 트리를 TalkBack이나 VoiceOver와 같은 스크린 리더가 이해할 수 있는 네이티브 의미 트리로 번역합니다. 또는 Maestro와 같은 접근성 기반 테스트 프레임워크에서 이해할 수 있도록 합니다!

![이미지](/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_0.png)

<div class="content-ad"></div>

당연히 이것은 단순화된 내용입니다 (Flutter는 시맨틱 트리라는 다른 트리에서 접근성 정보를 유지합니다), 하지만 핵심은 동일합니다.

Flutter의 탁월한 접근성 지원 덕분에 Maestro는 항상 훌륭하게 작동했습니다.

## Flutter에서의 키

반응형 프레임워크(예: React 또는 Flutter)를 사용한 적이 있다면 키(Key) 개념에 익숙할 것입니다. Flutter의 모든 위젯은 선택적인 "key" 인수를 받습니다. 키는 프레임워크가 어떻게 하나의 위젯이 다른 위젯을 대체하는지 제어하는 데 사용됩니다. 예를 들어 편집 가능한 목록에서 자주 발생하는 경우입니다. 키는 위젯을 화면에서 고유하게 식별하는 데도 사용될 수 있지만 그것이 주요 목적은 아닙니다.

<div class="content-ad"></div>

# 문제 설명

몇 달 전에 Maestro 사용자 중 한 명이 GitHub에 흥미로운 기능 요청을 제출했습니다. 그는 Maestro가 키를 기반으로 플러터 앱과 상호 작용할 수 있기를 원했습니다:

![image](/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_1.png)

다시 말해, 그는 Maestro 플로우에서 이 코드를 작성할 수 있기를 원했습니다:

<div class="content-ad"></div>


- tapOn:
    flutterKey: add_comment
- tapOn:
    flutterKey: comment_text_field
- inputText: Mobile UI Testing, Simplified.


이 기능 요청은 Maestro 사용자들에게 중요한 것으로 나타났습니다 - 빠르게 30개의 좋아요를 받아 GitHub 저장소에서 두 번째로 많이 추천된 이슈가 되었습니다!

<img src="/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_2.png" />

커뮤니티로부터 이러한 강력한 피드백을 받아 우리는 이 문제에 대해 심층적으로 알아보기로 결정했습니다. 다행히 이 이슈 작성자는 자체적으로 flutter_driver 패키지를 사용한 초기 구현을 공유하기도 했습니다.


<div class="content-ad"></div>

# 입력: flutter_driver

플러터 드라이버는 플러터의 원본 솔루션으로 엔드 투 엔드 UI 테스트를 위한 것입니다. 사용하려면, 개발자는 flutter_driver 패키지에 종속성을 추가하고 앱 시작 중에 이를 초기화해야 합니다. 그런 다음 테스트 스크립트를 Dart로 작성하여 호스트 기계에서 실행합니다. 테스트 스크립트는 웹소켓을 통해 실행 중인 앱과 JSON-RPC 명령(탭 또는 enterText와 같은)을 주고받습니다.

![이미지](/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_3.png)

드라이버 스크립트는 플러터 앱을 실행하는 동일한 Dart VM과 통신하기 때문에 탭, 클릭, 텍스트 입력 등 다양한 작업을 수행할 수 있습니다. 이는 위젯 키로 위젯을 찾는 것도 가능합니다:

<div class="content-ad"></div>

```js
async void main() {
  final driver = await FlutterDriver.connect();
  await driver.tap(find.byValueKey('add_comment'));
  await driver.close();
}
```

flutter_driver의 중요한 제한 사항은 Flutter에 의해 렌더링된 UI와만 상호 작용할 수 있다는 것입니다. 즉, 권한 대화상자와 같은 네이티브 UI 요소에 액세스할 수 없다는 것입니다. 다행히 Maestro는 이미 시맨틱 정보를 사용하여 네이티브 UI와 상호 작용하고 있기 때문에 이것은 문제가 되지 않습니다.

지금까지 잘 진행되고 있습니다. 그럼 함정은 무엇일까요?

# flutter_driver에서 문제점


<div class="content-ad"></div>

지금까지 몇 년 동안 플러터 팀에 의해 flutter_driver가 서서히 제거되고 있습니다. 제거될 예정이라는 신호는 없지만 구글 내부를 포함한 많은 기존 테스트가 이에 의존하므로 지원, 버그 수정 또는 개발을 거의 받지 않고 있습니다. 이는 처리되지 않은 이슈의 많은 숫자에서 확인할 수 있습니다.

플러터 개발자들은 flutter_driver에서 integration_test 플러그인으로 이주하도록 권장받습니다. integration_test 플러그인은 flutter_driver의 몇 가지 문제점(예: 완전한 블랙박스인 것, JSON을 주고받기만 하는 것)을 해결하지만 새로운 문제점을 도입하고, 여전히 심각한 UI 테스트에 대한 실용적인 솔루션은 아닙니다. 또한 2021년 1분기에 Flutter 2.0에서 출시된 이후 큰 업데이트를 받지 않았습니다.

이제 flutter_driver를 안정적인 기반으로 보지 않는 이유를 이해하셨습니다. 하지만 안정성과 유지 보수 측면을 제외하고도 다른 심각한 단점들이 있었습니다.

## 릴리스 빌드에서 작동하지 않음

<div class="content-ad"></div>

플러터 드라이버는 디버그 및 프로필 모드로 빌드된 앱에서만 작동합니다. 이 제한은 Dart VM 서비스 확장으로 구현되었기 때문에 릴리스 빌드에서는 비활성화된 서비스 확장으로 인해 발생합니다. 이 동작을 변경하려면 패키지를 포크하고 서비스 확장 대신 일반 HTTP 서버를 사용해야 합니다.

## 앱 코드 수정 필요

앱 개발자는 pubspec.yaml 파일에 flutter_driver를 포함하고 일부 초기화 코드를 추가해야 합니다. 이로 인해 Maestro의 가장 인기 있는 기능 중 하나인 놀랍도록 간단한 설정 또는 설정 없음이 사라지게 됩니다.

## 컨텍스트 전환

<div class="content-ad"></div>

현재 선택한 flutterKey 인수를 selectors에 추가해도 테스트 중인 Flutter 앱에서만 작동할 것입니다. 따라서 Maestro 사용자들은 테스트의 현재 "컨텍스트"(내 Flutter 앱이 현재 실행 중인가요?)를 추적해야 합니다. 이는 분명히 직관적이지 않습니다.

## Flutter 위젯 트리와 의미 트리 간의 복잡한 상호작용

탭 또는 입력 텍스트와 같은 간단한 경우는 잘 작동할 것입니다. 그러나 보다 복잡한 상황은 어떨까요? Maestro의 훌륭한 기능 중 하나는 강력한 selector 시스템입니다. 다음과 같이 복잡한 명령을 작성할 수 있습니다:

```js
- tapOn:
    below: "View above that has this text"
    above:
        id: "view_below_id"
    leftOf: "View to the right has this text"
    rightOf: "View to the left has this text"
    containsChild: "Text in a child view"
    childOf:
        - id: "buy-now"
    containsDescendants:
        - id: "title_id"
          text: "A descendant view has id 'title_id' and this text"
        - "Another descendant view has this text"
```

<div class="content-ad"></div>

플러터 드라이버와 함께 작동할 수 있는 방법은 무엇일까요? 명확한 답이 없네요. 어떤 멋진 트리 병합을 사용해야 할까요? Maestro는 가장 간단하고 효과적인 테스트 프레임워크를 지향한다는 것을 기억하세요.

## 불량 사례

이 접근 방식은 전반적으로 Maestro에 플러터의 존재를 "가르쳐 주어야 한다는" 것을 필요로할 것입니다. 하지만 우리는 정말 그렇게 하고 싶지 않습니다. Maestro의 핵심 기능 중 하나는 당신이 앱을 작성하는 기술에 관계없이 항상 잘 작동한다는 것입니다. 이는 당신의 앱이 생성한 의미 정보만 사용함으로써 가능합니다. (내부 작동 방식에 대해 더 알아보려면 이 기사를 확인하세요).

플러터를 위해 특별한 지원을 추가하면 잘못된 선례가 될 수 있습니다. 우리는 접근성만을 고려한 접근 방식에서 벗어나 Flutter를 지원하기 위해 존재하는 추가 코드를 유지해야 할 수도 있습니다.

<div class="content-ad"></div>

# 다시 스케치하는 중

플러터 드라이버 접근 방식이 마음에 들지 않았어요. 

에드워도(원래 이슈 작성자)가 제공한 초안 실행 방식만 사용하고 (화면 테스트 도구를 개발했던 제 경험을 많이 참고하여) 몇 가지 사고 실험을 진행했더니 이미 많은 문제점이 발견되었어요.

한 발 물러나서 생각해 보았어요. 정말 옳은 문제를 해결하려고 노력 중인 걸까요?

<div class="content-ad"></div>

## 시맨틱 위젯 — 누락된 조각

처음에 Flutter 앱의 의미론 정보가 어떻게 AccessibilityBridge에 의해 네이티브 의미론으로 변환되는지 보여드렸습니다. 이것은 복잡한 시스템이지만, 앱 개발자의 관점에서는 위젯을 시맨틱 정보와 함께 주석 처리하기 위해 Semantics 위젯을 사용하는 것으로 이해할 수 있습니다:

```js
Semantics(
  label: '이상한 빨간 상자 위젯',
  child: InkWell(
    onTap: () => launchUrl('https://youtu.be/YlUKcNNmywk?t=148'),
    child: Container(width: 100, height: 100, color: Colors.red),
  ),
)
```

Semantics.label 필드는 AccessibilityBridge에 의해 접근성 레이블로 변환되며, 이는 텍스트 마에스트로의 선택기라고 불립니다:

<div class="content-ad"></div>

```js
- tapOn:
    text: "이상한 빨간 상자 위젯"
```

접근성 레이블의 문제는 스크린 리더가 사용자에게 읽혀진다는 점입니다. 실제로 그것이 목적이죠. 도움이 되는 콘텐츠를 UI 테스트에 사용될 이상한 식별자로 대체하고 싶지 않으시겠죠. 악인이 되지 마세요!

다행히도 안드로이드와 iOS의 기본 시맨틱 요소는 접근성 식별자도 지원합니다. 이는 접근성 레이블과 동일하지만 UI 테스트에만 사용됩니다. 스크린 리더는 이를 읽지 않습니다.

마에스트로는 항상 시맨틱 식별자에 기반한 상호 작용을 지원해 왔습니다:

<div class="content-ad"></div>

```yaml
- tapOn:
    id: login_button
```

Flutter에서 작동하지 않는 이유는 무엇일까요? 답은 간단합니다: 'Semantics' 위젯에는 의미 식별자용 속성이 없습니다.

빠른 구글 검색으로도 Flutter 문제인 "테스트 목적을 위한 접근성 ID 누락"이라는 적절한 이름의 문제가 확인되었습니다.

<img src="/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_4.png" />


<div class="content-ad"></div>

테스트와 관련된 Flutter 이슈 중에서 가장 많이 투표를 받은 문제였어요. 2018년 5월 생성되어 129개의 투표를 받았죠. 접근성 식별자가 빠져 있어서 짜증났던 사람들이 우리뿐만이 아니었다는 사실을 알게 되었어요!

만약 이 문제를 해결하면, Maestro 사용자뿐만 아니라 Flutter 앱을 진지하게 테스트하고자 하는 모든 사람들이 혜택을 받을 거예요.

# Plan B — Flutter 수정하기

이 문제를 해결하는 데 시간이 좀 걸렸어요. 5년 동안 120개의 댓글을 다 읽는 것은 꽤 어려운 작업이었죠! 하지만 해냈고, 이미 필요한 변경이 무엇인지 대충 감이 왔어요 — Semantics 위젯에 식별자 속성을 추가해야 합니다:

<div class="content-ad"></div>

```js
Semantics(
  identifier: 'tap_me',
  label: 'A weird red box widget',
  child: InkWell(
    onTap: () => launchUrl('https://youtu.be/YlUKcNNmywk?t=147'),
    child: Container(width: 100, height: 100, color: Colors.red),
  ),
)
```

위젯을 시멘틱스 식별자로 탭하려면:

```js
- tapOn:
    id: "tap-me"
```

## 장단점

<div class="content-ad"></div>

새로운 "fix Flutter approach"가 매력적으로 보였어요. 이점들은 분명하게 있었습니다:

- 높은 보상 — 모든 것들이 갑자기 작동하기 시작할 거에요
- Maestro의 철학에 충실 — 설정이 제로이고 사용하기 쉽고 간단함
- flutter_driver의 문제점이 전혀 없어요

불확실한 점들도 몇 가지 있었어요:

- 외부 프로젝트에 (복잡한 지도) 기여가 필요해요
- 프로젝트 소유자들이 받아들일 수도 없을 기여가 필요해요
- 받아들여진다 해도, 주기적으로 큰 Flutter 릴리즈에만 사용 가능할 거에요, 이는 매 분기마다 일어나요
- 앱 개발자들은 이를 활용하기 위해 Flutter 버전을 업그레이드해야만 할 거에요

<div class="content-ad"></div>

다행히도이 기능 요청은 긴급하지 않았기 때문에 우리는 시간을 들여 제대로 처리하기로 결정했습니다. "Flutter 방식 수정"이 실패할 경우 언제든지 "flutter_driver 접근 방식"을 재고할 수 있을 것이기 때문입니다.

# 구현

가장 재미있는 부분!

우리가 기여한 기능은 간단해 보이지만, Flutter의 모든 레이어 — 프레임워크, 엔진 및 Android 및 iOS 내장 프로그램 — 에 변경이 필요했고, 다양한 프로그래밍 언어 — Dart, Java, Objective-C++ 및 C++ — 을 사용하여 일부 코드를 작성해야 했습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_5.png" />

플러터 프로젝트는 Framework(flutter/flutter)와 Engine(flutter/engine)로 두 가지 주요 리포지토리로 나뉘어져 있어요.

Framework에서의 변경사항은 빠르고 즐거웠지만, Engine은 완전히 다른 수준이었어요. 수십만 줄의 코드가 있었죠. 컴파일하는 데 한 시간을 기다리는 것은 재미 없었지만 다행히도 점진적 빌드는 단 몇 분밖에 걸리지 않았어요.

또한, 플러터 프로젝트는 모든 PR이 master로 병합되기 전에 모두 초록색이어야 한다고 강요하고 있어요. 이로 인해 Framework ↔ Engine 경계에서 일부 내부 API를 변경해야 했고, 결과적으로 빨간색 CI가 발생했던 어려움이 있었어요.

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_6.png)

의도적인 순환 종속성은 프레임워크와 호환되지 않는 엔진 변경 사항을 병합할 수 없도록 합니다.

임시 API를 도입하고 식별자 속성을 주의 깊게 추가함으로써 이 문제를 해결했습니다. 결과적으로 5개의 풀 리퀘스트가 생성되었습니다:

- [엔진] 임시 API 추가
- [프레임워크] 임시 API에 종속
- [엔진] 원본 API를 임시 API와 동일하게 업데이트
- [프레임워크] 원본 API에 종속, 임시 API에 대한 종속 제거
- [엔진] 임시 API 제거



<div class="content-ad"></div>

프로세스는 상당히 복잡했지만, Flutter와 같은 큰 프로젝트에서 완벽한 의미를 가집니다.

# 결과

성공! 🎉

새 Semantics.identifier 속성은 Android 및 iOS의 원래 접근성 트리에 존재하며 Maestro 및 Maestro Studio 모두에서 매끄럽게 작동합니다.

<div class="content-ad"></div>

그리고 깜박해서 Flutter에서 가장 많은 추천을 받은 테스팅 이슈 #1도 해결해 버렸어요! 그리고 이건 Maestro뿐만 아니라 모든 모바일 UI 테스팅 프레임워크를 사용하는 사용자들을 위해 한 거에요! 그래서 여전히 Appium을 사용해서 Flutter 앱을 테스트하고 Maestro로 전환하고 싶지 않은 경우에도, 여전히 당신의 기여에서 이익을 얻을 수 있어요.

커뮤니티에서는 5년 된 이슈가 해결되어 기뻤답니다 🤠

<img src="/assets/img/2024-06-22-Thepowerofopen-sourceMakingMaestroworkbetterwithFlutter_7.png" />

# 지금 바로 시도해보세요!

<div class="content-ad"></div>

최신 안정화된 Flutter 3.19 릴리스에서는 이미 Semantics.identifier을 사용할 수 있어요:

```js
$ flutter channel stable
$ flutter upgrade
```

또한 Flutter 문서도 업데이트했습니다 — 이제 Semantics.identifier 사용법을 설명하는 것뿐만 아니라 더 포괄적이고 유용해졌어요 🙌

지금은 이만 글을 마치겠습니다 — 다음에 또 뵙겠습니다!

<div class="content-ad"></div>

아직 Maestro를 사용하여 Flutter 앱을 테스트해보지 않았다면, 지금 해보세요! 확실히 머리가 터질 것입니다. 여기에서 시작하고 Slack에 참여해주세요!