---
title: "4년 동안 스타트업의 인프라를 운영하면서 내가 지지하거나 후회하는 인프라 결정 거의 모든 것"
description: ""
coverImage: "/assets/img/2024-05-27-AlmostEveryinfrastructuredecisionIendorseorregretafter4yearsrunninginfrastructureatastartup_0.png"
date: 2024-05-27 17:01
ogImage:
  url: /assets/img/2024-05-27-AlmostEveryinfrastructuredecisionIendorseorregretafter4yearsrunninginfrastructureatastartup_0.png
tag: Tech
originalTitle: "(Almost) Every infrastructure decision I endorse or regret after 4 years running infrastructure at a startup"
link: "https://medium.com/@cep21/almost-every-infrastructure-decision-i-endorse-or-regret-after-4-years-running-infrastructure-at-d2aeba3b6a45"
---

## 기술 스타트업 인프라 추천 도구 모음

![이미지](/assets/img/2024-05-27-AlmostEveryinfrastructuredecisionIendorseorregretafter4yearsrunninginfrastructureatastartup_0.png)

저는 지난 4년 동안 스케일을 빠르게 키워야 했던 스타트업의 인프라를 이끌어 왔습니다. 처음부터 회사가 4년 동안 지켜야 했던 중요한 결정들을 내렸는데, 이러한 결정들을 지지하는지 또는 후회하며 다른 것을 선택하는 것이 좋을지에 대해 이 게시물에서 소개하겠습니다.

# AWS

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

## AWS를 Google Cloud 대신 선택한 이유

🟩 추천

처음에는 GCP와 AWS를 둘 다 사용했습니다. 그 시기에는 Google Cloud의 “계정 매니저”가 누구인지 몰랐는데, 동시에 AWS의 계정 매니저와는 정기적인 회의를 가졌습니다. Google은 로봇과 자동화에 의존하는 반면, Amazon은 고객 중심적으로 운영된다는 느낌을 받았습니다. 이런 지원은 새로운 AWS 서비스를 평가할 때 우리를 도와주었습니다. 지원 외에도, AWS는 안정성과 호환되지 않는 API 변경을 최소화하는 데 큰 노력을 기울였습니다.

한 때 Google Cloud가 Kubernetes 클러스터를 선택할 때였습니다, 특히 AWS가 EKS에 투자할지 ECS에 투자할지에 대한 모호함이 있을 때였습니다. 그러나 이제는 AWS 서비스 주변의 추가 Kubernetes 통합(external-dns, external-secrets 등)이 많아져 이제는 더는 그런 문제가 아닙니다.

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

## EKS

🟩 Endorse

페니를 꿀리는 경우가 아니라면 (시간이 무료하다면), EKS 대신 자체 제어 평면을 실행할 이유가 없습니다. AWS에서 대체로 사용하는 주요 이점은 AWS 서비스와의 깊은 통합입니다. 다행히도 Kubernetes는 많은 면에서 따라잡았습니다. 예를 들어, external-dns를 사용하여 Route53과 통합할 수 있습니다.

## EKS 관리 애드온

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

🟧 후회

EKS 관리 애드온을 사용했던 이유는 EKS를 사용하는 "올바른" 방법이라고 생각했기 때문입니다. 그러나 우리는 항상 설치 자체를 사용자 정의해야 하는 상황에 직면했습니다. also 다음과 같은 CPU 요청, 이미지 태그 또는 일부 configmap일 수 있습니다. 그 이후로는 애드온들을 위해 helm 차트를 사용하도록 전환했으며, 기존의 GitOps 파이프라인과 유사하게 잘 맞는 프로모션을 이용하여 일을 진행하고 있습니다.

## RDS

🟩추천

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

데이터는 인프라에서 가장 중요한 부분입니다. 네트워크를 잃으면 다운타임이 발생하지만 데이터를 잃으면 회사를 끝낼 수 있는 사건이 발생합니다. RDS(또는 제어 데이터베이스)를 사용하는 표시 비용은 극도로 가치 있습니다.

## Redis ElastiCache

🟩추천

