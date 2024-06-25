---
title: "DNS 성능을 모니터링하기 위해 알아야 할 모든 것"
description: ""
coverImage: "/assets/img/2024-06-19-EverythingyouneedtoknowaboutmonitoringCoreDNSforDNSperformance_0.png"
date: 2024-06-19 13:07
ogImage:
  url: /assets/img/2024-06-19-EverythingyouneedtoknowaboutmonitoringCoreDNSforDNSperformance_0.png
tag: Tech
originalTitle: "Everything you need to know about monitoring CoreDNS for DNS performance"
link: "https://medium.com/@seifeddinerajhi/everything-you-need-to-know-about-monitoring-coredns-for-dns-performance-9394f6f09b5a"
---

![CoreDNS Monitoring](/assets/img/2024-06-19-EverythingyouneedtoknowaboutmonitoringCoreDNSforDNSperformance_0.png)

# 📚 소개:

DNS 집중 워크로드를 실행할 때 종종 DNS 쓰로틀링에 의한 간헐적인 CoreDNS 실패가 발생할 수 있습니다. 이러한 문제는 애플리케이션에 중대한 영향을 미칠 수 있습니다.

이러한 중단은 서비스의 신뢰성과 성능에 영향을 미칠 수 있으므로 모니터링 솔루션이 필수적입니다.

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

AWS는 모니터링 목적으로 통합할 수 있는 오픈 소스 도구인 CloudWatch, Fluentd 및 Grafana를 제공합니다. 이 도구들은 CoreDNS를 모니터링하는 데 사용할 수 있습니다.

# Kubernetes DNS 소개:

Kubernetes는 클러스터 내에서 서비스 검색에 DNS를 의존합니다. 팟에서 실행되는 응용 프로그램이 서로 통신해야 할 때, 그들은 주로 IP 주소가 아닌 도메인 이름을 사용하여 서비스를 참조합니다.

이 때 Kubernetes DNS가 필요합니다. 이는 도메인 이름이 올바른 IP 주소로 해석되도록 보장하여 팟과 서비스가 통신할 수 있도록 합니다.

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

Kubernetes에서는 각 파드마다 일시적인 IP 주소가 할당됩니다. 그러나 이러한 IP 주소는 동적이며 시간이 지남에 따라 변경될 수 있어, 애플리케이션이 이를 추적하기 어렵습니다.

Kubernetes는 이러한 도전에 대응하기 위해 파드와 서비스에 완전히 정규화된 도메인 이름(FQDNs)을 할당합니다.

Kubernetes의 기본 DNS 제공자인 CoreDNS는 클러스터 내에서 DNS 쿼리를 처리하는 역할을 담당합니다. 그는 이러한 FQDN을 해당 IP 주소로 매핑하여 파드와 서비스 간의 통신을 가능하게 합니다.

# 왜 DNS 문제가 흔할까요:

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

네트워크 문제 해결 중 공통적으로 발생하는 번거로움의 원인 중 하나인 DNS 문제! DNS는 사람이 읽기 쉬운 도메인 이름을 기계가 이해할 수 있는 IP 주소로 변환하는 데 큰 역할을 합니다.

그러나 DNS 문제는 설정 오류, 네트워크 문제 또는 서버 장애와 같은 여러 요인으로 발생할 수 있습니다. 도메인 이름을 올바르게 해석하지 못할 때 애플리케이션이 외부 서비스에 연결 문제를 경험하거나 액세스에 실패할 수 있습니다.

# 쿠버네티스의 CoreDNS:

CoreDNS는 쿠버네티스 클러스터 내에서 DNS 서비스를 제공하는 데 중요한 역할을 합니다. Kubernetes v1.13 이후 기본 DNS 제공자로 사용되어 온 CoreDNS는 DNS 이름 대신 IP 주소 대신 DNS 이름을 사용하여 클라이언트가 서비스에 액세스할 수 있도록 함으로써 클러스터 네트워킹을 간소화합니다. 그는 도메인 이름 요청을 해결하고 클러스터 내에서 서비스 검색을 용이하게 합니다.

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

# 코어디엔에스의 작동 방식:

코어디엔에스는 쿠버네티스 클러스터 내에서 DNS 요청의 리졸버 및 포워더로 작동합니다. 파드가 다른 서비스와 통신해야 할 때, 대상 서비스의 도메인 이름을 지정하여 DNS 쿼리를 코어디엔에스에 보냅니다. 그럼 코어디엔에스는 내부 레코드를 사용하여 도메인 이름을 해당 IP 주소로 매핑하여 이 쿼리를 해결합니다.

코어디엔에스가 권한이 없는 외부 도메인 이름에 대해서는, 이를 공개 리졸버나 상위 DNS 서버로 전달하여 해결합니다.

성능을 향상시키고 대기 시간을 줄이기 위해 코어디엔에스는 자주 액세스하는 도메인 이름에 대한 DNS 응답을 캐시할 수 있습니다. 이 캐싱 메커니즘은 DNS 쿼리의 응답 속도를 향상시키고 상위 DNS 서버의 부하를 줄입니다.

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

CoreDNS는 이 기능을 모듈식 아키텍처와 확장 가능한 플러그인 시스템을 통해 구현하며, 운영자가 독자적인 요구 사항에 따라 DNS 해상도를 사용자 정의하고 최적화할 수 있게 합니다.

# Amazon EKS에서의 CoreDNS 쓰로틀링 완화 방법:

Amazon EKS 클러스터에서 CoreDNS와 DNS 쓰로틀링 문제는 식별하고 해결하기 어려울 수 있습니다. 많은 사용자가 CoreDNS 로그와 메트릭을 모니터링하는 데 주력하지만, 엘라스틱 네트워크 인터페이스(ENI) 수준에서 강제되는 초당 1024개 패킷(PPS)의 하드 제한을 자주 간과합니다. 이 한계가 쓰로틀링 문제로 이어질 수 있는 방식을 이해하려면 Kubernetes 파드의 전형적인 DNS 해상도 흐름에 대한 통찰력이 필요합니다.

Kubernetes 환경에서는 파드가 통신을 가능하게 하기 위해 내부 및 외부 서비스의 도메인 이름을 해상해야 합니다. 이 해상도 프로세스는 DNS 쿼리를 워커 노드의 ENI를 통해 라우팅하는 것을 포함하며, 특히 외부 엔드포인트를 해상하는 경우입니다. 내부 엔드포인트의 경우에도 쿼리하는 파드와 동일한 위치에 CoreDNS 파드가 없으면 DNS 패킷이 여전히 워커 노드의 ENI를 통해 이동합니다.

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

갑자기 DNS 쿼리가 급증하여 PPS가 하드 제한값 1024에 접근하는 상황이 발생할 수 있습니다. 이러한 상황은 DNS 쓰로틀링을 유발할 수 있으며, 이로 인해 영향을 받는 작업 노드에서 실행 중인 모든 마이크로서비스에 영향을 미칠 수 있습니다. 유감스럽게도, 이러한 문제 해결은 주로 CoreDNS pod에 초점을 맞추는 ENI 메트릭보다 어려울 수 있습니다.

EKS 클러스터에서 DNS 쓰로틀링 문제를 완화하기 위해서는 ENI 수준에서 발생하는 패킷 손실을 지속적으로 모니터링하는 것이 중요합니다. 이 모니터링을 통해 잠재적인 중단을 조기에 감지하고 예방할 수 있습니다. 이 블로그 포스트에서는 네트워크 성능 메트릭을 활용하여 DNS 쓰로틀링 문제를 효과적으로 식별하는 솔루션을 소개합니다.

## 해결책: 🎉

작업 노드에서 DNS 쓰로틀링 문제를 식별하는 간단한 방법은 Elastic Network Adapter (ENA) 드라이버에서 제공하는 linklocal_allowance_exceeded 메트릭 및 다른 메트릭을 캡처하는 것입니다.

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

`linklocal_allowance_exceeded`은 로컬 프록시 서비스로의 트래픽 PPS가 네트워크 인터페이스의 최대를 초과하여 드롭된 패킷 수입니다. 이는 DNS 서비스, 인스턴스 메타데이터 서비스 및 Amazon 시간 동기화 서비스로의 트래픽에 영향을 줍니다.

