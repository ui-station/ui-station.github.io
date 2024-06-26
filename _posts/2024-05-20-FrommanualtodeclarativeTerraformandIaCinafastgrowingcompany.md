---
title: "매뉴얼에서 선언적 방식으로 빠르게 성장하는 회사에서의 Terraform과 IaC"
description: ""
coverImage: "/assets/img/2024-05-20-FrommanualtodeclarativeTerraformandIaCinafastgrowingcompany_0.png"
date: 2024-05-20 17:31
ogImage:
  url: /assets/img/2024-05-20-FrommanualtodeclarativeTerraformandIaCinafastgrowingcompany_0.png
tag: Tech
originalTitle: "From manual to declarative: Terraform and IaC in a fast growing company"
link: "https://medium.com/@workabletechblog/from-manual-to-declarative-our-journey-with-terraform-and-iac-c95e6778f3f0"
---

우리의 여정은 수동으로 인프라 리소스를 프로비저닝하고 유지하는 것에서 완전히 선언적 인프라 코드(IaC)로 이동하는 것이었습니다.

작성자: 워커블의 시니어 사이트 신뢰성 엔지니어 테오도어 커키리스, 워커블의 시니어 사이트 신뢰성 엔지니어 콘스탄티노스 루소프로스

![이미지](https://miro.medium.com/v2/resize:fit:1400/1*VLL84MCp0Wo4Ec3zwuoVvA.gif)

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

이 기사에서는 Workable에서의 인프라스트럭처 코드 (IaC) 여정을 논의할 것입니다. 이미 IaC에 익숙하신 분들 중에서 Terraform을 선호하는 도구로 채택한 분들은 이미 코드를 DRY하게, 간단하고 유지 보수가 쉽도록 구성하는 데 오는 도전과 불편함을 이해하고 계실 겁니다. (다름이 아니라 버그나 오타 하나로 생산 리소스가 중대한 문제를 일으킬 수 있습니다.) 동시에 확장 가능하고 유연하며 변화하는 빠르게 성장하는 회사의 요구 사항을 충족하기 위해 확장 가능하고 유연하며 확장 가능하게 만들기 위한 도구로 Terraform을 채택한 고객이 이미 이러한 도전과 불편함을 이해하고 있을 겁니다.

이 기사는 여러 해 동안의 IaC의 발전을 제공하지만, 만약 귀하가 귀사의 IaC를 구조화하는 방법에 대한 제안을 찾는 중이라면 마지막 섹션으로 건너뛰어도 됩니다. 해당 섹션에서는 현재 아키텍처를 설명하며, DRY(반복하지 마세요), 유연하며 우리 팀이 지속적으로 성장하는 인프라를 효과적으로 관리하는 데 효율적이고 확신을 주는 아키텍처로 믿고 있습니다.

Workable의 엔지니어링 발전 일부를 간단히 소개하기 위해 언급되었지만, 이것은 완전한 여정은 아닙니다. 더 알고 싶으시다면 우리의 엔지니어링 부사장들이 Voxxed Days에서 하는 훌륭한 발표를 시청해 보세요.

2012년 Workable이 시작됐을 때, 아주 소수의 엔지니어 팀은 최초 세대에서 작업했습니다. 이는 매우 적은 인프라 리소스를 요구하는 모놀리스 였습니다. 우리는 주로 PostgreSQL을 주요 지속성 계층으로, Solr를 텍스트 검색, Redis를 분산 캐싱으로 사용했습니다. 코드는 Heroku에 배포되었습니다. Heroku는 통합된 데이터 서비스를 제공하고 현대 어플리케이션을 배포하고 실행하기 위한 강력한 생태계를 제공하는 컨테이너 기반 플랫폼 서비스였습니다. 이를 통해 개발자들은 생산용 용량에서 인프라 관리의 복잡성 없이도 응용 프로그램 로직에 집중할 수 있었습니다.

제품은 연도가 흘러가면서 계속 성장했고, 2016년으로 빨리 앞으로 가면 이미 엔지니어링 팀은 사용자 경험을 향상시키는 보다 다양한 기능을 제공하기 위해 몇 개의 마이크로서비스를 도입했습니다. 그러나 이러한 마이크로서비스는 모놀리스의 스택과 도메인에 잘 들어맞지 않았으며 더 많은 마이크로서비스가 추가될 것이었습니다. 코드는 여전히 Heroku에 배포되었지만 제품은 상당히 커졌습니다. 우리는 이미 외부 제공업체의 서비스 (클라우드 저장소, 데이터베이스 등)를 사용하기 시작했고, 인프라 유지 및 모니터링은 엔지니어링 팀 간의 공동 책임으로 남아 있었습니다. 이때 SRE 팀이 형성되었습니다.

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

우리는 처음에 인프라를 관리하고 어떻게 관리해야 할지 평가하는 동안 모든 것을 수동으로 프로비저닝했어요. 회사로서 성장하던 2017년, 우리는 인프라를 프로비저닝, 구성 및 관리하는 더 효율적인 방법이 필요한 지점에 이르렀어요. 반복적이고 복잡해지며 실수하기 쉬운 수동 작업에서 벗어나 우리의 인프라 구성을 단순화하고 표준화하며 최소한의 노력으로 확장할 수 있는 프로세스가 필요했어요.

이것이 테라폼과 함께 하는 이야기: 우리 회사와 함께 성장하고 변화하는 인프라스트럭처를 정의하는 여정입니다.

# 1G IaC — 테라폼 도입

인프라스트럭처 코드 (IaC) 도구는 설정 파일을 사용하여 인프라를 관리할 수 있게 해줍니다. 이러한 도구들은 자원 구성을 정의하여 버전 관리, 재사용, 공유가 가능하게 함으로써 안전하고 일관되며 반복 가능한 방식으로 인프라를 구축, 수정 및 유지할 수 있도록 도와줍니다. 테라폼은 이러한 도구 중 하나로 HashiCorp에서 개발했어요.

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

테라폼은 인간이 읽기 쉬운 구성 파일을 사용하여 리소스와 인프라를 정의할 수 있게 해줍니다. 이는 HashiCorp가 개발한 선언형 언어인 HCL(HashiCorp Configuration Language)을 사용합니다. 선언형이란 인프라를 위한 원하는 최종 상태를 설명하는 것을 의미하며, 잘 정의된 단계별 지침이 필요한 절차적 프로그래밍 언어와는 다릅니다(예: Ansible은 IaC 영역에서 사용됩니다).

또한 테라폼은 인프라의 수명 주기를 관리하도록 허용하여 상태 파일을 유지함으로써 인프라의 원하는 최종 상태가 정의된 구성과 일치하는지 확인하고 변경 사항을 식별하고 적용합니다. 또한 리소스 간의 종속성을 결정하고 올바른 순서로 생성하거나 제거할 수 있습니다.

초기 단계에서 우리의 인프라 요구 사항은 매우 복잡하지 않았으며 단일 제공업체에 한정되었습니다. 결과적으로, 우리의 테라폼 코드의 디렉토리 구조는 소규모에서 중간 복잡성의 인프라에 대한 당시 표준을 따르고 있었습니다:

```js
infrastructure
└── aws
    ├── production
    │   ├── eu-west-1
    │   │   ├── ec2
    │   │   ├── lambda
    │   │   └── ...
    │   ├── global
    │   │   └── iam
    │   └── us-east-1
    │       ├── ec2
    │       ├── rds
    │       └── ...
    └── staging
        └── ...
```

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

일부 리소스를 관리하는 데는 문제가 없었고 깔끔하며 어떤 리소스가 어디에 설정되었는지 쉽게 추적할 수 있었으며, 프로덕션 및 스테이징 환경을 완전히 격리시킵니다. 그러나 마이크로서비스 아키텍처를 채택하고 개발, 테스트 및 프로덕션용 여러 환경으로 확장하면 다음과 같이 복잡해졌습니다:

- 모듈을 사용하지 않았기 때문에 코드가 DRY(반복이 줄어든 개발) 또는 표준화되지 않았습니다.
- 동일한 AWS 리소스의 인스턴스가 한 파일에 도입될수록 구성 파일이 더욱 장황해졌습니다:
  - 업데이트/검토하는 데 더 많은 시간과 노력이 필요했습니다.
  - Terraform이 계획을 더 많이 수행하고 적용하는 데 시간이 더 오래 걸렸습니다.

![FrommanualtodeclarativeTerraformandIaCinafastgrowingcompany_0.png](/assets/img/2024-05-20-FrommanualtodeclarativeTerraformandIaCinafastgrowingcompany_0.png)

우리는 각각이 있는 상당수의 마이크로서비스의 인프라를 관리해야 했습니다:

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

- 다른 리소스 세트가 필요합니다.
- 약간 다른 구성으로 여러 환경에 배포됩니다.

그리고 비즈니스 로직을 추가하여 조건부로 인프라를 생성하고 구성하는 코드를 넣으세요. 예를 들어:

- 마이크로서비스는 프로덕션을 위해 전용 리소스가 필요하지만 개발 또는 테스트 환경에서는 공유 리소스를 사용할 수 있습니다. 예: 클라우드 저장소
- 개발 및 테스트 환경을 위한 인프라는 꼭 고가용성이 필요 없습니다.

그러나 디렉터리 구조가 마이크로서비스 아키텍처를 반영하지 않아서 모든 필요한 리소스를 하나의 마이크로서비스에 번들로 제공하지 못했습니다. 이는 모두 동시에 프로비저닝하거나 폐기하는 것이 어려워져서 임시로 사용하지 않는 리소스를 남길 수 있는 가능성이 있습니다.

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

상기 내용 외에도 2018년에는 Heroku를 포기하고 배포 플랫폼으로 Kubernetes를 도입하기로 결정했습니다. Heroku가 우수한 선택이었지만, 회사가 성장하며 새로운 리소스 수요가 증가함에 따라 비용 부담이 커졌습니다. 또한 필요한 유연성과 자세한 모니터링 기능이 부족했습니다. 이 결정으로 Terraform을 사용하여 관리해야 할 인프라의 크기와 복잡성이 상당히 증가했습니다. 그 당시 우리는 간단한 구조가 확장 가능하지 않음을 깨닫고 IaC 아키텍처를 다시 고려해야 한다는 것을 알았습니다.

Terraform은 인프라를 관리하는 데 탁월한 도구이지만, 2018년 당시 제한이 있었습니다. 비즈니스 로직을 구현하는 것이 어려웠고, 설정 파일을 공유하는 것이 간단하지 않았으며, "환경별 애플리케이션"을 지원하기 위해 디렉터리 구조를 변경하는 것은 네이티브로 처리할 수 없었습니다. Terraform 모듈을 사용하더라도 각 모듈과 환경마다 상당한 양의 코드를 수동으로 중복해야 했기 때문에 우리의 설정은 DRY 원칙을 따르지 못하며 리소스를 묶어 함께 처리하는 문제는 해결되지 않았습니다.

지금은 CI/CD에 대해 생각하고 있을 수 있습니다. 당신의 의견을 이해합니다만, 아직 그 단계에는 이르지 못했습니다.

목표는 리소스 기반 구조에서 마이크로서비스 기반 구조로 전환하는 것이었지만, Terraform은 IaC를 재설계하는 데 필요한 주요 기능이 부족했습니다. 그때 Terragrunt가 등장했습니다.

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

# 2G IaC — Terragrunt으로 구조화된 Terraform 코드

Terragrunt은 Gruntwork에서 개발한 Terraform 래퍼로, 설정을 DRY 유지하고 여러 Terraform 모듈을 사용하며 원격 상태를 관리하는 추가 도구를 제공합니다.

Terragrunt을 사용하면 다음과 같은 작업을 수행할 수 있습니다:

- Terraform 코드를 DRY 상태로 유지
- 중복된 백엔드 코드 삭제
- 상위 디렉토리에서 설정 상속
- 한 번에 여러 모듈 적용

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

이를 통해 우리는 원하는 마이크로서비스 블루프린트를 만들고 IaC를 재구성할 수 있게 되었습니다.

이 단계에서는 코드의 디렉토리 구조를 재설계하여 우리의 인프라 구조에 맞추었습니다. 클라우드 공급업체나 서비스 제공자에 중립적인 디자인으로 조직화되어 있으며, 스코프의 명확한 격리를 가지고 있습니다.

- 조직, 예: 스테이징, 프로덕션 등
- 환경, 예: 테스트, 개발, 프로덕션 등
- 마이크로서비스

IaC를 재구성하고 보다 일관성 있게 만들기로 결정했기 때문에, 우리는 리소스에 대한 새로운 일관된 명명 규칙을 수립해야 했습니다. 이 명명 규칙은 Terraform 리소스와 실제 클라우드 인프라에 적용될 것이며, 각 환경을 최상위 추상화 수준으로 보고 리소스가 다른 환경 간에 공유되지 않을 것을 고려하여 Terraform 리소스 이름, 변수 이름 및 리소스 태그에 대한 명명 규칙 제안을 마련했습니다.

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

## 모듈

명확한 디렉토리 구조와 명명 규칙을 가지고 인프라를 모듈로 분할하여 아키텍처를 기반으로 인프라를 기술하는 것이 목표였습니다. Terraform의 최상의 관행을 따라, 각 마이크로서비스를 위한 리소스를 번들로 제공하고 조건부 프로비저닝 및 구성을 위한 비즈니스 로직을 통합하기 위해 재사용 가능한 모듈을 만들었습니다.

최종적으로, 디렉토리 구조는 다음과 같이 보이게 되었습니다:

modules/
├── README.md
└── organization
└── infrastructure
└── environments
├── gke
│ ├── firewall.tf
│ ├── iam.tf
│ ├── main.tf
│ ├── nodepools.tf
│ ├── providers.tf
│ ├── remote_state.tf
│ └── variables.tf
├── microservice1
│ ├── README.md
│ ├── iam.tf
│ ├── mongo.tf
│ ├── providers.tf
│ ├── s3.tf
│ └── variables.tf
└── microservice2
├── README.md
├── cloudfront.tf
├── iam.tf
├── iam_policy.json
├── postgres.tf
├── providers.tf
├── remote_state.tf
├── s3.tf
├── s3_policy.json
└── variables.tf

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

테라폼 모듈로 전환함으로써 필요한 추상화를 달성할 수 있었습니다. 그러나 각 모듈을 인스턴스화하고 입력 변수에 값을 설정하고 출력 변수를 정의하며, 공급자를 구성하고 원격 상태를 제공하는 코드는 여전히 많은 유지 보수 부담을 야기했습니다.

## 라이브

Terragrunt를 사용하여 코드의 추상화 수준을 추가하고 서로 다른 환경에서 코드의 버전화된, 변경 불가능한 artifact를 제공할 수 있었습니다. 이 도구는 일반적인 테라폼 코드에 존재하는 원격 테라폼 구성을 가져올 수 있으며, 환경 간에 다를 수 있는 값에 대해 입력 값을 요구합니다.

별도의 저장소에서 비슷한 디렉토리 구조를 따라 우리의 모든 환경에 대한 라이브 코드를 정의했습니다. 이제 라이브 코드는 단 3개의 파일로 이루어져 있습니다:

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

- [필수] 코드의 소스를 지정하는 Terragrunt .hcl 파일
- [필수] 리소스를 구성하는 데 필요한 키/값 쌍만 포함해야 하는 Terraform .auto.tfvars 파일
- [선택 사항] Terraform 비밀 .auto.tfvars 파일은 구성에 비밀을 유지하려는 경우에만 사용됩니다. 비밀을 안전하게 유지하기 위해 git-crypt를 사용하여 git 저장소에서 파일의 투명한 암호화 및 해독을 가능하게 합니다.

이 방법으로 모듈은 조직 및 환경에 구애받지 않으며, 구성 코드는 다른 환경 및 조직 간에 각 마이크로서비스마다 다를 것입니다. 최종적으로 우리의 라이브 구성은 다음과 같았습니다:

live/
├── production
│ ├── org_config.auto.tfvars
│ ├── production1
│ │ ├── env_config.auto.tfvars
│ │ ├── gke
│ │ │ ├── terragrunt.hcl
│ │ │ ├── variables.auto.tfvars
| | | └── secrets.auto.tfvars
│ │ ├── microservice1
│ │ │ ├── terragrunt.hcl
│ │ │ └── variables.auto.tfvars
│ │ └── microservice2
│ │ ├── terragrunt.hcl
│ │ └── variables.auto.tfvars
│ └── production2
│ ├── env_config.auto.tfvars
│ ├── gke
│ │ ├── terragrunt.hcl
│ │ ├── variables.auto.tfvars
| | └── secrets.auto.tfvars
│ ├── microservice1
│ │ ├── terragrunt.hcl
│ │ └── variables.auto.tfvars
│ └── microservice2
│ ├── terragrunt.hcl
│ └── variables.auto.tfvars
└── staging
├── dev
│ ├── env_config.auto.tfvars
│ ├── gke
│ │ ├── terragrunt.hcl
│ │ ├── variables.auto.tfvars
| | └── secrets.auto.tfvars
│ ├── microservice1
│ │ ├── terragrunt.hcl
│ │ └── variables.auto.tfvars
│ └── microservice2
│ ├── terragrunt.hcl
│ └── variables.auto.tfvars
├── org_config.auto.tfvars
└── qa
├── env_config.auto.tfvars
├── gke
│ ├── terragrunt.hcl
│ ├── variables.auto.tfvars
| └── secrets.auto.tfvars
├── microservice1
│ ├── terragrunt.hcl
│ └── variables.auto.tfvars
└── microservice2
├── terragrunt.hcl
└── variables.auto.tfvars

그러한 방법으로 코드 측면에서 모든 것이 갖추어졌고 대부분의 경우에 잘 작동했습니다. Terraform은 애플리케이션이 실행될 인프라를 생성하고 관리하는 도구입니다. 리소스와 그 사양을 선언적인 방식으로 정의하고, 의존성을 매핑하고, 모든 것을 구축하고, 심지어 현재 인프라와 원하는 최종 상태 간의 일치를 보장하기 위해 상태를 유지할 것입니다. 그러나, 소프트웨어에는 적용되지 않습니다. Terraform은 리소스 자체를 생성하는 데 사용되지만 실행 중인 소프트웨어를 관리하지는 않습니다.

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

쿠버네티스에서 대부분의 워크로드를 실행하고 일부는 가상 머신(VMs)에서 실행하면 많은 클러스터 및 개별 서버를 특정 소프트웨어 관점에서 일정 수준의 사용자 정의로 관리해야 했습니다. 예를 들어, GitOps 파이프라인(Flux)을 구축하고 클러스터의 네트워킹 기능을 확장하는 소프트웨어를 원하거나, VM에서 실행 중인 Redash 및 Airflow와 같은 주로 내부 도구와 같은 서비스를 부트스트랩하는 것이 목표였습니다.

여러 환경을 유지하기 위해서는 소프트웨어를 설치, 구성 및 관리하는 일련의 일관된, 신뢰할 수 있고 안전한 방법이 필요했습니다. 이것이 Ansible이 등장한 곳입니다. 일부 관리 되는 리소스용 Terraform 프로바이더가 그 때에 이미 사용 가능했지만, 팀의 역량 및 기존 Ansible에 대한 친숙함과 함께 특정 사용자 정의 작업, 주로 소프트웨어 설치를 위해 채택하는 결정을 내렸습니다.

Ansible

Ansible은 시스템을 구성하고 소프트웨어를 배포하며 연속 배포나 제로 다운타임 롤링 업데이트와 같은 보다 고급 IT 작업을 조정할 수 있는 IT 자동화 (IaC) 도구입니다. Ansible은 미리 정의된 단계 집합을 실행하고 원하는 최종 상태보다는 자동화 프로세스에 집중합니다.

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

테라폼과 앤서블은 상호 배타적이지 않지만 둘 다 인프라스트럭처 코드 (IaC)에 사용할 수 있는 도구들입니다. 테라폼은 선언적인 접근 방식을 따라 구성 파일에 기반하여 인프라스트럭처 리소스를 프로비저닝, 수정, 관리 및 파괴하는 데 이상적입니다. 한편, 앤서블은 주로 절차적인 방식을 따르는 구성 관리 도구로, 특정 단계가 특정 순서로 실행되어야 하는 상황에서 리소스를 구성하는 데 뛰어납니다. 예를 들어 소프트웨어 설치/업데이트, 런타임 환경 설정, 시스템 구성 파일 업데이트 등.

테라폼과 앤서블을 결합함으로써 새로운 인프라스트럭처를 구축하고 필요한 하드웨어와 소프트웨어를 구성하는 유연한 워크플로우를 만들었습니다. 우리는 테라폼의 로컬 실행자(provisioner)를 활용하여 모듈 내부에서 앤서블 플레이북을 실행하고, 테라폼 변수를 기반으로 플레이북을 사용자 정의하기 위해 템플릿을 사용했습니다. 이 하이브리드 접근 방식을 통해 워크로드의 의존성에 필요한 클러스터를 빠르게 구성할 수 있었습니다. 게다가, 플레이북이 모듈에 통합되어 있었기 때문에 모든 프로비저닝된 리소스가 동일한 방식으로 구성되도록 일관성을 확보할 수 있었습니다. 또한, 모든 환경에서 일관성을 유지함으로써 유지 관리와 문제 해결을 단순화했습니다… 또는 아니었을지도 모릅니다 :)

