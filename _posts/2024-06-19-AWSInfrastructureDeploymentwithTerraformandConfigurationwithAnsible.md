---
title: "테라폼을 이용한 AWS 인프라 배포와 앤서블을 이용한 구성 설정"
description: ""
coverImage: "/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_0.png"
date: 2024-06-19 13:24
ogImage: 
  url: /assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_0.png
tag: Tech
originalTitle: "AWS Infrastructure Deployment with Terraform and Configuration with Ansible"
link: "https://medium.com/@harsh05/aws-infrastructure-deployment-with-terraform-and-configuration-with-ansible-14e72b134959"
---


# 소개:

현재의 동적 클라우드 컴퓨팅 환경에서는 인프라 프로비저닝을 자동화하는 것이 확장성, 신뢰성, 및 비용 효율성을 원하는 기관들에게 필수적입니다. Terraform은 오픈 소스 인프라 코드 도구로, 선언적 구성 파일을 사용하여 인프라를 정의하고 관리할 수 있도록 팀에게 권한을 부여합니다. 이 블로그에서는 Terraform이 AWS 인프라 구성 요소의 배포를 체계적이고 효율적으로 간소화하는 방법을 탐색해보겠습니다.

# 프로젝트 개요:

우리 Terraform 프로젝트는 가상 사설 클라우드(VPC), 보안 그룹, Amazon Machine Image(AMI), Elastic Block Store(EBS) 볼륨, 및 EC2 인스턴스로 구성된 AWS 인프라 스택의 배포를 자동화하는 데 초점을 맞춥니다. Terraform의 모듈식이자 반복 가능한 구성을 활용하여 환경 간 일관성과 신뢰성을 보장하고, 수동 개입과 인적 오류를 최소화합니다.

<div class="content-ad"></div>

# 상세 단계:

## 1. AWS CLI 설치하기:

- 먼저 로컬 컴퓨터에 AWS CLI를 설치해야 합니다.
- 다음 링크를 따라 AWS CLI를 설치하세요:

![AWS CLI 설치 링크](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_0.png)

<div class="content-ad"></div>

- AWS CLI를 이곳에서 다운로드하여 계정에 구성하세요.
- 또는 Linux 버전을 선택하여 설치할 수도 있습니다.
- 이를 위해 계정에 IAM 사용자를 만들어야 합니다.

![이미지1](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_1.png)

![이미지2](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_2.png)

- 현재는 관리자 액세스를 제공하지만 좋은 실천 방법은 아닙니다.

<div class="content-ad"></div>

![2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_3.png](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_3.png)

![2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_4.png](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_4.png)

- 보안 자격 증명에서 "액세스 키 생성" 옵션을 클릭하여 액세스 키 및 비밀 키를 생성하고 둘 다 복사합니다.

![2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_5.png](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_5.png)

<div class="content-ad"></div>

- Access Key ID에 액세스 키를 붙여 넣고, Secret Access Key ID에 비밀 키를 입력해주세요. 그리고 설정하고 싶은 기본 지역도 지정해주세요.

## 2. Terraform 초기화하기.

- main.tf 파일을 생성하고, 제공자(provider) 구성을 거기에 입력해주세요.

```js
# main.tf

# Provider configuration
provider "aws" {
  region = "us-west-1"
}
```

<div class="content-ad"></div>

- 그럼 아래 명령어를 입력해주세요.

```js
terraform.exe init
```

## 3. VPC 생성:

우리는 VPC 구성을 정의합니다. CIDR 블록을 포함하여 네트워크 구조의 기반을 마련합니다. 이는 AWS 환경 내에서 격리되고 안전한 통신을 제공합니다.

<div class="content-ad"></div>

- 이 프로젝트 전체에 대해 동일한 main.tf 파일을 사용할 것입니다.

```js
# main.tf

resource "aws_vpc" "vpc"{
        cidr_block = "192.168.0.0/16"

        tags = {
                Name = "Terraform_VPC"
        }
}
```

## 4. 서브넷 생성:

- 이제 서브넷 구성을 정의합니다. 이 구성에는 공용 서브넷과 사설 서브넷이 모두 포함됩니다.

<div class="content-ad"></div>

