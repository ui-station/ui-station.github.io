---
title: "Terraform을 활용한 Multi-Cloud 관리 마스터하기"
description: ""
coverImage: "/assets/img/2024-05-27-MasteringMulti-CloudManagementwithTerraform_0.png"
date: 2024-05-27 17:46
ogImage: 
  url: /assets/img/2024-05-27-MasteringMulti-CloudManagementwithTerraform_0.png
tag: Tech
originalTitle: "Mastering Multi-Cloud Management with Terraform"
link: "https://medium.com/@bijit211987/mastering-multi-cloud-management-with-terraform-0615675415d9"
---


<img src="/assets/img/2024-05-27-MasteringMulti-CloudManagementwithTerraform_0.png" />

여러 공개 클라우드(Azure, AWS, GCP 등)를 사용하면 유연성을 제공하고 비용을 최적화하며 벤더 락인을 줄일 수 있습니다. 그러나 다양한 클라우드 플랫폼 간에 인프라 및 서비스를 관리하는 것은 어렵습니다.

테라폼을 통해 일관되고 자동화된 멀티 클라우드 관리가 가능한지 알아봅시다. 이 포함사항은 멀티 클라우드 아키텍처, 테라폼 추상화, 프로비저닝, 거버넌스, 네트워킹, 배포 패턴, 테스팅, 그리고 Azure 및 GCP 전반적인 모니터링입니다.

# 개요

<div class="content-ad"></div>

다음 주제를 다룰 것입니다:

- 테라폼을 사용한 멀티 클라우드 아키텍처
- 인프라 차이점 추상화
- 리소스 프로비저닝 및 의존성 관리
- 정책 강화 및 거버넌스
- 네트워킹 토폴로지와 연결
- 블루-그린, 카나리아, 멀티 리전 배포 패턴
- 통합 테스트 및 목 객체(Mock) 사용
- 중앙 집중식 로깅, 메트릭 및 관찰 가능성

이 코드 예제들은 테라폼을 사용해 Azure와 GCP로 인프라 및 서비스를 배포하는 방법을 보여줍니다. 이는 테라폼을 일관된 추상화 계층으로 사용하여 실제 멀티 클라우드 관리를 보여줍니다.

# 멀티 클라우드 아키텍처

<div class="content-ad"></div>

기본적인 멀티 클라우드 아키텍처는 Terraform을 사용하면 다음과 같이 보입니다:

Terraform은 여러 클라우드 계정에 인프라를 프로비저닝하고 플랫폼 간의 차이를 추상화합니다. 상태는 원격으로 저장되며 계정 간에 공유됩니다.

멀티 클라우드 아키텍처를 위한 몇 가지 주요 디자인 원칙:

- 차이 추상화 — 공급자별 논리를 최소화하고 차이를 추상화 뒤에 숨깁니다
- 환경 모듈화 — 환경 및 구성 요소별로 모듈화된 파일로 구성을 분리합니다
- 표준화된 네이밍 — 쉽게 연관을 확인하기 위해 클라우드 간에 일관된 네이밍 체계 사용
- 정책 캡슐화 — 정책 및 거버넌스 규칙을 모듈화된 재사용 가능한 파일에 유지합니다
- 상태 중앙화 — 원격 상태를 사용하여 서비스 및 클라우드 전체의 상태를 공유합니다
- 공유 서비스 추출 — ID, DNS, CDN과 같은 공유 서비스를 한 번 빌드하고 재사용합니다
- 파이프라인 통합 — 모든 클라우드 대상으로 배포하는 표준 CI/CD 파이프라인 사용

<div class="content-ad"></div>

이러한 원칙을 따라서, 여러 대상 클라우드 간에 일관성있는 이동 가능한 구성을 구축할 수 있습니다.

그 다음으로, Terraform이 클라우드 간의 차이를 추상화하는 데 어떻게 도움을 주는지 살펴보겠습니다.

# 인프라 추상화

Terraform의 강점 중 하나는 클라우드 플랫폼 간의 차이를 추상화하는 균일한 추상화 계층을 제공할 수 있는 능력입니다.

