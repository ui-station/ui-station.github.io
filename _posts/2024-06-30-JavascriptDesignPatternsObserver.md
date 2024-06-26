---
title: "JS 디자인 패턴 옵저버 사용법 이해하기"
description: ""
coverImage: "/assets/img/2024-06-30-JavascriptDesignPatternsObserver_0.png"
date: 2024-06-30 21:52
ogImage: 
  url: /assets/img/2024-06-30-JavascriptDesignPatternsObserver_0.png
tag: Tech
originalTitle: "Javascript Design Patterns: Observer"
link: "https://medium.com/arionkoder-engineering/javascript-design-patterns-observer-91c3d42b6771"
---


애플리케이션 내에서 상태를 관리하는 것은 시스템의 복잡성에 따라 매우 어려운 작업이 될 수 있습니다. 때때로 비동기 이벤트가 다른 이벤트에 영향을 받아 애플리케이션이 어떻게 동작해야 하는지를 결정하는 복잡한 규칙에 의존하는 시나리오에 놓일 수 있습니다. 한 요소의 변경이 입력에 따라 달라지는 변수에 의해 다른 요소에 변경을 일으킬 수도 있습니다.

이러한 상황에서 옵저버 디자인 패턴을 적용하는 것이 흥미로운 아이디어일 수 있습니다. 디자인 패턴 서적에서 정의를 통해 설명해보겠습니다:

일반적으로 변화를 "전달"하는 객체는 주체/Observable로 불리며, 통지를 받을 객체들은 옵저버들이라고 합니다.

![이미지](/assets/img/2024-06-30-JavascriptDesignPatternsObserver_0.png)

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

# 간단한 VanillaJS Subject/Observable 만들기

이러한 행동 패턴의 응용 프로그램을 더 잘 이해하기 위해 먼저 이 패턴이 어떻게 작동하는지 이해해야 합니다. 이를 위해 우리는 우리의 로직을 캡슐화 할 간단한 Subject 클래스를 만들 것입니다. 이 패턴의 여러 구현에서 공통으로 사용되는 몇 가지 용어를 사용할 것입니다:

- 모든 옵저버에게 브로드캐스트될 값을 전달하려면 next 메서드를 사용하여 값을 전달합니다.
- 변경 사항을 듣기 위해 옵저버를 만들려면 subscribe 메서드를 사용하여 옵저버를 전달하십시오.
- 옵저버를 제거하여 Subject 변경 사항을 더 이상 듣지 않게하려면 해당 옵저버를 전달하고 unsubscribe 메서드를 호출하시면 됩니다.

그리고 이로써 우리는 Subject의 기본 구조를 갖게 되었습니다. 이러한 시나리오에서, 그리고 자바스크립트에서 이 패턴의 일반적인 사용 사례에서도 Subject는 클래스로 구현되고 옵저버는 함수로 구현됩니다. 이 구현은 옵저버들에게 많은 유연성을 제공합니다. 함수로서 값들을 많은 방법으로 처리할 수 있기 때문에, 그리고 이전의 구현에 의존하지 않습니다.

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

아래에 주제와 수학 함수로 간주되는 관찰자가 있는 예시가 있습니다:

이 코드 일부를 보려면 콘솔을 여는 것이 관찰자 패턴의 핵심입니다: 주 객체인 이 경우에는 number$ 변수가 여러 변수에 가입하고, 그런 다음 구독된 모든 변수에 값을 브로드캐스트하기 위해 next 호출을 보낼 수 있습니다.

이제 RxJS를 사용하여 좀 더 복잡한 구현을 해보도록 하겠습니다.

# RXjs로 파워 추가하기

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

복잡하고 많이 사용되는 패턴인 옵저버 디자인 패턴에서 자바스크립트의 Observable을 다루는 데 유용한 작업 세트와 도우미 메서드를 제공하는 RxJS 라이브러리가 있습니다. 보통 일상적인 옵저버 디자인 패턴 사용 사례에서 이 라이브러리와 상호 작용해야 할 수도 있습니다.

이 예시를 가져와서 RxJS를 사용하여 슈퍼충전해 보겠습니다. 유저 포스트 UI를 만들겠지만 RxJS가 제공하는 다양한 기능을 활용할 것입니다.

API 생성하기

API를 만들기 위해 이 공개 엔드포인트를 사용할 것입니다. 포스트(Post), 유저(User) 및 코멘트(Comment) 엔드포인트를 사용할 것입니다. 이를 위해 각 포스트에는 유저 정보, 포스트 자체 및 댓글 정보가 있는 다른 소셜 미디어와 유사한 구조를 사용할 것입니다. 이로써 우리는 더 복잡한 예제를 만들 수 있게 되며 옵저버블의 옵저버블이나 고차 옵저버블과 같은 다양한 연산자들을 활용할 수 있게 됩니다.

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

from 메서드를 사용하여 배열이나 프로미스와 같은 일반적인 소스에서 옵저버블 소스를 생성할 수 있습니다.

이제 API를 갖게 됐으니, 입력 옵저버블과 포스트 옵저버블을 생성해보겠습니다.

입력 옵저버블 생성하기

그래서 애플리케이션 내에서 변수 상태를 만들기 위해, 간단한 숫자 입력을 사용하여 사용자 ID를 변경하고 싶을 것입니다. 입력은 디바운싱 및 범위 제한이 있어야 하며, 유효한 값은 1부터 10 사이여야 합니다. RxJS를 사용해서 어떻게 할 수 있을까요?

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

`fromEvent` 메서드를 사용하면 HTML 요소와 이벤트 이름을 전달하여 해당 이벤트에 따라 값을 방출하는 옵저버블을 반환할 수 있습니다. 그러니 우리는 입력 요소와 입력 이벤트를 사용해 보겠습니다. 이는 요소에 사용자 입력이 있을 때마다 발생할 것입니다.

