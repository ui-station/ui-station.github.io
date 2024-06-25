---
title: " 테라폼을 사용하여 쿠버네티스에서 MERN 앱을 위한 엔드투엔드 CICD 파이프라인 구축하기, GitHub Actions와 Ansible으로"
description: ""
coverImage: "/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_0.png"
date: 2024-05-18 16:59
ogImage:
  url: /assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_0.png
tag: Tech
originalTitle: "🧬Build an End-to-End CI CD Pipeline for a MERN App in Kubernetes with Terraform using GitHub Actions , Ansible"
link: "https://medium.com/aws-in-plain-english/build-an-end-to-end-ci-cd-pipeline-for-mern-app-terraform-using-github-actions-with-ansible-d7686ccc8db1"
---

<img src="/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_0.png" />

쿠버네티스에서 MERN 웹 애플리케이션을 위한 완벽한 CI/CD 파이프라인을 알아보고 테라폼을 GitHub Actions와 통합하여 인프라 프로비저닝 및 관리를 자동화합니다. AWS EC2 구성, Kubernetes (K3s) 설치 및 EC2에 풀 스택 MERN 프로젝트를 배포하는 데 Ansible을 사용합니다.

# 🛠️사전 요구 사항

- 필요한 권한을 갖춘 AWS 계정
- 전체 스택 응용 프로그램(MERN)을 컨테이너화
- HCP Terraform, GitHub 및 Docker Hub 계정
- AWS 클라우드의 기본 지식
- DNS 편집 권한이 있는 도메인

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

이 기사는 조금 길어요. 그래서 마지막 단계로 따라와서 테라폼을 사용해 쿠버네티스에 MERN 앱용 엔드투엔드 CI/CD 파이프라인을 구축하는 방법을 배우시면 좋겠네요. GitHub Actions와 Ansible을 활용해요.

이 프로젝트는 고급 수준의 프로젝트에요. 그래서 AWS, 테라폼, 앤서블, MERN 프로젝트, GitHub Actions의 기본 개념이 명확하다고 가정할게요. 기본 수준은 말하지 않아요. 그냥 프로젝트를 통합하고 구현했어요.

이 기사는 세 가지 섹션으로 구성되어 있어요:

섹션 1: GitHub Actions를 사용해 테라폼을 활용해 AWS 인프라 배포를 자동화하기.

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

Section 2: EC2 구성, Kubernetes (K3s) 설치 및 Ansible을 사용하여 MERN 앱 배포하기.

Section 3: GitHub Actions를 사용한 Kubernetes (K3s)에서 MERN 앱을 위한 CI/CD 파이프라인 구성.

그럼, 지금 가보자, 만약 이 글에 대한 질문이 있거나 도움이 필요하면 언제든지 연락해 주세요.

# 📘Section 1: GitHub Actions를 이용하여 Terraform을 사용하여 AWS 인프라 자동화하기

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

우선 GitHub에서 Terraform을 사용하여 AWS 인프라를 생성하기 위해 GitHub 액션을 이용해 git 저장소(terraform)를 복제하세요.

단계 1: 로컬 머신에서 저장소를 복제합니다.

```js
git clone https://github.com/bjnandi/terraform-ci-cd-aws.git
```

복제 후에는 VS Code 편집기의 Dev Container에서 해당 저장소를 엽니다. Dev Container를 사용하면 주요 머신으로부터 환경(Terraform)을 격리할 수 있습니다.

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

단계 2: HCP Terraform Cloud 설정하기
우리가 생성할 GitHub Action은 HCP Terraform Cloud에 연결하여 구성을 계획하고 적용할 것입니다. 액션 워크플로우를 설정하기 전에, HCP Terraform Cloud에 로그인하고 조직 내에서 워크스페이스를 생성한 다음 AWS 자격 증명을 HCP Terraform 워크스페이스에 추가해야 합니다. 다음 단계를 따라주세요:

조직 `워크스페이스` 변수

![이미지](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_1.png)

단계 3: API 토큰 생성하기
HCP Terraform 사용자 API 토큰을 생성하세요. 이를 위해 HCP Terraform 사용자 설정의 토큰 페이지로 이동하세요. API 토큰 생성을 클릭한 다음 "Generate token"을 클릭하여 GitHub Actions 토큰을 설명란에 입력하세요.

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

![이미지](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_2.png)

**단계 4:** GitHub Secrets에 GitHub 저장소에 API 토큰을 설정합니다.
자세한 내용은 GitHub Secrets에서 확인하실 수 있습니다. 이제 시크릿과 변수로 이동하여 값을 설정합니다.

GitHub `저장소 이름` 설정 `시크릿과 변수` 작업 `저장소 시크릿` 새 저장소 시크릿 :

TF_API_TOKEN: HCP Terraform 사용자 설정에서 생성된 API 토큰입니다.

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