<div class="content-ad"></div>

예를 들어, Azure 대 GCP에서 MySQL 데이터베이스 프로비저닝:

Azure

```js
resource "azurerm_mysql_server" "db" {
  name                = "mysqlserver"
  location            = "eastus"
  resource_group_name = azurerm_resource_group.rg.name
```

```js
  sku {
    name     = "B_Gen5_2"
    capacity = 2
  }
}
resource "azurerm_mysql_database" "db" {
  name                = "mydatabase"
  resource_group_name = azurerm_resource_group.rg.name
  server_name         = azurerm_mysql_server.db.name
  charset             = "utf8"
  collation           = "utf8_unicode_ci"
}
```

<div class="content-ad"></div>

GCP

```js
resource "google_sql_database_instance" "db" {
  name             = "mysql-instance"
  database_version = "MYSQL_5_7"
  region           = "us-central1"
```

```js
  settings {
    tier = "db-f1-micro"
  }
}
resource "google_sql_database" "database" {
  name     = "my-database"
  instance = google_sql_database_instance.db.name
}
```

Terraform의 리소스 유형은 일관된 인터페이스를 제공하기 위해 기본 API 차이를 추상화합니다.

<div class="content-ad"></div>

이 추상화는 대규모 변경없이 클라우드 간에 쉽게 전환할 수 있도록 도와줍니다. 필요한 경우 각 클라우드 공급업체에 맞게 엣지에서 맞춤 설정할 수 있습니다.

테라폼이 다중 클라우드 추상화를 제공하는 주요 분야 몇 가지:

- 컴퓨팅 — 가상 머신, 컨테이너, 쿠버네티스, 스케일링
- 네트워크 — 서브넷, 라우팅, 보안 그룹, 로드 밸런싱
- 스토리지 — 블롭, 디스크, 데이터베이스
- 아이덴티티 — 역할, 권한, 액세스 제어
- 인프라스트럭처 — DNS, VPN, 규칙, 정책

최대한 다중 클라우드 추상화를 활용하여 구성을 작성하면 이동성이 높아집니다.

<div class="content-ad"></div>

# 프로비저닝 및 의존성

여러 클라우드 인프라를 프로비저닝할 때 가장 좋은 방법은 리소스를 의존성 레이어로 구조화하는 것입니다.

상위 수준의 구성은 하위 수준의 구성을 의존합니다. 예를 들어:

테라폼을 사용할 때, depends_on 속성을 사용하여 리소스 의존성을 명시적으로 정의하세요:

<div class="content-ad"></div>

```md
리소스 "azurerm_subnet" "public" {
  #...
}
```

```md
리소스 "azurerm_network_interface" "nic" {
  #...
  subnet_id = azurerm_subnet.public.id
}
리소스 "azurerm_virtual_machine" "main" {
  #...
  
  network_interface_ids = [
    azurerm_network_interface.nic.id,
  ]
  depends_on = [
    azurerm_network_interface.nic
  ]
}
```

테라폼은 리소스 간 종속성을 분석하고 변경 사항을 올바른 순서로 적용합니다.

