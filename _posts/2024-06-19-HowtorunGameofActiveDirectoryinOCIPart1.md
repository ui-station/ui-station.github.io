---
title: "OCI에서 Active Directory 게임을 실행하는 방법  1부"
description: ""
coverImage: "/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_0.png"
date: 2024-06-19 13:32
ogImage: 
  url: /assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_0.png
tag: Tech
originalTitle: "How to run Game of Active Directory in OCI — Part 1"
link: "https://medium.com/learnoci/how-to-run-game-of-active-directory-in-oci-part-1-5be51387a7a2"
---


제가 정말 즐겁게 즐겼던 프로젝트 중 하나는 GOAD입니다. 게임 오브 스로운즈 팬으로서 사이버 지식을 시험하고 동시에 즐길 수 있는 수단이었습니다. 만약 여러분이 스로운즈의 팬이라면, 이 Windows/AD/SCCM 랩은 여러분을 위한 것입니다.

처음에는 중첩 가상화 서버를 만들려고 했는데, 테라폼과 앤서블 지식이 거의 없어서 어려움을 겪었습니다. 하지만 저는 자동화로 나아가기로 결정했습니다. 일상적인 작업에서 시간을 절약하고, 올바르게 구현된다면 인간 에러를 줄일 수 있기 때문에 자동화가 많은 도움이 된다는 점에 동의합니다.

`Game of Active Directory (GOAD)` 프로젝트는 현실적이고 실용적인 경험을 통해 사이버 보안 기술을 향상시키는 포괄적인 랩 환경입니다. Oracle Cloud Infrastructure (OCI)에서 호스팅되며, 다양한 OCI 서비스와 통합하여 현실 세계의 보안 시나리오를 모의할 수 있습니다.

https://github.com/adibirzu/GOAD/blob/main/docs/install_with_oci.md

<div class="content-ad"></div>

랩 환경으로서 VCN 보안 목록에서 엄격한 ACL을 구현하지는 않았어요. 그러나 모든 포트에서 192.168.0.0/16에서의 액세스는 허용했어요.

1- 우리는 OCI의 Windows Server/Ubuntu 이미지를 사용할 거에요. 이 선택은 PAYG 요금제를 사용해. Windows Server 평가판을 사용할 계획이라면, 자체 이미지를 빌드하고 테라폼 스크립트를 사용자 정의 이미지 OCID로 설정해야 해요:

Oracle Cloud Infrastructure 이미지

2- 테라폼 스크립트와 PowerShell 스크립트는 ad →GOAD →providers →OCI 하위에 추가됐어요.

<div class="content-ad"></div>

![Screenshot](/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_0.png)

```js
./goad.sh -t check -l GOAD -p oci -m local
```

3- 서로 다른 공급업체에서 사용자들이 알고 있는 동일한 워크플로를 제공하기 위해 goad.sh 스크립트에 제공자로 oci를 추가했습니다.

![Screenshot](/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_1.png)

<div class="content-ad"></div>

OCI에서 우분투의 기본 사용자는 ubuntu이므로 goad.sh의 모든 참조는 ubuntu 사용자를 사용할 것입니다:

```
![2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_2](/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_2.png)
```

4. 스크립트 아래에, 저는 설치 후 우분투 서버의 선행 조건을 설치하기 위해 setup_oci.sh 스크립트를 만들었습니다.

```
![2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_3](/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_3.png)
```

<div class="content-ad"></div>

5- 오라클 클라우드 인프라에서 랩을 프로비저닝하는 방법을 설명하는 docs/install_with_oci.md를 업데이트했습니다.

6- ad →GOAD →providers →oci →ssh_keys →ubuntu-jumpox.pem에 개인 키를 배치하여 Ubuntu 인스턴스에 연결을 테스트했습니다.

!!!! 이 키를 매우 주의해서 다루고, git에 공개하지 마세요.

![이미지](/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_4.png)

<div class="content-ad"></div>

테이블 태그를 마크다운 형식으로 변경하였습니다.

<div class="content-ad"></div>

9- ad → GOAD → providers → oci → terraform 폴더에 Terraform/PowerShell 파일을 만들어주세요:

- windows_cloud_init.ps1