<img src="/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_3.png" />

5단계: VS Code 편집기로 돌아가서 "tf-infa-test"라는 이름의 새 브랜치를 만듭니다.

```js
git checkout -b 'tf-infa-test'
```

그런 다음 "env/dev" 디렉토리의 main.tf 파일을 엽니다. HCP Terraform에서 생성된 HCP Terraform 조직 및 워크스페이스 이름으로 "organization"과 "workspaces"를 설정한 다음 파일을 저장하세요.

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
  cloud {
    organization = "YOUR-ORGANIZATION-HERE"

    workspaces {
      name = "YOUR-WORKS-SPACE"
    }
  }
```

그런 다음 ".github/workflows/" 디렉토리에서 두 개의 워크플로 파일 (terraform-plan.yml 및 terraform-plan.yml)을 엽니다.

- terraform-plan.yml
- terraform-plan.yml

“TF_CLOUD_ORGANIZATION” 및 “TF_WORKSPACE”를 HCP Terraform 조직 및 워크스페이스의 이름으로 업데이트하고 파일을 저장하세요.

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

6단계: 저희의 테라폼 파일 구조는 루트 작업 디렉토리가 "env/dev"인 블루프린트 모델입니다. 그래서 HCP 테라폼 클라우드에서 테라폼 작업 디렉토리에 "env/dev"를 추가했습니다.

이를 위해 테라폼 클라우드로 이동하십시오:

Organizations `Workspaces` Workspace Name `Setting` Terraform Working Directory

```js
env / dev;
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

아래의 코드를 추가하세요.

![image](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_4.png)

마지막으로 "Save settings"를 클릭하세요.

이제 GitHub Actions를 사용하여 인프라 프로비저닝을 준비했습니다.

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

**단계 7: 풀 리퀘스트 생성하기**
이제 VS Code 편집기로 돌아가서 데모 테스트용 코드를 약간 수정한 후, 해당 코드를 GitHub "tf-infa-test" 브랜치에 커밋하고 푸시하세요. 그런 다음 GitHub에서 이 브랜치에 대한 풀 리퀘스트를 생성하세요.

풀 리퀘스트를 생성한 후 GitHub 워크플로우(테라폼 플랜)를 실행하여 테라폼 플랜을 확인하세요.

![이미지](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_5.png)

**단계 8: 풀 리퀘스트 검토 및 병합**
"Terraform plan"에 만족한다면, 'main' 브랜치에 '병합' 코드로 풀 리퀘스트를 닫은 다음 인프라 구축을 위해 워크플로우(테라폼 애플라이)를 실행하세요.

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

![Step 9](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_6.png)

9단계: 프로비전된 EC2 인스턴스 확인

모든 리소스가 생성되었습니다.

![Step 9](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_7.png)

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

# 📘 섹션 2: EC2 구성, Kubernetes (K3s) 설치 및 Ansible를 사용하여 MERN 앱 배포

인프라 프로비저닝이 완료되면 GitHub의 두 번째 git 저장소 (Ansible)로 이동하여 AWS EC2를 구성하고 Kubernetes (K3s)를 설치하고 Ansible를 사용하여 MERN 앱을 배포합니다.

단계 1: 로컬 머신에서 저장소를 복제합니다.

```js
git clone https://github.com/bjnandi/ansible-k3s-nginx-lb.git
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

깃 저장소를 복제한 후, VS Code의 Dev Container에서 열어보세요. Dev Container는 환경(Ansible)을 메인 머신과 격리시킵니다.

단계 2: SSH 키를 위해 ".ssh" 폴더에 "linux.pem" 파일을 만드세요.

```bash
nano ~/.ssh/linux.pem
```

여기에 "pem" 파일 코드를 붙여넣고 파일을 저장하세요.

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

Step 3: "linux.pem" 파일의 권한을 설정하세요

```js
chmod 400 ~/.ssh/linux.pem
```

Step 4: 이제 디렉토리의 권한을 설정하세요. Dev Container는 모든 사용자에 대해 기본 권한으로 디렉토리를 마운트합니다.

```js
chmod 755 /workspaces/ansible-k3s-nginx-lb
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

단계 5: 이제 우리의 AWS EC2 IP 주소로 IP 주소를 업데이트하세요.

- “hosts” 파일에서:

- bastion (공용 IP)
- k3s_nodes, additional_agent_nodes 및 nginx_lb (사설 IP)
- nginx_lb(사설 IP)
- ProxyCommand용 (공용 IP)