```js
# main.tf

변수 "aws_azs"를 정의합니다. 여기에는 public 서브넷의 가용 영역이 저장됩니다. 그리고 public 서브넷의 CIDR 블록을 저장하는 다른 변수를 만듭니다.

그런 다음 각 가용 영역에 2개의 public 서브넷을 만듭니다.
- length 함수는 주어진 목록, 맵 또는 문자열의 길이를 결정합니다. 목록이나 맵이 주어지면 해당 컬렉션의 요소 수가 결과로 나옵니다.
- element() 함수는 목록에서 특정 인덱스의 요소를 검색합니다. 사용 사례: 목록에서 특정 요소에 액세스하는 것은 해당 인덱스를 기준으로 목록에서 특정 리소스나 매개변수를 선택하려고 할 때 유용합니다.
- count.index 객체는 count 내의 현재 인스턴스의 인덱스를 나타냅니다. 인덱스는 0부터 시작하며, count가 4인 리소스가 있으면 count.index 객체는 0, 1, 2 및 3이 됩니다.
- 그런 다음 private 서브넷을 만드는 동일한 단계를 반복합니다.

## 5. Internet-Gateway 및 Route-table 생성:

이제 public 서브넷에 인터넷에 연결할 Internet-Gateway를 만들 것입니다. 이를 위해 라우팅 테이블도 생성하여 경로를 만들어야 합니다.
```

<div class="content-ad"></div>

```js
# main.tf

resource "aws_internet_gateway" "igw" {
        vpc_id = aws_vpc.vpc.id
        tags = {
                Name = "terrform-vpc-igw"
        }
}

resource "aws_route_table" "second-rt" {
        vpc_id = aws_vpc.vpc.id
        route {
                cidr_block = "0.0.0.0/0"
                gateway_id = aws_internet_gateway.igw.id
        }

        tags = {
                Name = "Public-route-table"
        }
}

resource "aws_route_table_association" "public-subnets-asso" {
        count = length(var.public_subnet_cidrs)
        subnet_id = element(aws_subnet.public_subnets[*].id, count.index)
        route_table_id = aws_route_table.second-rt.id
}
```

- 여기서, 먼저 VPC 내에서 인터넷 게이트웨이를 생성합니다.
- 그런 다음 인터넷 게이트웨이를 통해 안전한 인터넷 연결을 가능하게 하는 라우트를 생성하는 라우트 테이블을 만듭니다.
- 그런 다음 이 라우트 테이블과 공용 서브넷을 연결해야합니다.

## 6. 보안 그룹 구성:

EC2 인스턴스로의 들어오고 나가는 트래픽을 제어하기 위해 보안 그룹 규칙을 지정합니다. 이렇게 하면 사전 정의된 규칙 세트에 따라 액세스를 제한하여 네트워크 보안이 강화되며 잠재적인 보안 취약점을 완화할 수 있습니다.

<div class="content-ad"></div>

```hcl
resource "aws_security_group" "sg" {
    name        = "terraform_sg"
    description = "This security group is for terraform practice"
    vpc_id      = aws_vpc.vpc.id

    tags = {
        Name = "terraform_vg"
    }
}

resource "aws_vpc_security_group_ingress_rule" "sg_in_rule" {
    security_group_id = aws_security_group.sg.id
    cidr_ipv4        = "0.0.0.0/0"
    from_port        = 80
    ip_protocol      = "tcp"
    to_port          = 80
}

resource "aws_vpc_security_group_ingress_rule" "sg_in_rule2" {
    security_group_id = aws_security_group.sg.id
    cidr_ipv4        = "0.0.0.0/0"
    from_port        = 22
    ip_protocol      = "tcp"
    to_port          = 22
}

resource "aws_vpc_security_group_egress_rule" "sg_eg_rule" {
    security_group_id = aws_security_group.sg.id
    cidr_ipv4         = "0.0.0.0/0"
    ip_protocol       = "-1" # semantically equivalent to all ports
}
```

- 먼저 VPC 내에서 보안 그룹을 만듭니다.
- 그런 다음 보안 그룹에 대한 인바운드 규칙을 정의하여 클라이언트가 포트 80 및 22에 도달할 수 있도록 허용합니다.
- 그런 다음 아무 포트에서 어디로든 연결을 허용하는 보안 그룹에 대한 아웃바운드 규칙을 정의합니다.

## 7. 데이터 소스를 활용한 AMI 구성:

Terraform의 데이터 소스를 활용하여 지정된 필터에 따라 기존 AWS AMI에 대한 정보를 가져올 수 있습니다. 이러한 필터는 지역, 운영 체제 및 아키텍처와 같은 미리 정의된 것으로 구성됩니다. 이는 EC2 인스턴스에 가장 적합한 AMI를 동적으로 선택함으로써 배포 간의 호환성 및 일관성을 보장합니다.