Redis는 캐시 및 일반 제품으로 훌륭하게 작동했습니다. 빠르며 API는 간단하고 잘 문서화되어 있으며 구현이 실전에서 검증되었습니다. Memcached와 같은 다른 캐시 옵션과 달리 Redis는 캐시 이외에도 유용한 기능이 많아 더 유용합니다. "빠른 데이터 처리"의 스위스 아미 나이프입니다.

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

일부는 클라우드 공급업체에 대한 Redis 상태에 대해 확신이 없지만, AWS 고객이 널리 사용하고 있다는 점에서 AWS가 계속해서 잘 지원해 줄 것이라고 생각해요.

## ECR

🟩인증

원래 quay.io에 호스팅을 했었는데, 안정성 문제가 많이 발생했어요. ECR로 이전한 이후에는 훨씬 안정적으로 운영되었어요. EKS 노드나 개발 서버와의 깊은 권한 통합도 큰 장점이었어요.

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

## AWS VPN

🟩Endorse

Zero Trust VPN 대안은 CloudFlare와 같은 회사에서 제공합니다. 이 제품들이 잘 작동할 것이라 확신하지만 VPN은 설정하고 이해하기가 너무나 쉬워요 ("단순함이 우선"이 제 모토에요). 저희는 VPN 액세스를 관리하기 위해 Okta를 사용하고 있어요. 이것은 훌륭한 경험이었어요.

## AWS 프리미엄 지원

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

🟧아쉬운 점

가격이 무척 비싸요: 다른 엔지니어의 비용과 비슷하거나 더 비쌉니다. AWS에 대해 거의 모르는 경우에는 가치가 있을 것 같아요.

## 테라폼을 위한 컨트롤 타워 계정 공장

🟩 추천

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

AFT를 통합하기 전에는 제어 타워를 사용하는 것이 굉장히 귀찮았습니다. 자동화하기가 매우 어려웠어요. 하지만 AFT를 스택에 통합한 후에는 계정을 빠르게 생성하는 작업이 잘 되었습니다. AFT가 우리에게 더 쉽게 만드는 또 다른 점은 계정의 태그를 표준화하는 것입니다. 예를 들어, 우리의 프로덕션 계정에는 피어링 결정을 내릴 수 있는 태그가 있습니다. 우리에게는 태그가 구성보다 나은 이유가 있습니다. 왜냐하면 "이 계정을 설명하는 속성은 무엇인가"라는 결정이 항상 트리 구조가 아니기 때문입니다.

# 프로세스

## 슬랙 봇을 활용한 사후 분석 프로세스 자동화

🟩 지지

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

모두가 바빠요. 포스트모텀을 작성하라고 사람들을 일일이 알린다는 것은 자신이 "나쁜 사람"인 것 같은 느낌을 줄 수 있어요. 로봇이 그 역할을 대신해 준다면 훌륭한 아이디어죠. SEV 및 포스트모텀 절차를 따르도록 사람들을 살짝 밀어줌으로써 프로세스를 간소화할 수 있어요.

시작할 때 너무 복잡할 필요는 없어요. "메시지가 한 시간 동안 없습니다. 누군가 업데이트를 올려주세요" 또는 "일정 초대장이 없는 날이 하루 지났습니다. 누군가 포스트모텀 미팅을 예약해주세요"와 같이 기본 사항만으로도 많은 도움이 될 거예요.

## PagerDuty의 장애 템플릿 사용하기

🟩 지지하기

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

바퀴를 다시 발명할 필요가 있을까요? PagerDuty는 사고 발생 시 무엇을 해야 하는지에 대한 템플릿을 게시합니다. 우리는 이를 약간 수정했는데, Notion의 유연성이 유용하게 쓰였습니다. 그러나 이것은 매우 좋은 시작점이었습니다.

## 정기적으로 PagerDuty 티켓을 검토하는 과정

🟩 승인

회사에 대한 경보는 다음과 같이 진행됩니다:

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

- 전혀 경고가 없습니다. 경고가 필요합니다.
- 경고가 있습니다. 경고가 너무 많아 무시합니다.
- 우리는 경고를 우선순위로 두었습니다. 이제 중요한 것만 저를 깨웁니다.
- 중요하지 않은 경고는 무시합니다.

