---
title: "Kubernetes 보안 마스터하기 - 내 Admission Controllers 여행"
description: ""
coverImage: "/assets/img/2024-06-19-MasteringKubernetesSecurityMyJourneyWithAdmissionControllers_0.png"
date: 2024-06-19 13:06
ogImage:
  url: /assets/img/2024-06-19-MasteringKubernetesSecurityMyJourneyWithAdmissionControllers_0.png
tag: Tech
originalTitle: "Mastering Kubernetes Security — My Journey With Admission Controllers"
link: "https://medium.com/itnext/mastering-kubernetes-security-my-journey-with-admission-controllers-ca6f163e8c2a"
---

Kubernetes는 Admission Controllers라는 확장 포인트를 포함하고 있습니다. 이들은 Kubernetes 클러스터의 문지기 역할을 하며 들어오고 나가는 모든 자원 요청을 감독합니다. 단순한 감시뿐만 아니라 이들 컨트롤러는 정교한 필터 역할을 합니다.

그들은 조직의 정책을 집행하고 규정 준수를 보장하며 요청을 미리 정의된 표준에 맞게 수정할 수 있습니다.

이 기사는 Kubernetes 보안 시리즈의 네 번째로 Admission Controllers에 중점을 둡니다.

인증된 Kubernetes 보안 (CKS) 자격증 획득을 향해 진로를 나아가면서, CKS 커리큘럼의 "마이크로서비스 취약점 최소화" 섹션에서 Admission Controllers의 중요성을 탐구하고 있습니다.

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

# 어드미션 컨트롤러란 무엇인가요?

어드미션 컨트롤러는 쿠버네티스 생태계에서 중요한 역할을 하는데, 쿠버네티스 API 서버의 출입구 역할을 합니다.

어드미션 컨트롤러는 요청이 인증되고 권한이 부여된 후에 호출되며, 해당 객체가 쿠버네티스 클러스터에 지속되기 전에 작동합니다. 그들의 주요 기능은 요청을 가로채고 처리하여 클러스터의 운영 무결성과 보안을 유지하는 것입니다.

쿠버네티스에서 어드미션 컨트롤러는 기본적으로 클러스터의 사용 방법을 지배하고 강제화하는 플러그인입니다. 이들은 쿠버네티스 API 서버로의 요청(예: 리소스 생성, 수정 또는 삭제)을 미리 정의된 규칙과 정책에 맞춰평가합니다.

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

## 두 가지 유형의 입장 컨트롤러: 변형 및 확인

입장 컨트롤러는 변형과 확인 두 가지 주요 유형으로 분류될 수 있습니다.

변형 입장 컨트롤러는 그들이 수락하는 객체들을 수정할 수 있습니다. 이는 객체가 특정 규칙을 준수하거나 추가 메타데이터로 객체를 향상시킬 때 Kubernetes API 서버에 의해 처리되기 전에 요청 내용을 변경할 수 있음을 의미합니다.

예를 들어, 변형 입장 컨트롤러는 모니터링 에이전트가 모든 작업 부하에 존재함을 보장하기 위해 사이드카 컨테이너를 자동으로 주입할 수 있습니다.

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

한편, 유효성 검사 입합기는 객체를 수정하지 않습니다. 대신 요청이 모든 필요한 기준을 충족하는지 확인합니다. 예를 들어, 배포가 이미지의 최신 버전을 사용하는지 보장할 수 있습니다.

만약 요청이 이러한 검사를 통과하지 못하면, 유효성 검사 입합기는 작업을 거부하고 객체가 생성, 수정 또는 삭제되지 않습니다.

이 프로세스는 조직 정책을 강제하는 데 중요하며 클러스터에 적용되는 규정 준수 및 안전한 구성만이 보장됩니다.

특정 요구 사항에 맞는 사용자 정의 유효성 검사 입합기를 만들 수 있지만, Kubernetes 클러스터에는 이미 내장된 유효성 검사 입합기 스위트가 있습니다.

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

아래 섹션에서는 미리 구성된 컨트롤러의 역할과 기능을 탐색할 것입니다.

## Kubernetes 내장 Admission 컨트롤러

기본 Kubernetes 설치에는 자체 내장 Admission 컨트롤러 모음이 자동으로 포함되어 활성화됩니다. 대부분의 관리자와 사용자에게는 이러한 컨트롤러를 만지는 일이 거의 없고, 기본적으로 활성화된 것을 비활성화할 필요도 거의 없을 것입니다.

그러나 클러스터의 동작을 사용자 정의하거나 활성화된 Admission 컨트롤러를 확인해야 하는 경우, Kubernetes는 제어 플레인에서 직접 수행할 수 있는 간단한 방법을 제공합니다.

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

현재 활성화된 입장 컨트롤러 목록을 확인하려면 제어 플레인 터미널에서 다음 명령을 실행하세요:

```js
kube-apiserver -h | grep enable-admission-plugins
```

