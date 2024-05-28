---
title: "GitOps와 Kubernetes, Terraform, Gitlab 그리고 FluxCD"
description: ""
coverImage: "/assets/img/2024-05-27-GitOpswithKubernetesTerraformGitlabandFluxCD_0.png"
date: 2024-05-27 17:43
ogImage:
  url: /assets/img/2024-05-27-GitOpswithKubernetesTerraformGitlabandFluxCD_0.png
tag: Tech
originalTitle: "GitOps with Kubernetes, Terraform, Gitlab and FluxCD"
link: "https://medium.com/@prag-matic/gitops-with-kubernetes-terraform-gitlab-and-fluxcd-2875d1010dac"
---

<img src="/assets/img/2024-05-27-GitOpswithKubernetesTerraformGitlabandFluxCD_0.png" />

# 소개:

이 블로그 포스트에서는 테라폼, gitlab, fluxcd 및 kustomize를 사용하여 엔드 투 엔드 gitops 워크플로를 설계하는 방법에 대해 논의하려고 합니다.

그러나 구현 세부 정보에 대해 깊이 파고들기 전에, gitops가 다루는 문제 설명과 테라폼이 어떻게 관련되는지에 대해 이해해 봅시다.

<div class="content-ad"></div>

# GitOps의 무엇과 왜

현재 우리가 인프라를 구축하는 방식을 살펴보면 대부분의 경우 인프라를 코드로 구축하거나 적어도 대부분의 클라우드 인프라가 그러한 방식으로 구축됩니다. 인프라를 코드로 사용하는 것의 하나의 어려움은 코드베이스가 성장하고 더 많은 사람들이 개발 프로세스에 참여함에 따라 코드베이스를 유지하는 것이 어려워진다는 것입니다.

이것이 바로 gitops가 나타나는 곳입니다. 핵심적으로 gitops는 git을 활용하여 인프라 코드의 코드 베이스를 유지하고 설정을 대상 환경에 자동으로 매칭하는 방식을 사용합니다. 이러한 방식으로 우리는 이전보다 빠르게 배포할 수 있을 뿐만 아니라 시간이 지남에 따라 무엇이 변경되었는지 완전한 기록이 있기 때문에 이전보다 빨리 오류를 감지하고 복구할 수 있습니다.

# GitOps에 대한 Terraform 선택이유

<div class="content-ad"></div>

지금쯤이면 terraform이 어떻게 관련되며 어떻게 도움이 되는지 궁금해할 수 있습니다. 이전 섹션에서는 구성을 매치하는 자동화된 방법에 대해 이야기했습니다. 일반적인 Kubernetes 환경에서는 fluxcd와 같은 도구를 사용하여 이를 수행합니다. 그러나 이러한 추가 구성과 설정이 필요합니다. 여기서 terraform이 등장합니다. terraform을 사용하면 인프라를 구축할 뿐만 아니라 flux와 같은 도구를 설치하고 구성할 수도 있습니다. 이렇게 하면 한 번에 Kubernetes 클러스터를 구축하고 flux를 부트스트랩하며 그에 따라 gitops 워크플로우를 설정할 수 있습니다. 이렇게 하면 처음부터 완전히 기능적인 gitops 워크플로우가 구축됩니다.

## 도구 개요:

## FluxCD:

Flux는 Kubernetes 클러스터를 구성 소스(예: Git 저장소)와 동기화하고 새 코드를 배포할 때 구성을 자동으로 업데이트하는 도구입니다.

<div class="content-ad"></div>

# Gitlab:

GitLab은 응용 프로그램 빌드 및 릴리스 프로세스를 자동화하는 데 도움이되는 CI/CD 도구입니다.

# Terraform:

Terraform을 사용하면 선언적 구성 언어를 사용하여 가상 머신, 네트워크, 저장소 및 기타 클라우드 서비스와 같은 인프라 리소스를 정의하고 프로비저닝할 수 있습니다.

<div class="content-ad"></div>

# 우리가 만들 것:

해결책에서는 nginx 앱을 eks 클러스터에 배포하고, flux를 사용하여 eks 클러스터에 대한 배포를 관리하며, 인프라 프로비저닝 및 flux 부트스트랩 구성을 위해 terraform을 사용합니다.

# 설정

# 준비 사항:

<div class="content-ad"></div>


