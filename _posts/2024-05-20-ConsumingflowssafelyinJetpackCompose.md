---
title: "제트팩 컴포즈에서 안전하게 플로우 소비하기"
description: ""
coverImage: "/assets/img/2024-05-20-ConsumingflowssafelyinJetpackCompose_0.png"
date: 2024-05-20 15:56
ogImage:
  url: /assets/img/2024-05-20-ConsumingflowssafelyinJetpackCompose_0.png
tag: Tech
originalTitle: "Consuming flows safely in Jetpack Compose"
link: "https://medium.com/androiddevelopers/consuming-flows-safely-in-jetpack-compose-cde014d0d5a3"
---

![](/assets/img/2024-05-20-ConsumingflowssafelyinJetpackCompose_0.png)

Lifecycle-aware 방식으로 Flow를 수집하는 것이 Android에서 Flow를 수집하는 권장 방법입니다. Jetpack Compose로 안드로이드 앱을 개발 중이라면 UI에서 Lifecycle-aware 방식으로 Flow를 수집하기 위해 collectAsStateWithLifecycle API를 사용하십시오.

collectAsStateWithLifecycle을 사용하면 앱이 백그라운드에 있을 때와 같이 필요하지 않은 경우 앱 리소스를 저장할 수 있습니다. 불필요한 리소스를 오랫동안 유지하면 사용자 기기의 성능에 영향을 줄 수 있습니다. 이러한 리소스에는 Firebase 쿼리, 위치 또는 네트워크 업데이트 및 데이터베이스 연결이 포함될 수 있습니다.

이 API에 대해 더 알아보고, Lifecycle-aware 방식으로 수집해야 하는 이유 및 collectAsState API와 비교하는 방법을 계속 읽어보세요.

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

# collectAsStateWithLifecycle

collectAsStateWithLifecycle은 flow에서 값들을 수집하고 최신 값을 Compose State로 라이프사이클을 고려하여 표현하는 컴포저블 함수입니다. 새로운 flow 이벤트가 발생할 때마다 State 객체의 값이 업데이트됩니다. 이는 구성 내의 모든 State.value 사용의 recomposition을 유발합니다.

기본적으로 collectAsStateWithLifecycle은 Lifecycle.State.STARTED를 사용하여 flow로부터 값들을 수집하기 시작하고 중지합니다. 이는 라이프사이클이 대상 상태로 이동하고 벗어날 때 발생합니다. 이 라이프사이클 상태는 minActiveState 매개변수에서 구성 가능합니다.

![이미지](/assets/img/2024-05-20-ConsumingflowssafelyinJetpackCompose_1.png)

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

아래 코드 스니펫은 ViewModel에서 노출한 StateFlow의 uiState 필드를 수집하는 collectAsStateWithLifecycle의 사용 방법을 보여줍니다:

AuthorViewModel의 uiState가 새로운 AuthorScreenUiState 값을 방출할 때마다, AuthorRoute가 recompose됩니다. collectAsStateWithLifecycle의 더 많은 사용 예는 Now in Android 앱과 해당 마이그레이션 PR을 확인해보세요.

프로젝트에서 collectAsStateWithLifecycle API를 사용하려면 androidx.lifecycle.lifecycle-runtime-compose artifact를 프로젝트에 추가하세요.

# Under the hood

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

collectAsStateWithLifecycle의 구현은 View 시스템을 사용하여 안드로이드에서 플로우를 수집하는 권장 방법인 repeatOnLifecycle API를 사용합니다.

collectAsStateWithLifecycle을 사용하면 아래 표시된 보일러플레이트 코드를 입력하는 번거로움을 덜어줍니다. 또한 콤포저블 함수에서 라이프사이클을 인식하는 방법으로 플로우를 수집합니다.

# 아키텍처에서의 플로우 수집

앱 아키텍처의 유형은 다른 유형의 구현 세부 정보를 알아서는 안 됩니다. UI는 ViewModel이 UI 상태를 어떻게 생성하는지 알아서는 안 됩니다. UI가 화면에 표시되지 않으면 플로우 수집을 중지하여 적절한 경우 앱 리소스를 해제해야 합니다.

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

UI는 collectAsStateWithLifecycle를 사용하여 UI 상태를 수집함으로써 리소스를 해제하는 데 도움이 될 수 있습니다. ViewModel은 UI 상태를 수집자 인식적으로 생성함으로써 동일한 작업을 수행할 수 있습니다. UI가 화면에 표시되지 않은 경우와 같이 수집자가 없는 경우 데이터 계층에서 오는 상류 플로우를 중지할 수 있습니다. UI 상태를 생성할 때 .stateIn(WhileSubscribed) 플로우 API를 사용하여 이 작업을 수행할 수 있습니다. 이에 대한 자세한 정보는 Kotlin flows in practice에서 이야기하는 부분을 참조하십시오. 이 방법으로 UI 상태를 생성하는 ViewModel을 테스트하려면 테스트 가이드를 확인하십시오.

