---
title: "AKS 패칭 자동화 및 Terraform과 Logic Apps를 사용하여 Slack 알림 받기 파트 1"
description: ""
coverImage: "/assets/img/2024-06-19-AutomateAKSPatchingandGetSlackNotificationswithTerraformandLogicAppsPart1_0.png"
date: 2024-06-19 13:15
ogImage: 
  url: /assets/img/2024-06-19-AutomateAKSPatchingandGetSlackNotificationswithTerraformandLogicAppsPart1_0.png
tag: Tech
originalTitle: "Automate AKS Patching and Get Slack Notifications with Terraform and Logic Apps (Part 1)"
link: "https://medium.com/@sahayagodson/automate-aks-patching-and-get-slack-notifications-with-terraform-and-logic-apps-part-1-d5cb2a5ac2d5"
---


AKS 완전 자동 패칭 소개: 최신 패치와 업데이트로 Azure Kubernetes 서비스 (AKS) 클러스터를 최신 상태로 유지하는 것은 보안, 안정성 및 성능을 유지하는 데 중요합니다. 패치되지 않은 클러스터는 알려진 보안 취약점, 버그 및 성능 문제에 취약해지며, 다운타임, 데이터 침해 및 기타 비용 소모적인 결과로 이어질 수 있습니다.

그러나 AKS 클러스터를 수동으로 패칭하는 것은 특히 여러 클러스터가 있는 대규모 환경에서 시간이 많이 소요되고 오류가 발생하기 쉬운 과정일 수 있습니다. 수동으로 패칭하려면 유지 보수 창을 조정하고 패칭 프로세스를 모니터링하며 모든 클러스터가 중요한 작업 부하를 방해하지 않고 성공적으로 업데이트되도록 보장해야 합니다.

![AKS Patching](/assets/img/2024-06-19-AutomateAKSPatchingandGetSlackNotificationswithTerraformandLogicAppsPart1_0.png)

Terraform 및 Azure의 유지 보수 창 기능을 활용하여 AKS 패칭 프로세스를 간편하게 진행하고 클러스터가 최신 상태로 안정하게 유지되도록 할 수 있습니다. 설정 작업이나 다운타임을 최소화하면서 클러스터를 업데이트할 수 있습니다.

<div class="content-ad"></div>

# 사전 준비 사항 (초보자용)

AKS 패치 자동화를 위해 Terraform을 사용하여 시작하려면 다음 사전 준비 사항(기본 사항)이 필요합니다:

- Azure 구독: Azure 구독이 활성화되어 있어야 합니다. Azure 클라우드 플랫폼 내에서 리소스를 생성하고 관리할 수 있습니다.
- Terraform 설치: Terraform은 선언적 구성 파일을 사용하여 클라우드 리소스를 정의하고 제공할 수 있도록 해주는 오픈 소스 인프라 코드(IaC) 도구입니다. 로컬 머신이나 빌드 서버에 Terraform이 설치되어 있어야 합니다.
- Azure CLI 설치 및 인증: Azure CLI는 Azure 서비스와 상호 작용할 수 있는 명령줄 인터페이스입니다. Azure CLI를 설치하고 Azure 구독과 인증해야 합니다. 이를 통해 Terraform이 Azure와 인증하고 필요한 작업을 수행할 수 있습니다.

이러한 사전 준비 사항을 갖추었다면, Terraform 코드를 구성하여 AKS 패치 자동화를 진행할 수 있습니다.

<div class="content-ad"></div>

# Azure Provider 구성하기 (초보자용)

우선, Terraform을 위해 Azure Provider를 구성해야 합니다. 이를 통해 Terraform에게 어떤 공급자(provider)를 사용할지 알려주고 선택적 기능을 활성화합니다.

```js
provider "azurerm" {
  features {}
}
```

features 블록을 사용하여 공급자의 동작을 사용하거나 비활성화하여 사용자 정의할 수 있습니다. 이 경우 비워 두었지만 필요에 따라 여기에 기능 플래그를 추가할 수 있습니다.

<div class="content-ad"></div>