```js
1) AWS 계정
2) Gitlab 클라우드 계정 / 자체 호스팅된 Gitlab
3) 로컬 시스템에 설치된 최신 버전의 Terraform

# 환경 설정:

## AWS 설정:

ECR Repository 생성
```

<div class="content-ad"></div>


AWS ECR 레지스트리에 로그인합니다.

```bash
aws ecr get-login-password | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com
```

Docker Hub에서 Nginx 이미지를 가져옵니다.

```

<div class="content-ad"></div>

```js
도커 pull nginx:latest
```

당신의 ECR 저장소에 Nginx 이미지를 태그하기

```js
도커 tag nginx:latest <aws_account_id>.dkr.ecr.<region>.amazonaws.com/nginx-repo:0.0.1
```

이미지를 ECR 저장소에 푸시하기



<div class="content-ad"></div>

```js
도커 푸시 <aws_account_id>.dkr.ecr.<region>.amazonaws.com/nginx-repo:0.0.1
```

## Gitlab 설정

PAT 토큰 생성

```js
- GitLab 인스턴스에 로그인하세요
- 프로필 아이콘을 클릭하세요
- 환경설정을 클릭한 후에 접근 토큰을 클릭하세요
- 토큰의 이름을 입력하고 만료 날짜를 설정하세요
- 그 다음에 api 라는 scopes 아래의 첫 번째 확인란을 선택하세요
- 개인 접근 토큰 만들기를 클릭한 후에 토큰을 표시하세요
- 토큰을 저장하세요.
```

<div class="content-ad"></div>

프로젝트 설정:

```js
- gitlab의 클라우드 인스턴스에서 새 프로젝트 버튼을 클릭합니다.
- 빈 프로젝트 생성을 클릭합니다.
- 프로젝트 이름을 제공합니다 (나중에 사용할 이름을 메모해 둡니다).
- 프로젝트 URL 옆의 드롭 다운에서 그룹 이름을 선택하고,
   이 그룹 이름을 나중에 사용할 것이니 메모해 둡니다.
- 프로젝트를 위한 이름을 제공합니다.
- 가시성을 선택합니다.
- 프로젝트를 저장합니다.
- 코드 버튼을 클릭하고, https용 github URL을 복사하여
  나중에 필요할 때를 대비하여 URL을 메모해 둡니다.
```

## Terraform 설정

Terraform 구성 및 스니펫을 사용하여 인프라를 구축하기 전에 아래 명령어를 사용하여 AWS 자격 증명을 설정해야 합니다.

<div class="content-ad"></div>

AWS 자격 증명 설정

```js
% export AWS_ACCESS_KEY_ID="anaccesskey"
% export AWS_SECRET_ACCESS_KEY="asecretkey"
% export AWS_REGION="us-west-2"
```

위 설정을 완료한 후 로컬 환경에서 gitlab 토큰을 설정해야 합니다. 아래 섹션에서 그 방법을 안내하겠습니다. gitlab 설정 섹션에서 pat 토큰 섹션에서 복사한 토큰을 붙여넣어주세요.

GitLab 토큰 설정 (Linux/MacOS)

<div class="content-ad"></div>

```js
export GITLAB_TOKEN=<Gitlab 설정에서 저장한 토큰>
```

Windows

```js
$env: GITLAB_TOKEN = "<Gitlab 설정에서 저장한 토큰>";
```

# 안내:

<div class="content-ad"></div>

# 데모 레포지토리 복제

```js
git clone https://gitlab.com/devops5480719/devops-samples.git
```

그런 다음 인프라 디렉토리로 이동하세요

```js
cd devops-samples/gitops-gitlab/infra/
```

<div class="content-ad"></div>

디렉토리 안으로 들어가시면 아래의 파일과 폴더를 찾을 수 있어요.

```js
├── config_files
│   ├── app-nginx.yml
│   ├── ecr-sync.yml
│   └── nginx.yml
├── main.tf
├── variables.tf
└── versions.tf
```

안에 config_files라는 폴더가 있고, 몇 개의 terraform 파일도 있어요.

우선 terraform 파일들을 자세히 살펴보도록 해봐요.

<div class="content-ad"></div>

## 변수 구성

먼저 변수 구성을 살펴봅시다.

variables.tf

```js
variable "gitlab_group" {
  type = string
  default = <gitlab 그룹의 이름>
}

