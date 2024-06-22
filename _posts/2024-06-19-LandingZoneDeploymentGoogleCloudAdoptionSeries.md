---
title: "랜딩 존 배포 Google Cloud 채택 시리즈"
description: ""
coverImage: "/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_0.png"
date: 2024-06-19 13:19
ogImage: 
  url: /assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_0.png
tag: Tech
originalTitle: "Landing Zone Deployment (Google Cloud Adoption Series)"
link: "https://medium.com/google-cloud/landing-zone-deployment-google-cloud-adoption-series-bdc0b36106d0"
---


구글 클라우드 채택 및 이전 시리즈에 다시 오신 것을 환영합니다. 이 글이 좀 늦게 나온 점 죄송합니다. 기술적인 문제가 있어서요! (나중에 더 설명하겠죠.)

이전에는 핵심 LZ 팀을 구성하는 방법, 필요한 지원을 받는 방법, 실행해야 할 워크샵, 그리고 설계를 기록하고 문서화하는 방법을 다뤘습니다.

오늘은 재미있는 부분에 진입합니다! 이제 실제로 LZ를 배포해 봅시다! 

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_0.png)

<div class="content-ad"></div>

# LZ를 배포하는 네 가지 방법

Google 클라우드 랜딩 존을 배포하는 네 가지 접근 방법이 있습니다. 이러한 방법들은 다음과 같습니다:

- “클릭하고 배포” — Google 클라우드 콘솔의 안내에 따라 클릭 단계별 프로세스를 따르는 방식으로, Google 클라우드 Foundation 설정 체크리스트에서 지원합니다. 버튼을 누르면 실제 배포가 수행됩니다.
- “클릭하고 다운로드하고 배포” — 여기서는 콘솔에서 UI 기반 프로세스를 계속 따릅니다. 그러나 콘솔에서 배포를 실행하는 대신, 프로세스에 의해 생성된 Terraform 구성을 다운로드합니다. 이를 통해 배포를 별도로 저장, 조정 및 실행할 수 있습니다. 또한 배포를 취소할 수도 있습니다!
- Cloud Foundation Fabric FAST — 사전에 Fabric 참조 블루프린트 세트를 사전 집계함으로써 구축된 기업용 랜딩 존 블루프린트 시범 구현입니다. 이는 Terraform 기반의 솔루션으로 처음부터 GCP LZ를 부트스트래핑하고 빌드하는 것입니다.
- 직접 Terraform 구축 — 가능하긴 합니다. 그러나 추천하지는 않습니다. Google은 처음부터 FAST를 구축하고 다양한 기업들로부터 많은 피드백을 모았습니다. 그래서 이 옵션에 대해 더 이상 다루지 않겠습니다.

# 사전 요구사항

<div class="content-ad"></div>

어떤 방식을 선택하든, LZ 설계 단계를 완료하는 것이 중요합니다. 해야 할 결정 사항이 많이 있습니다.

# 1 — 클릭 옵스 및 배포

이 방법은 Google Cloud 콘솔을 통해 진행하는 클릭 단위 안내 프로세스를 사용합니다.

## 누구를 위한 것인가?

<div class="content-ad"></div>

이는 매우 작은 조직 및 매우 작은 Google Cloud 플랫폼 팀을 대상으로하며, Terraform 기술이 거의나 전혀 없는 경우가 많습니다.

## 장점

- 전반적인 프로세스가 매우 빠릅니다. Google 조직 및 랜딩 존을 구성하고 배포하는 데 1시간 또는 2시간이면 충분합니다.
- Terraform 기술이 필요하지 않습니다. 전체 프로세스를 Google Cloud 콘솔을 통해 실행할 수 있습니다.
- 제한된 구성 옵션을 제공하여 간단하고 쉽게 따르기 쉽습니다.

## 단점

<div class="content-ad"></div>

- 최종 LZ의 제한된 구성 가능성.
- 반복성 없음.
- 자동화된 CI/CD 파이프라인, 테넌트 팩토리 또는 프로젝트 팩토리 생성 불가.

## 단계 요약

- 조직 설정 — 조직 생성; 조직에 연결된 클라우드 ID 테넌트 설정; 도메인 확인; 및 수퍼 관리자 계정 설정.
- 클라우드 ID의 사용자 및 그룹 구성 — GCDS를 사용한 동기화 포함; 필요한 경우 SSO 설정; 관리자 그룹 설정 (예: 조직, 결제, 네트워크, 보안, 로깅).
- IAM을 사용한 관리적 액세스 설정 — 이전에 설정한 그룹에 사용자를 매핑; Google Cloud IAM 역할을 그룹에 할당.
- 결제 설정 — 결제 계정; 예산 및 결제 경고; 결제 내보내기.
- 리소스 계층 구조 및 액세스 생성 — 폴더 및 공통 프로젝트를 포함한 초기 리소스 계층 구조.
- BigQuery로의 로깅 중앙화.
- 네트워킹 — 공유 VPC 배포; 하이브리드 연결 구성; 초기 방화벽 구성; 출발 라우트 및 NAT.
- 하이브리드 연결
- 모니터링 — 중앙 집중식 클라우드 모니터링 구성.
- 보안 — 조직 정책 구성; SCC 대시보드 활성화.
- 지원 — 기본 또는 프리미엄 지원과 같은 선호하는 지원 옵션 선택.