```powershell
# 변수
$adminUsername = “ansible”
$adminPassword = ConvertTo-SecureString “YourSecurePassword123!” -AsPlainText -Force

# ansible 사용자 생성
New-LocalUser $adminUsername -Password $adminPassword -FullName $adminUsername -Description “Ansible admin user”
Add-LocalGroupMember -Group “Administrators” -Member $adminUsername

# WinRM 활성화
winrm quickconfig -q
winrm set winrm/config/service/auth @{Basic=”true”}
winrm set winrm/config/service @{AllowUnencrypted=”true”}
winrm set winrm/config/service @{EnableCompatibilityHttpsListener=”true”}
winrm set winrm/config/service @{EnableCompatibilityHttpListener=”true”}
$cert = New-SelfSignedCertificate -DnsName $(hostname) -CertStoreLocation Cert:\LocalMachine\My
winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=$(hostname); CertificateThumbprint=$($cert.Thumbprint)}
Set-Service -Name winrm -StartupType Automatic
Start-Service -Name winrm

# WinRM을 위한 기본 인증 및 암호화 트래픽 활성화
Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -Value $true

# WinRM 방화벽 예외 설정
New-NetFirewallRule -Name "WinRM-HTTP" -DisplayName "WinRM (HTTP-In)" -Protocol TCP -LocalPort 5985 -Action Allow -Enabled True
New-NetFirewallRule -Name "WinRM-HTTPS" -DisplayName "WinRM (HTTPS-In)" -Protocol TCP -LocalPort 5986 -Action Allow -Enabled True

# TLS 1.2 프로토콜 설정
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

# NuGet 공급자 설치 및 PowerShellGet 업데이트
Install-PackageProvider -Name NuGet -Force -Confirm:$false
Update-Module -Name PowerShellGet -Force -AllowClobber -Confirm:$false
```

- profile.tf

<div class="content-ad"></div>

```hcl
provider "oci" {
  tenancy_ocid      = var.tenancy_ocid
  user_ocid         = var.user_ocid
  fingerprint       = var.fingerprint
  private_key_path  = var.private_key_path
  region            = var.region
}
```

variables.tf

```hcl
variable "tenancy_ocid" {
  description = "The OCID of your tenancy."
  type        = string
}

variable "user_ocid" {
  description = "The OCID of the user calling the API."
  type        = string
}

variable "fingerprint" {
  description = "The fingerprint of the API key."
  type        = string
}

variable "private_key_path" {
  description = "The path to the private key."
  type        = string
  default     = "/Users/abirzu/.ssh/newpemkey.pem"
}

variable "region" {
  description = "The region to use."
  type        = string
  default     = "eu-frankfurt-1"
}

variable "compartment_ocid" {
  description = "The OCID of the compartment to use."
  type        = string
}

variable "availability_domain" {
  description = "The availability domain to use."
  type        = string
  default     = "NoEK:EU-FRANKFURT-1-AD-1"
}

variable "shape" {
  description = "The shape of the instance to be created."
  type        = string
  default     = "VM.Standard.E5.Flex"
}

variable "ocpus" {
  description = "The number of OCPUs to allocate."
  type        = number
  default     = 1
}

variable "memory_in_gbs" {
  description = "The amount of memory in GBs."
  type        = number
  default     = 12
}

variable "ssh_authorized_keys" {
  description = "The public key for SSH access to the instances."
  type        = string
}

variable "image_ocid" {
  description = "The OCID of the image to use."
  type        = string
}

variable "windows2016_image_ocid" {
  description = "The OCID of the Windows Server 2016 image."
  type        = string
}

variable "windows2019_image_ocid" {
  description = "The OCID of the Windows Server 2019 image."
  type        = string
}
```

network.tf
```hcl
```

<div class="content-ad"></div>

