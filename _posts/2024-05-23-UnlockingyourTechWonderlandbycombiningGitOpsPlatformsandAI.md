---
title: "기술 원더랜드의 문을 열어보세요 GitOps, 플랫폼 및 AI를 결합해 보세요"
description: ""
coverImage: "/assets/img/2024-05-23-UnlockingyourTechWonderlandbycombiningGitOpsPlatformsandAI_0.png"
date: 2024-05-23 14:21
ogImage:
  url: /assets/img/2024-05-23-UnlockingyourTechWonderlandbycombiningGitOpsPlatformsandAI_0.png
tag: Tech
originalTitle: "Unlocking your Tech Wonderland by combining GitOps, Platforms and AI"
link: "https://medium.com/itnext/unlocking-your-tech-wonderland-by-combining-gitops-platforms-and-ai-a200d0e0449b"
---

<table>
  <tr>
    <td>2024-05-23</td>
    <td>Unlocking your Tech Wonderland by combining GitOps, Platforms, and AI</td>
    <td>0.png</td>
  </tr>
</table>

지난 몇 년 동안 많은 기술이 등장하고 사라지는 과정을 지켜봤고, 많은 사람들과 기업이 클라우드 네이티브 여정을 함께했습니다. 종종 듣는 말들 중에는 "왜 쿠버네티스는 복잡한가요?"와 "문제 XYZ를 해결할 하나의 해결책만 없는 이유는 무엇인가요?"라는 것들이 있었습니다. 동시에 플랫폼 엔지니어링과 AI가 현재 떠오르고 있는데, 저는 이 세 가지 주제가 서로 관련이 있다고 생각하기 때문에 함께 자세히 살펴보도록 하겠습니다.

# 쿠버네티스의 등장 및 왜 그것이 복잡한가

쿠버네티스는 응용 프로그램 인프라를 구축하기 위한 플랫폼 오케스트레이터로, 기반이 되는 인프라에 신경 쓰지 않고 기반을 만들도록 돕습니다. 우리가 조명을 비추면 (저는 예전 시스템 엔지니어로서) 익숙한 것들이 많이 보일 수 있습니다.

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

- 시스템에서 잘 알려진 운영 절차들을 위한 추상화: Kubernetes에서 객체를 생성할 때, 우리는 과거에 우리가 한 것을 설명만 할 뿐입니다. 예를 들어, 인그레스 객체를 만들 때, 시스템에게 "이 패턴과 일치하는 트래픽을 이 서비스로 전달하는 역방향 프록시를 구성해주세요" 라고 말합니다. 파드 객체를 만들 때, 우리는 시스템에게 유닉스 네임스페이스를 생성하고 그 안에 프로세스를 캡슐화하며 환경 변수, 요청, 제한 등을 설정하도록 지시합니다.
- 컨테이너: 컨테이너 개념은 완전히 새로운 것은 아니며 그 주변에 마법도 없습니다. 우리는 BSD jails로부터 이를 배웠고, OpenVZ나 LXC로 컨테이너를 만들었으며 마침내 Docker로 그 가능성을 확장했습니다.
- 확장성과 탄력성: 시스템 엔지니어로서, 저는 종종 더 강력하고 확장 가능한 시스템을 만들기 위해 직면했습니다. 과거에 우리는 이를 위해 어떤 모험적인 구성을 만들었고, 많은 사람들은 클러스터 관리자, 부하 분산, 복제 및 하트비트 메커니즘과 맞닥뜨렸을 것입니다. 그들은 일을 잘했지만 구성하기 쉽지 않았고 종종 스스로 문제를 일으켰습니다. 익숙한 느낌이겠죠?

제가 쿠버네티스 여정을 시작했을 때, 과거의 이러한 문제들을 보고 이를 해결해주는 것을 발견했습니다. 예를 들어, 간단한 구성 객체로 서비스를 여러 노드에 분산해서 로드 밸런싱할 수 있고, 수백 개의 노드로 자동으로 확장할 수 있습니다. 사실, 이러한 메커니즘들이 다소 무섭게 느껴질 수 있지만 결국에는 이를 배우고 시스템에 적용하는 것은 매우 간단합니다.

