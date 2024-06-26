---
title: "모바일 앱을 위한 최고의 Firebase 도구들"
description: ""
coverImage: "/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_0.png"
date: 2024-06-27 19:33
ogImage: 
  url: /assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_0.png
tag: Tech
originalTitle: "Best Firebase Tools for Your Mobile Application"
link: "https://medium.com/proandroiddev/best-firebase-tools-for-your-mobile-application-b0d1857a07f3"
---


<img src="/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_0.png" />

모바일 애플리케이션을 개발할 때, 보통 권한 부여, 푸시 알림 등과 같은 필수 기능을 구현해야 하는데, 이는 예산과 시간이 부족한 스타트업에게 특히 어려운 과제일 수 있습니다. 이러한 기능을 구현하는 것은 매우 시간이 많이 소요되고 자원이 많이 소모될 수 있습니다.

Firebase는 개발 프로세스를 간편하게 만들어주는 다양한 도구를 제공합니다. 이 도구들은 인증, 알림, 실시간 데이터베이스 등에 대한 사용 준비 완료 솔루션을 제공합니다. 이를 통해 스타트업은 핵심 제품에 집중할 수 있고 백엔드 인프라를 구현하고 유지하는 데 소중한 자원을 낭비할 필요가 없습니다.

이 게시물에서는 Firebase를 모바일 및 웹 애플리케이션에 활용하는 방법을 배우게 될 것입니다. 인증, 푸시 알림, 원격 구성, 분석 등과 같은 기능을 포함한 내용을 다룰 예정입니다.

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

# Firebase란 무엇인가요?

Firebase는 애플리케이션에서 가장 중요한 기능을 구현하는 데 큰 도움이 되는 다양한 도구를 제공합니다. 이러한 기능은 푸시 알림, 분석 및 실시간 데이터베이스 등이 있는데, 이러한 것들을 제로베이스부터 구축하는 데는 상당한 노력이 필요합니다.

Firebase는 해당 서비스를 실행할 수 있도록 맞춤형 서버를 제공합니다. 푸시 알림, 분석 및 실시간 데이터베이스와 같은 기능을 구현하기 위해 서버를 구축하고 유지할 필요가 없습니다. Firebase를 사용하면 백엔드 작업을 크게 줄일 수 있습니다. 때로는 Firebase의 견고한 인프라를 활용하여 자체 서버를 호스팅하지 않고도 모바일 응용 프로그램을 출시할 수 있습니다.

클라이언트 측에서 Firebase는 여러 클라이언트 SDK를 제공하여 해당 서버와의 통신을 용이하게 하고 비즈니스 로직 빌드 프로세스를 간단하게 만들어 Firebase 서비스와의 원활한 통합을 보장합니다. 또한 Firebase 콘솔은 웹 브라우저를 통해 이벤트를 추적하고 서비스를 쉽게 관리할 수 있게 하는 사용자 친화적 인터페이스를 제공하여 비 기술 직군인 제품 소유자와 같은 팀 구성원이 이용할 수 있습니다.

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

스택쉐어에 따르면 Firebase는 능력과 신뢰성을 입증했으며, 인스타카트, 블록, 트위치, 액센처와 같은 주요 기업들이 사용하고 있습니다. 게다가 Firebase는 무료 요금제(스파크 플랜)를 제공하여 비용을 들이지 않고 서비스를 향상시키려는 스타트업들에게 매력적인 옵션이 됩니다. Firebase를 시도하지 않을 이유가 없습니다. 

# 인증

![Firebase 도구](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_1.png)

인증은 사용자를 식별해야 하는 대부분의 애플리케이션에서 꼭 필요한 기능입니다. 사용자의 신원을 파악하면 애플리케이션은 사용자 데이터를 안전하게 서버에 저장하고 해당 사용자의 모든 기기에서 개인화된 경험을 제공할 수 있습니다.

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

앱에서 인증을 처리하는 가장 쉬운 방법은 Google, Meta, Twitter 등의 잘 알려진 플랫폼의 인증 서비스를 활용하는 것입니다. 이러한 서비스들은 각 회사에서 철저히 검토되어 견고한 보안을 제공합니다. 또한 사용자는 기존 계정을 사용하여 손쉽게 신원을 인증할 수 있어 새로운 계정을 수동으로 등록할 필요가 없어져 온보딩 프로세스가 간단해집니다.