```js
resource “oci_core_vcn” “generated_oci_core_vcn” {
 cidr_block = “192.168.0.0/16”
 compartment_id = var.compartment_ocid
 display_name = “goad-virtual-network”
 dns_label = “goadvcn”
}
resource “oci_core_subnet” “public_subnet” {
 cidr_block = “192.168.57.0/24”
 compartment_id = var.compartment_ocid
 display_name = “public-subnet”
 dns_label = “publicsubnet”
 vcn_id = oci_core_vcn.generated_oci_core_vcn.id
 route_table_id = oci_core_route_table.public_route_table.id
}
resource “oci_core_subnet” “private_subnet” {
 cidr_block = “192.168.56.0/24”
 compartment_id = var.compartment_ocid
 display_name = “private-subnet”
 dns_label = “privatesubnet”
 vcn_id = oci_core_vcn.generated_oci_core_vcn.id
 route_table_id = oci_core_route_table.private_route_table.id
 prohibit_internet_ingress = true
 prohibit_public_ip_on_vnic = true
 security_list_ids = [oci_core_security_list.winrm_rdp_security_list.id]
}
resource “oci_core_internet_gateway” “generated_oci_core_internet_gateway” {
 compartment_id = var.compartment_ocid
 display_name = “Internet Gateway goad-virtual-network”
 enabled = true
 vcn_id = oci_core_vcn.generated_oci_core_vcn.id
}
resource “oci_core_nat_gateway” “generated_oci_core_nat_gateway” {
 compartment_id = var.compartment_ocid
 display_name = “NAT Gateway goad-virtual-network”
 vcn_id = oci_core_vcn.generated_oci_core_vcn.id
}
resource “oci_core_route_table” “public_route_table” {
 compartment_id = var.compartment_ocid
 vcn_id = oci_core_vcn.generated_oci_core_vcn.id
 display_name = “public-route-table”
route_rules {
 destination = “0.0.0.0/0”
 destination_type = “CIDR_BLOCK”
 network_entity_id = oci_core_internet_gateway.generated_oci_core_internet_gateway.id
 }
}
resource “oci_core_route_table” “private_route_table” {
 compartment_id = var.compartment_ocid
 vcn_id = oci_core_vcn.generated_oci_core_vcn.id
 display_name = “private-route-table”
route_rules {
 destination = “0.0.0.0/0”
 destination_type = “CIDR_BLOCK”
 network_entity_id = oci_core_nat_gateway.generated_oci_core_nat_gateway.id
 }
}
  options {
    type                  = "DomainNameServer"
    server_type           = "CustomDnsServer"
    custom_dns_servers    = ["192.168.56.10","8.8.8.8"]
  }
 options {
        type = "SearchDomain"
        search_domain_names = [ "sevenkingdoms.local" ]
    }
}
resource “oci_core_security_list” “winrm_rdp_security_list” {
 compartment_id = var.compartment_ocid
 vcn_id = oci_core_vcn.generated_oci_core_vcn.id
 display_name = “winrm_rdp_security_list”
egress_security_rules {
 protocol = “all”
 destination = “0.0.0.0/0”
 stateless = false
 }
ingress_security_rules {
 protocol = “all”
 source = “192.168.0.0/16”
 stateless = false
 }
}
```

```js
resource “oci_core_default_dhcp_options” “default_dhcp_options” {
 manage_default_resource_id = oci_core_vcn.generated_oci_core_vcn.default_dhcp_options_id
options {
 type = “DomainNameServer”
 server_type = “CustomDnsServer”
 custom_dns_servers = [“192.168.56.10”]
 search_domain_names = [“sevenkingdoms.local”]
 }
```

The DHCP configuration part is essential, as if it’s not properly configured, it will result in the failure of the ansible jobs. If the terraform variable will not add the search domain, you need to do this manually:

Go to the VCN → DHCP Options:```

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_7.png" />

화면 오른쪽 상단에 있는 3 점을 클릭한 후 편집을 선택하세요. 여기에서 외부 DNS 서버를 추가하고 사용자 정의 검색 도메인인 Seven Kingdoms를 추가할 수 있습니다.

<img src="/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_8.png" />

이 부분에서 DNS 문제를 해결해야합니다.

<div class="content-ad"></div>

```js
options {
type = "도메인 네임 서버"
server_type = "사용자 정의 DNS 서버"
custom_dns_servers = ["192.168.56.10","8.8.8.8"]
}
options {
type = "검색 도메인"
search_domain_names = ["seven kingdoms.local"]
}
```

jumpbox.tf

```js
resource "oci_core_instance" "jumpbox" {
 availability_domain = var.availability_domain
 compartment_id = var.compartment_ocid
 display_name = "jumpbox"
 shape = var.shape
shape_config {
 baseline_ocpu_utilization = "BASELINE_1_1"
 memory_in_gbs = var.memory_in_gbs
 ocpus = var.ocpus
 }
source_details {
 source_id = var.image_ocid
 source_type = "이미지"
 }
#이미지 OCID’S https://docs.oracle.com/en-us/iaas/images/image/bd616d0a-fae4-490e-bd31-a9406095b844/ 
 create_vnic_details {
 assign_ipv6ip = false
 assign_private_dns_record = true
 assign_public_ip = true
 subnet_id = oci_core_subnet.public_subnet.id
 }
metadata = {
 ssh_authorized_keys = var.ssh_authorized_keys
 }
agent_config {
 is_management_disabled = false
 is_monitoring_disabled = false
plugins_config {
 desired_state = "사용 안 함"
 name = "취약점 스캐닝"
 }
 plugins_config {
 desired_state = "사용 안 함"
 name = "관리 에이전트"
 }
 plugins_config {
 desired_state = "사용함"
 name = "사용자 지정 로그 모니터링"
 }
 plugins_config {
 desired_state = "사용 안 함"
 name = "컴퓨팅 RDMA GPU 모니터링"
 }
 plugins_config {
 desired_state = "사용함"
 name = "컴퓨팅 인스턴스 모니터링"
 }
 plugins_config {
 desired_state = "사용 안 함"
 name = "컴퓨팅 HPC RDMA 자동 구성"
 }
 plugins_config {
 desired_state = "사용 안 함"
 name = "컴퓨팅 HPC RDMA 인증"
 }
 plugins_config {
 desired_state = "사용함"
 name = "클라우드 가드 워크로드 보호"
 }
 plugins_config {
 desired_state = "사용 안 함"
 name = "블록 볼륨 관리"
 }
 plugins_config {
 desired_state = "사용 안 함"
 name = "바스천"
 }
 }
availability_config {
 is_live_migration_preferred = true
 recovery_action = "인스턴스 복원"
 }
platform_config {
 is_symmetric_multi_threading_enabled = true
 type = "AMD_VM"
 }
instance_options {
 are_legacy_imds_endpoints_disabled = false
 }
}
```

windowsvm.tf
```

<div class="content-ad"></div>

```js
리소스 "oci_core_instance" "windows_instance" {
 for_each = {
 kingslanding = {
 name = "kingslanding"
 private_ip_address = "192.168.56.10"
 admin_username = "ansible"
 admin_password = "8dCT-DJjgScp"
 image_ocid = var.windows2019_image_ocid
 }
 winterfell = {
 name = "winterfell"
 private_ip_address = "192.168.56.11"
 admin_username = "ansible"
 admin_password = "NgtI75cKV+Pu"
 image_ocid = var.windows2019_image_ocid
 }
 castelblack = {
 name = "castelblack"
 private_ip_address = "192.168.56.22"
 admin_username = "ansible"
 admin_password = "NgtI75cKV+Pu"
 image_ocid = var.windows2019_image_ocid
 }
 meereen = {
 name = "meereen"
 private_ip_address = "192.168.56.12"
 admin_username = "ansible"
 admin_password = "Ufe-bVXSx9rk"
 image_ocid = var.windows2016_image_ocid
 }
 braavos = {
 name = "braavos"
 private_ip_address = "192.168.56.23"
 admin_username = "ansible"
 admin_password = "978i2pF43UJ-"
 image_ocid = var.windows2016_image_ocid
 }
 }
availability_domain = var.availability_domain
 compartment_id = var.compartment_ocid
 display_name = each.value.name
 shape = "VM.Standard.E5.Flex"
shape_config {
 ocpus = 2
 memory_in_gbs = 32
 }
source_details {
 source_id = each.value.image_ocid
 source_type = "image"
 }
create_vnic_details {
 assign_ipv6ip = false
 assign_private_dns_record = true
 assign_public_ip = false
 subnet_id = oci_core_subnet.private_subnet.id
 hostname_label = each.value.name
 private_ip = each.value.private_ip_address
 }
metadata = {
 user_data = base64encode(file("${path.module}/windows_cloud_init.ps1"))
 admin_password = each.value.admin_password
 }
}
```