아래 그림은 쿠버네티스로 가능한 자동화의 일부를 보여줍니다. 이 예시는 여러 컴포넌트에 작업 수행을 지시하는 매니페스트를 한 번에 만들 수 있다는 것을 보여줍니다. 이 경우에는 클러스터로의 인그레스 경로 생성, TLS 인증서 발급, 특정 호스트 이름을 위한 DNS 이름 생성 등이 될 것입니다. 물론, 이러한 지점에 도달하기 위해 약간의 구성 작업이 필요하지만 (전에 언급한 예시들보다는 훨씬 적습니다), 결국에는 복잡한 것들을 아주 간단한 방법으로 사용할 수 있도록 합니다.

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

몇 년 전, Kelsey Hightower가 한 게시물에서 다음과 같이 썼어요:

"이것이란 암시적으로는 우리가 쿠버네티스를 애플리케이션을 배포하는 데 사용할 수 있다는 것을 의미하지만, 실제로 우리가 플랫폼을 구축하는 데 도움이 되는 플랫폼으로 고려하면 더 많은 것을 얻을 수 있습니다. 이로 인해 다음 질문에 이르게 됩니다:"

# 문제 XYZ를 해결할 수 있는 하나의 해결책만 있는 이유는 무엇인가요?

쿠버네티스와 클라우드 네이티브 주변에 엄청난 생태계가 형성되었고, 많은 도구들이 사람들이 문제를 해결하는 데 도움을 줬어요. 우리가 그림 1에서 본 것처럼, 인그레스 오브젝트를 통해 들어오는 네트워크 트래픽을 구성하고, 인증서를 요청하거나 쿠버네티스 컨트롤러를 통해 DNS 항목을 구성하는 것은 오늘날 매우 간단해졌어요. 시간이 지남에 따라 많은 도구들이 이 생태계에 합류했고, 종종 사람들은 왜 같은 문제를 해결하는 것처럼 보이는 다양한 도구들이 존재하는지 묻곤 해요.

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

![Tech Wonderland](/assets/img/2024-05-23-UnlockingyourTechWonderlandbycombiningGitOpsPlatformsandAI_2.png)

G. Hohpe’s book “Cloud Strategy”에서 회사에 가장 적합한 클라우드 공급업체를 찾는 데 관한 인용문을 발견했습니다.

나는 클라우드 네이티브 랜드스케이프에 대해서도 동일하게 생각합니다(도표 2 참조). 다양한 옵션이 있지만 일부는 귀하의 요구 사항(및 전략)에 더 잘 맞을 수 있고 다른 일부는 그렇지 않을 수 있습니다. 예를 들어, 섬세하고 명료한 접근 방식을 따르는 도구를 선택할 수 있지만, 구성하거나 자체 의견을 구현할 수 있도록 지원해주는 도구도 있을 수 있습니다. 또한, 모든 것이 지원에 관한 문제일 수 있습니다. 가장 좋은 커뮤니티 주도형 오픈 소스 프로젝트가 제품마다 24/7 벤더 지원이 필요하다는 회사 정책을 준수해야 하는 경우에는 귀하의 요구 사항과 일치하지 않을 수 있습니다.

우리는 이러한 것들을 끊임없이 진행할 수 있다고 상상할 수 있다고 생각하지만, 랜드스케이프의 각 도구는 더 많은 아이디어를 제공하며 더 많은 옵션을 제공하고(항상 그것들이 필요하지 않을 수 있지만) 귀하의 응용 프로그램에 대한 견고한 플랫폼을 구축하는 데 도움을 줍니다. 이를 통해 내가 좋아하는 이야기의 핵심인 플랫폼 엔지니어링으로 진행하고 싶습니다.

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

# 플랫폼 엔지니어링의 등장

지난 두 섹션에서는 왜 사람들이 쿠버네티스를 복잡하게 생각하는지 (내 의견으로는 그렇지 않다고 생각합니다) 그리고 비슷한 문제를 해결하기 위해 수많은 도구가 있는 이유에 대해 설명했습니다. 이제 이러한 구성 요소들을 함께 조합해서 왜 쿠버네티스 위에 플랫폼을 구축하는 것이 절대적으로 의미가 있는지 알아보겠습니다.

