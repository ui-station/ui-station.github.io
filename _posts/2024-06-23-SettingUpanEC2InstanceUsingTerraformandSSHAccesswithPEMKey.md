---
title: "Terraform과 PEM 키를 사용하여 EC2 인스턴스 및 SSH 접근 설정 방법"
description: ""
coverImage: "/assets/img/2024-06-23-SettingUpanEC2InstanceUsingTerraformandSSHAccesswithPEMKey_0.png"
date: 2024-06-23 00:22
ogImage:
  url: /assets/img/2024-06-23-SettingUpanEC2InstanceUsingTerraformandSSHAccesswithPEMKey_0.png
tag: Tech
originalTitle: "Setting Up an EC2 Instance Using Terraform and SSH Access with PEM Key"
link: "https://medium.com/@garvit1189/setting-up-an-ec2-instance-using-terraform-and-ssh-access-with-pem-key-a2236e0c2fb6"
---

이 안내서는 PEM 키를 사용하여 SSH 액세스가 설정된 Terraform을 사용하여 EC2 인스턴스를 생성하는 데 도움이 될 것입니다.

## 아키텍처 개요

설치에는 다음이 포함됩니다:

- 서브넷과 인터넷 게이트웨이가 있는 VPC.
- EC2 인스턴스.
- SSH 액세스용 보안 그룹.
- 라우팅 테이블.

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

# AWS 자격 증명 구성

AWS CLI 설치: AWS CLI를 아직 설치하지 않았다면 여기에서 다운로드하여 설치할 수 있습니다.

AWS CLI 구성: 아래 명령을 실행하여 AWS 자격 증명을 구성하세요. 이를 통해 Terraform이 사용할 필요한 구성 파일을 설정합니다.

```js
aws configure
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

다음과 같이 자격 증명을 입력해주세요:

```js
AWS Access Key ID [None]: <당신의 AWS 액세스 키 ID>
AWS Secret Access Key [None]: <당신의 AWS Secret 액세스 키>
Default region name [None]: us-west-2
Default output format [None]: json
```

설정 확인: 설정을 완료한 후에는 다음 명령어를 실행하여 설정을 확인하세요:

```js
aws sts get-caller-identity
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

이 명령은 IAM 사용자에 대한 세부 정보가 포함된 JSON 응답을 반환해야 합니다.

# 디렉토리 구조 설정하기

터미널을 열고 다음과 같이 메인 디렉토리를 생성하세요:

```shell
mkdir terraform_project
cd terraform_project
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

모듈을 위한 하위 디렉토리를 생성해주세요:

```bash
mkdir ec2_setup
cd ec2_setup
```

필요한 Terraform 파일을 생성해주세요:

```bash
touch main.tf variables.tf vpc.tf subnet.tf security_group.tf ec2.tf internet_gateway.tf route_table.tf key_pair.tf
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

파일이 생성되었는지 확인하기 위해 파일 목록을 나열해보세요:

```js
ls;
```

## 구성 시작

## 1. 제공자 정의

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

main.tf 파일에서 공급업체를 설정하십시오:

```js
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.18"
    }
  }
}

provider "aws" {
  region = "us-west-2"
}
```

## 2. 입력 변수 정의

variables.tf 파일에서 변수를 정의하세요.

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
variable "ssh_key_name" {
  description = "SSH 키 쌍의 이름"
  type        = string
}

variable "ec2_instance_type" {
  description = "EC2 인스턴스의 유형"
  type        = string
}

variable "ec2_instance_tag" {
  description = "EC2 인스턴스를 위한 태그"
  type        = string
}

variable "instance_count" {
  description = "생성할 인스턴스 수"
  type        = number
}

variable "private_key_file" {
  description = "개인 키 파일의 이름"
  type        = string
}

variable "vpc_cidr_block" {
  description = "VPC의 CIDR 블록"
  type        = string
}

