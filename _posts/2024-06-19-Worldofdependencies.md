---
title: "의존성의 세계"
description: ""
coverImage: "/assets/img/2024-06-19-Worldofdependencies_0.png"
date: 2024-06-19 10:47
ogImage:
  url: /assets/img/2024-06-19-Worldofdependencies_0.png
tag: Tech
originalTitle: "World of dependencies"
link: "https://medium.com/gitconnected/world-of-dependencies-4639100d16ef"
---

<img src="/assets/img/2024-06-19-Worldofdependencies_0.png" />

아직 어리기는 했지만, Y2K 버그를 생생히 기억합니다. 친숙하지 않은 분들을 위해 설명드리자면 (놀랍게도!), Y2K 버그는 컴퓨터 시스템에서 날짜가 저장되는 방식에 연결된 문제였습니다. 이는 '98'을 1998년으로 나타내는 두 자리 연도 표기법의 널리 사용에서 비롯되었습니다.

따라서 2000년이 다가오자, 컴퓨터 시스템이 '00'을 1900년이 아닌 2000년으로 오해할 수 있음을 우려했습니다. 이는 모든 컴퓨터 시스템이 사실상 100년 전으로 되돌아갈 수도 있어, 상당한 혼란을 초래할 수 있었습니다.

그 시대의 개발자들은 이 문제를 해결하는 데 일을 했습니다. 그들은 주로 COBOL과 같은 언어를 사용했는데, 이 언어는 goto 문을 자주 사용했습니다.

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

`goto`문은 프로그램이 코드의 다른 위치에있는 레이블로 "점프"할 수 있도록했습니다.

여기서 우리는 프로그램이 start_loop 레이블로 "점프"하는 것을 볼 수 있습니다.

"goto"를 사용하는 것은 종종 많은 의존성을 유발하는 코딩 접근 방식을 촉진합니다:

![의존성 이미지](/assets/img/2024-06-19-Worldofdependencies_1.png)

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

우리가 볼 수 있듯이, 내일 날짜를 출력하는 간단한 예제조차도 많은 종속성이 있습니다.

날짜와 관련된 코드는 프로그램 전체에 흩어져 있었고 여러 번 중복되었습니다.

버그를 수정하는 것이 비용이 많이 발생하고 어려운 이유 중에는 goto 코드 내에 많은 종속성이 있었기 때문입니다.

개발자들은 많은 위치를 수정해야 했고, 그들이 가한 모든 변경사항이 시스템 전체에 영향을 미쳤습니다.

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

최종적으로 이야기는 긍정적으로 끝이 났어요: Y2K 버그가 당시 프로그래머들의 노력으로 성공적으로 방지되었어요. 하지만, 이 모든 일은 훨씬 간단히 처리될 수도 있었어요.

의존성이 중요한 역할을 하는 것은 사실이지만, 그것들을 어떻게 식별할 수 있을까요?

# 의지를 식별하는 것은 반가워요

스파게티 코드를 쓰는 것을 방지하기 위해서, 먼저 의존성이 무엇인지를 이해하는 것이 중요해요.

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

이 문서에서는 여러 정의가 있지만 이 기사에서는 다음 정의를 채택할 것입니다: 의존성은 특정 코드 조각이 독립적으로 이해하거나 수정할 수 없을 때 발생합니다. 코드는 다른 코드와 어떤 방식으로든 관련이 있으며, 만약 주어진 코드가 변경된다면 다른 코드도 고려하거나 수정해야 할 수 있습니다.

다음 예제에서 의존성을 식별해 봅시다:

만약 calculateTotal의 인터페이스를 수정하여 매개변수를 추가한다면, 함수를 호출하는 모든 부분을 그에 따라 업데이트해야 합니다.

CartItem 인터페이스를 가격이 아닌 id만 가지도록 변경한다면 calculateTotal 구현을 변경해야 합니다.

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