모든 것이 준비되어 좋은 시작이었지만, 우리가 작업을 완료할 쯤 다른 영역에서 비롯된 다른 유형의 제약 조건에 부딪히기 시작했습니다.

## 문제

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

WET 코드
우리의 대부분의 마이크로서비스는 RDS 인스턴스나 S3 버킷과 같은 다양한 종류의 리소스가 필요합니다. 그래서 우리는 리소스 기반에서 마이크로서비스 기반 설계로 전환하기로 결정했습니다. 그러나 동일한 유형의 리소스 정의를 서로 다른 모듈 간에 중복하는 실천은 WET 코드뿐만 아니라 구성에서의 일관성 부족을 만들어내기도 했습니다. 우리 모든 S3 버킷에 대해 데이터 암호화를 강제하는 등 변경 사항을 적용해야 하는 경우에 코드 일관성을 보장하기 위해 상당한 인지적 부담이 도입되었습니다.

하나의 모듈로 모두 통합
다양한 리소스를 재사용 가능한 모듈로 번들링하고 비즈니스 로직을 포함하는 것이 가야 였습니다(그리고 우리는 여전히 그것이 옳다고 믿습니다). 그러나 서로 다른 팀에서 자주 다른 환경에 대한 요구사항을 갖고 장비 제공 및 동일한 마이크로서비스의 구성을 관리하는 것은 모든 가능한 시나리오를 수용하기 위한 지루한 양의 비즈니스 로직을 유발했습니다.