기본적으로 설치된 주요 입장 컨트롤러와 쿠버네티스 생태계에 대한 기여에 대해 간략히 살펴보겠습니다. 입장 컨트롤러는 쿠버네티스 버전에 따라 다양하며, 이것들은 v1.29에서 내장된 것들입니다.

- NamespaceLifecycle — 삭제 중인 네임스페이스에 대한 요청을 차단하고 예약된 이름과 일치하는 경우 새로운 네임스페이스를 생성할 수 없도록 보장합니다. 네임스페이스별 리소스의 무결성을 유지하는 데 중요합니다.
- LimitRanger — LimitRange 개체로 지정된 제약 조건을 네임스페이스 내의 Pod, Container 및 PersistentVolumeClaim 리소스에 적용합니다. 관리자가 정의한 제한을 초과하지 않도록하여 자원 사용을 효율적으로 할당하고 낭용을 방지합니다.
- ServiceAccount — 명시적 ServiceAccount가 지정되지 않은 Pod에 자동으로 기본 서비스 계정을 첨부합니다. 권한을 관리하고 적절한 수준의 액세스로 Pod가 Kubernetes API에 안전하게 액세스할 수 있도록 중요합니다.
- PersistentVolumeClaimResize — 기존 PersistentVolumeClaim (PVC)의 크기를 다시 조정할 수 있게 합니다. 이 기능을 통해 응용 프로그램 요구 사항이 변경될 때 저장소 리소스를 조정하는 작업이 간편해집니다.
- PodSecurity — 사전 정의된 Pod 보안 설정을 강화하는 Pod Security Standards를 적용합니다. 지정된 보안 요구 사항을 충족하지 않는 Pod의 생성을 방지하여 클러스터 내 보안 취약성 위험을 크게 줄입니다.

그 외에도 다음과 같은 입장 컨트롤러가 있습니다:

- MutatingAdmissionWebhook 및 ValidatingAdmissionWebhook — 외부 서비스에 의해 강제된 사용자 정의 입장 정책을 허용하는 웹훅입니다. 동적 입장 제어에 대한 섹션에서 논의할 예정입니다.
- ResourceQuota — 네임스페이스 내 리소스 할당량 제한을 적용하여 CPU, 메모리, 저장소 및 Pod, 서비스 등의 개수를 다룹니다. 클러스터 리소스의 공정한 사용을 장려하고 모든 서비스 및 사용자 간에 공정한 사용을 촉진합니다.
- Priority — PriorityClass 이름을 기반으로 Pod의 스케줄링 우선 순위를 결정합니다. 특정 애플리케이션의 중요성을 기준으로 Pod 스케줄링의 우선 순위를 설정하여 중요한 애플리케이션이 최적으로 실행될 수 있도록 보장합니다.
- RuntimeClass — 컨테이너 런타임 구성의 선택을 지원합니다. Pod에 대해 서로 다른 컨테이너 런타임을 허용하여 런타임별 기능 및 최적화를 간소화합니다.
- DefaultStorageClass, DefaultIngressClass 및 DefaultTolerationSeconds — 명시적으로 지정되지 않은 경우 Pod에 대해 저장소 클래스, 인그레스 클래스 및 허용 시간의 기본값을 자동으로 설정합니다. 구성을 간소화하고 기본 정책이 클러스터 전체에 일관되게 적용되도록 보장합니다.

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

# 쿠버네티스의 동적 입장 제어: 클러스터 보안 및 관리 향상

동적 입장 제어는 사용자 정의 정책을 강요하고 MutatingAdmissionWebhook 및 ValidatingAdmissionWebhook 두 가지 중요한 구성 요소를 통해 클러스터 관리를 간소화하는 강력한 메커니즘입니다.

## 동적 입장 제어 컨트롤러 이해

동적 입장 제어 컨트롤러에는 MutatingAdmissionWebhook 및 ValidatingAdmissionWebhook 두 가지 종류가 있습니다. 이 컨트롤러는 외부 서비스에서 정의된 사용자 정책에 따라 쿠버네티스 API 서버에 대한 요청을 허용하거나 거부하는 게이트키퍼 역할을 합니다.

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

## MutatingAdmissionWebhook: 변환자

MutatingAdmissionWebhook는 변환하는 어드미션 컨트롤러이며, 이전 섹션에서 본 것처럼 쿠버네티스 API 서버가 처리하기 전에 들어오는 요청을 수정하거나 변환하는 데 사용됩니다. 이 능력은 다음과 같은 시나리오에 유용합니다:

- 사이드카 컨테이너 주입: 팟에 보조 컨테이너를 자동으로 추가하여 로깅, 모니터링 또는 네트워크 트래픽 제어에 사용할 수 있습니다.
- 구성 변경: 조직 표준을 준수하도록 포드 사양을 수정하여 레이블이나 환경 변수를 추가하는 것과 같은 작업을 수행할 수 있습니다.

## ValidatingAdmissionWebhook: 게이트키퍼

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

한편, ValidatingAdmissionWebhook은 미리 정의된 규칙에 대한 요청을 검증하여 계속 진행하기 전에 확인하는 데 중점을 둔 유효성 검사 입장 컨트롤러입니다. 다음과 같은 중요한 역할을 합니다:

- 사용자 지정 리소스 할당량 강제 적용: 리소스 생성이 조직 정책에서 설정한 한도를 초과하지 않도록 보장합니다.
- 사용자 지정 보안 정책 확인: 구성이 보안 표준을 준수하는지 확인하여 컨테이너가 루트 사용자가 아닌 사용자로 실행되도록 합니다.

# 웹훅 Admission 컨트롤러 구현

사용자 정의 Admission 컨트롤러를 통합하거나 개발하는 두 가지 방법이 있습니다. 전통적인 방법은 Go와 같은 프로그래밍 언어를 사용하여 사용자 정의 Admission 컨트롤러를 생성하는 것인데, 이는 강력한 제어를 제공하지만 심층적인 Kubernetes 생태계 지식이 필요합니다.

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

대신, 쿠버네티스는 MutatingAdmissionWebhook 및 ValidatingAdmissionWebhook이라는 웹훅 어드미션 컨트롤러 덕분에 더 접근성이 좋은 옵션을 제공합니다. 이를 통해 개발자는 쿠버네티스 API 서버가 웹훅을 통해 통신할 수 있는 REST API 서비스를 구현하여 사용자 정의 어드미션 로직을 도입할 수 있습니다.

## 배포 유연성

웹훅을 호스팅하는 REST API 서비스는 쿠버네티스 클러스터 내부나 외부 중 어디에나 배포할 수 있습니다. 이러한 유연성은 다양한 배포 시나리오와 아키텍처 디자인을 지원합니다.

## 언어 중립성

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

웹훅 기반 컨트롤러의 중요한 장점 중 하나는 언어에 구애받지 않는다는 점입니다. 개발자들은 Node.js, Java, C# 또는 기타 선호하는 프로그래밍 언어로 REST API 서비스를 구현할 수 있어 팀의 전문지식에 기반한 더 넓은 채택과 사용자 정의를 유도할 수 있습니다.

## 운영 메커니즘

두 종류의 웹훅은 JSON 형식의 직렬화된 AdmissionReview 객체와 상호작용하여 작동합니다. ValidatingAdmissionWebhook은 객체를 사용하여 허용/거부 결정을 내릴 수 있어야 합니다.

동시에 MutatingAdmissionWebhook은 요청 페이로드를 변경하여 Kubernetes 리소스의 동작을 동적으로 강화하거나 수정하는 다재다능한 메커니즘을 제공할 수 있습니다.

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

GitHub에서 웹훅 구현을 보여주는 다양한 예제를 찾을 수 있어요. 예를 들면, 이겪은 Python을 사용합니다. [https://github.com/garethr/kubernetes-webhook-examples](https://github.com/garethr/kubernetes-webhook-examples)

# Admission Controllers와 CKS 시험 준비

CKS (Certified Kubernetes Security Specialist) 자격증 시험을 통과하기 위해 나의 여정 중, 마이크로서비스 취약점을 최소화하는 막대 아래에 있는 Admission Controllers 섹션에 도달했어요.

아직 시험을 보지는 않았지만, 커스텀 Admission Controller를 제로부터 만들거나 검증을 위한 웹훅의 코딩 세부 사항에 대해 물어보지는 않을 거라고 생각해요.

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

그 대신, 나는 더 가능성이 높은 도전 과제에 대비하고 있어요: 어떤 입학 컨트롤러가 켜져 있는지 끄는지 알아내고 이러한 설정을 어떻게 조정하는지 알아내야 해요. 이것은 CKS가 추구하는 것과 더 일치하는데, 실제 Kubernetes 보안에서 요구되는 실용적이고 실무적인 기술에 부합합니다.

# 마무리: Kubernetes의 문지기들

CKS 시험 준비를 시작하기 전에, 입학 컨트롤러가 존재하는 것조차 몰랐어요. 지금은 몇 개의 입학 컨트롤러를 사용하여 배포에 레이블과 같은 기본적인 것들을 추가했어요.

나의 공부를 통해, 다양한 작업을 위해 입학 컨트롤러를 활용하는 많은 도구와 구현을 발견했어요. 예를 들어, 특정 이미지의 최신 버전만 사용되도록 하는 것과 같이 컨테이너 이미지 스캔을 입학 컨트롤러와 통합하는 것이 가능해요.

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

더 나아가 몇 가지 도구는 입학 컨트롤러의 기초를 기반으로하여, 특정 보안 정책을 구현하는 과정을 간소화하는 계층을 추가합니다. 사용자 정의 정책 설명 언어를 사용하여 구체적인 보안 정책을 구현하는 과정을 단순화하는 도구들이 있습니다.

그 중 하나가 OPA 또는 Open Policy Agent입니다. 이는 CKS 시험의 요구 사항 중 하나인 것으로 알고 있습니다. 다음 글에서 OPA에 대해 자세히 다룰 계획입니다.

열심히 공부하세요!