몇 년 전으로 돌아가서 회사들이 쿠버네티스를 채택하기 시작했던 시점에 대해 생각해 봅시다. 어떤 사람들은 새로운 멋진 기술(플랫폼 오케스트레이터)에 대해 들었고 이 기술을 기술 스택에 도입하고 싶어했습니다. 이 기간 동안 kubectl을 사용하여 응용 프로그램을 배포하고, 해당 플랫폼 구성 요소를 설치하고, Helm이라는 패키지 관리자에 대해 배워서 실천을 시작했습니다. 대기업의 경우, 이 일은 많은 기능 팀들에 의해 수행되었을 수 있고, 어느 순간에 회사는 이러한 것들을 함께 두어야 한다고 결정했습니다. 이 시점에서 다음과 같은 질문들에 직면하게 됩니다:

- 어떻게 해야만 할 수 있을까요? 응용 프로그램 A의 리소스 소비가 응용 프로그램 B의 것과 간섭하지 않게 보장할까요?
- 인그레스 컨트롤러로 ingress-nginx 또는 traefik을 사용해야 할까요? 그리고 누가 리소스를 마이그레이션해야 할까요?
- 응용 프로그램 팀이 현재 사용 중인 5가지 메커니즘 중에서 선택해야 할 때 어떻게 응용 프로그램을 배포해야 할까요?
- 개발자로서 클라우드 환경에서 데이터베이스를 사용하고 싶습니다. 어떤 것을 선택해야 할까요? 또 누가 그것을 관리해야 할까요?

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

독립적인 응용 프로그램 팀마다 도구 스택에 약간의 노력을 기울였다고 상상해보세요. 대부분의 팀은 그것에 만족하고 있습니다. 그러나 이 방식으로 진행하면 확장이 잘 되지 않을 수 있으므로, 개발팀을 더 쉽게 만들고 제품 관리자를 더 만족시키기 위해 일부 사전 정의된 빌딩 블록을 사용하는 것이 합리적일 수 있습니다. 여러분의 플랫폼 엔지니어링 여정이 시작되었습니다.

Martin Fowler와 Evan Bottcher는 이렇게 플랫폼을 정의합니다:

다음과 같이 관련 부분으로 나눠봅시다:

- 플랫폼은 기반이다: 개발자들의 운영 부담과 기초 작업을 줄이고, 플랫폼에서 제공되는 몇 가지 요소가 개발자들이 직접 만들어야 할 것이라는 점을 원합니다.
- 셀프 서비스 API: 개발자들은 셀프 서비스 API를 통해 플랫폼을 사용할 수 있어야 합니다. 제 의견으로는 이것이 RESTful API일 수도 있지만, Kubernetes 객체로 구성돼 Git 리포지토리에 커밋되고 승인된 것이라면 안심할 수도 있습니다.
- 도구: 플랫폼을 사용하는 데 필요한 모든 것
- 서비스: 개발자들이 제품을 제공하는 데 도움이 되는 플랫폼의 일부인 것들, 예를 들어 시크릿 관리, 데이터베이스, 배달 도구, 메시지 큐잉 등
- 지식과 지원: 플랫폼을 사용하는 사람들을 돕는 사항 및 서비스

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

IT에서 배운 한가지는 우리가 종종 무언가를 한 번 이상, 때로는 여러 차례 만들어낸다는 것입니다. 많은 경우, 우리는 이전에 만들어둔 인프라를 청사진으로 사용하고 많은 것을 반복합니다. 유감스럽게도, 이는 종종 해결책과 가끔은 유지보수가 잘 되지 않는 것들을 포함합니다. 플랫폼 접근 방식으로 수렴하는 것은 시간이 지남에 따라 개선될 수 있는 패턴 및 템플릿 카탈로그를 구축하는 데 도움이 될 수 있습니다 (각 IaC 템플릿을 버전화된 서비스로 생각해 보세요). 넓은 범위에서 적용될 때 해결책을 줄이는 데 관심 있는 많은 사람들이 있습니다. 플랫폼을 강아지집을 짓는 데 필요한 모든 도구 및 자재를 제공하는 도구 시장으로 생각해 보세요. 더 나아가, 프로젝트를 돕는 영업사원들과 문서가 있을 수 있습니다.