이것은 몇 가지 분명한 의존성입니다: 우리가 함수를 만들 때마다, 그 구현과 호출 코드 사이에 의존성을 불문불답(묵시적으로) 만든다.

우리가 다음 예시에서 볼 수 있는 덜 명백한 의존성도 있습니다.

우리는 초콜릿을 만드는 조립 라인을 프로그래밍 중입니다. 공장에서 초콜릿을 만드는 과정은 다음 클래스에 설명된 것처럼 구분된 단계로 이루어져 있습니다:

첫눈에는 각 함수 호출이 독립적으로 작동하는 것처럼 보일 수 있습니다. 그러나 실제로 그렇지 않습니다.

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

우리는 초콜릿 콩을 수확하고 발효시키기 전에 그들을 말릴 수 없습니다.

우리는 수확하고 발효시키기 전에 dryAndRoastCocoaBeans를 호출할 수 없습니다.

우리는 순차 의존성의 예시를 방금 보았어요. 순차 의존성은 프로그램이 올바르게 작동하기 위해 일련의 작업이나 작업의 올바른 실행 순서가 중요한 상황을 말합니다.

또 다른 명백하지 않은 종속성을 발견하기 위해 또 다른 예시를 사용하겠습니다.

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

강아지 자선 웹사이트를 위해 매력적인 웹페이지를 만들고 있는 중이에요. 이미 몇 가지 다른 페이지를 만들었어요.

자선에게 모든 페이지에 동일한 로고를 보여주길 원했기 때문에 모든 페이지로 로고를 복사했고, 모든 사람이 만족했어요.

시간이 지난 후, 자선은 한 페이지의 로고를 바꾸기로 결정했어요. 아쉽게도, 로고를 업데이트하기 위해 수작업으로 각 페이지를 작업해야 했어요.

로고를 도입하면 모든 페이지 사이에 종속성이 생겼어요. 즉, 한 페이지에서 로고를 바꾼다면 다른 모든 페이지도 동일하게 업데이트해야 했어요.

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

<img src="/assets/img/2024-06-19-Worldofdependencies_2.png" />

의존성을 식별한 후에는 이를 해결하는 시간입니다 (마침내!).

# 명백하게 만들기

자선사업 예제로 돌아가 봅시다. 간단한 해결책이 있습니다: 의존성을 명시적으로 만드는 것입니다.

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

<img src="/assets/img/2024-06-19-Worldofdependencies_3.png" />

각 페이지는 logoAPI(또는 다른 공유 리소스)를 사용하여 로고를 검색합니다.

이제 페이지들은 서로 격리되어 있어 종속성의 수가 크게 줄어들었습니다.

우리는 다른 로고를 갖는 페이지를 추가할 때 걱정할 필요가 없습니다. 더불어 로고를 업데이트해야 할 경우에는 logoAPI만 수정하면 됩니다. (큰 성공!)

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

알기 어려우며 관리하기 어려운 종속성을 간단하고 명확한 것으로 대체했습니다.

좀 더 많은 종속성을 제거합시다!

# 상태 기계

초콜릿 공장 예제로 돌아가서 순차적 종속성을 관찰해봅시다.

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

이 코드에서 각 단계는 발생하기 위해서는 모든 이전 단계에 종속되어 있습니다. 그렇지 않으면 작동하지 않을 것입니다.

우리의 의존성 그래프는 다음과 같습니다:

![의존성](/assets/img/2024-06-19-Worldofdependencies_4.png)

순차적 의존성 해결 방법 중 하나는 상태 기계를 구현하는 것입니다. 상태 기계는 서로 다른 상태와 그 상태들을 연결하는 전이로 구성됩니다.

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

초콜릿 제조 과정에 여러 상태를 정의할 수 있습니다. 하나의 상태만이 언제나 현재 상태가 됩니다.

![image](/assets/img/2024-06-19-Worldofdependencies_5.png)