우리는 중요하고 중요하지 않은 두 단계의 경고 시스템을 가지고 있습니다. 중요한 경고는 사람들을 깨웁니다. 중요하지 않은 경고는 당직자에게 이메일로 제공됩니다. 문제는 중요하지 않은 경고가 종종 무시된다는 것입니다. 이 문제를 해결하기 위해 우리는 주기적으로 (보통 2주마다) PagerDuty 회의를 진행하여 모든 경고를 검토합니다. 중요한 경고의 경우, 그것이 중요한 상태를 유지해야 하는지 논의합니다. 그런 다음 중요하지 않은 경고를 순환 (보통 각 회의마다 몇 개씩 선정)하고 해당 사항을 해결하기 위해 어떤 조치를 취할 수 있는지 논의합니다 (일반적으로 임계치 조정 또는 자동화 생성).

## 매월 비용 추적 회의

🟩후원

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

저는 이른 시기에 매월 SaaS 비용 (AWS, DataDog 등)을 검토하기 위한 회의를 진행했습니다. 이전에는 이를 재무적인 측면에서만 검토했었지만, "이 비용이 올바른가요?"와 같은 일반적인 질문에 대답하기가 어려웠습니다. 이 회의는 일반적으로 재무 및 엔지니어링팀 모두가 참석하는데, 모든 관련 소프트웨어 청구서를 검토하고 "이 비용이 적당해 보이나요?"라는 직감적인 판단을 내립니다. 높은 비용에 대한 숫자를 자세히 살펴보고 세부 사항을 파헤칩니다.

예를 들어, AWS의 경우 태그로 항목을 그룹화하고 계정으로 분리합니다. 이 두 가지 차원은 일반 서비스 이름(예: EC2, RDS 등)과 결합되어 주요 비용 요소가 어디에 있는지에 대한 좋은 아이디어를 제공합니다. 이 데이터로 수행하는 일부 작업은 스팟 인스턴스 사용 더 깊이 파고들거나 네트워킹 비용에 가장 많은 영향을 미치는 계정을 확인하는 것입니다. AWS에만 머무르지 말고, 회사에 가장 큰 지출을 야기하는 모든 주요 요소로 들어가세요.

## DataDog나 Pager Duty에서 사후 분석 관리

🟥 아쉬움

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

모든 사람들은 사후 조치를 수행해야 합니다. DataDog와 PagerDuty는 각각 사후 조치 작성을 관리하기 위한 통합을 갖추고 있습니다. 우리는 각각을 시도해 봤어요. 불행하게도, 두 툴 모두 사후 조치 프로세스를 사용자 정의하기 어렵게 만들어요. Notion과 같은 강력한 위키 도구를 사용하여 사후 조치를 관리하는 것이 더 나아 보입니다.

## 함수를 서비스(Functions as a Service, FaaS)를 더 활용하지 않은 점

🟥아쉬움

GPU 워크로드를 실행하기에 좋은 FaaS 옵션이 없기 때문에 완전히 FaaS로 전환하지 못했어요. 그러나 많은 CPU 워크로드는 FaaS(람다 등)로 처리될 수 있었어요. 사람들이 제기하는 가장 큰 반론은 비용입니다. "이 EC2 인스턴스 유형이 24/7로 완전히 가동 중인 것은 람다보다 훨씬 저렴하다"라고 말하는 사람들이 많아요. 이는 사실이지만, 비교 자체가 잘못되었다고 생각해요. 아무도 서비스를 100% CPU 사용량으로 가동시키고 그냥 두는 게 아니잖아요. 항상 "100%에 도달하지 않도록. 70%에 이르면 추가로 스케일업"이라고 하는 스케일러에 의해 실행돼요. 그리고 언제 스케일 다운할 지는 항상 모호하게 남아 있어요. 대신 "10%에서 10분 동안 머물렀다면, 스케일 다운"이라는 휴리스틱이에요. 그리고 사람들은 항상 온디맨드 인스턴스로 가정하지만 시장에 항상 그런 인스턴스가 있는 건 아니에요.

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