outputs.tf

```js
output "ubuntu_jumpbox_ip" {
 value = oci_core_instance.jumpbox.public_ip
}
output "windows_instance_opc_passwords" {
 value = { for k, v in oci_core_instance.windows_instance : k => v.metadata.admin_password }
 sensitive = true
}
```

프로비저닝 중 발생한 일반적인 오류입니다.
```

<div class="content-ad"></div>

다음과 같이 Markdown 형식에 맞게 표 태그를 변경하십시오.

```markdown
![이미지](/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_9.png)

모든 TF가 생성된 후, GOAD git 저장소가 정상적으로 동기화된 경우 다음을 실행해야 합니다:

abirzu@abirzu-mac GOAD % ./goad.sh -t destroy -l GOAD -p oci -m local

모든 서버가 가동되고 작동 중인 것을 확인할 수 있습니다.
```

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-HowtorunGameofActiveDirectoryinOCIPart1_10.png" />

축하합니다! 환경이 작동 중에 있습니다.

# 서버

이 랩은 실제로 다섯 개의 가상 머신으로 구성되어 있습니다:

<div class="content-ad"></div>

- kingslanding: DC01는 Windows Server 2019에서 실행 중입니다(기본으로 windefender가 활성화됨)
- winterfell: DC02는 Windows Server 2019에서 실행 중입니다(기본으로 windefender가 활성화됨)
- castelblack: SRV02는 Windows Server 2019에서 실행 중입니다(windefender가 기본적으로 비활성화됨)
- meereen: DC03은 Windows Server 2016에서 실행 중입니다(기본으로 windefender가 활성화됨)
- braavos: SRV03은 Windows Server 2016에서 실행 중입니다(기본으로 windefender가 활성화됨)

도메인: north.sevenkingdoms.local

- winterfell: DC01
- castelblack: SRV02: MSSQL / IIS

도메인: sevenkingdoms.local

<div class="content-ad"></div>

- kingslanding: DC02
- castelrock: SRV01 (자원 부족으로 비활성화됨)

# 도메인: essos.local

- braavos: DC03
- meeren: SRV03: MSSQL / ADCS

인터넷에서 몇 가지 가이드를 따르거나 자신만의 방법을 찾아 시작할 수 있습니다!

<div class="content-ad"></div>

AD | Mayfly (mayfly277.github.io)

Solving Game of Active Directory (GOAD) by Orange Cyberdefense Part-1 | by n00🔑 | Medium

제1부에서는 OCI에서 GOAD 랩을 만드는 방법을 소개했고, 다음 부분에서는 다음에 초점을 맞출 것입니다:

OCI 통합:

<div class="content-ad"></div>

1. OCI Management Agent:

- 설명: 클라우드 작업을 자동화하고 모니터링합니다.
  
- 통합: GOAD 환경 내 자원의 배포 및 관리를 간소화합니다.

2. Sysmon 및 로깅 분석:

<div class="content-ad"></div>

• 설명: Sysmon은 시스템 활동을 기록하여 침입 탐지를 지원합니다.

• 통합: Sysmon 이벤트를 수집하고 OCI Logging Analytics로 전송하여 고급 로그 분석 및 시각화를 지원합니다. 자세한 내용은 여기에서 확인하세요.

3. Arkime 통합:

• 설명: Arkime은 오픈 소스 패킷 캡처 및 검색 도구입니다.

<div class="content-ad"></div>

• 통합: 효율적인 데이터 색인 및 검색을 위해 OCI OpenSearch를 활용합니다. 자세한 내용은 여기에서 확인할 수 있습니다.

4. 클라우드 가드 인스턴스 보안:

• 설명: 클라우드 가드는 포괄적인 클라우드 보안 관리를 제공합니다.

• 통합: 로깅 분석을 활용하여 인스턴스 보안을 강화하며 실시간 위협 감지 및 대응이 가능합니다. 더 많은 정보는 여기에서 확인할 수 있습니다.