상태 간 전환은 특정 이벤트나 트리거를 통해서만 가능하며 다른 방법으로는 불가능합니다.

우리는 상태 머신을 구현하고 코드를 그에 맞게 리팩터링할 것입니다:

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

우리는 현재 상태에서 다음 상태로 전환하는 기능 하나만 노출했습니다.

서로 다른 단계들 간에 의존 관계가 있지만, 상태 머신을 사용하여 내부적으로 관리되므로 외부 세계에 노출되지 않음을 반드시 기억해 주세요.

<img src="/assets/img/2024-06-19-Worldofdependencies_6.png" />

저희 의존성 그래프가 훨씬 나아 보입니다!

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

다른 중요한 장점은 소비자의 코드를 수정하지 않고 초콜릿 공장 내에서 프로세스를 추가하거나 제거할 수 있다는 것입니다.

상태 머신을 사용하는 것을 망설이지 마세요. 그것들은 환상적이고 사용하기 쉽며 구현하기 쉽습니다 (또는 기존 라이브러리를 사용하세요 :)).

의존성 제거를 위한 또 다른 방법을 살펴보겠습니다.

# 전역 데이터 피하기

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

이 코드 스니펫에는 어떤 종속성이 있나요?

![image](/assets/img/2024-06-19-Worldofdependencies_7.png)

addItemToCart 및 removeItemToCart 함수는 명백히 globalShoppingCart 배열에 의존합니다.

처음에는 코드가 원할하게 작동했습니다. 그러나 새로운 기능 요청이 발생했습니다. 고객이 가격 비교를 위해 두 번째 카트를 유지할 수 있도록 하는 것입니다.

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

우리가 처음에 생각해낸 이 끔찍한 해결책은 이랬어요:

우리의 업데이트된 의존성 그래프는 여기 있어요:

![의존성 그래프](/assets/img/2024-06-19-Worldofdependencies_8.png)

의존성이 두 배로 증가했고, 코드를 유지보수하기가 훨씬 어려워졌어요.

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

두 번째 카트 기능의 석공을 보고, 고객들이 이제는 자신의 요구에 맞는 여러 카트를 만들 수 있는 옵션을 요청하고 있습니다.

기존 코드의 꽉 끼인 성격을 감안할 때, 이 기능을 구현하는 것은 불가능해졌고, 따라서 완전히 다시 작성하기로 결정했습니다.

새로운 의존성:

![의존성 그림](/assets/img/2024-06-19-Worldofdependencies_9.png)

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

이러한 함수들은 카트의 특정 인스턴스에 의존하지 않고 인터페이스(배열이라는 사실)에만 의존하기 때문에 종속성의 수가 크게 줄어들었습니다.

마지막으로, 저희 고객들은 원하는 만큼의 카트를 가질 수 있습니다.

이 예시들이 간단해 보일 수 있지만, 더 복잡한 시나리오에서는 전역 또는 가변 데이터에 대한 의존성이 쉽게 간과될 수 있어 코드 유지보수에 상당한 어려움을 초래할 수 있습니다.

결국, 이러한 종속성들은 우리를 코드 유지보수가 어려운 코드를 작성하거나 전면적인 재작성을 해야 하는 상황으로 이끌기도 합니다. 따라서, 가변성보다는 불변성, 그리고 전역 변수 대신 지역 변수를 우선시하는 것이 더 나은 방향입니다.

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

다음은 캣 프로필 UI, 랭킹 알고리즘 및 데이터베이스로 구성된 시니어와 고양이를 연결하기 위한 스타트업의 핵심 구성 요소입니다:

- 캣 프로필 UI: 현재 브라우징하는 시니어 사용자의 매칭 기준에 따라 고양이 프로필을 전시하는 웹페이지입니다.
- 랭킹 알고리즘: 라이프스타일, 에너지 수준 및 선호도 등을 고려하여 시니어와 고양이 간의 호환성을 평가하는 알고리즘입니다.
- 데이터베이스: 사용자 데이터(사용자 프로필, 선호도, 이력 관리) 및 고양이 데이터(건강 기록, 행동, 입양 상태 정보 등)를 저장하고 관리합니다.

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