실제 배포 단계는 설정의 8단계 이후에만 발생합니다. 이 지점까지 모든 것은 적용할 구성입니다.

<div class="content-ad"></div>

세부 단계를 자세히 살펴보기 전에 먼저 Google Cloud 설정 체크리스트를 시작해 보세요. 이 체크리스트는 프로세스를 완료하는 데 단계별 지침을 제공합니다. 체크리스트의 각 번호가 매겨진 항목은 더 많은 세부 정보를 보여줍니다.

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_1.png)

## 1 — 조직 설정

우선 Google Cloud Identity를 설정해야 하며(이미 Cloud Identity 또는 Google Workspace 고객이 아닌 경우), 그런 다음 Cloud Identity 계정을 Google Cloud 조직에 연결해야 합니다.

<div class="content-ad"></div>

아직 구글 클라우드 아이덴티티 고객이 아니라면, 클라우드 아이덴티티 가입 페이지를 열어 진행할 것입니다. 클라우드 아이덴티티에는 무료 및 프리미엄 버전이 있지만, 이 프로세스에서는 무료 티어를 사용하는 방법을 안내해 드릴게요.

여기 유용한 정보가 있어요! 저와 같이 이미 클라우드 아이덴티티 계정을 보유하고 구글 클라우드 조직을 만든 경우를 상상해봅시다. 하지만 이 프로세스를 실험하기 위해 새로운 클라우드 아이덴티티 계정과 별도의 조직을 만들고 싶을 수 있습니다. 그렇다면 서브도메인을 사용하여 그것을 할 수 있어요! 예를 들어, 저는 이미 구글 클라우드 조직으로 just2good.co.uk 도메인을 사용하고 있습니다. 그러나 데모 목적으로 gcp-demos.just2good.co.uk 서브도메인을 방금 만들었어요.

클라우드 아이덴티티 가입은 다음과 같이 보이는 화면에서 시작됩니다:

![클라우드 아이덴티티 가입 페이지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_2.png)

<div class="content-ad"></div>

그럼 비즈니스 도메인 이름을 지정해야 합니다. 이것은 우리의 Google Cloud 조직 이름이 될 것입니다.

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_3.png)

그런 다음 도메인 이름을 입력합니다:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_4.png)

<div class="content-ad"></div>

이제 슈퍼 관리자 계정 세부 정보를 입력해야 합니다. 이 이메일 주소는 Google Cloud Identity의 슈퍼-관리자가 될 것입니다. Google Cloud Identity Admin 콘솔에 로그인하는 데 사용할 이메일 주소입니다. (Google Cloud 콘솔과는 구별되어야 합니다.) 방금 전에 지정한 비즈니스 도메인과 관련된 이메일이어야 합니다. 예를 들어: mydomain.com의 Bob이 슈퍼 관리자라면, super-bob@mydomain.com과 같은 주소를 사용하는 것이 좋습니다.

참고: 이는 강력한 계정으로, Google Cloud 조직과 관련된 이메일 주소나 그룹과 완전히 분리되어 있습니다.

Cloud Identity Admin 콘솔에 로그인하십시오:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_5.png)

<div class="content-ad"></div>

이제 도메인 이름을 확인해야 합니다.

![도메인 확인](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_6.png)

도메인 확인 프로세스는 빠르고 쉽습니다. 일반적으로 구글에서 제공하는 값을 사용하여 DNS TXT 레코드를 생성하여 이 작업을 수행합니다. Admin Console에서 프로세스를 안내해줍니다.

도메인이 확인되면 Google Cloud 조직 리소스가 자동으로 생성됩니다. 이는 Google Cloud의 최상위 조직입니다.

<div class="content-ad"></div>

어드민 콘솔에서는 사용자를 만드는 것을 제안합니다. 그러나 Cloud Identity 어드민 콘솔이 아닌 Google Cloud 콘솔에서 진행하려고 합니다. 그래서 "Google Cloud 콘솔에서 설정" 링크를 클릭해주세요.

![Google Cloud 콘솔에서 설정](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_7.png)

이제 Google Cloud 콘솔에서 Cloud Foundation 설정 체크리스트를 계속 진행할 수 있습니다.

![Google Cloud 콘솔에서 설정](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_8.png)

<div class="content-ad"></div>

그러나 현재 이 단계에서 - Cloud Identity 관리자 콘솔에서 - 제안드리는 것이 있습니다:

- 하나 또는 두 개의 추가 수퍼 관리자 계정을 추가하는 것. (만일 유일한 수퍼 관리자가 거대한 구덩이로 떨어진다면 어떻게 될까요?)

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_9.png)

- 수퍼 관리자를 위해 이중 인증(MFA) 설정.
- 계정 복구 설정.
- 암호 만료를 포함한 암호 정책 정의.

<div class="content-ad"></div>

1단계가 완료되었습니다!

