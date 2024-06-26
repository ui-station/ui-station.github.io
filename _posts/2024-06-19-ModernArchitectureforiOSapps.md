---
title: "iOS 앱을 위한 현대적 아키텍처"
description: ""
coverImage: "/assets/img/2024-06-19-ModernArchitectureforiOSapps_0.png"
date: 2024-06-19 11:02
ogImage:
  url: /assets/img/2024-06-19-ModernArchitectureforiOSapps_0.png
tag: Tech
originalTitle: "Modern Architecture for iOS apps"
link: "https://medium.com/@antony.karpov/modern-architecture-for-ios-apps-7a791439f9e3"
---

![이미지](/assets/img/2024-06-19-ModernArchitectureforiOSapps_0.png)

많은 iOS 개발 측면 중 앱의 아키텍처는 가장 중요하고 흥미로운 주제 중 하나입니다. 아키텍처는 규칙 세트가 아닌 시스템의 속성으로, 앱이 작동하는 방식 뿐만 아니라 성장과 평가 방식을 정의합니다. 이는 앱의 모든 부분에 영향을 미치며 개발 프로세스와 최종 제품의 품질에도 직접적인 영향을 미칩니다.

거대한 코드베이스와 강하게 결합된 구성 요소가 보편적이던 시절은 지나갔습니다. 이제 모바일 우선 접근 방식이 이긴 상황이고, 오늘날 모바일 응용 프로그램은 진지하고 매우 경쟁력있는 비즈니스입니다. 결과적으로 고객의 높은 요구 사항을 충족하기 위해 더 모듈화되고 테스트 가능하며 견고한 코드베이스를 만들기 위해 정교한 아키텍처 패턴을 활용합니다.

본문에서는 좋은 아키텍처가 어떻게 보여야 하는지와 그 이유, 도래하는 혜택 및 최대한 활용하기 위해 현대 기술 스택을 활용하여 어떻게 구축해야 하는지에 대한 경험을 공유하고 있습니다.

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

- 제가 언급한 방법이 유일한 올바른 방법이라고 주장하지는 않습니다. 소프트웨어 개발에서는 문제를 해결할 수 있는 다양한 방법이 일반적으로 있으므로, 다른 해결책이 잘 작동하고 만족스러운 경우에는 괜찮습니다.
- 이 방법은 팀 또는 여러 팀의 개발자들이 개발한 상대적으로 큰 앱에 가장 적합합니다.
- 이 글은 iOS 개발을 중점적으로 다루지만, 처음 봤을 때 안드로이드 개발에도 큰 노력 없이 도입될 수 있을 것으로 보입니다.

# 좋은 아키텍처란 무엇인가요?

실제 앱에 적합한 좋은 아키텍처를 정의해 봅시다. 이에 대해 약간 다른 의견이 많이 있지만, 일반적으로 다음 요구 사항에 초점을 맞춥니다:

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

- 쉽게 읽고 이해하기 쉽습니다.
- 재사용, 확장 및 확장하기 쉽습니다.
- 테스트하고 테스트가 저렴합니다.
- 쉽게 디버그하고 수정하며 유지 관리하기 쉽습니다.
- 새로운 개발자를 온보딩하기 쉽습니다.

많이들 '쉽다'라고 하는데, 해결책은 최소한의 부가 코드를 가지고 끝내야 하며, 엔지니어링은 너무 많이 하지 않아야 쉽다고 할 수 있습니다. 동시에 개발 프로세스에서 매우 효율적이어야 합니다. 또 한 가지 중요한 특징을 더할 거면, 그것은 놈, 구조는 부적절하게 사용되는 것에 저항할 정도로 충분히 강력해야 합니다. 예를 들어, MVC는 잘못 사용하기 매우 쉬우며, 그것이 바로 우리가 "Massive ViewController"라고 하는 반패턴을 갖게 된 이유입니다 (대신에 "Model-View-Controller"). 좋은 아키텍처는 스스로를 보호하고 기반이 되는 원칙을 지켜야 합니다.

반면에, 아키텍처는 다소 융통성 있으면서 지나치게 과도하지 않아야 합니다. 소프트웨어 개발은 순수한 수학 추상화가 아니며, 앱은 실제 사람들이 개발합니다 (실수를 할 수 있음), 그리고 실제 사람들이 (아키텍처에 대해 전혀 신경쓰지 않을 수도 있는) 실제 하드웨어에서 앱을 사용합니다 (이에는 제한이 있을 수 있음), 종종 3rd party 프레임워크를 사용하며 (이것들은 때로는 우리의 제어 하에 있지 않을 수도 있음). 앱을 외부 환경과 조건에 쉽게 적응시키는 것이 상대적으로 쉬워야 합니다. 다시 말해, 좋은 아키텍처는 가능한 한 간단해야 하고, 필요한 만큼 복잡해야 합니다.