![이미지](/assets/img/2024-06-19-Worldofdependencies_10.png)

UI는 API를 통해 데이터베이스에서 현재 시니어 정보와 고양이 프로필을 가져옵니다.

UI는 API를 통해 랭킹 알고리즘에 액세스하고 시니어 데이터를 가져와 고양이 목록을 생성합니다.

데이터베이스에서 고양이 목록을 가져온 후, 랭킹 알고리즘은 정렬하여 UI로 다시 보냅니다.

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

시니어 및 고양이가 함께 시간을 보내며 애플리케이션의 성공으로 새로운 요구사항이 발생했습니다: 모바일 애플리케이션 개발이 필요해졌어요.

안타깝게도 UI가 데이터베이스에 직접 액세스하고 비즈니스 로직을 포함하고 있어, 모바일 앱과 같은 다른 UI 구성 요소를 통합하는 것이 어려웠어요.

결과적으로, 우리는 작은 리팩토링을 하기로 결정했고, 계층적 접근 방식을 사용하기로 했어요.

계층적 접근 방식에서는 애플리케이션을 서로 구분되는 계층으로 나누어 각각이 아래 계층에 대한 추상화 수준을 제공하도록 했어요.

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

전통적인 계층 구조 접근 방식은 애플리케이션을 세 가지 계층으로 분할합니다: 데이터 계층, 비즈니스 로직 계층 및 UI 계층.

각 계층은 특정 관심사에 특화되어 있으며 그 아래의 계층과 직접 통신하는 것만 허용됩니다.

이 접근 방식을 적용하면 의존성은 다음 구조와 유사할 것입니다:

![의존성 구조](/assets/img/2024-06-19-Worldofdependencies_11.png)

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

이 예시를 통해 AWESOME한 만큼 더 적은 종속성을 가지고 있으면서도 변경에 적응할 수 있는 사례를 확인할 수 있습니다 — 예를 들어, 모바일 UI의 추가와 같은 경우입니다.

![이미지](/assets/img/2024-06-19-Worldofdependencies_12.png)

우리는 동일한 비즈니스 로직을 유지함으로써 모바일 UI를 신속하게 통합할 수 있게 되었습니다.

하지만, 모든 게 환히 빛나고 무지개로 둘러싸인 것은 아닙니다; 계층화된 디자인 패턴을 사용하는 것에는 오버헤드와 복잡성이 증가하는 등 일부 단점이 있습니다.

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

애플리케이션으로 돌아오면, 모바일 앱이 추가되어 모두를 기쁘게 했지만 한 사람을 제외하고는—우리 상사를 말이죠.

우리 상사가 회사의 데이터베이스 비용에 대해 우려를 표명하며 더 비용 효율적인 대안으로 교체하고자 한다는 욕구를 표현했습니다.