## 2 — 클라우드 아이덴티티에서 사용자 및 그룹 설정

여기서 사용자 그룹을 생성하고 이러한 그룹에 구성원을 추가합니다. 체크리스트의 이 단계에서는 클라우드 설정의 모든 단계에 참여할 사용자를 추가하는 것이 좋습니다.

구글 클라우드 설정으로 돌아가 Step 2로 이동할 수 있습니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_10.png" />

클라우드 ID 관리자 콘솔을 통해 슈퍼 관리자가 관리 그룹을 프로비저닝하고 초기 사용자를 설정할 수 있습니다. 다음과 같은 방법으로 할 수 있습니다:

- 클라우드 ID 관리자 콘솔에서 그룹(및 사용자)을 수동으로 추가하는 것.
- 기존 LDAP 또는 Active Directory (AD) 시스템에서 ID를 복제하기 위해 Google Cloud Directory Sync (GCDS)를 설정하는 것. 이 무료 도구는 사용자 및 그룹을 Google Cloud로 단방향 동기화합니다. 기존 LDAP/AD 시스템이 여전히 원본 소스로 유지됩니다. 시리즈 이전 기사에서 더 자세히 설명하겠습니다.

<img src="/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_11.png" />

<div class="content-ad"></div>

- 그러나 가장 쉬운 방법은 Google Cloud Setup의 "모든 그룹 생성" 버튼을 사용하여 그룹을 만드는 것입니다. 이렇게 하면 클라우드 ID에서 권장하는 그룹이 자동으로 할당됩니다.

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_12.png)

이 단계를 완료하면 그룹이 할당되어 Google 관리자 콘솔에서 볼 수 있습니다.

이제이 그룹에 사용자(멤버)를 추가할 수 있습니다. "Google 관리자 콘솔로 이동"을 클릭하세요:

<div class="content-ad"></div>


![그룹에 사용자 추가](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_13.png)

그런 다음 이 그룹에 사용자를 추가하세요. 몇 가지 샘플 사용자를 만들어 두었습니다:

![샘플 사용자](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_14.png)

이 사용자들은 자동 이메일을 받게 됩니다. 물론... 유효한 메일함이 있는 경우에만요! (이와 같은 데모용으로, 일반적으로 *@my-domain 전달 규칙을 설정해 유효한 이메일 계정으로 전달하곤 합니다.)


<div class="content-ad"></div>

다음으로, 해당 그룹에 그들을 추가하겠습니다:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_15.png)

이제, 구글 클라우드 콘솔로 돌아가주세요. 그룹에 멤버가 추가된 것을 확인할 수 있을 거에요:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_16.png)

<div class="content-ad"></div>

이제 "관리 액세스로 계속"을 클릭할 수 있습니다.

## 3 - 관리 액세스

다음으로, 이전 단계에서 생성한 각 그룹에 Google Cloud IAM 역할을 할당합니다. 이는 클라우드 ID 그룹에 적절한 Google Cloud 권한을 부여합니다.

구글 클라우드 설정은 이전에 생성한 각 그룹에 할당될 역할을 제안할 것입니다. "저장 및 액세스 권한 부여"를 클릭하여 진행할 수 있습니다.

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_17.png)

## 4 — 청구

이 단계에서는 청구 계정을 생성하고(이미 보유하고 있지 않은 경우) 새 Google Cloud 조직과 연결합니다. 예산 설정, 청구 알림 및 청구 내보내기를 선택적으로 구성할 수도 있습니다.

청구 계정은 소비한 모든 Google Cloud 자원을 지불하는 데 사용됩니다. 자원 소비(및 비용)은 프로젝트 수준에서 누적되며, 각 프로젝트는 청구 계정과 연결됩니다.


<div class="content-ad"></div>

아래는 해당 데모를 위해 사용할 기본 청구 계정 유형인 온라인("셀프 서비스") 유형으로 계속하겠습니다. 자격을 갖춘 조직은 나중에 청구서 청구 계정으로 변경할 수 있습니다.

"온라인 청구 계정"을 선택한 후 "계속"을 클릭한 다음 "청구 계정 생성"을 클릭하세요:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_19.png)

<div class="content-ad"></div>

청구 계정 설정을 완료하려면 결제 카드 정보를 입력해야 합니다. 걱정하지 마세요. 아직 요금이 청구되지는 않을 거에요. 참고로 Google Cloud 초기 설정에는 300달러의 무료 크레딧이 제공됩니다.

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_20.png)

"내 청구 계정"을 클릭하면 예산 및 청구 알림을 설정할 수 있습니다. (나중에도 언제든지 설정할 수 있어요.)

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_21.png)

<div class="content-ad"></div>

예산 및 알림을 클릭한 후 예산 생성을 선택하세요. 예산 이름을 지정하고 시간 범위를 설정하고 적용할 프로젝트를 선택하세요 ("모두" 프로젝트가 기본 설정입니다).

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_22.png)

다음으로 경고를 발생시킬 임계값과 경고를 전송할 위치를 지정합니다. 이 예에서는 청구 관리자 그룹에 이메일을 보낼 것이지만, Pub/Sub 주제에 쓰는 것과 같이 더 복잡한 작업도 수행할 수 있습니다.

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_23.png)

