---
title: "테라폼으로 AWS-VPC 피어링하기"
description: ""
coverImage: "/assets/img/2024-05-27-AWS-VPCPeeringwithTerraform_0.png"
date: 2024-05-27 17:35
ogImage:
  url: /assets/img/2024-05-27-AWS-VPCPeeringwithTerraform_0.png
tag: Tech
originalTitle: "AWS-VPC Peering with Terraform"
link: "https://medium.com/@nik2701k/aws-vpc-peering-with-terraform-3010eccfb8fe"
---


![AWS VPC Peering with Terraform](/assets/img/2024-05-27-AWS-VPCPeeringwithTerraform_0.png)

AWS에서 VPC는 무엇인가요?

AWS에서 VPC 피어링이란 무엇인가요?

Terraform은 무엇인가요?


<div class="content-ad"></div>

이 글에서는 AWS에서 Terraform을 사용하여 VPC 피어링 프로세스를 자동화하는 단계를 살펴보겠습니다.

AWS에서 생성된 자원 목록

- EC2 (2대)
- VPC (2개)
- Internet Gateways (2개)
- 보안 그룹 (4개)
- VPC 피어링 연결 (1개)
- 라우트 테이블 (2개)

# 프로젝트 구조

<div class="content-ad"></div>


terraform_project/
│
├── module/
│   ├── ec2/
│   │   ├── main.tf
│   │   ├── output.tf
│   │   └── variables.tf
│   ├── vpc/
│   │   ├── main.tf
│   │   ├── output.tf
│   │   └── variables.tf
│   └── ...
│
├── main.tf
├── variables.tf
└── variable.tfvars/
    ├── dev-env.tfvars
    ├── stage-env.tfvars
    └── prod-env.tfvars


## 단계 1: VPC를 위한 모듈 생성 및 가져오기

modules/vpc-peering/main.tf

```js
terraform {
  required_providers {
    aws = {
      source                = "hashicorp/aws"
      version               = "~> 5.0"
      configuration_aliases = [aws.us_west]
    }
  }
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-1"
}

resource "aws_vpc" "main_vpc" {
  cidr_block = var.vpc_cidr

  tags = {
    Name       = var.vpc_name
    created_by = var.owner_name
  }
}
```

<div class="content-ad"></div>

지금, 프로젝트 루트의 main.tf 파일에 모듈을 가져오겠습니다.

```js
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-1"
}

module "vpc_A" {
  source     = "./modules/vpc"
  vpc_name   = var.vpcs["vpc_A"]["vpc_name"]
  vpc_cidr   = var.vpcs["vpc_A"]["cidr_block"]
  owner_name = var.owner_name
}

module "vpc_B" {
  source     = "./modules/vpc"
  vpc_name   = var.vpcs["vpc_B"]["vpc_name"]
  vpc_cidr   = var.vpcs["vpc_B"]["cidr_block"]
  owner_name = var.owner_name
  providers = {
    aws = aws.us_west
  }
}
```

## Step 2: Subnets용 모듈을 만들어 import해보세요.

modules/subnets/main.tf

<div class="content-ad"></div>

```js
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
      # configuration_aliases = [aws.us_west]
    }
  }
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-1"
}

resource "aws_subnet" "main_subnet" {
  vpc_id            = var.vpc_id
  cidr_block        = var.subnet_cidr
  availability_zone = var.availability_zone

  tags = {
    Name       = var.subnet_name
    created_by = var.owner_name
  }
}
```

이제 프로젝트 루트의 main.tf 파일에 모듈을 가져 오겠습니다.