Firebase Authentication은 백엔드 서비스와 클라이언트 라이브러리를 제공하며, 안드로이드, iOS, Flutter, Web, Unity 등 여러 플랫폼에서 사용자를 인증할 수 있는 UI 라이브러리를 쉽게 제공합니다. Google, Facebook, Apple, Twitter, GitHub, Microsoft, 수동 이메일-비밀번호, 전화번호 인증을 포함한 다양한 인증 제공자와 통합되어 사용할 수 있습니다. 이러한 다양한 옵션을 통해 사용자들이 선호하는 플랫폼이나 인증 방식에 관계없이 원활하고 안전한 로그인 경험을 제공할 수 있습니다.

Firebase Authentication은 또한 다중 요소 인증, 차단 기능, 남용 방지, SAML(Security Assertion Markup Language) 및 OpenID Connect 제공자 지원과 같은 고급 기능을 제공합니다. 이러한 기능은 보안을 강화하고 사용자 인증을 효율적으로 관리할 수 있는 더 많은 유연성을 제공합니다. 이러한 기능에 대해 자세히 알아보고 싶다면 공식 문서를 확인해보세요.

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

푸시 알림은 사용자들을 참여시키기 위한 모바일 애플리케이션의 핵심 기능이며, 활성 사용자 참여 및 유지에 효과적인 전략이 될 수 있습니다. 또한 사용자 상호 작용 간에 좋아요, 댓글, 채팅 메시지 및 팔로우와 같은 실시간 정보 및 업데이트를 제공하는 데 매우 유용할 수 있습니다.

예를 들어 푸시 알림을 통해 새 메시지, 친구 요청, 또는 게시물 업데이트 등에 대한 사용자 경고를 할 수 있어서 앱과 계속 연결되어 있도록 유지할 수 있습니다. 효과적인 푸시 알림 사용은 사용자 경험 향상, 높은 유지율 및 앱에서 사용자 활동 증가로 이어질 수 있습니다.

제로부터 알림 시스템을 구축하는 것은 자원 투자가 많이 필요하고 복잡합니다. 알림 클라우드 서버를 설정하고 실시간 동기화 메커니즘을 구현하여 알림 페이로드를 가져오고, 모바일 장치가 올바르게 깨어나도록 보장하고, 서버와 클라이언트 간의 필요한 페이로드 프로토콜을 생성해야 합니다.

Firebase는 Firebase Cloud Messaging (FCM)이라는 내장 솔루션을 제공하여 별도의 알림 서버를 구축할 필요 없이 알림 시스템을 구축할 수 있습니다. 또한 모바일 애플리케이션용 클라이언트 SDK를 제공하여 사용자가 올바른 알림을 받을 수 있도록 보장합니다. Firebase의 종합적인 지침을 따르면 FCM을 사용하여 스타트업이 복잡한 푸시 알림 시스템을 효율적으로 구축할 수 있으며, 종종 하루 사이에 구현할 수 있습니다.

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

FCM은 비기술 직원인 제품 매니저 등이 GUI 기반 Firebase 콘솔을 통해 쉽게 푸시 알림을 보낼 수 있는 편리한 사용자 경험을 지원합니다. 이는 제품 매니저가 기술 지식이 없어도 구체적인 주제 구독자에게 데이터 페이로드와 예약된 알림을 포함한 푸시 알림 메시지를 보낼 수 있고 특정 사용자를 대상으로 할 수 있다는 것을 의미합니다.

![이미지1](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_2.png)

공식 문서에 따르면 FCM의 전체 아키텍처는 아래 그림과 같이 설명되어 있습니다:

![이미지2](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_3.png)

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

위 그림에 설명된 아키텍처는 아래와 같이 작동합니다:

- 알림 요청 생성: FCM 콘솔이나 귀하의 백엔드 서비스를 사용하여 알림 요청을 생성합니다. 귀하의 백엔드 서비스는 신뢰할 수 있는 서버 환경 내에서 작동해야 하며 Firebase Admin SDK 또는 FCM 서버 프로토콜을 지원해야 합니다.
- FCM 백엔드 처리: FCM 백엔드는 메시지 요청을 수락하고 주제를 통해 메시지를 전파하며 메시지 ID를 포함한 메시지 메타데이터를 생성합니다.
- 플랫폼 수준 전송 계층: 플랫폼 수준 전송 계층은 메시지를 대상 기기로 라우팅합니다. 메시지 전달을 처리하고 필요한 경우 플랫폼별 구성을 적용합니다.
- 사용자 기기의 FCM SDK: 사용자의 기기에서 FCM SDK는 앱의 전경/배경 상태 및 관련 응용 프로그램 논리에 따라 알림 표시 또는 메시지 처리를 처리합니다. SDK는 알림이 앱 내에서 적절하게 표시되거나 처리되도록 보장합니다.

더 많은 정보를 원하시면 FCM의 공식 문서를 확인해보세요.

# Firebase Remote Config

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

모바일 개발에서 가장 큰 도전 중 하나는 UI 레이아웃 수정, 특정 기능 활성화 또는 비활성화, 기타 조정 등 애플리케이션 변경 사항에 대한 업데이트를 푸시해야 한다는 점입니다. 일반적으로 이는 Google Play Store 또는 Apple App Store로 새 패키지를 전달하는 작업을 포함합니다. 또한, 사용자는 해당 변경 사항을 보려면 애플리케이션을 수동으로 업데이트해야 합니다.

Firebase Remote Config를 사용하면 이러한 간단한 변경 사항을 위해 앱을 업데이트하는 복잡한 과정을 피할 수 있습니다. Firebase Remote Config는 사용자가 업데이트를 다운로드할 필요 없이 앱의 동작 및 외관을 수정할 수 있는 클라우드 서비스입니다.

Firebase 콘솔에서 값(매개변수 키 및 값)을 업데이트하면 앱 또는 서버 구현에서 이러한 업데이트가 적용되는 시점을 결정합니다. 앱은 주기적으로 업데이트를 확인하고(시간당 한 번 또는 이와 유사한 주기로) 성능에 미치는 영향을 최소화하며 이를 적용할 수 있습니다. Firebase는 비기술적인 직원이 전략에 따라 이러한 값을 제어할 수 있도록 GUI 기반의 콘솔도 제공합니다.

아래 이미지는 Firebase 도구를 통해 모바일 애플리케이션에 사용할 수 있는 최상의 기능을 보여줍니다:

![Firebase 도구](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_4.png)

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

Firebase 원격 구성은 앱 업데이트 없이 기능을 활성화하거나 비활성화하는 데 매우 유용할 수 있습니다. 이를 통해 다양한 구성을 실험하고 계절 이벤트나 보상을 관리하며 다양한 시간대에서 특정 기능을 원활하게 활성화할 수 있습니다. 귀하의 응용 프로그램에서 Firebase 원격 구성을 효과적으로 활용하면 다음과 같은 유연성을 크게 향상시킬 수 있습니다:

- 리스크 감소: 대상 사용자에게 기능을 점진적으로 전파하고 필요한 경우 긴급 롤백을 수행합니다.
- 사용자 참여 증대: 사용자가 앱을 사용하는 동안 빠르게 사용자 경험을 맞춤 설정할 수 있습니다. 예를 들어 구글 애널리틱스 사용자 속성과 일치하는 사용자에게 배너를 업데이트하고 인센티브를 제공하거나 다른 사용자 그룹에 대해 동적으로 판매 금액을 조정할 수 있습니다.
- 개발자 생산성 향상: 개발 및 테스트 팀에 기능 플래그로 사용할 원격 구성 매개변수를 사용하여 제품의 기능을 더 효과적으로 노출하고, 실제 사용자에게는 숨겨진 상태로 유지합니다. 이는 빌드 종속성을 줄이고 개발 프로세스를 간소화합니다.

Firebase 원격 구성에 대해 더 알고 싶다면, "실시간 원격 구성 이해하기"를 확인해보세요.

# 실시간 데이터베이스

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

