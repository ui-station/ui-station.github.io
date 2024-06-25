---
title: "테라스캔Terrascan이란 무엇인가요"
description: ""
coverImage: "/assets/img/2024-05-23-WhatisTerrascan_0.png"
date: 2024-05-23 14:39
ogImage:
  url: /assets/img/2024-05-23-WhatisTerrascan_0.png
tag: Tech
originalTitle: "What is Terrascan?"
link: "https://medium.com/devops-dev/what-is-terrascan-26d53f2051e6"
---

이 기사에서는 Terrascan을 살펴볼 것입니다. 이것이 무엇인지, 왜 사용해야 하는지, 어떻게 설치해야 하는지, 그리고 그 기능을 활용하는 방법에 대해 알아볼 것입니다. 우리는 일부 사용 사례 예제를 살펴보고, Terraform, Kubernetes (K8S) 및 Helm 차트를 스캔하는 방법을 보여줄 것입니다. 그리고 사용자 정의 정책을 정의하는 방법을 살펴보고, 마지막으로 Terrascan이 Chekov 및 TFSec와 같은 다른 유사한 제품과 비교되는 방법을 자세히 살펴볼 것입니다.

![What is Terrascan](/assets/img/2024-05-23-WhatisTerrascan_0.png)

## Terrascan이란 무엇인가요?

Terrascan은 인프라를 코드로 정의하는(Infrastructure as Code, IaC)를 위한 오픈 소스 정적 코드 분석 도구입니다. Terrascan 웹사이트 태그라인이 Terrascan을 요약합니다:

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

여기의 핵심 키워드 중 하나는 'before'입니다. Terrascan 도구의 목표는 인프라가 프로비저닝되기 전에 규정 준수나 보안 문제를 알려줌으로써 이를 해결하여 문제를 피하는 것입니다.

Terrascan은 Tenable이 소유하고 있으며, 이 회사는 Cloud Native Computing Foundation (CNCF) 및 Open Source Security Foundation (OpenSSF)의 회원입니다.

## Terrascan의 사용 목적

Terrascan은 개발자와 DevOps 팀이 인프라 코드가 모범 사례, 보안 표준 및 규정 요구 사항을 준수하는지를 보장하는 데 도움을 줍니다. Terrascan은 500개 이상의 미리 설정된 정책을 제공하여 CIS Benchmark와 같은 공통 정책 표준에 대해 IaC를 검사할 수 있습니다. Terrascan을 처음 사용할 때 -p 플래그가 지정되지 않은 경우, Terrascan은 Terrascan 리포지토리에서 최신 정책을 다운로드합니다. terrascan init를 실행하여 로컬 환경을 리포지토리에 게시된 최신 정책으로 업데이트할 수 있습니다.

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

## Terrascan 특징

- Terrascan은 AWS, Azure, Google Cloud Platform (GCP), K8S, ArgoCD, Atlantis, GitHub, Docker을 포함한 다양한 클라우드 제공업체 및 코드 유형과 함께 사용할 수 있어 교차 클라우드 인프라 배포에 다재다능합니다.
- Terrascan은 사용자 정의 가능한 사전 정의된 보안 및 규정 준수 규칙 라이브러리를 제공합니다. 이러한 규칙은 암호화, 액세스 제어, 리소스 구성 등 인프라 보안 및 규정 준수의 다양한 측면을 포함합니다. 기본적으로 사용되는 정책을 자세히 보려면 terrascan 정책 문서 페이지를 확인할 수 있습니다.
- Terrascan은 CI/CD 파이프라인, IDE(통합 개발 환경) 및 다른 개발 및 배포 워크플로에 통합할 수 있습니다. 이를 통해 IaC 코드의 자동 스캔 및 유효성 검사가 개발 프로세스의 일부로 이루어질 수 있습니다.
- Terrascan은 클라우드 인프라의 지속적인 모니터링에 사용될 수 있습니다. 취약점이나 규정 준수 문제를 발생시킬 수 있는 변질이나 구성 변경을 정기적으로 검사하고 확인할 수 있도록 설정할 수 있습니다.
- 보고 및 개선: Terrascan은 분석 중 발견된 문제를 강조하는 자세한 보고서를 생성합니다. 또한 이러한 문제를 어떻게 개선할지에 대한 지침을 제공하여 팀이 보안 및 규정 준수 문제를 효과적으로 해결할 수 있도록 도와줍니다.

## Terrascan 설치 방법

- Windows

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

인기 있는 Windows 패키지 관리자 Chocolately를 사용하거나 수동으로 다운로드하여 설치할 수 있어요.

```js
choco install terrascan
```

릴리스 페이지에서 Terrascan을 다운로드하세요:

zip 파일에서 파일을 추출하고 Windows 경로에 추가하세요.

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

- 시작 단추를 마우스 오른쪽 버튼으로 클릭합니다.
- 콘텍스트 메뉴에서 “시스템”을 선택합니다.
- “고급 시스템 설정”을 클릭합니다.
- “고급” 탭으로 이동합니다.
- “환경 변수…”를 클릭합니다.
- “Path”라는 변수를 클릭하고 “편집…”을 클릭합니다.
- “새로 만들기”를 클릭합니다.
- PATH에 포함하려는 Terrascan 파일이 있는 폴더의 경로를 입력하세요.