```js
module "subnetsForvpc_A" {
  for_each          = var.subnetsForvpc_A
  source            = "./modules/subnets"
  vpc_id            = module.vpc_A.vpc_id
  subnet_cidr       = var.subnetsForvpc_A[each.key]["subnet_cidr"]
  subnet_name       = var.subnetsForvpc_A[each.key]["subnet_name"]
  availability_zone = var.subnetsForvpc_A[each.key]["availability_zone"]
  owner_name        = var.owner_name
}

module "subnetsForvpc_B" {
  for_each          = var.subnetsForvpc_B
  source            = "./modules/subnets"
  vpc_id            = module.vpc_B.vpc_id
  subnet_cidr       = var.subnetsForvpc_B[each.key]["subnet_cidr"]
  subnet_name       = var.subnetsForvpc_B[each.key]["subnet_name"]
  availability_zone = var.subnetsForvpc_B[each.key]["availability_zone"]
  owner_name        = var.owner_name
  providers = {
    aws = aws.us_west
  }
}
```

## 단계 3: 보안 그룹 모듈을 생성하고 가져 오기

<div class="content-ad"></div>

modules/security-groups/main.tf

```js
terraform {
  required_providers {
    aws = {
      source                = "hashicorp/aws"
      version               = "~> 5.0"
      configuration_aliases = [aws.us_west]
    }
  }
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-1"
}

resource "aws_security_group" "allow_ssh" {
  name        = var.sg_name
  description = var.sg_description
  vpc_id      = var.vpc_id

  ingress {
    description = var.ingress_description
    from_port   = var.ingress_from_port
    to_port     = var.ingress_to_port
    protocol    = var.ingress_protocol
    cidr_blocks = var.ingress_cidr_blocks
  }

  egress {
    from_port   = "0"
    to_port     = "0"
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name       = var.sg_name
    created_by = var.owner_name
  }
}
```

보안 그룹을 위해, 저는 main.tf 파일에서 프로젝트 루트로 모듈을 두 번 가져왔습니다. 첫 번째 보안 그룹은 EC2로 SSH를 허용하고, 두 번째는 EC2에서 ICMP 패킷의 흐름을 허용합니다.

```js
module "ssh_sg_vpcA" {
  source              = "./modules/security-groups"
  sg_name             = var.sgs["ssh_sg"]["sg_name"]
  sg_description      = var.sgs["ssh_sg"]["sg_description"]
  ingress_description = var.sgs["ssh_sg"]["ingress_description"]
  ingress_from_port   = var.sgs["ssh_sg"]["ingress_from_port"]
  ingress_to_port     = var.sgs["ssh_sg"]["ingress_to_port"]
  ingress_protocol    = var.sgs["ssh_sg"]["ingress_protocol"]
  ingress_cidr_blocks = var.sg_cidr_blocks["ssh_sg"]["ingress_cidr_blocks"]
  owner_name          = var.owner_name
  vpc_id              = module.vpc_A.vpc_id
}

module "ssh_sg_vpcB" {
  source              = "./modules/security-groups"
  sg_name             = var.sgs["ssh_sg"]["sg_name"]
  sg_description      = var.sgs["ssh_sg"]["sg_description"]
  ingress_description = var.sgs["ssh_sg"]["ingress_description"]
  ingress_from_port   = var.sgs["ssh_sg"]["ingress_from_port"]
  ingress_to_port     = var.sgs["ssh_sg"]["ingress_to_port"]
  ingress_protocol    = var.sgs["ssh_sg"]["ingress_protocol"]
  ingress_cidr_blocks = var.sg_cidr_blocks["ssh_sg"]["ingress_cidr_blocks"]
  owner_name          = var.owner_name
  vpc_id              = module.vpc_B.vpc_id
  providers = {
    aws = aws.us_west
  }
}

module "icmp_sg_vpcA" {
  source              = "./modules/security-groups"
  sg_name             = var.sgs["icmp_sg"]["sg_name"]
  sg_description      = var.sgs["icmp_sg"]["sg_description"]
  ingress_description = var.sgs["icmp_sg"]["ingress_description"]
  ingress_from_port   = var.sgs["icmp_sg"]["ingress_from_port"]
  ingress_to_port     = var.sgs["icmp_sg"]["ingress_to_port"]
  ingress_protocol    = var.sgs["icmp_sg"]["ingress_protocol"]
  ingress_cidr_blocks = var.sg_cidr_blocks["icmp_sg"]["ingress_cidr_blocks_for_vpcA"]
  owner_name          = var.owner_name
  vpc_id              = module.vpc_A.vpc_id
}

module "icmp_sg_vpcB" {
  source              = "./modules/security-groups"
  sg_name             = var.sgs["icmp_sg"]["sg_name"]
  sg_description      = var.sgs["icmp_sg"]["sg_description"]
  ingress_description = var.sgs["icmp_sg"]["ingress_description"]
  ingress_from_port   = var.sgs["icmp_sg"]["ingress_from_port"]
  ingress_to_port     = var.sgs["icmp_sg"]["ingress_to_port"]
  ingress_protocol    = var.sgs["icmp_sg"]["ingress_protocol"]
  ingress_cidr_blocks = var.sg_cidr_blocks["icmp_sg"]["ingress_cidr_blocks_for_vpcB"]
  owner_name          = var.owner_name
  vpc_id              = module.vpc_B.vpc_id
  providers = {
    aws = aws.us_west
  }
}
```