예를 들어, 클라우드 스토리지와 그에 접근하기 위한 서비스 계정, 포스트그레SQL 데이터베이스 및 CDN이 필요한 단일 마이크로서비스를 고려해 봅시다. 일부 잠재적인 시나리오는 다음과 같습니다:

- QA 팀: 여러 번 파일을 업로드할 필요가 없도록 X 환경에 생성된 공유 버킷을 사용해야 함
- 개발 팀: 여러 출처를 유지 관리할 필요가 없도록 Y 환경에 생성된 공통 CDN을 사용해야 함
- 프로덕션 환경: 리소스 분리 및 격리가 필요함

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

앤서블을 실행하는 것이 무서워졌어요
우리의 인프라 구조 요구 사항이 증가하고 제품 아키텍처가 더 복잡해지면서, 앤서블에 의해 수행된 사용자 정의 및 구성이 점점 관리하기 어려워졌어요. 1000줄 이상의 단일 플레이북과 100개의 앤서블 태스크가 있는 상황에서는 제어하고 변경 사항을 추적하기 어려워졌습니다. 테라폼과 달리, 앤서블은 어떤 태스크가 리소스를 수정할지 여부를 명확하게 제공하지 않았어요. 특히 더 많은 중요한 소프트웨어가 앤서블로 관리되는 상황에서 프로덕션 환경에서 실행해야 할 때 마다 불안함을 가중시키기도 했죠.