# 아키텍처에 대해 더 자세히 알아보기

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

아키텍처는 일종의 타협안이며, 결과가 따라온다는 것을 명심해야 해요. MVC, MVP, MVVM과 같은 이러한 아키텍처 패턴들은 매우 간단하고 짧은 학습 후에 쉽게 사용을 시작할 수 있어요. 하지만 이러한 간단함의 반대편은 이러한 패턴들이 보통 코드베이스의 분리 방법을 설명하기 때문에 실제로 해석과 구현이 상당히 다르다는 사실이에요. 결과적으로 각 팀은 레이어 사이의 분리를 유지하기 위해 자체 규칙을 가지고 있으며, 이러한 규칙은 일반적으로 쉽게(실제로는 아마도) 어기기 쉽다는 것이죠. 이러한 패턴의 전형적인 문제는 거대한 비즈니스 로직 레이어인데, 이는 레이어 간의 명확한 경계가 없으면 의존성 주입 없이도 서비스 레이어 및 다른 엔티티를 직접 사용하는 경향이 있어서 제대로 테스트하기 어렵다는 점이에요.

RIBs와 VIPER와 같은 고급 아키텍처 패턴도 있지만, 둘 다 기술적으로 낡았으며 UIKit과 함께 작동하도록 개발되었으므로 SwiftUI와는 잘 작동하지 않아요. 게다가 VIPER는 혜택이 그리 크지 않으면서도 많은 보일러플레이트 코드를 가지고 있어요. RIBs는 여전히 UIKit만을 사용하고 SwiftUI를 사용할 계획이 없다면 좋은 아키텍처일 수 있지만, 실제로는 어렵다고 보여요 — SwiftUI로만 구현할 수 있는 일부 새로운 iOS 기능들이 있을 수 있다는 점이죠.

구성 가능 아키텍처(TCA)는 아마 SwiftUI와 함께 작동할 수 있도록 구상된 유일한 아키텍처일 거예요. 주요 단점은 애플리케이션 전체에 하나의 Scope를 사용하는 것이 권장된다는 것이어서, 앱의 한 부분을 다른 부분과 분리하기 어려운 점이 있어요. 이는 앱이 MVP 이상이거나 여러 개발자 팀에 의해 개발되는 경우에 중요하답니다. 또한, 몇몇 곳에서 TCA의 구현은 상당히 범위가 크게 보일 수 있고(많은 사용자 정의 내장 도구)과 오버 엔지니어링(보일러플레이트 코드)에 대한 우려도 있어요. 또한, 많은 사람들이 가파른 학습 곡선과 성능 문제에 대해 언급하고 있어요. 그렇지만 SwiftUI를 사용해야 하고 어떤 종류의 아키텍처가 필요한지 감이 오지 않는다면, TCA가 좋은 시작점이 될 수 있어요.

## 현대 소프트웨어는 팀워크입니다.

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

현대의 대부분의 경쟁력 있는 모바일 앱은 단독으로 개발되는 것이 아니라, 경우에 따라 수십 개의 팀에 의해 개발됩니다. 물론 시장에는 많은 독립 개발자들이 있지만, 대부분의 경우 비즈니스 앱과는 조금 다릅니다.

팀워크는 앱이 병렬 개발을 허용하고 쉽게 수행해야 한다는 것을 뜻합니다. 또한, 각 앱의 부분은 많은 개발자에 의해 개발되어야 합니다. 이는 아키텍처가 코드의 각 특정 부분이 어디에 위치해야 하며 어떻게 구현되어야 하는지에 대한 엄격한 규칙이 있어야 한다는 것을 의미합니다. 또한, 아키텍처는 일관성 있는 높은 수준을 가져야 합니다. 그렇지 않으면 한 개발자가 다른 개발자의 작업을 이어 나가지 못할 것입니다. 이 모든 것을 고려하면 아키텍처는 탁월한 수준의 테스트 가능성을 제공해야 합니다. 집중적인 개발의 경우, 아키텍처는 인식 및 이해 부담을 증가시키지 않으면서 수평적 확장성의 좋은 수준을 제공해야 합니다.

## 두 수준의 아키텍처