```js
[bastion]
bastion ansible_host=34.195.33.137
[bastion:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/linux.pem

[k3s_nodes]
master ansible_host=10.0.1.222
[k3s_nodes:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/linux.pem
ansible_ssh_common_args='-o ProxyCommand="ssh -o StrictHostKeyChecking=no -i ~/.ssh/linux.pem -W %h:%p -q ubuntu@34.195.33.137"'

[additional_agent_nodes]
worker1 ansible_host=10.0.2.244
worker2 ansible_host=10.0.1.236
[additional_agent_nodes:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/linux.pem
ansible_ssh_common_args='-o ProxyCommand=" ssh -o StrictHostKeyChecking=no -i ~/.ssh/linux.pem -W %h:%p -q ubuntu@34.195.33.137"'

[nginx_lb]
nginx_lb ansible_host=10.0.5.187
[nginx_lb:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/linux.pem
ansible_ssh_common_args='-o ProxyCommand=" ssh -o StrictHostKeyChecking=no -i ~/.ssh/linux.pem -W %h:%p -q ubuntu@34.195.33.137"'
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

2. "nginx.conf" 파일에서:

- 업스트림 클라이언트 및 API 서버

```js
   upstream client {
        server 10.0.1.222:30001; # 귀하의 EC2 인스턴스 사설 IP로 교체하세요
        server 10.0.2.244:30001; # 귀하의 EC2 인스턴스 사설 IP로 교체하세요
        server 10.0.1.236:30001; # 귀하의 EC2 인스턴스 사설 IP로 교체하세요
    }

    upstream apiserver {
        server 10.0.1.222:30005;  # 귀하의 EC2 인스턴스 사설 IP로 교체하세요
        server 10.0.2.244:30005; # 귀하의 EC2 인스턴스 사설 IP로 교체하세요
        server 10.0.1.236:30005; # 귀하의 EC2 인스턴스 사설 IP로 교체하세요
    }
```

3. 이제 "project_vars" 파일에서:

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
master_ip: 10.0.1.222
```

단계 6: Ansible 구성이 실행 준비 완료되었습니다

- Makefile을 사용하여 모든 구성을 한 주석에서 실행하려면:

```js
make run_ansible
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

- 하나씩 실행하려면:

```js
 ansible-playbook -i hosts k3s_install.yaml

 ansible-playbook -i hosts k3s_mern_deploy.yaml

 ansible-playbook -i hosts config_nginx_lb.yaml

 ansible-playbook -i hosts config_bastion.yaml