Azure Provider를 구성함으로써 Terraform은 Azure 리소스를 사용할 때 Azure Resource Manager(azurerm) 제공자를 사용하도록 알고 있습니다. 이 제공자를 통해 Terraform은 Azure 구독 내에서 리소스를 생성, 관리 및 업데이트할 수 있습니다.

# 리소스 그룹 생성 (초보자용)

다음으로 AKS 클러스터와 관련 리소스를 포함하는 Azure 리소스 그룹을 생성합니다. 우리는 리소스 그룹의 이름과 위치를 지정합니다.

```js
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}
```

<div class="content-ad"></div>

이 블록에서는 azurerm_resource_group 리소스를 사용하여 새로운 Azure 리소스 그룹을 정의합니다. "name" 인수는 리소스 그룹의 이름을 "example-resources"로 지정합니다. "location" 인수는 리소스 그룹이 생성될 Azure 지역을 설정하며, 이 경우 "West Europe"입니다.

전용 리소그룹을 생성함으로써 AKS 클러스터 배포에 관련된 모든 리소스를 논리적으로 그룹화하고 관리할 수 있습니다. 이는 깨끗하고 조직적인 Azure 환경을 유지하고 이 프로젝트와 관련된 리소스를 관리하고 모니터링하기 쉽게 만들어줍니다.

# AKS 클러스터 정의

이제, azurerm_kubernetes_cluster 리소스를 사용하여 AKS 클러스터를 정의해 봅시다. 클러스터 이름, 위치, 리소스 그룹, DNS 접두사, 기본 노드 풀 및 시스템 자동 할당 관리 ID와 같은 필요한 구성을 제공합니다.

<div class="content-ad"></div>

```js
resource "azurerm_kubernetes_cluster" "example" {
  name                = "example-aks1"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  dns_prefix          = "exampleaks1"
  default_node_pool {
    name       = "default"
    node_count = 1
    vm_size    = "Standard_D2_v2"
  }
  identity {
    type = "SystemAssigned"
  }
  tags = {
    Environment = "Production"
  }
  # ... (maintenance window configuration)
}
```

이 블록에서는 AKS 클러스터의 이름을 example-aks1로 지정했습니다. 위치와 resource_group_name 속성은 이전에 만든 리소스 그룹을 참조하여 클러스터가 원하는 위치와 리소스 그룹에 배포되도록 합니다.

dns_prefix는 클러스터에 대한 고유한 DNS 접두어로, 클러스터의 완전한 도메인 이름을 만들기 위해 필요합니다.

default_node_pool 블록은 클러스터의 기본 노드 풀 구성을 정의합니다. 노드 풀의 이름을 default로 설정하고, 단일 노드를 지정하고(노드 수 = 1), Standard_D2_v2 가상 머신 크기를 선택합니다.

<div class="content-ad"></div>

아이덴티티 블록은 AKS 클러스터에 시스템 지정 관리되는 아이덴티티를 사용하려는 것을 나타냅니다. 이 관리되는 아이덴티티는 클러스터가 Azure Container Registry와 같은 다른 Azure 리소스에 액세스할 수 있도록 사용할 수 있습니다.

마지막으로 우리는 클러스터 리소스에 키가 "환경"이고 값이 "Production"인 태그를 추가합니다.

# 유지보수 창구 관리

AKS 패치를 자동화하기 위해 AKS 클러스터 리소스 내에서 maintenance_window_auto_upgrade 및 maintenance_window_node_os 블록을 활용할 것입니다. 이러한 블록을 사용하여 자동 업그레이드 및 노드 OS 업그레이드를 위한 주기, 간격, 월별 일, 지속 시간, 시작 시간, UTC 오프셋 및 시작 날짜를 정의할 수 있습니다.

<div class="content-ad"></div>

우리는 var.automatic_channel_upgrade 및 var.node_os_channel_upgrade 변수의 값에 따라 조건에 따라 유지 관리 창 구성을 동적 블록을 사용하여 포함합니다.