멀티 클라우드 환경에서의 종속성에 대한 팁:
```

<div class="content-ad"></div>

- 공통 빌딩 블록을 기본 모듈에 네트워크와 같은 것으로 포함시킵니다.
- 독립적인 구성 요소를 계층별로 분리합니다.
- 암시적이어도 `depends_on`을 명시적으로 정의합니다.
- 데드락을 일으키는 종속성 순환이 있는지 확인합니다.
- 종속성 순서대로 계획하고 적용합니다.

자원 종속성을 올바르게 설정하는 것이 여러 클라우드에서 원활한 프로비저닝을 위해 중요합니다.

# 정책 집행과 거버넌스

기관 정책과 규정 준수 요구사항을 강제하는 것은 여러 클라우드 관리에 있어 중요합니다.

<div class="content-ad"></div>

다음은 운용 규칙의 몇 가지 예시입니다:

- VM 유형 제한
- 데이터 주권을 위한 지역 및 존 제어 
- 태깅 규칙과 표준 설정
- 네트워킹 노출 제한
- 암호화 요구 사항 강제
- 고위험 자원 사용 제한

테라폼을 통해 정책 강제를 활성화할 수 있습니다:

변수

<div class="content-ad"></div>

허용된 값 제한:

```js
variable "region" {
  type    = string
  default = "us-east-1"
}
```

```js
resource "aws_db_instance" "db" {
  region = var.region # us-east-1만 허용됩니다
}
```

모듈

<div class="content-ad"></div>

친구야, 모듈 내에서 지배 논리를 캡슐화하고 재사용하세요:

```js
module "server" {
  source = "./modules/certified_server"
# 인증된 서버는 합리적인 기본값을 설정합니다
}
```

Sentinel 정책

리소스를 제한하는 대상 정책을 적용하십시오:

<div class="content-ad"></div>

```js
aws_instance_disallowed_type = rule {
  all aws_instance as _, instance {
    instance.instance_type is not in ["t2.micro", "t3.micro"]
  }
}
```

이러한 메커니즘은 구성에 직접 규제를 포함하여 규정 준수를 쉽게 만듭니다.

# 네트워킹 토폴로지

다양한 클라우드 간 네트워크 연결성을 관리하는 것은 복잡성을 증가시킵니다. 일부 하이브리드 클라우드 네트워크 토폴로지는 다음과 같습니다:```

<div class="content-ad"></div>

테이블 형식을 Markdown 형식으로 변경하실래요.

```markdown
| Pairing | 
|--------|
| Connect cloud regions to on-prem data centers. Useful for migration and hybrid workloads. |

| Hub-and-Spoke |
|---------------|
| Central hub VPC with connectivity to multiple cloud spokes. Enables transitivity between spokes. |
```

<div class="content-ad"></div>

메시

클라우드 간 사이트 간 VPN이 구성된 완전히 메시된 네트워크입니다. 지역 간 직접 통신을 제공합니다.

테라폼은 terraform-provider-aws, terraform-provider-azurerm 및 유사한 네트워킹 제공업체를 통해 이러한 네트워크 토폴로지를 조정하는 것을 단순화합니다.

예를 들어, AWS에서 Azure로 사이트 간 VPN을 생성하는 방법:

<div class="content-ad"></div>

```js
# AWS 측
리소스 "aws_customer_gateway" "gw" {
  bgp_asn    = 65002
  ip_address = "172.0.0.1"
  type       = "ipsec.1"
}
```

```js
리소스 "aws_vpn_connection" "main" {
  vpn_gateway_id      = aws_vpn_gateway.vgw.id
  customer_gateway_id = aws_customer_gateway.gw.id
  type                = "ipsec.1"
  static_routes_only  = true
  tunnel1_ike_versions   = ["ikev2"]
  tunnel2_ike_versions   = ["ikev2"]
  tunnel1_phase1_dh_group_numbers = [31]
  tunnel2_phase1_dh_group_numbers = [31] 
}
# Azure 측
리소스 "azurerm_local_network_gateway" "lgw" {
  name                = "aws-conn"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  gateway_address = aws_customer_gateway.gw.ip_address
  address_space     = ["172.16.0.0/16"]
}
리소스 "azurerm_virtual_network_gateway_connection" "main" {
  name                       = "aws-conn"
  resource_group_name        = azurerm_resource_group.rg.name
  location                   = azurerm_resource_group.rg.location
  type                       = "IPsec"
  virtual_network_gateway_id = azurerm_virtual_network_gateway.vgw.id
  local_network_gateway_id   = azurerm_local_network_gateway.lgw.id
  shared_key = aws_vpn_connection.main.tunnel1_preshared_key
  
  ipsec_policy {
    dh_group         = "DHGroup31"
    ike_encryption   = "AES256"
    ike_integrity    = "SHA256"
    ipsec_encryption = "AES256"
    ipsec_integrity  = "SHA256"
    pfs_group        = "PFS31"
    sa_datasize      = 536870912
  }
}
```

