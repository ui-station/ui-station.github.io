---
title: "SwiftUI 앱을 위한 디자인 패턴 선택하기"
description: ""
coverImage: "/assets/img/2024-06-22-ChoosingaDesignPatternforyourSwiftUIApp_0.png"
date: 2024-06-22 23:06
ogImage:
  url: /assets/img/2024-06-22-ChoosingaDesignPatternforyourSwiftUIApp_0.png
tag: Tech
originalTitle: "Choosing a Design Pattern for your SwiftUI App"
link: "https://medium.com/@alexanderson_16451/choosing-a-design-pattern-for-your-swiftui-app-163c06ffcd9b"
---

디자인 패턴은 문제를 해결해야 합니다

![이미지](/assets/img/2024-06-22-ChoosingaDesignPatternforyourSwiftUIApp_0.png)

2022년 초, 저는 라커우드에 소재한 소프트웨어 컨설팅 회사인 우드리지 소프트웨어에서 근무하고 있었습니다. 그때 한 고객이 저희에게 수년간 지속될 대규모 기업용 앱을 개발해 달라는 요청을 했습니다. 프로젝트의 범위가 크기 때문에, 저희는 SwiftUI로 완전히 앱을 구축하는 도전을 수용하기로 결정했습니다.

우드리지의 iOS 팀 아키텍트로서, 저는 앱의 아키텍처를 기획하는 업무를 맡았습니다. 이런 방대한 프로젝트에 SwiftUI를 도입하는 것은 잘 정립된 아키텍처적인 모베스트 프랙티스의 부재로 독특한 도전이었습니다. 제가 디자인 패턴을 선택하고 나중에 발명하게 된 여정은 세 단계로 나뉘었습니다.

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

# 첫 번째 단계: UIKit 친화적인 디자인 패턴 선택하기

UIKit 개발자로서, 저는 처음에는 UIKit 세계에서 잘 작동하는 아키텍처 패턴을 선택하는 것이 최선일 것이라고 생각했습니다. 그러나 이는 문제가 있었고, 그 이유는 간단한 역사 수업 이후에 가장 잘 설명됩니다.

## iOS 아키텍처 간단 역사

애플이 2008년에 iPhone SDK를 출시했을 때, UIKit 애플리케이션을 구축하기 위한 권장 디자인 패턴은 MVC(Model-View-Controller)였습니다. 이 패턴은 데이터 모델, 뷰 및 이들을 연결하는 컨트롤러(UIKit에서는 뷰와 컨트롤러가 단일 "뷰 컨트롤러"로 결합됩니다)를 포함합니다. 기본적인 것을 넘어서 애플리케이션의 복잡도가 증가할수록, 이 패턴은 종종 익숙한 문제인 거대한 뷰 컨트롤러로 이어집니다.

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

대형 뷰 컨트롤러가 생긴 이유는 여러 가지가 있습니다. 예를 들어 UIKit 내에서 뷰 구성(큰 뷰를 작은 하위 뷰로 분해하는 것)을 사용하기가 매우 어렵기 때문에 대부분의 개발자들이 포기하고 앱의 전체 화면 당 하나의 뷰 컨트롤러를 만들어 각 뷰 컨트롤러를 불합리하게 늘리고 있습니다. 게다가 대부분의 뷰 컨트롤러가 네트워킹과 같은 공통 기능을 추가하여 부풀게되어 있습니다. 또한 모바일 애플리케이션에서 가장 중요한 측면 중 하나인 상태 관리도 쉽지 않았습니다. 애플리케이션 상태의 저장 및 관리는 대부분 개발자에게 맡겨져 있어 결과적으로 모든 것이 기본적으로 뷰 컨트롤러에 저장되었습니다.

총론적으로 문제는 뷰 컨트롤러가 맡은 책임이 너무 많았던 것입니다. 이 문제를 해결하기 위해 개발자들은 일반적으로 뷰 컨트롤러가 보유하던 관심사를 분리하여 해결하려는 의도로 많은 추가 디자인 패턴을 사용했습니다:

- MVP (Model-View-Presenter): 각 뷰 컨트롤러와 데이터 모델 간의 통신을 처리하는 "프레젠터" 구성 요소를 소개합니다.
- MVVM (Model-View-ViewModel): MVP와 유사하게, 각 뷰 컨트롤러에 데이터 모델과 통신을 원활하게 하는 "뷰 모델"이라고 불리는 구성 요소가 포함됩니다. 뷰 모델은 프레젠터와 차이점이 있으며 뷰 상태를 저장하고 그 상태를 직접 관련된 뷰에 바인딩합니다.
- Coordinator: 코디네이터 구성 요소는 네비게이션 관심사를 뷰 컨트롤러로부터 분리합니다.
- VIPER (View-Interactor-Presenter-Entity-Router): 아마도 그중에서 가장 복잡한 VIPER는 뷰 컨트롤러가 일반적으로 맡은 각 관심사를 개별 구성 요소로 분리하려고 합니다. 뷰(뷰 컨트롤러)는 UI 표시만 다루며, 프레젠터는 뷰용 데이터를 조작하며, 인터랙터는 비즈니스 로직을 처리하며, 엔티티는 기본 데이터 모델을 표현하며, 라우터는 네비게이션을 다룹니다.

## SwiftUI 도입

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

2019년에 Apple은 새로운 UI 프레임워크인 SwiftUI를 소개했습니다. SwiftUI는 UIKit과 매우 다릅니다. UIKit은 명령형인 반면, SwiftUI는 선언적입니다. 개발자가 뷰를 직접 처리하는 대신, UI가 어떻게 보여야 하는지를 지정하고 프레임워크가 나머지를 처리합니다. 개발자가 상태 관리를 직접 처리하는 대신 SwiftUI에는 내장된 상태 관리가 있으며 상태 변경 시 뷰 업데이트를 자동으로 처리합니다. UIKit에서 뷰 구성이 어려운 반면, SwiftUI에서는 기본적인 것입니다.

보시다시피, SwiftUI는 단순히 새로운 UI 프레임워크가 아닙니다. UIKit과 너무 다르기 때문에 우리가 하는 게임을 완전히 바꿉니다. UIKit에서 발생했던 대규모 뷰 컨트롤러 문제가 SwiftUI 세계에 존재하지 않으므로 이 문제를 해결하기 위해 특별히 고안된 디자인 패턴을 사용할 필요가 없음을 의미합니다.

따라서 UIKit 친화적인 디자인 패턴을 선택하는 것이 처음에는 합리적으로 보일지라도, 이러한 패턴이 SwiftUI 세계에서는 존재하지 않는 문제를 해결하려는 것임을 빨리 깨닫게 됩니다. 더구나 VIPER와 같은 몇 가지 패턴은 개발자가 좋은 SwiftUI 코드를 작성하는 것을 방해할 수도 있습니다. VIPER와 같은 패턴은 뷰당 중요한 보일러플레이트를 생성하여 각 뷰가 많은 코드와 복잡성을 추가하기 때문에, 개발자들이 뷰를 더 작은 단위로 나누는 것을 열심히 하지 않게 됩니다. Michael Long이 이에 대해 상세히 쓴 바 있습니다.

결국, 디자인 패턴은 문제를 해결하기 위해 존재해야 하며, 가능한 문제를 만들어서는 안 됩니다. UIKit 친화적 아키텍처는 대규모 뷰 컨트롤러 문제를 해결하기 위해 고안된 것인 반면, 이 문제가 SwiftUI에서는 더 이상 존재하지 않습니다.

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

# 두 번째 단계: 가장 추천되는 SwiftUI 디자인 패턴 선택: MVVM

알겠어요, 지금 UIKit 지식을 적용해도 이 문제를 해결할 수 없을 거에요. 그래서 웹을 철저히 조사해본 결과, 대부분의 사람들이 MVVM을 표준 SwiftUI 디자인 패턴으로 추천하고 있어요.