maintenance_window_auto_upgrade 블록은 Kubernetes 클러스터 자체의 자동 업그레이드 설정을 구성하는 데 사용됩니다. 여기에서는 업그레이드 채널 (패치, 빠른, 노드 이미지 또는 안정), 업그레이드 빈도 (주간, 월간 등), 업그레이드할 달의 날짜, 유지 관리 창의 기간, 시작 시간, UTC 오프셋 및 시작 날짜를 지정할 수 있습니다.

```js
dynamic "maintenance_window_auto_upgrade" {
  for_each = var.automatic_channel_upgrade != "none" ? [1] : []
  content {
    frequency     = var.auto_upgrade_frequency
    interval      = var.auto_upgrade_interval
    day_of_month  = var.auto_upgrade_day_of_month
    duration      = var.auto_upgrade_duration
    start_time    = var.auto_upgrade_start_time
    utc_offset    = var.auto_upgrade_utc_offset
    start_date    = var.auto_upgrade_start_date
  }
}
```

마찬가지로, maintenance_window_node_os 블록은 노드 OS 이미지의 업그레이드 설정을 구성하는 데 사용됩니다. 업그레이드 채널 (Unmanaged, SecurityPatch, NodeImage 또는 None), 빈도, 달의 날짜, 지속 시간, 시작 시간, UTC 오프셋 및 시작 날짜를 지정할 수 있습니다.

<div class="content-ad"></div>

```json
dynamic "maintenance_window_node_os" {
  for_each = var.node_os_channel_upgrade != "None" ? [1] : []
  content {
    frequency     = var.node_os_upgrade_frequency
    interval      = var.node_os_upgrade_interval
    day_of_month  = var.node_os_upgrade_day_of_month
    duration      = var.node_os_upgrade_duration
    start_time    = var.node_os_upgrade_start_time
    utc_offset    = var.node_os_upgrade_utc_offset
    start_date    = var.node_os_upgrade_start_date
  }
}
```

이 유지 보수 창을 구성함으로써, 우리는 지정된 일정 및 환경 설정에 따라 AKS 클러스터 및 노드 OS 이미지가 자동으로 패치되고 업데이트되도록 보장할 수 있습니다. 이를 통해 수동 개입이 줄어들고 전체적인 보안과 안전성이 향상됩니다.

# 변수 정의

Terraform 구성을 더 유연하고 재사용 가능하게 만들기 위해 유지 보수 창 설정에 대한 변수를 정의합니다. 이러한 변수는 업그레이드 채널, 빈도, 기간, 시작 시간 및 기타 매개변수를 사용자 정의할 수 있도록 합니다.
```

<div class="content-ad"></div>

다음과 같이 변수를 정의합니다:

```js
변수 "automatic_channel_upgrade" {
    유형        = 문자열
    설명 = "이 Kubernetes 클러스터의 업그레이드 채널입니다. patch, rapid, node-image, stable 중에서 선택할 수 있습니다. 이 필드를 생략하면 해당 값은 없음으로 설정됩니다."
    기본값     = "rapid"
유효성 검사 {
        조건     = (var.automatic_channel_upgrade == null ? true : contains(["patch", "rapid", "node-image", "stable"], var.automatic_channel_upgrade))
        오류 메시지 = "반드시 'patch', 'rapid', 'node-image', 'stable' 또는 null이어야 합니다. 기본값: null."
    }
}

변수 "node_os_channel_upgrade" {
    유형        = string
    설명 = "이 Kubernetes 클러스터 노드의 OS 이미지의 업그레이드 채널입니다. Unmanaged, SecurityPatch, NodeImage, None 중에서 선택할 수 있습니다."
    기본값     = "Unmanaged"
}
```

이러한 변수들은 Kubernetes 클러스터 및 노드 OS 이미지의 업그레이드 채널을 제어합니다. automatic_channel_upgrade 변수를 사용하면 클러스터의 업그레이드 채널을 지정할 수 있으며, patch, rapid, node-image, stable과 같은 옵션이 있습니다. node_os_channel_upgrade 변수는 노드 OS 이미지의 업그레이드 채널을 제어하며, Unmanaged, SecurityPatch, NodeImage, None과 같은 옵션을 갖습니다.

또한 유지 관리 창 설정을 구성하는 변수들을 정의합니다:

<div class="content-ad"></div>

```js
variable "auto_upgrade_frequency" {
    description = "유지 관리 빈도입니다. 가능한 옵션은 주간, 절대월간 및 상대월간입니다."
    default     = "AbsoluteMonthly"
}