보통 사람들은 이에 대해 많이 이야기하지 않지만, 잘 구조화된 모듈화 수준이 높은 앱은 두 수준의 아키텍처를 갖고 있습니다. 첫 번째 수준은 건물 블록과 같은 것으로, MVC, MVP, MVVM 등과 같은 하나의 특정 화면 또는 해당 일부 (복잡한 화면의 경우)를 구축하는 책임이 있는 아키텍처입니다. 두 번째 수준은 모든 건물 블록을 조합하고 이를 위해 모든 필요한 종속성을 제공하며 앱의 다양한 부분 간의 라우팅을 포함하는 아키텍처를 담당합니다.

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

중간 규모 이상의 모바일 앱을 위해서는 좋은 앱 아키텍처에 대한 위에서 언급한 모든 요구 사항을 충족시키기 위해 두 가지 아키텍처가 필요합니다. 예를 들어, 이 두 번째 아키텍처 없이는 앱의 각 부분에 대해 명확하고 강력한 경계를 설정하는 것이 불가능하며, 가로 확장성, 테스트 가능성, 디버깅 및 유지 관리와 같은 것들을 달성하는 것이 극도로 어려울 것입니다. 이 역할을 수행하기에 가장 적합한 후보 중 하나는 "Composition Root" 패턴입니다. 이에 대해 이 글에서 나중에 자세히 설명하겠습니다.

# Elm 아키텍처

Elm 아키텍처는 웹 개발에서 잘 알려져 있지만, 모바일 개발을 포함한 다른 영역에 대해 거의 고려되지 않습니다. 명백히 웹을 위해 개발된 아키텍처를 모바일 개발에 그대로 사용하는 것은 불가능합니다. 그러나 최고의 아이디어를 가져와서 이를 모바일 특이사항에 맞게 확장하고 동작하는 프로토타입을 구현하는 것은 가능합니다.

## Elm 아키텍처란 무엇인가요?

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

어떤 Elm 프로그램도 항상 다음과 같은 요소로 구분됩니다:

- 앱의 현재 상태를 유지하는 Model
- Model을 사용자 인터페이스(UI)로 변환하는 View
- UI에서 발생하는 이벤트인 Message
- Message를 기반으로 새로운 Model을 생성하는 Update

![Elm Program Structure](/assets/img/2024-06-19-ModernArchitectureforiOSapps_1.png)

이를 기반으로, 몇 가지 매우 중요한 결과가 있습니다:

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

- 모델은 주로 모바일 개발에서 State로 명명되며, 앱의 현재 상태나 현재 화면 등을 나타내는 데 필요한 모든 것을 유지하는 단일한 진실의 원천입니다.
- 뷰는 모바일 개발에서는 뷰 빌더라고하는 순수한 함수인 뷰로, 상태를 입력으로 받고 사용자 인터페이스를 출력으로 하는 기능입니다.
- 메시지 또는 이벤트는 사용자(또는 앱의 다른 부분)이 보낼 수 있는 한정된 집합이며, 여기서 키워드는 한정된 것이므로 예측할 수 없는 것이 오는 것은 불가능합니다.
- 업데이트 함수 또는 리듀서는 현재 상태와 새 이벤트를 입력으로 받고 새 상태를 출력으로 하는 순수한 함수입니다. 이는 상태가 변경될 수 있는 유일한 장소입니다.

실제로 앱의 모든 부분은 고립되어 있지 않고 다른 부분들과 협력하여 작동하며 몇 가지 종속성이 있습니다. 이 모든 것은 일종의 환경입니다. 또한 각 이벤트는 상태를 변경할 수 있는 몇 가지 부작용을 일으킬 수 있습니다. 사실, 새로운 상태는 현재 상태에 이벤트의 부작용을 적용한 결과물입니다. 그리고 사용자 인터페이스로부터 이벤트를 받아오기 위한 일종의 추상화, 즉 사용자 이벤트를 생성할 환경인 "UI 피드백"이 필요합니다.

![image](/assets/img/2024-06-19-ModernArchitectureforiOSapps_2.png)

예를 들어 사용자가 Pull-to-Refresh 제스처를 수행하면 "사용자가 업데이트 요청함" 이벤트가 리듀서 함수로 전달됩니다. 리듀서는 첫 번째 부작용 "로딩이 시작됨"을 생성하고 새 데이터를 가져 오기 위해 네트워크 API 호출을 수행합니다. 그런 다음 API 호출이 완료되면 리듀서는 두 번째 부작용 "로딩이 중지됨"을 생성합니다. 세 번째 부작용은 API 호출의 결과에 따라 다르며, 성공하는 경우 리듀서는 "새로운 데이터가 도착함" 효과를 생성하고, 그렇지 않으면 "오류가 발생함" 효과가 됩니다. 각 효과는 현재 상태에 적용되며 각 상태의 변경 후, 뷰 빌더가 사용자를 위해 업데이트된 실제 UI를 생성합니다.

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