<div class="content-ad"></div>

## 5 - 리소스 계층구조와 액세스

이제 이른바 초기 설계 결정을 활용하기 시작하는 단계입니다. 여기서는 조직 리소스 계층구조를 설정합니다.

이 단계에서는 계층구조 단계에서 여러 프로젝트를 생성하기 때문에 청구 계정과 연결된 프로젝트 할당량을 증가 요청해야 할 수도 있습니다. 클라우드 설정 단계에서 할당량 요청 방법을 안내해줍니다. 할당량 요청을 수행하는 계정이 유효한 이메일 주소를 가지고 있는지 확인해주세요!

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_24.png)

<div class="content-ad"></div>

한동안 이 기사를 완성하는 데 방해가 된 것은 할당량 요청 단계였어요. Google은 2영업일 이내에 요청에 대응해야 한다고 했지만 작성 시점에서 할당량 상승을 처리하는 구글 프로세스가 조금 문제가 있었네요. 그래서 수동 개입이 필요했죠! 구글 친구들이 이 문제가 곧 해결될 것이라고 말해 주었어요.

그럼 'Cloud Setup'으로 돌아가볼게요… 우리는 네 가지 프리셋 계층 청사진 중 하나를 선택할 수 있어요.

![Image](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_25.png)

목록에서 계층을 선택하면 콘솔에서 생성된 계층이 어떻게 보일지 미리 보여 줍니다. 함께 비교해 볼까요…

<div class="content-ad"></div>

간단하고 환경 중심의 계층 구조입니다:

이 계층 구조는 매우 소규모 조직을 운영할 때 좋습니다. 아마 개발자 소수만 있을 것입니다.

```js
Org/
├── Common/
│   ├── vpc-host-prod 📦
│   ├── vpc-host-nonprod 📦
│   ├── logging 📦
│   ├── monitoring-prod 📦
│   ├── monitoring-nonprod 📦
│   └── monitoring-dev 📦
├── Prod/
│   ├── app1-prod-svc 📦
│   └── app2-prod-svc 📦
├── Nonprod/
│   ├── app1-nonprod-svc 📦
│   └── app2-nonprod-svc 📦
└── Dev/
```

- 프로젝트는 📦 아이콘으로 표시됩니다. 다른 항목들은 폴더입니다.
- 이 계층 구조는 공통(Common) 폴더와 Prod, Nonprod, Dev와 같은 세 가지 환경을 가장 상위 수준으로 구성합니다.
- 여기서 Prod와 Nonprod에서 두 개의 공유 VPC 디자인을 구현하고 있음에 주목하세요. Common에는 각각의 호스트 프로젝트가 있습니다.
- 이 디자인은 환경 당 메트릭 범위를 갖는 모니터링 디자인을 구현합니다. 각 메트릭 범위를 호스팅할 프로젝트가 있습니다.

<div class="content-ad"></div>

간단하고 팀 중심적인 등급체계:

```js
Org/
├── Common/
│   ├── vpc-host-prod 📦
│   ├── vpc-host-nonprod 📦
│   ├── logging 📦
│   ├── monitoring-prod 📦
│   ├── monitoring-nonprod 📦
│   └── monitoring-dev 📦
├── team-huey/
│   ├── Prod/
│   │   └── huey-prod-svc 📦
│   ├── Nonprod/
│   │   └── huey-nonprod-svc 📦
│   └── Dev/
└── team-dewey/
    ├── Prod/
    │   └── dewey-prod-svc 📦
    ├── Nonprod/
    │   └── dewey-nonprod-svc 📦
    └── Dev/
```

- 여기서 최상위 분류는 환경이 아닌 팀에 따라 구성됩니다.
- 각 팀은 그런 다음 세 환경을 위한 폴더로 나뉩니다.

<div class="content-ad"></div>

```js
조직/
├── 공통/
│   ├── vpc-host-prod 📦
│   ├── vpc-host-nonprod 📦
│   ├── 로깅 📦
│   ├── 모니터링-운영 📦
│   ├── 모니터링-비운영 📦
│   └── 모니터링-개발 📦
├── 운영/
│   ├── 리테일뱅킹/
│   │   ├── 휴이/
│   │   │   └── 운영/
│   │   │       └── rb-huey-prod-svc 📦
│   │   └── 듀이/
│   │       └── 운영/
│   │           └── rb-dewey-prod-svc 📦
│   ├── 자산관리/
│   └── 모기지/
├── 비운영/
│   ├── 리테일뱅킹/
│   │   ├── 휴이/
│   │   │   └── 비운영/
│   │   │       └── rb-huey-nonprod-svc 📦
│   │   └── 듀이/
│   │       └── 비운영/
│   │           └── rb-dewey-nonprod-svc 📦
│   ├── 자산관리/
│   └── 모기지/
└── 개발/
    ├── 리테일뱅킹
    ├── 자산관리
    └── 모기지
```