Lambda의 또 다른 숨겨진 혜택은 비용을 매우 정확하게 추적할 수 있다는 것입니다. Kubernetes에서 서비스를 배포할 때 비용은 노드 당 개체 또는 동일한 노드에서 실행되는 다른 서비스 뒤에 숨을 수 있습니다.

## GitOps

🟩삭제

지금까지 GitOps는 상당히 잘 확장되었으며, 우리는 서비스, 테라폼, 구성 파일 등에서 많은 부분에 사용하고 있습니다. 주된 단점은 파이프라인 중심적인 워크플로우가 "여기는 커밋을 한 상자이고, 여기는 그 상자에서 파이프라인 끝까지 이어지는 화살표"라는 명확한 그림을 제공한다는 것입니다. GitOps를 사용하면 "내가 커밋을 했는데 왜 아직 배포되지 않았는지"와 같은 질문에 답변할 수 있는 도구를 개발하는 데 투자해야 했습니다.

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

아직까지 GitOps의 유연성은 큰 이점이 되었으며 귀사에 강력히 추천합니다.

## 외부 요구 사항보다 팀 효율성 우선

🟩지지

아마도 귀사는 인프라 자체를 판매하는 것이 아니라 다른 제품을 판매하고 있을 것입니다. 이는 팀에 기능을 제공하고 귀사의 업무량 확장을 막는 압력을 줍니다. 비행기가 자신의 마스크를 먼저 착용하라고 요청하는 것과 같이 팀이 효율적인지 확인해야 합니다. 드물게 예외가 발생하더라도, 자동화 또는 문서 작성에 시간을 내는 것을 우선하기로 한 것을 후회한 적이 없습니다.

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

## 데이터베이스를 공유하는 여러 응용 프로그램

🟥아쉬운 결정

대부분의 기술 부채처럼, 우리는 이 결정을 내린 것이 아니라, 이 결정을 내리지 않았습니다. 결국, 누군가 제품이 새로운 작업을 수행하길 원하고 새로운 테이블을 만듭니다. 이것은 좋은 느낌입니다. 왜냐하면 이제 두 테이블 간에 외래 키가 있기 때문입니다. 그러나 모든 것이 누군가에 의해 소유되고 그 누군가가 테이블의 한 행이라면, 전체 스택의 모든 객체 간에 외래 키가 있습니다.

데이터베이스가 모두 사용하기 때문에 아무도 관리하지 않습니다. 스타트업은 DBA(DATABASE ADMINISTRATOR)의 편애를 누릴 여유가 없으며, 아무도 소유하지 않은 모든 것은 결국 인프라가 소유하게 됩니다.

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

공유 데이터베이스의 가장 큰 문제점은 다음과 같습니다:

- 데이터베이스에 개발 및 삭제된 내역이 누적되며, 삭제할 수 있는지 여부가 명확하지 않습니다.
- 성능 문제가 발생할 때 인프라(심층적인 제품 지식 없이)는 데이터베이스를 디버깅하고 어디로 리디렉션할지 파악해야 합니다.
- 데이터베이스 사용자는 데이터베이스에 해를 끼치는 나쁜 코드를 업로드할 수 있습니다. 이러한 문제는 PagerDuty가 인프라 팀에 경보를 보내게 됩니다(데이터베이스 소유자이기 때문에). 한 팀이 다른 팀의 문제로 인해 깨어나야 하는 상황은 그 누구에게나 좋지 않습니다. 어플리케이션 소유 데이터베이스의 경우, 어플리케이션 팀이 처음 대응할 수 있습니다.

그럼에도 불구하고 저는 하나의 데이터베이스를 공유하려는 스택에 반대하지 않습니다. 그러나 위에서 언급된 대가를 인식하고, 그것들을 어떻게 관리할지에 대한 좋은 전략이 있어야 합니다.

# SaaS

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

## Identity platform을 초기에 도입하지 않았던 것에 대한 후회

🟥후회