<div class="content-ad"></div>

```js
data "aws_ami" "rhel9" {
        most_recent = true

        owners = ["309956199498"] // Red Hat의 계정 ID.

        filter {
                name   = "architecture"
                values = ["x86_64"]
        }

        filter {
                name   = "root-device-type"
                values = ["ebs"]
        }

        filter {
                name   = "virtualization-type"
                values = ["hvm"]
        }

        filter {
                name   = "name"
                values = ["RHEL-9.*"]
        }
}
```

- 데이터 소스는 외부 시스템이나 기존 리소스에서 정보를 조회하고 해당 정보를 Terraform 구성에 통합하는 데 사용됩니다.

## 8. EC2 인스턴스 프로비저닝:

인스턴스 유형, 키페어, 보안 그룹을 포함한 EC2 인스턴스 구성을 정의합니다. Terraform은 VPC 내에서 EC2 인스턴스를 프로비저닝하여 지정된 구성을 준수하면서 연결성과 리소스 격리를 보장합니다.

<div class="content-ad"></div>

```json
파일 이름 : main.tf
```

## 3. EC2 인스턴스 생성 :

이 코드는 Terraform을 사용하여 AWS EC2 인스턴스를 생성하는 예시입니다. 우리는 AMI ID, 인스턴스 유형, 키 이름, 서브넷 ID, 보안 그룹 ID 등을 정의하고 있습니다. 이를 통해 인프라스트럭처 스택에 EC2 인스턴스를 통합할 수 있습니다. 태그를 지정하여 리소스를 식별할 수도 있습니다.


테이블
------
제목  | 설명
------|---------
AMI   | data.aws_ami.rhel9.id
유형  | "t2.micro"
키 이름 | "IAM_California"
서브넷 ID | aws_subnet.public_subnets[0].id
VPC 보안 그룹 ID | aws_security_group.sg.id
퍼블릭 IP 주소 연결 | true

태그 :


<div class="content-ad"></div>

- 이제 새로운 EBS 볼륨을 생성했습니다. 중요한 점은 ec2 인스턴스와 ebs 볼륨이 동일한 가용 영역에 있어야 한다는 것입니다. 그렇지 않으면 서로 연결할 수 없습니다.

## 10. EBS 볼륨 연결

이제 다음 단계는 새로 생성한 ebs 볼륨을 ec2 인스턴스에 연결하는 것입니다.

```js
resource "aws_volume_attachment" "ebs_attach" {
        device_name = "/dev/xvdb"
        volume_id = aws_ebs_volume.EBS.id
        instance_id = aws_instance.ec2-1.id
}
```

<div class="content-ad"></div>

## 11. 공용 IP 주소 출력:

마지막으로, 테라폼의 출력 기능을 사용하여 프로비저닝된 EC2 인스턴스의 공용 IP 주소를 표시합니다. 이는 관리자와 최종 사용자 모두가 인스턴스에 쉽게 액세스하고 관리할 수 있도록 합니다.

```js
output "ec2_instance_ip" {
        value = aws_instance.ec2-1.public_ip
}
```

## 12. 테라폼 파일 적용하기

<div class="content-ad"></div>

지금은 여태까지 AWS 인프라를 만든 main.tf 파일을 적용할 것입니다.

```js
terraform.exe apply
```

![AWS Infrastructure Deployment](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_6.png)

- 먼저 전체 계획을 알려줍니다. 그렇지 않으면 계획을 보는 별도의 명령어가 있습니다. 즉, terraform.exe plan
- 계획을 알려준 후에는 앞으로 진행하고 AWS 클라우드에 적용할 것인지 물어봅니다.

<div class="content-ad"></div>


![AWS Infrastructure Deployment with Terraform and Configuration with Ansible 7](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_7.png)

![AWS Infrastructure Deployment with Terraform and Configuration with Ansible 8](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_8.png)

# Let’s Go on our AWS Console to verify this deployment.

## VPC ARCHITECTURE:


<div class="content-ad"></div>


![AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_9](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_9.png)

![AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_10](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_10.png)

## SECURITY GROUP :

![AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_11](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_11.png)


<div class="content-ad"></div>

## EC2-INSTANCE :

![EC2-INSTANCE](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_12.png)

# Ansible Configuration Management:

인프라가 프로비저닝된 후에는 프로비저닝된 인스턴스 내에서 Ansible을 사용하여 구성 관리 작업으로 신속하게 전환합니다. Ansible은 우리에게 idempotent playbooks 및 모듈을 사용하여 복잡한 구성 작업을 자동화할 수 있는 기능을 제공하여 효율적이고 확장 가능한 인프라 관리를 가능하게 합니다.