![이미지](/assets/img/2024-06-19-ModernArchitectureforiOSapps_3.png)

지금까지 우리가 가지고 있는 내용을 정리해 봅시다:

- State — 진실의 단일 출처
- Event — Reducer에 대한 외부(일반적으로 사용자 입력 또는 “앱이 비활성 상태가 되었음”과 같은 시스템 이벤트) 액션
- Effect — 내부 액션, State를 변경하는 이유
- Environment — 이벤트를 이펙트로 변환하는 데 도움이 되는 모든 필수 의존성
- UI-피드백 — 이벤트를 생성하는 함수
- Reducer — Event를 Environment와 현재 State에 의해 Effect로 변환하는 "이벤트 핸들러"와 현재 State에 사이드 이펙트를 적용하여 새로운 State를 생성하는 "이펙트 핸들러" 두 가지 순수 함수를 포함하는 유한 상태 머신

처음에는 조금 복잡해 보일 수 있지만, 실제로 사용하는 데 매우 쉽습니다. 특정 엔티티의 한정된 세트가 있고 각각이 특정 작업에만 책임 있기 때문입니다. 일방향 흐름이기 때문에 이를 따르고 무슨 일이 일어나고 있는지 이해하는 것이 쉽습니다. 대부분의 로직은 순수 함수이며, 진실의 단일 출처이며, 비즈니스 및 프리젠테이션 로직이 확실히 분리되어 있습니다. 이 모든 것을 고려할 때, 이 접근 방식은 정말 높은 수준의 테스트 가능성을 가지고 있습니다.

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

# 구현

iOS를 위한 Elm 아키텍처의 공식적인 구현은 없지만 적어도 두 가지 매우 좋은 시도가 있습니다:

- RxFeedback: 기술적으로 오래되었으며 이벤트/이펙트를 분리하지 않고 SwiftUI 지원이 없습니다.
- The Composable Architecture (TCA): 하나의 scope를 사용하며 앱의 다른 부분 간에 명확한 경계가 없으며 이벤트/이펙트를 분리하지 않습니다.

왜 이벤트/이펙트를 분리해야 하는 이유일까요? — 비즈니스 로직의 보호를 위해서입니다. 시스템에 이 분리가 없다면 시스템은 외부에서 내부 이펙트로 확장될 수 있는 공개 이벤트를 가로채고 로직을 망가뜨릴 수 있습니다.

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

왜 명확한 경계가 중요한가요? — 앱을 작은 독립적인 조각으로 분할하는 가능성은 중요합니다. 전체 코드베이스를 개별 패키지와 타겟으로 분리하면 적은 결합도와 각 부분의 제한된 범위가 유지되어 테스트 가능성, 디버깅 용이성 및 유지 관리에 필수적인 좋은 수준을 유지할 수 있습니다.

## 기술 스택

기술적 측면에서 iOS 개발은 멈춰있지 않고 지속적으로 발전하고 있으며, 최근 몇 년 동안을 돌아보면 다음 몇 단계를 구분할 수 있습니다:

- UIKit + GCD (iOS 9–12): iOS 개발의 기술 스택은 주로 Grand Central Dispatch (GCD) 및 Operation Queue와 함께 작동하는 UIKit으로 구성되어 있었으며 (일부는 스토리보드를 사용했지만, 보다 고급화된 개발자들은 UI를 프로그래밍적으로 구현했습니다).
- UIKit + RxSwift (iOS 10–13): 반응형 프로그래밍의 등장: MVVM과 UI 바인딩은 제게는 새로운 것이 아니었습니다. 이전에 Microsoft Windows Phone에서 사용해본 적이 있었는데, 대부분의 모바일 개발자들에게는 마음을 바꾸는 혁명같은 것이었습니다.
- UIKit + Combine (iOS 13–16): Apple이 마침내 원시적인 반응형 프레임워크인 Combine을 제시했는데, RxSwift와 같은 일부 영역에서는 그만큼 강력하지 않지만, 일상적인 루틴에 충분히 좋으며 여전히 대부분의 앱에서 활발히 사용되고 있습니다.
- SwiftUI + Async/Await (iOS 15+): 네이티브 SwiftUI를 사용한 선언적 UI의 새 시대의 시작으로 처음에는 충분히 안정적이지 않았지만, 매 iOS 릴리스마다 점점 더 안정되고 매력적으로 변화했습니다. Async/Await는 여러 해 동안 많이 원하던 언어 기능이 드디어 출시되었으며 구현이 훌륭하고 강력합니다. 여전히 개선 중이지만, 확실히 제작용으로 완성되었으며 적어도 다음 몇 년 동안 기술 표준이 될 것입니다.

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