처음에 Google Workspace을 사용하여 직원을 위한 그룹을 생성하여 권한을 할당하는 방식으로 진행했었습니다. 그러나 이 방식은 충분히 유연하지 않았습니다. 되돌아보면, 우리가 훨씬 이른 시기에 Okta를 선택했으면 하는 바람이 있습니다. Okta는 매우 잘 작동하며 거의 모든 것에 대한 통합이 있고, 많은 규정 준수/보안 측면의 문제를 해결해 주었습니다. 초기에 Identity 솔루션에 주안점을 두고, 그와 통합되는 SaaS 공급 업체만 받아들이는 것이 좋습니다.

## Notion

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

🟩추천

모든 회사는 문서를 저장할 곳이 필요합니다. 노션은 지금까지 사용해본 Wikis, Google Docs, Confluence 등보다 훨씬 쉽고 효율적으로 작동했어요. 페이지 구성을 위한 데이터베이스 개념을 통해 어려운 페이지 조직도 만들 수 있었습니다.

## Slack

🟩추천

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

다행히도 더 이상 HipChat을 사용할 필요가 없어졌어요. Slack은 기본 커뮤니케이션 도구로 훌륭하지만, 스트레스와 소음을 줄이기 위해 다음을 권장해요:

- 커뮤니케이션을 간결하게 정리하기 위해 스레드 사용하기
- 사람들이 빠르게 메시지에 응답하지 않을 수 있다는 기대 전달하기
- 개인 메시지 사용을 자제하고 공개 채널을 장려하기

## JIRA를 사용하여 linear로 이동

🟩지지

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

그거랑은 아예 달라요. 제이라는 너무 무겁다고 생각해요. 인공지능 회사에서 돌리면 그냥 완전히 감각적으로 바뀔 것 같아요. Linear를 사용할 때 종종 "X를 할 수 있을까?"라고 생각한 다음 시도해보니까 가능했어요!

## Terraform Cloud을 사용하지 않은 이유

🟩 후회 없음

처음에 우리의 Terraform을 Terraform Cloud로 마이그레이션하려고 노력했어요. 가장 큰 단점은 비용을 정당화할 수 없었다는 거에요. 그래서 Atlantis로 옮겼는데 충분히 잘 작동했어요. Atlantis가 부족한 부분에서는 CI/CD 파이프라인에 자동화 조각을 조금 작성해 이를 보완했어요.

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

## GitHub Actions을 이용한 CI/CD

🟧 보증적 원칙

우리는 대부분의 기업들과 마찬가지로 GitHub에 코드를 호스팅하고 있어요. 처음에는 CircleCI를 사용했지만 지금은 GitHub Actions를 CI/CD에 활용하고 있어요. 워크플로우에 사용할 수 있는 액션들의 마켓플레이스가 다양하고 문법이 쉽게 읽히는 것이 장점이에요. GitHub Actions의 주된 단점은 자체 호스팅된 워크플로우에 대한 지원이 매우 제한되어 있다는 점이에요. 우리는 EKS를 사용하여 EKS에 호스팅된 자체 호스팅된 러너들에 대해 actions-runner-controller를 사용하고 있지만 통합은 종종 버그가 있어요(하지만 우회할 수 없는 문제는 아니에요). 앞으로 GitHub이 Kubernetes 자체 호스팅을 더 진지하게 다루길 바라요.

## Datadog

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

## 값싼 호스팅 서비스를 찾고 계십니다? 카카오 클라우드는 한국 최고의 클라우드 서비스 제공업체 중 하나입니다. 친절한 가격과 뛰어난 성능을 원하신다면 카카오 클라우드를 추천드립니다. 더 자세한 정보는 [카카오 클라우드 웹사이트](https://cloud.kakao.com)를 방문해주세요.

언제든지 궁금하신 사항이 있으시면 언제든지 문의해주세요.

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

Pagerduty는 멋진 제품이며 가격도 합리적입니다. 우리는 선택한 것을 후회한 적이 없습니다.

# 소프트웨어

## Diff를 통한 스키마 마이그레이션

🟧추천-ish

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

스키마 관리는 어떻게 하든 어려운 작업이며, 대부분 무서운 이유로 그렇습니다. 데이터는 중요하며 잘못된 스키마 이동은 데이터를 삭제할 수 있습니다. 이 어려운 문제를 해결하는 무서운 방법 중 하나로 git에 전체 스키마를 체크인하고 그런 다음 데이터베이스를 스키마에 동기화하기 위한 SQL을 생성하는 도구를 사용하는 아이디어로 정말 만족했습니다.

