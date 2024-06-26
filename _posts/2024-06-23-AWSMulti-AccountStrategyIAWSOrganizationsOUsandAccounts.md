---
title: "AWS 멀티 계정 전략 I AWS Organizations, OUs, 그리고 계정 활용 방법"
description: ""
coverImage: "/assets/img/2024-06-23-AWSMulti-AccountStrategyIAWSOrganizationsOUsandAccounts_0.png"
date: 2024-06-23 22:34
ogImage:
  url: /assets/img/2024-06-23-AWSMulti-AccountStrategyIAWSOrganizationsOUsandAccounts_0.png
tag: Tech
originalTitle: "AWS Multi-Account Strategy I: AWS Organizations, OUs, and Accounts"
link: "https://medium.com/towards-aws/aws-multi-account-strategy-i-aws-organizations-ous-and-accounts-a4860f475161"
---

생산 시작 조직 및 고급 조직을 위한 권장되는 다중 계정 패턴 두 가지

제품 엔지니어로서 항상 AWS 조직, OU 및 계정 솔루션을 마스터하는 데 어려움을 겪었습니다. 회사의 전체 AWS 환경을 종합적으로 파악할 수 없기 때문에 매일 적어도 하나의 AWS 계정에서 작업하더라도 문제입니다.

엔지니어의 관점에서 개발 목적으로 단일 계정은 충분합니다. 그렇다면 왜 다중 계정 전략이 필요할까요?

# 다중 계정 전략의 필요성

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

질문에 대답하기 위해서는 CTO의 관점으로 바꿔봐야 해요:

우리 회사에는 서로 다른 비즈니스 단위 또는 제품 팀이 각자 독특한 프로세스를 가지고 있어요. 비즈니스 목적과 소유권에 따라 작업량을 관리하는 OU가 중요해요.

또한, 서로 다른 비즈니스 부문은 클라우드 지출을 별도로 보고, 제어, 예측 및 예산 편성해야 할 수도 있어요. AWS 계정은 계정 수준에서 통합 비용 보고를 지원해, 이러한 금융 분할을 용이하게 해 줘요.

CTO로서, 보안 및 운영 정책도 고려해야 해요. 작업량은 종종 별도의 보안 요구 사항이 필요해, 별도의 제어 정책이 필요할 수 있어요. 예를 들어, 비생산 및 생산 환경은 일반적으로 서로 다른 보안 및 운영 정책이 필요해요.

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

여러 계정을 사용하여 비생산 및 생산 환경을 분리함으로써 각 환경의 리소스와 데이터가 다른 환경과 격리되어 안정성과 운영 관리가 강화됩니다.

뿐만 아니라, 멀티 계정 전략은 한 환경에서 발생하는 불상사의 영향을 제한하여 다른 환경을 보호합니다. 이는 다양한 IT 운영 모델을 지원하며 기관장과 운영 전략을 조직 내 다른 부분에 맞게 구성할 수 있도록 합니다.

따라서 저희 회사는 멀티 계정 전략을 통해 재무를 효율적으로 관리하고 보안을 강화하며 위험을 완화하며 다양한 운영 요구를 지원받습니다.

이제 AWS에서 저희의 영향력을 확대하기 위해 멀티 계정 디자인 패턴을 탐색해 봅시다.

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

# 서비스

- AWS 계정: AWS 리소스와 해당 리소스에 액세스할 수 있는 ID를 포함하는 표준 AWS 계정입니다.
- AWS 조직: 통합된 관리를 위해 AWS 계정을 통합하는 데 생성된 엔터티입니다.
- 루트: 조직 내 모든 계정의 상위 컨테이너입니다. 여기에 적용된 정책은 모든 OU와 회원 계정에 영향을 미칩니다.
- AWS 조직 단위 (OU): 루트 내의 계정을 포함하는 컨테이너입니다. OU에 부착된 정책은 모든 하위 OU와 계정에 영향을 줍니다.

# 솔루션

## 프로덕션 스타터 조직

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

이 패턴은 주요 초점이 생산 환경에서 업무를 지원하는 데 있는 최소한의 시작 환경을 나타냅니다.

![이미지](/assets/img/2024-06-23-AWSMulti-AccountStrategyIAWSOrganizationsOUsandAccounts_0.png)

## Root

모든 조직 단위(OU) 및 계정의 상위 컨테이너입니다. 여기에 적용된 관리 정책은 모든 OU 및 계정, 관리 계정을 포함하여 영향을 미칩니다.

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

## 보안 조직 단위

보안 조직 단위는 AWS 환경에 보안 기능을 제공하는 계정, 작업 부하, 및 리소스를 포함하는 중요한 조직 단위입니다. 이 조직 단위는 일반적으로 보안 팀의 다양한 대표자로 구성된 중앙 집중화된 클라우드 플랫폼 또는 클라우드 엔지니어링 팀에 의해 관리됩니다.