![image](/assets/img/2024-05-20-ConsumingflowssafelyinJetpackCompose_2.png)

플로우의 소비자와 생성자는 서로의 구현 방식을 알 필요가 없습니다. 여러 환경, 변형, 라이브러리 및 기능이 있는 큰 앱에서 구현 세부 정보를 파악하는 것은 매우 시간이 소요될 수 있습니다. 게다가, 구현 세부 정보에 의존하는 코드를 유지하는 것은 어려울 수 있습니다.

# 백그라운드에서 리소스 활성 유지하기

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

안녕하세요! 안드로이드 앱은 수많은 안드로이드 기기에서 실행될 수 있어요. 하지만 모든 기기와 사용자가 무한한 리소스를 가지고 있는 것은 아닙니다. 앱은 일반적으로 제한된 환경에서 실행돼요. 안드로이드 앱이 실행 중일 때는 사용자 경험과 기기 시스템 상태에 영향을 미치는 중요한 요인들이 있어요:

- CPU 사용량: CPU는 모든 기기 구성 요소 중에서 배터리 소모량이 가장 높아요. 배터리 수명은 사용자들이 항상 걱정하는 문제에요. 남용하면 사용자가 앱을 제거할 수도 있어요.
- 데이터 사용량: Wi-Fi에 연결되지 않은 경우 앱에서 네트워크 트래픽을 줄이면 사용자가 돈을 절약할 수 있어요.
- 메모리 사용량: 앱이 메모리를 사용하는 방식은 기기의 전체 안정성과 성능에 매우 큰 영향을 미칠 수 있어요.

사용자, 기기 시스템 상태를 존중하거나 수십억 대상으로 앱을 개발하려는 안드로이드 개발자는 시장, 기기 또는 대상 국가에 따라 이러한 다양한 요인들을 최적화해야 해요. 필요 없는 리소스를 계속 유지하면 기기의 종류와 사용 중인 안드로이드 버전에 따라 부정적인 영향을 줄 수 있어요. UI 레이어에서 collectAsStateWithLifecycle를 사용하면 나머지 계층이 리소스를 해제할 수 있어요.

# collectAsState 비교

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

개발자들이 종종 묻곤 합니다: 안드로이드의 컴포저블 기능에서 플로우를 수집하는 가장 안전한 방법은 collectAsStateWithLifecycle인데, collectAsState API가 왜 필요한가요? collectAsState에 라이프사이클 관리 기능을 추가하지 않고 새 API를 만드는 이유가 뭔가요?

컴포저블 함수의 라이프사이클은 Compose가 실행 중인 플랫폼과 상관이 없다. 컴포저블 함수의 라이프사이클은 '라이프사이클에서 컴포저블' 페이지에 문서화되어 있습니다. 컴포저블 함수의 인스턴스는 Composition에 진입하고, 0회 이상 다시 구성되고, Composition을 떠납니다.

![이미지](/assets/img/2024-05-20-ConsumingflowssafelyinJetpackCompose_3.png)

collectAsState API는 Composition의 라이프사이클을 따릅니다. 컴포저블이 Composition에 진입할 때 플로우 수집을 시작하고, Composition을 떠날 때 수집을 멈춥니다. collectAsState는 플로우를 수집하는 데 사용할 수 있는 플랫폼에 중립적인 API입니다.

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

그러나 안드로이드 앱에서 Compose를 사용할 때는 안드로이드 라이프사이클도 리소스 관리에 중요한 역할을 합니다. Compose가 안드로이드 앱이 백그라운드에 있을 때 recompositions을 일시 중지하더라도, collectAsState는 컬렉션을 활성 상태로 유지합니다. 이는 계층 구조의 나머지 부분이 리소스를 해제할 수 없게 만듭니다.

collectAsState와 collectAsStateWithLifecycle은 Compose에서 각자의 목적이 있습니다. 안드로이드 앱을 개발할 때는 후자를 사용하고, 다른 플랫폼을 위해 개발할 때는 전자를 사용합니다.

collectAsState에서 collectAsStateWithLifecycle로 마이그레이션하는 것은 간단합니다:

Lifecycle-aware 방식으로 플로우를 수집하는 것은 안드로이드에서 플로우를 수집하는 권장 방법으로, 필요한 경우 앱의 다른 부분이 리소스를 해제할 수 있도록 합니다.

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

Jetpack Compose을 사용하여 Android 앱을 개발 중이라면, collectAsStateWithLifecycle 조합 기능을 사용하여 이 작업을 수행할 수 있습니다.

부록: 이 기사를 검토해 준 Jose Alcérreca,
Marton Braun,
Alejandra Stamato,
그리고 Jake Roseman에게 감사드립니다.