그때 우리는 다음과 같은 결정을 내렸어요:

- 가능한 것을 테라폼 프로바이더로 이동시키기로 했어요: 쿠버네티스 및 헬름 오퍼레이터는 이 시점에서 더 성숙해졌으므로, kubectl 명령을 실행하거나 직접 헬름 차트를 설치하는 대신 이들을 사용하기 시작했어요.
- 쿠버네티스 서비스 설치를 테라폼 밖으로 이동하기로 결정했어요: 가능하고 적절한 경우, 쿠버네티스 서비스의 설치 프로세스를 Flux에 의해 관리되는 GitOps 워크플로로 이동했어요.
  새로운 환경, 일관성 및 자동화에 대해 궁금해할 수 있어요. 우리의 헬름 / Flux 설정은 별도의 기사가 필요한 지금, 대부분의 기능이 헬름 차트 방향으로 이동되었지만 이 서비스들에 대한 헬름 릴리스는 여전히 테라폼을 통해 관리되며, 테라폼을 통해 이 서비스에 대한 헬름 릴리스를 실행하고 있습니다.
- 남은 작업을 개별 플레이북으로 분해하기로 결정했어요: 관리를 단순화하고 유연성을 향상시키기 위해 남은 작업을 더 작고 명확한 작업을 수행하는 개별 플레이북으로 분할했기 때문에 이들을 필요에 따라 별도로 실행할 수 있어요.

