---
title: "테라폼 DevOps 안내서 보안 최상의 실천 방안"
description: ""
coverImage: "/assets/img/2024-05-17-TheGuidetoTerraformDevOpsSecurityBestPractices_0.png"
date: 2024-05-17 18:29
ogImage:
  url: /assets/img/2024-05-17-TheGuidetoTerraformDevOpsSecurityBestPractices_0.png
tag: Tech
originalTitle: "The Guide to Terraform DevOps: Security Best Practices"
link: "https://medium.com/towards-aws/the-guide-to-terraform-devops-security-best-practices-085ee46cfb3e"
---

<img src="/assets/img/2024-05-17-TheGuidetoTerraformDevOpsSecurityBestPractices_0.png" />

# 요약

전통적으로, 인프라 구축은 수동 구성을 포함하는 시간이 많이 소요되고 오류 발생 가능성이 있는 프로세스였습니다. Terraform은 코드로 인프라를 정의할 수 있도록 함으로써 이러한 관행을 바꿉니다. HashiCorp Configuration Language (HCL)로 작성된 코드는 필요한 리소스(서버, 네트워크, 데이터베이스)와 이들의 구성을 구체화합니다. 경험이 풍부한 실무자든 초보자든, 본 안내서는 현대적인 DevOps 방법론에 대한 Terraform의 패러다임 전환적인 영향에 대한 귀중한 통찰을 제공합니다.

# 목차

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

- Terraform DevOps 안내서: 보안 모범 사례
- 요약
- 목차
- 소개
- 전제 조건
  - 💡해결책💡
- Terraform 상태를 안전하게 유지 🫶
- 비밀 정보 안전하게 관리하기 🫶
- 역할 기반 액세스 제어 (RBAC) 구현
- 코드 리뷰와 협업 🫶
- CI/CD 파이프라인 구현 💣
- Terraform 모듈 및 제공업체 안전하게 사용하기 💣
- 로깅 및 모니터링 🫶
- 네트워크 보안 🫶
- 감사 및 규정 준수 👀
- 재해 복구 및 백업 👀
- 결론
- 내 정보
- 참고 문헌

# 소개

Terraform은 인프라스트럭처의 코드 (IaC)에 대한 필수 도구로, 여러 클라우드 제공업체 간에 자원을 통합적으로 관리할 수 있게 해줍니다. 그러나 큰 권한에는 큰 책임이 따릅니다. 어떠한 구성 오류도 전체 인프라스트럭처에 영향을 줄 수 있기 때문에 보안이 매우 중요합니다.

전통적으로 인프라스트럭처 프로비저닝은 수동 구성을 필요로 하며, 시간이 많이 걸리고 오류가 발생하기 쉬운 과정이었습니다. Terraform은 이에 도전하여 인프라스트럭처를 코드로 정의할 수 있도록 해주는 접근법을 제공합니다. HashiCorp Configuration Language (HCL)로 작성된 코드는 필요한 리소스 (서버, 네트워크, 데이터베이스)와 그 구성을 지정합니다.

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

이 기사는 Terraform의 이점에 대해 탐구하고 IaC 배포가 안전하고 신뢰할 수 있도록 보장하기 위한 필수 보안 모범 사례를 제공합니다. 우리는 다음과 같은 주요 영역을 탐색할 것입니다:

💣 취약점 감사: Terraform 구성에서 취약성을 예방적으로 식별하고 제거하는 방법을 배웁니다.

💣 접근 제어 모범 사례: 접근 자격 증명을 안전하게 관리하고 최소 권한 원칙을 시행하는 전략을 발견합니다.

💣 안전한 Terraform 모듈: 모듈을 안전하게 활용하고 보안을 염두에 두고 자체 모듈을 생성하는 방법을 이해합니다.

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

"IBM와 HashiCorp의 병합된 포트폴리오는 고객이 증가하는 애플리케이션 및 인프라 복잡성을 관리하고 AI 시대를 위해 설계된 포괄적인 하이브리드 클라우드 플랫폼을 만들 수 있도록 도와줍니다." - IBM 회장이자 최고 경영자인 아빈드 크리슈나가 말했습니다.

# 준비 사항

우리가 본격적인 작업에 들어가기 전에, 먼저 로컬 머신이나 개발 서버에 필요한 서비스가 있는지 확인해야 합니다:

- React 및 Terraform에 대한 기본 지식.
- AWS 계정
- GitHub 계정
- AWS CLI 설치 및 구성 완료.
- 로컬에 Docker 설치.
- Terraform

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

## 💡 솔루션 💡

데브옵스 환경에서 Terraform을 구현하려면 인프라스트럭처 코드(IaC) 방법론이 취약점을 도입하지 않도록 보안에 중점을 두어야 합니다.