- 상위 수준에서 각 환경을 기준으로 구성되었지만, 각 환경은 각각의 사업부로 나누어집니다.
- 각 사업부에는 여러 팀이 포함되어 있습니다.

사업부 중심 계층 구조:

```js
조직/
├── 공통/
│   ├── vpc-host-prod 📦
│   ├── vpc-host-nonprod 📦
│   ├── 로깅 📦
│   ├── 모니터링-운영 📦
│   ├── 모니터링-비운영 📦
│   └── 모니터링-개발 📦
├── 리테일뱅킹/
│   ├── 휴이/
│   │   ├── 운영/
│   │   │   └── rb-huey-prod-svc 📦
│   │   ├── 비운영/
│   │   │   └── rb-huey-nonprod-svc 📦
│   │   └── 개발/
│   └── 듀이/
│       ├── 운영/
│       │   └── rb-dewey-prod-svc 📦
│       ├── 비운영/
│       │   └── rb-dewey-nonprod-svc 📦
│       └── 개발/
├── 자산관리/
│   ├── 휴이/
│   │   ├── 운영/
│   │   ├── 비운영/
│   │   └── 개발/
│   └── 듀이/
│       ├── 운영/
│       ├── 비운영/
│       └── 개발/
└── 모기지/
    ├── 휴이/
    │   ├── 운영/
    │   ├── 비운영/
    │   └── 개발/
    └── 듀이/
        ├── 운영/
        ├── 비운영/
        └── 개발/
```

<div class="content-ad"></div>

- 여기서는 비즈니스 단위, 상위 레벨의 팀, 환경을 카테고리화합니다.

우리가 선택한 계층 구조에 관계없이 다음을 구성할 수 있다는 점에 유의하세요:

- 비즈니스 단위의 수 및 이름. (본 데모에서는 내 조직이 금융 기관 / 은행이라고 가정하고, 소매 은행, 자산 관리 및 모기지를 위한 최상위 비즈니스 단위를 만들었습니다.)
- 팀의 수와 이름.
- 세 개의 환경의 이름.
- 공유 VPC 호스트 프로젝트와 연결된 서비스 프로젝트의 이름.
- 추가 사용자 정의 프로젝트.

상당한 규모의 조직의 경우 환경 중심의 계층 구조 (환경 → 비즈니스 단위 → 팀)을 선호하는 편이며, 이 데모에서 이 계층 구조를 사용하려고 합니다.

<div class="content-ad"></div>

이제 우리는 계층 구조의 폴더와 프로젝트에 IAM 역할을 적용해야 합니다. Google Cloud 설정은 각 그룹에 추가해야 할 역할을 권장합니다. 그러나 이번에는 조직 수준에서만 적용하는 것이 아니라 리소스 계층에 적용합니다. IAM 정책은 계층 구조를 따라 상속되며 권한이 누적됩니다. 따라서 유효한 액세스는 각 수준에서 상속된 정책과 가장 낮은 수준의 정책을 합한 것입니다.

Google Cloud 설정 권장 사항은 다음과 같을 것입니다:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_26.png)

"드래프트 구성 확인"을 클릭해주세요.

<div class="content-ad"></div>

## 6 - 중앙 집중식 로깅

여기서 클라우드 설정을 통해 중앙 집중식 로깅을 설정할 수 있습니다.

![Cloud Setup Image](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_27.png)

시작 로깅 구성을 클릭하세요:

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_28.png" />

먼저, 모든 감사 로그의 집계 로깅을 중앙 집중식 로깅 버킷에 설정합니다. 이 버킷은 이전에 생성한 로깅 프로젝트에 저장됩니다. (여기에 문서화한 최상의 모범 사례대로입니다.)

<img src="/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_29.png" />

또한 BigQuery로 로그 라우팅 및 아카이브 로깅(예: 규정 준수 목적)을 더 저렴한 GCS 버킷으로 라우팅할 수 있습니다.

<div class="content-ad"></div>

아래와 같이 Markdown 형식으로 테이블 태그를 변경해주세요.


Go ahead and “Confirm draft configuration.”

![image](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_30.png)

## 7 — VPC Networks

In this step, we set up a pair of shared virtual private cloud (VPC) networks, as per the dual shared VPC pattern: one in prod, and one in non-prod.


<div class="content-ad"></div>

마크다운 형식의 이미지 테그를 변경하세요.

![](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_31.png)

Google Cloud 설정은 각 VPC마다 쌍으로 서브넷을 구성해야 합니다. 이것이 최소 요구사항이며, 추가 서브넷을 이 단계에서 구성할 수도 있습니다.

![](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_32.png)

추천드리는 것은:

<div class="content-ad"></div>

