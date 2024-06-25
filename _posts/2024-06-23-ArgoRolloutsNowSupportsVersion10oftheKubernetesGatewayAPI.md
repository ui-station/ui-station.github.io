---
title: "Argo Rollouts가 이제 Kubernetes Gateway API 10 버전을 지원합니다"
description: ""
coverImage: "/assets/img/2024-06-23-ArgoRolloutsNowSupportsVersion10oftheKubernetesGatewayAPI_0.png"
date: 2024-06-23 01:09
ogImage:
  url: /assets/img/2024-06-23-ArgoRolloutsNowSupportsVersion10oftheKubernetesGatewayAPI_0.png
tag: Tech
originalTitle: "Argo Rollouts Now Supports Version 1.0 of the Kubernetes Gateway API"
link: "https://medium.com/argo-project/argo-rollouts-now-supports-version-1-0-of-the-kubernetes-gateway-api-acc429729e42"
---

안녕하세요! Argo Rollouts, 쿠버네티스를 위한 프로그레시브 딜리버리 컨트롤러,이(가) 새로운 쿠버네티스 게이트웨이 API(다음 세대 인그레스/서비스 메시 표준)를 지원하는 새로운 플러그인을 통해 이제 새로운 기능을 제공합니다.

이제 거의 모든 인그레스, 게이트웨이 또는 서비스 메시를 사용하여 쿠버네티스에서 카나리 배포를 수행할 수 있습니다. 지원되는 공급업체의 전체 목록을 확인하고 자세한 내용을 알아보세요.

Argo Rollouts은 프로그레시브 딜리버리 시나리오(블루/그린 및 카나리 배포)에 중점을 둔 Argo 패밀리 프로젝트 중 하나입니다.

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

카나리 배포에서 Argo Rollouts는 애플리케이션의 새 버전으로 트래픽을 점진적으로 이동시킬 수 있는 트래픽 공급자를 선택적으로 사용할 수 있습니다.

![Argo Rollouts](/assets/img/2024-06-23-ArgoRolloutsNowSupportsVersion10oftheKubernetesGatewayAPI_1.png)

쿠버네티스를 위한 여러 네트워킹 제품이 있으며, 대부분은 인그레스(클러스터로 들어오는 트래픽) 또는 서비스 메시(클러스터 내에서의 트래픽)의 형태로 제공됩니다.

Argo Rollouts는 이미 여러 인그레스 및 서비스 메시를 내장 지원하고 있습니다. 이러한 도구들의 지원은 Argo Rollouts의 인트리 소스 코드의 일부이며, 새 도구를 도입하는 과정이 불편합니다.

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

- Argo Rollouts 프로젝트 전체의 소스 코드를 확인해보세요.
- 전체 컨트롤러가 어떻게 작동하는지 이해하세요.
- 본 Argo Rollouts 릴리즈에 병합될 풀 리퀘스트를 제출하세요.
- 프로젝트 유지자가 승인/병합하도록 기다리세요.
- Argo Rollouts의 새로운 릴리즈가 나올 때마다 툴 특화 코드를 유지/추적해야합니다.

사람들이 Argo Rollouts와 함께 사용하고 싶어하는 여러 네트워킹 도구가 있습니다. 이 복잡한 과정으로 컨트롤러가 네이티브 방식으로 이러한 도구를 지원하지 못했습니다.

그러나 최근 두 가지 발전으로 이 과정이 더 이상 필요하지 않아졌습니다.

첫 번째는 Argo Rollouts가 1.5 버전부터 트래픽 플러그인을 지원한다는 점입니다. 플러그인이 어떻게 작동하는지 릴리스 공지에서 설명했습니다.

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

두 번째로 소개할 것은 새로운 Kubernetes Gateway API입니다.

# Gateway API — 쿠버네티스의 서비스 매쉬와 인그레스를 위한 통합 표준

Gateway API는 쿠버네티스 SIG 프로젝트로, 쿠버네티스의 모든 네트워킹 솔루션을 통합하여 기본 인그레스 API에 누락된 몇 가지 기능을 추가하면서 동시에 모든 서비스 매쉬 솔루션을 통합합니다.

Gateway API는 쿠버네티스 인그레스 API의 다음 세대로 생각할 수 있으며, 동시에 (이제는 폐지된) SMI 표준의 다음 세대로도 볼 수 있습니다.

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

쿠버네티스 게이트웨이 API는 인프라를 정의하는 사람과 인프라를 사용하는 사람 사이에 명확히 구분을 정의했다는 점에서 주목할 만합니다.

![이미지](/assets/img/2024-06-23-ArgoRolloutsNowSupportsVersion10oftheKubernetesGatewayAPI_2.png)

동시에, 모든 주요 네트워킹 공급업체들의 일치된 지원을 받고 있습니다. 대부분의 네트워킹 솔루션은 게이트웨이 API에 대한 초기 지원을 추가했거나 해당 API를 따를 것임을 선언했습니다.

API 지원자의 상세 목록은 구현 목록에서 확인하실 수 있습니다.

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

# Argo Rollouts 및 게이트웨이 API - 젤리와 채피 버터처럼

Argo Rollouts에는 이제 Gateway API를 지원하는 플러그인이 있습니다. 저희는 쿠버네티스 게이트웨이 API의 0.x 버전을 대상으로 한 초기 개발을 시작했으며 테스트된 구현체 목록을 계속 확장하고 있습니다.

이것은 Argo Rollouts를 사용하여 쿠버네티스 게이트웨이 API의 현재 및 향후 구현체와 함께 캐너리 배포에 사용할 수 있음을 의미합니다. 얼마나 멋진가요?

![ArgoRolloutsNowSupportsVersion10oftheKubernetesGatewayAPI_3](/assets/img/2024-06-23-ArgoRolloutsNowSupportsVersion10oftheKubernetesGatewayAPI_3.png)

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

쿠버네티스 게이트웨이 API는 2023년에 1.0 버전을 출시했으며, 최신 버전의 플러그인(0.3)은 이제 1.0 사양에 맞춰 컴파일되었습니다.

귀하의 네트워킹 제공 업체로 플러그인을 테스트하시고 궁금한 점이나 문제가 있으면 언제든지 알려주시기 바랍니다.

캐너리 배포를 즐기세요!

Unsplash의 Shivansh Singh 사진을 참고해주세요.