variable "auto_upgrade_day_of_month" {
    description = "유지 보수 실행을 위한 달의 날짜입니다. 절대월간 빈도와 함께 필요합니다. 0부터 31 사이의 값(포함)입니다."
    default     = "9"
}
variable "auto_upgrade_duration" {
    description = "유지 관리가 실행되는 창의 기간입니다."
    default     = "4"
}
variable "auto_upgrade_start_time" {
    description = "유지 보수가 시작되는 시간입니다. utc_offset에 따라 결정된 시간대를 기준으로합니다. 형식은 HH:mm입니다."
    default     = "12:33"
}
variable "auto_upgrade_utc_offset" {
    description = "클러스터 유지 관리를 위한 시간대를 결정하는데 사용됩니다."
    default     = "+00:00"
}
variable "auto_upgrade_start_date" {
    description = "유지 보수 창이 효력을 발생하는 날짜입니다."
    default     = "2024-05-09T00:00:00Z"
}
variable "auto_upgrade_interval" {
    description = "유지 보수 실행 간격입니다. 주간 또는 월간 기반으로이 간격이 지정됩니다."
    default     = "1"
}
```

이러한 변수들은 Kubernetes 클러스터의 유지 관리 창에 대한 여러 측면을 제어합니다. 주기(주간, 월간), 달의 날짜, 기간, 시작 시간, UTC 오프셋, 시작 날짜 및 간격과 같은 것이 포함됩니다.

노드 OS 유지 관리 창에 대해 유사한 변수가 정의되어 있습니다:

```js
variable "node_os_upgrade_frequency" {...}
variable "node_os_upgrade_day_of_month" {...}
variable "node_os_upgrade_duration" {...}
variable "node_os_upgrade_start_time" {...}
variable "node_os_upgrade_utc_offset" {...}
variable "node_os_upgrade_start_date" {...}
variable "node_os_upgrade_interval" {...}
```

<div class="content-ad"></div>

이러한 변수를 정의함으로써, 쿠버네티스 클러스터와 노드 OS 이미지의 유지 보수 창 설정을 쉽게 사용자 정의하고 조정할 수 있습니다. 이는 우리의 테라폼 구성을 다양한 환경과 요구 사항에 걸쳐 더 유연하고 재사용 가능하게 만들어 줍니다.

# 출력 설정 구성

AKS 클러스터에 대한 중요 정보를 검색하기 위해 출력 블록을 정의합니다. 이러한 출력을 사용하면 클라이언트 인증서 및 원시 쿠버네티스 구성에 액세스할 수 있습니다.

```js
output "client_certificate" {
  value     = azurerm_kubernetes_cluster.example.kube_config[0].client_certificate
  sensitive = true
}