<div class="content-ad"></div>

## 단계 4: EC2를 위한 모듈 생성 및 가져오기

modules/ec2/main.tf

```js
terraform {
  required_providers {
    aws = {
      source                = "hashicorp/aws"
      version               = "~> 5.0"
      configuration_aliases = [aws.us_west]
    }
  }
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-1"
}

resource "aws_instance" "my_ec2" {

  ami                         = var.ami_id
  instance_type               = var.instance_type
  subnet_id                   = var.subnet_id
  associate_public_ip_address = true
  key_name                    = var.key_name

  tags = {
    Name       = var.ec2_name
    created_by = var.owner_name
  }
}

resource "aws_network_interface_sg_attachment" "ssh_sg_attachment" {
  security_group_id    = var.ssh_sg_id
  network_interface_id = aws_instance.my_ec2.primary_network_interface_id
}

resource "aws_network_interface_sg_attachment" "icmp_sg_attachment" {
  security_group_id    = var.icmp_sg_id
  network_interface_id = aws_instance.my_ec2.primary_network_interface_id
}
```

이제 모듈을 프로젝트 루트의 main.tf 파일에 가져오겠습니다.

<div class="content-ad"></div>


module "ec2_vpcA" {
  source        = "./modules/ec2"
  subnet_id     = module.subnetsForvpc_A["subnet1"].subnet_id
  ami_id        = var.ec2s["ec2_vpcA"]["ami_id"]
  instance_type = var.ec2s["ec2_vpcA"]["instance_type"]
  ec2_name      = var.ec2s["ec2_vpcA"]["ec2_name"]
  key_name      = var.ec2s["ec2_vpcA"]["key_name"]
  ssh_sg_id     = module.ssh_sg_vpcA.sg_id
  icmp_sg_id    = module.icmp_sg_vpcA.sg_id
  owner_name    = var.owner_name
}

module "ec2_vpcB" {
  source        = "./modules/ec2"
  subnet_id     = module.subnetsForvpc_B["subnet1"].subnet_id
  ami_id        = var.ec2s["ec2_vpcB"]["ami_id"]
  instance_type = var.ec2s["ec2_vpcB"]["instance_type"]
  ec2_name      = var.ec2s["ec2_vpcB"]["ec2_name"]
  key_name      = var.ec2s["ec2_vpcB"]["key_name"]
  ssh_sg_id     = module.ssh_sg_vpcB.sg_id
  icmp_sg_id    = module.icmp_sg_vpcB.sg_id
  owner_name    = var.owner_name
  providers = {
    aws = aws.us_west
  }
}


## Step 5: Create modules for Internet Gateways and importing them

modules/internet-gateways/main.tf