<img src="/assets/img/2024-05-17-TheGuidetoTerraformDevOpsSecurityBestPractices_1.png" />

다음은 Terraform을 사용할 때 보안을 유지하기 위한 모베스트 프랙티스입니다:

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

# 테라폼 상태 보안을 강화하세요 🫶

- 원격 상태 저장소 사용: 상태 파일을 AWS S3, Azure Blob Storage 또는 Google Cloud Storage와 같은 원격 백엔드에 저장하여 무단 액세스를 방지하는 적절한 액세스 제어를 적용하세요.
- 상태 파일 암호화: 원격 백엔드에 저장된 상태 파일에 대해 서버 측 암호화를 활성화하세요.
- 액세스 제한: IAM 역할 및 정책을 사용하여 상태 파일에 대한 액세스를 필요한 사용자에게로 제한하세요.

예시:

```js
variable "db_password" {
  type    = string
  sensitive = true
}

data "terraform_remote_state" "foo" {
  backend = "http"
  config = {
    address = "http://my.rest.api.com"
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

여기에서 더 많은 정보를 얻을 수 있습니다:

# **보안적으로 비밀을 관리하기 🫶**

- **비밀 직접 코딩 피하기**: Terraform 파일에 비밀번호, API 키 또는 기밀 정보와 같은 중요한 정보를 직접 코딩하지 마세요.
- **시크릿 관리 도구 사용하기**: HashiCorp Vault, AWS Secrets Manager 또는 Azure Key Vault와 같은 시크릿 관리 도구를 통합하여 Terraform 구성에 안전하게 시크릿을 관리하고 주입하세요.

예를 들어 Azure Key Vault를 사용하여 비밀을 안전하게 관리하는 방법:

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

provider.tf

```js
terraform {
  required_version = ">=1.0"
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~>3.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "~>3.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

main.tf

```js
resource "random_string" "secret_value" {
  length  = 20
  special = true
}

resource "azurerm_key_vault" "vault" {
  name                       = "my-key-vault"
  location                   = "East US"
  resource_group_name        = "my-resource-group"
  tenant_id                  = data.azurerm_client_config.current.tenant_id
  sku_name                   = "standard"
  soft_delete_retention_days = 7

  access_policy {
    tenant_id = data.azurerm_client_config.current.tenant_id
    object_id = local.current_user_id
    secret_permissions = ["get", "list"]
  }
}

resource "azurerm_key_vault_secret" "my_secret" {
  name         = "my-secret"
  value        = random_string.secret_value.result
  key_vault_id = azurerm_key_vault.vault.id
}

data "azurerm_client_config" "current" {}
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

# Role-Based Access Control (RBAC) 구현

- IAM 정책 사용: 사용자 및 서비스가 필요한 권한만 갖도록 IAM 정책을 정의하고 강제합니다.
- 역할 분리: 각 단계 (개발, 스테이징, 프로덕션)에 대해 서로 다른 역할을 사용하고 최소 권한의 원칙을 적용합니다.

예를 들어 Azure 리소스를 위한 Role-Based Access Control (RBAC) :

```js
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~>2.0"
    }
    azuread = {
      source = "hashicorp/azuread"
    }
  }
}

provider "azurerm" {
  features {}
}

// main.tf
data "azuread_user" "aad_user" {
  for_each            = toset(var.avd_users)
  user_principal_name = format("%s", each.key)
}

data "azurerm_role_definition" "role" {
  name = "Desktop Virtualization User"
}

resource "azuread_group" "aad_group" {
  display_name     = var.aad_group_name
  security_enabled = true
}

resource "azuread_group_member" "aad_group_member" {
  for_each         = data.azuread_user.aad_user
  group_object_id  = azuread_group.aad_group.id
  member_object_id = each.value["id"]
}

