---
title: "Google Cloud에서 Terraform을 사용하여 인프라 구축하기 - Google Challenge Lab 안내"
description: ""
coverImage: "/assets/img/2024-05-20-BuildInfrastructureonGoogleCloudwithTerraformGoogleChallengeLabWalkthrough_0.png"
date: 2024-05-20 17:28
ogImage: 
  url: /assets/img/2024-05-20-BuildInfrastructureonGoogleCloudwithTerraformGoogleChallengeLabWalkthrough_0.png
tag: Tech
originalTitle: "Build Infrastructure on Google Cloud with Terraform — Google Challenge Lab Walkthrough"
link: "https://medium.com/google-cloud/build-infrastructure-on-google-cloud-with-terraform-google-challenge-lab-walkthrough-30a592373d3e"
---


구글 클라우드에서 Terraform으로 인프라를 구축하는 코스의 챌린지 랩 워크스루입니다.

이 랩은 다음을 테스트합니다:

- 기존 인프라를 Terraform 구성으로 가져오는 능력.
- 직접 Terraform 모듈을 빌드하고 참조하는 것.
- 원격 백엔드를 구성에 추가하는 것.
- Terraform 레지스트리에서 모듈을 사용하고 구현하는 것.
- 인프라를 다시 프로비저닝하고 파괴하고 업데이트하는 것.
- 생성한 리소스 간의 연결성을 테스트하는 것.

# 챌린지 랩 소개

<div class="content-ad"></div>

Google은 구글 클라우드 스킬즈 부스트(이전 명칭: QwikLabs)라는 온라인 학습 플랫폼을 제공합니다. 이 플랫폼에서는 학습 경로에 맞는 교육 과정을 따르거나 특정 제품 또는 솔루션에 대한 교육 과정을 진행할 수 있습니다.

이 플랫폼에서의 학습 경험 중 하나는 퀘스트입니다. 퀘스트에 참여하면 안내형 실습 랩을 수행한 후 도전 랩을 완료합니다. 도전 랩은 목표가 명시되지만 목표를 달성하는 방법에 대한 안내가 거의 없는 다른 랩과는 다릅니다.

제가 가끔 이 도전 랩에 대한 해결 방법을 제공합니다. 목표는 도전 랩을 도와주어서 쉽게 풀어내는 데 도움을 주는 것이 아닙니다! 대신에:

- 랩을 완료하는 데 가장 이상적인 경로로 이끄는 것을 보여드립니다.
- 랩을 스스로 완료할 수 없는 특정 문제나 장애물을 해결하는 데 도움을 줍니다.

<div class="content-ad"></div>

도움이 필요한 경우, 도전 과제에 대한 도움을 얻으려면 올바른 곳에 오신 것을 환영합니다. 그러나 먼저 퀘스트를 진행하고 직접 랩을 시도한 후에 계속 읽기를 강력히 권장합니다!

이러한 랩들은 문제 해결에 대해 다양한 방법이 항상 있습니다. 일반적으로 저는 더 반복 가능하고 프로그래밸하게 문제를 해결할 수 있도록 문제를 해결하는 것이 좋습니다. 그러나 물론 클라우드 콘솔도 사용할 수 있습니다.

# 이 랩 개요

이 랩에서는 Terraform을 사용하여 Google Cloud에서 인프라를 생성, 배포 및 관리해야 합니다. 또한 일부 관리되지 않은 인스턴스를 구성에 가져와 수정해야 합니다.

<div class="content-ad"></div>

# 나의 해결책

우리가 이 도전 과제 동안 사용할 몇 가지 변수를 정의하는 것부터 시작해봅시다. 실제 변수는 랩을 시작할 때 제공될 것입니다.

```js
gcloud auth list

region=<지역 입력>
zone=<존 입력>
prj=<프로젝트 ID 입력>
```

## 작업 1 — 구성 파일 생성

<div class="content-ad"></div>

이 폴더 구조를 만들라고 지시받았어요:

```js
main.tf
variables.tf
modules/
└── instances
|   ├── instances.tf
|   ├── outputs.tf
|   └── variables.tf
└── storage
    ├── storage.tf
    ├── outputs.tf
    └── variables.tf
```

이렇게 만들어볼까요:

```js
# 루트 디렉토리에 main.tf와 variables.tf 파일 생성
touch main.tf variables.tf

# main 디렉토리 및 파일 생성
mkdir -p modules/instances
mkdir modules/storage

# 'instances' 모듈 디렉토리에 필요한 파일 생성
touch modules/instances/instances.tf
touch modules/instances/outputs.tf
touch modules/instances/variables.tf

# 'storage' 모듈 디렉토리에 필요한 파일 생성
touch modules/storage/storage.tf
touch modules/storage/outputs.tf
touch modules/storage/variables.tf
```

<div class="content-ad"></div>

이제 변수.tf 파일을 업데이트하여 다음 변수들을 포함하도록 합니다:

```js
variable "region" {
  description = "Google Cloud 지역"
  type        = string
  default     = "실습에서 제공하는 지역"
}

variable "zone" {
  description = "Google Cloud 존"
  type        = string
  default     = "실습에서 제공하는 존"
}

variable "project_id" {
  description = "리소스를 프로비저닝할 프로젝트의 ID"
  type        = string
  default     = "귀하의 프로젝트 ID"
}
```

루트 모듈 main.tf를 업데이트하여 Google Cloud Provider를 포함하도록 합니다. Terraform Registry에서 항상 확인할 수 있습니다. 제공자 블록에 세 가지 변수를 모두 포함하도록 요청되었습니다.

```js
terraform {
  required_providers {
    google = {
      version = "~> 4.0"
    }
  }
}

provider "google" {
  project     = var.project_id
  region      = var.region
  zone        = var.zone
}
```

<div class="content-ad"></div>

이제 테라폼을 초기화해야 합니다. 따라서 다음 명령을 실행하세요:

```js
terraform init
```

## 작업 2 — 인프라 가져오기

여기서의 목표는 지금까지 테라폼 외부에서 프로비저닝된 인프라를 테라폼 제어 아래로 가져오는 것입니다.

<div class="content-ad"></div>

테라폼 가져오기 워크플로우를 사용할 거에요:

![이미지](/assets/img/2024-05-20-BuildInfrastructureonGoogleCloudwithTerraformGoogleChallengeLabWalkthrough_0.png)

이것들은 가져오기 단계들이에요:

- 가져올 기존 인프라를 식별하세요.
- 인프라를 테라폼 상태에 가져오세요.
- 해당 인프라와 일치하는 테라폼 구성을 작성하세요.
- 구성이 예상 상태와 인프라와 일치하는지 확인하기 위해 테라폼 계획을 검토하세요.
- 구성을 적용하여 테라폼 상태를 업데이트하세요.

<div class="content-ad"></div>

## 가져와야 할 기존 인프라 확인

이미 두 개의 GCE 인스턴스가 생성되었습니다. Cloud 콘솔에서 기존 인스턴스 중 하나인 tf-instance-1을 확인하세요. 우리는 다음을 검색하려고 합니다:

- 네트워크
- 머신 유형
- 디스크

다음으로, 우리는 main.tf에 우리의 인스턴스 모듈을 두 번 호출해야 합니다. 이 두 호출은 빈 정의를 포함할 것이므로 가져올 수 있습니다.

<div class="content-ad"></div>

```md
module "tf_instance_1" {
  source        = "./modules/instances"
  instance_name = "tf-instance-1"
  zone          = var.zone
  region        = var.region
}

module "tf_instance_2" {
  source        = "./modules/instances"
  instance_name = "tf-instance-2"
  zone          = var.zone
  region        = var.region
}
```

각 모듈 정의에는 고유한 레이블이 있어야 합니다.

이제 초기화해보세요:

```bash
terraform init
```

<div class="content-ad"></div>

이제 instances.tf에 모듈 구성을 작성합니다. 우리는 포함해야 할 최소한의 구성 요소를 알려받았습니다:

```js
resource "google_compute_instance" "instance" {
  name         = var.instance_name
  machine_type = "기존 인스턴스에서 하드 코딩"
  zone         = var.zone

  boot_disk {
    initialize_params {
      # image = "debian-cloud/debian-11"
      image = "기존 인스턴스에서 하드 코딩"
    }
  }

  network_interface {
    # network = "default"
    network = "기존 인스턴스에서 하드 코딩"
    access_config {
      // 일시적 공용 IP
    }
  }

  metadata_startup_script = <<-EOT
          #!/bin/bash
      EOT
  allow_stopping_for_update = true
}
```

인스턴스 모듈의 변수를 전달할 수 있도록 variables.tf를 업데이트하십시오. instance_name을 전달할 수 있도록 합니다:

```js
variable "instance_name" {
  description = "인스턴스의 이름."
  type        = string
}
```

<div class="content-ad"></div>

## Terraform State로 기존 인프라 가져오기

```js
terraform import module.tf_instance_1.google_compute_instance.instance \
  projects/$prj/zones/$zone/instances/tf-instance-1

terraform import module.tf_instance_2.google_compute_instance.instance \
  projects/$prj/zones/$zone/instances/tf-instance-2

# 가져오기 확인
terraform show
```

다음과 같이 가져와야 합니다:

<img src="/assets/img/2024-05-20-BuildInfrastructureonGoogleCloudwithTerraformGoogleChallengeLabWalkthrough_1.png" />

<div class="content-ad"></div>

## 계획 및 적용

이제 우리는 apply를 실행하여 인스턴스를 그 자리에서 업데이트합니다:

```js
terraform plan
terraform apply
```

## 작업 3 — 원격 백엔드 구성

<div class="content-ad"></div>

이건 꽤 쉬워요. 원격 GCS 백엔드에 Terraform 상태를 저장하려고 할 때 실행해야 하는 표준 단계들이에요:

- Terraform을 사용하여 GCS 버킷을 프로비저닝합니다.
- 새로운 GCS 버킷을 가리키는 백엔드 블록을 추가합니다.
- Terraform을 다시 초기화하고 로컬 상태 파일에서 원격 백엔드로 상태를 이관합니다.