terraform {
  required_providers {
    aws = {
      source                = "hashicorp/aws"
      version               = "~> 5.0"
      configuration_aliases = [aws.us_west]
    }
  }
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-1"
}

resource "aws_internet_gateway" "i_gw" {
  vpc_id = var.vpc_id

  tags = {
    Name       = var.igw_name
    created_by = var.owner_name
  }
}


<div class="content-ad"></div>

이제, 프로젝트 루트의 main.tf 파일에 모듈을 가져오겠습니다.

```js
module "igw_vpcA" {
  source     = "./modules/internet-gateways"
  vpc_id     = module.vpc_A.vpc_id
  igw_name   = var.igws["igw_vpcA"]["name"]
  owner_name = var.owner_name
}

module "igw_vpcB" {
  source     = "./modules/internet-gateways"
  vpc_id     = module.vpc_B.vpc_id
  igw_name   = var.igws["igw_vpcB"]["name"]
  owner_name = var.owner_name
  providers = {
    aws = aws.us_west
  }
}
```

## 단계 6: VPC Peering, VPC Peering accepter, VPC Peering configure용 모듈 생성 및 가져오기

모듈 - VPC Peering (modules/vpc-peering/main.tf)

<div class="content-ad"></div>

```kotlin
테라폼 {
  필수_제공자 {
    aws = {
      소스 = "hashicorp/aws"
      버전 = "~> 5.0"
      구성_별칭 = [aws.us_west]
    }
  }
}

제공자 "aws" {
  별칭  = "us_west"
  지역 = "us-west-1"
}

리소스 "aws_vpc_peering_connection" "vpc_peering" {
  peer_vpc_id = var.peer_vpc_id
  vpc_id      = var.vpc_id
  peer_region = "us-west-1"
  tags = {
    이름       = var.vpcpeer_name
    생성자 = var.owner_name
  }
}
```

모듈 — VPC 피어링 수락자 (modules/vpc-peering-accepter/main.tf)

```kotlin
테라폼 {
  필수_제공자 {
    aws = {
      소스 = "hashicorp/aws"
      버전 = "~> 5.0"
      구성_별칭 = [aws.us_west]
    }
  }
}

제공자 "aws" {
  별칭  = "us_west"
  지역 = "us-west-1"
}

리소스 "aws_vpc_peering_connection_accepter" "peer_vpc_AB" {
  provider = aws.us_west

  vpc_peering_connection_id = var.vpc_peering_id
  auto_accept               = true

  tags = {
    쪽       = "수락자"
    소유자_이름 = var.owner_name
  }
}
```

모듈 — VPC 피어링 구성 (modules/vpc-peering-configure/main.tf)



<div class="content-ad"></div>

```js
terraform {
  required_providers {
    aws = {
      source                = "hashicorp/aws"
      version               = "~> 5.0"
      configuration_aliases = [aws.us_west]
    }
  }
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-1"
}

resource "aws_vpc_peering_connection_options" "vpc_peering_configure" {
  vpc_peering_connection_id = var.vpc_peering_connection_id
}
```

이제, 위의 모든 모듈을 루트의 main.tf 파일로 가져오겠습니다.

```js
module "vpc_peering" {
  source       = "./modules/vpc-peering"
  peer_vpc_id  = module.vpc_B.vpc_id
  vpc_id       = module.vpc_A.vpc_id
  vpcpeer_name = var.vpcpeer_name
  owner_name   = var.owner_name
}

module "vpc_peering_accepter" {
  source         = "./modules/vpc-peering-accepter"
  vpc_peering_id = module.vpc_peering.aws_vpc_peering_connection_id
  owner_name     = var.owner_name
  providers = {
    aws = aws.us_west
  }
}

module "vpc_peering_configureA" {
  source                    = "./modules/vpc_peer_configure"
  vpc_peering_connection_id = module.vpc_peering_accepter.aws_vpc_peering_connection_accepter
}

module "vpc_peering_configureB" {
  source                    = "./modules/vpc_peer_configure"
  vpc_peering_connection_id = module.vpc_peering_accepter.aws_vpc_peering_connection_accepter
  providers = {
    aws = aws.us_west
  }
}
```