모바일 애플리케이션 개발에서는 종종 사용자 데이터를 저장하거나 사용자 간 통신을 가능하게해야 합니다. 이러한 경우에는 모바일 애플리케이션과 상호 작용하는 백엔드 서버를 설정하고 관리해야합니다. 그러나 대부분의 스타트업은 매우 제한된 개발자 수로 구성되어 있기 때문에 사용자 정의 서버를 구축하고 유지하는 것이 어려울 수 있습니다. 또한 비용이 소요될 수 있습니다.

시장 피드백을 수집하려면 간단한 서버를 구축하거나 애플리케이션 개발자로서 백엔드 서버를 구축하는 비용을 줄이려는 경우 Firebase Realtime Database가 최적의 선택입니다. Firebase Realtime Database는 빠르게 백엔드를 설정하고 관리할 수 있는 이상적인 솔루션을 제공하여 앱 개발에 집중할 수 있게 해줍니다. 이를 통해 서버 인프라를 처음부터 구축하는 복잡성과 비용을 줄일 수 있습니다.

Firebase Realtime Database를 사용하면 NoSQL 클라우드 데이터베이스를 활용하여 데이터를 저장하고 동기화시킬 수 있습니다. 데이터는 실시간으로 모든 클라이언트 간에 동기화되며 앱이 오프라인 상태가 되었을 때에도 액세스할 수 있습니다. 이는 복잡한 SQL 쿼리나 무거운 계산 작업이 필요하지 않은 경우 매우 편리한 옵션이 될 것입니다.

Firebase Realtime Database를 사용하는 주요 장점은 다음과 같습니다:

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

- 실시간 데이터 동기화: 전형적인 HTTP 요청과 달리 Firebase 실시간 데이터베이스는 데이터 동기화를 활용하여 데이터에 대한 모든 변경 사항이 연결된 모든 기기에서 즉시 업데이트되어 밀리초 내에 동기화됩니다. 이 기능을 통해 네트워킹 코드를 관리하지 않고도 협업 및 몰입적인 경험을 구현할 수 있습니다. 
- 오프라인 보존: Firebase 앱은 오프라인 상태에서도 반응성을 유지합니다. 실시간 데이터베이스 SDK는 데이터를 기기의 디스크에 보존합니다. 연결이 복구되면 클라이언트 기기가 놓친 업데이트를 받아서 현재 서버 상태와 동기화합니다. 이는 네트워크 상태에 관계 없이 끊김 없는 사용자 경험을 보장합니다.

Firebase 실시간 데이터베이스에 대해 더 자세히 알아보려면 공식 문서를 확인해보세요.

# 앱 배포

![이미지](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_5.png)

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

모바일 개발자이신 경우, 적어도 한 번은 APK, AAB 또는 IPA 파일과 같은 응용 프로그램 파일을 제품 관리자나 QA 전문가에게 테스트를 위해 전달해야 한 적이 있을 것입니다. 그 후 팀에서 이전 버전을 제거하고 새로운 버전을 설치할 것입니다.

기본적인 방식으로는 응용 프로그램 파일을 팀원들에게 전달하는 목표를 달성할 수 있지만 몇 가지 단점이 있습니다:

- 확장성 문제: 팀이 크고 많은 이해 관계자가 응용 프로그램을 테스트해야 하는 경우 파일을 각 사람에게 개별적으로 배포해야 합니다.
- 버전 관리: 전달된 버전을 추적하는 것이 어려울 수 있으며 혼란과 불일치로 이어질 수 있습니다.
- 설치 번거로움: 팀원들은 응용 프로그램 파일을 수동으로 다운로드하고 지역 장치로 전송한 다음 이전 버전을 제거하고 새 버전을 설치해야 합니다. 이 과정은 시간이 걸리고 번거로울 수 있습니다.

Firebase는 Firebase App Distribution을 통해 이러한 시나리오에 우수한 솔루션을 제공합니다. 이 도구는 신뢰하는 테스터들에게 앱을 배포하는 프로세스를 간소화합니다. Firebase 콘솔에서 팀원들을 테스터로 추가하고 콘솔을 통해 응용 프로그램을 게시함으로써, 팀원들은 이메일 통지를 받고 Firebase Tester 앱을 통해 앱을 설치할 수 있습니다. 이 방법은 앱 배포를 최적화하고 모든 이해 관계자가 쉽게 테스트를 위해 최신 버전에 액세스할 수 있도록 보장합니다.

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