애플리케이션이 잘 관리되고 리팩터링이 필요한 부분을 계속 수행하는 경우, 이러한 단계들은 서로 점진적으로 전환됩니다. 만약 앱이 iOS 16의 최소 배포 버전을 갖고 있지만 여전히 RxSwift를 적극적으로 사용 중이라면, 무언가 잘못되었을 가능성이 높으며 앱의 기술적 측면에 충분한 주의를 기울이지 않고 있는 것일 수도 있습니다.

오늘날, 인기 있는 많은 앱이 iOS 13 이하 버전을 지원하지 않기 때문에, 우리는 GCD를 사용한 쓰레드 수동 관리에 대해 이미 잊어도 좋을 것입니다(일부 필요한 드문한 경우를 제외하고) RxSwift 및 ReactiveX와 같은 경쟁자들에 대해서도 마찬가지입니다. 현재의 주류는 UIKit + Combine이지만, 내일의 주류는 SwiftUI + Async/Await입니다.

## 소스 코드

우리는 다 알다싶이 - "말은 싸다. 코드를 보여줘."라는 말처럼; Elm 아이디어를 기반으로 하고 SOLID와 Clean Architecture 원칙을 염두에 두어 오늘날 가장 인기있는 기술 스택인 Combine + UIKit으로 Elm 아키텍처를 내 비전대로 구현했습니다. 이 구현은 여기에서 찾아볼 수 있습니다: https://github.com/angryscorp/TEA-Combine.

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

이 구현을 쉽게 평가할 수 있지만 몇 가지 순간을 강조하고 싶습니다.

모든 개체는 구조체 및 열거형이며, 비즈니스 로직 레이어에 클래스가 없습니다. 이는 잠재적인 상속 문제 없이 더 나은 성능을 의미합니다.

```js
public struct SceneEnvironment {
    let getRandomNumber: () -> AnyPublisher<Int, Never>
    let selectSomething: () -> AnyPublisher<Int?, Never>
}

public struct SceneState: Equatable {
    var something: Int? = nil
    var currentValue = 0
    var counter = 0
}

public enum SceneEvent {
    case userDidRequestIncrease
    case userDidRequestDecrease
    case userDidRequestSomething
}

public enum SceneEffect {
    case resultDidReceive(Int)
    case somethingDidReceive(Int?)
}
```

어셈블리 함수는 부작용이 없는 순수 함수로, 현재 장면이 의존하는 모든 것을 쉽게 확인할 수 있습니다.

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
public struct MainScene {

    public static func create(
        setRootVC: (UIViewController) -> Void,
        selectSomething: @escaping (UIViewController) -> AnyPublisher<Int?, Never>
    ) {
        let vc = ViewController()

        let env = SceneEnvironment(
            getRandomNumber: { Just(Int.random(in: 1...9)).eraseToAnyPublisher() },
            selectSomething: { selectSomething(vc) }
        )

        TEA.start(
            initialState: SceneState(),
            environment: env,
            feedback: vc.bind,
            transform: SceneReducer.transform,
            apply: SceneReducer.apply
        ).store(in: &vc.subscriptions)

        setRootVC(vc)
    }
}
```

Reducer의 함수들도 순수하며, Event와 Effect가 단순히 열거형이기 때문에 이곳에서 switch 연산자를 효과적으로 사용할 수 있고, Event나 Effect에 대한 변경사항이 눈에 띄게 놓치지 않도록 합니다 (소스 코드가 컴파일 중지됩니다).

```js
public enum SceneReducer {

    public static func transform(
        state: SceneState,
        event: SceneEvent,
        env: SceneEnvironment
    ) -> AnyPublisher<SceneEffect, Never> {
        switch event {
        case .userDidRequestDecrease:
            return env.getRandomNumber()
                .map { SceneEffect.resultDidReceive(-$0) }
                .eraseToAnyPublisher()

        case .userDidRequestIncrease:
            return env.getRandomNumber()
                .map { SceneEffect.resultDidReceive($0) }
                .eraseToAnyPublisher()

        case .userDidRequestSomething:
            return env.selectSomething()
                .map(SceneEffect.somethingDidReceive)
                .eraseToAnyPublisher()
        }
    }