variable "gitlab_project" {
  type = string
  default = <gitlab 프로젝트의 이름>
}

variable "aws_region" {
  type = string
  default = <리소스를 배포할 AWS 지역의 이름>
}
```

<div class="content-ad"></div>

위 코드 조각에서 기본 값 대신에 gitlab 섹션에서 이전에 복사한 이름이있는 gitlab_group으로 대체하십시오. gitlab_project에 대해서도 마찬가지입니다.

aws_region으로는 리소스를 배포할 지역을 선택하십시오.

## 공급자 구성

이제 versions.tf 파일에서 공급자 구성을 살펴봅시다.

<div class="content-ad"></div>

변수.tf 파일에는 인증 및 권한 부여를 위한 각 프로바이더 블록의 필수 구성 및 필수 제공자가 포함되어 있습니다.

버전.tf

```js
required_providers {
    flux = {
      source  = "fluxcd/flux"
      version = ">= 1.0.0"
    }
    gitlab = {
      source  = "gitlabhq/gitlab"
      version = ">=15.10.0"
    }
    aws = {
      source  = "hashicorp/aws"
      version = ">= 5.0"
    }
    kubernetes = {
      source  = "hashicorp/kubernetes"
      version = ">= 2.16.1"
    }
  }
}
provider "gitlab" {
  base_url = "https://gitlab.com/api/v4/"
}
provider "flux" {
  kubernetes = {
    host                   = module.eks.endpoint
    cluster_ca_certificate = base64decode(module.eks.cluster_ca_certificate)
    exec = {
      api_version = "client.authentication.k8s.io/v1beta1"
      args        = ["eks", "get-token", "--cluster-name", var.cluster_name]
      command     = "aws"
    }
  }
  git = {
    url = "ssh://git@gitlab.com/${data.gitlab_project.this.path_with_namespace}.git"
    ssh = {
      username    = "git"
      private_key = tls_private_key.flux.private_key_pem
    }
    branch = "main"
  }
}
```

## 리소스 구성:

<div class="content-ad"></div>

자원 구성을 살펴보겠습니다.

VPC 구성

main.tf

```js
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  name = "flux-vpc"
  cidr = "10.0.0.0/16"
  azs = ["<aws-region>a", "<aws-region>b", "<aws-region>c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24", "10.0.4.0/24", "10.0.5.0/24", "10.0.6.0/24"]
  public_subnets = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
  enable_nat_gateway = true
  enable_vpn_gateway = false
  single_nat_gateway = true
  public_subnet_tags = {
    "kubernetes.io/role/elb" = "1"
  }
  tags = {
    Terraform = "true"
    Environment = "dev"
  }
}
```

<div class="content-ad"></div>

EKS 구성

EKS를 위해 아래 리소스를 정의할 것입니다.

- EKS 클러스터
- kubeconfig를 컨텍스트에 추가하기 위한 Null 리소스

main.tf

<div class="content-ad"></div>

```md
모듈 "eks" {
source = "terraform-aws-modules/eks/aws"
version = "~> 20.0"
cluster_name = "flux-cluster"
cluster_version = "1.29"
cluster_endpoint_private_access = true
cluster_endpoint_public_access = true
cloudwatch_log_group_retention_in_days = 7
cloudwatch_log_group_class = "INFREQUENT_ACCESS"
cluster_enabled_log_types = ["api"]
vpc_id = module.vpc.vpc_id
subnet_ids = module.vpc.private_subnets
cluster_addons = {
coredns = {
most_recent = true
}
kube-proxy = {
most_recent = true
}
aws-ebs-csi-driver = {
most_recent = true
}
eks-pod-identity-agent = {
most_recent = true
}
vpc-cni = {
most_recent = true
}
}
eks_managed_node_group_defaults = {
ami_type = "AL2_x86_64"
disk_size = 50
instance_types = ["t3.large"]
capacity_type = "SPOT"
update_config = {
max_unavailable_percentage = 100
}
}
eks_managed_node_groups = {
ps-cluster-sample = {
min_size = 1
desired_size = 1
max_size = 4
instance_types = ["t3.large"]
capacity_type = "SPOT"
}
}
enable_cluster_creator_admin_permissions = false
access_entries = {
admin_sso = {
kubernetes_groups = []
principal_arn = "<arn of the role or the user to which you want to grant admin privileges>"
policy_associations = {
ClusterAdmin = {
policy_arn = "arn:aws:eks::aws:cluster-access-policy/AmazonEKSClusterAdminPolicy"
access_scope = {
type = "cluster"
}
}
EKSAdmin = {
policy_arn = "arn:aws:eks::aws:cluster-access-policy/AmazonEKSAdminPolicy"
access_scope = {
type = "cluster"
}
}
}
}
}
}
resource "null_resource" "update_kubeconfig" {
triggers = {
eks_cluster_id = module.eks.cluster_id
}
provisioner "local-exec" {
command = "aws eks update-kubeconfig --name ${module.eks.cluster_name} --region ${var.aws_region}"
}
}
```

<div class="content-ad"></div>

```js
자바스크립트
리소스 "tls_private_key" "flux" {
  depends_on = [module.eks, module.vpc, null_resource.update_kubeconfig]
  algorithm   = "ECDSA"
  ecdsa_curve = "P256"
}
데이터 "gitlab_project" "this" {
  path_with_namespace = "${var.gitlab_group}/${var.gitlab_project}"
}
리소스 "gitlab_deploy_key" "this" {
  depends_on = [module.eks, module.vpc]
  project  = data.gitlab_project.this.id
  title    = "Flux"
  key      = tls_private_key.flux.public_key_openssh
  can_push = true
}
리소스 "flux_bootstrap_git" "this" {
  depends_on = [gitlab_deploy_key.this, module.eks, module.vpc, null_resource.update_kubeconfig]
  path = "clusters/${module.eks.cluster_name}"
  components_extra = ["image-reflector-controller","image-automation-controller"]
}
로컬 {
  yaml_files = fileset("${path.module}/config_files", "*.yml")
  yaml_content = { for file in local.yaml_files : file => file("${path.module}/config_files/${file}") }
}
리소스 "gitlab_repository_file" "yaml_files" {
  depends_on = [flux_bootstrap_git.this, null_resource.update_kubeconfig]
  for_each = local.yaml_content
  project = data.gitlab_project.this.id // 새 프로젝트를 생성한 경우 기존 프로젝트나 gitlab_project.example_project.id의 프로젝트 ID를 사용하십시오.
  file_path = "clusters/${module.eks.cluster_name}/${each.key}"
  content   = base64encode(each.value)
  commit_message = "added flux image configs"
  branch    = "main"
}
```

위의 코드 조각에서는 flux_bootstrap_git 리소스에 image-reflector-controller 및 image-automation-controller라는 두 가지 추가 컴포넌트를 추가하고 있습니다.

이 두 컨트롤러는 ECR 리포지토리에서 태그를 가져오거나 업데이트할 책임을 질 것입니다.

flux가 이 작업을 수행하려면 복제 데모 리포지토리의 config_files 폴더에서 찾을 수 있는 몇 가지 추가 구성 요소를 정의해야 합니다.```