Firebase App Distribution은 개발자들을 위한 매우 유용한 플러그인도 제공합니다. 이를 통해 빌드 프로세스 중에 터미널에서 애플리케이션 파일을 자동으로 배포할 수 있습니다. Android용으로는 Firebase가 Gradle 플러그인을 제공하고, iOS용으로는 Fastlane을 통해 배포할 수 있습니다.

더 많은 정보는 Firebase App Distribution을 확인해보세요.

# Crashlytics

충돌은 향상된 사용자 경험을 보장하기 위해 해결해야 할 가장 중요한 문제 중 하나입니다. 모바일 장치에서 충돌이 발생하면 애플리케이션이 종료되는데 그뿐만 아니라 사용자가 현재 상태와 작업 흐름을 모두 잃게됩니다. 이러한 중단은 사용자 불만을 유발할 수 있고, 낮은 평가나 사용자 유지에 이를 수도 있습니다.

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

크래시 이해는 중요하지만 각 사용자의 기기에서 모든 문제를 개별적으로 진단하는 것은 비효율적입니다. 여기서 Firebase Crashlytics가 등장합니다. 이는 사용자 기기에서 발생하는 크래시를 정리하고 보고하여 대규모의 크래시 데이터를 처리 가능한 문제 목록으로 제공합니다. Crashlytics는 맥락 정보를 제공하고 크래시의 심각성과 보편성을 강조하여 루트 원인을보다 빨리 파악할 수 있게 합니다.

Firebase 콘솔을 통해 크래시에 대한 자세한 정보를 얻을 수 있습니다. 이 정보에는 문제를 일으킨 코드, 문제를 겪은 특정 기기, 그리고 이러한 발생 빈도가 포함됩니다. 아래 이미지에서 확인할 수 있습니다:

![crash-details](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_6.png)

Android Studio에서 App Quality Insights Tool을 제공하여 Android 애플리케이션에서 크래시를 직접 추적할 수 있습니다. IDE와 Firebase 계정을 통합하면 자세한 크래시 보고서를 확인하고 문제를 효율적으로 디버그할 수 있습니다.

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


![Firebase Tools](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_7.png)

안드로이드 스튜디오의 App Quality Insights 도구를 사용하면 안드로이드 중요한 문제를 볼 수 있고, 필터링할 수 있으며, 스택 트레이스에서 해당 코드로 직접 이동할 수 있습니다. Firebase와 통합되어 프로젝트 내 문제를 효율적으로 디버깅하고 빠르게 해결할 수 있습니다. 더 많은 정보를 원하시면 App Quality Insights에서 Firebase Crashlytics 및 Android Vitals로부터 문제를 분석하는 것을 확인해보세요.

# 성능 모니터링

![Firebase Tools](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_8.png)


Firebase Tools 이미지와 함께 안드로이드 스튜디오의 App Quality Insights 도구를 사용하면 안드로이드 중요한 문제를 볼 수 있고, 필터링할 수 있으며, 스택 트레이스에서 해당 코드로 직접 이동할 수 있습니다. Firebase와 통합되어 프로젝트 내 문제를 효율적으로 디버깅하고 빠르게 해결할 수 있습니다. 더 많은 정보를 원하시면 App Quality Insights에서 Firebase Crashlytics 및 Android Vitals로부터 문제를 분석하는 것을 확인해보세요.

# 성능 모니터링

Firebase Tools 이미지와 함께 성능 모니터링을 할 수 있습니다. 만약 질문이 있으시다면 언제든지 물어봐주세요! 😊


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

파이어베이스는 Performance Monitoring이라는 가치 있는 도구를 제공합니다. 이 도구를 사용하면 개발자들은 전체 애플리케이션의 성능을 모니터링하고 추적하거나 매우 특정한 기능에 초점을 맞출 수 있습니다. Performance Monitoring을 사용하면 앱이 실제 세계에서 어떻게 수행되고 있는지에 대한 통찰력을 얻을 수 있으며 성능 병목 현상을 식별하고 사용자 경험을 향상시키기 위한 데이터 기반 개선을 할 수 있습니다.