이 이벤트를 실시간으로 추적하는 대신, 우리는 이 메트릭을 Amazon Managed Service for Prometheus로 스트리밍하고 Amazon Managed Grafana에서 시각화할 수도 있습니다.

![이미지](/assets/img/2024-06-19-EverythingyouneedtoknowaboutmonitoringCoreDNSforDNSperformance_1.png)

# 실전: AWS EKS에서 CoreDNS 메트릭 수집 및 시각화:

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

CoreDNS 프로메테우스 플러그인은 OpenMetrics 형식으로 메트릭을 노출하며, 이는 프로메테우스 형식에서 발전한 텍스트 기반 표준입니다. Kubernetes 클러스터에서는 플러그인이 기본적으로 활성화되어 있어 클러스터를 시작하는 즉시 많은 중요한 메트릭을 모니터링할 수 있습니다.

기본 설정에서 프로메테우스 플러그인은 각 CoreDNS 팟의 포트 9153에 있는 /metrics 엔드포인트에 메트릭을 기록합니다.

Amazon Managed Service for Prometheus 워크스페이스와 Managed Service for Grafana를 생성하세요:

이 단계에서는 Amazon Managed Service for Prometheus 및 Managed Service for Grafana를 위한 워크스페이스를 생성합니다.

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

이 파일 구성은 다음을 생성합니다:

- AMP 작업 공간
- AMP 경보 관리자 정의.

main.tf:

```js
module "prometheus" {
  source = "terraform-aws-modules/managed-service-prometheus/aws"

  workspace_alias = "demo-coredns"

  alert_manager_definition = <<-EOT
  alertmanager_config: |
    route:
      receiver: 'default'
    receivers:
      - name: 'default'
  EOT

  rule_group_namespaces = {}
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

versions.tf:

```js
terraform {
  required_version = ">= 1.3"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 5.32"
    }
  }
}
```

테라폼을 실행하려면 다음을 실행해야 합니다:

```js
$ terraform init
$ terraform plan
$ terraform apply
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

아래 구성 파일은 다음과 같은 것을 생성합니다:

- 기본 Grafana 작업 공간 (모듈에서 제공하는 기본값 사용)

main.tf:

```js
provider "aws" {
  region = local.region
}

data "aws_availability_zones" "available" {}

locals {
  region      = "eu-west-1"
  name        = "amg-ex-${replace(basename(path.cwd), "_", "-")}"
  description = "AWS Managed Grafana service for ${local.name}"

  vpc_cidr = "10.0.0.0/16"
  azs      = slice(data.aws_availability_zones.available.names, 0, 3)
}

################################################################################
# Managed Grafana Module
################################################################################

module "managed_grafana" {
  source = "terraform-aws-modules/managed-service-grafana/aws"

  # Workspace
  name                      = local.name
  associate_license         = false
  description               = local.description
  account_access_type       = "CURRENT_ACCOUNT"
  authentication_providers  = ["AWS_SSO"]
  permission_type           = "SERVICE_MANAGED"
  data_sources              = ["CLOUDWATCH", "PROMETHEUS", "XRAY"]
  notification_destinations = ["SNS"]
  stack_set_name            = local.name
  grafana_version           = "9.4"

  configuration = jsonencode({
    unifiedAlerting = {
      enabled = true
    },
    plugins = {
      pluginAdminEnabled = false
    }
  })

  # vpc configuration
  vpc_configuration = {
    subnet_ids = module.vpc.private_subnets
  }
  security_group_rules = {
    egress_postgresql = {
      description = "Allow egress to PostgreSQL"
      from_port   = 5432
      to_port     = 5432
      protocol    = "tcp"
      cidr_blocks = module.vpc.private_subnets_cidr_blocks
    }
  }

  # Workspace API keys
  workspace_api_keys = {
    viewer = {
      key_name        = "viewer"
      key_role        = "VIEWER"
      seconds_to_live = 3600
    }
    editor = {
      key_name        = "editor"
      key_role        = "EDITOR"
      seconds_to_live = 3600
    }
    admin = {
      key_name        = "admin"
      key_role        = "ADMIN"
      seconds_to_live = 3600
    }
  }

  # Workspace IAM role
  create_iam_role                = true
  iam_role_name                  = local.name
  use_iam_role_name_prefix       = true
  iam_role_description           = local.description
  iam_role_path                  = "/grafana/"
  iam_role_force_detach_policies = true
  iam_role_max_session_duration  = 7200
  iam_role_tags                  = { role = true }


  tags = local.tags
}

module "managed_grafana_default" {
  source = "terraform-aws-modules/managed-service-grafana/aws"

  name              = "${local.name}-default"
  associate_license = false

  tags = local.tags
}

module "managed_grafana_disabled" {
  source = "terraform-aws-modules/managed-service-grafana/aws"

  name   = local.name
  create = false
}

################################################################################
# Supporting Resources
################################################################################

module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"

  name = local.name
  cidr = local.vpc_cidr

  azs             = local.azs
  private_subnets = [for k, v in local.azs : cidrsubnet(local.vpc_cidr, 4, k)]
  public_subnets  = [for k, v in local.azs : cidrsubnet(local.vpc_cidr, 8, k + 48)]

  enable_nat_gateway = false
  single_nat_gateway = true

  tags = local.tags
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

"versions.tf" 파일입니다:

```js
terraform {
  required_version = ">= 1.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 5.0"
    }
  }
}
```

이 코드를 실행하려면 다음을 실행해야 합니다:

```js
$ terraform init
$ terraform plan
$ terraform apply
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