variable "subnet_az" {
  description = "서브넷의 가용 영역"
  type        = string
}
```

# 3. VPC 생성

vpc.tf 파일에 VPC 리소스를 정의하세요:

```js
resource "aws_vpc" "my_vpc" {
  cidr_block           = var.vpc_cidr_block
  enable_dns_support   = true
  enable_dns_hostnames = true
  tags = {
    Name = "my_vpc"
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

# 4. 서브넷 정의하기

subnet.tf 파일에서 다음과 같이 서브넷을 정의하세요:

```js
resource "aws_subnet" "my_subnet" {
  cidr_block        = cidrsubnet(aws_vpc.my_vpc.cidr_block, 3, 1)
  vpc_id            = aws_vpc.my_vpc.id
  availability_zone = var.subnet_az
}
```

# 5. 인터넷 게이트웨이 설정하기

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

internet_gateway.tf 파일에서 인터넷 게이트웨이를 정의하세요:

```js
resource "aws_internet_gateway" "my_igw" {
  vpc_id = aws_vpc.my_vpc.id
}
```

# 6. 보안 그룹 생성하기

security_group.tf 파일에서 보안 그룹을 정의하세요:

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
resource "aws_security_group" "ssh_access" {
  name   = "ssh_access"
  vpc_id = aws_vpc.my_vpc.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = -1
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

# 7. EC2 인스턴스 시작하기

ec2.tf에서 다음과 같이 EC2 인스턴스를 정의하세요:

```js
data "aws_ami" "latest_ubuntu" {
  most_recent = true

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

  owners = ["<Your AWS Account ID>"]
}

resource "aws_instance" "my_ec2_instance" {
  count                       = var.instance_count
  ami                         = data.aws_ami.latest_ubuntu.id
  instance_type               = var.ec2_instance_type
  key_name                    = var.ssh_key_name
  security_groups             = [aws_security_group.ssh_access.id]
  associate_public_ip_address = true
  subnet_id                   = aws_subnet.my_subnet.id

  tags = {
    Name = var.ec2_instance_tag
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

# 8. 라우트 테이블 생성

route_table.tf 파일에서 라우트 테이블 및 연관을 정의하세요:

```js
resource "aws_route_table" "my_route_table" {
  vpc_id = aws_vpc.my_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.my_igw.id
  }
}
resource "aws_route_table_association" "my_route_table_assoc" {
  subnet_id      = aws_subnet.my_subnet.id
  route_table_id = aws_route_table.my_route_table.id
}
```

# 9. 키페어 생성

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

key_pair.tf 파일에서 키페어를 정의하고 개인 키를 저장하세요:

```js
resource "tls_private_key" "my_rsa_key" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "my_key_pair" {
  key_name   = var.ssh_key_name
  public_key = tls_private_key.my_rsa_key.public_key_openssh
}

resource "local_file" "my_private_key" {
  content  = tls_private_key.my_rsa_key.private_key_pem
  filename = var.private_key_file
}
```

# 구성 배포

Terraform 구성을 초기화하세요:

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

```bash
테이블 태그를 Markdown 형식으로 변경하세요.
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

```js
terraform apply -var 'ssh_key_name=my_ssh_key' -var 'ec2_instance_type=t2.micro' -var 'ec2_instance_tag=["TestInstance"]' -var 'instance_count=1' -var 'private_key_file=my_private_key.pem' -var 'vpc_cidr_block=10.0.0.0/16' -var 'subnet_az=us-west-2a'
```

AWS에서 리소스를 확인해보세요:

![이미지1](/assets/img/2024-06-23-SettingUpanEC2InstanceUsingTerraformandSSHAccesswithPEMKey_0.png)

![이미지2](/assets/img/2024-06-23-SettingUpanEC2InstanceUsingTerraformandSSHAccesswithPEMKey_1.png)

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

아래는 Markdown 형식으로 변경한 것입니다.

![이미지1](/assets/img/2024-06-23-SettingUpanEC2InstanceUsingTerraformandSSHAccesswithPEMKey_2.png)

![이미지2](/assets/img/2024-06-23-SettingUpanEC2InstanceUsingTerraformandSSHAccesswithPEMKey_3.png)

PEM 키를 사용하여 인스턴스로 SSH 연결:

```js
ssh -i my_private_key.pem ubuntu@<public-ip>
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

<img src="/assets/img/2024-06-23-SettingUpanEC2InstanceUsingTerraformandSSHAccesswithPEMKey_4.png" />

# 결론

테라폼을 사용하여 SSH 액세스가 가능한 EC2 인스턴스를 성공적으로 생성했습니다. 이 구성은 모듈식이므로 향후 프로젝트에 맞게 쉽게 적응시킬 수 있습니다. 완전한 모듈은 GitHub에서 찾을 수 있습니다.

행복한 Terraforming!