resource "azurerm_role_assignment" "role" {
  scope              = azurerm_virtual_desktop_application_group.dag.id
  role_definition_id = data.azurerm_role_definition.role.id
  principal_id       = azuread_group.aad_group.id
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

# 코드 리뷰 및 협업 🫶

- 버전 관리 사용: 테라폼 구성을 Git과 같은 버전 관리 시스템(VCS)에 저장하세요.
- 코드 리뷰: 잠재적인 보안 문제를 미리 발견하기 위해 테라폼 구성에 대한 모든 변경에 대해 필수 코드 리뷰를 실시하세요.
- 풀 리퀘스트: 변경 사항을 본 브랜치로 병합하기 전에 리뷰 및 토론하기 위해 풀 리퀘스트를 사용하세요.

# CI/CD 파이프라인 구현 💣

```js
name: 테라폼 CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

env:
  ARM_CLIENT_ID: ${ secrets.ARM_CLIENT_ID }
  ARM_CLIENT_SECRET: ${ secrets.ARM_CLIENT_SECRET }
  ARM_SUBSCRIPTION_ID: ${ secrets.ARM_SUBSCRIPTION_ID }
  ARM_TENANT_ID: ${ secrets.ARM_TENANT_ID }
  TF_VAR_client_id: ${ secrets.ARM_CLIENT_ID }
  TF_VAR_client_secret: ${ secrets.ARM_CLIENT_SECRET }
  TF_VAR_subscription_id: ${ secrets.ARM_SUBSCRIPTION_ID }
  TF_VAR_tenant_id: ${ secrets.ARM_TENANT_ID }

jobs:
  terraform:
    name: '테라폼'
    runs-on: ubuntu-latest

    steps:
      - name: 코드 체크아웃
        uses: actions/checkout@v2

      - name: 테라폼 설정
        uses: hashicorp/setup-terraform@v2
        with:
          terraform_version: 1.3.7

      - name: 테라폼 초기화
        run: terraform init

      - name: 테라폼 포맷
        run: terraform fmt -check

      - name: 테라폼 유효성 검사
        run: terraform validate

      - name: 테라폼 계획
        id: plan
        run: terraform plan -out=plan.tfplan
        continue-on-error: true

      - name: 테라폼 적용
        if: github.ref == 'refs/heads/main' && github.event_name == 'push'
        run: terraform apply -auto-approve plan.tfplan

      - name: 테라폼 삭제 (옵션)
        if: github.event_name == 'pull_request' && github.event.action == 'closed' && github.event.pull_request.merged == false
        run: terraform destroy -auto-approve
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

- 배포 자동화: CI/CD 파이프라인을 사용하여 Terraform 배포를 자동화하여 일관성을 유지하고 인간 에러의 위험을 줄입니다.
- 취약점 검사: CI/CD 파이프라인에 보안 스캔 도구를 통합하여 배포 전에 Terraform 구성에서 취약점을 감지합니다.

# Terraform 모듈과 프로바이더 안전하게 사용하기 💣

- 프로바이더 확인: Terraform 레지스트리 또는 신뢰할 수 있는 소스에서 확인된 프로바이더와 모듈만 사용합니다.
- 버전 핀: 프로바이더와 모듈의 버전을 핀하여 상위 업데이트로 인한 의도하지 않은 변경을 방지합니다.
- 코드 검토: 모듈과 프로바이더의 코드를 정기적으로 검토하고 감사하여 보안 위험을 방지합니다.

예시:

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

```js
module "network" {
  source  = "terraform-azure-modules/network/azurerm"
  version = "2.0.0"
  # 기타 모듈 입력 값
}
```

# 로깅 및 모니터링 🫶

- 로깅 활성화: 테라폼에서 수행되는 모든 작업이 로깅되고, 이러한 로그가 이상 활동을 모니터링하는 데 사용될 수 있도록 합니다.
- 상태 변경 모니터링: 테라폼 상태 및 인프라 변경에 대한 경보를 설정하여 무단 수정에 신속하게 대응할 수 있도록 합니다.

# 네트워크 보안 🫶

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

- VPC 및 서브넷 사용: 가상 사설 클라우드(VPC) 및 서브넷을 이용하여 리소스를 격리하도록 네트워크 아키텍처를 설계하세요.
- 보안 그룹 및 방화벽: 엄격한 보안 그룹 규칙과 네트워크 방화벽 정책을 적용하여 트래픽 흐름을 제어하고 노출을 제한하세요.

# 감사 및 규정 준수 👀

- 정기 감사: Terraform 구성 및 배포된 인프라의 보안 감사와 규정 준수 점검을 정기적으로 수행하세요.
- 규정 준수 코드: 규정 준수를 보장하기 위해 Terraform 구성에 규정 준수 점검을 통합하세요. regulatory and organizational standards을 준수합니다.

# 재해 복구 및 백업 👀

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

```js
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}

resource "azurerm_recovery_services_vault" "recovery_vault" {
  name                = "myRecoveryServicesVault"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  sku                 = "Standard"
}

resource "azurerm_backup_policy_vm" "backup_policy" {
  name                = "myBackupPolicy"
  resource_group_name = azurerm_resource_group.rg.name
  recovery_vault_name = azurerm_recovery_services_vault.recovery_vault.name

  backup {
    frequency = "Daily"
    time      = "23:00"
  }

  retention_daily {
    count = 7
  }
}

resource "azurerm_kubernetes_cluster" "aks" {
  name                = var.aks_name
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  dns_prefix          = var.dns_prefix

  default_node_pool {
    name       = "default"
    node_count = var.node_count
    vm_size    = var.vm_size
  }

  identity {
    type = "SystemAssigned"
  }

  tags = {
    environment = "Production"
  }
}