<div class="content-ad"></div>

저희 프로젝트는 Terraform과 Ansible을 완벽하게 통합하여 일관된 배포 파이프라인을 구축합니다. Terraform은 인프라 스택의 기반을 마련하는 데 도움을 주고, Ansible은 프로비전된 인스턴스를 구성하여 그들이 그 의도한 목적을 위해 완전히 기능하고 최적화되도록 보장합니다.

## EC2-인스턴스 구성을 위한 Ansible의 실용적인 단계:

- ANSIBLE 인벤토리:

```js
vim /etc/ansible/hosts
```

<div class="content-ad"></div>

\<img src="/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_13.png" />

2. REQUIRED MODULES 설치하기.

```js
ansible-galaxy collections install community.general 
ansible-galaxy collections install posix
```

3. ANSIBLE-PLAYBOOK:

<div class="content-ad"></div>

```js
vim <file-name>.yml
```

![AWS Infrastructure Deployment with Terraform and Configuration with Ansible](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_14.png)

![AWS Infrastructure Deployment with Terraform and Configuration with Ansible](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_15.png)

![AWS Infrastructure Deployment with Terraform and Configuration with Ansible](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_16.png)


<div class="content-ad"></div>

```yaml
- hosts: all    
  become: yes
  tasks:
    - name: "파티션 생성"
      community.general.parted:
        device: "/dev/xvdb"
        name: "GPT"
        number: 1
        part_end: "1GiB"
        fs_type: "ext4"
        state: present
        label: "gpt"
        unit: GiB

    - name: "중요 명령어 실행"
      command: 
        cmd: "udevadm settle"
      register: cmd

    - debug:
        var: cmd

    - command: 
        cmd: "lsblk"
      register: cmd2

    - debug: 
        var: cmd2 

    - name: "파티션 포맷"
      community.general.filesystem:
        fstype: ext4
        dev: "/dev/xvdb1"

    - name: "웹 서버 설치"
      package:
        name: "httpd"
        state: present

    - name: "마운트된 볼륨과 연결"
      ansible.posix.mount:
        path: "/var/www/html"
        src: "/dev/xvdb1"
        state: mounted
        fstype: ext4

    - name: "데몬 다시로드"
      command:
        cmd: "systemctl daemon-reload"
        
    - command: 
        cmd: "lsblk"
      register: cmd3

    - debug:
        var: cmd3

    - name: "인덱스 파일을 웹 서버로 복사"
      ansible.builtin.copy:
        src: "index.html"
        dest: "/var/www/html/index.html"

    - name: "서버 재시작"
      service:
        name: "httpd"
        state: "started"
```

## 이 Playbook은 다음을 수행할 수 있습니다:

- 우리가 연결한 볼륨인 /dev/xvdb에 파티션 생성.
- ext4 유형으로 새로 생성된 파티션 포맷.
- 시스템에 아파치 웹 서버 설치.
- 아파치 루트 문서인 /var/www/html에 파티션을 마운트.
- 로컬 시스템의 인덱스 파일을 대상 시스템의 루트 문서에 복사.
- 아파치 서비스 시작.

## ANSIBLE PLAYBOOK 실행하기


<div class="content-ad"></div>

```js
ansible-playbook <file-name>.yml
```

![Image](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_17.png)

![Image](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_18.png)

## 웹 서버가 성공적으로 시작되었는지 확인해 봅시다.

<div class="content-ad"></div>


![AWS Infrastructure Deployment with Terraform and Configuration with Ansible](/assets/img/2024-06-19-AWSInfrastructureDeploymentwithTerraformandConfigurationwithAnsible_19.png)

# 결론:

요약하면, Terraform과 Ansible의 통합은 AWS 인프라 자동화에서 강력한 패러다임 변화를 나타냅니다. 인프라 프로비저닝에 Terraform을 활용하고 구성 관리에는 Ansible을 활용함으로써, 조직은 클라우드 배포에서 전례 없는 민첩성, 확장성 및 신뢰성을 달성할 수 있습니다. 이 통합 접근 방식을 통해 팀은 DevOps 성숙도로 나아가는 여정을 가속화하고 클라우드 자동화의 모든 잠재력을 발휘할 수 있습니다.

오늘 Terraform과 Ansible의 힘을 받아 AWS 인프라 배포 및 구성 워크플로를 혁신하세요! 🚀🔧