이전에 언급한 바와 같이, MVVM은 각 뷰가 자체 ViewModel 구성 요소를 갖고, 해당 상태와 비즈니스 로직을 포함하며, ViewModel은 상태를 직접 뷰에 바인딩할 것을 규정하고 있어요. 이는 SwiftUI의 상태 중심 및 바인딩 기반 구조에 매우 적합한 후보로 보이며, ObservableObject(그리고 새 @Observed 매크로)를 사용하여 쉽게 구현할 수 있어요.

이 접근 방식에는 장단점이 있어요. 장점은 MVVM이 비즈니스 로직을 뷰에서 분리하여 이식 가능하고 테스트하기 쉽게 만든다는 것입니다. 그러나 SwiftUI에서 MVVM을 사용하는 단점은 많으며, 내 의견으로는 장점을 상회한다고 생각해요:

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

- 개발자들은 ViewModels 내에서 withAnimation, @FocusState, @FetchRequest 등과 같은 SwiftUI 기능을 사용할 수 없습니다. 이겈 view models는 그냥 보통의 클래스이기 때문에 그들은 뷰가 아니며, 따라서 뷰의 기능 세트를 활용할 수 없습니다. 이러한 제한을 피하기 위해 우회 방법을 사용하는 것은 번거로울 수 있습니다.
- SwiftUI 뷰 간의 데이터 전달은 도전적인 과제가 됩니다. 왜냐하면 이제는 바인딩을 쉽게 사용하거나 뷰 모델 내에서 환경에서 정보에 액세스할 수 없기 때문입니다(@Environment 또는 @EnvironmentObject를 통해).
- ViewModel에서 하나의 속성을 변경하면 해당 속성이 연관된 전체 뷰가 아닌 이 뷰를 무효화시키는 문제가 발생합니다(이는 iOS 17의 새로운 관찰 시스템으로 해결되었지만, iOS 17 이전의 SwiftUI 버전에는 여전히 언급할 가치가 있습니다).
- MVVM은 비즈니스 로직과 뷰를 강하게 결합하기 때문에 뷰 구성을 억제할 수 있습니다. 단일 뷰를 여러 개의 뷰로 분해하면 여러 개의 새로운 뷰 모델을 생성하고 그들 사이의 복잡한 데이터 흐름을 조율해야 합니다.

이러한 단점을 고려하면 SwiftUI에 적용할 때 MVVM은 적합한 도구가 아닌 것으로 판단됩니다. 이 패턴은 효과적으로 뷰와 비즈니스 로직을 분리하지만 이 과정에서 개발자와 핵심 SwiftUI 기능 사이에 수많은 장애물이 놓여 있습니다. SwiftUI의 세계에 더 적합한 것을 찾아야 한다고 생각합니다. 이것이 저만의 의견이 아닌 것 같습니다.

# 세 번째 단계: 새로운 시도

그렇다면 이제 어떻게 해야 할까요? SwiftUI와 같이 상대적으로 새로운 프레임워크를 사용하는 것은 최상의 방법이 아직 확립되지 않았다는 것이 멋진 점이자 무서운 점입니다. 다른 사람들이 당신을 위해 길을 밟은 사례를 더 찾아보면, 당신이 하나의 길을 밟는이라는 것을 더 많이 깨닫게 됩니다. 이를 염두에 두고, 우리가 나름대로의 방법을 제시해야 하고, 우리만의 아키텍처 패턴을 처음부터 개발해야 할 것이라는 것을 깨달았습니다.

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

한 발 물러서서 생각을 해보면, 결국 소프트웨어 아키텍처의 목적은 무엇인가요? 아무튼 그냥 해야 하는 건가요? 아키텍처 신에게 기쁘기 위해서요?

전 반대의 의견을 가지고 있어요. 제가 생각하는 아키텍처 철학은 그것이 목적을 가져야 한다는 것이에요: 문제를 해결해야 해요. SwiftUI의 본질은 방대한 뷰 컨트롤러를 만들어낸 문제들을 해결했고, 이는 더 일반적인 아키텍처 문제를 해결하는 데 노력을 집중할 수 있다는 것을 의미합니다. 그 결과로 나타나는 아키텍처는 UIKit 중심의 아키텍처보다 더 간단하고 우아할 수 있습니다.