그 후에는 pipe 연산자와 RxJS 연산자 함수를 사용하여 최종 옵저버블을 얻도록 도와줄 수 있습니다. pipe 연산자를 사용하여 옵저버블이 방출하는 값을 연결하는 파이프로 생각하고, 파이프에 삽입된 각 메소드는 데이터를 변경하고 앞으로 전달합니다. 마치 가전제품과 수도 밸브처럼 파이프를 흐르는 물의 특성을 변경하는 것처럼, 각각이 다른 작업을 수행합니다.

사용된 각 메소드를 살펴보겠습니다:

- `map` 메소드는 일반적인 배열 메소드처럼 작동하여 값을 가져와 다른 값으로 반환합니다. 여기서는 이벤트를 가져와 값을 기준으로 정수를 반환할 것입니다.
- `filter` 메소드는 일반적인 배열 상대로 작동하여 흐름에서 10 이하의 값만 방출하도록 합니다.
- `debounceTime` 메소드는 값이 방출되고 300ms 이내에 다른 값이 방출된다면 그 사이 값은 무시되고 300ms 후의 마지막 값만 고려됩니다.
- 마지막으로 `startWith`를 사용하면 초기 값이 1로 설정되어 처음 방출되기 전에 값이 됩니다.

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

그러면 최종적으로 파이프라인 결과가 준비됩니다. 사용자가 입력 이벤트를 발생시킬 때마다, 1과 10 사이의 숫자가 생성되며 다른 모든 숫자는 무시됩니다.

## 게시물 옵저버블 생성

이제 게시물이 있으면, 프로젝트의 가장 복잡한 부분이 될 것입니다. 사용자 및 해당 사용자의 게시물을 가져와야 합니다. 그 후 각 게시물에 대해 해당 게시물의 댓글을 가져와서 모든 내용을 묶어야 합니다.

백엔드 솔루션으로는 이미 관련 데이터를 그룹화하는 다양한 DB 라이브러리와 패러다임이 지원되기 때문에 이 작업은 어렵지 않을 것입니다. 그러나 프론트엔드에서 이 작업을 수행하는 것은 어려울 수 있습니다. 왜냐하면 사용자 입력 로직도 함께 그룹화해야 하기 때문입니다.

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

- 먼저, 입력 Observable을 연결하고 switchMap 연산자를 호출합니다. 이 연산자는 Observable을 가져와 자동으로 구독하여 결과를 반환합니다. 이 패턴에 대한 자세한 내용은 여기에서 확인할 수 있습니다;
- switchMap 메서드에 공급될 Observable은 zip 도우미 함수를 사용하여 구축되며, 이 함수는 n개의 observables을 가져와 모든 작업이 완료된 후 응답의 배열을 방출하는 Observable을 반환합니다.
- 이후에는 사용자 정보와 해당 사용자의 게시물을 반환해야 할 배열이 있지만, 여전히 각 게시물의 코멘트를 가져와 추가해야 합니다.

이제 제대로 이해해 주세요, 조금 복잡해질 수 있습니다:

- 먼저, 우리의 게시물을 위해 생성된 Observable 스트림으로 switchMap을 입력하여, 각 게시물이 Observable에서 방출되는 값이 됩니다;
- 그 후에는 해당 Observable에서 나오는 각 게시물에 대해 mergeMap을 사용합니다. MergeMap을 사용하면 이전 Observable이 완료될 때까지 다음 Observable을 시작할 필요가 없도록 만듭니다. 여기서 중요한 이유는 각 게시물을 기다릴 필요가 없고, 모든 게시물이 완료되었을 때만 신경을 써야하기 때문입니다;
- 이 mergeMap은 코멘트를 가져오고, 코멘트를 가져온 후에는 모든 값을 매핑하여 출력 객체를 생성합니다;
- 그 후에는 toArray 메서드가 있습니다. 이 메서드는 Observable에 의해 방출된 모든 값을 배열로 다시 그룹화합니다;
- 그 이후에는 값으로 UI를 채우는 문제만 남았습니다, 우리는 이를 템플릿 문자열과 tap 메서드를 사용하여 할 수 있습니다, 이를 side effect로 수행합니다;

최종 결과는 아래에서 확인할 수 있습니다:

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

Observables는 일대다 관계와 복잡한 데이터 흐름이 있는 시나리오에서 가장 잘 사용됩니다. 응용 프로그램에 더 많은 보일러플레이트 코드를 추가하는데 항상 그 가치가 있는 것은 아닙니다.

예를 들어, 복잡한 페이지네이션, 필터, 검색 및 기타 비동기 상호작용이 있는 프론트엔드를 구축할 때, 이벤트를 처리하고 필요한 변경 사항을 트리거하는 데 Observable을 사용하면 상태를 더 예측 가능하게 만들고 관리하기 쉬워집니다.

단순한 HTTP 로그인 호출을 Observable로 변환하는 것은 간단한 메소드가 로직을 처리할 수 있을 때 과하게 느껴질 수 있습니다.

핵심은 응용 프로그램 내에서 대가 균형을 평가하고 그에 따라 실행하는 것입니다. 조심히 사용해주세요.

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

## 고지사항

## 디자인 패턴에 대해 더 알아보기

## 링크

저를 팔로우하세요: https://medium.com/@dgramaciotti

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

LinkedIn: [다니엘 귀데스](https://www.linkedin.com/in/daniel-guedes-79a05a176/)

GitHub: [dgramaciotti](https://github.com/dgramaciotti)

이 글이 마음에 드셨나요? 좋아요를 눌러주시고 소셜 네트워크에서 공유해주세요!