<div class="content-ad"></div>


├── config_files
│   ├── app-nginx.yml
│   ├── ecr-sync.yml
│   └── nginx.yml
├── main.tf
├── variables.tf
└── versions.tf


이제 이 파일들 각각을 살펴보고 그 내용을 살펴보겠습니다. 우선 app-nginx.yaml 파일부터 시작해봅시다.

app-nginx.yaml

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: nginx-auth-pat
  namespace: flux-system
type: Opaque
data:
  password: < gitlab 토큰을 base64로 인코딩 된 문자열 >
  username: < 사용자 이름을 base64로 인코딩 된 문자열 >
---
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: nginx
  namespace: flux-system
spec:
  interval: 1m0s
  ref:
    branch: main
  url: <gitlab_repo_url>
  secretRef:
    name: nginx-auth-pat
---
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: nginx
  namespace: flux-system
spec:
  prune: true
  interval: 1m0s
  path: "./app/nginx/environments/production"
  sourceRef:
    kind: GitRepository
    name: nginx
---
apiVersion: image.toolkit.fluxcd.io/v1beta2
kind: ImageRepository
metadata:
  name: nginx
  namespace: flux-system
spec:
  image: <aws-account-id>.dkr.ecr.<region>.amazonaws.com/<image name>
  interval: 1m0s
  secretRef:
    name: ecr-credentials