그렇다면, 더 일반적인 아키텍처로 어떤 문제들을 해결하려고 하는 것인가요? 주로 우리는 스파게티 코드를 방지하려고 하며, 코드 구조가 이해하기 쉽고 확장하기 쉬운 구조로 끝나길 원합니다. 일반적으로 유지보수 가능한 코드를 지향합니다. 이 새로운 아키텍처 패턴을 위해 선택한 일반적인 원칙들은 다음과 같아요:

- 관심사 분리 (예: 뷰와 비즈니스 로직 분리)
- DRY 원칙 준수 — 일반적인 작업들은 자체 유틸리티로 정리되어야 합니다
- 이 패턴은 SwiftUI를 보완하고 프레임워크와 대립하지 않아야 합니다

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

## MVSU (Model-View-Service-Utility)

위 요구 사항을 고려하여, 저는 MVSU라는 디자인 패턴을 고안했습니다. 이 패턴은 MVC, 도메인 주도 디자인, 그리고 Clean architecture에 깊은 뿌리를 두고 있지만, SwiftUI에 특히 잘 어울릴 것으로 생각되는 고유한 특성을 갖고 있습니다. 이 패턴은 4개 그룹의 구성 요소로 이루어져 있습니다:

- **유틸리티**는 특정 작업 하나를 수행하거나 몇 가지 밀접하게 관련된 작업을 수행하는 좁게 중점을 둔 재사용 가능한 도구들입니다. 서비스는 하위 수준의 세부 사항을 걱정하지 않고 기능 수행을 가능케 합니다.
- **서비스**는 비즈니스 로직 계층입니다. 서비스는 애플리케이션의 특정 도메인에 관련된 기능의 상태를 가지지 않은 그룹입니다. 이를 보면 뷰가 사용하는 API로 생각할 수 있습니다.
- **모델**은 가장 일반적인 부분이며, 앱 데이터를 캡슐화하는 역할을 합니다. 서비스는 모델과 통신하여 기능을 수행합니다.
- **뷰**는 대체로 자명하지만 알아둬야 할 몇 가지 규칙이 있습니다. 뷰는 주로 자신의 상태를 처리하며, 주로 유틸리티가 아닌 서비스를 사용하여 기능을 수행합니다. 뷰 구성을 적극적으로 활용하여 각 뷰를 쉽게 이해할 수 있는 구성 요소로 분해하여 유지하고, 그로 인해 각 뷰가 작고 중점적으로 유지됩니다.

![이미지](/assets/img/2024-06-22-ChoosingaDesignPatternforyourSwiftUIApp_1.png)

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

각 구성 요소 유형에 대해 자세히 설명하겠습니다:

## 유틸리티

일부 앱에서 네트워킹, 알림 표시, 오류 처리 등과 같은 일반 작업은 코드베이스 전체에 흩어지거나 반복될 수 있습니다 (또는 뷰모델과 같은 중재 레이어에 위치할 수도 있습니다). MVSU는 특히 이러한 유형의 반복 작업을 “유틸리티” 접미사를 가진 클래스로 이동해야 한다고 규정합니다 (예: NetworkUtility, SessionUtility, LoggingUtility 등).

단일 책임 원칙을 준수하는 한, 이러한 유틸리티는 매우 집중되어 있어야 합니다. 한 가지 유형의 작업이나 몇 가지 밀접한 관련 작업만 수행해야 합니다. 서비스는 그들의 상위 수준 기능을 수행하기 위해 유틸리티를 활용합니다. 이러한 작업을 유틸리티로 이동함으로써 코드베이스를 DRY(반복하지 마세요) 상태로 유지하고 서비스가 독자적 도메인에만 집중하도록 합니다. 여기서의 규칙은 여러 서비스가 동일한 논리를 수행한다면, 아마도 유틸리티로 이동해야 한다는 것입니다.

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

```swift
class NetworkUtility {
    func perform<ResultType: Decodable>(request: Request<ResultType>) async throws -> ResultType {
        // ...
    }
}

class LogUtility {
    func log(type: LogType, description: String) {
        // ...
    }
}
class FileUtility {
    func save(data: Data, toFileUrl fileUrl: URL) throws {
        // ...
    }
    func retrieveData(fromFileUrl fileUrl: URL) throws -> Data {
        // ...
    }
}
```