output "kube_config" {
  value     = azurerm_kubernetes_cluster.example.kube_config_raw
  sensitive = true
}
```

<div class="content-ad"></div>

client_certificate 출력은 Kubernetes 클러스터와 인증하는 데 필요한 클라이언트 인증서를 제공합니다. Terraform이 sensitive = true로 표시함으로써, 해당 값이 계획 출력에 표시되지 않거나 상태 파일에 저장되지 않도록 보장합니다.

kube_config_raw 출력에는 클러스터 엔드포인트, 클라이언트 인증서 및 클러스터에 연결하는 데 필요한 기타 정보가 포함되어 있습니다. 이 출력도 민감한 데이터를 보호하기 위해 sensitive로 표시됩니다.

이러한 출력을 정의함으로써, AKS 클러스터의 클라이언트 인증서 및 Kubernetes 구성에 쉽게 액세스할 수 있습니다. 이를 통해 컨테이너 애플리케이션을 안전하게 연결하고 관리할 수 있습니다.

# 경보 및 작업 그룹 설정하기

<div class="content-ad"></div>

AKS 클러스터의 생성 또는 업데이트 작업을 모니터링하기 위해, azurerm_monitor_activity_log_alert 리소스를 사용하여 Azure Monitor Activity Log Alert를 설정했습니다. 이 경고는 AKS 클러스터의 생성 또는 업데이트 작업이 시작될 때 트리거되어 진행 중인 변경 사항을 추적할 수 있도록 합니다.

```js
resource "azurerm_monitor_activity_log_alert" "aks-cluster-activity-log-alert" {
  name                = "${azurerm_resource_group.example.name}-aks-activity-Started-alert"
  resource_group_name = azurerm_resource_group.example.name
  scopes              = [azurerm_kubernetes_cluster.example.id]
  description         = "이 경고는 시작된 Azure Kubernetes Service (AKS) 클러스터의 생성 또는 업데이트 작업을 모니터링합니다. AKS 클러스터의 생성 또는 업데이트 작업이 시작될 때 트리거되어 진행 중인 변경 사항을 추적할 수 있습니다."
  enabled             = true
  criteria {
    category           = "Administrative"
    level              = "Informational"
    operation_name     = "Microsoft.ContainerService/managedClusters/write"
    status             = "Started"
    resource_provider  = "Create or update managed cluster"
   }
  action {
    action_group_id = azurerm_monitor_action_group.aks-activity-log-action-Started.id
  }
}
```

또한, azurerm_monitor_action_group 리소스를 사용하여 Azure Monitor Action Group을 생성했습니다. 이 액션 그룹은 활동 로그 경고와 연결되어 경고가 활성화될 때 Logic App을 트리거합니다.

```js
resource "azurerm_monitor_action_group" "aks-activity-log-action-Started" {
  name                = "${azurerm_resource_group.example.name}-aks-activity-log-action-Started"
  resource_group_name = azurerm_resource_group.example.name
  short_name          = "akslog"
  logic_app_receiver {
    name                    = "logicappaction-patch-start"
    resource_id             = ""
    callback_url            = azurerm_logic_app_trigger_http_request.logicapp-started-trigger.callback_url
    use_common_alert_schema = true
  }
}
```

<div class="content-ad"></div>

이러한 경고 및 액션 그룹을 설정함으로써 AKS 클러스터를 효과적으로 모니터링하여 생성 또는 업데이트 작업이 발생할 때 필요에 따라 추가 작업이나 알림을 수행하는 로직 앱을 트리거할 수 있습니다.

# 결론

이 블로그 포스트에서는 Terraform과 Azure의 유지 보수 창 기능을 사용하여 AKS 패치를 자동화하는 방법에 대해 살펴보았습니다. AKS 클러스터를 정의하고 유지 보수 창을 구성함으로써 최신 패치와 업데이트를 반영하여 클러스터가 항상 최신 상태를 유지할 수 있습니다. 이 접근 방식은 패치 프로세스를 간소화하고 수동 노력을 줄이며 Kubernetes 환경의 전반적인 보안과 안정성을 향상시킵니다.

이 자동화 프로세스에 관련된 주요 단계는 다음과 같습니다:

<div class="content-ad"></div>

- Azure Provider를 구성하고 리소스 그룹을 생성합니다.
- 필요한 구성, 노드 풀 및 관리 식별자와 함께 AKS 클러스터를 정의합니다.
- 자동 업그레이드 및 노드 OS 업그레이드 일정을 예약하는 maintenance_window_auto_upgrade 및 maintenance_window_node_os 블록을 활용합니다.
- 업그레이드 채널, 빈도, 기간, 시작 시간 및 기타 매개변수를 사용자 정의하는 변수를 정의합니다.
- AKS 클러스터에 관한 중요한 정보를 검색하기 위해 outputs를 구성합니다.
- AKS 클러스터 생성 또는 업데이트 작업을 모니터링하고 추적하기 위해 Azure Monitor Activity Log Alerts 및 Action Groups를 설정합니다.

이 블로그 시리즈의 다음 부분에서는 Logic Apps를 사용하여 자동화된 Slack 메시지를 보내 AKS 패치 프로세스의 상태에 대해 알림을 받는 내용을 살펴볼 것입니다. 더 많은 자동화 소식을 기대해 주세요!