- MacOS

```js
curl -L "$(curl -s https://api.github.com/repos/tenable/terrascan/releases/latest | grep -o -E "https://.+?_Darwin_x86_64.tar.gz")" > terrascan.tar.gz
tar -xf terrascan.tar.gz terrascan && rm terrascan.tar.gz
install terrascan /usr/local/bin && rm terrascan
sudo install terrascan /usr/local/bin
```

- Linux

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
curl -L "$(curl -s https://api.github.com/repos/tenable/terrascan/releases/latest | grep -o -E "https://.+?_Linux_x86_64.tar.gz")" > terrascan.tar.gz
tar -xf terrascan.tar.gz terrascan && rm terrascan.tar.gz
install terrascan /usr/local/bin && rm terrascan
sudo install terrascan /usr/local/bin
```

- 도커

도커 허브에서 이미지를 다음과 같이 가져올 수도 있습니다:

```js
$ docker run --rm tenable/terrascan version
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

테라스캔을 설치한 후 설치 여부를 확인하기 위해 명령줄에서 테라스캔을 실행하세요.

![Terrascan](/assets/img/2024-05-23-WhatisTerrascan_1.png)

## IaC 코드를 스캔하는 방법

코드를 스캔하려면 구성 파일이 있는 폴더로 이동한 후 다음 명령을 실행하세요:

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
terrascan scan
```

--c 옵션을 사용하여 스캔할 디렉토리를 지정할 수도 있어요.

## Terrascan 사용 사례 - 예시

Terrascan을 여러 유형의 구성 파일과 함께 사용할 수 있어요. 여기에 몇 가지 예시가 있어요. 이를 통해 쉽게 시작할 수 있을 거예요.

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

## 예제 1 — Terraform 코드 스캔

Azure를 위한 예제 Terraform 설정 파일이 있습니다. 이 파일은 간단히 리소스 그룹을 생성합니다:

main.tf

```js
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "East US"
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

파일을 저장한 후 해당 디렉토리로 이동하여 terrascan scan을 실행하십시오. Terrascan이 1개의 정책 위반을 감지하고 리소스 그룹에 리소스 잠금을 활성화해야 한다고 권장함을 볼 수 있어요. 이 문제는 LOW 심각도로 표시돼요.

![Terrascan Screenshot](/assets/img/2024-05-23-WhatisTerrascan_2.png)

## 예제 2 - Helm 차트 스캔

Helm 차트를 사용하여 Terrascan을 테스트하기 위해 demo-chart라는 새 차트를 만들어보겠어요:

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
helm create demo-chart
```

이제 values.yaml 파일을 편집하여 Terrascan이 보고할 몇 가지 추가 취약점을 추가해보겠습니다.

<img src="/assets/img/2024-05-23-WhatisTerrascan_3.png" />

values.yaml 파일이 있는 디렉토리로 이동한 다음 terrascan scan을 실행하세요. 보고된 위반 정책이 여러 개 표시되어야 합니다. 여기에는 위에 securityContext 라인을 추가하여 발생시킨 HIGH 심각도의 2개의 정책 위반이 포함될 것입니다.

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

<img src="/assets/img/2024-05-23-WhatisTerrascan_4.png" />

## 예제 3 — 쿠버네티스 매니페스트 스캔하기

K8S 매니페스트를 스캔하는 방법을 테스트하기 위해 간단한 nginx 배포용 파일을 작성해보겠습니다:

nginx.yaml

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

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

nginx.yaml 파일이 있는 폴더로 이동하여 terrascan scan을 실행해주세요.

Terrascan에서 여러 권장 사항을 보게 될 것인데, 그 중에는 2개의 HIGH 우선 순위 이슈('Minimize Admission of Root Containers' 및 'Containers Should Not Run with AllowPrivilegeEscalation')도 포함되어 있습니다.

<img src="/assets/img/2024-05-23-WhatisTerrascan_5.png" />

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

## 예제 4 — Terrascan을 ArgoCD와 통합하기

Terrascan은 인기있는 CI/CD 시스템인 Azure DevOps, GitHub, GitLab 및 Argo와 통합할 수 있습니다. ArgoCD의 경우, Terrascan은 ArgoCD의 리소스 후크를 사용하여 응용 프로그램 사전 동기화 프로세스 중 Argo CD 작업으로 구성될 수 있습니다. 또한 Terrascan의 K8S admission 컨트롤러를 사용하여 사전 동기화 및 컨트롤러 웹훅과 함께 구성된 저장소를 스캔할 수 있습니다.

Terrascan 문서 페이지에서 전체 통합 예시를 찾을 수 있습니다.

## Terrascan 사용자 정의 정책

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

Terrascan은 사용자가 Rego 쿼리 언어를 사용하여 쉽게 사용자 정의 정책을 만들 수 있도록 Open Policy Agent (OPA) 엔진을 활용합니다. 각 rego 정책에는 정책에 대한 메타데이터를 정의하는 JSON "rule" 파일이 포함되어 있습니다. Terrascan에 포함된 정책은 pkg/policies/opa/rego 디렉토리에 저장됩니다. rule.json 파일은 Rego 파일에서 정의된 사용자 정의 정책을 참조하는 구성 파일로, Terrascan 스캔 중에 어떤 정책이 적용되고 심각성 수준이 어떻게되는지를 제어할 수 있습니다. 사용자 정책 로직 및 규칙은 .rego 파일에 정의되며, rule.json 파일은 해당 정책을 적용하고 구성하는 방법을 지정합니다.

아래 정책 예시는 Azure 리소스가 "UK South" 또는 "UK West"에만 있어야 한다는 것을 강제합니다. 다른 위치에서 리소스를 찾으면 Terrascan에서 보고됩니다.

azure_region_policy.rego

```js
package main