- 한 지역에서 첫 번째 프로드 Subnet을 생성하고, 다른 지역에서 두 번째 프로드 Subnet을 생성하세요. 이렇게 함으로써 지역적인 Redundancy가 필요한 이중 지역 아키텍처를 배포할 수 있습니다. 또한 제가 여기서 설명한 대로 99.99% SLA를 갖는 HA Hybrid Connectivity를 구성할 수 있습니다.
- 비 프로드에도 비슷한 두 개의 Subnet을 구성하세요. 참고: 비 프로드 VPC에는 프로드 VPC에 선택한 IP CIDR 범위와 동일한 것을 사용할 수 있습니다. 이는 IP가 특정 VPC 내에서만 고유해야 한다는 점 때문입니다. 그러나 이로 인해 프로드 Subnet을 비프로드 Subnet에 피어링할 수 없게 되지만, 이 분리를 강제하고 싶을 수도 있습니다.
- 각 Subnet에서 개인 Google 액세스를 활성화하세요. 이를 통해 외부 IP 주소가 없는 VM도 Google API 및 서비스의 공용 IP 주소에 액세스할 수 있습니다.
- 각 Subnet에서 Cloud NAT를 활성화하세요. 이를 통해 다음에 대한 인터넷 외부 연결이 가능해집니다: 외부 IP 주소가 없는 VM, Private GKE 클러스터, 서버리스 VPC 액세스를 통한 Cloud Run, 서버리스 VPC 액세스를 통한 Cloud Functions.
- 권장 VPC 방화벽 규칙을 구성된 상태로 유지하세요. 기본적으로 Google은 VPC 내의 어느 곳에서나 ICMP를 허용하는 방화벽 규칙을 적용하고, SSH 또는 RDP는 클라우드 ID 기반 프록시 (IAP) 범위(35.235.240.0/20)에서만 허용합니다.
- 기본적으로 설정은 방화벽 규칙 로깅을 활성화합니다. 그러나 이는 비용이 많이 발생할 수 있습니다. 특정 규칙에 대해서만 로깅을 활성화하는 것을 고려해보세요.
- 기본적으로 설정은 VPC 플로우 로그를 활성화합니다. 이는 VPC 내 VM(포함하여 GKE 노드)에 의해 송수신된 네트워크 흐름의 샘플을 기록합니다. 네트워크 분석 및 포렌식에 유용합니다. 그러나 다시 말씀드리지만, 비용이 많이 발생할 수 있습니다. 제 추천은 로그를 켜둔 채로 완전하게 설정한 후, 플로우 로그를 조절하여 로깅 양을 제한하는 것입니다. (나중에 FinOps 권장사항에서 이를 다룰 예정입니다.)

여기가 제 구성입니다. (요금을 최대한 저렴하게 유지하기 위해 로깅은 꺼져 있습니다. 이 데모의 목적을 위해)

`![image](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_33.png)`

“서비스 프로젝트로 계속하기”를 클릭하세요. 이렇게 하면 우리가 이전에 구성한 프로젝트를 서비스 프로젝트로 연결할 수 있는 화면으로 이동합니다. 이를 통해 이러한 프로젝트가 공유 VPC를 사용할 수 있도록 할 수 있습니다.

<div class="content-ad"></div>

마지막으로, "확인된 초안 설정"을 클릭하세요.

## 8—하이브리드 연결

그다음, 클라우드 설정에서는 하이브리드 연결을 설정합니다. 현재 이 설정 작업은 미리보기 단계에 있습니다. IPsec VPN을 사용하여 하이브리드 연결을 구성할 수 있습니다.

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_34.png)

<div class="content-ad"></div>

시작하면 다음과 같은 화면을 볼 수 있습니다:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_35.png)

이 데모의 목적으로 하이브리드 연결 설정은 하지 않겠습니다.

## 배포 또는 다운로드

<div class="content-ad"></div>

이제 중요한 선택이 있습니다. 지금까지 구성한 모든 것을 적용할 수 있는 콘솔에서 직접 배포할 수 있습니다.

또는 구성한 Terraform을 다운로드할 수도 있습니다. Terraform을 다운로드하면...

# 2 — “ClickOps, 다운로드하고 배포하기”

여기서 위에서 한 모든 단계는 동일합니다. 그러나 콘솔에서 배포하는 대신에 Terraform을 다운로드합니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_36.png" />

## 누구를 위한것인가요?

이것은 중소 규모 조직을 위한 것입니다. 이 조직은:

- Terraform을 사용하여 지속적인 LZ 및 Google Cloud 인프라 자원을 관리하려고 합니다. (그리고 이것은 항상 좋은 생각입니다!)
- 기본 클릭 옵션 설정에 추가적인 사용자 정의 및 구성을 추가할 수 있기를 원합니다.
- Fabric FAST와 같이 더 정교한 엔터프라이즈 LZ 배포로 진행하기에 충분히 강력한 플랫폼 팀이 필요하지는 않습니다.

<div class="content-ad"></div>

## 장점

- Cloud Console의 "Click-Ops" 프로세스로 생성된 Terraform 구성은 제가 설명한대로 가능한 한 쉽게 하루 안에 완료할 수 있습니다.
- 원하는대로 Terraform을 조정할 수 있습니다.
- Terraform은 소스 제어(예: GitHub)에 배치해야 합니다.
- Terraform 구성을 협업하여 작업할 수 있습니다.
- 향후 Terraform 구성에 변경 사항이 있으면 해당 변경 사항을 적용할 수 있습니다.
- 전체 LZ를 삭제하려면 한 줄로 몇 초만에 삭제할 수 있습니다.

