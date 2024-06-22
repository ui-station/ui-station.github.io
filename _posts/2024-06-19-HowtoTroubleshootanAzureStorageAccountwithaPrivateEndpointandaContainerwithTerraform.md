---
title: "Azure Storage Account에 개인 엔드포인트와 Terraform을 사용하여 컨테이너 문제 해결하는 방법"
description: ""
coverImage: "/assets/img/2024-06-19-HowtoTroubleshootanAzureStorageAccountwithaPrivateEndpointandaContainerwithTerraform_0.png"
date: 2024-06-19 13:28
ogImage: 
  url: /assets/img/2024-06-19-HowtoTroubleshootanAzureStorageAccountwithaPrivateEndpointandaContainerwithTerraform_0.png
tag: Tech
originalTitle: "How to Troubleshoot an Azure Storage Account with a Private Endpoint and a Container with Terraform"
link: "https://medium.com/@gmusumeci/how-to-troubleshoot-an-azure-storage-account-with-a-private-endpoint-and-a-container-with-terraform-c907f8f49d2b"
---


<img src="/assets/img/2024-06-19-HowtoTroubleshootanAzureStorageAccountwithaPrivateEndpointandaContainerwithTerraform_0.png" />

오늘은 Terraform으로 Azure Storage Account와 Private Endpoint를 배포할 때 발생하는 일반적인 오류에 대해 이야기해보겠습니다.

저희가 스토리지 계정 컨테이너를 추가하려고 시도하면, 다음과 같은 오류가 발생합니다:

# 1. 우리의 시나리오

<div class="content-ad"></div>

- 테라폼이 실행 중인 가상 머신이 있습니다.

- 리소스 그룹 "kopicloud-core-dev-we-rg"
- 가상 네트워크 "kopicloud-core-dev-we-vnet"
- 서브넷 "kopicloud-core-dev-we-subnet"

2. 기존의 "privatelink.blob.core.windows.net" 프라이빗 DNS 영역이 있습니다.

- 리소스 그룹 "kopicloud-core-dev-we-dns-rg"

<div class="content-ad"></div>

3. 새로운 Azure Storage Account와 프라이빗 엔드포인트를 배포할 것입니다.

- 리소스 그룹 "kopicloud-storage-dev-we-rg"
- 가상 네트워크 "kopicloud-storage-dev-we-vnet"
- 서브넷 "kopicloud-storage-dev-we-endpoint-subnet"

# 2. Azure Storage Account와 프라이빗 엔드포인트를 배포하는 Terraform 코드

이 코드에 대해 설명은 이 이야기에서 하지 않겠습니다. 더 자세한 내용은 "Terraform을 사용한 Azure Storage Account의 프라이빗 엔드포인트" 이야기를 확인해주세요.

<div class="content-ad"></div>

아래는 "network-variables.tf" 파일입니다:

```js
variable "network-vnet-cidr" {
  type        = string
  description = "네트워크 VNET의 CIDR"
}

variable "network-endpoint-subnet-cidr" {
  type        = string
  description = "네트워크 서브넷의 CIDR"
}
```

아래는 "network.tf" 파일입니다:

```js
# 네트워크를 위한 리소스 그룹 생성
resource "azurerm_resource_group" "network-rg" {
  name     = "kopicloud-storage-dev-we-rg"
  location = var.location
}

# 네트워크 VNET 생성
resource "azurerm_virtual_network" "network-vnet" {
  name                = "kopicloud-storage-dev-we-vnet"
  address_space       = [var.network-vnet-cidr]
  resource_group_name = azurerm_resource_group.network-rg.name
  location            = azurerm_resource_group.network-rg.location
}

# Endpoint 서브넷 생성
resource "azurerm_subnet" "endpoint-subnet" {
  name                 = "kopicloud-storage-dev-we-endpoint-subnet"
  address_prefixes     = [var.network-endpoint-subnet-cidr]
  virtual_network_name = azurerm_virtual_network.network-vnet.name
  resource_group_name  = azurerm_resource_group.network-rg.name

  private_endpoint_network_policies_enabled = true

  service_endpoints = ["Microsoft.Storage"]
}
```

<div class="content-ad"></div>

"storage-account.tf" 파일:

