---
title: "프론트엔드를 위한 백엔드BFF 패턴 - 왜 이를 알아야 할까요"
description: ""
coverImage: "/assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_0.png"
date: 2024-05-20 17:51
ogImage: 
  url: /assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_0.png
tag: Tech
originalTitle: "Backend for frontend (BFF) pattern— why do you need to know it?"
link: "https://medium.com/mobilepeople/backend-for-frontend-pattern-why-you-need-to-know-it-46f94ce420b0"
---


일반적인 문제는 모바일 앱에 API를 통합해야 할 때 발생합니다. 기존 시스템에 모바일 앱을 만들어야 하는 경우를 상상해보죠. 이 시스템은 API를 제공하여 웹 클라이언트만을 위한 단일 솔루션이었습니다.

![이미지](/assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_0.png)

고객의 아이디어는 새로운 모바일 앱에만 국한되지 않습니다. 음성 어시스턴트와 저희 API를 사용할 3rd party 서비스도 고려 중입니다. 그래서 이 문제는 한 API가 이러한 종류의 클라이언트를 모두 지원해야 하며, 그들의 요구 사항과 유지 보수에 신경 써야 한다는 것입니다.

![이미지](/assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_1.png)

<div class="content-ad"></div>

이러한 경우에 도움이 될 수 있는 해결책은 Backend For Frontend 패턴입니다.

# Backend for Frontend (BFF) 디자인 패턴

사용자를 대상으로 한 응용 프로그램을 두 구성 요소로 고려해야 합니다. 클라이언트 측 응용 프로그램은 보안 범위 외부에 위치하고 서버 측 구성 요소 (BFF)는 보안 범위 내부에 위치합니다. BFF는 API 게이트웨이 패턴의 변형이지만 각 클라이언트 유형별로 마이크로서비스와 각 클라이언트 간의 추가 계층을 제공합니다. 단일 진입점 대신 여러 게이트웨이를 소개합니다. 이를 통해 모바일, 웹, 데스크톱, 음성 비서 등 각 클라이언트의 요구를 대상으로 하는 맞춤형 API를 갖고 하나의 공간에 모두 모아두었을 때 발생하는 부풀림을 줄일 수 있습니다. 아래 이미지는 작동 방식을 설명합니다.

![백엔드 포 프론트엔드(BFF) 패턴](/assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_2.png)

<div class="content-ad"></div>

# 친구를 위한 최상의 API 만드는 방법

저희의 클라이언트와 그의 Web API로 돌아와 봅시다. 모바일 관점에서 중요한 데이터 일부만 사용하는 레거시 XML 기반 솔루션일 수 있습니다. BFF를 사용하면 중요한 데이터를 추출하고 더 나은 형식(예: JSON)으로 변환할 수 있습니다. 특정 앱 화면이나 기능에 전용 엔드포인트를 만들고 필요한 데이터를 제공하는 것도 좋은 방법입니다. 예를 들어 아래 이미지에서 녹색 및 노란색 부분이 레거시 API에서 별도로 추출될 수 있음을 보여줍니다. 왜냐하면 다른 모바일 앱 화면이 필요로하기 때문이죠.

![이미지](/assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_3.png)

여기에서 이 단계에 대한 몇 가지 제안을 찾으실 수 있습니다:

<div class="content-ad"></div>

- UI/UX에 중점을 두고 필요한 데이터에 집중하세요.
- 처음부터 모든 것을 일반화하려고 하지 마세요. 이렇게 하면 그 컴포넌트가 여러 조직에서 사용되며 많은 사람들이 기여하고자 할 수 있습니다.
- 특정 기능보다는 일반적인 사용 전략보다 먼저 사용하세요. 이것이 한 클라이언트에 특화된 깔끔한 API를 유지하는 가장 좋은 방법입니다.
- 3법칙을 따르세요.

# BFF의 이유

백엔드와 프론트엔드의 분리는 UI/UX에 중점을 두기 때문에 프론트엔드 팀이 고유한 요구사항을 제공하는 전용 백엔드 팀을 가질 수 있어 시장 진입 시간을 단축시킵니다. 한 프론트엔드의 새로운 기능 릴리스가 다른 프론트엔드에 영향을 미치지 않습니다.

우리는 API를 훨씬 쉽게 유지하고 수정할 수 있으며, 심지어 특정 프론트엔드를 위한 API 버전 관리를 제공할 수 있습니다. 이는 사용자가 앱을 즉시 업데이트하지 않는 모바일 앱 관점에서 큰 이점입니다.

<div class="content-ad"></div>

프론트엔드와 백엔드 측면에서 시스템을 단순화해봅시다. 타협이 필요하지 않습니다.

BFF는 불필요하거나 민감한 데이터를 프론트엔드 애플리케이션 인터페이스로 전송하기 전에 숨길 수 있어서, 3rd party 서비스를 위한 키 및 토큰을 BFF에서 저장하고 사용할 수 있습니다.

포맷된 데이터를 프론트엔드로 전송할 수 있어 로직을 최소화할 수 있습니다.

이에 더불어 성능 향상과 모바일에 대한 최적화 기회를 제공해줍니다.