---
apiVersion: image.toolkit.fluxcd.io/v1beta2
kind: ImagePolicy
metadata:
  name: nginx
  namespace: flux-system
spec:
  imageRepositoryRef:
    name: nginx
  policy:
    semver:
      range: 0.0.x
---
apiVersion: image.toolkit.fluxcd.io/v1beta1
kind: ImageUpdateAutomation
metadata:
  name: nginx
  namespace: flux-system
spec:
  interval: 1m0s
  sourceRef:
    kind: GitRepository
    name: nginx
  git:
    checkout:
      ref:
        branch: main
    commit:
      author:
        email: flux@example.com
        name: flux
      messageTemplate: "{range .Updated.Images}{println .}{end}"
    push:
      branch: main
  update:
    path: ./clusters/flux-cluster/nginx.yml
    strategy: Setters
```



<div class="content-ad"></div>

위 구성에서는 아래 구성 요소를 정의했습니다:

- Secret: 우리는 gitlab 설정 섹션에서 만든 base64로 인코딩된 pat 토큰 및 gitlab의 git repo에 대한 사용자 이름을 정의했습니다. 사용자 이름은 terraform의 provider 섹션 구성에 따라 'git'을 사용했습니다.
- GitRepository: 이는 flux가 yaml 파일을 폴링할 gitlab 리포지토리 URL입니다. 이는 gitlab 설정 섹션에서 만든 파일입니다.
- ImageRepository: 특정 태그 집합을 스캔하고 저장할 리포지토리를 정의했습니다.
- ImagePolicy: 이미지 리포지토리에서 "latest" 이미지를 선택하는 규칙을 정의합니다.
- ImageUpdateAutomation: 동일한 네임스페이스 내의 이미지 정책 객체를 기반으로 git 리포지토리를 업데이트할 자동화 프로세스를 정의합니다.

ImageUpdateAutomation 아래 파일에서, flux가 이미지 태그를 업데이트할 경로를 .spec.update.path 아래 ./clusters/flux-cluster/nginx.yml로 정의했습니다. 이것은 flux에 이미지 태그를 업데이트할 위치를 알려줍니다.

위 구성 이후에는 또한 ecr 동기화 파일을 정의해야 합니다. 이 파일은 ecr에 대한 인증을 자동화하고 이미지 자동화 컨트롤러가 ecr 리포지토리에서 이미지 태그를 가져올 수 있도록 합니다. 아래 파일은 매 6시간마다 ecr에서 인증하는 데 필요한 권한을 가진 크론 작업을 설정하고 비밀 정보를 업데이트합니다.

<div class="content-ad"></div>

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: ecr-credentials-sync
  namespace: flux-system
rules:
- apiGroups: [""]
  resources:
  - secrets
  verbs:
  - get
  - create
  - patch
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: ecr-credentials-sync
  namespace: flux-system
subjects:
- kind: ServiceAccount
  name: ecr-credentials-sync
roleRef:
  kind: Role
  name: ecr-credentials-sync
  apiGroup: ""
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ecr-credentials-sync
  namespace: flux-system
  # Uncomment and edit if using IRSA
  # annotations:
  #   eks.amazonaws.com/role-arn: <role arn>
---
apiVersion: batch/v1
kind: CronJob
metadata:
  name: ecr-credentials-sync
  namespace: flux-system
spec:
  suspend: false
  schedule: 0 */6 * * *
  failedJobsHistoryLimit: 1
  successfulJobsHistoryLimit: 1
  jobTemplate:
    spec:
      template:
        spec:
          serviceAccountName: ecr-credentials-sync
          restartPolicy: Never
          volumes:
          - name: token
            emptyDir:
              medium: Memory
          initContainers:
          - image: amazon/aws-cli
            name: get-token
            imagePullPolicy: IfNotPresent
            # You will need to set the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY environment variables if not using
            # IRSA. It is recommended to store the values in a Secret and load them in the container using envFrom.
            # envFrom:
            # - secretRef:
            #     name: aws-credentials
            env:
            - name: REGION
              value: us-east-1 # change this if ECR repo is in a different region
            volumeMounts:
            - mountPath: /token
              name: token
            command:
            - /bin/sh
            - -ce
            - aws ecr get-login-password --region ${REGION} > /token/ecr-token
          containers:
          - image: ghcr.io/fluxcd/flux-cli:v0.25.2
            name: create-secret
            imagePullPolicy: IfNotPresent
            env:
            - name: SECRET_NAME
              value: ecr-credentials
            - name: ECR_REGISTRY
              value: <account id>.dkr.ecr.<region>.amazonaws.com # fill in the account id and region
            volumeMounts:
            - mountPath: /token
              name: token
            command:
            - /bin/sh
            - -ce
            - |-
              kubectl create secret docker-registry $SECRET_NAME \
                --dry-run=client \
                --docker-server="$ECR_REGISTRY" \
                --docker-username=AWS \
                --docker-password="$(cat /token/ecr-token)" \
                -o yaml | kubectl apply -f -
```