## 개발 서버용 Ubuntu

🟩추천

원래는 쿠버네티스 노드가 실행되는 기본 OS를 개발 서버로 사용해 개발 환경을 운영 환경과 가깝게 만들겠다고 시도해봤지만, 되돌아보니 이러한 노력은 가치가 없다고 생각했습니다. 저희가 개발 서버에 Ubuntu를 계속 사용하고 있어서 기뻐합니다. 이 운영 체제는 잘 지원되며 필요한 대부분의 패키지가 제공됩니다.

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

## AppSmith

🟩 Endorse

내부 엔지니어의 일부 프로세스를 자동화해야 하는 경우가 많습니다: 재시작/승격/진단 등. 이러한 문제를 해결하기 위해 API를 만드는 것은 쉽지만, 누군가의 CLI/OS/의존성 등을 디버깅하는 것은 약간 귀찮습니다. 엔지니어가 우리 스크립트와 상호 작용하기 위한 간단한 UI를 만들 수 있다는 것은 매우 유용합니다.

우리는 AppSmith를 자체 호스팅하고 있습니다. 꽤 잘 작동합니다. 물론 변경하고 싶은 부분들이 있지만, "무료" 가격 대비 충분히 만족스럽습니다. 처음에 retool과의 심층적인 통합을 탐색했지만, 그 당시 몇 가지 통합만 있었기 때문에 그 가격을 정당화할 수 없었습니다.

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

## 헬름

💡 좋아요

헬름 v2는 나쁜 평판을 얻었지만(v3도 그 이유가 있음), 헬름 v3는 충분히 잘 작동합니다. 여전히 CRD를 배포하는 데 문제가 있고, 개발자들에게 왜 그들의 헬름 차트가 올바르게 배포되지 않았는지에 대해 교육하는 문제가 있습니다. 그러나 전반적으로, 헬름은 버전화된 Kubernetes 객체를 패키지로 만들고 배포하기에 충분히 잘 작동하며, Go 템플릿 언어는 디버그하기 어렵지만 강력합니다.

## ECR(oci)에 있는 헬름 차트

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

🟩인증

원래 우리의 헬름 차트는 S3 안에 호스팅되고 플러그인을 사용하여 다운로드되었습니다. 주요 단점은 사용자 정의 헬름 플러그인을 설치하고 수동으로 라이프사이클을 관리해야 했습니다. 그러나 최근에 OCI 저장소에 있는 헬름 차트로 전환했고, 이러한 설정으로 어떤 문제도 발생하지 않았습니다.

## bazel

🟧확실치 않음

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

공평하게 말해서, 많은 스마트한 사람들이 bazel을 좋아합니다. 그래서 그건 나쁜 선택은 아닌 것 같아요.

Go 서비스를 배포할 때 bazel을 사용하는 건 저에게 개인적으로 지나칠 것 같아요. 만약 지난 회사에서 bazel을 사용했고 그리움을 느낀다면 좋은 선택이죠. 하지만 그 외에는 다수가 사용하는 GitHub Actions과 비교해 몇몇 엔지니어만 깊게 파볼 수 있는 빌드 시스템이에요.

## 초기에 OpenTelemetry 사용하지 않은 것

🟥후회함

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

우리는 DataDog의 API를 사용하여 지표를 직접 전송하기 시작했습니다. 이렇게 하면 그들을 제거하기가 매우 어렵습니다.

4년 전에는 오픈 텔레미트가 그리 성숙하지 않았지만, 지금은 훨씬 나아졌습니다. 지표 텔레미트는 아직 조금 미성숙한 것 같지만, 추적은 훌륭합니다. 어떤 회사든 처음부터 사용하는 것을 추천합니다.

## dependabot 대신 renovatebot 선택

🟩추천

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