이 도구를 사용하면 네트워크 연결 상태, 무거운 자원 로드 및 복잡한 UI 렌더링과 같은 애플리케이션 성능의 여러 측면을 실시간으로 모니터링할 수 있습니다. 파이어베이스 콘솔에서 제공되는 성능 보고서를 확인함으로써 병목 현상을 식별하고 특정 구현을 최적화하여 앱의 전체 성능을 향상시킬 수 있습니다.

이 도구는 앱 시작 시간, HTTP 네트워크 요청, 화면 렌더링 데이터 등을 자동으로 측정합니다. 또한, 애플리케이션의 특정 기능의 성능을 추적하기 위해 사용자 정의 코드를 추가할 수 있습니다. 이를 통해 특정 기능에 대한 상세한 통찰력을 얻고 수집된 데이터를 기반으로 최적화할 수 있습니다.

이 도구에 대해 더 알고 싶다면 파이어베이스 Performance Monitoring을 확인해보세요.

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

# Firebase Extensions

![Firebase Extensions](/assets/img/2024-06-27-BestFirebaseToolsforYourMobileApplication_9.png)

파이어베이스는 특별한 기능을 제공하는 Firebase Extensions라고 불리는 추가 도구를 제공합니다. 이 도구들은 개발자가 사전 패키지된 솔루션을 사용하여 애플리케이션 내에서 기능을 빠르게 배포, 구축 또는 분석할 수 있도록 돕습니다. Firebase Extensions는 애플리케이션이나 프로젝트 내에서 사전 정의된 이벤트가 발생했을 때 작업을 실행하는 코드 조각이며, 이들은 Cloud Functions for Firebase를 사용하여 작성됩니다.

이 확장 기능은 Firebase Extensions 허브를 통해 액세스할 수 있습니다. 여기서 다양한 유용한 확장 기능을 살펴보고 프로젝트에 통합할 수 있습니다. 추가로, 개발자 커뮤니티에 기여하거나 SaaS(서비스로서의 소프트웨어) 솔루션을 연결하기 위해 자체 Firebase 확장 기능을 개발할 수도 있습니다.

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

최근 Google Cloud 팀은 Gamini Firebase 확장 프로그램을 소개했습니다. 이 확장 프로그램을 사용하면 클라우드 Firestore에서 저장 및 관리되는 Gemini 모델을 사용하여 빠르게 챗봇을 구축할 수 있습니다. 이 확장 프로그램을 사용하면 Vertex AI에서 Generative AI를 활용하는 챗봇 시스템을 구현하기 쉬워집니다.

Firebase 확장 프로그램을 구축하는 최상의 방법으로, Stream Chat을 사용하여 인증하는 것을 고려해보세요. 이 확장 프로그램을 사용하면 Firebase 인증 기반의 사용자 정보를 인증할 수 있습니다. Stream SDK 고객은 소셜 식별, 전화번호 확인, 이메일 및 비밀번호 인증과 같이 Firebase가 제공하는 인증 방식을 활용하여 Stream 인증을 Firebase 인증과 쉽게 통합할 수 있습니다. 자세한 내용은 "Announcing Stream Firebase Extensions for Chat and Feeds"를 확인해보세요.

# 결론

이 글에서는 Firebase가 비즈니스 및 애플리케이션 성능을 향상시키는 데 제공하는 가장 유용한 도구 몇 가지를 살펴봤습니다. Firebase는 주로 모바일 애플리케이션을 제공하는 대부분의 회사들에 이점을 제공하며, 특히 복잡한 시스템을 처음부터 구축하거나 모바일 친화적인 백엔드 인프라를 개발하기 위한 예산이 제한된 스타트업에게 많은 혜택을 줍니다.

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

본 문서에 궁금한 점이나 피드백이 있으면 작성자를 Twitter에서 @github_skydoves 또는 GitHub에서 찾아보세요. Stream에 대해 최신 정보를 받고 싶다면 Twitter에서 @getstream_io를 팔로우해보세요. 

언제나 즐거운 코딩되세요!

— Jaewoong

원문은 GetStream.io에 게시되었습니다.