불행히도, 우리의 비즈니스 로직은 데이터베이스와 긴밀하게 결합되어 있어서 직접 그 함수들을 호출합니다 :(.

우리는 종속성 주입을 활용하여 이번에는 또 다른 리팩터링을 시도하기로 결정했습니다.

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

# 의존성 주입

의존성 주입에 대해 Stack Overflow의 좋은 인용구로 설명을 시작하겠습니다.

우리 예제에서 비지니스 로직은 5세 아이를 나타내며, 데이터베이스는 직접 호출하는 냉장고와 유사합니다.

![의존성](/assets/img/2024-06-19-Worldofdependencies_13.png)

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

어떻게 하면 엄마와 아빠를 끌어들여서 우리를 도와주고 데이터베이스에서 데이터를 검색할 수 있을까요?

먼저 의존성 주입(DI)의 핵심 개념을 파악해봅시다:

인터페이스를 전기 소켓으로 생각할 수 있습니다.

![이미지](/assets/img/2024-06-19-Worldofdependencies_14.png)

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

셀프설명서에 따르면 소켓에 무언가를 꽂을 때는 우리의 플러그가 소켓의 정확한 형태와 일치해야 합니다. 여기서 소켓은 우리의 인터페이스 역할을 합니다.

또한, 같은 소켓에 많은 다른 가전제품을 연결할 수 있거나 말그대로 많은 구현을 가질 수 있습니다.

![image](/assets/img/2024-06-19-Worldofdependencies_15.png)

이 소켓은 전기 가전제품을 전기 시스템에서 분리합니다.

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

전기 가전제품을 직접 벽에 연결해 놓았다면 교체하는 것이 얼마나 어려울지 생각해보세요. 그에 비해 콘센트에 연결되어 있을 때는 얼마나 손쉬운지 생각해보세요.

이전 예시로 돌아와서, 데이터베이스에 직접 의존하는 대신 인터페이스에 의존할 것입니다.

![이미지](/assets/img/2024-06-19-Worldofdependencies_16.png)

이제 인터페이스를 준수하는 여러 가지 구현체를 가질 수 있습니다.

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

<img src="/assets/img/2024-06-19-Worldofdependencies_17.png" />

다음 단계는 직접적으로 의존하지 않고 구체적인 구현을 얻는 것입니다.

의존성 주입의 이 측면은 실제로 복잡하며, 여러 책들이 이에 대해 쓰여졌습니다.

하지만 간단한 예제로 가장 직관적인 접근 방법을 보여드리겠습니다:

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

먼저, createCatsRanking 함수가 awsDataProvider 또는 azureDataProvider와 같은 구체적인 구현에 의존하지 않는다는 것을 알아봅시다.

대신, DataProvider 인터페이스에 의존하며, 함수 매개변수를 통해 주입되므로 DataProvider의 모든 구현과 함께 작동할 수 있다는 것을 의미합니다.

이 접근 방식을 통해 우리는 선택한 임의의 데이터베이스를 원활하게 활용할 수 있으며, 궁극적으로 우리 상사를 기쁘게 할 수 있습니다!

# 요약

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

저희는 역사상 가장 비싼 버그 중 하나를 탐구하면서 코드 의존성과의 연관성을 살펴보며 여정을 시작했습니다.

그 다음으로, 우리는 의존성과 결합을 정의하고, 이를 식별하여 발생할 수 있는 문제에 대처하는 방법에 대해 탐구했습니다.

소프트웨어에서 의존성을 관리하기 위한 다양한 전략을 살펴보았는데, 이는 의존성을 명시적으로 만들기, 상태 기계 활용, 전역 데이터 피하기, 계층 구조화, 의존성 주입 등이 있었습니다.

이 목록이 완벽하진 않지만, 이 글에서 가장 중요한 교훈은 항상 의존성을 고려해야 한다는 것입니다.

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

성공적인 소프트웨어는 변화에 적응할 수 있어야 한다는 것을 기억해야 합니다. 계속해서 새로운 기능을 추가하고 기존 기능을 확장해야 하므로, 종속성을 고려하고 느슨하게 결합된 코드를 작성하는 것이 이 목표를 달성하는 데 큰 도움이 될 수 있습니다.

코드를 분리해야 하는 부분과 그렇게 하지 말아야 하는 부분을 고려해야 합니다. 너무 많은 추상화와 확장 지점을 추가하면 복잡한 코드가 생성되어 관리하기 어려운 악몽이 될 수 있습니다.

결국, 중요한 것은 적절한 균형을 찾고 언제 어떤 것이 다른 것에 의존해야 하는지 결정하는 것입니다.

즐거운 코딩하세요!

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

# 참고문헌