## 서비스

MVC, MVP, MVVM, VIPER과 같은 많은 패턴은 뷰별로 비즈니스 로직을 분리합니다 (즉, 각 뷰는 별도의 컨트롤러, 뷰모델 등을 갖습니다). MVSU에서는 비즈니스 로직을 도메인별로 분리합니다. 이는 앱이 사용하는 각 데이터 유형마다 해당하는 서비스를 갖는 것을 의미합니다 (예: 도넛 가게의 경우 도넛 서비스, 주문 서비스, 사용자 서비스 등).

도메인별로 비즈니스 로직을 분리하는 것에는 몇 가지 장점이 있습니다:

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

- 관심사 분리가 더 잘 이루어집니다: 뷰 모델과 같은 중재자는 특정 뷰에 연결되도록 설계되어 있기 때문에 해당 뷰에 대해 많은 정보를 알게 됩니다. 도메인별로 분리하면 서비스가 자신의 도메인에 대해 많은 지식을 갖지만, 뷰에 대해서는 거의 또는 전혀 알지 못합니다. 이것은 다른 뷰 모델과 달리 특정 뷰를 고려하여 설계되지 않았기 때문에 더 이동성이 있습니다.
- 논리적으로 관련된 코드는 논리적으로 그룹화됩니다. Donut을 생성하고 삭제하는 뷰 각각을 가지는 MVVM 앱의 경우, 관련성이 높은 이 코드도 2개의 다른 뷰모델에 들어가게 되지만, MVSU에서는 해당 코드가 단일 DonutService에 모두 들어가 관련된 코드를 유지하기 쉽게 만듭니다.
- 기능은 뷰 간에 쉽게 공유할 수 있습니다. 서비스 기능은 한 뷰 이상에서 사용될 수 있기 때문입니다. 이는 MVVM과는 달리 뷰 당 하나의 뷰모델을 가지고 있는 것과 대립적입니다.
- 도메인별로 분리하면 SwiftUI의 뷰 구성 세계에서 boilerplate가 줄어듭니다. MVVM이나 VIPER와 같은 패턴에서 개발자들은 boilerplate로 인해 하위 뷰를 만들기가 꺼려진다. MVSU에서는 뷰 당 boilerplate가 아닌 도메인 당 boilerplate가 생기기 때문에 하위 뷰로 세분화하는 단점이 없어집니다. 이것은 SwiftUI가 거대한 뷰 컨트롤러 문제를 해결한 주요 이유이므로 아키텍처에서 하위 뷰를 권장해야 합니다.

또한 서비스에 대해 더 알아야 할 점은 상태가 없다는 것입니다 - 상태는 서비스가 아닌 뷰에 저장되어야 합니다. 이에 대한 더 자세한 내용은 Views 섹션에서 설명하겠습니다.

```js
// 주입 전략은 본인에게 달려 있지만, Factory (https://github.com/hmlongco/Factory)와 같은 라이브러리를 사용할 것을 권장합니다.

import Factory

class OrderService {
    @Injected(\.networkUtility) private var networkUtility
    @Injected(\.logUtility) private var logUtility

    func createOrder(items: [ItemModel]) async throws -> OrderModel {
        do {
            let response = try networkUtility.perform(request: .createOrder(items: items))
            return OrderModel(record: response.order)
        } catch {
            logUtility.log(type: .error, description: "Order error occurred: \(error.localizedDescription)")
            throw RuntimeError("주문 생성이 불가능합니다.")
        }
    }
}
```

## Views

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

언급한 대로, MVSU는 뷰 구성을 권장하여 큰 뷰를 작은 뷰로 나누고, 각 뷰를 가늠있고 좁게 중점을 두어 과거의 방대한 뷰 컨트롤러 문제를 피합니다.

MVSU에서 서비스는 상태를 유지하지 않습니다. 대신 뷰가 모든 필요한 상태를 유지합니다. 이는 몇 가지 이유로 인해 이루어집니다:

- 관심사의 분리: 어떤 면에서는 응용 프로그램 상태는 도메인 문제가 아니라 뷰 문제입니다. 뷰의 관심사를 뷰 내부에 유지함으로써 더 유지보수 용이한 코드를 얻을 수 있습니다.
- 향상된 가용성과 단순성: 데이터를 뷰 계층 구조를 위아래로 전달하는 것은 뷰 모델과 같은 중간 계층을 처리할 때 혼란스러운 도전이 될 수 있습니다. 대조적으로 @Binding 및 @Environment를 사용하면 매우 간단하고, 이에 따라 가용성을 촉진합니다.
- SwiftUI 기능의 쉬운 활용: 뷰 내부에 상태를 유지하는 것은 애니메이션과 같은 기능을 쉽게 활용할 수 있게 해줍니다.

```swift
struct OrderView: View {
    @Injected(\.orderService) private var orderService
    @Injected(\.paymentService) private var paymentService

    let availableItems: [ItemModel]
    @State private var itemsInCart: [ItemModel]
    @State private var orderAwaitingPayment: OrderModel?
    @State private var error: Error?

    var body: some View {
        VStack {
            ForEach(availableItems) { availableItem in
                AddItemToCartButton(availableItem) { item in
                    itemsInCart.append(item)
                }
            }

            Divider()

            // "AsyncButton" 뷰를 만드는 것을 강력히 권장합니다: https://www.swiftbysundell.com/articles/building-an-async-swiftui-button/
            AsyncButton("Create Order") {
                do {
                    orderAwaitingPayment = await orderService.createOrder(items: itemsInCart)
                } catch let orderError {
                    error = orderError
                }
            }
        }
        .sheet(item: $orderAwaitingPayment) { order in
            PaymentInfoView(order: order)
            PaymentButton() {
                await paymentService.createPayment(forOrder: order)
                orderAwaitingPayment = nil
            }
        }
        .errorAlert(error)
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

## 모델

이 부분은 패턴 중 가장 독특하지 않은 부분입니다. 모델은 데이터 유형과 필요한 모든 지속성 레이어(예: Core Data, SwiftData, Realm 등)를 포함합니다. MVSU에서 SwiftUI의 기능 세트를 유지하는 것이 중요하므로 뷰는 SwiftUI의 프로퍼티 래퍼(예: @FetchRequest 또는 @Query)를 통해 모델과 직접 통신할 수 있어야 합니다.

# 결론

내 의견으로는 SwiftUI의 도입은 iOS 개발 영역에서 가장 중요한 변화 중 하나입니다. SwiftUI는 그냥 새로운 UI 프레임워크가 아닙니다. UIKit과는 매우 다르기 때문에 앱을 구축하는 전체 방식에 대해 다시 생각하도록 요구한다고 생각합니다. 아마도 iOS 커뮤니티가 처음부터 아키텍처적 결정을 왜 내리는지에 대해 다시 한번 고민할 때가 되었을지도 모릅니다.

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

디자인 패턴을 선택하고 결국 만드는 과정을 거치며 왜 처음부터 일반적인 UIKit 디자인 패턴을 사용했는지 깨달았어요. 제 의견으로는 대부분의 앱에서 달성한 복잡성 수준에 UIKit이 잘 맞지 않아서, 이로 인해 우리는 특별히 설계된 아키텍처 패턴으로 문제를 해결해야 했어요. SwiftUI는 같은 문제가 없어서 그런 해결책이 필요하지 않아요.

이 새로운 환경은 두렵고 흥미로운 만큼 새로운 도전이에요. 이는 불가피하게 UIKit 세계에서 연마해온 지식과 기술 일부가 적용되지 않을 수 있다는 의미예요. 또한, 우리가 나만의 방향을 개척하고, 아마도 더 이상 UIKit 개발의 최악 부분들과 다시는 싸울 필요가 없을지도 모른다는 것을 의미해요. 저는 이 길을 계속 나아가며 알려지지 않은 세계를 탐험하는 것에 흥미를 느끼고 있어요.