솔직히 말해서, "의존성을 최신 상태로 유지해야 한다"라는 사항에 대해 먼저 생각했으면 좋았을 텐데요. 이 부분을 너무 오래 방치하면 버전이 너무 오래된 상태가 되어 업그레이드 과정이 오래 걸리고 결국 버그가 발생하게 됩니다. Renovatebot은 유연성을 가지고 필요에 맞게 사용자 정의할 수 있어 잘 작동했습니다. 가장 큰 단점은 매우 복잡한 설정과 디버깅이어야 한다는 것입니다. 아마도 나쁜 옵션들 가운데에서 가장 나은 선택인 것 같아요.

## 쿠버네티스

🟩 지지

장기적으로 실행되는 서비스를 호스팅할 수 있는 것이 필요합니다. 쿠버네티스는 인기 있는 선택지이며 저희에게 잘 작동했습니다. 쿠버네티스 커뮤니티는 AWS 서비스(로드 밸런서, DNS 등)를 쿠버네티스 생태계에 효과적으로 통합한 좋은 일을 해왔습니다. 그러나 유연한 시스템의 가장 큰 단점은 다양한 방법으로 사용할 수 있기 때문에, 사용법이 많을수록 잘못된 사용법도 많다는 점입니다.

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

## 우리 자체 IP 구매

🟩지지

외부 파트너와 작업을 하는 경우, 그들을 위해 자주 IP 화이트리스트를 게시해야 할 것입니다. 안타깝게도, 나중에 자체 IP가 필요한 시스템이 더 많이 개발될 수 있습니다. 자체 IP 블록을 구매하는 것은 외부 파트너에게 더 큰 CIDR 블록을 화이트리스트로 제공하여 이를 피하는 훌륭한 방법입니다.

## k8s GitOps를 위해 Flux 선택하기

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

🟩 후회 없는 선택

쿠버네티스를 위한 초기 GitOps 선택 중 ArgoCD와 Flux 중에서 선택해야 했는데, 저는 그 당시에는 Flux(v1)를 선택했습니다. 아주 잘 작동했어요. 현재는 Flux 2를 사용 중이에요. 유일한 단점은 배포 상태를 이해하는 데 도움이 되는 우리만의 도구를 만들어야 했다는 점입니다.

ArgoCD에 대해 많은 좋은 이야기를 들었기 때문에, 만약 여러분이 ArgoCD를 선택했다면 안심할 수 있을 거예요.

## 노드 관리를 위한 Karpenter

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

🟩추천

EKS를 사용 중이라면 (그리고 완전히 Fargate를 사용하지 않는다면), Karpenter를 사용해야 합니다. 100% 확실해요. 다른 오토스케일러를 사용해봤는데 기본 Kubernetes 오토스케일러와 SpotInst를 포함해요. 이 중에서 Karpenter가 가장 신뢰할 수 있고 가장 비용 효율적입니다.

## SealedSecrets를 사용하여 k8s 비밀을 관리하기

🟥아쉽습니다

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

제 원래 생각은 시크릿 관리를 GitOps 스타일로 전환하는 것이었습니다. sealed-secrets를 사용하는 두 가지 주요 단점은 다음과 같습니다:

- 인프라에 대한 지식이 부족한 개발자들에게 비밀을 생성/업데이트하기가 더 복잡했습니다.
- 우리는 AWS가 비밀을 로테이션하는 데 사용하는 기존의 자동화를 모두 잃었습니다.

## k8s 시크릿 관리를 위해 ExternalSecrets 사용

🟩 추천

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

ExternalSecrets는 AWS - Kubernetes 시크릿을 동기화하는 데 매우 잘 작동했습니다. 개발자가 이해하기 쉬운 간단한 프로세스이며, AWS 내에서 시크릿을 쉽게 생성/업데이트할 수있게 하여 terraform의 장점을 활용할 수 있습니다. 또한 사용자가 시크릿을 생성/업데이트하는 데 사용할 수있는 UI를 제공합니다.

## ExternalDNS를 사용하여 DNS 관리하기

🟩추천

ExternalDNS는 훌륭한 제품입니다. Kubernetes - Route53 DNS 항목을 동기화하고 지난 4년 동안 매우 적은 문제를 일으켰습니다.

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

## SSL 인증서 관리를 위해 cert-manager 사용하기

🟩좋아함