```js
# 기존 Private DNS Zone 참조
data "azurerm_private_dns_zone" "dns-zone" {
  name                = "privatelink.blob.core.windows.net"
  resource_group_name = "kopicloud-core-dev-we-dns-rg"
}

# Private DNS Zone 네트워크 링크 생성
resource "azurerm_private_dns_zone_virtual_network_link" "network_link" {
  name                  = "kopicloud-storage-dev-we-vnet-link"
  resource_group_name = data.azurerm_private_dns_zone.dns-zone.resource_group_name
  private_dns_zone_name = data.azurerm_private_dns_zone.dns-zone.name
  virtual_network_id    = azurerm_virtual_network.network-vnet.id
}

# 스토리지 계정 생성
resource "azurerm_storage_account" "storage" {
  name                = "kopicloudstoragedevwesta"
  resource_group_name = azurerm_resource_group.network-rg.name
  location            = azurerm_resource_group.network-rg.location

  account_kind             = "StorageV2"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

# 프라이빗 엔드포인트 생성
resource "azurerm_private_endpoint" "endpoint" {
  name                = "kopicloudstoragedevwesta-pe"
  resource_group_name = azurerm_resource_group.network-rg.name
  location            = azurerm_resource_group.network-rg.location
  subnet_id           = azurerm_subnet.endpoint-subnet.id

  private_service_connection {
    name                           = "kopicloudstoragedevwesta-psc"
    private_connection_resource_id = azurerm_storage_account.storage.id
    is_manual_connection           = false
    subresource_names              = ["blob"]
  }
}

# DNS A 레코드 생성
resource "azurerm_private_dns_a_record" "dns_a" {
  name                = "kopicloudstoragedevwesta"
  resource_group_name = data.azurerm_private_dns_zone.dns-zone.resource_group_name
  zone_name = data.azurerm_private_dns_zone.dns-zone.name
  ttl                 = 300
  records             = [azurerm_private_endpoint.endpoint.private_service_connection.0.private_ip_address]
}
```

# 3. 공개 액세스 해제

기본적으로 이 코드는 공개 액세스가 있는 스토리지 계정을 생성합니다.

<div class="content-ad"></div>


![이미지](/assets/img/2024-06-19-HowtoTroubleshootanAzureStorageAccountwithaPrivateEndpointandaContainerwithTerraform_1.png)

공개 액세스를 종료해야만 Private Endpoint를 구현하는 의미가 있습니다.

따라서 "azurerm_storage_account" 리소스를 수정하여 public_network_access_enabled = false 라인을 추가하겠습니다.

```js
// 스토리지 계정 생성
resource "azurerm_storage_account" "storage" {
  name                = "${lower(replace(var.company," ","-"))}${var.app_name}${var.environment}${var.shortlocation}sta"
  resource_group_name = azurerm_resource_group.network-rg.name
  location            = azurerm_resource_group.network-rg.location

  account_kind             = "StorageV2"
  account_tier             = "Standard"
  account_replication_type = "LRS"

  public_network_access_enabled = false
}
``` 

<div class="content-ad"></div>

"Terraform Apply" 명령을 실행하여 공개 액세스가 비활성화되었음을 확인했습니다.

![이미지](/assets/img/2024-06-19-HowtoTroubleshootanAzureStorageAccountwithaPrivateEndpointandaContainerwithTerraform_2.png)

# 4. 스토리지 계정 컨테이너 생성

이제 다음 코드로 스토리지 계정 컨테이너를 생성할 차례입니다:

<div class="content-ad"></div>

```hcl
// 외부 저장소 계정용 Azure Storage 컨테이너 생성
resource "azurerm_storage_container" "external" {
  name                  = "container"
  storage_account_name  = azurerm_storage_account.storage.name
  container_access_type = "private"
} 
```

"Terraform Apply" 명령을 실행했는데 오류가 발생했습니다.

# 4. 특정 네트워크에서 액세스 권한 활성화

따라서 이 문제를 해결하기 위해 특정 네트워크에서의 공개 액세스를 허용할 것입니다.


<div class="content-ad"></div>

그럼 "azurerm_storage_account" 리소스를 수정하여 public_network_access_enabled = true 라인을 추가하겠습니다.