프로메테우스 ethtool 익스포터 배치:

ethtool은 워커 노드의 이더넷 장치에 대한 정보를 구성하고 수집하는 리눅스 도구입니다. 우리는 ethtool의 출력을 사용하여 패킷 손실을 감지하고 이를 프로메테우스 ethtool 익스포터 유틸리티를 사용하여 프로메테우스 형식으로 변환할 것입니다.

배포에는 ethtool에서 정보를 가져 와서 프로메테우스 형식으로 게시하는 Python 스크립트가 포함되어 있습니다.

```js
kubectl apply -f https://raw.githubusercontent.com/Showmax/prometheus-ethtool-exporter/master/deploy/k8s-daemonset.yaml
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

이제 ADOT 수집기를 배포하고 ADOT 수집기를 구성하여 Amazon Managed Service for Prometheus로 메트릭을 수집할 것입니다.

우리는 Amazon EKS 애드온을 사용하여 ADOT 오퍼레이터를 CoreDNS 모니터링을 위해 메트릭 "linklocal_allowance_exceeded"를 Amazon Managed Service for Prometheus로 보내게 될 것입니다.

IAM 역할과 Amazon EKS 서비스 계정을 생성하세요.

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

ADOT 수집기를 Kubernetes 서비스 계정 "adot-collector"의 신원으로 배포할 예정입니다. 서비스 계정에 IAM 역할(IRSA)을 연결하여 Kubernetes 서비스 계정에 AmazonPrometheusRemoteWriteAccess 역할을 할당함으로써 해당 서비스 계정을 활용하는 모든 파드가 Amazon Managed Service for Prometheus에 메트릭을 수집하는 데 필요한 IAM 권한을 부여할 수 있습니다.

스크립트를 실행하려면 kubectl 및 eksctl CLI 도구가 필요합니다. 이 도구들은 Amazon EKS 클러스터에 액세스할 수 있도록 구성되어 있어야 합니다.

```js
eksctl create iamserviceaccount \
--name adot-collector \
--namespace default \
--region eu-west-1\
--cluster coredns-monitoring-demo\
--attach-policy-arn arn:aws:iam::aws:policy/AmazonPrometheusRemoteWriteAccess \
--approve \
--override-existing-serviceaccounts
```

ADOT 애드온 설치하기:

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

여러분은 다음 명령어를 사용하여 Amazon EKS의 다른 버전에 활성화된 애드온 목록을 확인할 수 있습니다:

클러스터 버전에서 지원하는 ADOT 버전을 확인하십시오.

```js
aws eks describe-addon-versions --addon-name adot --kubernetes-version 1.28 \
  --query "addons[].addonVersions[].[addonVersion, compatibilities[].defaultVersion]" --output text