    public static func apply(
        state: SceneState,
        effect: SceneEffect
    ) -> SceneState {
        switch effect {
        case let .resultDidReceive(value):
            return .init(
                something: state.something,
                currentValue: state.currentValue + value,
                counter: state.counter + 1
            )

        case let .somethingDidReceive(value):
            return .init(
                something: value,
                currentValue: state.currentValue,
                counter: state.counter
            )
        }
    }
}
```

사용자 인터페이스와 비즈니스 로직을 연결하는 모든 것은 딱 한 가지 함수 뿐이며, 따라서 UI 부분을 다른 것으로 쉽게 대체할 수 있습니다.

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

func bind(state: AnyPublisher<SceneState, Never>) -> AnyPublisher<SceneEvent, Never> {
state
.receive(on: DispatchQueue.main)
.sink(receiveValue: { [unowned self] state in
self.somethingLabel.text = "Something: " + (state.something.map {"\($0)"} ?? "nil")
self.counterLabel.text = "Counter: \(state.counter)"
self.currentValueLabel.text = "Current value: \(state.currentValue)"

            })
            .store(in: &subscriptions)

        return eventSubject.eraseToAnyPublisher()
    }

환경은 모든 종속성 및 라우팅을 포함하며, 장면 관점에서 봤을 때, 서비스 종속성과 다른 화면에서 값을 가져오는 것 사이에 기본적인 차이가 없습니다. 두 종속성 모두 순수 함수로 표현할 수 있습니다.

public struct SceneEnvironment {
let getRandomNumber: () -> AnyPublisher<Int, Never>
let selectSomething: () -> AnyPublisher<Int?, Never>
}

## 추가 개발

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

하지만 시간은 멈추지 않고 Combine에서 Async/Await와 SwiftUI로 이동할 시간입니다. 여기 Elm 아이디어가 여기서도 잘 작동하는 증거입니다: https://github.com/angryscorp/TEA-AsyncAwait.

모든 것이 비슷하게 보이고 이미 익숙한데, 반응형 접근 방식이 새로운 Swift Modern Concurrency로 대체되어 더 간결하고 명확해졌습니다.

```swift
public enum ExampleReducer {

    public static func transform(
        state: ExampleState,
        event: ExampleEvent,
        env: ExampleEnvironment
    ) async -> ExampleEffect {
        switch event {
        case .increase:
            let newValue = await env.increment(state.currentValue)
            return .newValue(newValue)
        case .decrease:
            let newValue = await env.decrement(state.currentValue)
            return .newValue(newValue)
        }
    }

    public static func apply(
        state: inout ExampleState,
        effect: ExampleEffect
    ) async -> ExampleState {
        switch effect {
        case .newValue(let newValue):
            state.currentValue = newValue
            return state
        }
    }
}
```

모든 이점이 동일합니다:

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

- 리듀서를 위한 순수 함수
- 비즈니스 로직이 없는 뷰
- 상태는 단일 진실의 원천
- 이벤트와 효과는 그냥 열거형
- 적절한 종속성 주입
- 비즈니스 로직과 UI가 확연히 분리됨
- 응용 프로그램의 각 부분에 대한 명확한 경계
- 모든 것이 쉽게 테스트, 디버깅 및 유지 관리될 수 있음
- 강력하고 따르기 쉬운 구조

이러한 구현 방식들은 iOS 개발에도 뛰어난 성과를 보여주는 Elm 아키텍처가 잘 작동함을 예시로 제시한 것입니다. 물론 그대로 사용할 수 있지만, 이것을 초안으로 삼아 개선하거나 이 원칙을 기반으로 자체 솔루션을 구현하는 등 다양하게 활용할 수 있습니다.

# 테스트

이미 테스트 가능성에 대한 언급을 조금 했었지만, 이번에 한 번 더 테스트의 중요성을 강조하고 싶습니다. 모바일 개발에서 일반적인 문제 중 하나는 테스트 작성이 인기 없다는 것입니다. 실제로 그 이유는 대부분 응용 프로그램 아키텍처가 안 좋기 때문입니다. 안 좋은 아키텍처는 기능을 테스트하기 어렵게 만듭니다. 테스트하기 어렵다는 것은 제대로 테스트하기 위해 많은 시간이 소요된다는 것을 의미합니다. 많은 시간이 든다는 것은 매우 비용이 소요된다는 것입니다. 매우 비용이 소요된다는 것은 비즈니스적인 측면에서 볼 때 이미 모든 게 올바르게 작동하고 있다고 보기 때문에 구현되지 않을 것입니다.

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

![이미지](/assets/img/2024-06-19-ModernArchitectureforiOSapps_4.png)

그래서 이것의 주요 결과는 테스트 작성이 저렴해야 합니다. 이상적으로는 테스트를 몇 줄의 코드로 구현할 수 있어야 합니다. Elm 아키텍처를 사용하면 가능합니다. 예를 들어 비즈니스 로직을 테스트하는 방법을 보여드릴게요.

일반적인 Elm 아키텍처 단위 테스트는 다음과 같습니다 - 말 그대로 세 줄의 코드입니다:

- 어떤 이벤트가 발생했는가
- 초기 상태
- 기대되는 상태

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
    func test_userDidRequestIncrease() {
        test(
            event: .userDidRequestIncrease,
            initialState: .init(currentValue: 2, counter: 2),
            expectedState: .init(currentValue: 7, counter: 3)
        )
    }
```