resource "azurerm_log_analytics_workspace" "log_analytics" {
  name                = var.log_analytics_workspace_name
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "PerGB2018"
  retention_in_days   = 30
}

resource "azurerm_kubernetes_cluster_node_pool" "node_pool" {
  name                  = "additionalpool"
  kubernetes_cluster_id = azurerm_kubernetes_cluster.aks.id
  vm_size               = "Standard_DS2_v2"
  node_count            = 1
}

resource "azurerm_backup_protected_vm" "protected_vm" {
  resource_group_name = azurerm_resource_group.rg.name
  recovery_vault_name = azurerm_recovery_services_vault.recovery_vault.name
  source_vm_id        = azurerm_kubernetes_cluster.aks.id
  backup_policy_id    = azurerm_backup_policy_vm.backup_policy.id
}
```

- 백업 상태 파일: 우연한 삭제나 손상으로부터 회복할 수 있도록 Terraform 상태 파일을 정기적으로 백업합니다.
- 재해 복구 계획: 재해 복구 계획을 개발하고 테스트하여 치명적인 장애 발생 시 Terraform을 사용하여 인프라를 복원할 수 있도록 합니다.

이런 모범 사례를 따르면 Terraform으로 관리되는 인프라의 보안을 크게 향상시키고 잠재적인 취약점과 잘못된 구성의 위험을 줄일 수 있습니다.

고려해야 할 몇 가지 부가 사항이 있습니다:

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

- 데이터의 존속중 암호화 #1 — 서버 측 암호화는 데이터를 적절히 보호하지만, 특히 환경 간 전송 시 상태 파일을 암호화하는 것을 고려해보세요. 👑
- 서비스 계정에 대한 최소 권한 부여 #2 — Terraform과 서비스 계정을 사용할 때, 해당 계정이 특정 작업에 필요한 최소한의 권한을 갖도록하십시오.
- 인프라 제거 테스트 #3 — 자동화된 테스트를 CI/CD 파이프라인에 포함하여 Terraform이 인프라를 안전하게 제거할 수 있는지 확인하세요. 재해 복구 시나리오에 중요합니다. 👑
- 최신 유지 #3 — 정기적으로 Terraform 자체, 모듈 및 프로바이더를 업데이트하여 보안 패치와 버그 수정의 이점을 누리세요.

# 결론

DevOps 환경에서 Terraform을 보안하는 데 이러한 모범 사례를 따르면 리스크를 최소화하고 인프라를 코드로 제어하는 강력한 방어수단을 확보할 수 있습니다. 안전한 상태 관리, 적절한 비밀 처리, 역할 기반 액세스 제어, 철저한 코드 리뷰, 자동화된 CI/CD 파이프라인, 주시적인 로깅 및 모니터링, 엄격한 감사 및 규정 준수 프로세스 등의 조치를 시행하여 취약점에 대응하는 인프라를 강화할 수 있습니다. 궁극적으로 보안에 대한 선제적인 접근은 시스템을 안전하게 지키는 데 도움이 되는 것뿐만 아니라 조직 내 신뢰와 신뢰성을 육성할 것입니다.

읽어 주셔서 감사합니다! 🙌🏻 구독하고 CLAP을 꼭 눌러주세요 👏 다음 기사에서 만나요.🤘

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

# 나에 대해

“안녕하세요! 저는 조엘 오웜보이라고 합니다. AWS 인증 클라우드 아키텍트, 백엔드 개발자, 그리고 AWS 커뮤니티 빌더입니다. 필리핀에 거주하고 있어요. 제가 가지고 있는 강점은 클라우드 아키텍처, 데브옵스 실천 방법, 그리고 고가용성 (HA) 원칙에 대한 깊은 이해를 결합한 것입니다. 제 지식을 활용하여 효율적인 기업 배포를 위해 오픈 소스 도구를 사용하여 견고하고 확장 가능한 클라우드 애플리케이션을 만들어내고 있습니다.”

저자(Joel O. Wembo)에 대한 더 많은 정보를 원하시면 아래 링크를 확인해주세요:

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

- Linkedin: [https://www.linkedin.com/in/joelotepawembo/](https://www.linkedin.com/in/joelotepawembo/)
- Website: [https://joelwembo.com](https://joelwembo.com)
- Twitter: [https://twitter.com/joelwembo1](https://twitter.com/joelwembo1)
- GitHub: [https://github.com/joelwembo](https://github.com/joelwembo)
- Portfolio: [joelwembo.github.io](joelwembo.github.io)
- [https://www.patreon.com/joelwembo](https://www.patreon.com/joelwembo)

# References