## GCS Bucket 프로비저닝하기

다음 리소스 정의를 main.tf에 추가하세요:

<div class="content-ad"></div>

위의 코드를 아래와 같이 번역해 드립니다.

```js
리소스 "google_storage_bucket" "test-bucket-for-state" {
  이름        = "할당 받은 버킷 이름"
  위치    = "US"
  uniform_bucket_level_access = true

  force_destroy = true
}
```

그리고 다음을 적용하세요:

```js
terraform apply
```

## GCS 백엔드 추가하기

<div class="content-ad"></div>

main.tf 파일을 수정하여 백엔드를 terraform 블록에 포함시키세요:

```js
terraform {
  backend "gcs" {
    bucket  = "제공받은 버킷 이름"
    prefix  = "terraform/state"
  }
}
```

## 상태 이전

로컬 상태 파일에서 GCS 백엔드로 Terraform 상태를 이전하는 부분입니다.

<div class="content-ad"></div>

```js
terraform init -migrate-state
```

상태를 이전하려는 것을 확인하라는 메시지가 표시됩니다:

<img src="/assets/img/2024-05-20-BuildInfrastructureonGoogleCloudwithTerraformGoogleChallengeLabWalkthrough_2.png" />

## 작업 4 — 인프라 수정 및 업데이트

<div class="content-ad"></div>

우리는 machine_type을 포함하는 variables.tf를 업데이트해야합니다:

```js
variable "machine_type" {
  description = "인스턴스의 머신 유형"
  type        = string
  default     = "e2-standard-2"
}
```

그런 다음, instance.tf를 수정하여 machine_type 매개변수를 허용할 수 있도록 설정해야합니다:

```js
resource "google_compute_instance" "instance" {
  name         = var.instance_name
  machine_type = var.machine_type
  zone         = var.zone

  ...
```

<div class="content-ad"></div>

마지막으로, main.tf를 수정해서 세 번째 인스턴스를 추가해야 합니다. 세 번째 모듈을 호출하여 main.tf에 추가합니다. 이미 기본값을 설정했으므로 machine_type을 전달할 필요가 없습니다.

이제 초기화하고(모듈 인스턴스를 추가했으므로) 적용하세요.

```js
terraform init
terraform apply
```

## 작업 5 — 리소스 파기

<div class="content-ad"></div>

이전에 추가했던 인스턴스를 제거해 보겠습니다. main.tf에서 이 모듈 호출을 제거한 다음 다시 적용하십시오:

```js
terraform init
terraform apply
```

## 작업 6 — 레지스트리에서 모듈 사용하기

Google Network Module를 사용하려고 합니다.

<div class="content-ad"></div>

```bash
module "network" {
  source  = "terraform-google-modules/network/google"
  version = "6.0.0"

  project_id   = var.project_id
  network_name = "Use Supplied VPC Name"
  routing_mode = "GLOBAL"

  subnets = [
    {
      subnet_name           = "subnet-01"
      subnet_ip             = "10.10.10.0/24"
      subnet_region         = var.region
    },
    {
      subnet_name           = "subnet-02"
      subnet_ip             = "10.10.20.0/24"
      subnet_region         = var.region
    }
  ]
}
```

초기화하고 적용하세요:

```bash
terraform init
terraform apply
```

인스턴스 모듈을 업데이트하여 네트워크 매개변수와 서브넷 매개변수를 사용하도록 하십시오.


<div class="content-ad"></div>

variables.tf:

```js
variable "network" {
  description = "네트워크"
  type        = string
}

variable "subnet" {
  description = "서브넷"
  type        = string
}
```

instance.tf:

```js
network_interface {
  network = var.network
  subnetwork = var.subnet

  access_config {
    // 일시적인 공용 IP
  }
}
```

<div class="content-ad"></div>

그럼 이렇게 인스턴스를 생성하도록 main.tf를 업데이트하세요:

```js
module "tf_instance_1" {
  source        = "./modules/instances"
  instance_name = "tf-instance-1"
  zone          = var.zone
  region        = var.region

  network       = module.network.network_name
  subnet        = "subnet-01"
}

module "tf_instance_2" {
  source        = "./modules/instances"
  instance_name = "tf-instance-2"
  zone          = var.zone
  region        = var.region
  network       = module.network.network_name
  subnet        = "subnet-02"
}
```

```js
terraform init
terraform apply
```

## 작업 7 — 방화벽 추가

<div class="content-ad"></div>

위의 코드를 Korean으로 번역하였습니다:

md
main.tf을 업데이트 해주세요:

```js
resource "google_compute_firewall" "default" {
  name          = "tf-firewall"
  network       = module.network.network_name
  direction     = "INGRESS"
  source_ranges = ["0.0.0.0/0"]

  allow {
    protocol = "tcp"
    ports    = ["80"]
  }
}
```

그리고 마지막으로 한 번 더 실행해주세요...

```js
terraform apply
```


<div class="content-ad"></div>

그럼 끝났어요!