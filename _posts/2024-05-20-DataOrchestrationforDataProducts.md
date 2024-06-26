---
title: "데이터 제품을 위한 데이터 조작"
description: ""
coverImage: "/assets/img/2024-05-20-DataOrchestrationforDataProducts_0.png"
date: 2024-05-20 16:25
ogImage:
  url: /assets/img/2024-05-20-DataOrchestrationforDataProducts_0.png
tag: Tech
originalTitle: "Data Orchestration for Data Products"
link: "https://medium.com/@hugolu87/data-orchestration-for-data-products-7a5d6e4bda9f"
---

이 문서는 Modern Data Stack 101에 처음 등장했습니다. 만일 Medium 회원이 아니라면, 무료로 여기에서 읽을 수 있어요 💸

# 제 소개

저는 후고 루입니다. 런던에서 M&A로 일한 후 JUUL에 합류하여 데이터 엔지니어링에 빠져들게 되었습니다. 런던을 기반으로 하는 스케일업 기업 Codat의 데이터 기능을 이끌었습니다. 지금은 Orchestra의 CEO입니다. Orchestra는 데이터 팀이 신뢰성 있고 효율적으로 데이터를 프로덕션 환경으로 릴리즈할 수 있도록 도와주는 데이터 릴리즈 파이프라인 도구입니다. 🚀

⭐️ 또한 우리의 Substack와 내부 블로그도 확인해 보세요 ⭐️

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

# 소개

현재 데이터 팀들은 오르막길을 오르는 중입니다. 한편으로, 데이터 제품에 대한 수요는 이전보다 커졌습니다. AI가 데이터 기반의 기능, 제품 및 능력에 대한 수요를 높였고, CEO들의 욕망 또한 증가했습니다. 이러한 요구는 데이터 파이프라인 및 데이터 및 데이터 수집의 효율적 관리에 의해 가능케 되었습니다.

다른 한편으로, 데이터 팀의 예산은 삭감되고 인력도 줄어들고 있습니다. 이로 인해 데이터를 조직하는 능력이 저하되었고, 결과적으로 조직이 활용할 수 없는 많은 다양한 데이터 소스(구조화된 및 비구조화된 데이터)가 계속되고 있습니다.

성장과 성공을 위한 가장 큰 촉매제 중 하나는 수동 데이터 사용의 고통을 없애고 데이터 조종, 데이터 관측, 계보, 메타데이터 수집 및 경보를 통합하는 통합 제어 평면을 가지고 있는 것입니다. 이 글에서는 데이터 조종 프로세스가 데이터 및 AI 제품 속도를 가속화하는 데 어떤 역할을 하는지와 채택할 수 있는 방법을 살펴보겠습니다.

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

# 오픈 소스 워크플로 오케스트레이션 도구 대 전체 제어 플랜

Airflow 및 Prefect와 같은 오픈 소스 워크플로 오케스트레이션 도구는 데이터 워크플로 관리 및 자동화에 강력한 기능을 제공하지만, 전체 제어 플랜과 비교할 때 접근 방식과 기능이 다릅니다.

Airflow는 에어비앤비에서 개발한 오픈 소스 도구로, 워크플로 스케줄링, 모니터링 및 관리에 중점을 둡니다. Airflow는 방향성 비순환 그래프(DAG)를 사용하여 워크플로를 정의하고 다양한 외부 시스템과 통합을 지원합니다. Airflow의 장점은 유연성과 확장성에 있어, 사용자가 복잡한 워크플로를 생성하고 대량의 데이터를 처리할 수 있도록 합니다.

반면 Prefect는 사용 편의성, 신뢰성 및 데이터 버전 관리를 강조하는 또 다른 오픈 소스 워크플로 오케스트레이션 도구입니다. Prefect는 워크플로를 정의하는 데 더 직관적인 인터페이스를 제공하며 자동 재시도 논리, 작업 의존성 및 워크플로를 모니터링하고 관리하는 중앙 대시보드와 같은 기능을 제공합니다.

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

오픈 소스 워크플로 오케스트레이션 도구인 Airflow와 Prefect를 All-in-One 제어 플레인인 Orchestra, Apache NiFi, DataOS 또는 AWS Step Functions과 같은 플랫폼 또는 프로프리터리 솔루션과 비교할 때, All-in-One 제어 플레인은 종종 더 간편한 사용자 경험을 제공하고 학습 커브가 낮다는 큰 차이점이 있습니다. 그들은 사용자들이 기술적 지식이 적은 사용자도 접근할 수 있게끔 그래픽 표현, 드래그 앤 드롭 기능, 일반적인 워크플로를 위한 미리 구축된 템플릿과 같은 선호하는 형태의 표준 인터페이스를 제공할 수 있습니다.