그게 다에요. 다양한 Event와 State 조합을 테스트함으로써 모든 비즈니스 로직을 테스트하고 모든 것이 올바르게 작동하는지 확인할 수 있어요. 또한 한 가지 모범 사례를 포함하여 더 포괄적인 테스트를 위해 Effect 체인을 테스트할 수 있어요.

```js
    func test_userDidRequestDoubleIncreaseAndDecrease() {
        test(
            events: [.userDidRequestIncrease, .userDidRequestIncrease, .userDidRequestDecrease],
            initialState: .init(currentValue: 23, counter: 2),
            expectedState: .init(currentValue: 28, counter: 5)
        )
    }
```

The full implementation of XCTestCase for the Elm architecture can be found here.

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

단위 테스트 외에도 스냅샷 테스트가 존재합니다. 많은 이점이 있음에도 불구하고, 스냅샷 테스트는 모바일 개발에서 매우 인기가 없으며, 그 이유는 보통 비슷합니다. 스냅샷 테스트를 구현하려면 뷰를 초기화해야 하는데, 이는 품질이 낮거나 적절한 의존성 주입 메커니즘이 없는 경우 쉽지 않을 수 있습니다.

그러나 Elm을 사용하면 매우 쉽습니다. 강력한 레이어 분할 덕분에 뷰는 매우 간단하며 기본적으로 상태에만 의존합니다 (상태는 종종 초기값 또는 기본값을 갖습니다). 따라서 스냅샷 테스트를 만들려면 몇 줄의 코드를 작성해야 하며, 단위 테스트와 마찬가지로 간단합니다.

스냅샷 테스트는 두 가지 이유로 매우 유용하고 중요합니다:

- UI 레이어를 재발을 보호하므로 어떠한 변경과 리팩터링도 두렵지 않고 수행할 수 있습니다.
- 테스트의 필요성을 염두에 두고 뷰를 올바른 방식으로 구현하도록 개발자들에게 유도하므로 유지보수와 시스템 디버깅 수준을 향상시킵니다.

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

# 구성 루트

엘름 아키텍처는 장면 수준에서(장면이 보통 하나의 화면을 의미함) 발생하는 문제를 완벽하게 해결하지만, 이 외에도 앱에 다른 문제가 있을 수 있으며, 그 중 두 가지가 가장 중요합니다:

- 적절한 종속성 주입이 없음
- 모듈화가 되어 있지 않음

적절한 종속성 주입의 부재는 다음과 같은 결과로 이어집니다:

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

- 장면 내에서 종속성 해결(Service-Locator 반패턴)
- 주입 없이 종속성을 직접 사용하여 장면 테스트가 불가능해지는 문제

모듈화 부족(앱이 모놀리스이며 모듈로 분리되지 않은 것을 의미)은 다음과 같은 문제로 이어집니다:

- 유지보수 및 디버깅이 어려워지는 단일 전역 범위
- 작은 변경이 앱의 매우 다른 부분에 영향을 미칠 수 있으며, 강력한 회귀 테스트 없이는 눈에 띄지 않을 수 있음

모든 이러한 문제는 "구성 루트" 패턴을 통해 성공적으로 해결하고 전체 코드 베이스를 독립적인 모듈(스위프트 패키지 매니저(SPM) 용어에서 패키지 및 타겟)로 분할함으로써 해결할 수 있습니다.

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

Composition Root은 모듈이 결합되는 애플리케이션의 고유 위치입니다.

![이미지](/assets/img/2024-06-19-ModernArchitectureforiOSapps_5.png)

실제 응용 프로그램에서 "Composition Root" 패턴이 어떻게 보이는지 더 잘 이해하기 위해 이를 보여드릴게요.

![이미지](/assets/img/2024-06-19-ModernArchitectureforiOSapps_6.png)

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