문서화
모듈의 복잡성 증가는 이들을 이해, 업데이트 및 새로운 환경에서 리소스 프로비저닝에 사용하기 어렵게 만들었습니다. 기능을 명확히 파악하고 입력 변수를 구성할 필요가 있는 값을, 예상 결과를 파악하기 위한 오퐋이 미비하다는 사실이 점점 명백해졌습니다.

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

# 3G IaC — Terraform의 귀환

Terraform를 v1.x.x로 업그레이드 한 후, 우리는 현재 구조의 한계와 직면한 문제를 평가하기 시작할 좋은 위치에 있었습니다.

지금까지 사용해온 IaC의 마이크로서비스 기반 조직은 필요한 빌딩 블록을 만들고 필요한 비즈니스 로직을 통합하여 비즈니스 요구 사항을 충족하는 데 유익한 것으로 입증되었습니다. 이 방식은 새로운 마이크로서비스를 도입하거나 기존 마이크로서비스를 개선하는 데 용이했습니다. 예를 들어, 만약 마이크로서비스 X가 NoSQL 데이터베이스를 활용해야 한다면, 해당 모듈을 업데이트하여 Mongo 클러스터를 포함시키고 이 변경 사항을 마이크로서비스 X가 배포된 모든 환경에 적용하면 됩니다. 그러나 모든 마이크로서비스의 NoSQL 데이터베이스에 대해 수평적인 변경을 수행해야 하는 경우에는 점점 더 지루해지는 일이었습니다. 회사가 성장함에 따라 코드 기반이 점점 커지고 관리하기 어려워지며, 보다 복잡한 요구 사항을 구현하는 것이 어려워졌기 때문에 모든 위의 문제를 가능한 빨리 해결해야 했습니다.