환경 설정이 매우 직관적이고 문제 없이 잘 작동합니다. 쿠버네티스를 위한 Let's Encrypt 인증서를 생성하는 데 강력히 추천합니다. 유일한 단점은 때로는 고대의 (SaaS 문제, 그런 게 있죠?) 기술 스택을 사용하는 고객들이 Let's Encrypt를 신뢰하지 않아서 해당 고객들을 위해 유료 인증서를 구해야 할 수 있습니다.

## EKS용 Bottlerocket

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

🟥 후회

우리의 EKS 클러스터는 이전에 Bottlerocket에서 실행되었습니다. 주요 단점은 네트워킹 CSI 문제에 자주 직면했으며, Bottlerocket 이미지를 디버깅하는 것이 표준 EKS AMI를 디버깅하는 것보다 훨씬 더 어려웠습니다. 노드에 EKS 최적화 AMI를 사용하면 문제가 없고, 이상한 네트워킹 문제가 발생했을 때 노드 자체를 디버깅하기 위한 통로가 여전히 있습니다.

## Cloudformation 대신 Terraform 선택

🟩 지지

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

인프라스트럭처를 코드로 관리하는 것은 어떤 회사에게든 필수적입니다. AWS 환경에서 주로 사용하는 것은 CloudFormation과 Terraform입니다. 저는 두 가지 모두 사용해봤는데 Terraform을 선택한 것을 후회하지 않았어요. 다른 SaaS 공급업체(예: Pagerduty)와 쉽게 확장할 수 있었고, CloudFormation보다 읽기 쉬운 구문을 가지고 있어서 저희에게는 방해 없이 진행되었어요.

## 더 코드스럽지 않은 IaC 솔루션(Pulumi, CDK 등)을 사용하지 않을 때

🟩후회 없음

Terraform과 CloudFormation은 인프라스트럭처를 설명하는 데이터 파일(HCL 및 YAML/JSON)이지만, Pulumi나 CDK와 같은 솔루션은 코드로 동일한 작업을 수행할 수 있게 해줍니다. 코드는 물론 강력하지만, Terraform의 HCL이 제한적인 성격이라는 것이 복잡성을 줄일 수 있는 장점이라고 생각했어요. Terraform을 통해 복잡한 작업을 수행하는 것이 불가능한 것은 아니지만, 그럴 때 더 명확하게 알 수 있었어요.

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

일부 솔루션 중 Pulumi와 같은 것들은 많은 년전에 개발되었는데, Terraform은 현재 많은 기능이 부족했던 시기에 만들어졌습니다. 최신 버전의 Terraform에는 우리가 복잡성을 줄일 수 있는 많은 기능이 통합되어 있습니다. 대신, 저희는 우리가 추상화하고 싶은 부분들을 위해 Terraform 코드의 기본 뼈대를 생성하는 중간 방법을 사용합니다.

## 네트워크 망을 사용하지 않기(istio/linkerd/등)

🟩후회 없음

네트워크 망은 정말 멋지고 많은 똑똑한 사람들이 그것을 지지하는 경향이 있기 때문에, 나는 그것들이 괜찮은 아이디어라고 확신합니다. 불행히도, 회사들이 일반적으로 복잡성을 과소평가한다고 생각합니다. 제 일반적인 인프라 조언은 "덜하는 게 더 낫다"입니다.

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

## EKS 인그레스용 Nginx 로드 밸런서

🟩 후회 없음

Nginx는 오래되었고 안정적이며 전투 검증을 거친 상태입니다.

## 회사 스크립트용 홈브류

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

🟩지지

귀사는 엔지니어들이 사용할 스크립트와 이진 파일을 배포할 방법이 필요할 것입니다. Homebrew는 리눅스와 맥 사용자 모두에게 스크립트와 이진 파일을 배포하는 방법으로 충분히 잘 작동했습니다.

## 서비스 선택

🟩지지

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

Go는 새로운 엔지니어들이 배우기 쉽고 전반적으로 좋은 선택지입니다. 대부분 네트워크 IO에 바인딩된 비 GPU 서비스의 경우, Go가 기본 언어로 적합합니다.