모든 구성 요소는 별도의 타겟으로 Swift Package에 있으며, 이상적으로 관련된 몇 가지 구성 요소/타겟(예: "온보딩" 플로우와 같이 함께하는 사용자 흐름)은 별도의 Swift 패키지에 위치해 있습니다.

여기에 "구성 루트(Composition Root)" 구현의 간단한 예제를 찾을 수 있습니다.

구성 루트와 모듈화는 다음과 같은 탁월한 장점을 가지고 있습니다:

- 앱의 독립적인 부분이 서로 다른 모듈에 위치
- 모듈간 명확하고 강력한 경계
- 한 모듈의 변경이 다른 모듈에 영향을 미칠 수 없음
- 가볍고 최적화하기 쉬운 실행 레이어
- 전체 앱 및 그 어떤 부분에 대한 안정성, 유지보수 및 디버깅이 크게 향상됨
- 모든 종속성이 안전하게 해결되었음을 정적 컴파일 시 보장
- 의도하지 않은 모듈 간 사용 또는 간섭 제외
- 앱의 모든 레이어 및 부분 간의 낮은 결합도
- 변경되지 않은 모듈을 캐싱하여 컴파일 프로세스가 빨라짐

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

# 걱정하는 것을 그치고, 삶을 시작하는 방법

혹시 이 모든 것이 흥미롭긴 하지만, 여러분의 앱의 현재 상태에서 실제로 시도해보기에는 너무 멀게 느껴진다면, 그것은 오산이다. 앱의 상태에 따라 Elm 아이디어와 Clear Architecture 원칙으로의 여정이 더 오래 또는 더 짧을 수 있지만, 확실히 가능하며 가능한 한 빨리 해야 할 것입니다.

이를 위해 특별한 리팩터링 작업이 필요하지는 않습니다. 단지 Core 패키지를 만들고 Domain 타겟(비어있어도 괜찮습니다)을 생성한 뒤, 일상적인 기능 작업의 일부로 점진적으로 채워 넣기 시작하면 됩니다. 새로운 멋진 기능을 구현하려면 새로운 Swift 패키지를 만들어 이 패키지에 모든 코드를 작성하면 됩니다(한 화면씩 한 타겟). 이 기능을 위해 새로운 Domain 엔티티가 필요하다면 FeatureDomain 타겟을 만들고 해당 엔티티를 넣으면 됩니다. 몇 개의 화면에서 로직을 재사용해야 한다면 새로운 UseCase 타겟을 만들고 해당 로직을 옮기면 됩니다. Composition Root을 시도할 준비가 되면 한 번에 앱 전체를 다시 쓸 필요는 없습니다. 적절한 진입점을 찾아서 진입점 이후의 모든 것이 자동으로 모듈성과 Composition Root의 장점을 얻을 수 있습니다.

다시 강조하고 싶은 것은, 앱을 개선하기 위해 특별한 작업이 필요하지 않으며, 일상적인 루틴의 일부로 이를 수행하면 된다는 것입니다.

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

그 모든 것은 이론적인 접근이나 환상에 그치는 것이 아닙니다. 몇 차례 시도해 본 경험을 통해 알려주겠습니다. 많은 문제와 안티패턴이 존재하는 레거시 앱을 성공적으로 최고 수준의 견고하고 효율적인 솔루션으로 변환했습니다.

# 결론

Elm 아키텍처와 Composition Root 패턴 뒤에 있는 아이디어는 새로운 것이 아니지만, 모바일 개발에서 지나치게 소홀히 다루는 것을 알 수 있습니다. 이 기사는 이러한 원칙이 어떻게 실무에 적용될 수 있는지에 대한 이해의 공백을 채우기 위한 지식과 경험을 공유하기 위한 것입니다. 저는 이러한 접근 방식을 일상적인 업무에 사용하며, 제 팀뿐만 아니라 저 자신도 매우 만족하고 있습니다.

실제 제작 개발에서 검증된 작동하는 구현을 제공했지만, 항상 솔루션과 아이디어를 특정 상황과 기술 스택에 맞게 맞추는 것이 더 좋습니다. 이를 강화시키기 위해 발전시킬 수 있는 초안으로도 고려할 수 있습니다.

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

제가 권장하고 장려하는 바에 따라 아키텍처 주제에 조금 더 깊이 파고들어보시고 이 글에서 제안하는 아이디어를 귀하의 프로젝트에서 시도해보세요. 그것이 잘 작동하고 귀하의 앱을 다음 수준으로 끌어 올릴 수 있다는 것을 직접 확인하면서, 없을 때 어떻게 살아온 지 의심스러울 정도일 겁니다.