## 단계 7: 라우트 테이블을 위한 모듈 만들기 및 가져오기

<div class="content-ad"></div>

modules/route-tables/main.tf

```js
terraform {
  required_providers {
    aws = {
      source                = "hashicorp/aws"
      version               = "~> 5.0"
      configuration_aliases = [aws.us_west]
    }
  }
}

provider "aws" {
  alias  = "us_west"
  region = "us-west-1"
}

resource "aws_route" "route" {
  route_table_id            = var.route_table_id
  destination_cidr_block    = var.destination_cidr_block
  gateway_id                = var.gateway_id != "" ? var.gateway_id : null
  vpc_peering_connection_id = var.vpc_peering_connection_id != "" ? var.vpc_peering_connection_id : null
}
```

이제 프로젝트 루트의 main.tf 파일에 모듈을 가져와보겠습니다.

```js
module "route_table_vpcA_pcgw" {
  source                    = "./modules/route-table"
  route_table_id            = module.vpc_A.default_route_table_id
  destination_cidr_block    = var.destination_cidr_blocks_for_routetable["for_route_vpcA"]["pcgw_cidr"]
  vpc_peering_connection_id = module.vpc_peering.aws_vpc_peering_connection_id
  gateway_id                = ""
}

module "route_table_vpcB_pcgw" {
  source                    = "./modules/route-table"
  route_table_id            = module.vpc_B.default_route_table_id
  destination_cidr_block    = var.destination_cidr_blocks_for_routetable["for_route_vpcB"]["pcgw_cidr"]
  vpc_peering_connection_id = module.vpc_peering.aws_vpc_peering_connection_id
  gateway_id                = ""
  providers = {
    aws = aws.us_west
  }
}

module "route_table_vpcA_igw" {
  source                    = "./modules/route-table"
  route_table_id            = module.vpc_A.default_route_table_id
  destination_cidr_block    = var.destination_cidr_blocks_for_routetable["for_route_vpcA"]["igw_cidr"]
  gateway_id                = module.igw_vpcA.igw_id
  vpc_peering_connection_id = ""
}

module "route_table_vpcB_igw" {
  source                    = "./modules/route-table"
  route_table_id            = module.vpc_B.default_route_table_id
  destination_cidr_block    = var.destination_cidr_blocks_for_routetable["for_route_vpcB"]["igw_cidr"]
  gateway_id                = module.igw_vpcB.igw_id
  vpc_peering_connection_id = ""
  providers = {
    aws = aws.us_west
  }
}
```

<div class="content-ad"></div>

## 단계 8: dev-env.tfvars 파일 작성

variable.tfvars/dev-env.tfvars