```js
// 저장소 계정 생성
resource "azurerm_storage_account" "storage" {
  name                = "${lower(replace(var.company," ","-"))}${var.app_name}${var.environment}${var.shortlocation}sta"
  resource_group_name = azurerm_resource_group.network-rg.name
  location            = azurerm_resource_group.network-rg.location
  account_kind             = "StorageV2"
  account_tier             = "Standard"
  account_replication_type = "LRS"

  public_network_access_enabled = true
}
```

또한 지정된 네트워크에서 액세스 허용하도록 "Azure Storage Account Network Rules" 리소스를 생성해야 합니다; 이 경우 Storage Account Subnet에서 액세스를 허용할 것입니다.

```js
# Azure Storage Account Network Rules 생성
resource "azurerm_storage_account_network_rules" "rules" {
  storage_account_id = azurerm_storage_account.storage.id

  default_action = "Deny"
  virtual_network_subnet_ids = [ azurerm_subnet.endpoint-subnet.id ]
  bypass         = ["Metrics", "Logging", "AzureServices"]
}
```

<div class="content-ad"></div>

“Terraform Apply” 명령을 실행했을 때 다시 403 오류가 발생했어요.

# 5. Azure Storage Account 네트워크 규칙 업데이트

가시성을 위해 규칙을 변경하여 VM이 Terraform을 실행할 때 서브넷에서의 트래픽을 허용하도록 할게요. 우리는 데이터를 사용하여 서브넷 ID를 가져올 거에요.

```js
# 핵심 서브넷을 참조합니다
data "azurerm_subnet" "core" {
  name                 = "kopicloud-core-dev-we-subnet"
  virtual_network_name = "kopicloud-core-dev-we-vnet"
  resource_group_name  = "kopicloud-core-dev-we-rg"
}

# Azure Storage Account 네트워크 규칙 생성
resource "azurerm_storage_account_network_rules" "rules" {
  storage_account_id = azurerm_storage_account.storage.id
  
  default_action = "Deny"
  virtual_network_subnet_ids = [ azurerm_subnet.endpoint-subnet.id, data.azurerm_subnet.core.id ]
  bypass         = ["Metrics", "Logging", "AzureServices"]
}
```

<div class="content-ad"></div>

"Terraform Apply" 명령을 실행했는데 다시 403 오류가 발생했어요.

포털을 살펴보니 규칙이 구현되지 않았다는 것을 알았어요. 컨테이너가 이전에 실패했기 때문이에요.

<img src="/assets/img/2024-06-19-HowtoTroubleshootanAzureStorageAccountwithaPrivateEndpointandaContainerwithTerraform_3.png"/>

# 6. 코어와 스토리지 가상 네트워크 간 피어링 생성하기

<div class="content-ad"></div>

그 후에, 어쩌면 두 가상 네트워크 사이에 피어링이 필요할지도 모른다고 생각했어요.

그래서 "peering.tf" 파일을 만들고 이 코드를 추가했어요:

```js
locals {
  core_rg_name = "kopicloud-core-dev-we-rg"
  core_vnet_name = "kopicloud-core-dev-we-vnet"
}

// Core VNET에 대한 참조
data "azurerm_virtual_network" "core" {
  name                = local.core_vnet_name
  resource_group_name = local.core_rg_name
}

// Core에서 스토리지로 피어링 생성
resource "azurerm_virtual_network_peering" "core-to-storage" {
  name                      = "Core-to-Storage"
  resource_group_name       = local.core_rg_name
  virtual_network_name      = local.core_vnet_name
  remote_virtual_network_id = azurerm_virtual_network.network-vnet.id
}

// 스토리지에서 Core로 피어링 생성
resource "azurerm_virtual_network_peering" "storage-to-core" {
  name                      = "Storage-to-Core"
  resource_group_name       = azurerm_resource_group.network-rg.name
  virtual_network_name      = azurerm_virtual_network.network-vnet.name
  remote_virtual_network_id = data.azurerm_virtual_network.core.id
}
```

그리고 "Terraform Apply" 명령어를 실행했더니, 마침내 우리 코드가 잘 실행되고 있어요!

<div class="content-ad"></div>

최종 네트워크 구성은 다음과 같아야 합니다:

![이미지](/assets/img/2024-06-19-HowtoTroubleshootanAzureStorageAccountwithaPrivateEndpointandaContainerwithTerraform_4.png)

그리고 이것이 전부에요. 만약 이 이야기를 좋아하셨다면 👏을 눌러 서포트를 보여주세요. 읽어 주셔서 감사합니다!