```

이제, 모든 EC2를 구성하고 Kubernetes(k3s)를 설치한 다음 Kubernetes(k3s)에 MERN 앱을 배포합니다.

단계 7: 이제 도메인 DNS 설정으로 이동하여 "A" 레코드를 업데이트합니다. 저희 도메인은 Squarespace에서 제공합니다. 그래서, Squarespace DNS 설정에서 이 도메인 DNS를 업데이트했습니다.

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

우리 앱에 대해 EC2 인스턴스(개발 로드밸런서)의 공개 IP로 두 개의 사용자 정의 "A" 레코드를 추가했어요.

![이미지](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_8.png)

단계 8: 이제 도메인을 사용하여 앱을 보여줄 차례에요.

이 도메인(http://bjtechlife.com)에서 우리 앱을 봤어요.

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

만약 Docker Hub repository 이미지가 있거나 수동으로 이미지를 빌드하여 Docker Hub에 푸시했다면, 먼저 출력에서 앱 UI를 확인할 수 있습니다. 그렇지 않은 경우 (섹션 3)를 완료한 다음 출력에서 앱 UI를 확인할 수 있습니다.

![](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_9.png)

# 📘세션 3: Kubernetes (k3s)에서 MERN 앱을 위한 CI/CD 파이프라인 구축하기 (GitHub Actions 사용)

EC2 구성, (k3s) 설치 및 앱 배포를 완료하고, AWS EC2 인스턴스에서 GitHub Actions를 사용하여 Kubernetes (k3s)에서 MERN 앱을 위한 CI/CD 파이프라인을 만들기 위해 GitHub의 세 번째 git 저장소(MERN 프로젝트)로 이동하세요.

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

스텝 1: 로컬 머신에서 저장소를 복제하세요

```js
git clone https://github.com/bjnandi/ci-cd-pipeline-MERN-k3s.git
```

복제한 후, VS Code 에디터에서 열어주세요.

".github/workflows/" 디렉토리에 두 개의 워크플로우 파일 (docker-ci 및 k3s-cd)이 있습니다.

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

- docker-ci.yml
- k3s-cd.yml

첫 번째 워크플로우 "docker-ci.yml" 파일은 이미지를 빌드하여 Docker Hub에 푸시하고, 다른 워크플로우 "k3s-cd.yml" 파일은 Kubernetes(k3s)에서 이미지를 업데이트합니다.

단계 2: 이제 GitHub Secrets를 사용하여 시크릿을 채워넣어야합니다. GitHub 리포지토리에 추가할 수 있습니다. 자세한 정보는 GitHub Secrets에서 확인할 수 있습니다. 이제 시크릿과 변수로 이동하여 값을 설정합니다.

Github `Repo Name` 설정 `Secrets and variables` 작업 `Repository Secrets` 새 리포지토리 시크릿 :

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

EC2_HOST: 배스천 호스트 EC2 인스턴스의 공개 IP 주소는 대략 이와 같을 것입니다. "34.195.33.137".

MASTER_NODE: k8s-instance-master EC2 인스턴스의 사설 IP 주소는 대략 이와 같을 것입니다. "10.0.1.222".

EC2_USERNAME: EC2 인스턴스의 사용자 이름은 일반적으로 "ubuntu"입니다.

SSH_PRIVATE_KEY: 인스턴스에 로그인하는 데 사용할 “.pem” 파일입니다.

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

DOCKER_USERNAME: 이것은 도커 허브 계정 프로필로 이동하면 확인할 수 있는 도커 허브의 "사용자 이름"입니다. 도커 이미지를 도커 허브에 푸시하는 데 사용됩니다.

DOCKER_PASSWORD: 이것은 도커 허브 계정의 "비밀번호"입니다.

![이미지](/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_10.png)

단계 3: "VS Code Editor"로 돌아가서 데모 테스트를 위해 코드나 UI 파일을 편집하거나 작은 변경 사항을 수행한 후, 현재 파일에서 앱의 CI/CD 테스트를 위해 GitHub의 "main" 브랜치에 코드를 커밋하고 푸시하세요.

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

고객님, `src` 폴더의 `components` 폴더 안에 있는 `Navbar.js` 파일을 수정해야 합니다.

"CRUD" 텍스트를 "CRUD test"로 변경한 다음, 변경사항을 커밋하고 메인 브랜치에 푸시하세요.

<img src="/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_11.png" />

빌드, 푸시, 그리고 배포하는 데 시간이 걸릴 수 있습니다. 나중에 앱 UI(http://bjtechlife.com)에 변경 사항이 표시되고 데이터를 삽입하는 것과 같이 제대로 작동하는 것을 확인할 수 있습니다.

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

<img src="/assets/img/2024-05-18-BuildanEnd-to-EndCICDPipelineforaMERNAppinKuberneteswithTerraformusingGitHubActionsAnsible_12.png" />

와우! 😎🚀 여기에는 우리가 코드에서 변경한 "CRUD 테스트"가 나와 있네요.

저희 앱 UI에서 서로 다른 변경을 계속해서 적용하면 변경 사항을 볼 수 있어요.

결과적으로, GitHub Actions를 사용하여 Kubernetes(k3s)에서 MERN 앱을 위한 엔드 투 엔드 CI/CD 파이프라인을 완료했어요.

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

🌟축하합니다!!🌟, 저희가 GitHub Actions 및 Ansible을 사용하여 Kubernetes에서 Terraform을 활용하여 MERN 앱을 위한 End-to-End CI/CD 파이프라인을 성공적으로 구축했습니다.

✨이제 AWS에서 리소스를 제거할 시간입니다. 그러므로, Terraform 저장소 VS Code 편집기 터미널(/workspaces/terraform-ci-cd-aws/env/dev)로 돌아가서 다음을 실행해주세요:

```js
terraform login
terraform init
terraform destroy -auto-approve
```

이렇게 함으로써 저와 함께 오래 집중해 주셔서 감사합니다. 앞으로 또 다른 주제로 포스팅하겠습니다.

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

# K8s #쿠버네티스 #k3s #테라폼 #앤서블 #MERN #풀스택 #도커 #웹애플리케이션 #컨테이너화 #데브옵스 #컨테이너화된앱 #EC2 #MERN스택 #도커화된웹앱 #MERN앱 #도커컨테이너 #CI/CD 파이프라인 #GitHub 액션 #제로다운타임 #CI/CD #자동화 #배포 #기술팁

만일 쿠버네티스에서 테라폼을 사용하여 GitHub 액션 및 앤서블을 이용해 MERN 앱을 위한 엔드 투 엔드 CI/CD 파이프라인을 구축하는 데 문제가 발생한다면 언제든지 저에게 연락해 주세요. 최선을 다해 도와드리겠습니다. 감사합니다.

# 평문으로 🚀

In Plain English 커뮤니티의 일원이 되어 주셔서 감사합니다! 떠나시기 전에:

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

- 작가를 응원하고 팔로우를 눌러주세요! 👏
- 팔로우하기: X | LinkedIn | YouTube | Discord | 뉴스레터
- 다른 플랫폼도 방문해보세요: Stackademic | CoFeed | Venture | Cubed
- 알고리즘 콘텐츠를 다루도록 강요하는 블로깅 플랫폼에 지쳤나요? Differ를 시도해보세요
- 더 많은 콘텐츠는 PlainEnglish.io에서 확인하세요