이 방법을 통해 멀티 클라우드에 일관된 방식으로 전체 연결 구성을 정의할 수 있습니다.

# 배포 패턴
```  

<div class="content-ad"></div>

여러 클라우드로 배포할 때 블루-그린, 카나리아, 다중 지역과 같은 패턴을 사용하면 관리가 간단해질 수 있어요.

블루-그린

블루-그린은 새 버전을 병렬로 배포한 다음 트래픽을 원자적으로 전환합니다. 이는 롤백 및 점진적인 롤아웃 기능을 제공합니다.

예시:

<div class="content-ad"></div>

```js
# 파란 환경
module "blue_env" {
  source = "./env"
  color  = "blue"
}
```

```js
# 초록 환경
module "green_env" {
  source = "./env"
  color  = "green"
  # 처음에는 트래픽이 없음
  traffic_weight = 0
}
# 트래픽 분할기
resource "aws_lb" "main" {
  # 100%의 트래픽을 파란 쪽에 보냅니다.
}
```

그런 다음 로드 밸런서를 통해 점진적으로 파란 색에서 초록 색으로 트래픽을 이동합니다.

카나리아
```

<div class="content-ad"></div>

파란색-초록색처럼 변경되지만 처음에는 일부 사용자에게만 공개됩니다.

```js
module "prod_env" {
  source = "./env"
```

```js
  # 대부분의 트래픽이 본 프로덕션 환경으로 이동합니다.
}
module "canary_env" {
  source = "./env"
  # 소수의 트래픽이 canary로 이동합니다.
  traffic_weight = 0.1
}
```

Multi-Region

<div class="content-ad"></div>

고가용성 및 낮은 대기 시간을 위해 여러 지역에 리소스를 프로비저닝합니다.

예를 들어:

```js
# 서부 지역
module "west" {
  source = "./region"
  providers = {
    azurerm.west = azurerm.west
  }
}
```

```js
# 동부 지역 
module "east" {
  source = "./region" 
  providers = {
    azurerm.east = azurerm.east
  }
}
```

<div class="content-ad"></div>

그럼 DNS와 로드 밸런싱을 사용하여 전 세계적으로 분산하세요.

이러한 패턴은 멀티 클라우드 배포를 간단하게 해줍니다. 모듈 재사용은 일관성을 도와줍니다.

# 통합 테스트

여러 클라우드에 걸친 배포를 유효성 검사하려면 자동화된 통합 테스트가 필요합니다.

<div class="content-ad"></div>

테라폼을 사용한 멀티 클라우드 테스팅을 위한 몇 가지 최상의 방법:

- 인프라 테스트 — terraform plan 및 terraform show를 사용하여 올바른 구성의 리소스를 검증합니다.
- 프로비저닝 테스트 — 처음부터 일회용 테스트 환경을 배포합니다.
- 실패 테스트 — 종료된 인스턴스와 같은 실패를 시뮬레이션합니다.
- 서비스 테스트 — 목업을 사용하여 서비스 접근성과 동작을 유효성 검사합니다.

예를 들어, 일회용 테스트 환경을 생성하는 방법:

```js
module "test_env" {
  source = "./env"
  providers = {
    aws = aws.test
  }
}
```

<div class="content-ad"></div>

테이블 태그를 Markdown 형식으로 변경해주세요.

<div class="content-ad"></div>

다양한 환경에서 배포를 유효성 검사하려면 자동화된 테스트가 필요합니다.

Terraform을 사용하는 몇 가지 권장사항:

- 사용 및 폐기 가능한 테스트 환경 프로비저닝
- 리소스 구성 검증
- 다양한 장애 시나리오 시뮬레이션
- 의존성에 대한 모의 테스트 사용

예시:

<div class="content-ad"></div>

```js
module "test_env" {
  source = "./env"
```

```js
  # AWS 계정 자격 증명 테스트
}
resource "null_resource" "check_connectivity" {
  provisioner "local-exec" {
    command = "ping.exe -n 3 ${module.test_env.ip}"
  }
}
```

이렇게 하면 안전하게 변경 사항을 테스트할 수 있는 격리된 환경이 생성됩니다.