```

다음 명령어를 실행하여 ADOT 애드온을 설치하십시오. 위 단계에서 기술된 것에 따라 --addon-version 플래그를 교체하십시오.

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
aws eks create-addon --addon-name adot --addon-version v0.66.0-eksbuild.1 --cluster-name coredns-monitoring-demo
```

다음 명령어를 사용하여 ADOT 애드온이 준비되었는지 확인하세요.

```js
kubectl get po -n opentelemetry-operator-system
```

다음 절차는 배포를 모드 값으로 사용하는 예제 YAML 파일을 사용합니다. 이는 기본 모드이며 ADOT Collector를 독립 애플리케이션과 유사하게 배포합니다. 이 구성은 샘플 애플리케이션으로부터 OTLP 메트릭을 수신하고 클러스터의 pod에서 스크래핑된 Amazon Managed Service for Prometheus 메트릭을 수신합니다.

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

curl -o collector-config-amp.yaml https://raw.githubusercontent.com/aws-observability/aws-otel-community/master/sample-configs/operator/collector-config-amp.yaml

collector-config-amp.yaml 파일에서 다음을 본인의 값으로 바꿔주세요: _ mode: deployment _ serviceAccount: adot-collector _ endpoint: "" _ region: "" \* name: adot-collector

kubectl apply -f collector-config-amp.yaml

adot collector가 배포되면 메트릭이 Amazon Prometheus에 성공적으로 저장됩니다.

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

Amazon Managed Grafana에서 ethtool 메트릭을 시각화해보세요:

Amazon Managed Grafana 콘솔 내에서 Prometheus 워크스페이스를 데이터 소스로 구성합니다.

이제 Amazon Managed Grafana에서 메트릭을 살펴봅시다: "탐색" 버튼을 클릭한 후 ethtool을 검색하세요:

![이미지](/assets/img/2024-06-19-EverythingyouneedtoknowaboutmonitoringCoreDNSforDNSperformance_2.png)

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

링크로컬 할당 초과 메트릭을 사용하여 대시보드를 만들어 봅시다. 쿼리는 다음과 같습니다.

```js
rate(node_net_ethtool{device="eth0",type="linklocal_allowance_exceeded"} [30s])
```

![이미지](/assets/img/2024-06-19-EverythingyouneedtoknowaboutmonitoringCoreDNSforDNSperformance_3.png)

값이 0이기 때문에 드랍된 패킷이 없다는 것을 확인할 수 있습니다. Amazon Managed Service for Prometheus의 경고 관리자에서 알림을 구성하여 알림을 보낼 수 있습니다.

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

# 결론:

이 글에서는 AWS Distro for OpenTelemetry (ADOT), Amazon Managed Service for Prometheus 및 Amazon Managed Grafana를 사용하여 CoreDNS 쓰로틀링 문제를 모니터링하고 경고를 생성하는 방법을 보여드렸습니다. CoreDNS 메트릭을 모니터링함으로써 고객은 패킷 손실을 사전에 감지하고 예방 조치를 취할 수 있습니다.

다음에 또 만나요 🇵🇸 🎉

![이미지](/assets/img/2024-06-19-EverythingyouneedtoknowaboutmonitoringCoreDNSforDNSperformance_4.png)

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

읽어 주셔서 감사합니다!! 🙌🏻😁📃, 다음 블로그에서 만나요.🤘🇵🇸

🚀 끝까지 함께해 줘서 감사합니다. 이 블로그에 관한 질문/피드백이 있으면 언제든지 연락해 주세요:

♻️ 🇵🇸LinkedIn: https://www.linkedin.com/in/rajhi-saif/

♻️🇵🇸 Twitter : https://twitter.com/rajhisaifeddine

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

끝! ✌🏻

# 🔰 계속 배우고!! 계속 공유해요!! 🔰

# 참고:

[https://aws.amazon.com/blogs/mt/monitoring-coredns-for-dns-throttling-issues-using-aws-open-source-monitoring-services/](https://aws.amazon.com/blogs/mt/monitoring-coredns-for-dns-throttling-issues-using-aws-open-source-monitoring-services/)