"보안 조직 단위" 내에 "로그 아카이브 계정"과 "보안 도구 계정"을 생성하는 것이 권장됩니다.

- 로그 아카이브 계정: 조직의 모든 계정에서 로그 데이터를 통합하는 중요한 역할을 합니다. 감사, 구성 규정 준수, 운영 로그를 위한 중앙화된 저장 공간을 제공합니다. AWS API 접근 로그(AWS 클라우드트레일), 리소스 변경 로그(AWS Config) 및 기타 보안 관련 로그를 통합하는 것이 권장됩니다. 또한 계정 간 VPC 피어링을 사용하는 경우 VPC 플로우 로그 데이터를 통합할 수 있습니다.
- 보안 도구 (감사) 계정: AWS 보안 도구 및 콘솔에 대한 중앙집중식 위임된 관리자 액세스를 제공합니다. 보안 도구 계정은 조직의 모든 계정에 걸쳐 조사 목적의 뷰 전용 액세스를 제공합니다. AWS 보안 허브, Amazon GuardDuty, Amazon Macie, AWS AppConfig, AWS Firewall Manager, Amazon Detective, Amazon Inspector 및 IAM 액세스 분석기를 포함한 AWS 보안 서비스를 집중화하는 집계 지점 역할을 합니다.

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

## 워크로드 OU

비즈니스별 워크로드를 포함하며, 생산 및 비생산 환경을 모두 포함합니다. 우리 조직은 현재 프로덕션 스타터 단계에 있으므로, 이 패턴에서는 주로 프로덕션 환경의 워크로드를 지원하는 데 초점을 맞춥니다.

## 고급 조직

이 고급 조직 패턴은 일반 보안 서비스를 위한 보안 도구 환경, 더 격리된 워크로드 및 인프라 및 배포 환경 지원을 포함합니다. 추가 내용은 다음과 같습니다:

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

<img src="/assets/img/2024-06-23-AWSMulti-AccountStrategyIAWSOrganizationsOUsandAccounts_1.png" />

## Workloads OU

이 더 발전된 패턴에서는 프로덕션 워크로드 환경과 데이터를 프로덕션 계정에서 분리하여 스테이징 및 개발을 위한 별도의 OUs를 제공합니다.

## Infrastructure OU

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

인프라스트럭처 OU는 조직 내에서 공유되거나 사용되는 인프라 자원을 보유한 AWS 계정을 보유하며 중앙 집중식 운영 및 모니터링을 포함한 계정 관리를 담당합니다. 이 OU는 인프라 및 운영 팀이 소유하고 관리합니다.

- Identity Account: 기타 관리 및 작업 활동과 격리된 중앙 집중식 신원 연동을 위한 계정입니다. 해당 계정은 계정에 대한 액세스 및 통합 애플리케이션에 대한 권한을 관리합니다.
- Network Account: AWS 조직 내에서 네트워킹을 위한 중앙 허브입니다. 계정 간 및 온프레미스, 인터넷 트래픽 간의 트래픽을 관리합니다. 네트워크 관리자는 네트워크 트래픽에 대한 보안 조치를 구축 및 관리합니다. VPC를 관리하고 계정 간에 자원을 공유합니다.
- Monitoring Account: AWS 계정 간의 리소스, 애플리케이션, 로그 데이터 및 성능을 모니터링합니다. CloudWatch, Amazon Managed Service for Prometheus, Amazon Managed Grafana, Amazon OpenSearch 등의 도구를 활용합니다.
- Shared Services Account: AWS 계정 간에 공유되는 중앙 IT 서비스 및 자원을 호스팅하고 관리합니다. Shared Services 계정의 주요 목적은 유사한 공유 서비스를 통합하여 관리, 인터페이스 및 사용할 수 있는 단일 액세스 포인트를 제공하는 것입니다. 예를 들어, EC2 Image Builder를 AWS Resource Access Manager (AWS RAM)와 통합하여 특정 자원을 아무 AWS 계정이나 AWS 조직을 통해 공유할 수 있습니다.

## 배포 OU

배포 OU에는 워크로드를 빌드, 유효성 검사, 프로모션 및 릴리스하는 데 지원되는 리소스와 워크로드가 포함됩니다. AWS에서 지속적 통합/지속적 전달 (CI/CD) 능력을 배포하고 관리할 때, 배포 OU 내의 프로덕션 배포 계정 집합을 사용하여 CI/CD 관리 능력을 보유하는 것이 권장됩니다.

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

# 참고 자료

- AWS 조직용어 및 개념
- 여러 계정을 사용하여 AWS 환경 구성하기