```js
owner_name = "NIKUNJ"

vpcs = {
  "vpc_A" = {
    vpc_name   = "vpc-us-east1"
    cidr_block = "10.1.0.0/16"
  }
  "vpc_B" = {
    vpc_name   = "vpc-us-west1"
    cidr_block = "10.2.0.0/16"
  }
}

vpc_A에 대한 서브넷 목록 = {
  "subnet1" = {
    subnet_cidr       = "10.1.0.0/20"
    subnet_name       = "subnet-us-east1a"
    availability_zone = "us-east-1a"
  }
  "subnet2" = {
    subnet_cidr       = "10.1.16.0/20"
    subnet_name       = "subnet-us-east1b"
    availability_zone = "us-east-1b"
  }
  "subnet3" = {
    subnet_cidr       = "10.1.32.0/20"
    subnet_name       = "subnet-us-east1c"
    availability_zone = "us-east-1c"
  }
}

vpc_B에 대한 서브넷 목록 = {
  "subnet1" = {
    subnet_cidr       = "10.2.0.0/20"
    subnet_name       = "subnet-us-west1a"
    availability_zone = "us-west-1a"
  }
  "subnet2" = {
    subnet_cidr       = "10.2.16.0/20"
    subnet_name       = "subnet-us-west1b"
    availability_zone = "us-west-1b"
  }
}

보안 그룹 목록 = {
  "ssh_sg" = {
    sg_name             = "allow_ssh"
    sg_description      = "내 IP에서 SSH 허용"
    ingress_description = "내 IP를 위한 SSH"
    ingress_from_port   = "22"
    ingress_to_port     = "22"
    ingress_protocol    = "tcp"
  }
  "icmp_sg" = {
    sg_name             = "allow_ping_traffic"
    sg_description      = "내 IP에서 ping 허용"
    ingress_description = "내 IP로부터의 ping"
    ingress_from_port   = "8"
    ingress_to_port     = "0"
    ingress_protocol    = "icmp"
  }
}

sg_cidr_blocks = {
  "ssh_sg" = {
    ingress_cidr_blocks = ["12.34.56.78/32"] # ec2로의 SSH가 허용된 IP를 언급
  }
  "icmp_sg" = {
    ingress_cidr_blocks_for_vpcA = ["10.2.0.0/16"]
    ingress_cidr_blocks_for_vpcB = ["10.1.0.0/16"]
  }
}

ec2 목록 = {
  "ec2_vpcA" = {
    ami_id        = "ami-0e8a34246278c21e4"
    instance_type = "t2.micro"
    ec2_name      = "ec2-us-east1"
    key_name      = "us-east-1-demo"
  }
  "ec2_vpcB" = {
    ami_id        = "ami-09ab9d570789dfdd4"
    instance_type = "t2.micro"
    ec2_name      = "ec2-us-west1"
    key_name      = "us-west-1-demo"
  }
}

인터넷 게이트웨이 목록 = {
  "igw_vpcA" = {
    name = "igw_vpcA"
  }
  "igw_vpcB" = {
    name = "igw_vpcB"
  }
}

VPC 피어링 이름 = "vpc_AB_peering"

route 테이블용 대상 CIDR 블록 = {
  "for_route_vpcA" = {
    pcgw_cidr = "10.2.0.0/16"
    igw_cidr  = "0.0.0.0/0"
  }
  "for_route_vpcB" = {
    pcgw_cidr = "10.1.0.0/16"
    igw_cidr  = "0.0.0.0/0"
  }
}
```

## 단계 9: 작업 디렉토리 초기화

<div class="content-ad"></div>

- 작업 디렉토리에서 terraform init 명령을 실행하세요. 이 명령은 필요한 모든 공급자 및 모듈을 다운로드하고 백엔드를 초기화합니다.

## 단계 10: 테라폼 실행 계획 생성

- 작업 디렉토리에서 terraform plan 명령을 실행하세요. 실행 계획을 제공합니다.

## 단계 11: terraform apply 실행

<div class="content-ad"></div>

- 작업 디렉토리에서 terraform apply 명령을 실행하면 AWS에 필요한 모든 리소스가 생성됩니다.

## 단계 12: 연결 확인

- SSH를 사용하여 EC2 인스턴스 중 하나에 연결하고 다른 EC2 인스턴스의 사설 IP를 사용하여 핑을 보냅니다.
- 모든 것이 잘 되었다면 ICMP 데이터 패킷이 성공적으로 전송됩니다.

여기까지입니다. 이제 Terraform을 사용하여 AWS VPC 피어링 연결을 생성하는 방법을 배웠습니다. 이제 여러분은 이것을 사용하고 필요에 따라 수정할 수 있습니다.

<div class="content-ad"></div>

전체 코드는 여기에서 찾을 수 있어요.

이 가이드가 도움이 되었다면 👏 버튼을 클릭해 주세요. 댓글도 자유롭게 남겨주세요.