<div class="content-ad"></div>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  template:
    spec:
      containers:
        - name: nginx
          image: <aws-account-id>.dkr.ecr.<aws-region>.amazonaws.com/nginx:0.0.0 # {"$imagepolicy": "flux-system:nginx"}
```

위의 두 줄 # '"$imagepolicy": "flux-system:nginx"' 는 flux-system이 네임스페이스이고 nginx가 이미지 정책의 이름임을 나타내는 표식입니다. 이는 app-nginx.yml 파일에서 정의된 자동화 구성과 일치해야 합니다.

이 작은 단편은 flux에게 컨테이너 이미지를 업데이트할 때 사용해야 하는 정책을 알려줍니다.

이 모든 파일은 이것을 적용하기 위해 flux 폴더로 복사되어 메인.tf 파일의 아래 코드 단편을 통해 eks 클러스터에 적용될 것입니다.```

<div class="content-ad"></div>

```js
locals {
  yaml_files = fileset("${path.module}/config_files", "*.yml")
  yaml_content = { for file in local.yaml_files : file => file("${path.module}/config_files/${file}") }
}
resource "gitlab_repository_file" "yaml_files" {
  depends_on = [flux_bootstrap_git.this, null_resource.update_kubeconfig]
  for_each = local.yaml_content
  project = data.gitlab_project.this.id // Use the project ID of an existing project or gitlab_project.example_project.id if you created a new project
  file_path = "clusters/${module.eks.cluster_name}/${each.key}"
  content   = base64encode(each.value)
  commit_message = "added flux image configs"
  branch    = "main"
}
```

# 배포:

그 다음 아래 명령어를 실행하여 Terraform 초기화를 수행하십시오.

```js
terraform init
```

<div class="content-ad"></div>

그럼 진행되는 모든 리소스를 확인하려면 plan 명령어를 실행해주세요.

```js
terraform plan
```

그 다음에는 테라폼 설정을 적용할 수 있어요.

```js
terraform apply --auto-approve
```

<div class="content-ad"></div>

아래 Terraform이 다음 작업을 수행할 것입니다.

```js
VPC 및 지원 리소스 생성
EKS 클러스터 및 지원 리소스 생성
EKS와 함께 Flux를 부트스트랩하고 모든 구성 및 애플리케이션 파일을 GitLab 저장소에 저장
```

Terraform 실행이 완료되면 아래 명령을 실행하세요.

```js
kubectl get po -n flux-system
```

<div class="content-ad"></div>

아래의 Flux 구성 요소가 모두 실행 중인 것을 확인해야 합니다.

```js
NAME                                           READY   STATUS    RESTARTS   AGE
helm-controller-5f7457c9dd-6svk8               1/1     Running   0          40s
image-automation-controller-79447887bb-blqp4   1/1     Running   0          40s
image-reflector-controller-65df777f5c-nrgsx    1/1     Running   0          40s
kustomize-controller-5f58d55f76-tpgs6          1/1     Running   0          40s
notification-controller-685bdc466d-mp46f       1/1     Running   0          40s
source-controller-86b8b57796-vl2fp             1/1     Running   0          39s
```

이제 모든 구성 요소가 실행 중이므로, ecr 비밀 cron 작업이 실행되었는지 확인해보겠습니다. 터미널에서 다음 명령을 실행하세요.

```js
kubectl get cronjob -n flux-system
```

<div class="content-ad"></div>

아래 출력을 얻어야 합니다.

```js
NAME                   SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
ecr-credentials-sync   0/1 * * * *   False     0        51초           2분 42초
```