우리의 현재 구조를 살펴보겠습니다:

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

<img src="/assets/img/2024-05-20-FrommanualtodeclarativeTerraformandIaCinafastgrowingcompany_1.png" />

## 코어 리소스로서의 자식 모듈

이전 문제를 해결하기 위해 우리는 자식 모듈의 사용을 도입하여 추상화의 추가 층을 더했습니다.

자식 모듈을 개발하기 시작하려면 일관성과 균일성을 보장하기 위한 규칙 세트를 확립해야 했습니다:

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

- Terraform 리소스를 항상 함께 배치해야 하는 번들로 구성합니다. 예를 들어, 파라미터가 있는 PostgreSQL RDS 인스턴스, 서브넷 및 보안 그룹입니다.
- 인프라 전역에 적용되는 기본 값을 설정하고 강제합니다. 예를 들어, S3 버킷은 공개 액세스 차단, HTTP 요청 거부 및 서버 측 암호화를 사용해야 합니다.
- 어떠한 비즈니스 로직도 포함해서는 안 됩니다.
  "비즈니스 논리"는 Terraform 리소스를 특정 환경 및 애플리케이션에 맞게 구성하는 방식 및 이러한 리소스를 번들로 묶는 방식을 가리킵니다. 모든 비즈니스 로직은 루트 모듈에 유지되어야 합니다.