![image](/assets/img/2024-05-23-UnlockingyourTechWonderlandbycombiningGitOpsPlatformsandAI_3.png)

플랫폼을 구축할 때 주요 주제 중 하나는 배포 전략입니다. 이는 시작부터 여러 가지 질문을 던지며 여러 가지 방법으로 플랫폼을 결정할 수 있습니다. 예를 들어, 어떤 서비스를 어떤 클라우드에서 제공할 것이며 어떻게 실행할 것인지 등의 질문에 대해 다룰 수 있습니다. 작은 예로, 모든 애플리케이션을 Kubernetes에서 제공하고 데이터베이스와 같은 종속 지원 서비스를 제공 업체의 PaaS 서비스로 사용하고 싶다는 결론에 도달할 수 있습니다. 이로 인해 다음과 같은 플랫폼 관련 솔루션이 나오게 됩니다:

- 플랫폼 기능을 제공하는 방법
- 애플리케이션 및 인프라 구성요소를 제공하는 방법
- 솔루션 카탈로그의 첫 부분

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

IaC 솔루션과 소프트웨어를 전달하는 GitOps Controller에 자주 의존하는 편입니다. 이러한 솔루션을 기반으로 몇 가지 더 궁금증이 생길 것입니다. 예를 들어, 비밀 정보는 어떻게 전달할까요? DNS 항목은 어떻게 다룰까요? 버전 관리는 어떻게 진행할까요? 이러한 질문 목록은 끝없이 계속될 수 있으며 어떤 질문은 먼저 나오고 다른 것은 나준히 알려질 것입니다. 배포 전략은 현재 사용 가능한 애플리케이션과 해당 현재 메커니즘에 따라 당연히 달라질 것입니다. 그러나 애플리케이션과 배포 전략을 통해 지금까지 생각하지 못했을지도 모를 여백과 문제점을 찾게 될 것입니다. 하나의 중요한 포인트는 전달할 아티팩트의 유형과 표준화할 수 있는 방법입니다. 예를 들어, 컨테이너를 전달하고 배포 설명에 대한 내부 표준(예: manifests 및 helm charts)이 있는 경우 모든 것이 더 쉬워집니다.

이 모든 과정을 거치면서 플랫폼은 발전하고 해당 구조를 정의하게 될 것입니다. Figure 4는 프로덕션 시스템에서 고려해야 할 내용의 예시를 보여줍니다. 개발자 포털을 통해 템플릿 활용 및 서비스에 대한 통찰력을 얻을 수 있으며 정보를 저장하는 리포지토리, 또한 서비스 인터페이스 및 개발자가 서비스에서 사용할 수 있는 서비스도 포함됩니다.

이러한 플랫폼은 개발자에게 엄청난 가치를 제공할 수 있지만, 이들을 강요하여 그들이 만족하지 않는 프로세스에 밀어 넣어서는 안된다는 점을 명심해야 합니다.

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

# 개발자를 배려하며 과거를 생각해보기

플랫폼을 제공할 때, 주요 이해관계자는 그것을 사용하는 개발자들입니다. 따라서, 그들을 염두에 두고 그들의 의견을 물어보고 플랫폼 사용에 대해 교육해야 합니다. 또한, 사람들이 자신의 도구를 선택하고 이러한 방식으로 것들을 구축한 이유가 있습니다. 이로 인해 플랫폼 구축은 일상적인 해결책이 아니지만 여정에서 도움이 되는 패턴이 있습니다.

- 플랫폼의 일부분으로 사람들을 만들기: 질문에 대한 답변을 얻고 최선을 다해 문제를 해결하고 싶어하는 사람들이 있을 수 있습니다. 사람들은 기술에 대해 이야기하는 것을 좋아하며, 서로 이야기를 나누면 더 나은 해결책을 찾을 수 있습니다.
- 오픈/내부 소싱: 공유할 수 있는 아티팩트(예: 인프라 템플릿 및 구성)를 만들 때, 이를 회사 내에서 전역적으로 공유하고 다른 팀이 재사용할 수 있도록 하는 것이 유용할 수 있습니다. 이 경우, 모범 사례와 패턴이 수립되어 사람들이 전진하는 데 도움이 될 것입니다. 또한, 사람들은 풀/머지 요청을 제출하고 플랫폼에 기여할 수 있습니다.
- 커뮤니티 구축: 내부 모임을 개최하고, 플랫폼을 기반으로 한 시스템을 소개하고 성공을 전달하세요. 이렇게 하면 플랫폼이 부각되고 해당 주제에 더 많은 사람들이 끌리게 될 것입니다.

