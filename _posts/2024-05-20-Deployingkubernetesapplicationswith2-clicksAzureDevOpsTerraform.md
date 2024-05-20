---
title: "2클릭으로 Kubernetes 애플리케이션 배포하기  Azure DevOps, Terraform"
description: ""
coverImage: "/assets/img/2024-05-20-Deployingkubernetesapplicationswith2-clicksAzureDevOpsTerraform_0.png"
date: 2024-05-20 17:25
ogImage: 
  url: /assets/img/2024-05-20-Deployingkubernetesapplicationswith2-clicksAzureDevOpsTerraform_0.png
tag: Tech
originalTitle: "Deploying kubernetes applications with 2-clicks | Azure DevOps , Terraform"
link: "https://medium.com/@geralexgr/deploying-kubernetes-applications-with-2-clicks-azure-devops-terraform-944fe008a9cb"
---


제목을 보고 클릭베이트일 것이라고 생각할 수 있지만, 이 기사를 끝까지 읽어보는 것이 좋습니다. 인프라를 코드로 생성하면 Azure DevOps와 테라폼을 사용하여 k8s 애플리케이션을 배포하는 것이 매우 쉬워질 수 있음을 확인할 수 있습니다.

이 예제에서는 Azure DevOps 파이프라인과 테라폼을 활용하여 Azure에서 실행되는 AKS 클러스터에 yaml 정의를 배포할 것입니다. 이를 위해 세 단계가 필요합니다.

첫 번째 단계는 Azure에서 AKS 클러스터를 생성하는 것입니다. 인프라가 준비되면 Azure DevOps 파이프라인을 AKS 리소스와 바인딩하여 클러스터에 배포할 수 있도록 해야 합니다. 마지막 단계는 배포하고 실행할 애플리케이션의 yaml 정의를 가져와 Azure DevOps 내에서 애플리케이션 배포 프로세스를 실행하는 것입니다.

![이미지](/assets/img/2024-05-20-Deployingkubernetesapplicationswith2-clicksAzureDevOpsTerraform_0.png)

<div class="content-ad"></div>

프로젝트 구조는 아래 그림과 같이 구성되어 있습니다.

- 코드 폴더에는 yaml k8s 정의 파일이 포함되어 있습니다.
- iac_aks는 Azure 내에서 AKS 클러스터를 생성합니다.
- iac_devops는 필요한 Azure DevOps 리소스(AKS와의 서비스 연결)를 생성합니다.
- 마지막으로 azure-pipeline 및 application-pipeline은 자동화를 실행하고 작업을 수행할 파이프라인입니다.

예제를 시도해 보려면 먼저 Azure DevOps 내에 변수 그룹을 생성하고 두 가지 값을 저장해야 합니다. 첫 번째 값은 Azure DevOps 리소스를 만들 때 사용할 비밀 개인 액세스 토큰입니다. 두 번째 값은 Azure DevOps 조직의 URL입니다.

<div class="content-ad"></div>

아래는 마크다운 형식으로 변경한 코드입니다:

```
![Deploying Kubernetes applications with 2 clicks Azure DevOps Terraform](/assets/img/2024-05-20-Deployingkubernetesapplicationswith2-clicksAzureDevOpsTerraform_2.png)

위 설정이 완료되면 tfvars 파일을 변경하고, 생성할 리소스에 사용하고자 하는 이름을 추가해야 합니다. 마지막으로 인프라 파이프라인을 클릭 한 번, 애플리케이션 파이프라인을 클릭 한 번으로 배포를 끝내실 수 있습니다.

![Deploying Kubernetes applications with 2 clicks Azure DevOps Terraform](/assets/img/2024-05-20-Deployingkubernetesapplicationswith2-clicksAzureDevOpsTerraform_3.png)

코드는 Github에 호스팅되어 있습니다.
https://github.com/geralexgr/globalazuregreece2024
```

<div class="content-ad"></div>

https://blog.geralexgr.com에 2024년 5월 19일에 원래 게시되었습니다.
더 많은 기사를 보려면 내 계정을 팔로우해보세요. Medium의 모든 이야기에 완전 액세스하려면 회원이 되어주세요.