- 하나의 제공업체를 대상으로 한 리소스가 포함됩니다.

하위 모듈에는 동일한 제공업체의 리소스만 포함되어야 합니다. 이것은 이러한 모듈에 따라 가야 할 파일 구조에서 더 잘 보여집니다.

```js
modules-terraform/
├── aiven
│  └── kafka
│     ├── README.md
│     ├── main.tf
│     ├── outputs.tf
│     ├── variables.tf
│     └── versions.tf
├── aws
│  └── db_instance
│     ├── README.md
│     ├── main.tf
│     ├── outputs.tf
│     ├── variables.tf
│     └── versions.tf
└── gcp
   └── storage_bucket
      ├── README.md
      ├── main.tf
      ├── outputs.tf
      ├── variables.tf
      └── versions.tf
```

자식 모듈은 더 포괄적인 자식 모듈을 만들기 위해 연결될 수 있습니다. 예를 들어, AWS RDS 인스턴스에 Postgres DB를 인스턴스화하는 경우, 모든 Postgres 인스턴스에 걸쳐 적용하는 표준 Postgres 구성이 있는 Postgres 자식 모듈을 갖습니다. 그럼에도 불구하고 인기 있는 RDS 구성 (로그 기록, SSL, 휴면 시 암호화 등)과 모든 표준 구성물을 포함하며 Postgres에 특정한 표준 구성 사항을 위해 Postgres 자식 모듈을 활용합니다.

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

그래서 새 구조에 하위 모듈이 추가된 것은 다음과 같이 표현될 수 있습니다:

![구조](/assets/img/2024-05-20-FrommanualtodeclarativeTerraformandIaCinafastgrowingcompany_2.png)

자원 번들링 및 비즈니스 로직을 위한 Terragrunt 루트 모듈

Terraform 루트 모듈은 여러 하위 모듈을 인스턴스화하고, 특정 마이크로서비스에 대한 모든 자원을 제공하며, 환경 및 마이크로서비스 요구사항에 따른 "비즈니스 로직"을 담당합니다.

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

예를 들어, 마이크로서비스를 위해 새로운 RDS 데이터베이스를 만들어야 한다고 가정해봅시다:

우리의 요구 사항:

- 각 환경/애플리케이션(비즈니스 로직)에 대한 네이밍 규칙 적용
  클라우드 인프라의 네이밍 규칙은 다음과 같습니다:
  `environment_name`-`service_name`-`resource_type`-`random_id`
- 인프라 전반에 표준 태그 적용 (비즈니스 로직)
  태그는 항상 이름, 제공자, 팀, 애플리케이션, 환경, 조직을 포함해야 합니다
- 원격 상태에서 VPC 보안 그룹 사용\*\* (비즈니스 로직)
- 한꺼번에 모든 것 생성 (Child 모듈)
  RDS PostgreSQL 인스턴스, SSL 적용을 강제하는 매개변수 그룹, 서브넷 그룹, 모든 아웃바운드 트래픽을 허용하는 보안 그룹을 만듭니다.
  이 경우 모든 것이 한 번에 처리될 것이며, 이 요구 사항은 항상 이러한 리소스를 함께 프로비저닝해야 한다는 것입니다. 심지어 SSL을 강제하지 않는 것을 허용하지 않는 엄격한 보안 요구 사항 같은 유효성 검사도 Child 모듈에서 처리될 것입니다.

```js
변수 "db_parameter_group_parameters" {
  description = "적용할 DB 매개변수 맵 목록"
  타입        = 목록(맵(문자열))
  기본값     = []

  유효성 {
    조건 = alltrue(
      [
        매개변수 중에 var.db_parameter_group_parameters :
        (
          !포함(["rds.force_ssl"], 매개변수["이름"])
        )
      ]
    )
    에러 메시지 = "force_ssl을 덮어쓸 수 없습니다."
  }
}
```

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

우리의 새로운 구조를 사용하면 이 작업을 단 40줄의 코드로 손쉽게 수행할 수 있습니다.