## 단점

- Terraform 기술이 약간 필요합니다.
- 자동화된 CI/CD 파이프라인, 테넌트 팩토리 또는 프로젝트 팩토리를 생성하지 않습니다.

<div class="content-ad"></div>

## Terraform 설정 파일 다운로드하기

시작해 봅시다!

먼저, "Terraform으로 다운로드"를 클릭하세요. 이제 테라폼 상태를 저장할 버킷의 지역을 선택해 주세요. 실제로는 아직 버킷을 생성하지 않고, 나중에 다운로드할 backend.tf 파일에 고유한 버킷 식별자가 생성됩니다.

그런 다음, 테라폼 구성 파일을 다운로드하세요. 이 파일은 terraform.tar.gz로 로컬 기기에 다운로드됩니다.

<div class="content-ad"></div>

## 테라폼을 클라우드 셸에 업로드하세요

다운로드한 후, 테라폼을 실행하는 방법은 두 가지가 있습니다:

- Google Cloud Shell에서 실행.
- Cloud SDK가 설치된 장치에서 실행.

이 데모를 위해 간단하게 클라우드 셸을 사용하겠습니다. 테라폼으로 기본 구성을 배포하는 데 필요한 모든 것이 미리 설치되어 있습니다.

<div class="content-ad"></div>

클라우드 셸에 인증하고 Terraform 구성을 위한 폴더를 만들어봅시다:

```js
# 인증하기
gcloud auth list

# Terraform 구성을 위한 폴더 생성
mkdir tf-foundation-setup
cd $_
```

이제 콘솔을 통해 Terraform 구성 파일(.gz 파일)을 업로드한 다음, 이전에 만든 폴더에 파일을 추출해보세요:

```js
# 방금 업로드한 파일 추출
tar -xzvf ../terraform.tar.gz
```

<div class="content-ad"></div>

우리 폴더에 이 파일들이 있습니다:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_37.png)

## 테라폼 상태를 저장할 시드 프로젝트 생성

```sh
# 테라폼 상태를 저장할 시드 프로젝트 생성
SUFFIX=$RANDOM
PROJECT_ID=시드-프로젝트-$SUFFIX
gcloud projects create $PROJECT_ID
gcloud config set project ${PROJECT_ID}

# 요금 청구 계정 연결
gcloud billing projects link $PROJECT_ID --billing-account <당신의_청구_계정_ID>

# 테라폼으로 배포할 필요가 있는 API 활성화
gcloud services enable cloudresourcemanager.googleapis.com
gcloud services enable iam.googleapis.com
gcloud services enable serviceusage.googleapis.com
gcloud services enable cloudbilling.googleapis.com
gcloud services enable cloudidentity.googleapis.com
gcloud services enable orgpolicy.googleapis.com
```

<div class="content-ad"></div>

저희 조직에서 현재 가지고 있는 프로젝트를 간단히 살펴봅시다:

![프로젝트 이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_38.png)

방금 만들어 놓은 씨드 프로젝트 seed-project-28844를 확인할 수 있습니다.

이제 백엔드.tf 파일에서 상태 유지에 사용될 버킷을 살펴보겠습니다. 실제로 제 버킷 이름을 더 의미 있는 것으로 변경했습니다. 또한 전 세계적으로 고유한 이름이어야 합니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_39.png" />

이 버킷을 사용하기 전에 만들어야 합니다:

```js
gsutil mb gs://tfstate-28844
```

다음을 통해 만들어졌는지 확인할 수 있습니다:

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_40.png" />

## GitHub에 저장하기

이제 테라폼 구성을 GitHub에 저장하는 것이 좋을 시기입니다. 또는 Google Cloud Repos에 저장할 수도 있습니다. GitHub의 개인 저장소에 저장하는 과정을 다음과 같이 따를 수 있습니다:

```js
# 이전에 생성한 tf-foundation-setup 폴더에 있다고 가정합니다

# Cloud Shell에서 git을 설정합니다. 이전에 설정하지 않았다면
git config --global user.email "bob@wherever.com"
git config --global user.name "Bob"

# 로컬 git 저장소를 생성합니다.
# 진행하기 전에 .gitignore 파일이 생성되어 있는지 확인해주세요.
# .terraform 디렉토리 및 로컬 state, plans 등을 무시하도록 설정되어 있어야 합니다.
git init
git add .
git commit -m "Initial commit"

# GitHub 명령줄 도구를 인증합니다.
# 이미 Cloud Shell에 설치되어 있습니다.
gh auth login

# 이제 gh cli를 사용하여 GitHub에 원격 개인 저장소를 생성합니다.
gh repo create gcp-demos-foundation-setup --private --source=.
git push -u origin master
```

<div class="content-ad"></div>

좋아요. 이제 우리 코드는 안전하게 추적되어 있고 팀원들에게 제공됩니다.

![image](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_41.png)

## 권한이 있는지 확인하기

조직 관리자 그룹이 시드 프로젝트에서 적절한 역할을 부여받았는지 확인해야 합니다:

<div class="content-ad"></div>