또한, 모든 회사는 각자의 역사, 내부 가치 및 프로세스를 가지고 있습니다. 플랫폼 구축을 시작할 때, 이를 염두에 두고 사람들을 어떻게 차지할지 알아보려고 노력해야 합니다.

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

# AI에 대해 다루는 제목을 보면서 함께 멋진 것들에 대해 이야기해주셨네요!

그동안 AI와 무슨 상관이 있는지, 그리고 여러분의 여정에서 어떻게 도움을 줄 수 있는지 궁금해하셨을 수도 있습니다. 이 기사의 처음에는 Kubernetes의 복잡성과 왜 일부 사람들이 어려워하는지에 대해 이야기했습니다. Kubernetes와 함께 작업하는 사람들을 돕기 위해 많은 노력이 기울여지고 있지만, 경험 많은 엔지니어조차도 문제를 해결하는 것에 어려움을 겪을 수 있습니다.

Kubernetes와 AI 영역에서의 최초 프로젝트 중 하나인 K8sGPT는 Kubernetes 환경의 문제 해결을 돕기 위해 시작되었습니다. 따라서 Kubernetes 클러스터에서 간단한 명령(k8sgpt analyze — explain)을 실행하면, 다양한 AI 제공 업체를 기반으로 한 잘못된 구성과 그 해결책을 찾을 수 있습니다. 또한 Developer Portals인 Backstage에서도 사용될 수 있을 것입니다.

<img src="/assets/img/2024-05-23-UnlockingyourTechWonderlandbycombiningGitOpsPlatformsandAI_5.png" />

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

이러한 것들은 다양한 다른 도구들과 결합할 수 있습니다. 예를 들어, GitOps 도구를 사용하여 애플리케이션을 전달하고 분석을 실행한 후, 애플리케이션이 제대로 작동하는지 확인할 수 있습니다. 이와 같은 문제를 감지하고 자동으로 해결하기 위한 노력이 이미 있습니다. 내 최근 데모 중 하나에서는 GitOps 컨트롤러를 사용하여 애플리케이션을 배포하고, Keptn을 사용하여 기능을 유효성 검사하고, K8sGPT를 내장하여 배포에서 문제를 찾아내었습니다.

# 요약

본문은 플랫폼이 클라우드 네이티브 여정에서 어떻게 도와줄 수 있는지와 어떤 문제를 해결하는지에 대한 개요를 제공했어요. 시작할 때는 Kubernetes의 복잡성에 대한 대략적인 개요가 있었습니다. 그 후에는 대규모 클라우드 네이티브 생태계에 대해 이야기했고, 모든 것은 플랫폼으로 이어지도록 조합되었습니다. 이 섹션에서 배송 전략에 대해 작업하는 것이 문제를 해결하고(새로운 문제를 찾아내는 것이 가능) 도움이 된다는 것을 배웠습니다. 또한 플랫폼 구축과 표준화는 대부분 사람들에 관한 것이라는 것도 알게 되었습니다. 마지막으로 플랫폼에서의 AI와 Kubernetes의 복잡성을 줄일 수 있는 방법에 대해 알아보았습니다.

# 참고문헌

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

- Hohpe, G. (2020). Cloud Strategy: A Decision-Based Approach to Successful Cloud Migration.
- CNCF Platforms Working Group. (2023). CNCF Platforms Whitepaper. [https://tag-app-delivery.cncf.io/whitepapers/platforms/](https://tag-app-delivery.cncf.io/whitepapers/platforms/)
- K8sGPT, [https://k8sgpt.ai](https://k8sgpt.ai)
- Backstage K8sGPT Plugin, [https://github.com/suxess-it/backstage-plugin-k8sgpt/blob/main/README.md](https://github.com/suxess-it/backstage-plugin-k8sgpt/blob/main/README.md)