그러나 제어 플레인의 진정한 힘은 데이터 생태계의 모든 엔티티와 상호 작용할 수 있는 능력에 있습니다. 이로 인해 불필요한 통합 오버헤드를 완전히 줄일 수 있습니다. 더 흥미로운 점은 중앙 제어 플레인이 글로벌 메타데이터 및 거버넌스 프로토콜에 접근할 수 있어 데이터 주변의 완전한 컨텍스트와 안전하게 작동할 수 있는 오케스트레이션을 제공할 수 있다는 것입니다.

# OSS 오케스트레이션 시스템이 안티 패턴을 장려

기존 OSS 워크플로는 데이터 아키텍처에 대한 통합적인 접근을 장려합니다. 이는 "모던 데이터 스택"의 모든 구성 요소를 처리할 수 있는 단일 저장소와 단일 애플리케이션이 있다는 것을 의미합니다.

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

많은 사람들이 이 아키텍처를 간단히 갖고 있지 않습니다. 데이터 서비스를 구축하는 것은 어렵습니다. 데이터 파이프라인을 구축하는 것은 종종 파이프라인의 다른 부분에서 특화된 서비스가 필요합니다. 사실, 서로 다른 팀이 있는 것이 필요하게 만드는 경우가 많습니다.

예를 들어, 데이터 엔지니어링 팀은 Kafka나 다른 이벤트 기반 스트리밍 응용 프로그램을 관리할 책임이 있을 수 있습니다. 분석 팀은 dbt 저장소를 관리할 수도 있습니다. 이들은 분리되어야 합니다.

따라서 OSS Workflow Orchestration 도구로 현대 데이터 스택을 활용하는 것은 어느 정도 반대 패턴이며 추가적이고 비싼 감시 플랫폼이 필요합니다. "Quis custodiet ipsos custodes?" 또는 "수호자를 지킬 자가 누구인가?" 라는 문구가 떠오릅니다.

![이미지](/assets/img/2024-05-20-DataOrchestrationforDataProducts_0.png)

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

이미지 제공: 작성자

# 효율적인 마이크로서비스 아키텍처 활성화 방법

데이터 제품 속도에 미치는 통합 데이터 조작의 영향

데이터 조작 프로세스 자동화를 위한 통합 제어 평면을 갖는 것은 여러 가지 이점을 제공합니다.

## 보일러플레이트 작업 제거

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

기존 인프라를 사용하여 모든 중요한 계산을 처리하고 데이터 Orchestration 도구가 기존 프로세스를 조정하는 데 기여함으로써 엔지니어는 새로운 기술(Airflow 등)을 배우거나 인프라를 배포(Kubernetes 등)하거나 복잡한 UI에 대처할 필요가 없습니다.

엔지니어와 데이터 팀은 데이터 수집, 데이터 정리, 분석 그리고 기계 학습에 집중하며 조정이 나머지 부분을 처리하도록 의지할 수 있습니다.

## 비용과 메타데이터에 대해 걱정하지 마세요

통합되지 않은 데이터 또는 Workflow Orchestration 도구를 사용하면 소중한 메타데이터가 원본 시스템 내에서 통합되지 않은 상태로 남아 있고 데이터 수명 주기 정책으로 인해 심지어 사라질 수 있습니다.

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

통합 조작 및 감시는이 데이터가 실시간으로 효율적으로 수집되어 데이터 팀에서 사용할 수 있도록합니다. 이를 통해 이집트된 데이터 소스 간의 메타데이터 관리가 쉬워집니다.

## 비즈니스 가치 계산

통합 조작 플랫폼을 사용하면 데이터 엔지니어가 데이터 및 AI 제품의 비즈니스 가치를 정량화 할 수 있습니다. 예를 들어, 쿼리당 비용 데이터 및 AWS 컴퓨팅 비용을 가져오는 동시에 대시보드 및 머신러닝 모델 사용 통계를 볼 수 있습니다.

이 비즈니스 중요한 운영 데이터를 데이터 제품 수준에서 가지면 데이터 팀은 좀 더 효율적으로 우선순위를 정할뿐만 아니라 기업에 그들의 가치와 가치를 보여줄 수있는 창문을 마침내 가질 수 있습니다.

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

통합 데이터 조율 및 데이터 가시성 플랫폼은 모든 작업을 청소하는 것, 유용한 메타데이터를 수집하고 데이터 제품 수준에서 제공함으로써 제품 속도를 빠르게 가속화할 수 있습니다. 엔지니어들이 기술적인 도전을 극복하는 데 시간을 절약하고, 다수의 감각적 이점을 얻습니다.