다른 예시:
```

<div class="content-ad"></div>

- 성능 테스트를 위해 과거 트래픽을 다시 재생합니다.
- 종료된 인스턴스와 같은 결함을 주입합니다.
- 결정론적 테스트를 위해 외부 서비스를 스텁 처리합니다.

자동화된 테스트는 구성 요소를 리팩토링할 때 자신감을 유지하는 데 중요합니다.

## 가시성

이질적인 클라우드 간 로그, 지표 및 추적에 대한 가시성을 확보하는 것은 어렵습니다.

<div class="content-ad"></div>

테라폼을 사용하면 관측 가능성 데이터를 공유 플랫폼에 집계할 수 있습니다:

로그 기능

```js
resource "aws_cloudwatch_log_group" "app" {
  name = "/aws/lambda/app"
}
resource "azurerm_monitor_diagnostic_setting" "app" {
  name               = "diag"
  target_resource_id = azurerm_function_app.app.id
  log_analytics_workspace_id = azurerm_log_analytics_workspace.main.id
  log {
    category = "FunctionAppLogs"
    enabled  = true
  }
}
resource "google_logging_project_sink" "app" {
  name        = "app-sink"
  destination = google_storage_bucket.logs.name  
}
```

Splunk와 같은 도구에서 로그를 중앙 집계하세요.

<div class="content-ad"></div>

메트릭

```js
리소스 "signalfx_detector" "지연시간" {
  이름 = "높은 지연시간"
  프로그램_텍스트 = <<-EOF
    A = 데이터('지연시간', 필터=필터('클라우드', '*') and 필터('환경', '*')).게시(label='A')
    B = (A).합계(by=['클라우드', '환경']).게시(label='B')
    detect(when(B > 1000, '5m')).게시('높은 지연시간!')
  EOF
}
```

Datadog와 같은 플랫폼에서 메트릭을 통합해보세요.

트레이싱

<div class="content-ad"></div>

```js
module "opentelemetry" {
  source = "./opentelemetry"
  providers = {
    aws = aws
    azure = azurerm
    google = google
  }
}
```

일반적인 형식으로 OpenTelemetry을 사용하면 추적을 쉽게 연결할 수 있어요.

테라폼을 사용하면 다양한 플랫폼 사이에서 공유 관찰 패턴을 구축할 수 있어요.

# 예시: 멀티-클라우드 웹 애플리케이션

<div class="content-ad"></div>

실제로 멀티 클라우드 웹 애플리케이션을 Terraform으로 배포하는 실제 사례를 살펴봅시다.

AWS ECS 및 Azure Container Instances (ACI)의 클러스터 전체에 애플리케이션 인프라를 배포할 것입니다. 글로벌 로드 밸런서가 트래픽을 플랫폼 간에 분배할 것입니다.

네트워킹

먼저, AWS VPC와 Azure VNet을 연결해야 합니다.

<div class="content-ad"></div>

```js
# AWS VPC 및 서브넷
resource "aws_vpc" "main" {
  cidr_block = "10.1.0.0/16"
}
```

```js
resource "aws_subnet" "public" {
  vpc_id = aws_vpc.main.id
  cidr_block = "10.1.1.0/24"
}
# Azure VNet 
resource "azurerm_virtual_network" "main" {
  name                = "app-network"
  address_space       = ["10.2.0.0/16"]
}
resource "azurerm_subnet" "public" {
  name                 = "public-subnet"
  resource_group_name  = azurerm_resource_group.main.name
  virtual_network_name = azurerm_virtual_network.main.name
  address_prefixes     = ["10.2.1.0/24"]
}
```

VPC 피어링을 통해 이들을 연결하십시오:

```js
# AWS 쪽 피어링
resource "aws_vpc_peering_connection" "peer" {
  vpc_id      = aws_vpc.main.id
  peer_vpc_id = azurerm_virtual_network.main.id
  auto_accept = true
}
```

<div class="content-ad"></div>

```js
# Azure 측 피어링
resource "azurerm_virtual_network_peering" "peer" {
  name                      = "peer-aws"
  resource_group_name       = azurerm_resource_group.main.name
  virtual_network_name      = az
```

컴퓨팅

Azure Container Instances 배포:

```js
resource "azurerm_container_group" "app" {
  name                = "app-aci"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
  ip_address_type     = "public"
  dns_name_label      = "app-aci"
  os_type             = "Linux"
```  

<div class="content-ad"></div>

```js
  container {
    name   = "app"
    image  = "myapp:v1" 
    cpu    = "1"
    memory = "1"
    ports {
      port     = 80
      protocol = "TCP"
    }
  }
```

아마존 ECS 클러스터 및 서비스:

```js
resource "aws_ecs_cluster" "main" {
  name = "myapp-cluster"
}
```

```js
resource "aws_ecs_service" "web" {
  name            = "web"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.app.arn
  desired_count   = 3
  launch_type     = "FARGATE"
}
```

<div class="content-ad"></div>

로드 밸런싱

각 클라우드에 백엔드가 있는 글로벌 로드 밸런서를 배포하십시오:

```js
resource "aws_lb" "web" {
  name               = "myapp-lb"
  internal           = false
  
  subnets = [
    aws_subnet.public.id
  ]
}
```

```js
resource "azurerm_lb" "web" {
  name                = "myapp-lb"
  location            = azurerm_resource_group.main.location
  resource_group_name = azurerm_resource_group.main.name
   
  frontend_ip_configuration {
    name                 = "public"
    public_ip_address_id = azurerm_public_ip.main.id
  }
}
```

<div class="content-ad"></div>

Markdown 포맷으로 DNS

로드 밸런서를 통해 단일 도메인을 라우팅합니다:

```js
resource "aws_route53_zone" "main" {
  name = "myapp.com"
}
```

```js
resource "aws_route53_record" "webapp" {
  zone_id = aws_route53_zone.main.id
  name    = "webapp.myapp.com"
  type = "CNAME"
  ttl  = "300"
  records = [aws_lb.web.dns_name]
}
resource "azurerm_dns_cname_record" "webapp" {
  name                = "webapp"
  zone_name           = azurerm_dns_zone.main.name
  resource_group_name = azurerm_resource_group.main.name
  ttl                 = 300
  record              = azurerm_lb.web.fqdn
}
```

<div class="content-ad"></div>

이는 웹 애플리케이션을 다중 클라우드에서 실행하기 위한 핵심 인프라를 제공하며, 로드 밸런싱과 DNS를 통해 연결됩니다.

테라폼 추상화는 우리에게 클라우드 플랫폼 간에 일관된 방식으로 아키텍처를 표현할 수 있는 기회를 제공합니다. 배포를 확장하고 필요에 따라 데이터베이스, 객체 저장소 및 캐시와 같은 추가 구성 요소를 추가할 수 있습니다.

# 결론

이 블로그에서는 테라폼을 사용하여 다중 클라우드 인프라, 네트워킹, 배포, 테스트 및 모니터링을 관리하는 패턴과 모범 사례를 다루었습니다.

<div class="content-ad"></div>

- 추상화 — 추상화된 공급자 및 리소스를 사용하여 이식 가능한 구성물 생성
- 모듈 — 복잡한 구성 요소를 재사용 가능한 모듈로 캡슐화
- 상태 — 협업 가능하도록 원격 상태 저장
- 네트워킹 — VPC와 VNET 간의 연결성 조정
- 배포 — 블루-그린, 카나리아, 다중 지역 패턴 사용
- 테스트 — 테스트 환경의 자동 프로비저닝
- 가시성 — 로그, 메트릭 및 추적을 중앙 집중화

테라폼은 각종 공용 클라우드, 개인 데이터 센터 및 SaaS 환경에서 인프라를 프로비저닝하고 관리하기 위한 일관된 워크플로우를 제공합니다. 이 가이드에서 다룬 패턴을 사용하면 다양한 API, 플랫폼 및 토폴로지를 연결하여 다중 클라우드 자동화 및 오케스트레이션을 더 쉽게 구현할 수 있습니다.