<div class="content-ad"></div>

# BFF를 프로젝트에 소개하는 방법

우리 예시에서는 모바일 클라이언트 전용 BFF를 생성하여 시작했으며, 첫 번째 버전에서 웹 클라이언트는 동일한 레거시 API를 사용했습니다. 이 단계는 기존 시스템의 리팩토링 없이 하나의 BFF만 다루기 때문에 보통 작은 단계입니다. 릴리스 후에는 유지 및 개발 관점에서 고객 및 다른 팀 (백엔드, 다른 프론트엔드 팀)에게 그 이점을 보여줄 수 있습니다. 이 경우, 레거시 API 전처리 및 변환과 관련된 전체 부분이 성능 관점에서 별도의 마이크로서비스로 추출되었습니다. 레거시 단일체 시스템과 API의 미래 리팩토링 요구가 이 추출의 또 다른 이유였습니다.

![이미지](/assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_4.png)

그 다음 단계는 기존 프론트엔드를 위한 전체 BFF 구조였습니다. 아래 그림에서 각각의 마이크로서비스 유형과 사용법을 보여주는 도표를 확인해주세요:
- 파란색은 리팩토링 중인 모노리스 시스템 위에 위치한 레이어들입니다.
- 녹색은 두 BFF에서 동시에 사용되는 서비스입니다.
- 반면, 노란색은 배포 관점에서 중복되어 각각의 BFF에 전용으로 제공되며 더 나은 성능을 제공합니다.
- 빨간색은 모바일 BFF 전용이며, 알림, 메시지 큐 등과 같은 모바일에 특화된 솔루션을 제공합니다.

<div class="content-ad"></div>


![BFF Pattern](/assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_5.png)

# BFF에서의 용납력

용납력은 BFF의 가장 중요한 기능으로, 클라이언트(프론트엔드)에서의 대부분의 문제를 BFF 계층에서 처리할 수 있게 해줍니다. 어떠한 마이크로서비스 변경이 발생해도 비상 배포 없이 모든 BFF에서 제어할 수 있습니다. 모바일 애플리케이션의 업데이트는 스토어에서 동시에 진행되어야 하는 쉬운 작업이 아닙니다. 리뷰와 같은 추가 시간이 필요하며, 때로는 결과가 예상치 못한 거절일 수도 있습니다. BFF 솔루션을 통해 클라이언트(프론트엔드)별로 버전 관리와 하위 호환성을 개별적으로 다룰 수 있습니다. 전체 용납력 및 해당 전략은 BFF 계층에서 처리하고 관리할 수 있습니다. 예를 들어, 저희 시스템에서는 각 모바일 클라이언트마다 별도의 BFF를 도입하여 한 클라이언트에 문제가 발생해도 시스템 전체에 영향을 미치는 문제를 피할 수 있습니다. 자가 DDoS를 실행하는 등의 문제가 있는 경우 해당 BFF를 시스템에서 분리하고 영향을 받지 않으면서 내부 문제를 조사할 수 있습니다. 이는 제3자 서비스를 위해 특별히 설계된 BFF에 대한 좋은 전략입니다.

![BFF Pattern](/assets/img/2024-05-20-BackendforfrontendBFFpatternwhydoyouneedtoknowit_6.png)


<div class="content-ad"></div>

# BFF를 사용하는 시점은?

다른 패턴들처럼, BFF를 애플리케이션에 사용하는 것은 문맥과 따라야 하는 아키텍처에 따라 다릅니다.

앞단(front-end) 하나만 제공하는 애플리케이션의 경우, 서버 측에서 상당한 양의 집계 작업이 요구될 때에만 Backend For Frontend가 의미가 있을 것으로 생각됩니다. 그래도 그런 경우라도 API 게이트웨이 개념을 사용할 수 있습니다. BFF 스타일은 프론트엔드 유형의 수를 늘릴 계획이 있을 때 고려할 수 있습니다.

애플리케이션이 특정 프론트엔드 인터페이스를 위해 최적화된 백엔드를 개발해야 하거나, 클라이언트가 백엔드에서 집계를 필요로 하는 데이터를 소비해야 하는 경우, BFF는 확실히 적합한 선택지입니다. 물론, 추가 BFF 서비스를 배포하는 비용이 높다면 재고해야 할 수도 있지만, 대부분의 경우에 BFF가 제공할 수 있는 관심사 분리는 상당히 매력적인 제안으로 여겨질 수 있습니다.

<div class="content-ad"></div>

# 결론

Backend For Frontend은 개발자와 더 중요한 사용자 및 사용자 경험을 염두에 두고 만들어진 디자인 패턴입니다. 이는 고객을 만나고 상호 작용하고 서비스하는 애플리케이션의 증가에 대한 대답으로, 다양하고 계속 변화하는 필요를 충족하면서 일관성을 유지합니다.

# Droidcon Berlin의 BFF에 관한 비디오

# 다음 기사

<div class="content-ad"></div>

https://medium.com/mobilepeople/bff-backend-is-a-friend-for-frontend-pros-and-cons-71857725fe7f