데이터 품질을 선제적으로 모니터링하고 데이터 문제를 예방하며 비즈니스 이해관계자에게 (따라서 신뢰를 얻으며) 데이터 및 AI 이니셔티브를 무제알고 효율적으로 우선순위를 설정하는 것은 모두 통합 데이터 조율 플랫폼 또는 데이터 제어 플레인으로 가능합니다. Orchestra와 같은 제어 플레인은 기업이 전례 없는 속도로 데이터를 활용할 수 있도록 길을 열고 있습니다.

# 기존 데이터 조율 솔루션의 한계점

기존 조율 솔루션은 여러 가지 중요한 영역에서 종종 한계점을 지니고 있습니다. 첫째, 그들은 종종 사용자들에게 파이썬 코드를 큰 monorepo에서 작성하도록 강요하여 코드베이스가 커질수록 규모화하고 관리하기 어렵게 만듭니다. 이는 복잡성과 유지보수 도전을 증가시킬 수 있습니다.

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

또한, 많은 기존 도구들에서는 가시성과 종단간 메타데이터 추적이 부족합니다. 이로 인해 사용자들이 데이터 및 프로세스의 흐름을 이해하기 어려워지며, 디버깅과 모니터링에 문제가 발생할 수 있습니다.

마지막으로, 데이터 및 AI 제품의 성능을 종합적으로 파악할 수 있는 사용자 친화적 인터페이스가 부족합니다. 이러한 UI의 부재로 사용자들은 워크플로우를 쉽게 모니터링하고 최적화하여 성능과 효율성을 향상시키기 어려워집니다.

## Multi-Tenancy와 거버넌스 부재

다양한 팀이 다양한 소프트웨어를 사용하는 환경에서는 전체 데이터 운영에 대한 다중 테넌트 뷰를 얻는 것이 불가능합니다. 이는 팀 간 디버깅 라인지 그래프를 어렵게 만들며, 협업에 있어서 도전을 초래하며, 데이터 파이프라인을 관리하기 어렵게 만듭니다.

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

## 보안 준수

단일 저장소에서는 개인 및 클러스터 모두 필요 이상의 비밀 정보에 액세스할 수 있습니다. 이는 여러 사람이 실제로 액세스할 필요가 없는 코드 베이스의 일부에 액세스할 수 있어 장애 발생 가능성을 높이게 됩니다.

## 경보 및 소유권

다양한 팀이 사용하는 여러 저장소가 있는 경우, 경보 및 데이터 자산 소유는 도전적일 수 있습니다.

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

## 원활한 지속적 통합

자주 놓치는 요소 중 하나인 데이터 파이프라인 저장소의 지속적 통합은 서비스 기반의 데이터 수집 스크립트와 같은 서비스 기반의 마이크로서비스에 대해 비교적 직관적입니다.

그러나 dbt를 활용한 분석 저장소의 경우, dbt Slim CI와 같은 것이 필요하거나 프로젝트 Nessie의 데이터를 위한 Git-Control이 필요합니다. 이러한 환경에서 최소한의 CI가 실행되도록 보장하는 것만으로도 챌린지입니다. 그리고 데이터 수집, 스트리밍, 변환, 운영 분석에 대한 코드가 동일한 위치에 있는 경우, 더 복잡해집니다.

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

본질적으로, 기본 사항인 제품 접근 방식으로 내려가는 것이 중요합니다. 제품 중심이 되는 것은 무엇을 의미할까요? 사용자 경험과 개선에 대한 완전한 초점. XYZ 작업 수행을 위한 노력을 줄이는 것입니다.

데이터 제품을 구축할 때, 우리는 고객을 '사용자'로만 다루지 않고, 데이터 엔지니어와 분석 엔지니어도 데이터 제품 접근 방식의 '사용자'로 취급합니다. 그들의 삶은 기관이 데이터를 구축하는 방식으로 지속적으로 영향을 받는데, 그것은 최종 사용자에도 항상 영향을 줍니다.

제품 접근 방식은 거대한 전략이 아닙니다. 사실, 데이터 워크플로우의 작은 구석마다 그리고 마지막 순간마다 살아있는 원자적 전략입니다. 오케스트레이션은 이 이야기에서 제외되지 않으며 사실, 이를 상당부분 이끌어 내는 요소입니다. 데이터 제품의 맥락에서 오케스트레이션도 사용자 중심 접근 방식을 채택하여 데이터 및 분석 엔지니어가 약간 더 용이하게 호흡할 수 있도록 합니다.