```js
locals {
  identifier = (var.microservice_pg_identifier == null
    ? "${var.env_name}-${var.microservice_name}-pg-${random_id.id.hex}"
    : var.microservice_pg_identifier
  )
  db_parameter_group_name = "${var.env_name}-${var.microservice_name}-postgres-${element(split(".", var.microservice_pg_engine_version), 0)}"
  db_subnet_group_name    = "${var.env_name}-${var.microservice_name}-${var.aws_region}-db-subnet"
  db_security_group_name  = "${var.env_name}-${var.microservice_name}-${var.aws_region}-db-sg"

  tags = {
    name        = local.identifier
    provisioner = "terraform"
    team        = var.team_name
    app         = var.microservice_name
    env         = var.env_name
    org         = var.org_name
  }
}

resource "random_id" "id" {
  byte_length = 2
}

module "microservice_pg" {
  source = "../../../../modules-terraform/aws/db_instance"

  identifier        = local.identifier
  engine_version    = var.microservice_pg_engine_version
  allocated_storage = var.microservice_pg_allocated_storage

  vpc_security_group_ids = [
    data.terraform_remote_state.vpc.outputs.postgres_security_group_production_id,
    data.terraform_remote_state.vpc.outputs.postgres_security_group_staging_id,
    data.terraform_remote_state.vpc.outputs.postgres_security_group_services_id,
  ]

  backup_retention_period = var.microservice_pg_backup_retention_period

  db_subnet_group_name       = local.db_subnet_group_name
  db_subnet_group_subnet_ids = data.terraform_remote_state.vpc.outputs.vpc_id

  db_parameter_group_name   = local.db_parameter_group_name
  db_parameter_group_family = var.microservice_pg_parameter_group_family

  db_security_group_name   = local.db_security_group_name
  db_security_group_vpc_id = data.terraform_remote_state.vpc.outputs.vpc_id

  tags = local.tags
}
```

실시간 설정

실제 설정에서는 Terragrunt를 사용하여 전역 구성 변수를 설정하고 프로바이더 버전을 생성하며, 인프라 전체에 걸쳐 화이트리스트로 지정된 IP를 조작합니다.

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

테라폼 프로바이더 버전 관리

테라폼 프로바이더 버전을 관리하기 위해 각 모듈마다 versions.tf 파일을 생성하는 데 Terragrunt를 사용합니다. 루트 Terragrunt 구성에서 생성 블록을 추가하고 로컬 블록에서 현재 사용 중인 프로바이더 버전이 포함된 YAML 파일을 디코딩합니다. 이를 통해 전체 모듈에 걸쳐 프로바이더 버전을 업데이트할 수 있습니다. 특정 모듈이 프로바이더의 다른 버전을 사용하길 원한다면, 해당 모듈의 라이브 구성에서 덮어쓸 수 있습니다.

루트 terragrunt.hcl:

```js
locals {
  provider_version = yamldecode(file("provider_versions.yaml"))
  [...]
}

generate "versions" {
  path      = "versions.tf"
  if_exists = "overwrite"
  contents  = <<EOF
  terraform {
    required_version = ">= 1.0"
    required_providers {
      aws = {
        source  = "hashicorp/aws"
        version = "${local.provider_version.aws}"
      }
      [...]
    }
  }
EOF
}
```

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

provider_versions.yaml:

```yaml
aws: "3.74.1" # [AWS Provider](https://registry.terraform.io/providers/hashicorp/aws/3.74.1)
google: "3.90.1" # [Google Cloud Provider](https://registry.terraform.io/providers/hashicorp/google/3.90.1)
[...]
```

What about Ansible?

As mentioned earlier, we have decided to simplify Ansible and migrate as much as possible to more suitable IaC or GitOps workflows. In our IaC code, we only use Ansible for:

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

- Istio 설치. 우리가 리펙터링을 진행한 당시, Istio의 생산 준비가 된 유일한 설치 방법은 istioctl이었으며, 여전히 istioctl에서 Helm으로 이동하려면 삭제 및 재설치가 필요하므로 우리의 프로덕션 환경에 신중히 계획해야 합니다.
- VM 구성 및 관리. Ansible, Chef, 그리고 Puppet과 같은 절차적 도구들이 아직도 VM 소프트웨어를 구성하고 유지하는 데 최적의 선택지입니다. Ansible의 장점 중 하나는 클라이언트 측에서 실행될 수 있으므로 VM이 구성 관리 도구에 액세스할 필요가 없다는 것입니다.

# CI

이 내용은 이 글의 범위를 벗어나므로 자세히 다루지 않겠습니다. 하지만 조만간...

IaC CI/CD 여정을 설명할 후속 기사가 곧 공개될 예정입니다.