```js
gcloud 조직 add-iam-policy-binding $ORG_ID \
  --member="group:gcp-organization-admins@gcp-demos.just2good.co.uk" \
  --role="roles/storage.admin"

gcloud 조직 add-iam-policy-binding $ORG_ID \
  --member="group:gcp-organization-admins@gcp-demos.just2good.co.uk" \
  --role="roles/compute.xpnAdmin"

gcloud 프로젝트 add-iam-policy-binding $PROJECT_ID \
  --member="group:gcp-organization-admins@gcp-demos.just2good.co.uk" \
  --role="roles/serviceusage.serviceUsageConsumer"
```

## 테라폼

마지막으로, 테라포밍할 준비가 됐어요!!

```js
# 테라폼 초기화
terraform init
```

<div class="content-ad"></div>

다음은 명령의 출력입니다.


![2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_42](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_42.png)


GCS 버킷의 콘솔 뷰를 새로고침하면 이제 버킷에 Terraform 상태 파일이 생성된 것을 확인할 수 있습니다:


![2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_43](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_43.png)


<div class="content-ad"></div>

지금까지 잘 진행되고 있어요.

```shell
# 테라폼 계획을 작성하고 확인합니다
terraform plan -out=plan.out
```

결과는 다음과 같습니다:

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_44.png)

<div class="content-ad"></div>

그리고 이제 진실의 순간입니다. 마침내 저희의 랜딩 존을 배포할 수 있습니다!

```js
# 계획을 적용하십시오!
terraform apply plan.out
```

몇 분이 소요됩니다. 그리고 성공했습니다! 만들어진 폴더와 프로젝트를 확인해보세요:

![랜딩 존 배포 이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_45.png)

<div class="content-ad"></div>

이제, 만든 모든 것을 파괴하고 싶다면, 이렇게 하면 됩니다:

```js
terraform destroy
```

![이미지](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_46.png)

한 가지 주의할 점: 클라우드 설정에서의 Terraform 구성에는 여러 하드코딩된 프로젝트 ID가 포함되어 있습니다. 위에서 설명한대로 LZ를 파괴하면 이러한 프로젝트 ID가 즉시 해제되지 않습니다. 결과적으로, 단순히 Terraform 구성을 다시 적용할 수 없습니다. 다시 적용하려면 프로젝트 ID를 변경해야 합니다.

<div class="content-ad"></div>

# 3 — 클라우드 기반 패브릭 FAST

## 누구를 위한가요?

구성 가능성이 높고, Terraform 기반 LZ를 사용하여 권한 분리, 다중 테넌시 및 즉시 사용 가능한 GitOps 및 CI/CD 파이프라인을 허용하는 대규모 조직을 위한 솔루션입니다.

## 방법은?

<div class="content-ad"></div>

실은, 이 주제에 대해 이전에 상세히 다룬 바 있습니다. 그래서 여기서 다시 다루지 않겠습니다.

# 요약

그럼 이 정도로 마무리 지어보겠습니다! 마침내, 착륙 구역 주제에 대한 이 섹션의 기사를 마쳤습니다. 이전에 다룬 내용은 다음과 같습니다:

- LZ 디자인
- LZ "기술 온보딩"을 위한 LZ 코어 팀 구성

<div class="content-ad"></div>

이 기사에는 LZ를 실제로 배포하는 방법을 안내했습니다.

이제 우리는 작동 중인 기업용 구글 클라우드 플랫폼을 갖추었고, 워크로드를 배포할 준비가 되었습니다!

다음 글에서 만나요.

# 가기 전에

<div class="content-ad"></div>

- 관심 있는 사람들에게 공유해주세요. 그들에게 도움이 될 수도 있고, 저에게 큰 도움이 됩니다!
- 박수를 좀 주세요! 여러 번 박수 치는 거, 알고 계시죠?
- 💬 댓글을 자유롭게 남겨주세요.
- 내 콘텐츠를 놓치지 않으려면 팔로우하고 구독해주세요. 프로필 페이지로 이동하여 다음 아이콘을 클릭하세요:

![Google Cloud](/assets/img/2024-06-19-LandingZoneDeploymentGoogleCloudAdoptionSeries_47.png)

# 링크

- 구글 클라우드의 랜딩 존: 무엇인가, 왜 필요한가, 어떻게 만드는가
- 구글 클라우드 콘솔: 클라우드 설정
- 구글 클라우드 설정 체크리스트
- 클라우드 아이덴티티 개요
- 구글 프라이빗 액세스
- 구글 클라우드 아이덴티티-인증 프록시 (IAP)
- 콘솔에서 다운로드한 테라폼으로 기본 구조 배포
- 테라폼 및 클라우드 기초 패브릭 FAST로 구글 클라우드 랜딩 존
- 구글 클라우드 아키텍처 프레임워크
- 엔터프라이즈 기본 토대

<div class="content-ad"></div>

# 시리즈 탐색

- 시리즈 전체 내용 및 구조
- 이전: 랜딩 존 기술 입사 - "어떻게" 
- 다음: 클라우드 소비자 및 테넌트를 활성화하기