import input.tfplan as tfplan

default allow = false

allowed_regions = ["UK South", "UK West"]

# 테라폼 계획의 모든 Azure 리소스를 반복합니다.
azure_resources[resource_name] {
    resource_name = input.tfplan.resource_changes[_].address
    input.tfplan.resource_changes[_].type == "azurerm_resource"
}

# 각 Azure 리소스의 지역이 허용되었는지 확인합니다.
allow {
    resource_name
    resource_config := input.tfplan.resource_changes[resource_name].change.after
    resource_config.location == allowed_region
    allowed_region = allowed_regions[_]
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

사용자 정책을 활성화하려면 rule.json 파일을 사용하여 Terrascan의 동작을 구성하고 스캔 중에 적용해야 할 정책을 지정합니다.

rule.json

```js
{
  "rules": {
    "azure_region_policy": {
      "severity": "HIGH",
      "message": "'UK South' 또는 'UK West' 지역에 Azure 리소스를 배포해야 합니다.",
      "rules_file": "azure_region_policy.rego"
    }
  }
}
```

마지막으로 사용자 정책을 사용하여 스캔하려면 스캔하려는 디렉토리로 이동하고 -rules 플래그를 사용하여 json 파일 경로를 지정하십시오.

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
terrascan scan -rules /path/to/rule.json
```

또한 Terrascan은 원하는 경우 정책을 준수하지 않으려면 해당 정책을 제외할 수도 있습니다. 예를 들어, 예시 1에서 구성한 Azure 리소스 그룹에 리소스 잠금을 사용하고 싶지 않다면 이 정책을 스캔에서 제외하여 이 문제로 표시되지 않게 할 수 있습니다. 이를 위해 -skip-rules 플래그를 사용하거나 특정 리소스에서 정책을 건너뛰는 파일 내부 기능을 사용합니다.

## Terrascan 대 Checkov

외관상으로 보면, Terrascan과 Checkov는 매우 유사합니다. 둘 다 IaC 보안 및 규정 준수 스캔을 위해 설계된 오픈 소스 정적 코드 분석 도구입니다.

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

Checkov은 Terraform에 주로 초점을 맞추고 있지만, AWS SAM을 포함한 CloudFormation, Azure Resource Manager (ARM), Serverless framework, Helm 차트, K8S 및 Docker와 같은 여러 유형의 파일을 스캔할 수 있습니다. Checkov는 활발한 기여 및 DevSecOps 커뮤니티에서 강력한 존재감을 보이는 커뮤니티 주도 프로젝트입니다.

Checkov는 Python을 사용하여 구축되었으며, Terrascan은 Go를 사용하며 Rego를 사용하여 사용자 정의 정책을 작성합니다. 사용자 정의 정책을 작성하는 데 Rego를 선호한다면 Terrascan은 좋은 선택입니다.

## Terrascan vs tfsec

Tfsec는 또 다른 오픈 소스 정적 코드 분석 도구이며, 고려할 수 있는 주요 옵션 중 하나입니다.

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

Terrascan과 달리 TFSec는 GO로 작성되었으며 사용자 정의 정책에는 Terrascan에서 사용되는 Rego 대신 YAML 정의를 사용합니다. K8S에 익숙한 사용자는 Rego 학습 대신 YAML을 사용하여 정책을 작성하는 것을 선호할 수 있습니다. 이는 TFSec를 선택하는 이유가 될 수 있습니다. 또한 커뮤니티에서 Checkov 및 Terrascan보다 가장 인기 있는 프로젝트이며 가장 많은 GitHub 스타를 가지고 있습니다.

## 주요 포인트

Terrascan은 IaC 템플릿 및 구성을 스캔하는 데 설계된 매우 유연하고 강력한 오픈 소스 정적 코드 분석 도구입니다. IaC 코드에서 보안 취약점, 준수 위반 및 최상의 실천 방법 문제를 식별하는 데 도움을 줍니다. 다양한 유형의 구성 파일과 함께 사용할 수 있으며 내장 정책을 제공하며 Rego를 사용하여 사용자 정의 정책을 사용할 수도 있습니다. CI/CD 시스템과 자동화 파이프라인에서 사용하는 것이 지원됩니다.

여기에서 Terraform에 관한 다른 기사들도 확인해보세요!

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

환호! 🍻

원래 spacelift.io에서 발행되었습니다.