이제 flux-system 네임스페이스에 시크릿이 있는지 확인해 보겠습니다. 아래 출력이 표시되어야 합니다:

```js
k get secret -n flux-system

NAME              TYPE                             DATA   AGE
ecr-credentials   kubernetes.io/dockerconfigjson   1      3분 7초
flux-system       Opaque                           3      5분 39초
nginx-auth-pat    Opaque                           2      4분 19초
```

<div class="content-ad"></div>

위의 출력에서 확인할 수 있듯이 ecr-credentials라는 시크릿이 있습니다.

이제 ecr을 확인해 봅시다.

이제 flux 이미지 컨트롤러가 우리의 ecr 저장소에서 태그를 가져올 수 있는지 확인해 보겠습니다.

```js
flux get image all
NAME                   LAST SCAN                       SUSPENDED       READY   MESSAGE
imagerepository/nginx   2024-05-23T16:55:46+05:30       False           True    successful scan: found 2 tags
NAME                    LATEST IMAGE                                                    READY   MESSAGE
imagepolicy/nginx       <aws-account-id>.dkr.ecr.<aws-region>.amazonaws.com/nginx:0.0.1       True    '<aws-account-id>.dkr.ecr.<aws-region>.amazonaws.com/nginx'의 최신 이미지 태그가 0.0.1로 해결되었습니다
NAME                            LAST RUN                        SUSPENDED       READY   MESSAGE
imageupdateautomation/nginx     2024-05-23T16:55:39+05:30       False           True    레포지토리가 최신 상태입니다
```

<div class="content-ad"></div>

위의 출력에서 볼 수 있듯이, 이미지 정책은 ECR에서 최신 이미지 태그를 스캔하고 가져왔습니다.

이제 아래 명령을 사용하여 배포에 동일한 업데이트가 반영되었는지 확인해봅시다:

```js
kubectl get deployment nginx-deployment -o=jsonpath='{.spec.template.spec.containers[*].image}' | awk -F '[:@]' '{print $1, $2}'
```

아래는 우리가 얻은 출력입니다. 이미지가 업데이트된 것을 확인할 수 있습니다.

<div class="content-ad"></div>

```js
<aws-account-id>.dkr.ecr.<aws-region>.amazonaws.com/nginx:0.0.1
```

이제 깃랩 쪽으로 이동하여 플럭스가 필요한 변경 사항을 수행했는지 확인해보세요.

UI를 통해 레포지토리의 커밋 히스토리를 확인하거나 레포지토리를 복제한 후 아래 명령어를 실행할 수 있습니다.

아래 명렁어를 실행해보세요:```

<div class="content-ad"></div>

```bash
git log --author="flux" --since="today 00:00" --until="today 23:59" --pretty=format:"%h %s" -- clusters/flux-cluster/nginx.yml
```

아래는 출력된 내용입니다:

```bash
85e5b48 <aws-account-d>.dkr.ecr.<aws-region>.amazonaws.com/nginx:0.0.1
```

위의 출력에서 확인할 수 있듯이 flux가 nginx.yml 파일에서 임의의 이미지 번호에서 실제 태그로 이미지를 업데이트했으며, 동일한 업데이트가 배포에서 수행되었습니다.

<div class="content-ad"></div>

이미지의 새로운 버전을 푸시해보고 flux가 자동으로 업데이트되는지 확인해봐요.

ECR 저장소에 Nginx 이미지를 태그해보세요.

```js
docker tag nginx:latest <aws_account_id>.dkr.ecr.<region>.amazonaws.com/nginx-repo:0.0.2
```

이제 아래 명령어를 사용하여 배포가 정상적으로 업데이트되었는지 확인해보세요:

<div class="content-ad"></div>

아래는 표시되는 출력 값 입니다:

```bash
<aws-account-id>.dkr.ecr.<aws-region>.amazonaws.com/nginx:0.0.2
```

# 결론



<div class="content-ad"></div>

그래서 결론적으로, 우리는 flux, gitlab, terraform, kubernetes를 사용하여 gitops 워크플로우를 설정했고, 새 이미지를 컨테이너 저장소에 푸시할 때마다 flux가 배포를 관리할 것입니다.

# 참고 자료:

Flux 문서: [https://fluxcd.io/flux/](https://fluxcd.io/flux/)

